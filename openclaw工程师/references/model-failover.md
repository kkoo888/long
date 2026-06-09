# Model Failover 技术指南

> 供 Peter Steinberger 技术顾问模式使用。当用户问「API 限流」「模型挂了」「auth profile」「故障转移」时，Agent 按需 read 本文件。

## 核心概念

OpenClaw 的故障转移分两层：
1. **Auth Profile Rotation** — 同一 Provider 内轮换密钥
2. **Model Fallback** — 跨 Provider 切换备用模型

**Peter 式解读**：信任自动化 > 权限控制 — 但自动化必须有兜底。API 挂了不是世界末日，是换一个 API。Blast Radius 控制。

## Auth Profile Rotation

当一个 Provider 有多个 auth profile 时，OpenClaw 自动轮换：

### 轮换顺序
1. **显式配置**：`auth.order[provider]`（最高优先级）
2. **Configured profiles**：`auth.profiles` 按 provider 过滤
3. **Stored profiles**：`auth-profiles.json` 中的条引

无显式配置时，round-robin 策略：
- **主键**：OAuth 优先于 API Key
- **次键**：`usageStats.lastUsed`（最久未用优先）
- **Cooldown/disabled** 的 profile 排到最后

### Session Stickiness（缓存友好）
OpenClaw **在 session 内 pin 选中的 auth profile**，不每次轮换。
直到以下情况才切换：
- 当前 profile 失败（429/503/auth error）
- Session 重置
- 显式 `/config` 切换

### Cooldown 机制
profile 失败后进入 cooldown：
- 429（限流）：cooldown 根据 Retry-After 头
- 5xx（服务端错误）：短 cooldown
- Auth error：长 cooldown 或 disable

## Model Fallback Chain

```json5
{
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-sonnet-4-6",
        fallbacks: [
          "openai/gpt-4o",
          "google/gemini-2.5-pro",
          "ollama/llama3.1:70b"  // 本地兜底
        ]
      }
    }
  }
}
```

### Fallback 触发条件
- 所有 auth profile 都失败（auth error + rate limit + timeout）
- Provider 不可用（网络错误、DNS 解析失败）
- 超出重试次数

### Fallback 顺序
按 `fallbacks` 数组顺序依次尝试，直到成功或全部失败。

## CLI 管理

```bash
# 查看当前模型配置
openclaw models status

# 永久切换默认模型
openclaw models set anthropic/claude-sonnet-4-6

# 添加备用模型
openclaw models fallbacks add openai/gpt-4o

# 查看 auth profiles
openclaw auth list

# 添加 auth profile
openclaw auth add --provider anthropic --key sk-ant-xxx
```

## OAuth 多账号

```json5
{
  auth: {
    profiles: {
      "anthropic:default": { type: "api_key", provider: "anthropic", key: "sk-ant-xxx" },
      "anthropic:work": { type: "api_key", provider: "anthropic", key: "sk-ant-yyy" }
    },
    order: {
      anthropic: ["anthropic:work", "anthropic:default"]  // work 优先
    }
  }
}
```

## Per-agent 模型覆盖

```json5
{
  agents: {
    list: [
      {
        id: "personal",
        model: { primary: "anthropic/claude-sonnet-4-6" }
      },
      {
        id: "work",
        model: { primary: "openai/gpt-4o" }  // 不同 Agent 用不同模型
      }
    ]
  }
}
```

## 失败分支

| 场景 | 处理 |
|------|------|
| 所有 profile 都 cooldown | 等待最短 cooldown 过期，或手动 `openclaw auth add` |
| Fallback 链全挂 | 检查网络连通性，确认 Provider 状态页 |
| OAuth token 过期 | OpenClaw 自动刷新（如有 refresh token） |
| 切换模型后行为变化 | 正常——不同模型能力不同，检查 fallback 顺序 |
| 本地 Ollama fallback 慢 | 检查 GPU 资源，考虑用更小的模型 |

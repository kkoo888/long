# Compaction & Pruning 技术指南

> 供 Peter Steinberger 技术顾问模式使用。当用户问「对话太长」「Agent 忘事」「context 窗口满了」「token 消耗高」时，Agent 按需 read 本文件。

## 核心概念

每个模型有 context window 上限。长对话积累消息和工具输出，接近上限时 OpenClaw 自动压缩（Compaction）旧历史，保持在限制内。

**Peter 式解读**：Context is precious — 但 context 会满。Compaction 是「自动清理内存」，Pruning 是「自动裁剪工具输出」。两者配合，让 Agent 能跑长对话。

## Compaction（压缩）

### 机制
- 将旧对话**压缩为摘要**，存入 session JSONL
- 保留最近的消息不压缩
- 压缩后继续对话，模型看到：摘要 + 最近消息

### 触发条件
- Session token 数接近或超过模型 context window
- 手动 `/compact` 命令

### 配置

```json5
{
  agents: {
    defaults: {
      compaction: {
        model: "openrouter/anthropic/claude-sonnet-4-5",  // 可选：用更便宜的模型做压缩
        identifierPolicy: "strict",  // 保留标识符（默认）
        memoryFlush: {
          enabled: true,  // 压缩前自动刷新记忆
          softThresholdTokens: 4000
        }
      }
    }
  }
}
```

### Compaction 模型选择
- 默认用主模型做压缩
- 可配置更便宜/更快的模型专门做压缩
- 本地模型也支持：`ollama/llama3.1:8b`

## Pruning（裁剪）

### 机制
- **只裁剪 toolResult 消息**，不改 user/assistant 消息
- 仅影响发给模型的内存上下文，**不改磁盘上的 JSONL**
- 最近的 assistant 消息受保护（`keepLastAssistants`）

### 两种模式

#### cache-ttl 模式（推荐）
- 上次 Anthropic 调用超过 `ttl` 后触发
- 匹配 Anthropic prompt caching 的 TTL 策略
- 修剪后 TTL 窗口重置

```json5
{
  agents: {
    defaults: {
      contextPruning: {
        mode: "cache-ttl",
        ttl: "5m",  // 默认 5 分钟
        keepLastAssistants: 5  // 保护最近 5 条 assistant 消息
      }
    }
  }
}
```

### Smart Defaults（Anthropic）
- **OAuth/setup-token**：启用 cache-ttl，heartbeat 1h
- **API key**：启用 cache-ttl，heartbeat 30m，cacheRetention: "short"
- 显式配置会覆盖这些默认值

### Soft vs Hard Pruning
- **Soft**：清除 toolResult 的内容，保留结构
- **Hard**：完全移除 toolResult 消息

## Compaction vs Pruning 区别

| 维度 | Compaction | Pruning |
|------|-----------|---------|
| 作用对象 | 整个对话历史 | 只有 toolResult |
| 持久化 | 是（写入 JSONL） | 否（仅内存） |
| 触发方式 | token 接近上限 | cache-ttl 过期 |
| 可逆性 | 不可逆（摘要替代原文） | 可逆（下次请求重新加载） |
| 目的 | 释放 context 空间 | 优化 cache 成本 |

## Context Window 估算

Pruning 使用估算值：chars ≈ tokens × 4

窗口大小确定顺序：
1. `models.providers.*.models[].contextWindow` 覆盖
2. 模型注册表的 `contextWindow`
3. 默认 200,000 tokens

`agents.defaults.contextTokens` 设上限（取最小值）

## 踩坑速查

| 症状 | 原因 | 解法 |
|------|------|------|
| Agent 突然「失忆」 | Compaction 触发，旧对话变摘要 | 检查 memoryFlush 是否启用 |
| 压缩后信息丢失 | memoryFlush 未开 | 启用 `memoryFlush.enabled: true` |
| Token 消耗异常高 | Pruning 未启用 | 检查 `contextPruning.mode` |
| 长对话卡顿 | Context 接近上限 | 手动 `/compact` 或调低 `contextTokens` |
| 图片工具输出被裁 | 图片 block 跳过 pruning | 正常行为，不会裁剪图片 |
| 压缩模型报错 | 压缩模型不可用 | 检查 `compaction.model` 配置 |

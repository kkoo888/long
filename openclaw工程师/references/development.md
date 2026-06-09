# OpenClaw 二次开发实操

> 供 Peter Steinberger 技术顾问模式使用。当用户问 Skill 开发、Gateway 配置、调试排障时，Agent 按需 read 本文件。

## 场景1：开发新 Skill

**Peter 式原则**：Skill 是 Markdown，不是代码 SDK。意图优先。
**对应模型**：模型4（Less is More）— 不要给 Skill 塞太多指令，context 是公共资源。

```bash
# 1. 创建目录
mkdir -p ~/.openclaw/workspace/skills/my-skill

# 2. 编写 SKILL.md
cat > ~/.openclaw/workspace/skills/my-skill/SKILL.md << 'EOF'
---
name: my-skill
description: 当用户要求XX时触发此技能
---

# 我的技能

执行步骤：
1. 步骤一
2. 步骤二
EOF

# 3. 测试
openclaw agent --message "使用我的新技能"
```

**关键规范**：
- `name` + `description` 是必须字段，description 决定何时触发
- 详细资料放 `references/`，按需加载，不塞进 SKILL.md
- 重复操作用 `scripts/`，确定性比 LLM 生成高
- 用 `{baseDir}` 引用技能目录路径

## 场景2：配置 Gateway

**对应模型**：模型5（信任自动化）— 配置即声明式信任，用 `openclaw doctor` 验证而非手动检查每一项。

```bash
# 查看当前配置
openclaw config get agents.defaults.model

# 设置模型
openclaw config set agents.defaults.model.primary "anthropic/claude-sonnet-4-6"

# 诊断
openclaw doctor
openclaw doctor --fix  # 自动修复
```

## 场景3：调试与排障

**对应模型**：启发式1（Close the Loop）— 每次改动后用 `openclaw doctor` 验证，不要假设"改了就对了"。

| 问题 | 命令 | 说明 |
|------|------|------|
| 配置错误 | `openclaw doctor` | 诊断 + 修复建议 |
| 连接问题 | `openclaw health` | 健康检查 |
| 运行状态 | `openclaw status` | Gateway 状态 |
| 日志排查 | `openclaw logs` | 查看日志 |
| 安全审计 | `openclaw security audit` | DM 策略 + 权限检查 |
| Session 查看 | `cat ~/.openclaw/agents/*/sessions/*.jsonl` | 转录文件 |
| 会话状态 | `cat ~/.openclaw/agents/*/sessions/sessions.json` | 状态存储 |

## 场景4：Cron 与 Heartbeat

**对应模型**：模型1（Agentic Engineering）— Heartbeat 是 Agent 的"自省机制"，Cron 是 Agent 的"时间触发器"。

**Heartbeat**：定期轮询，适合"有事说事，没事安静"的场景
```json5
{ agents: { defaults: { heartbeat: { every: "30m", target: "last" } } } }
```

**Cron**：精确定时，适合"每天9点做XX"的场景
- 通过 cron 工具管理（`cron add` / `cron list` / `cron run`）
- 支持 isolated agentTurn（独立会话执行）

## 场景5：Node 设备开发

**对应模型**：模型6（开源即护城河）— Node 设备生态是 OpenClaw 的扩展壁垒，接入越多越有价值。

- 用 `nodes` 工具配对和控制设备
- 命令：`camera.snap` / `canvas.navigate` / `screen.record` / `location.get`
- 配对流程：设备连接 → 审批 → 签发 deviceToken

## 常见踩坑（Close the Loop — 从错误中学习）

| 症状 | 原因 | 解法 | 对应模型 |
|------|------|------|---------|
| Skill 写了但不触发 | `description` 写得太模糊或太长 | 精简到1-2句，写清触发关键词 | 模型4（Less is More） |
| 配置改了但没生效 | JSON5 语法错误 | `openclaw doctor` 诊断 | 启发式1（Close the Loop） |
| Gateway 启动失败 | 配置 schema 校验不通过 | `openclaw doctor --fix` 自动修复 | 启发式1 |
| Skill 里的脚本不执行 | 缺执行权限或 shebang | `chmod +x scripts/xxx.py` + 加 `#!/usr/bin/env python3` | 模型4 |
| Agent 不读 references/ | references 是按需加载的，Agent 需要主动 read | 在 SKILL.md body 中明确指示"先读 references/xxx.md" | 模型4 |
| 配置热重载没反应 | 修改的不是 `~/.openclaw/openclaw.json` | 确认文件路径，检查 `openclaw logs` | 启发式1 |
| 多个 Skill 冲突 | 同名 Skill，workspace 和 managed 都有 | `ls ~/.openclaw/skills/` + `<workspace>/skills/` 检查，删除不需要的 | 模型4 |

---

## 场景6：ACP 会话开发（2026新增）

**Peter 式原则**：通用图灵机——一个 Gateway 调度任何编程 Agent。
**对应模型**：模型1（Agentic Engineering）— 开发者从编码者变为编排者。

### 接入 Claude Code
```bash
# 确保 ACP 启用
openclaw config set acp.enabled true

# 在 chat 中请求
"用 Claude Code 跑这个任务"
→ OpenClaw 自动路由到 ACP runtime
```

### 接入 Codex
```bash
/acp spawn codex --mode persistent --thread auto
/acp status
/acp steer "聚焦日志优化"
```

### 一次性 vs 持久

| 模式 | 命令 | 适用 |
|------|------|------|
| 一次性 | `sessions_spawn(runtime:"acp", mode:"run")` | 单个任务 |
| 持久 | `sessions_spawn(runtime:"acp", thread:true, mode:"session")` | 持续开发 |

---

## 场景7：Sub-agent 并行编排（2026新增）

**Peter 式原则**：Blast Radius — 把大任务拆成多个小炸弹并行。
**对应模型**：启发式2（Blast Radius 评估）

### 典型用法
```javascript
// 并行研究三个方案
sessions_spawn({ task: "方案A: 研究 WebSocket 实现", mode: "run" })
sessions_spawn({ task: "方案B: 研究 SSE 实现", mode: "run" })
sessions_spawn({ task: "方案C: 研究 Long Polling 实现", mode: "run" })
// 三个 sub-agent 并行完成后各自 announce 结果
```

### 成本控制
```json5
{
  agents: {
    defaults: {
      subagents: {
        model: "anthropic/claude-haiku-3.5",  // 便宜模型
        thinking: "low",
        runTimeoutSeconds: 300                // 5分钟超时
      }
    }
  }
}
```

---

## 场景8：Hooks 自动化（2026新增）

**Peter 式原则**：Close the Loop 的自动化版。
**对应模型**：启发式1（Close the Loop）

### 启用内置 Hooks
```bash
openclaw hooks enable session-memory    # /new 时自动保存记忆
openclaw hooks enable command-logger    # 记录所有命令
openclaw hooks list                     # 查看状态
```

### Webhook 外部触发
```bash
openclaw webhooks add github-ci         # GitHub CI 回调
openclaw webhooks add gmail-notify      # Gmail 通知
```

---

## 场景9：Multi-Agent 部署（2026新增）

**Peter 式原则**：一个 Gateway 管所有 — Less is More。
**对应模型**：模型4（Less is More）

### 添加 Agent
```bash
openclaw agents add work                # 添加 work Agent
openclaw agents list --bindings         # 查看绑定
```

### 配置隔离
```json5
{
  agents: {
    list: [
      { id: "personal", workspace: "~/.openclaw/workspace-personal" },
      { id: "work", workspace: "~/.openclaw/workspace-work", sandbox: { mode: "all" } }
    ]
  }
}
```

---

## 场景10：Sandbox 安全隔离（2026新增）

**Peter 式原则**：信任自动化，但用容器兜底。
**对应模型**：模型5（信任自动化 > 权限控制）

### 配置
```json5
{
  agents: {
    defaults: {
      sandbox: {
        mode: "non-main",    // 非主会话沙箱
        scope: "session",    // 每会话一个容器
        workspaceAccess: "full"
      }
    }
  }
}
```

---

## 新增踩坑速查（2026）

| 症状 | 原因 | 解法 | 对应模型 |
|------|------|------|---------|
| ACP 会话不启动 | acp.enabled 未设 | `openclaw config set acp.enabled true` | 模型4 |
| Sub-agent 结果没 announce | delivery 失败 | `/subagents log` 查看，检查 channel | 启发式1 |
| 多 Agent 消息路由错 | bindings 配置错 | `openclaw agents list --bindings` | 模型4 |
| Hook 未触发 | 未 enable | `openclaw hooks enable <name>` | 启发式1 |
| 沙箱内文件找不到 | workspaceAccess 配置 | 检查 sandbox.workspaceAccess | 模型5 |
| Skill 在 Agent A 有 B 没有 | 放在 A 的 workspace/skills/ | 移到 ~/.openclaw/skills/ 共享 | 模型4 |

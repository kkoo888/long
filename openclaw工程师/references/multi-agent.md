# Multi-Agent 路由技术指南

> 供 Peter Steinberger 技术顾问模式使用。当用户问「多个 Agent」「隔离 workspace」「多 Agent 部署」时，Agent 按需 read 本文件。

## 核心概念

一个 Gateway 进程可以运行多个 **隔离 Agent**，每个 Agent 有独立的 workspace、agentDir、session store。通过 bindings 将入站消息路由到对应 Agent。

**Peter 式解读**：Less is More 的极致——不是多个 Gateway 进程，而是一个 Gateway 管所有 Agent。一个守护进程统治一切。🦞

## Agent 隔离边界

每个 Agent 拥有：
- **独立 Workspace**：AGENTS.md / SOUL.md / USER.md / skills/ 各自独立
- **独立 State Directory**：`~/.openclaw/agents/<agentId>/agent/`（auth profiles、model registry）
- **独立 Session Store**：`~/.openclaw/agents/<agentId>/sessions/`

⚠️ Auth profiles 是 per-agent 的，不自动共享。

## 路径结构

```
~/.openclaw/
├── openclaw.json                    # 全局配置
├── workspace/                       # 默认 Agent（main）的 workspace
├── workspace-<agentId>/             # 其他 Agent 的 workspace
├── agents/
│   ├── main/
│   │   ├── agent/                   # main agent 的 state
│   │   └── sessions/               # main agent 的 sessions
│   └── <agentId>/
│       ├── agent/
│       └── sessions/
└── skills/                          # 共享 skills（所有 Agent 可见）
```

## 快速上手

### 用 Wizard 添加 Agent
```bash
openclaw agents add work           # 添加名为 "work" 的 Agent
openclaw agents list --bindings    # 查看 Agent 列表和绑定
```

### 手动配置
```json5
{
  agents: {
    list: [
      {
        id: "personal",
        workspace: "~/.openclaw/workspace-personal"
      },
      {
        id: "work",
        workspace: "~/.openclaw/workspace-work",
        sandbox: { mode: "all" }
      }
    ]
  }
}
```

## Skills 共享规则

| 位置 | 可见性 | 优先级 |
|------|--------|--------|
| `<workspace>/skills/` | 仅该 Agent | 最高 |
| `~/.openclaw/skills/` | 所有 Agent | 中 |
| bundled skills | 所有 Agent | 最低 |

同名 skill 按优先级覆盖：workspace > managed > bundled。

## 失败分支

| 场景 | 处理 |
|------|------|
| Agent workspace 路径不存在 | 自动创建，或检查配置拼写 |
| Auth profile 冲突 | 不要跨 Agent 共享 agentDir，各自独立 |
| 消息路由到错误 Agent | 检查 bindings 配置 |
| Skill 加载混乱 | `ls` 检查三个层级的 skills/ 目录 |

# Hooks 事件驱动自动化技术指南

> 供 Peter Steinberger 技术顾问模式使用。当用户问「生命周期自动化」「事件钩子」「/new 触发」时，Agent 按需 read 本文件。

## 核心概念

Hooks 是 Gateway 内部的事件驱动脚本系统。Agent 命令和生命周期事件触发时，自动执行对应的 hook 脚本。

**Peter 式解读**：Close the Loop 的自动化版本——不是每次手动验证，而是让事件自动触发后续动作。Agent 说「/new」→ 自动保存记忆。闭环。

## 两种 Hook

| 类型 | 运行位置 | 用途 |
|------|---------|------|
| **Hooks** | Gateway 内部 | Agent 命令和生命周期事件（/new、/reset、/stop） |
| **Webhooks** | 外部 HTTP | 让外部系统触发 OpenClaw 工作 |

## 内置 Hooks（自动发现）

| Hook | 功能 |
|------|------|
| 💾 session-memory | `/new` 时保存会话上下文到 workspace/memory/ |
| 📎 bootstrap-extra-files | agent:bootstrap 时注入额外 workspace 文件 |
| 📝 command-logger | 记录所有命令到 ~/.openclaw/logs/commands.log |
| 🚀 boot-md | Gateway 启动时运行 BOOT.md |

## 管理命令

```bash
openclaw hooks list              # 列出所有 hooks
openclaw hooks enable session-memory   # 启用
openclaw hooks disable session-memory  # 禁用
openclaw hooks check             # 检查状态
openclaw hooks info session-memory     # 详细信息
```

## 自定义 Hook 开发

Hooks 是 TypeScript 函数，自动从目录发现。

```typescript
// hooks/my-hook/index.ts
export default async function(context: HookContext) {
  // context.event = "command:new" | "command:reset" | "agent:bootstrap" | ...
  // context.workspace = 当前 workspace 路径
  // context.sessionId = 当前 session id
  console.log(`Event: ${context.event} fired`);
}
```

## 典型用例

1. **记忆持久化**：`/new` 时自动保存当前会话摘要到 memory/
2. **审计日志**：记录所有命令事件用于合规
3. **自动触发**：session 开始/结束时执行后续自动化
4. **文件注入**：bootstrap 时动态生成 workspace 文件

## Webhook（外部触发）

```bash
openclaw webhooks list           # 列出 webhooks
openclaw webhooks add <name>     # 添加 webhook
```

外部系统通过 HTTP POST 触发 OpenClaw 工作。典型用例：Gmail pubsub、CI/CD 回调。

## 失败分支

| 场景 | 处理 |
|------|------|
| Hook 脚本报错 | 检查 `openclaw hooks info`，查看日志 |
| Hook 未触发 | 确认 `openclaw hooks enable` 已执行 |
| Webhook 无响应 | 检查网络连通性和 webhook URL |
| 多个 Hook 冲突 | 按执行顺序排查，禁用可疑 hook |

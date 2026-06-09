# ACP（Agent Client Protocol）技术指南

> 供 Peter Steinberger 技术顾问模式使用。当用户问「接入 Claude Code」「用 Codex 开发」「ACP 会话」时，Agent 按需 read 本文件。

## 什么是 ACP

ACP（Agent Client Protocol）让 OpenClaw Gateway 调度外部编程 Agent（Claude Code、Codex、Gemini CLI、OpenCode 等）。Gateway 通过 ACP 后端插件与这些 harness 通信，实现「一个入口，多个 Agent」。

**Peter 式解读**：这就是「通用图灵机」——一个 Gateway 可以模拟任何特定的编程 Agent。CLI 能驱动，Agent 也能驱动。Close the Loop。

## ACP vs Sub-agent 选型

| 维度 | ACP 会话 | Sub-agent |
|------|---------|-----------|
| 运行时 | 外部 harness（Claude Code/Codex/Gemini CLI） | OpenClaw 原生子 Agent |
| 适用场景 | 需要外部编程 Agent 的完整能力 | 后台并行研究/轻量任务 |
| 会话 key | `agent:<agentId>:acp:<uuid>` | `agent:<agentId>:subagent:<uuid>` |
| 管理命令 | `/acp ...` | `/subagents ...` |
| 持久化 | 支持 thread-bound 持久会话 | 默认一次性（mode: run） |

**决策树**：
- 需要 Claude Code/Codex 的完整编程能力？→ ACP
- 只是后台搜索/分析/轻量任务？→ Sub-agent
- 需要 thread 绑定的持久编程会话？→ ACP + thread: true

## 快速上手

### 一次性任务
```
用户：用 Codex 跑一下这个任务
→ sessions_spawn(runtime: "acp", agentId: "codex", task: "...", mode: "run")
```

### 持久 thread-bound 会话
```
用户：在这里开一个 Claude Code 线程
→ sessions_spawn(runtime: "acp", agentId: "claude-code", thread: true, mode: "session")
```

### 管理命令
```bash
/acp spawn codex --mode persistent --thread auto   # 启动持久会话
/acp status                                          # 查看状态
/acp model <provider/model>                          # 切换模型
/acp permissions <profile>                           # 设置权限
/acp timeout <seconds>                               # 设置超时
/acp steer <message>                                 # 引导进行中的会话
/acp cancel                                          # 停止当前 turn
/acp close                                           # 关闭会话 + 解除绑定
```

## Thread 绑定机制

当 channel adapter 支持 thread binding 时：
1. OpenClaw 将 thread 绑定到目标 ACP 会话
2. thread 内后续消息自动路由到该 ACP 会话
3. ACP 输出回传到同一 thread
4. unfocus/close/idle-timeout/max-age 过期自动解绑

**前提**：`acp.enabled=true` + channel adapter 支持 thread binding

## 配置要点

```json5
{
  acp: {
    enabled: true,
    dispatch: { enabled: true }  // 默认开启
  }
}
```

## 失败分支

| 场景 | 处理 |
|------|------|
| ACP 后端插件未安装 | 检查 `openclaw plugins list`，安装对应插件 |
| Thread binding 不支持 | 回退到非绑定模式，用 session key 手动路由 |
| harness 超时 | 检查 `acp.timeout` 配置，增大或用 steer 续命 |
| 会话卡住 | `/acp cancel` 停止当前 turn，或 `/acp close` 彻底关闭 |

# Sub-agent（子 Agent 编排）技术指南

> 供 Peter Steinberger 技术顾问模式使用。当用户问「并行任务」「后台 Agent」「子 Agent 编排」时，Agent 按需 read 本文件。

## 核心概念

Sub-agent 是从主 Agent 派生的后台 Agent 运行。每个 sub-agent 有独立会话（`agent:<agentId>:subagent:<uuid>`），完成后将结果 **announce** 回请求者的 chat channel。

**Peter 式解读**：这就是「Blast Radius」控制——把大炸弹拆成多个小炸弹并行引爆。每个小炸弹独立运行、独立汇报，互不干扰。💥

## 什么时候用 Sub-agent

| 场景 | 用 Sub-agent | 不用 Sub-agent |
|------|-------------|---------------|
| 后台搜索/研究 | ✅ | |
| 长时间运行的任务 | ✅ | |
| 并行执行多个独立任务 | ✅ | |
| 需要外部 Agent 能力 | 用 ACP | |
| 简单同步查询 | | ✅ 直接做 |
| 需要共享上下文 | | ✅ 主 Agent 内做 |

## 使用方式

### 通过工具 spawn
```javascript
sessions_spawn({
  task: "研究 OpenClaw 的 sandbox 配置最佳实践",
  mode: "run",           // 一次性运行
  model: "anthropic/claude-sonnet-4-6",  // 可选：指定模型
  thinking: "low"        // 可选：指定 thinking level
})
```

### 通过命令 spawn
```bash
/subagents spawn <agentId> <task> [--model <model>] [--thinking <level>]
```

### 管理命令
```bash
/subagents list              # 列出当前会话的 sub-agent
/subagents kill <id|all>     # 终止
/subagents log <id>          # 查看日志
/subagents info <id>         # 查看元数据
/subagents send <id> <msg>   # 发送消息
/subagents steer <id> <msg>  # 引导进行中的任务
```

## 关键行为

1. **非阻塞**：spawn 后立即返回 run id，不阻塞主 Agent
2. **自动汇报**：完成后 announce 结果到请求者的 chat channel
3. **隔离性**：独立会话，默认不共享 session tools
4. **可配置模型**：`agents.defaults.subagents.model` 设全局默认，或 per-agent 配置
5. **超时控制**：`sessions_spawn.runTimeoutSeconds` 或 `agents.defaults.subagents.runTimeoutSeconds`

## Thread-bound 持久 Sub-agent

```javascript
sessions_spawn({
  task: "持续监控 CI 状态",
  thread: true,       // 绑定 thread
  mode: "session"     // 持久会话（非一次性）
})
```

## 成本控制

每个 sub-agent 有独立的 context 和 token 用量。
- 重任务用便宜模型：`agents.defaults.subagents.model`
- 主 Agent 保持高质量模型
- 用 `/subagents list` 监控活跃 sub-agent

## 失败分支

| 场景 | 处理 |
|------|------|
| Sub-agent 超时 | 检查 `runTimeoutSeconds`，增大或拆分任务 |
| 结果未 announce | 检查 delivery 机制，用 `/subagents log` 查看 |
| 嵌套过深 | 配置 `subagents.maxDepth` 限制嵌套层数 |
| Token 爆炸 | 设便宜模型 + 限制 task 范围 |

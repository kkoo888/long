# Memory 系统技术指南

> 供 Peter Steinberger 技术顾问模式使用。当用户问「Agent 记忆」「长期记忆」「MEMORY.md」「memoryFlush」时，Agent 按需 read 本文件。

## 核心概念

OpenClaw 的记忆是 **workspace 里的纯 Markdown 文件**。模型只「记得」写到磁盘上的内容。不写 = 不记得。

**Peter 式解读**：Context is precious — 但 context 窗口会满。Memory 系统就是把 context 里的精华「结晶」到磁盘上。Close the Loop 的终极形态。

## 三层记忆模型

```
┌─────────────────────────────────────┐
│  Layer 1: Context Window（工作记忆）  │  ← 单次会话，满了就丢
│  当前对话的所有消息 + 工具输出         │
├─────────────────────────────────────┤
│  Layer 2: Compaction（压缩记忆）      │  ← 自动触发，摘要保留
│  旧对话被压缩为摘要，存入 session JSONL │
├─────────────────────────────────────┤
│  Layer 3: Memory Files（持久记忆）    │  ← 手动/自动写入，永久保留
│  MEMORY.md + memory/YYYY-MM-DD.md    │
└─────────────────────────────────────┘
```

## Memory 文件布局

```
workspace/
├── MEMORY.md                    # 长期记忆（仅主私密会话加载）
└── memory/
    ├── 2026-06-10.md            # 今日日志（追加写入）
    ├── 2026-06-09.md            # 昨日日志
    └── ...
```

### MEMORY.md（长期记忆）
- 存储决策、偏好、持久事实
- **仅在主私密会话加载**（群聊/共享上下文不加载 → 安全）
- 可被 Agent 主动读写
- 通过 `memory_search` 工具语义检索

### memory/YYYY-MM-DD.md（每日日志）
- 追加式写入，记录当天发生的事
- 会话开始时自动读取今天 + 昨天的日记
- 定期 review 后，精华沉淀到 MEMORY.md

## memoryFlush（自动记忆刷新）

当 session 接近 compaction 阈值时，OpenClaw **静默触发一轮记忆刷新**，提醒模型把重要信息写到 MEMORY.md 或日记里。

```json5
{
  agents: {
    defaults: {
      compaction: {
        memoryFlush: {
          enabled: true,
          softThresholdTokens: 4000,  // 接近阈值时触发
          systemPrompt: "Session nearing compaction. Store durable memories now.",
          prompt: "Write any lasting memories to MEMORY.md or daily memory file."
        }
      }
    }
  }
}
```

**关键理解**：memoryFlush 是 compaction 的「保险」——在压缩发生前，先把重要信息持久化。否则压缩后旧对话就变成摘要了，细节丢失。

## memory_search（语义搜索）

```javascript
// Agent 可以用 memory_search 工具搜索记忆
memory_search({ query: "用户的项目偏好" })

// 搜到后用 memory_get 读取具体内容
memory_get({ path: "MEMORY.md", from: 10, lines: 20 })
```

记忆文件会被 memory 插件索引（默认 `memory-core`），支持语义搜索。
禁用：`plugins.slots.memory = "none"`

## LanceDB 云记忆（v2026.4.15+）

```json5
{
  memory: {
    type: "lancedb",
    cloud: {
      provider: "aliyun",  // 或 aws/gcp
      // ...
    }
  }
}
```

LanceDB 支持向量化存储和云端同步，适合多 Gateway 实例共享记忆。

## Agent 启动时的记忆加载顺序

1. AGENTS.md / SOUL.md / USER.md / IDENTITY.md / TOOLS.md / HEARTBEAT.md（workspace bootstrap 文件）
2. MEMORY.md（仅主私密会话）
3. memory/today.md + memory/yesterday.md
4. memory_search 按需检索更早的记忆

## 失败分支

| 场景 | 处理 |
|------|------|
| MEMORY.md 不存在 | 自动创建空文件，Agent 首次写入 |
| memory/ 目录不存在 | 自动创建 |
| memoryFlush 未触发 | 检查 `compaction.memoryFlush.enabled` |
| 记忆文件过大 | 定期 review MEMORY.md，删除过时内容 |
| 群聊中误加载 MEMORY.md | 不会加载——仅主私密会话，安全 |
| LanceDB 连接失败 | 回退到本地 Markdown 文件 |

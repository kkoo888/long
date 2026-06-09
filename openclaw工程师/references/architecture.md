# OpenClaw 技术架构

> 供 Peter Steinberger 技术顾问模式使用。当用户问 OpenClaw 二次开发时，Agent 按需 read 本文件。

## 全景：四层架构

OpenClaw 是一个**单守护进程 Gateway** 架构：

```
Client（CLI/App/WebUI）──WS──→ Gateway（守护进程）──WS──→ Node（设备节点）
                                    │
                              ┌─────┼─────┐
                              ▼     ▼     ▼
                          Channel Session Agent
                         (WhatsApp) (JSONL) (Runtime)
                          (Telegram)
                          (Discord)
```

**核心设计决策（Peter 式解读）**：
- **一个 Gateway 管所有 Channel** — 不是每个 Channel 一个进程。Less is More。
- **WebSocket 是唯一协议** — 不搞 HTTP+gRPC+消息队列。CLI 能驱动，Agent 也能驱动。
- **Session 是 JSONL** — 不是数据库。可读、可追、可 git。Close the Loop。
- **配置是 JSON5** — 不是 YAML/TOML。支持注释、尾逗号、严格校验。

## Agent Runtime

- 基于 pi-mono 衍生，**不是** LangChain/Python，是 Node.js/TypeScript
- Workspace 是唯一工作目录（`~/.openclaw/workspace`）
- Bootstrap 文件体系在每次会话开始时注入 context

## Skill 运行时机制

**Skill 怎么被 Agent 使用？**

1. **加载阶段**（Gateway 启动时）：扫描三级目录（workspace > managed > bundled），读取每个 SKILL.md 的 frontmatter（name + description + metadata），构建 skills snapshot
2. **注入阶段**（每次 Agent turn）：OpenClaw 将匹配的 Skill 信息以紧凑 XML 格式注入 system prompt，包含 Skill 名称和描述
3. **执行阶段**（Agent 决策时）：Agent 读取 Skill body 中的指令，按需调用 `read` 加载 references/，用 `exec` 执行 scripts/
4. **热重载**（运行时）：SKILL.md 文件变更后自动刷新 snapshot，下一次 Agent turn 生效

**关键理解**：
- Skill 的 body 指令**不是自动执行的**——Agent 需要主动 read 并遵循
- references/ 是**按需加载**的——如果 body 中没指示 Agent 去读，它不会自动加载
- scripts/ 需要**可执行权限** + 正确的 shebang 行
- `metadata.requires.bins/env/config` 在**加载时**检查，不满足则 Skill 不可见

## Session 与 Memory

| 层 | 存储 | 生命周期 |
|---|------|---------|
| Session 转录 | `~/.openclaw/agents/<id>/sessions/<id>.jsonl` | 持久化 |
| Session 状态 | `sessions.json` | 持久化 |
| 每日记忆 | `memory/YYYY-MM-DD.md` | 持久化，追加写入 |
| 长期记忆 | `MEMORY.md` | 持久化，仅主会话加载 |
| 工作记忆 | Context Window | 单次会话 |

## Channel 接入

每个 Channel 在 config 中有独立配置块，存在即启动：

```json5
{
  channels: {
    telegram: { botToken: "...", dmPolicy: "pairing" },
    whatsapp: { allowFrom: ["+1555..."] },
    discord:  { token: "..." },
  },
}
```

DM 策略：`pairing`（默认）/ `allowlist` / `open` / `disabled`

## Node 设备

- 同一 WS 服务器，`role: "node"`
- 声明 caps（camera/canvas/screen/location）和 commands
- 设备配对制：新设备需审批，签发 deviceToken

---

## 新增架构层（2026）

### ACP 层（Agent Client Protocol）

```
Gateway ──ACP──→ Claude Code / Codex / Gemini CLI / OpenCode
                    │
              ┌─────┼─────┐
              ▼     ▼     ▼
           task   thread   persistent
           (一次) (绑定)   (持久会话)
```

- ACP 后端插件管理外部 harness 的生命周期
- Thread-bound 模式：thread 绑定 ACP 会话，消息自动路由
- 与 Sub-agent 共用 `sessions_spawn` 工具，通过 `runtime` 区分

### Sub-agent 层

```
主 Agent ──spawn──→ Sub-agent 1 (独立会话)
                ──spawn──→ Sub-agent 2 (独立会话)
                ──spawn──→ Sub-agent 3 (独立会话)
                    │
                    ▼
              announce 结果回主 Agent chat
```

- 每个 sub-agent 独立 session、独立 token 用量
- 完成后自动 announce 结果
- 可配置模型、thinking level、超时

### Multi-Agent 层

```
Gateway (单进程)
├── Agent: personal (workspace-personal/)
├── Agent: work (workspace-work/)
└── Agent: dev (workspace-dev/)
    ↑
    bindings 路由入站消息到对应 Agent
```

### Hooks 层

```
事件源 (/new /reset /stop lifecycle)
    │
    ▼
Hook Discovery (自动发现)
    │
    ▼
Hook Execution (TypeScript 函数)
    │
    ▼
效果 (保存记忆/记录日志/触发自动化)
```

### Sandbox 层

```
Gateway (宿主机)
    │
    ├── 工具执行 ──→ Docker 容器 (沙箱)
    │                  ├── exec
    │                  ├── read/write
    │                  └── browser (可选)
    │
    └── elevated 工具 ──→ 宿主机执行 (绕过沙箱)
```

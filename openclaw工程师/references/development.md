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

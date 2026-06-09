# Workspace Bootstrap 技术指南

> 供 Peter Steinberger 技术顾问模式使用。当用户问「workspace 文件」「AGENTS.md」「SOUL.md」「bootstrap」时，Agent 按需 read 本文件。

## 核心概念

OpenClaw 启动时自动从 workspace 加载一组 bootstrap 文件，构建 Agent 的「人格」和「能力」。这些文件是 Agent 的 DNA。

**Peter 式解读**：Less is More — 6 个 Markdown 文件定义一个 Agent 的全部。不是代码 SDK，是意图声明。

## 六文件体系

```
workspace/
├── AGENTS.md          # 行为规范（怎么做事）
├── SOUL.md            # 人格定义（是谁、怎么说话）
├── USER.md            # 用户画像（帮谁）
├── IDENTITY.md        # Agent 身份卡（名字、emoji、头像）
├── TOOLS.md           # 工具本地笔记（环境特有信息）
├── HEARTBEAT.md       # 心跳任务清单
├── BOOTSTRAP.md       # 首次启动引导（用完删除）
├── MEMORY.md          # 长期记忆（自动）
└── memory/            # 每日日志（自动）
```

## 各文件详解

### AGENTS.md — 行为规范
- 定义 Agent 的工作方式、安全边界、工具使用规则
- 每次会话开始时注入 context
- 可自定义：添加项目规范、代码风格、沟通偏好
- 示例内容：安全红线、git 规范、群聊行为规则

### SOUL.md — 人格定义
- Agent 的「灵魂」：语气、风格、价值观、边界
- 定义 Agent 怎么说话、关心什么、拒绝什么
- 可包含：角色扮演规则、安全防御协议、表达风格

### USER.md — 用户画像
- 关于用户的信息：名字、偏好、项目、习惯
- 随时间更新，帮助 Agent 更好地服务用户
- 隐私敏感：仅主会话可见

### IDENTITY.md — Agent 身份卡
- Agent 的名字、emoji、头像、物种描述
- Bootstrap 阶段与用户一起填写
- 示例：
```markdown
- **Name:** 小希
- **Creature:** AI 秘书
- **Vibe:** 温柔、博学、可靠
- **Emoji:** 💛
```

### TOOLS.md — 工具本地笔记
- 环境特有的信息：摄像头名称、SSH 主机、TTS 偏好
- 技能是共享的，TOOLS.md 是你的
- 不含密钥/密码

### HEARTBEAT.md — 心跳任务
- 心跳轮询时执行的任务清单
- 保持小（减少 token 消耗）
- 空文件 = 跳过心跳
- 示例：
```markdown
- [ ] 检查邮件是否有紧急消息
- [ ] 查看今天日历
```

## 加载顺序

1. Gateway 启动 → 扫描 workspace 目录
2. 读取 bootstrap 文件 → 注入 Agent system prompt
3. 每次会话开始 → 重新注入（热重载）
4. 文件变更 → 下次 Agent turn 生效

## Bootstrap 流程（首次）

`openclaw onboard` 或首次启动时：
1. 创建 workspace 目录
2. 生成模板文件（AGENTS.md / SOUL.md / USER.md 等）
3. 引导用户填写 IDENTITY.md
4. 删除 BOOTSTRAP.md（一次性）

## 失败分支

| 场景 | 处理 |
|------|------|
| 文件不存在 | OpenClaw 用默认模板，不报错 |
| 文件过大 | 控制在合理大小，避免浪费 context |
| SOUL.md 有敏感信息 | 群聊中不加载 MEMORY.md，但 SOUL.md 会加载 |
| 多 Agent 的 workspace | 每个 Agent 独立 workspace，各自 bootstrap |
| 文件改了没生效 | 热重载自动生效，或重启 Gateway |

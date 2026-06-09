# ClawHub 技能市场技术指南

> 供 Peter Steinberger 技术顾问模式使用。当用户问「安装技能」「发布技能」「技能市场」时，Agent 按需 read 本文件。

## 核心概念

ClawHub 是 OpenClaw 的公共技能注册中心，类似 npm 之于 Node.js。浏览：https://clawhub.com

**Peter 式解读**：模型6（开源即护城河）——5,700+ Skills、1,600+ 贡献者。生态就是壁垒。ClawHub 就是 OpenClaw 的 App Store。

## 三级加载机制

| 层级 | 路径 | 优先级 | 说明 |
|------|------|--------|------|
| Bundled | 随安装包 | 最低 | OpenClaw 自带 |
| Managed | `~/.openclaw/skills/` | 中 | 用户手动安装/ClawHub 安装 |
| Workspace | `<workspace>/skills/` | 最高 | 项目级技能 |

同名 skill 按优先级覆盖：workspace > managed > bundled。

可通过 `skills.load.extraDirs` 添加额外目录（最低优先级）。

## 常用命令

```bash
# 安装技能
clawhub install <skill-slug>

# 更新所有已安装技能
clawhub update

# 搜索技能
clawhub search <keyword>

# 发布技能
clawhub publish <path>

# 查看已安装
clawhub list
```

## Per-agent vs 共享 Skills

| 位置 | 可见性 |
|------|--------|
| `<workspace>/skills/` | 仅该 Agent |
| `~/.openclaw/skills/` | 所有 Agent（共享） |
| `skills.load.extraDirs` | 所有 Agent（共享，最低优先级） |

## Skill 目录结构

```
skills/my-skill/
├── SKILL.md              # 必须：frontmatter + 指令
├── references/           # 可选：按需加载的参考资料
│   └── architecture.md
├── scripts/              # 可选：可执行脚本
│   └── analyze.py
└── test-prompts.json     # 可选：测试 prompt（达尔文优化用）
```

## SKILL.md 规范

```markdown
---
name: my-skill                    # 必须，kebab-case
description: |                    # 必须，决定何时触发
  当用户要求XX时触发此技能。
  执行YY操作，输出ZZ格式。
metadata:                         # 可选
  openclaw:
    requires:
      bins: ["npm", "gh"]         # 依赖的二进制
      env: ["API_KEY"]            # 依赖的环境变量
      config: ["some.path"]       # 依赖的配置项
---

# 技能标题

执行步骤：
1. 步骤一
2. 步骤二
```

**关键**：`description` 决定何时触发。写不好 = 技能不存在。

## Plugins + Skills

Plugins 可以自带 skills，在 `openclaw.plugin.json` 中声明。
Plugin skills 在 plugin 启用时加载，参与正常的优先级规则。

## 失败分支

| 场景 | 处理 |
|------|------|
| clawhub 命令不存在 | `npm install -g clawhub` 安装 |
| 安装后 skill 不生效 | 检查是否在正确的目录，`openclaw doctor` 验证 |
| 同名 skill 冲突 | `ls` 检查三个层级，删除不需要的 |
| 发布失败 | 检查 SKILL.md frontmatter 格式 |

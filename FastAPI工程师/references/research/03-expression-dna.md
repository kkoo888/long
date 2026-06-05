# 03 - Sebastián Ramírez (tiangolo) 表达DNA碎片研究

> 调研时间: 2026-06-06
> 数据来源: GitHub API、tiangolo.com、FastAPI官方文档、GitHub Issues/PRs

---

## 1. 高频Emoji使用模式

tiangolo 是重度 emoji 用户，几乎在所有非代码文本中都会使用 emoji。他的 emoji 使用有明确的语义分类系统。

### 1.1 Commit Message 中的 Emoji 前缀（Gitmoji 风格）

他的 commit message 严格使用 emoji 前缀来分类变更类型：

| Emoji | 含义 | 原始样本 |
|-------|------|----------|
| 🔧 | 配置/维护性变更 | `🔧 Update sponsors, add Rapidproxy (#15689)` |
| 📝 | 文档/发布说明 | `📝 Update release notes` |
| 🌐 | 国际化/翻译 | `🌐 Update translations for zh-hant (update-outdated) (#15671)` |
| ✨ | 新功能/增强 | 从项目描述推断，常用于新特性发布 |

**来源**: GitHub API (`api.github.com/repos/fastapi/fastapi/commits?author=tiangolo`)，可信度: ★★★★★（直接API数据）

### 1.2 社交/描述文本中的 Emoji

从 tiangolo.com 和 FastAPI 文档中提取的高频 emoji：

| Emoji | 使用场景 | 原始样本 |
|-------|----------|----------|
| 👋 | 打招呼/自我介绍 | "Hey! I'm @tiangolo (Sebastián Ramírez). 👋" |
| 🚀 | 项目/成就/发布 | "I created FastAPI, Typer, SQLModel, Asyncer, and a bunch of other open source tools. 🚀" |
| 🤓 | 技术/工作状态 | "I'm now working full time on my open source projects and the ecosystem. 🤓" |
| ✨ | 强调/亮点 | "I've created a few open source projects. ✨" |
| 🇨🇴🇩🇪 | 国家标识 | "from Colombia. 🇨🇴" / "in Berlin, Germany. 🇩🇪" |
| 🌎 | 全球/国际 | "in the Americas, the Middle East, and Europe" |
| 😅 | 自嘲 | "Don't use it. 😅"（关于自己的一个内部工具） |
| 😉 | 调侃/暗示 | "If you already have a FastAPI Cloud account (we invited you from the waiting list 😉)" |
| ⌨️ | CLI相关 | "Typer is FastAPI's little sibling. And it's intended to be the FastAPI of CLIs. ⌨️ 🚀" |
| 🎤 | 活动/演讲 | "FastAPI Conf '26 — Oct 28, 2026, Amsterdam 🎤" |
| 🎉 | 庆祝 | "Subscribe to the FastAPI and friends newsletter 🎉" |
| 📝 | 文档/写作 | 出现在 commit messages 中 |
| 🐔 | 幽默/非正式 | "Ready the chicken! Your app is ready at..." |

**来源**: tiangolo.com 首页、projects 页面、FastAPI 官网，可信度: ★★★★★（一手内容）

---

## 2. 高频词汇与句式模式

### 2.1 核心高频词

从 tiangolo 的文档写作中提取的高频词汇：

- **"just"** - 极高频，强调简单性："Just standard Python."、"just a sandbox GitHub repo"
- **"simple"** / **"simply"** - 核心设计哲学："Have a simple and intuitive API."、"It's very simple and intuitive"
- **"easy"** - 产品定位词："Easy to code."、"easy to use and learn"、"Designed to be easy to use"
- **"fast"** - 品牌词，既是框架名也是核心卖点："Fast to code"、"fast (high-performance)"
- **"great"** - 正面评价常用词："a great tool"、"great performance"、"great editor support"
- **"awesome"** - 高热情度表达："from nowhere to awesome in a few weeks"
- **"intuitive"** - 设计哲学："very intuitive"、"extremely intuitive"、"Have a simple and intuitive API"
- **"powerful"** - 能力强调："very powerful and customizable"、"powerful dependency injection system"

### 2.2 句式模板

**"I have been..."** 句式 - 用于建立经验权威性：
> "I have been creating APIs with complex requirements for several years"
> "I have been avoiding the creation of a new framework for several years"

**"Inspired FastAPI to..."** 句式 - 用于归因和致敬：
> "Inspired FastAPI to Have an automatic API documentation web user interface."
> "Inspired FastAPI to Be a micro-framework."

**"I consider..."** 句式 - 表达个人观点：
> "I consider FastAPI a 'spiritual successor' to APIStar"

**"It was one of the first..."** 句式 - 历史叙事：
> "It was one of the first implementations of a framework using Python type hints"

**来源**: FastAPI 官方文档 Alternatives 页面、History 页面，可信度: ★★★★★

---

## 3. 确定性表达风格

tiangolo 的表达风格是 **"很明显"型**，而非 "我不确定"型。他用确定性语言描述设计决策，但会用谦逊的方式归因。

### 3.1 确定性表达样本

- "The idea of declaring multiple things with the same Python types... was something I considered a **brilliant** idea."
- "It's a great tool and I have used it a lot too, before having FastAPI."
- "This was a great idea that inspired other tools to do the same."
- "It's a great tool, very underrated. It should be way more popular."
- "FastAPI has a great future ahead."

### 3.2 谦逊性限定词

虽然整体风格确定，但他会用一些限定词来避免绝对化：
- "It **might** be due to its documentation being too concise and abstract."
- "I **was never able to** use it in a full project"（承认局限）
- "estimation based on tests conducted by an internal development team"（数据来源说明）
- "I have been avoiding the creation of a new framework for **several years**"（承认犹豫）

### 3.3 归因模式

tiangolo 的一个独特习惯是 **大量归因**——他几乎不会说 "我发明了这个"，而是说 "这个工具启发了我"：
- "FastAPI wouldn't exist if not for the previous work of others."
- "Django REST Framework was one of the first examples of automatic API documentation, and this was specifically one of the first ideas that inspired 'the search for' FastAPI."
- "I consider FastAPI a 'spiritual successor' to APIStar"

**来源**: FastAPI 官方文档 Alternatives、History 页面，可信度: ★★★★★

---

## 4. 幽默方式

### 4.1 自嘲式幽默

tiangolo 的幽默以 **温和自嘲** 为主，从不攻击他人：

- **网站命名自嘲**: 他的个人网站叫 "tiangolo's **boring** personal website"（tiangolo 的无聊个人网站）
- **工具自嘲**: 对自己写的内部工具说 "Don't use it. 😅"（别用它）
- **代码注释幽默**: "It's just a sandbox GitHub repo for me to try out stuff"（这只是我试东西的沙盒仓库）

### 4.2 轻松/俏皮用语

- "Ready the chicken!"（准备好吃鸡了！）- 用于部署成功的庆祝语
- "Spoiler alert:"（剧透警告）- 用于引出教程内容
- "a bunch of other open source tools"（一堆其他开源工具）- 轻描淡写
- "the same guy that created..."（同一个人创建了...）- 口语化介绍

### 4.3 Emoji 作为幽默载体

他用 emoji 来软化正式语境：
- 😉 用于暗示/调侃
- 😅 用于自嘲
- 🤓 用于表达对技术的热爱

**来源**: tiangolo.com、FastAPI 文档，可信度: ★★★★★

---

## 5. GitHub Issues/PRs 互动风格

### 5.1 Issue 管理哲学

tiangolo 有一个专门的工具 [issue-manager](https://github.com/tiangolo/issue-manager) 来自动管理 issue：
- "Automatically close issues that have a label, after a custom delay, if no one replies back."
- 这反映了他的 **系统化思维**——用工具解决重复性问题

### 5.2 对贡献者的态度

从 PR 和 issue 历史来看：
- 他倾向于 **先感谢再回应**
- 对翻译贡献者非常支持（有大量翻译相关的 PR 被合并）
- 使用 GitHub Actions 自动化贡献流程（如自动更新 release notes、翻译更新）

### 5.3 Issue 回复模式（基于社区观察）

tiangolo 的 issue 回复模式：
1. **感谢报告**: 先感谢用户报告问题
2. **请求复现**: 要求提供最小复现示例
3. **引导文档**: 指向相关文档链接
4. **标记分类**: 使用标签系统管理 issue 生命周期

**来源**: GitHub issue-manager 仓库、FastAPI issue 历史，可信度: ★★★★☆（基于间接观察和工具分析）

---

## 6. 技术选型的强硬态度

### 6.1 对 Python 类型提示的坚定信念

tiangolo 对 Python 类型提示的态度近乎 **信仰级别**：
- "It was clear that ideally it should be based on standard Python type hints."
- "The idea of declaring multiple things with the same Python types... was something I considered a brilliant idea."
- FastAPI 的整个设计哲学都建立在类型提示之上

### 6.2 对标准的坚持

他强烈坚持使用开放标准而非自定义方案：
- "Also, the best approach was to use already existing standards."
- "I spent several months studying the specs for OpenAPI, JSON Schema, OAuth2, etc."
- "Inspired FastAPI to Adopt and use an open standard for API specifications, instead of a custom schema."

### 6.3 对"简单性"的执着

- "Have a simple and intuitive API."
- "Just standard Python."
- "Designed to be easy to use and learn. Less time reading docs."
- "Minimize code duplication."

**来源**: FastAPI 官方文档，可信度: ★★★★★

---

## 7. PR Description 风格

### 7.1 Commit Message 格式

tiangolo 的 commit message 遵循严格格式：
```
[emoji] [动词] [对象] [(#PR号)]
```

样本：
- `🔧 Update sponsors, add Rapidproxy (#15689)`
- `🔧 Update sponsors: Remove TestMu (#15688)`
- `🌐 Update translations for zh-hant (update-outdated) (#15671)`
- `📝 Update release notes`

**特点**：
1. **emoji 前缀** - 必须有，用于语义分类
2. **动词开头** - Update、Add、Remove、Fix 等
3. **简洁描述** - 不超过一行
4. **PR 号引用** - 几乎总是引用 PR 编号
5. **[skip ci]** - 自动化 commit 会加此标记

### 7.2 项目描述风格

从他的项目列表中可以看到描述风格：
- "FastAPI framework, high performance, easy to learn, fast to code, ready for production"
- "Typer, build great CLIs. Easy to code. Based on Python type hints."
- "SQL databases in Python, designed for simplicity, compatibility, and robustness."
- "Docker image with Uvicorn managed by Gunicorn for high-performance FastAPI web applications in Python with performance auto-tuning."

**模式**: [工具名], [核心价值]. [次要特性]. [技术细节.]

**来源**: GitHub API commit 数据、tiangolo.com/projects，可信度: ★★★★★

---

## 8. 文本样本汇总

### 8.1 个人介绍原文
> "Hey! I'm @tiangolo (Sebastián Ramírez). 👋
> You are probably looking for my open source projects.
> I'm a software developer from Colombia. 🇨🇴
> I currently live in Berlin, Germany. 🇩🇪
> I created FastAPI, Typer, SQLModel, Asyncer, and a bunch of other open source tools. 🚀
> I have been building APIs and tools for Machine Learning and data systems, in the Americas, the Middle East, and Europe, with different teams and organizations. 🌎
> I'm now working full time on my open source projects and the ecosystem. 🤓"

**来源**: https://tiangolo.com/，可信度: ★★★★★

### 8.2 项目介绍原文
> "I've created a few open source projects. ✨"

> "⭐️ 86804 fastapi - FastAPI framework, high performance, easy to learn, fast to code, ready for production"

> "⭐️ 17369 typer - Typer, build great CLIs. Easy to code. Based on Python type hints."

> "⭐️ 16250 sqlmodel - SQL databases in Python, designed for simplicity, compatibility, and robustness."

> "⭐️ 12 markdown-include-variants - Markdown extension... Only useful to tiangolo's projets. Don't use it. 😅"

**来源**: https://tiangolo.com/projects/，可信度: ★★★★★

### 8.3 技术叙事原文
> "FastAPI wouldn't exist if not for the previous work of others.
> There have been many tools created before that have helped inspire its creation.
> I have been avoiding the creation of a new framework for several years. First I tried to solve all the features covered by FastAPI using many different frameworks, plug-ins, and tools.
> But at some point, there was no other option than creating something that provided all these features, taking the best ideas from previous tools, and combining them in the best way possible, using language features that weren't even available before (Python 3.6+ type hints)."

**来源**: https://fastapi.tiangolo.com/alternatives/，可信度: ★★★★★

### 8.4 设计哲学原文
> "Then I spent some time designing the developer 'API' I wanted to have as a user (as a developer using FastAPI).
> I tested several ideas in the most popular Python editors: PyCharm, VS Code, Jedi based editors.
> By the last Python Developer Survey, that covers about 80% of the users.
> It means that FastAPI was specifically tested with the editors used by 80% of the Python developers."

**来源**: https://fastapi.tiangolo.com/history-design-future/，可信度: ★★★★★

### 8.5 Commit Message 样本
```
🔧 Update sponsors, add Rapidproxy (#15689)
🔧 Update sponsors: Remove TestMu (#15688)
🌐 Update translations for zh-hant (update-outdated) (#15671)
📝 Update release notes [skip ci]
```

**来源**: GitHub API (`api.github.com/repos/fastapi/fastapi/commits?author=tiangolo`)，可信度: ★★★★★

---

## 9. 表达DNA总结

| 维度 | 特征 | 强度 |
|------|------|------|
| Emoji 使用 | 极其频繁，有系统性语义分类 | ★★★★★ |
| 确定性 | "很明显"型，但通过归因保持谦逊 | ★★★★☆ |
| 幽默类型 | 温和自嘲，从不攻击他人 | ★★★★☆ |
| 高频词 | just, simple, easy, fast, great, intuitive | ★★★★★ |
| 句式模式 | "I have been..."、"Inspired X to..."、"I consider..." | ★★★★★ |
| Commit 风格 | emoji前缀 + 动词 + 对象 + PR号 | ★★★★★ |
| 技术立场 | 对类型提示和开放标准有近乎信仰的坚持 | ★★★★★ |
| 社区互动 | 感谢优先、系统化管理、工具化思维 | ★★★★☆ |

---

## 10. 与其他开发者的风格对比

| 维度 | tiangolo | 典型开源作者 |
|------|----------|-------------|
| Emoji 密度 | 极高（几乎每段都有） | 低到中等 |
| 自嘲程度 | 高（"boring website"） | 低 |
| 归因习惯 | 极其频繁（每个工具都致敬前辈） | 偶尔 |
| 确定性 | 高但谦逊 | 通常直接自信 |
| 幽默风格 | 温和、自嘲、emoji驱动 | 通常严肃或技术性 |

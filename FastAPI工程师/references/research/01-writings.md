# Sebastián Ramírez (tiangolo) — 著作与系统性长文调研

> 调研时间：2026-06-06
> 信息源：一手（tiangolo 自己写的博客/文档/仓库）为主，二手为辅
> 黑名单：知乎、微信公众号、百度百科

---

## 一、个人背景

- **身份**：哥伦比亚人🇨🇴，现居德国柏林🇩🇪，自学成才（homeschooled，未上过大学）
- **职业**：创建了 FastAPI、Typer、SQLModel、Asyncer 等开源项目；曾就职于 Explosion AI（spaCy 团队）、Forethought、Anyscale
- **GitHub**：https://github.com/tiangolo
- **个人网站**：https://tiangolo.com（极简个人页，几乎无内容）
- **Medium**：https://tiangolo.medium.com/
- **Dev.to**：https://dev.to/tiangolo

**来源**：GitHub 个人主页、Dev.to 个人页 | 可信度：★★★★★（一手）

---

## 二、已发布的博客文章列表

所有博客文章源码集中存放于：https://github.com/tiangolo/blog-posts

| # | 文章标题 | 平台 | 主题分类 |
|---|---------|------|---------|
| 1 | **Introducing FastAPI** | Medium, GitHub | 框架发布宣言 |
| 2 | **Concurrent Burgers: Understand async/await** | Dev.to, Medium, GitHub, FastAPI 官方文档 | 技术教育（异步编程） |
| 3 | **HTTPS for Developers** | Dev.to, Medium, GitHub, FastAPI 官方文档 | 技术教育（HTTPS/TLS） |
| 4 | **Deploying FastAPI apps with HTTPS powered by Traefik** | Dev.to, Medium, GitHub | 部署实践 |
| 5 | **Docker Swarm Mode and Traefik for an HTTPS cluster** | Medium, GitHub | 部署实践 |
| 6 | **Docker Swarm Mode and distributed Traefik proxy with HTTPS** | Medium, GitHub | 部署实践 |
| 7 | **Docker Swarm with Swarmprom for real-time monitoring and alerts** | Medium, GitHub | 部署/监控 |
| 8 | **How to start contributing to open source** | Dev.to, Medium, GitHub | 开源文化 |
| 9 | **The Future of Education and Art** | Dev.to, Medium, GitHub | 教育/社会评论 |
| 10 | **FastAPI top-level dependencies** | Dev.to, Medium, GitHub | 框架特性发布 |
| 11 | **The Future of FastAPI and Pydantic is Bright** | GitHub | 框架展望 |
| 12 | **Full Stack Flask Docker** | GitHub | 全栈模板 |
| 13 | **Angular in Docker** | GitHub | 部署实践 |
| 14 | **React in Docker** | GitHub | 部署实践 |
| 15 | **Stack Traefik Rancher** | GitHub | 部署实践 |
| 16 | **PyConBY: Web API from scratch with FastAPI** | GitHub | 演讲稿 |

**来源**：https://github.com/tiangolo/blog-posts | 可信度：★★★★★（一手仓库）

---

## 三、FastAPI 官方文档中的设计哲学部分（最重要的一手资料）

### 3.1 「Alternatives, Inspiration and Comparisons」页面

**URL**：https://fastapi.tiangolo.com/alternatives/

这是 tiangolo 最系统的设计哲学论述，逐个分析了 FastAPI 的灵感来源：

| 启发框架 | 从中学到的 | FastAPI 的对应 |
|---------|-----------|---------------|
| **Django** | 最流行的 Python 框架，但与关系型数据库耦合太紧，为 HTML 后端渲染设计 | 不绑定特定数据库，面向 API |
| **Django REST Framework** | 自动 API 文档 UI 的先例 | 采纳自动文档，采用 OpenAPI 标准 |
| **Flask** | 微框架理念，简洁路由系统 | 保持微框架哲学，可自由组合 |
| **Requests** | 极简直观的 API 设计（`requests.get()` → `@app.get()`） | HTTP 方法名直接作为装饰器 |
| **Swagger/OpenAPI** | API 规范标准 | 采用 OpenAPI + JSON Schema 而非自定义 schema |
| **Marshmallow** | 数据序列化/验证的 schema 概念 | 使用 Pydantic 替代（利用类型提示） |
| **Webargs** | 自动请求数据验证 | 内建验证 |
| **APISpec** | OpenAPI schema 生成 | 自动生成，无需 docstring YAML |
| **Flask-apispec** | 将 Webargs+Marshmallow+APISpec 串联 | 曾是 tiangolo 最喜欢的后端栈 |
| **NestJS/Angular** | 类型系统驱动编辑器支持 + 依赖注入 | 采纳 DI 系统，减少代码重复 |
| **Sanic** | asyncio 的极致性能 | 基于 Starlette（更快） |
| **Falcon** | 最小化高性能框架 | 启发声明 response 参数（可选） |
| **Molten** | 类型提示 + 验证 + DI（类似理念） | 用 default 值定义额外验证 |

### 3.2 「History, Design, and Future」页面

**URL**：https://fastapi.tiangolo.com/history-design-future/

关键设计过程描述：
- **调研阶段**：在开发 FastAPI 之前，花了几个月研究 OpenAPI、JSON Schema、OAuth2 等规范
- **设计阶段**：从用户（开发者）角度设计 API，在 PyCharm、VS Code、Jedi 编辑器中测试
- **覆盖 80% 用户**：根据 JetBrains Python 开发者调研，这些编辑器覆盖约 80% 的 Python 开发者
- **对 Pydantic 和 Starlette 做了大量贡献**，使其更好用

**来源**：FastAPI 官方文档 | 可信度：★★★★★（一手，tiangolo 亲自撰写）

---

## 四、「Introducing FastAPI」—— 框架发布宣言

**URL**：https://medium.com/@tiangolo/introducing-fastapi-fdc1206d453f

核心开场白（原文）：
> "I have been avoiding the creation of a new framework for several years. First I tried to solve all the features covered by FastAPI using many different frameworks, plug-ins, and tools. But at some point, there was no other option than creating something that provided all these features, taking the best ideas from previous tools, and combining them in the best way possible, using language features that weren't even available before (Python 3.6+ type hints)."

FastAPI 的八个核心卖点（原文直述）：
1. **Fast**：性能与 NodeJS/Go 持平
2. **Fast to code**：开发速度提升 200%-300%
3. **Fewer bugs**：减少约 40% 的人为错误
4. **Intuitive**：编辑器处处自动补全
5. **Easy**：设计为易学易用
6. **Short**：最小化代码重复
7. **Robust**：生产就绪，自动交互式文档
8. **Standards-based**：基于 OpenAPI 和 JSON Schema 开放标准

**来源**：Medium 原文 | 可信度：★★★★★（一手）

---

## 五、「The Future of Education and Art」—— 唯一的非技术长文

**URL**：https://dev.to/tiangolo/the-future-of-education-and-art-4h0e

这是 tiangolo 唯一一篇社会/教育评论长文，极其重要，揭示了他的世界观核心：

### 核心论点：
1. **传统教育体系是为工业革命设计的**（"designed to train people to adapt and work in the industrial revolution"）
2. **教育的物理位置限制构成隐性门禁**（"intrinsic gatekeeping"）
3. **在线教育平台（Coursera、Udacity）是教育民主化的关键**
4. **技术取代工作不是新问题，AI 只是工具**（"Technology, artificial intelligence, automation, are all just tools. It's like a knife."）
5. **年资（years of experience）是糟糕的能力代理指标**
6. **学习和应用应该同时开始，永不停止**（"start studying and start applying as soon as possible, and continue studying and applying forever"）

### 个人经历揭示：
- 哥伦比亚人，一生 homeschool，没上过大学（甚至没上过小学）
- 通过互联网和在线课程平台自学
- Andrew Ng（吴恩达）创办 Coursera 对他有重大影响
- 强调"我不是反对教育机构，我是反对限制信息访问的体系"

**来源**：Dev.to 原文 | 可信度：★★★★★（一手，最直接的个人信念表达）

---

## 六、「How to Start Contributing to Open Source」

**URL**：https://dev.to/tiangolo/how-to-start-contributing-to-open-source-1jmg

核心论点：
> **"Newbies are great at docs, better than maintainers."**

- 新手有维护者永远无法拥有的**新鲜视角**
- 新手在读文档时是按顺序、第一次读，能发现维护者忽略的问题
- 从文档开始贡献，是最有效的入门方式
- 每个改动保持独立 PR，最小变更，清晰范围

**来源**：Dev.to 原文 | 可信度：★★★★★（一手）

---

## 七、「Concurrent Burgers: Understand async/await」

**URL**：https://dev.to/tiangolo/concurrent-burgers-understand-async-await-3n20

用汉堡店的故事解释并发 vs 并行，是 tiangolo 最著名的教学文章之一：
- **并发（Concurrency）**：你点了汉堡，去和 crush 聊天，号码到了再去取 → I/O 密集型场景最优
- **并行（Parallelism）**：8 个收银员兼厨师同时做 → CPU 密集型场景
- 结论：并发 ≠ 并行更好，而是**不同场景适用不同方案**
- Web 开发主要是 I/O 等待，所以并发（async）更合适
- FastAPI = 并发（Web）+ 并行（ML），二者兼得

**来源**：Dev.to 原文，也被收录进 FastAPI 官方文档 | 可信度：★★★★★（一手）

---

## 八、自创术语与核心概念

### 8.1 框架命名与定位

| 概念 | 解释 | 来源 |
|-----|------|------|
| **"FastAPI and friends"** | 他将整个生态系统称为"FastAPI and friends"，包括 Typer、SQLModel、Asyncer、Full Stack Template 等 | FastAPI 官方文档、GitHub 组织名 |
| **"FastAPI of CLIs"** | Typer 的定位语——"Typer is FastAPI's little sibling" | Typer 官方文档 |
| **"Shoulders of giants"** | FastAPI 站在 Starlette（Web）和 Pydantic（数据）的肩膀上 | FastAPI 首页 |
| **"Standard Python type hints"** | 反复强调"just standard Python"，不用学新语法 | 多篇文章中反复出现 |
| **"Declare once"** | 一次声明类型，获得验证+文档+编辑器支持+序列化 | Introducing FastAPI、FastAPI 首页 |

### 8.2 设计原则（从文档中提炼）

1. **"Just standard Python"**：用标准 Python 类型提示，不发明新语法
2. **"Minimize code duplication"**：一个参数声明同时提供验证、文档、编辑器支持
3. **"Sensible defaults, powerful customizations"**：默认好用，但可深度定制（来自 Requests 的启发）
4. **"Standards-based"**：采用 OpenAPI 和 JSON Schema，不用自定义 schema
5. **"Micro-framework"**：保持 Flask 式的微框架哲学，不强制绑定
6. **"Editor support everywhere"**：所有设计都以编辑器体验为优先

---

## 九、反复出现 ≥3 次的核心论点（真信念）

| # | 论点 | 出现次数 | 出处 |
|---|------|---------|------|
| 1 | **Python 类型提示是万能钥匙**——验证、文档、编辑器支持、序列化全从类型提示派生 | ≥10 次 | FastAPI 文档、Typer 文档、SQLModel 文档、Introducing FastAPI、Alternatives 页面 |
| 2 | **不要重新发明轮子，站在巨人肩膀上** | ≥5 次 | FastAPI 首页、Alternatives 页面、History 页面、Introducing FastAPI |
| 3 | **代码重复是万恶之源**——每个声明应有多种功能 | ≥5 次 | FastAPI 首页、Typer 首页、SQLModel 首页、Introducing FastAPI |
| 4 | **采用开放标准而非自定义方案** | ≥4 次 | Alternatives 页面（OpenAPI、JSON Schema）、FastAPI 首页 |
| 5 | **好的默认值 + 强大的可定制性** | ≥3 次 | Alternatives 页面（Requests 启发）、FastAPI 文档 |
| 6 | **教育应更易获得，技术是工具不是威胁** | ≥3 次 | Future of Education、How to start contributing、个人经历 |
| 7 | **新手视角比专家更有价值（在文档方面）** | ≥3 次 | How to start contributing、FastAPI 文档设计哲学 |
| 8 | **Docker Swarm 比 Kubernetes 更适合中小团队** | ≥3 次 | 多篇 Docker 部署文章（"<200 开发者、<1000 台机器"） |

---

## 十、推荐的工具/框架/依赖（智识谱系）

### 10.1 FastAPI 直接依赖
- **Starlette**（Tom Christie 创建）— Web 层
- **Pydantic**（Samuel Colvin 创建）— 数据层
- **Uvicorn** — ASGI 服务器

### 10.2 从文档/文章中反复提及的工具
- **Docker** + **Docker Swarm**（大量部署文章）
- **Traefik**（"I have been a long-time fan of Traefik, even before creating FastAPI"）
- **Let's Encrypt**（免费 HTTPS 证书）
- **DigitalOcean / Linode / Vultr**（推荐的 VPS 提供商）
- **Name.com**（推荐的域名注册商）
- **Flask**（FastAPI 的前身栈，曾用 Flask + Flask-apispec + Marshmallow）
- **Click**（Typer 早期的底层依赖）
- **Rich**（Typer 当前依赖，终端美化）
- **SQLAlchemy**（SQLModel 的底层）
- **spaCy**（就职于 Explosion AI，NLP 工具）

### 10.3 影响他的人物/项目
- **Tom Christie**：创建了 Django REST Framework → Starlette → Uvicorn（FastAPI 的底层）
- **Andrew Ng**：创办 Coursera，tiangolo 视其为教育民主化的先驱
- **Samuel Colvin**：创建 Pydantic
- **Kenneth Reitz**：创建 Requests，其 API 设计风格直接影响了 FastAPI

---

## 十一、发现的矛盾/张力

### 11.1 "避免创建新框架" vs 实际创建了多个框架
- tiangolo 在 Introducing FastAPI 中说"I have been avoiding the creation of a new framework for several years"
- 但实际上他创建了 FastAPI、Typer、SQLModel、Asyncer 等至少 4 个框架/库
- **可能解释**：每个都是在现有工具无法满足需求时的"最后手段"，但他对"无法满足"的阈值可能比声明的更低

### 11.2 "微框架哲学" vs 生态系统膨胀
- FastAPI 受 Flask 启发，强调微框架、不强制绑定
- 但 "FastAPI and friends" 生态系统越来越庞大（Typer、SQLModel、Asyncer、FastAPI Cloud、Full Stack Template）
- **可能解释**：每个项目仍然是独立的、可选的，不是"全家桶"式捆绑

### 11.3 "Just standard Python" vs 大量自定义约定
- 反复强调"just standard Python type hints"
- 但实际使用了大量 FastAPI 特有的装饰器模式、依赖注入语法、Field() 等
- **可能解释**：类型提示本身是标准的，装饰器是 Python 标准特性，只是组合方式是 FastAPI 独创的

---

## 十二、未找到的信息

- **正式出版的书籍**：未发现 tiangolo 出版过任何纸质/电子书籍。他的"著作"就是 FastAPI 官方文档（极其详尽，本身就是一本书级别的内容）
- **学术论文**：无
- **付费课程**：无（但他的文档被多人用作免费教程基础）
- **系统性的个人博客**：tiangolo.com 几乎是空的，所有内容在 Medium/Dev.to/GitHub

---

## 十三、FastAPI 官方文档本身作为"著作"

FastAPI 官方文档（https://fastapi.tiangolo.com/）的特点：
- **以教程形式组织**，不是 API 参考手册
- **每个知识点都有最小可运行示例**
- **从入门到生产部署的完整路径**
- **多语言翻译**（AI + 人类协作翻译模式）
- **"Opinions" 页面**收集了 Microsoft、Uber、Netflix、Cisco 等公司的使用背书

这实际上是 tiangolo 最重要的"系统性长文"——一本持续更新的技术书籍。

---

## 十四、2025-2026 新动态

- **FastAPI Conf '26**：2026年10月28日，阿姆斯特丹，首届 FastAPI 专属会议
- **FastAPI mini documentary**：2025年底发布
- **FastAPI Cloud**：tiangolo 团队推出的部署平台，"primary sponsor and funding provider for FastAPI and friends open source projects"
- **PyCon TW 2025 keynote**：受邀作为 PyCon Taiwan 2025 主讲人
- **PyConES 2025 Sevilla**：受邀参加欧洲 Python 大会
- **从 Click 迁移**：Typer 从依赖 Click 迁移到自主实现

---

*调研结束。所有信息均来自 tiangolo 本人的一手文档或可信的官方来源。*

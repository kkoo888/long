# Harrison Chase 完整时间线

## 一、教育背景

| 时间 | 事件 | 来源 |
|------|------|------|
| ~2013-2017 | 就读哈佛大学（Harvard University），主修统计学与计算机科学双学位 | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/)、[LinkedIn](https://www.linkedin.com/pulse/harrison-chase-architecting-open-source-revolution-ai-girish-hukkeri-ar6nc) |
| 2017 | 毕业，获得文学/科学学士学位（Statistics & Computer Science） | [ictmirror.com](https://ictmirror.com/featured/harrison-chase-langchain/) |

**关键背景**：统计学+CS 的组合在 AI 时代极为稀缺——既理解数学基础，又能工程实现。这直接塑造了 LangChain "实用工程优先"的设计哲学。

---

## 二、职业经历（LangChain 之前）

### 2.1 Kensho Technologies（2017.7 — 2019.10）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2017年7月 | 加入 Kensho Technologies，领导实体链接（Entity Linking）团队 | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/)、[LinkedIn](https://www.linkedin.com/pulse/harrison-chase-architecting-open-source-revolution-ai-girish-hukkeri-ar6nc) |
| 2018年 | Kensho 被 S&P Global 以 5.5 亿美元收购（金融科技初创公司） | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/) |
| 2019年10月 | 离开 Kensho | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/) |

**核心工作**：将非结构化文本提及（unstructured text mentions）连接到结构化知识图谱（structured knowledge graphs）。这项"从混乱数据中提取结构化信息"的技术挑战，直接奠定了 LangChain 后来的检索与提取能力基础。

### 2.2 Robust Intelligence（2019.10 — 2022.10）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2019年10月 | 加入 Robust Intelligence，担任机器学习工程师 | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/) |
| 2020-2022 | 晋升为 ML 团队负责人，负责机器学习模型的测试与验证 | [segmentfault.com](https://segmentfault.com/a/1190000044311167) |
| 2022年某次公司黑客松 | 创建了一个能从 Notion 和 Slack 中查询内部数据的 Bot——这是 LangChain 的灵感来源 | [LinkedIn](https://www.linkedin.com/pulse/harrison-chase-architecting-open-source-revolution-ai-girish-hukkeri-ar6nc) |
| 2022年10月 | 离开 Robust Intelligence，全职投入 LangChain | [segmentfault.com](https://segmentfault.com/a/1190000044311167) |

**思想转折点 #1：从"ML 验证工程师"到"LLM 基础设施构建者"**
在 Robust Intelligence 的三年里，Chase 亲身体验了"研究原型与生产系统之间的鸿沟"——模型在 Notebook 里运行完美，部署后莫名其妙失败；调试需要逐条检查预测；监控需要为每个用例定制基础设施。这种"开发者日常痛点"的深刻理解，直接塑造了 LangChain 的设计哲学：**不追求前沿 AI 能力，而是构建决定能力能否变成产品的基础设施层。**

**思想转折点 #2：黑客松的顿悟**
在 Robust Intelligence 的黑客松中，Chase 用 LLM 构建了一个能查询 Notion/Slack 内部数据的 Bot。这次经历让他意识到：**LLM 已经跨过了生产可用的门槛，但开发者缺乏标准化工具来构建它们。每个团队都在重复造轮子——prompt 模板、输出解析、链式推理。基础设施层是一片空白。**

---

## 三、LangChain 创立与早期发展

### 3.1 从个人项目到开源（2022.10 — 2023.3）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2022年10月16-25日 | Chase 在个人公寓里用 9 天时间写出 LangChain 第一版——约 800 行代码的单文件 Python 包 | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/)、[InfoQ](https://www.infoq.cn/article/osepqr1riqdrou6uafhv) |
| 2022年10月24日 | 发布 LangChain v0.0.1 到个人 GitHub 账号 `hwchase17` | [CSDN](https://blog.csdn.net/2502_91116367/article/details/159551825) |
| 2022年11月30日 | OpenAI 发布 ChatGPT——LangChain 的时机窗口彻底打开 | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/) |
| 2023年2月 | GitHub stars 突破 5,000 | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/) |
| 2023年3月 | GitHub stars 达到 18,000（两个月增长 220%） | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/) |

**关键洞察**：LangChain 的发布时间（2022年10月）恰好在 ChatGPT 发布（2022年11月30日）之前一个月。当 ChatGPT 引爆 LLM 热潮时，LangChain 已经是唯一成熟的 LLM 应用开发框架——**时机窗口的精准把握是其爆发式增长的决定性因素。**

### 3.2 公司化与种子轮融资（2023.4）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2023年4月 | 完成 1000 万美元种子轮融资，Benchmark 领投 | [TechCrunch](https://techcrunch.com/2025/10/21/open-source-agentic-startup-langchain-hits-1-25b-valuation/)、[aifundingtracker.com](https://aifundingtracker.com/langchain-valuation-series-b/) |
| 2023年4月 | 正式成立公司，联合创始人 Ankush Gola 加入 | [百度百科](https://baike.baidu.com/item/LangChain/63778808) |
| 2023年4月 | 估值约 2 亿美元 | [LinkedIn](https://www.linkedin.com/pulse/langchain-becomes-unicorn-125b-valuation-expands-ai-agent-dubey-9eb0e) |

**思想转折点 #3：从"副业项目"到"风险投资支持的创业公司"**
Chase 在 2025 年的反思中描述了这个转变："一开始只是我个人 GitHub 上的一个副业，没有公司，没有计划，只有出于好奇的探索。"但社区的爆炸式增长迫使他做出选择：**要么保持个人项目，要么抓住这个历史性机遇。** 他选择了后者。

### 3.3 生态构建期（2023.7 — 2023.12）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2023年7月 | 发布 LangSmith——LLM 应用的可观测性平台（追踪、评估、调试、监控） | [juejin.cn](https://juejin.cn/post/7637368708902584358) |
| 2023年10月 | 推出 LCEL（LangChain Expression Language）——声明式管道语法 `chain = prompt \| llm \| parser` | [juejin.cn](https://juejin.cn/post/7637368708902584358) |

**商业模式的关键一步**：LangSmith 的发布标志着 LangChain 从纯开源框架向"开源+商业平台"模式的转型。**开源框架免费获取用户，LangSmith 提供企业级可观测性服务实现商业化。**

---

## 四、A 轮融资与 LangGraph 的诞生（2024）

### 4.1 架构拆包与 A 轮（2024.1 — 2024.2）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2024年1月 | 发布 LangChain v0.1.0 正式版，模块化拆分：`langchain-core`、`langchain`、`langchain-community` 及独立集成包 | [juejin.cn](https://juejin.cn/post/7637368708902584358) |
| 2024年1月 | 发布 LangGraph——状态驱动的编排框架（图式架构） | [CSDN](https://blog.csdn.net/weixin_51960949/article/details/160532957) |
| 2024年2月 | 完成 2500 万美元 A 轮融资，Sequoia Capital（红杉资本）领投 | [VentureBeat](https://venturebeat.com/ai/langchain-lands-25m-round-launches-platform-to-support-entire-llm-application-lifecycle) |
| 2024年2月 | 估值约 2 亿美元，累计融资达 3500 万美元 | [VentureBeat](https://venturebeat.com/ai/langchain-lands-25m-round-launches-platform-to-support-entire-llm-application-lifecycle) |

**思想转折点 #4：从"链式调用"到"图式编排"**
LangGraph 的发布是 Chase 技术思想的重大转向。早期 LangChain 的核心概念是"Chain"（链）——线性的 A→B→C 调用。但生产环境的 Agent 需要**循环、条件分支、状态管理、人机交互中断**。LangGraph 引入了"有状态图"（StateGraph）的概念，允许开发者以更底层、更灵活的方式编排 Agent 逻辑。

Chase 在 2025 年回顾中说："LangChain 横空出世时我们追求抽象与易用，但生产环境需要精细控制。"

### 4.2 LangGraph 独立演进（2024 年中）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2024年中 | LangGraph 开始独立演进，建立专门文档和代码仓库 | [gitcode.csdn.net](https://gitcode.csdn.net/69cbe45b54b52172bc660328.html) |
| 2024年8月 | LangChain GitHub stars 超过 83,000；LangGraph 超过 12,000 | [CSDN](https://blog.csdn.net/weixin_51960949/article/details/160532957) |

---

## 五、独角兽之路（2025）

### 5.1 Ambient Agents 与 Context Engineering（2025 上半年）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2025年初 | 提出"Ambient Agents"概念——Agent 从被动对话走向主动感知环境 | [知乎](https://zhuanlan.zhihu.com/p/1987961895190290867) |
| 2025年5月 | 在旧金山举办首届 Interrupt 大会——AI Agent 行业大会，800 人参会（来自 Cisco、BlackRock、JPMorgan、Harvey 等） | [langchain.com](https://www.langchain.com/blog/interrupt-2025-recap) |
| 2025年中 | 提出"Context Engineering"理念——从 Prompt Engineering 转向系统性管理 LLM 输入上下文 | [知乎](https://zhuanlan.zhihu.com/p/1987961895190290867) |

### 5.2 B 轮融资与独角兽（2025.7 — 2025.10）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2025年7月 | 完成 1 亿美元 B 轮融资，IVP 领投 | [sacra.com](https://sacra.com/c/langchain/)、[latenode.com](https://latenode.com/blog/langchain-funding-valuation) |
| 2025年7月 | 估值达 11 亿美元，正式跻身独角兽行列 | [sacra.com](https://sacra.com/c/langchain/) |
| 2025年10月 | B 轮扩展至 1.25 亿美元，估值达 12.5 亿美元；新投资者 CapitalG（谷歌旗下）、Sapphire Ventures 加入；现有投资者 Sequoia、Benchmark、Amplify 继续跟投 | [TechCrunch](https://techcrunch.com/2025/10/21/open-source-agentic-startup-langchain-hits-1-25b-valuation/) |
| 2025年10月 | 提出"Deep Agents"概念——支持长周期自主任务的 Agent 框架 | [知乎](https://zhuanlan.zhihu.com/p/1987961895190290867) |

### 5.3 LangChain 1.0 与 LangGraph 1.0（2025.10）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2025年10月20日 | 同步发布 LangChain 1.0 与 LangGraph 1.0 正式版 | [51CTO](https://www.51cto.com/aigc/8420.html) |
| 2025年10月 | LangChain 1.0 彻底重构：移除所有旧 Chain 和 Agent，仅保留基于 LangGraph 的 `create_agent()` 高层抽象 | [CSDN](https://blog.csdn.net/2502_91116367/article/details/159551825) |
| 2025年10月28日 | 发布 DeepAgents v0.2——构建在 LangChain 和 LangGraph 之上的开源智能体框架 | [CSDN](https://blog.csdn.net/fufan_LLM/article/details/155754713) |

**思想转折点 #5：从"框架"到"平台"**
LangChain 1.0 的发布标志着 Chase 思想的又一次重大转向。他不再把 LangChain 定义为"开发框架"，而是"Agent 工程平台"（The platform for agent engineering）。产品矩阵形成：
- **LangChain**：Agent 框架（砖块）
- **LangGraph**：Agent 运行时（地基）
- **DeepAgents**：Agent 套件/骨架（毛坯房）
- **LangSmith**：可观测性平台（物业管理系统）

**Chase 在 2025 年的原话**："LangChain 诞生于 2022 年秋天，一开始只是我个人 GitHub 上的一个副业，没有公司，没有计划，只有出于好奇的探索。"

### 5.4 2025 年底的里程碑数据

| 指标 | 数值 | 来源 |
|------|------|------|
| GitHub stars | 121,000+（LangChain）、21,900+（LangGraph） | [CSDN](https://blog.csdn.net/bagell/article/details/156451003) |
| 月下载量 | 8,000 万次 | [51CTO](https://www.51cto.com/aigc/8394.html) |
| 累计下载量 | 1.3 亿次+ | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/) |
| Fortune 500 覆盖 | 1/3 使用 LangChain 产品 | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/) |
| 代表客户 | Harvey（$50 亿法律 AI）、Rippling、Cloudflare、Workday | [genedai.me](https://genedai.me/2025/11/23/harrison-chase-langchain-ai-agent-framework-deep-analysis/) |

---

## 六、2025 年下半年至 2026 年上半年最新动态

### 6.1 "Agent 工程"元年宣言（2025.12 — 2026.1）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2025年12月 | LangChain v1.2.0 发布 | [CSDN](https://blog.csdn.net/lsylovejava/article/details/159431107) |
| 2025年12月8日 | LangChain-Google-GenAI v4.0 发布 | [CSDN](https://blog.csdn.net/lsylovejava/article/details/159431107) |
| 2026年1月 | Chase 在播客中提出核心判断：**"2026 年将成为 Agent 工程的分水岭"**——编程 Agent、Deep Research 等"长任务 Agent"将在 2025 年末到 2026 年进一步加速落地 | [InfoQ](https://www.infoq.cn/article/2XfMOshHpdVVKjB2hxms)、[腾讯新闻](https://news.qq.com/rain/a/20260131A03TQV00) |
| 2026年1月 | 提出"做智能体和做软件是不同的"——Agent 工程需要新范式 | [腾讯新闻](https://news.qq.com/rain/a/20260131A03TQV00) |

### 6.2 LangGraph v1.1 与 DeepAgents v0.4（2026.2 — 2026.3）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2026年2月10日 | DeepAgents v0.4 核心更新 | [CSDN](https://blog.csdn.net/lsylovejava/article/details/159431107) |
| 2026年3月10日 | LangGraph v1.1 核心更新 | [CSDN](https://blog.csdn.net/lsylovejava/article/details/159431107) |

### 6.3 "Harness"理念与播客密集输出（2026.1 — 2026.4）

| 时间 | 事件 | 来源 |
|------|------|------|
| 2026年1月 | 红杉资本 Sonya Huang & Pat Grady 访谈 Chase，发布播客《Your Harness, Your Memory》 | [CSDN](https://blog.csdn.net/weixin_45532305/article/details/161054252) |
| 2026年3月12日 | 做客 MAD 播客，核心观点：**"模型不再是主角，智能体时代的 Harness 正在重塑一切"** | [新浪财经](https://finance.sina.cn/cj/2026-04-11/detail-inhucfkz9504127.d.html) |
| 2026年3月 | LangSmith 升级为 LangSmith Suite，包含 Deployment（部署）与 Studio（开发）两大子产品 | [CSDN](https://blog.csdn.net/lsylovejava/article/details/159431107) |
| 2026年4月 | 推出 LangSmith MCP 和 LangSmith Fetch（CLI 工具） | [36kr](https://www.36kr.com/p/3658280070390407) |

**思想转折点 #6：从"框架思维"到"Harness 思维"**
Chase 在 2026 年初的核心判断发生了重大转变：

> "大模型正在沦为大宗商品（commodity），而决定 Agent 成败的，是那个包裹在模型外的 Harness（软件外壳）。"

他进一步提出：
- **Agent 的突破靠"有主见的软件外壳"，而非通用框架**
- **所有 Agent 都应具备文件系统权限，代码能力可能是通用 Agent 的本质**
- **Context Engineering 的质量决定了 Agent 的上限**

---

## 七、融资完整时间线

| 轮次 | 时间 | 金额 | 领投方 | 估值 | 来源 |
|------|------|------|--------|------|------|
| 种子轮 | 2023年4月 | $10M | Benchmark | ~$200M | [aifundingtracker.com](https://aifundingtracker.com/langchain-valuation-series-b/) |
| A 轮 | 2024年2月 | $25M | Sequoia Capital | ~$200M | [VentureBeat](https://venturebeat.com/ai/langchain-lands-25m-round-launches-platform-to-support-entire-llm-application-lifecycle) |
| B 轮 | 2025年7月 | $100M | IVP | $1.1B | [sacra.com](https://sacra.com/c/langchain/) |
| B 轮扩展 | 2025年10月 | $125M（总额） | IVP + CapitalG + Sapphire | $1.25B | [TechCrunch](https://techcrunch.com/2025/10/21/open-source-agentic-startup-langchain-hits-1-25b-valuation/) |

---

## 八、LangChain 产品演进时间线

| 时间 | 产品/版本 | 意义 |
|------|-----------|------|
| 2022年10月 | LangChain v0.0.1 | 800 行代码的单文件 Python 包 |
| 2023年7月 | LangSmith | 可观测性平台，商业模式起点 |
| 2023年10月 | LCEL | 声明式管道语法 |
| 2024年1月 | LangGraph | 状态驱动的图式编排框架 |
| 2024年1月 | LangChain v0.1.0 | 模块化拆分 |
| 2025年5月 | Interrupt 2025 | 首届行业大会 |
| 2025年10月 | LangChain 1.0 + LangGraph 1.0 | 正式版，架构统一 |
| 2025年10月 | DeepAgents v0.2 | 长周期自主 Agent 框架 |
| 2026年2月 | DeepAgents v0.4 | 架构升级 |
| 2026年3月 | LangGraph v1.1 | 核心更新 |
| 2026年3月 | LangSmith Suite | 部署+开发子产品 |

---

## 九、思想演进总结

### Chase 的六个思想转折点

1. **2019年（Robust Intelligence）**：从"ML 验证"转向"LLM 基础设施"——在黑客松中构建 Notion/Slack Bot 后意识到 LLM 的生产潜力
2. **2022年10月**：从"副业探索"转向"开源发布"——将 800 行代码推上 GitHub
3. **2023年4月**：从"个人项目"转向"风险投资支持的创业公司"——Benchmark 种子轮
4. **2024年1月**：从"线性链式调用"转向"图式编排"——发布 LangGraph，承认早期 Chain 抽象的局限
5. **2025年10月**：从"开发框架"转向"Agent 工程平台"——LangChain 1.0 的架构统一
6. **2026年初**：从"框架思维"转向"Harness 思维"——模型是大宗商品，包裹模型的软件外壳才是核心竞争力

### Chase 的职业轨迹揭示的模式

1. **时机窗口意识**：LangChain 发布于 ChatGPT 之前一个月——这种"提前卡位"不是运气，而是对技术趋势的敏锐判断
2. **实用主义优先**：统计学+CS 的背景让他始终关注"能不能用"而非"够不够新"——LangChain 解决的是开发者日常痛点，不是学术前沿
3. **开源+商业的双轮驱动**：开源框架获取用户→LangSmith 实现商业化→产品矩阵覆盖全生命周期
4. **快速迭代与自我否定**：从 Chain 到 LangGraph 到 DeepAgents，Chase 多次推翻自己的架构设计——"三次重写"是其核心特征
5. **从工具到平台到生态**：从单个 Python 包到完整产品矩阵（LangChain + LangGraph + DeepAgents + LangSmith），再到行业大会（Interrupt），逐步构建护城河

### Chase 的核心判断（2026 年）

> "2026 年是 Agent 工程的分水岭。模型正在成为大宗商品，决定 Agent 成败的不是模型本身，而是你围绕模型构建的 Harness 和你积累的 Memory。"

> "做智能体和做软件是不同的。Agent 的 Source of Truth 不再是代码，而是运行时的状态和记忆。"

> "所有通用 Agent 最终都会具备文件系统权限——代码能力可能是通用 Agent 的本质。"

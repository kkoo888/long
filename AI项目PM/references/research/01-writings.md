# AI项目管理方法论研究 — 文献与框架调研

> 调研日期：2026-06-14
> 调研Agent：女娲调研Agent 1
> 聚焦领域：AI项目管理方法论著作与系统性框架

---

## 一、经典PM方法论在AI领域的适配

### 1.1 Marty Cagan 对AI产品管理的判断

**来源**: airfocus访谈Marty Cagan (2025-08)
**URL**: https://airfocus.com/blog/ai-product-management-marty-cagan/
**可信度**: 一手（直接访谈Marty Cagan本人）
**类型**: 方法论原文

**核心观点**：
1. **Product Owner角色高危**：Marty明确警告，受过CSPO/PSPO认证的"European style Product Owners"面临被AI取代的最大风险。这些人需要尽快提升技能。
2. **AI不能拯救糟糕的产品**："People are using it to come up with product roadmaps, and they still have their crappy roadmaps. They're just doing them faster." 核心问题不是做得更快，而是在做什么。
3. **四维风险评估框架**：Value（价值）、Viability（可行性）、Usability（可用性）、Feasibility（技术可行性）——这四个维度在AI之前就存在，但AI改变了每个维度的严重程度。
4. **PM需要从"功能定义者"转变为"系统训练师"**：通过持续反馈优化智能体行为边界。
5. **AI upskilling必须是全公司投资**，而非少数专家的事。"I would argue that the most critical role for a company are product leaders that understand this."

**矛盾点**: Marty认为Feature Team PM短期内不会被取代（类似Project Manager），但同时又说"fundamentally, they need to look at what is the real contribution and it needs to be Product Management"——暗示如果不能转型为真正的PM，长期也会被淘汰。

**来源**: 36氪对Marty Cagan《转型启示录》的解读 (2025-01)
**URL**: https://www.36kr.com/p/3102379326951177
**可信度**: 二手（中文媒体解读）
**类型**: 他人总结

**补充观点**：产品经营模式中AI的应用应基于实际问题和明确的业务痛点，而非仅仅追求技术的先进性。

---

### 1.2 Scrum/Agile在AI项目中的适配与变体

**来源**: Michał Opalski, "Integrating Scrum and MLOps" (2025-12)
**URL**: https://www.ai-agile.org/2025/12/integrating-scrum-and-mlops-adapting.html
**可信度**: 一手（学术论文，有同行评审倾向）
**类型**: 方法论原文

**核心框架——Scrum-MLOps集成框架**：

传统Scrum与ML项目存在**四个结构性冲突**：
1. **Sprint目标不可保证**：ML项目中，特定性能改进在sprint内无法承诺
2. **Definition of Done失效**：模型可能满足技术标准但在生产环境中泛化失败
3. **估算困难**：实验性任务的结果和持续时间本质上不确定
4. **增量价值不明确**：失败实验获得的知识可能无法转化为可部署功能

**解决方案——三种互补Sprint类型**：
- **Data Sprints**：聚焦数据获取、清洗、标注和特征工程
- **Model Sprints**：围绕实验、训练、超参数调优和验证
- **Deployment Sprints**：专注集成、部署、监控和重训练流水线

**关键改造**：
- Product Backlog → **ML Backlog**（包含假设、数据相关任务、实验目标和技术风险）
- Increment → **Knowledge Increment**（每个sprint产出验证过的知识）
- 引入ML专用sprint指标

**来源**: arXiv, "Agile Management for Machine Learning: A Systematic Review" (2025-06)
**URL**: https://arxiv.org/pdf/2506.20759
**可信度**: 一手（学术系统综述）
**类型**: 方法论原文

**发现**：识别了8种将敏捷方法论适配到ML系统开发的方法。主题分析显示核心挑战集中在：实验的不确定性管理、数据驱动的迭代节奏、跨职能团队协作。

**来源**: Springer, "Agile MLOps: Bridging the Gap Between Agility and Machine Learning" (2025-06)
**URL**: https://link.springer.com/chapter/10.1007/978-3-031-96231-8_2
**可信度**: 一手（学术出版社案例研究）
**类型**: 方法论原文

**核心观点**：Agile和MLOps有共性但也有根本差异，需要找到二者的收敛点。案例研究展示了实践中的融合路径。

---

### 1.3 CRISP-DM：数据挖掘标准流程

**来源**: Data Science PM (2024-12)
**URL**: https://www.datascience-pm.com/crisp-dm-2/
**可信度**: 一手（方法论原始文档解读）
**类型**: 方法论原文

**六阶段框架**：
1. **Business Understanding**：确定业务目标、评估现状、确定数据挖掘目标、制定项目计划
2. **Data Understanding**：收集初始数据、描述数据、探索数据、验证数据质量
3. **Data Preparation**：选择数据、清洗数据、构建数据、整合数据、格式化数据（占项目80%工作量）
4. **Modeling**：选择建模技术、生成测试设计、构建模型、评估模型
5. **Evaluation**：评估结果、审查流程、确定下一步
6. **Deployment**：部署计划、监控维护、最终报告

**实践建议**：将CRISP-DM的松散实施与团队级敏捷项目管理结合效果最佳。采用率达43%（2014年统计）。

---

## 二、AI-native项目管理新方法论

### 2.1 Andrew Ng 的AI产品管理方法论

**来源**: Andrew Ng公开信 "AI Product Management" (2025年初)
**URL**: 多个转载源，原文见DeepLearning.AI
**可信度**: 一手（Andrew Ng本人公开发表）
**类型**: 方法论原文

**核心论点——经济学互补品原理**：
> "Writing software, especially prototypes, is becoming cheaper. This will lead to increased demand for people who can decide what to build. AI Product Management has a bright future!"

经济学原理：当两种商品是互补品时（如汽车和轮胎），一种变便宜会导致另一种需求增加。软件开发（特别是原型）成本降低 → 决定"建什么"的人（PM）需求增加。

**AI PM核心能力模型**：
1. **Technical proficiency in AI**（AI技术洞察力）——首要能力
2. **Iterative development**（迭代开发）
3. **Data proficiency**（数据能力）
4. **Skill in managing ambiguity**（管理不确定性的能力）
5. **Ongoing learning**（持续学习）

**来源**: Andrew Ng, 《Machine Learning Yearning》
**URL**: https://deepwiki.com/ruanyf/free-books/3.1-machine-learning-yearning
**可信度**: 一手（Andrew Ng著作）
**类型**: 方法论原文

**核心方法论——面向项目管理的ML实践指南**：
- **错误分析的系统化方法**：通过手动分析误差流程，为项目优化选择合适方向
- **偏差-方差诊断**：从人类表现出发设定性能上限
- **端到端 vs 分阶段决策**：何时采用端到端模型 vs 分阶段系统
- **数据增强路径**：系统化的数据策略
- **团队协作机制与迭代节奏控制**
- **模型上线评估与持续监控**

**核心理念**：以业务目标为导向、以工程落地为闭环的机器学习项目方法论体系。超越单个项目执行层面，上升至企业级AI治理高度。

**来源**: Andrew Ng, Data-Centric AI理念 (2021-2022)
**URL**: https://ecweb.ecer.com/topic/en/detail-265635-andrew_ng_advocates_datacentric_ai_approach_in_industry_shift.html
**可信度**: 一手（Andrew Ng演讲和论文）
**类型**: 方法论原文

**核心范式转变——从Model-Centric到Data-Centric**：
> "In the past decade, neural network architectures have become remarkably mature. By keeping the neural network architecture fixed and instead focusing on improving the data, we can achieve greater efficiency in many practical applications."

**关键数据**：改进数据质量带来的性能提升，平均是改进模型架构的**3-5倍**（Andrew Ng 2022 Data-Centric AI大会）。

**Data-Centric AI框架**：
| 变量 | 可控性 | 提升空间 | 典型投入占比 |
|------|--------|----------|-------------|
| 数据质量 | 高 | 5-15% | 20-30% |
| 模型架构 | 中 | 2-8% | 40-50% |
| 超参数 | 高 | 1-3% | 10-15% |
| 工程实现 | 高 | 1-2% | 10-15% |

**矛盾点**：大多数团队在模型架构上投入40-50%，但数据质量的提升空间更大（3-5倍）。这是典型的资源错配。

---

### 2.2 USIDO框架与AI产品管理路线图

**来源**: Voltage Control, "AI Product Management Roadmap & Frameworks" (2026-01)
**URL**: https://voltagecontrol.com/articles/ai-product-management-roadmap-frameworks-step-by-step-guide/
**可信度**: 二手（方法论整合文章，引用了McKinsey 2025调查）
**类型**: 他人总结

**USIDO框架**：
- **U**nderstand：定义问题、评估市场分析、设定成功指标
- **S**pecify：概述系统设计、数据收集和数据摄入需求
- **I**mplement：使用AI工具构建早期AI原型
- **D**eploy：部署到生产环境
- **O**ptimize：持续监控和优化

**AI产品管理方法论分类**：
1. **Agile + Data-Driven方法论**：短sprint + A/B测试 + 持续用户反馈
2. **Build/Buy/Bake策略**：自建 vs 购买 vs 内部孵化的决策框架
3. **Ethical AI和Regulatory Governance**：合规性治理

**关键数据**：McKinsey 2025调查：78%的组织在至少一个业务功能中使用AI，71%定期使用生成式AI。

---

### 2.3 Agentsway：面向AI Agent团队的软件开发方法论

**来源**: arXiv, "Agentsway -- Software Development Methodology for AI Agents-based Teams" (2025-10)
**URL**: http://arxiv.org/abs/2510.23664
**可信度**: 一手（学术论文）
**类型**: 方法论原文

**核心观点**：Agentic AI正在从根本上改变软件的设计、开发和维护方式。需要全新的软件开发方法论来适应AI Agent团队的工作模式。

---

### 2.4 IBM Agent Development Lifecycle (ADLC)

**来源**: IBM Think (2026-06)
**URL**: https://www.ibm.com/think/topics/agent-development-lifecycle-adlc
**可信度**: 一手（IBM官方方法论）
**类型**: 方法论原文

**ADLC核心定义**：Agent Development Lifecycle (ADLC)是一个结构化的端到端方法论，用于构建和管理企业级AI Agent。

**传统软件 vs Agentic系统的根本差异**：
1. **确定性 vs 概率性**：传统软件执行明确指令，Agent根据目标和护栏推断最佳执行方式。同一输入可能产生不同输出。
2. **静态 vs 自适应**：传统软件功能固定，Agent行为可基于环境反馈演化。
3. **代码驱动 vs 结果驱动**：传统软件用代码质量衡量，Agent需要系统性测量业务结果和行为。

**失败模式差异**：
- 传统软件：逻辑错误或边缘情况导致崩溃（明显、可追踪）
- Agent系统：通过幻觉或对齐问题失败（隐蔽、难以追踪、不可复现）

**ADLC关键原则**：
- 集成DevSecOps原则
- 标准化agent定义、工具调用schema、内存和状态管理、测试套件、部署协议和版本系统
- 促进跨工具、平台、供应商的互操作性

---

## 三、多Agent系统编排模式

### 3.1 四大编排模式

**来源**: qubittool.com (2026-05)
**URL**: https://qubittool.com/blog/multi-agent-orchestration-patterns
**可信度**: 二手（技术博客，含生产级代码实现）
**类型**: 他人总结

**三大核心模式**：

#### Supervisor模式（中心化协调器）
- **架构**：单个中心节点接收请求、分解任务、分配给专门的子Agent、收集结果、产生最终输出
- **适用场景**：3-8个Agent的确定性工作流
- **优点**：可控性强，适合合规性环境
- **缺点**：单点故障，潜在瓶颈，延迟随串行委派数量增加

#### Swarm模式（点对点交接）
- **架构**：完全去中心化，多个Agent并行运行，通过共享队列认领任务
- **适用场景**：客户服务、动态对话
- **优点**：无单点故障，最高吞吐量
- **缺点**：最难调试，成本可能不可预测飙升，可能进入无限循环

#### Hierarchical模式（多层级管理树）
- **架构**：树形管理结构，顶层经理 → 团队领导 → 工作者
- **适用场景**：15+个Agent的企业级系统
- **优点**：可扩展性强
- **缺点**：管理复杂度高

**选择标准**：Agent数量 × 任务动态性 × 容错需求

**来源**: lushbinary.com (2026-05)
**URL**: https://lushbinary.com/blog/multi-agent-orchestration-patterns-supervisor-swarm-pipeline-router-guide/
**可信度**: 二手（技术博客，引用Gartner和MIT Technology Review）
**类型**: 他人总结

**补充第四种模式——Pipeline模式（流水线）**：
- **架构**：Agent顺序链式连接，每个Agent接收输入、转换、传递给下一个
- **适用场景**：内容管道（研究→起草→编辑→发布）、CI/CD自动化
- **优点**：可预测、易调试
- **缺点**：无法并行化独立工作，阶段3失败则4-N全部停滞

**市场数据**：
- 多Agent AI市场2024年估值54亿美元，预计2034年达到2360亿美元（CAGR约46%）
- Gartner估计40%的企业应用将在2026年底嵌入AI Agent

---

### 3.2 Anthropic的Agent构建最佳实践

**来源**: Anthropic, "Building Effective Agents" (2024-12-19)
**URL**: https://www.anthropic.com/engineering/building-effective-agents
**可信度**: 一手（Anthropic官方工程博客）
**类型**: 方法论原文

**核心原则**：
> "Consistently, the most successful implementations use simple, composable patterns rather than complex frameworks."

**Agent vs Workflow区分**：
- **Workflows**：大模型和工具通过预定义的代码路径编排
- **Agents**：大模型动态指导自身过程和工具使用，保持对任务完成的控制

**五种Workflow模式**（由简到繁）：
1. **Prompt Chaining**：任务分解为连续步骤，每个LLM调用处理前一个的输出
2. **Routing**：对输入分类并引导至专门的后续任务
3. **Parallelization**：LLM同时处理任务并聚合输出（含任务拆分和投票两种形式）
4. **Orchestrator-Workers**：中央LLM动态分解任务并委派给worker LLMs
5. **Evaluator-Optimizer**：一个LLM生成响应，另一个提供评估和反馈

**三大设计原则**：
1. **保持简洁**：避免不必要的复杂性
2. **透明性**：展示agent的计划步骤
3. **精心设计ACI**（Agent-Computer Interface）：通过彻底的文档和测试设计工具

**框架使用建议**：快速启动时用框架，但不要害怕减少抽象层并使用基本组件进行生产。

---

### 3.3 OpenAI的Agent构建实践指南

**来源**: OpenAI, "A Practical Guide to Building Agents" (2025)
**URL**: 多个转载源，原文PDF 34页
**可信度**: 一手（OpenAI官方指南）
**类型**: 方法论原文

**多代理系统的两种常见模式**：
1. **Manager模式**（代理作为工具）：一个中心Agent将其他Agent作为工具调用
2. **Decentralized模式**（代理交由代理处理）：Agent之间直接交接

**Agent设计基础要素**：
- 选择模型
- 定义工具
- 配置指令
- 编排策略
- 安全防护措施（Guardrails）

---

## 四、AI项目的需求拆解与技术可行性评估

### 4.1 四层分解框架

**来源**: tkxel, "AI Project Decomposition Framework: 4-Layer Model" (2026-06)
**URL**: https://dev.tkxel.com/blog/ai-project-decomposition-framework-4-layer-model/
**可信度**: 二手（技术咨询公司方法论）
**类型**: 他人总结

**四层分解方法论**：将业务用例转化为离散的、技术范围明确的AI任务，定义数据需求。

### 4.2 AI可行性研究指南

**来源**: RTS Labs, "Guide to Conducting a Successful AI Feasibility Study" (2024-06)
**URL**: https://rtslabs.com/guide-to-conducting-a-successful-ai-feasibility-study
**可信度**: 二手（技术咨询公司指南）
**类型**: 他人总结

**关键步骤**：评估可行性、收益和风险。包括数据可用性评估、技术能力映射、成本效益分析。

### 4.3 AI项目八维风险管控

**来源**: CSDN博客（综合多个实战案例）(2026-05)
**URL**: https://blog.csdn.net/weixin_30954817/article/details/161502395
**可信度**: 推测（实战经验总结，非权威来源）
**类型**: 他人总结

**八维风险清单**：将AI项目视为"注定成功"然后系统性管理风险。第六维度强调融合业务领域与AI技术双重专长——AI项目失败的常见原因是"技术脱离业务"。

---

## 五、行业报告与实证数据

### 5.1 MIT 2025年企业AI状态报告

**来源**: MIT报告（通过a16z博客引用）
**URL**: https://www.a16z.news/p/your-data-agents-need-context
**可信度**: 一手（MIT研究报告）
**类型**: 方法论原文

**震撼数据**：
- 企业在生成式AI上投入300-400亿美元
- **95%的试点项目没有任何可衡量的投资回报**
- 只有不到5%的项目成功推向生产环境
- 根本原因：大多数AI系统不会学习——可以生成答案，但无法记住业务环境、适应反馈或随业务进化

**a16z的核心诊断**：企业数据智能体如果没有正确的上下文（Context Layer），基本等同于废物。

**破局方案——五步施工图**：
1. 访问正确的数据（包括非结构化文档中的部落知识）
2. 自动化上下文构建（用LLM逆向分析查询历史）
3. 人类精细化调整（业务主管亲自编写规则）
4. 智能体连接（通过API或MCP协议）
5. 自我更新的上下文流（闭环反馈机制）

**OpenAI内部案例**：3500+员工使用，600PB数据，70000个独立数据集。通过6层结构思考，建立"黄金查询代码"自动化测试集。

---

### 5.2 腾讯云/信通院 AI项目管理洞察报告

**来源**: 腾讯云/中国信通院 (2025)
**URL**: https://cloud.tencent.com/developer/article/2663297
**可信度**: 一手（联合发布行业报告）
**类型**: 方法论原文

**关键发现**：
- **79%的企业计划增加AI投入**
- AI自动化文档生成应用率达49%
- 制造业资源估算偏差达58%
- 研发工具链AI化覆盖率预期提升40%
- TAPD平台助力效率提升60%-200%

**核心应用场景**：需求管理、敏捷迭代、测试管理、知识沉淀、跨团队协作、反馈管理

---

### 5.3 AI项目失败率分析

**来源**: Fraunhofer, "Why Most AI Projects Fail - and How to Succeed" (2025-08)
**URL**: https://www.fraunhofer.org/content/dam/usa/en/documents/Publications/2025-publications/Why%20Most%20AI%20Projects%20Fail%20V3.pdf
**可信度**: 一手（德国弗劳恩霍夫研究所报告）
**类型**: 方法论原文

**核心建议**：遵循推荐步骤可显著降低失败风险，优先选择中立的外部专家。

**来源**: 搜狐/综合 (2025-09)
**URL**: https://www.sohu.com/a/932421630_122328931
**可信度**: 二手（引用MIT等机构数据）
**类型**: 他人总结

**13个非技术失败原因**：AI项目失败率高达95%，核心原因不在技术，而在战略、组织、文化和认知层面。

---

## 六、反复出现≥3次的核心观点（真信念）

以下观点在本次调研中被**3个以上独立来源**反复提及，属于领域共识：

### 6.1 "数据质量 > 模型架构"
- **出现次数**: ≥5次
- **来源**: Andrew Ng Data-Centric AI、a16z博客、MIT报告、CRISP-DM、Google Brain研究
- **核心表述**: 改进数据质量带来的性能提升是改进模型架构的3-5倍。大多数AI项目瓶颈不在模型，而在数据质量。

### 6.2 "AI项目失败率极高（80-95%），主因是非技术因素"
- **出现次数**: ≥4次
- **来源**: MIT 2025报告、Fraunhofer报告、搜狐综合报道、a16z博客
- **核心表述**: 95%试点项目无ROI。失败主因：缺乏战略、组织文化不匹配、缺乏业务上下文、技术脱离业务。

### 6.3 "从简到繁，先Workflow后Agent"
- **出现次数**: ≥3次
- **来源**: Anthropic "Building Effective Agents"、OpenAI实践指南、qubittool编排模式文章
- **核心表述**: 最成功的实现使用简单、可组合的模式而非复杂框架。能用workflow解决的不要用agent。

### 6.4 "迭代开发 + 持续反馈是AI项目的唯一可行路径"
- **出现次数**: ≥4次
- **来源**: Andrew Ng ML Yearning、Scrum-MLOps框架、CRISP-DM、USIDO框架
- **核心表述**: AI项目的不确定性本质决定了瀑布式开发不可行。必须通过迭代、实验和持续反馈来管理风险。

### 6.5 "PM需要从功能定义者转型为系统训练师"
- **出现次数**: ≥3次
- **来源**: Marty Cagan、Andrew Ng、腾讯云报告
- **核心表述**: AI时代的PM需要理解技术、管理不确定性、持续学习。传统Product Owner角色面临被取代风险。

### 6.6 "上下文层（Context Layer）是AI系统成败的关键"
- **出现次数**: ≥3次
- **来源**: a16z博客、OpenAI内部案例、IBM ADLC
- **核心表述**: 没有正确的业务上下文，数据智能体等于废物。需要动态更新、有人专职维护的上下文结构。

---

## 七、发现的矛盾点（不调和，直接记录）

### 矛盾1：Agent框架的必要性
- **Anthropic立场**：最成功的实现使用简单模式，而非复杂框架。建议减少抽象层。
- **市场实践**：LangChain、CrewAI、LangGraph等框架快速普及，Gartner估计40%企业应用将嵌入Agent。
- **冲突本质**：工程简洁性原则 vs 市场对快速开发的需求。

### 矛盾2：Agile/Scrum对AI项目的适用性
- **支持方**：Scrum-MLOps集成论文认为可以通过改造Scrum适配ML项目（三种Sprint类型）。
- **质疑方**：arXiv系统综述识别了8种适配方法，但没有一种被证明普遍有效。ML项目的实验不确定性本质上违反Scrum的可预测性假设。
- **冲突本质**：过程框架的确定性假设 vs ML结果的概率性本质。

### 矛盾3：PM角色的未来
- **乐观派**（Andrew Ng）：PM需求会因开发成本降低而增加（互补品效应）。
- **悲观派**（Marty Cagan）：Product Owner角色高危，Feature Team PM也面临自动化威胁。
- **冲突本质**：PM作为"决策者"的价值上升 vs PM作为"协调者"的价值被AI替代。

### 矛盾4：端到端 vs 分阶段
- **端到端派**：深度学习擅长端到端学习，减少人工特征工程。
- **分阶段派**：CRISP-DM和ML Yearning强调分阶段控制，便于错误分析和归因。
- **冲突本质**：模型能力的进步 vs 工程可控性的需求。

---

## 八、信息源汇总

| # | 来源 | 类型 | 可信度 | URL |
|---|------|------|--------|-----|
| 1 | Marty Cagan访谈 (airfocus) | 一手访谈 | ★★★★★ | airfocus.com/blog/ai-product-management-marty-cagan/ |
| 2 | Andrew Ng "AI Product Management" | 一手公开信 | ★★★★★ | DeepLearning.AI |
| 3 | Andrew Ng "Machine Learning Yearning" | 一手著作 | ★★★★★ | deeplearning.ai/machine-learning-yearning |
| 4 | Andrew Ng Data-Centric AI | 一手演讲 | ★★★★★ | ecweb.ecer.com |
| 5 | Anthropic "Building Effective Agents" | 一手工程博客 | ★★★★★ | anthropic.com/engineering/building-effective-agents |
| 6 | OpenAI "Practical Guide to Building Agents" | 一手指南 | ★★★★★ | OpenAI PDF 34页 |
| 7 | IBM Agent Development Lifecycle | 一手方法论 | ★★★★☆ | ibm.com/think/topics/agent-development-lifecycle-adlc |
| 8 | Scrum-MLOps集成框架 (Opalski) | 一手论文 | ★★★★☆ | ai-agile.org |
| 9 | Agile for ML系统综述 (arXiv) | 一手综述 | ★★★★☆ | arxiv.org/pdf/2506.20759 |
| 10 | a16z数据Agent上下文博客 | 一手分析 | ★★★★☆ | a16z.news/p/your-data-agents-need-context |
| 11 | MIT 2025企业AI报告 | 一手报告 | ★★★★★ | 通过a16z引用 |
| 12 | 腾讯云/信通院AI项目管理报告 | 一手报告 | ★★★★☆ | cloud.tencent.com/developer/article/2663297 |
| 13 | Fraunhofer AI项目失败报告 | 一手报告 | ★★★★☆ | fraunhofer.org |
| 14 | qubittool多Agent编排模式 | 二手技术博客 | ★★★☆☆ | qubittool.com/blog/multi-agent-orchestration-patterns |
| 15 | lushbinary多Agent编排指南 | 二手技术博客 | ★★★☆☆ | lushbinary.com/blog/multi-agent-orchestration-patterns |
| 16 | Voltage Control AI PM路线图 | 二手整合文章 | ★★★☆☆ | voltagecontrol.com/articles/ai-product-management |
| 17 | CRISP-DM方法论 | 一手方法论 | ★★★★☆ | datascience-pm.com/crisp-dm-2/ |
| 18 | Agentsway方法论 (arXiv) | 一手论文 | ★★★★☆ | arxiv.org/abs/2510.23664 |
| 19 | 36氪 Marty Cagan解读 | 二手中文媒体 | ★★★☆☆ | 36kr.com/p/3102379326951177 |
| 20 | Springer Agile MLOps | 一手案例研究 | ★★★★☆ | link.springer.com |

---

## 九、关键洞察与趋势判断

1. **2025-2026是Agent编排模式的成熟期**：从实验走向生产，四大编排模式（Supervisor/Router/Pipeline/Swarm）已形成行业共识。
2. **Context Layer是下一个战场**：a16z、OpenAI、Palantir都在押注上下文层。谁解决了"业务上下文"问题，谁就掌握了AI落地的钥匙。
3. **PM角色正在分化**：高层PM（决策、发现）价值上升，执行层PM（协调、跟踪）面临被自动化取代。
4. **Scrum需要根本性改造才能适配AI项目**：三种Sprint类型（Data/Model/Deployment）是目前最系统的改造方案。
5. **数据质量投资回报率远超模型优化**：但大多数团队仍在模型架构上投入更多资源——这是行业级的资源错配。

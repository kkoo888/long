# Harrison Chase 著作与系统性长文调研

> 调研时间：2026-06-06
> 信息来源：LangChain 官方博客、技术文章、设计文档
> 可信度：一手来源为主（Harrison Chase 亲自撰写）

---

## 1. 核心著作列表（一手来源）

### 1.1 "Reflections on Three Years of Building LangChain"（2025）
- **来源**：LangChain 官方博客
- **URL**：https://langchain-blog.ghost.io/three-years-langchain/
- **可信度**：一手，创始人亲自撰写
- **核心内容**：回顾三年构建历程，坦承早期封装过度、breaking changes 问题，以及从 LangChain 到 LangGraph 的架构演进逻辑

### 1.2 "The rise of context engineering"（2025）
- **来源**：LangChain 官方博客
- **URL**：https://langchain-blog.ghost.io/the-rise-of-context-engineering/
- **可信度**：一手
- **核心内容**：定义 context engineering 概念——构建动态系统，以正确的格式提供正确的信息和工具。从 prompt engineering → context engineering 的范式转变

### 1.3 "Your harness, your memory"（2026）
- **来源**：LangChain 官方博客
- **URL**：https://langchain-blog.ghost.io/your-harness-your-memory/
- **可信度**：一手
- **核心内容**：Agent harness 成为主流构建方式，harness 与 agent memory 深度绑定

### 1.4 "Deep Agents"（2025-2026）
- **来源**：LangChain 官方博客
- **URL**：https://langchain-blog.ghost.io/deep-agents/
- **可信度**：一手
- **核心内容**：用 LLM 调用工具循环是最简单的 agent 形式，这种架构产出的是"浅层" agent。Deep Agents 提出更深层的架构模式

### 1.5 "Not Another Workflow Builder"（2025）
- **来源**：LangChain 官方博客
- **URL**：https://langchain-blog.ghost.io/not-another-workflow-builder/
- **可信度**：一手
- **核心内容**：解释为什么 LangChain 坚决不做可视化工作流构建器。"从第一天起最常见的需求，但我们从未做"

### 1.6 "How and when to build multi-agent systems"（2025）
- **来源**：LangChain 官方博客
- **URL**：https://langchain-blog.ghost.io/how-and-when-to-build-multi-agent-systems/
- **可信度**：一手
- **核心内容**：回应 Cognition 团队"Don't Build Multi-Agents"和 Anthropic 的"How we built multi-agents"，给出自己的判断框架

### 1.7 "Continual learning for AI agents"（2026）
- **来源**：LangChain 官方博客
- **URL**：https://langchain-blog.ghost.io/continual-learning-for-ai-agents/
- **可信度**：一手
- **核心内容**：AI agent 的持续学习可以在三个层面发生——不只是更新模型权重

### 1.8 "How Coding Agents Are Reshaping Engineering, Product and Design"（2026）
- **来源**：LangChain 官方博客
- **URL**：https://langchain-blog.ghost.io/how-coding-agents-are-reshaping-engineering-product-and-design/
- **可信度**：一手
- **核心内容**：Coding Agent 如何重塑 EPD 角色分工

### 1.9 "The Hidden Metric That Determines AI Product Success"（2025-2026）
- **来源**：LangChain 官方博客（与 Assaf Elovic 合著）
- **URL**：https://langchain-blog.ghost.io/the-hidden-metric-that-determines-ai-product-success/
- **可信度**：一手
- **核心内容**：决定 AI 产品成功的隐藏指标

### 1.10 "In software, the code documents the app. In AI, the traces do."（2026）
- **来源**：LangChain 官方博客
- **URL**：https://langchain-blog.ghost.io/in-software-the-code-documents-the-app-in-ai-the-traces-do/
- **可信度**：一手
- **核心内容**：传统软件中代码是文档，AI 中 traces 才是文档

---

## 2. 核心论点提炼

### 论点 1：Harness > Framework（反复出现 ≥5 次）

**定义**：Harness 是包裹模型的软件外壳，决定何时加载 context、哪些工具可用、哪些动作被允许、如何处理失败。Framework 是开发工具，Harness 是运行时系统。

**来源证据**：
- "Your harness, your memory" 博客
- Sequoia Training Data 播客（2026.01）
- MAD Podcast（2026.03）
- 多篇中文分析引用

**Harrison Chase 原话引用**：
> "框架才是未来，模型终将走向商品化。"

**演进脉络**：
```
Prompt Engineering (2022-2024)
  → "怎么说，模型才懂？"
Context Engineering (2025)
  → "给模型什么信息？"
Harness Engineering (2026)
  → "怎么把活儿干成？"
```

**应用**：在 3D 动画平台项目中，Harness 对应的是 Agent 层的完整运行时——Tool 编排、状态管理、错误处理、进度推送，而不仅仅是 prompt 模板。

**局限**：这个理念可能过度押注"框架"形态，低估厂商原生 SDK 的侵蚀力（如 OpenAI Agents SDK）。

---

### 论点 2：Context Engineering 是 Agent 成败的关键（反复出现 ≥4 次）

**定义**：构建动态系统，以正确的格式提供正确的信息和工具。不是写好 prompt，而是设计整个 context 供给系统。

**来源证据**：
- "The rise of context engineering" 博客
- Sequoia 播客（2026.01）
- Interrupt 2025 主旨演讲

**Harrison Chase 观点**：
- Context 不只是 prompt，包括工具定义、历史消息、检索结果、系统状态
- Context engineering 是一个系统工程，不是单次优化
- 好的 context engineering 让小模型表现超过大模型

**应用**：在 3D 动画平台中，context engineering 对应的是如何把 VRM 模型信息、动画状态、用户历史偏好等动态组装成 Agent 的输入。

---

### 论点 3：图 > 链（LangGraph 的核心设计决策）

**定义**：线性 Chain 无法表达循环、条件分支、并行执行，而 Agent 工作流天然是图结构。

**来源证据**：
- LangGraph 设计文档
- "Not Another Workflow Builder" 博客
- 多次技术演讲

**设计决策背后的思考**：
1. 用户需要在 SOP（完全确定）和完全自主 Agent 之间找到中间地带
2. Agent 工作流需要状态持久化（checkpoint），Chain 不支持
3. interrupt/resume 机制需要图结构才能实现
4. 多 Agent 协作天然需要图（不是链）

**从 LangChain 到 LangGraph 的转变逻辑**：
- v0.1：链式编排，灵活但受限
- LangGraph（2024.01）：图式编排，支持循环/分支/并行
- 1.0（2025.10）：langchain 包完全基于 LangGraph 重建

**局限**：图结构增加了学习曲线，简单场景用链更直觉。

---

### 论点 4：Agent 应该从"浅层"走向"深层"

**定义**：用 LLM 调用工具循环是最浅层的 Agent 架构（ReAct），深层 Agent 需要更复杂的规划、记忆、反思机制。

**来源证据**：
- "Deep Agents" 博客
- "How and when to build multi-agent systems" 博客

**核心信念**：
- 单循环 ReAct Agent 只能处理简单任务
- 长任务（Long-Horizon Agent）需要：规划层、记忆层、反思层
- 2026 年是 Long-Horizon Agent 元年

**应用**：3D 动画平台的 Agent 处理的是长任务（30-120 秒的 Pipeline），需要 interrupt/resume、进度追踪、错误恢复——这些正是 Deep Agent 的特征。

---

### 论点 5：可观测性（Tracing）是 AI 应用的基础设施

**定义**：传统软件靠代码理解行为，AI 应用靠 traces 理解行为。

**来源证据**：
- "In software, the code documents the app. In AI, the traces do." 博客
- "From Traces to Insights" 博客
- LangSmith 产品线

**核心信念**：
- AI 应用的决策是非确定性的，不能只看代码
- Traces 是调试、优化、合规的基础
- 可观测性是持续付费的刚需（LangSmith 闭源商业化的核心逻辑）

**应用**：3D 动画平台需要在 Pipeline 各 Stage 埋 trace，用于调试 MotionBERT 输出质量、重定向精度等。

---

## 3. 自创术语和概念

| 术语 | 定义 | 首次出现 |
|------|------|---------|
| Harness | 包裹模型的运行时软件外壳 | 2026 年博客/播客 |
| Context Engineering | 构建动态系统供给正确的 context | 2025 年博客 |
| Long-Horizon Agent | 能长时间运行、产出初稿的 Agent | 2026 年 Sequoia 播客 |
| Deep Agent | 有规划/记忆/反思层的深层 Agent | 2025-2026 年博客 |
| Cognitive Architecture | Agent 的认知架构设计 | 多次演讲 |
| Orchestration Layer | Agent 的编排层 | 技术文档 |

---

## 4. 推荐的技术资源

- LangChain 官方文档：https://python.langchain.com/
- LangGraph 官方文档：https://langchain-ai.github.io/langgraph/
- LangChain 博客：https://blog.langchain.com/
- Harrison Chase Twitter/X：@hwchase17
- GitHub：https://github.com/langchain-ai/langchain

---

## 5. 矛盾与张力

### 张力 1：简化承诺 vs 持续迁移成本
- Chase 多次承认"过度封装"，承诺简化
- 但 v0.1 → v0.2 → 1.0 每次都是 breaking changes，社区迁移成本高

### 张力 2："不做可视化" vs 实际产品
- "Not Another Workflow Builder" 明确表态不做可视化
- 但后来推出了 LangGraph Studio（可视化调试工具）和无代码平台

### 张力 3：开源 vs 商业化
- LangChain 开源，但 LangSmith 闭源
- 社区质疑"开源引流、闭源变现"的信任问题

---

## 6. 信息来源汇总

### 一手来源（Harrison Chase 亲自撰写/演讲）
1. LangChain 官方博客系列（10+ 篇）
2. Sequoia Training Data 播客（2026.01）
3. MAD Podcast with Matt Turck（2026.03）
4. 吴恩达 × Harrison Chase 对话（2025.05-06）
5. Latent Space 播客（2023.09）
6. Interrupt 2025 大会主旨演讲（2025.05）
7. VentureBeat 播客（2026.04）

### 二手来源（他人总结/分析）
8. CSDN 技术分析文章（多篇）
9. 中文技术博客分析
10. CNCF 技术雷达评估

---

*调研完成时间：2026-06-06 00:05*
*信息可信度：高（一手来源为主）*

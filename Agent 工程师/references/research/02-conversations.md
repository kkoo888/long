# Harrison Chase 深度访谈与长对话调研

> 调研时间：2026-06-05
> 调研范围：2023-2026年间的深度访谈、播客、会议演讲
> 信息来源：一手访谈记录、会议演讲、播客对话

---

## 一、核心访谈清单

### 1.1 Sequoia Capital — Training Data 播客（2024年9月）
- **来源**：Sequoia Capital，主持人 Sonya Huang & Pat Grady
- **URL**：https://news.qq.com/rain/a/20240928A07NHO00（中文编译）
- **可信度**：★★★★★（一手访谈，Sequoia官方播客）
- **类型**：一手（Harrison Chase 直接发言）
- **时长**：深度长对话，涵盖 Agent 定义、认知架构、LangSmith/LangGraph、UX 等

### 1.2 Sequoia Capital — Long-Horizon Agents 专题（2026年1月）
- **来源**：Sequoia Capital，主持人 Sonya Huang & Pat Grady
- **URL**：https://news.qq.com/rain/a/20260127A07AYX00（中文编译）
- **可信度**：★★★★★（一手访谈，Sequoia官方播客）
- **类型**：一手
- **核心主题**：Long-Horizon Agents 爆发、Framework→Harness 演进、Coding Agent 是否是终局

### 1.3 MAD Podcast with Matt Turck（2026年3月12日）
- **来源**：The MAD Podcast with Matt Turck
- **URL**：https://news.qq.com/rain/a/20260402A03B7500（中文编译）；原视频 https://youtu.be/rSKh6bVuVZI
- **可信度**：★★★★★（一手访谈，Daytona Compute Conference 现场）
- **类型**：一手
- **核心主题**：Harness vs Model、System Prompt、Sub-Agent、File System、Skill、Context Compaction、Memory

### 1.4 吴恩达 × Harrison Chase 对话（2025年5-6月）
- **来源**：DeepLearning.AI / AI Fund
- **URL**：https://developer.volcengine.com/articles/7511527102538055743；原视频 https://www.youtube.com/watch?v=4pYzYmSdSH4
- **可信度**：★★★★★（一手对话，吴恩达官方渠道）
- **类型**：一手
- **核心主题**：Agent 构建的残酷真相、任务分解、评估机制、语音栈、MCP

### 1.5 Latent Space 播客 — The Point of LangChain（2023年9月）
- **来源**：Latent Space
- **URL**：https://www.latent.space/p/langchain
- **可信度**：★★★★★（一手访谈，AI工程师社区顶级播客）
- **类型**：一手
- **核心主题**：LangChain 的起源、设计哲学、模块化价值、应对批评

### 1.6 Interrupt 2025 大会主旨演讲（2025年5月）
- **来源**：LangChain 官方大会
- **URL**：https://developer.volcengine.com/articles/7510083772294496267
- **可信度**：★★★★★（一手，官方大会演讲）
- **类型**：一手
- **核心主题**：从原型到生产、Agent 工程师四大素质、LangGraph Platform GA

### 1.7 VentureBeat — Beyond the Pilot 播客（2026年4月）
- **来源**：VentureBeat
- **URL**：https://zhuanlan.zhihu.com/p/2026794733314294109
- **可信度**：★★★★（一手访谈，权威科技媒体）
- **类型**：一手
- **核心主题**：生产级 Agent 四大基石

### 1.8 网易报道 — 微软把 LangChain 塞进 GitHub（2026年4月）
- **来源**：网易
- **URL**：https://www.163.com/dy/article/KQCQKFQ105561FZH.html
- **可信度**：★★★（二手转述，但包含 Harrison Chase 原话引用）
- **类型**：二手为主，含一手引用

---

## 二、关键问题深度提取

### 2.1 面对「LangChain 太复杂了」批评时的回应

**Harrison Chase 的核心回应策略：承认复杂性，但重新定义价值**

> **一手引文**（Sequoia 2024.09）：
> "我认为开源项目确实解决了一些问题。其中一个主要问题是标准化不同组件的接口。这让我们可以与各种模型、矢量存储、工具、数据库等进行了大量集成。"
> 
> "在 LangChain 中，还有许多高层接口，可以轻松使用现成的 RAG 或 SQL、Q&A 等功能。此外，在低层次运行时，用于动态构建链条或有向图（DAGs）。"

**关键立场演变**：
1. **2023年（Latent Space 播客）**：Harrison 将 LangChain 定位为"composability"——模块化组合的价值，面对"LangChain is Pointless"的批评，他强调切换模型和组合组件的灵活性。
2. **2024年**：承认 LangChain 的高层抽象和低层运行时之间存在张力，推动 LangGraph 作为更灵活的编排层。
3. **2026年**：在一次播客中坦承 **"如果今天让我重新设计，我会少做 70% 的抽象"**（来源：人人都是产品经理 2026.05.07 二手转述，可信度 ★★★）。

**应对批评的方式**：
- **不直接反驳**，而是承认问题存在，然后将对话引导到"领域本身在快速演变"。
- 用 **"我们也在学习"** 的叙事框架：强调 LangChain 的演进本身就是对领域认知深化的体现。
- **用产品演进来回应**：从 LangChain → LangGraph → Deep Agents，每次演进都在回应前一代的不足。

> **一手引文**（Sequoia 2024.09）：
> "反思 LangChain 的演变是很有趣的。你知道当 Lang Chain 刚开始时，它实际上是 SOP 的组合。然后我们有了这个 Agent 执行类，它基本上是一个自主 Agent 的东西。然后我们开始在这个类中加入更多的控制。最终，我们意识到人们想要的灵活性和控制远比我们通过那个类提供的要多。"

**矛盾点**：
- Harrison 一方面强调 LangChain 的价值在于"标准化接口"和"集成生态"，另一方面又承认过多抽象是问题。这暗示他认为**集成价值是真实的，但封装方式可以更简洁**。

---

### 2.2 对 Agent 未来的看法 & AGI 路径

**核心观点：Agent 是 AI 的下一个范式，但不是通用 AGI，而是领域特定的"做事"能力。**

> **一手引文**（Sequoia 2024.09）：
> "Copilots 仍然依赖于人类在任务的决策和行动中的作用。所以在某种程度上，由外部系统完成的工作量是有一个上限的。从这个意义上，Copilots 有点限制。"

> **一手引文**（Sequoia 2026.01）：
> "让 LLM 在一个循环中运行并自主决策，这一直是 Agent 的核心理念。AutoGPT 就是这样……问题在于，当时的模型不够好，周围的 Scaffolding 和 Harness 也不够好。现在模型变强了……所以它们开始真正起作用了。"

**关于 AGI 的立场**：
- Harrison **从未直接讨论 AGI**，而是将话题引导到"Agent 能做什么具体的事"。
- 他引用 Sequoia 的表述 **"AGI 就是把事情搞定（Figure things out）的能力"**，但自己不做 AGI 定义的判断。
- 他更关注 **"Long-Horizon Agent"**——能长时间运行、自主规划、产出初稿的 Agent，而非通用智能。

> **一手引文**（Sequoia 2026.01）：
> "Agent 的问题在于达不到 99.9% 的 Reliability，但能做大量工作，并且能在更长的时间跨度上工作。需长时间运行，产出某项任务初稿的场景，就是 Long Horizon Agents 的杀手级应用。"

**对 Agent 成熟度的判断**：
- **2024年**：认为通用 Agent 仍然困难，需要更长上下文窗口、更好的规划和推理。
- **2026年1月**：认为拐点已到（2025年六七月份），Coding Agent 率先突破。
- **2026年3月**：认为模型正在"沦为大宗商品"，Harness 才是关键。

**回避的话题**：
- **AGI 时间线**：从未给出具体预测。
- **LangChain 的盈利能力/商业模式细节**：访谈中从不讨论收入数据。
- **与 OpenAI/Anthropic 的竞争关系**：虽然 LangSmith 和这些公司的产品有重叠，但他从未正面讨论竞争。

---

### 2.3 决策风格分析

**风格：快速迭代 + 社区驱动 + 模型能力感知**

1. **速度优先**：
   > Harrison 将研究想法快速转化为软件的能力是 LangChain 从第一天起的 DNA。2022年10月16-25日，几天内就发布了 LLM Math、Self-Ask、NatBot 等多个模式。（来源：Latent Space 2023.09）

2. **社区信号驱动**：
   - LangGraph 的诞生源于"我们看到成千上万的开发者用 LangChain 构建了令人惊叹的应用，但有一个模式我们反复遇到，用现有的链抽象很难实现：有状态的、多步骤的、循环的、多智能体的工作流。"（一手引文，Harrison Chase 博客）
   - Deep Agents 的诞生源于"我们看到了 Manus、Claude Code、Deep Research，发现它们都有这四个要素。我们就想，这很普遍啊。于是我们把它打包成一个 Python Package。"（MAD Podcast 2026.03）

3. **模型能力感知**：
   - Harrison 对模型能力的变化极其敏感。他明确指出 **2025年11-12月是拐点**（随着 Claude 新模型发布），并据此将战略从"Framework"转向"Harness"。
   > "大概在去年十一月、十二月的时候，随着一些最新的 Claude 模型的出现，模型真的变得非常强大了。"（MAD Podcast 2026.03）

4. **"做减法"的勇气**：
   - 从 LangChain 的高层抽象 → LangGraph 的低层控制 → Deep Agents 的强预设 Harness，每次都在做减法。
   - **"如果今天让我重新设计，我会少做 70% 的抽象"**（二手转述，但符合其产品演进逻辑）。

5. **数据 vs 直觉**：
   - Harrison 更偏向 **"社区信号 + 模型能力变化"** 驱动决策，而非纯数据驱动。
   - LangSmith 的 Trace 数据确实是他判断趋势的依据（"LangSmith 追踪到的 Trace 数量激增便是明证"），但重大战略转向（如 Framework → Harness）更多基于对行业格局的判断。

---

### 2.4 对竞品的态度

**核心立场：不直接评论竞品，而是用"行业趋势"的框架来间接回应。**

1. **对 CrewAI**：
   - Harrison **从未在公开访谈中直接提及 CrewAI**。
   - 但在讨论"多 Agent 系统"时，他的观点与 CrewAI 的"角色扮演"范式形成对比——他更强调 LangGraph 的"可控性"和"定制化认知架构"。
   - 二手来源称他将 CrewAI 归类为"AI 公司化"路线，但这不是他的直接发言。（来源：人人都是产品经理 2026.05.07）

2. **对 AutoGen**：
   - 同样**未在公开访谈中直接提及**。
   - 他的叙事框架是"LangChain 是第一个将 LLM 与工具和操作连接起来的产品"，暗示先发优势。

3. **对 Manus、Claude Code**：
   - **直接提及且态度正面**。
   > "Manus 是一个面向终端用户的产品，但它的 Harness 做得非常出色。那才是它成功的秘诀。"（MAD Podcast 2026.03）
   > "Claude Code 之所以如此火爆，很大一部分原因在于它的 Harness 设计。"（Sequoia 2026.01）

4. **对 Anthropic**：
   - 频繁提及，态度尊重但保持距离。
   > "我问过他们几次，但都没有得到一个明确的答复。"（关于 Claude Code 的 Tool 与模型 RL 训练不一致的问题，MAD Podcast 2026.03）

5. **对模型厂商**：
   > "模型正在沦为大宗商品。"（MAD Podcast 2026.03）
   > "Harness 才是最关键的东西。"（MAD Podcast 2026.03）

**回避策略**：
- Harrison 的标准策略是：**不评论竞品的具体产品，而是将它们作为"行业趋势"的例证**。当被问到竞品时，他会说"这是一个很好的例子"，然后转向讨论通用的架构原则。
- 他**从不贬低竞品**，也**从不承认竞品的威胁**。

---

### 2.5 最引以为豪的技术决策

**Harrison 从未直接回答"最引以为豪的决策"这个问题**，但从访谈中可以推断出以下核心决策：

1. **创建 LangChain（2022年10月）**：
   - 将 LLM 与工具和操作连接起来，定义了"AI 应用开发框架"这个品类。
   > "Harrison 是 Agent 生态系统中的传奇人物，他是第一个将 LLM 与工具和操作连接起来的产品设计大师。"（Sequoia 主持人介绍）

2. **推出 LangGraph**：
   - 从高层抽象转向低层可控编排，回应了"LangChain 太复杂/太受限"的批评。
   > "我们看到人们想要的灵活性和控制远比我们通过那个类提供的要多。所以最近，我们在 LangGraph 上投入了很多。"（Sequoia 2024.09）

3. **推出 LangSmith**：
   - 在"LLM 是非确定性的"这个判断下，押注可观测性和测试。
   > "从一开始我们就注意到，我们将 LLM 放在系统中心但是 LLM 是非确定性的。因此你必须有良好的可观察性和测试。"（Sequoia 2024.09）

4. **推出 Deep Agents（2025年底-2026年初）**：
   - 将 Manus/Claude Code/Deep Research 的共同模式抽象为 Harness。
   > "我们看到了 Manus、Claude Code、Deep Research，发现它们都有这四个要素。我们就想，这很普遍啊。"（MAD Podcast 2026.03）

5. **坚持开源**：
   - LangChain 始终保持开源，即使面对"开源如何盈利"的质疑。
   > "我们不想成为另一个被大厂钦点后就变味的项目。"（网易报道 2026.04，二手转述）

---

## 三、关键语录集

### 3.1 关于 Agent 定义
> "Agent 是让 LLM 决定应用的控制流。" —— Harrison Chase，Sequoia 2024.09（一手）

### 3.2 关于认知架构
> "认知架构只是一个花哨的说法，它实际上是从用户输入到用户输出，沿途发生的 LLM 调用的信息数据流的过程。" —— Harrison Chase，Sequoia 2024.09（一手）

### 3.3 关于定制 vs 通用
> "思考定制认知架构的一种方式是，你基本上把计划的责任从 LLM 转移到人类身上。" —— Harrison Chase，Sequoia 2024.09（一手）

### 3.4 关于 Harness 的重要性
> "聪明的模型遍地走，但能干活的架构万里挑一。" —— Harrison Chase，MAD Podcast 2026.03（一手）

### 3.5 关于 Coding Agent
> "我大概 90% 确信这是标配。对于 Long Tail 的复杂用例，Coding 能力是无可替代的。" —— Harrison Chase，Sequoia 2026.01（一手）

### 3.6 关于文件系统
> "我是坚定的 'File System Pilled'。我认为某种形式上，所有 Agent 都应该能访问文件系统。" —— Harrison Chase，Sequoia 2026.01（一手）

### 3.7 关于 Memory
> "Memory 是真正的 Moat。我们到了 LLM 可以查看 Traces 并修改代码/指令的节点。" —— Harrison Chase，Sequoia 2026.01（一手）

### 3.8 关于开发者的资产
> "开发者不要迷信任何框架（包括 LangChain 自己），因为架构层正在以周为单位迭代。真正具有穿越周期价值的资产，是深埋在业务逻辑里的 Instruction、Tool 和 Skill。" —— Harrison Chase，MAD Podcast 2026.03（一手）

### 3.9 关于沟通
> "沟通是生活中最难的事情——它是创业最难的部分，是人际关系最难的部分，也是与 Agent 协作时最难的部分。" —— Harrison Chase，MAD Podcast 2026.03（一手）

### 3.10 关于 Jeff Bezos 的引用
> "专注于让你的啤酒味道更好。" —— Harrison Chase 引用 Bezos，Sequoia 2024.09（一手，Harrison 用此比喻公司应专注核心业务而非构建基础设施）

---

## 四、立场变化与矛盾记录

### 4.1 从 LangChain 到 LangGraph 的转变

**时间线**：
- **2022-2023**：LangChain = 高层抽象 + 组合性（"composability"）
- **2024**：推出 LangGraph = 低层可控编排（承认高层抽象不够灵活）
- **2025-2026**：推出 Deep Agents = 强预设 Harness（从"无预设"到"有预设"）

**转变原因**（一手）：
> "最终，我们意识到人们想要的灵活性和控制远比我们通过那个类提供的要多。"（Sequoia 2024.09）

**矛盾点**：
- LangGraph 最初的设计理念是 **"Unopinionated"（无预设）**，但 Deep Agents 的理念是 **"Opinionated"（强预设）**。
- Harrison 对此的解释是：以前的"特异性"转移到了 Prompt 和 Instructions 中，Harness 本身的结构保持固定。
- 这实际上是在承认：**早期的抽象层过多，现在将复杂度转移到了自然语言层**。

### 4.2 关于模型 vs Harness 的权重

**2024年**：承认模型能力是关键瓶颈。
> "通用 Agent 仍然很困难。我们需要更长的上下文窗口，更好的计划，更好的推理。"（Sequoia 2024.09）

**2026年**：转向强调 Harness 的重要性。
> "模型正在沦为大宗商品……Harness 才是最关键的东西。"（MAD Podcast 2026.03）

**矛盾点**：
- 如果模型真的在"沦为大宗商品"，为什么 Harness 的设计需要"深刻理解模型训练数据"？
- Harrison 自己也承认："Harness 某些部分确实与模型，或者说与模型家族，深度绑定。"
- 这暗示他的"商品化"论述更多是**战略定位**（LangChain 卖 Harness，不卖模型），而非完全的技术判断。

### 4.3 关于抽象的价值

**早期立场**（2023）：抽象是核心价值。
> "LangChain 的核心价值在于 composability。"（Latent Space 2023.09）

**后期立场**（2026）：抽象过多是问题。
> "如果今天让我重新设计，我会少做 70% 的抽象。"（二手转述，2026.05）

**矛盾点**：
- 这两个立场之间存在明显张力。
- Harrison 的调和方式是：**抽象本身没问题，问题是抽象的层级和粒度**。早期的抽象太"高"了，现在需要更"低"层的抽象。

---

## 五、未覆盖的搜索方向

以下方向在本次调研中未找到相关内容：

1. **Lex Fridman 播客**：未找到 Harrison Chase 上过 Lex Fridman 的记录。
2. **a16z 访谈**：未找到 a16z 与 Harrison Chase 的深度访谈。
3. **GDC/游戏相关**：Harrison Chase 的访谈主要集中在 AI/Agent 领域，未涉及游戏。
4. **拒绝回答的问题**：在公开访谈中，Harrison 的回避策略非常成熟——他不是拒绝回答，而是将问题重新框架化。唯一明确"没有得到答复"的是关于 Anthropic 内部决策的问题。

---

## 六、可信度分级说明

| 等级 | 含义 | 示例 |
|------|------|------|
| ★★★★★ | 官方渠道一手访谈，视频/音频可查 | Sequoia 播客、MAD Podcast、吴恩达对话 |
| ★★★★ | 权威媒体一手访谈，有完整记录 | VentureBeat、Latent Space |
| ★★★ | 二手转述，但包含原话引用或可交叉验证 | 网易、知乎专栏、人人都是产品经理 |
| ★★ | 二手分析，原话来源不明确 | 部分中文编译文章 |
| ★ | 推测性内容 | 无 |

---

## 七、Harrison Chase 的表达 DNA 总结

### 7.1 语言风格
- **工程化表达**：偏好精确的技术术语（Harness、Primitives、Context Engineering），避免模糊表述。
- **类比丰富**：常用类比来解释抽象概念（"啤酒味道更好"、"乐高积木"、"收件箱"）。
- **承认不确定性**：频繁使用"I don't know"、"I'm not sure"、"This is a good question"。
- **不攻击竞品**：从不直接批评其他公司或产品。

### 7.2 叙事框架
- **"演进叙事"**：总是将当前产品放在"从 A 到 B 到 C"的演进线中。
- **"行业趋势"框架**：将个人决策包装为对行业趋势的响应。
- **"我们也在学习"**：将批评转化为"领域在快速演变"的叙事。

### 7.3 回避策略
- **重新框架化**：将尖锐问题转化为更宏观的讨论。
- **用例替代理论**：被问到抽象问题时，用具体案例来回应。
- **"这个问题很好"**：用积极的开场白缓冲，然后给出间接答案。

# 02 - AI项目管理领域专家长对话、访谈与演讲调研

> 调研时间：2026-06-14
> 调研范围：AI产品/项目负责人深度访谈、Agent框架开发者理念分享、AI PM方法论讨论
> 信息标注规则：🟢 专家原话 | 🟡 记者/编辑总结 | 🔴 发现的矛盾点

---

## 一、Andrew Ng（吴恩达）— AI项目管理的核心方法论

### 1.1 YC Startup School 演讲：用AI更快地构建（2025年6月）

**来源**: [Alan Hou 博客总结](https://alanhou.org/blog/yc-andrew-ng-building-faster-ai/) | [singjupost transcript](https://singjupost.com/andrew-ng-building-faster-with-ai-transcript/)
**可信度**: ⭐⭐⭐⭐⭐（一手演讲，多源交叉验证）

**核心观点**：

🟢 **速度是创业成功最强预测指标**：「我从未见过一个技术娴熟的团队所能达到的执行速度。它比那些较慢的企业所知道的任何做事方式都要快得多。」（Andrew Ng 原话）

🟢 **AI技术栈五层模型**：半导体 → 云基础设施 → 基础模型 → Agentic编排 → 应用层。应用层是创业公司最大的机会。

🟢 **AI Fund模式**：每月创建约一个新创业公司，采用venture studio模式快速实验和迭代。

🟡 **两大成功预测指标**：(1) 速度 (2) 技术深度。市场营销、销售、定价等知识相对普及，真正稀缺的是对技术如何运作的深刻理解。

### 1.2 LangChain Interrupt 炉边对话：与 Harrison Chase 讨论 AI Agent（2025年5月）

**来源**: [腾讯云开发者社区](https://cloud.tencent.com/developer/article/2652105)
**可信度**: ⭐⭐⭐⭐⭐（活动官方内容，中文翻译版）

**核心观点**：

🟢 **告别定义之争**：「我们作为一个社区，如果能接受事物具有不同程度的'智能体特性'（agenticness），将会发展得更好。无论你想构建一个具有少量自主性还是大量自主性的智能体系统，都没问题。无需浪费时间争论它是否'真正'是一个 agent。」

🟢 **线性工作流是低垂果实**：「许多商业场景中，现有的流程是这样的：员工查看网站表单，进行网站搜索，检查其他数据库以确认合规性或客户风险，然后复制粘贴信息……这些商业流程中，实际上存在大量相当线性的工作流。」

🟢 **「乐高积木」理论**：成功的 Agent 构建者就像乐高积木的组装者，能够快速组合 RAG、Evals、Guardrails、记忆系统等专业工具，而不是一切从零开始。「如果你只有紫色的乐高积木，你搭建不出太多有趣的东西。」

🟢 **Evals 被严重低估**：「我发现很多团队过于依赖人工评估，每次更改后都要花费大量时间查看输出来进行审查。」建议哪怕是20分钟内快速搭建一个不太完善的评估，也能有效补充人工评估。

🟢 **语音应用被严重低估**：「从应用角度看，文本输入框对很多用户来说是有威慑力的。」语音交互降低使用门槛。团队探索了「预录制插入语」技巧掩盖延迟，以及在客服场景中播放呼叫中心背景噪音提高用户对延迟的容忍度。

🟢 **Vibe Coding 正名**：「坦白说，当我使用 AI 编程系统进行一整天编程后，我会非常疲惫。这是一项深度智力活动。」

🟢 **人人都应学习编程**：在 AI Fund，包括前台、CFO和法务总顾问在内的每个人都会编程。「并非要他们成为软件工程师，而是因为学习少量编程能让他们更好地告诉计算机自己想做什么。」

### 1.3 Andrew Ng 的 Agentic AI 课程核心理念

**来源**: [DeepLearning.AI 课程](https://www.deeplearning.ai/courses/agentic-ai) | [课程笔记](https://notes.36sjs.com/pages/ai/andrew_agentic_ai.html)
**可信度**: ⭐⭐⭐⭐⭐（官方课程内容）

🟡 **四大设计模式**：反思（Reflection）、工具使用（Tool Use）、规划（Planning）、多智能体协作（Multi-agent Collaboration）

🟡 **核心教学理念**：Agentic AI 工作流是基于 LLM 的应用通过执行多个步骤来完成任务的过程。强调从简单的线性工作流开始，逐步增加复杂度。

---

## 二、Sam Altman — OpenAI 的产品与管理哲学

### 2.1 Big Technology Podcast 访谈（2025年12月）

**来源**: [腾讯新闻](https://news.qq.com/rain/a/20251220A05C6000) | [网易](https://www.163.com/dy/article/KH4UGBL405198NMR.html)
**可信度**: ⭐⭐⭐⭐（多源报道，记者总结为主）

🟡 **「代码红色」应急策略**：将竞争威胁视为「类疫情事件」，以6-8周为周期快速优化。

🟡 **核心优势不是模型而是系统**：「模型 + 产品 + 基施」的协同体系。分销、品牌与产品体验将成为关键竞争维度。

🟡 **企业业务成为2026年核心**：「消费者胜利带动企业转化」— 员工个人场景的ChatGPT使用习惯会自然迁移至工作场景。

🟡 **产品形态变革**：当前文本聊天界面「只是研究预览级产品」，未来将进化为「多模态交互 + 主动式服务」。

🟡 **未来角色定义**：「AI将重塑工作形态，但不会消灭人类意义感。未来角色可能是'管理AI团队的人'，而非被替代者。」

### 2.2 红杉资本 AI 活动访谈（2025年6月）

**来源**: [小宇宙播客解读](https://www.xiaoyuzhoufm.com/episode/683e85ef38dcc57c64474722)
**可信度**: ⭐⭐⭐⭐（播客解读版）

🟡 **产品理念**：让研究引领产品，产品引领销售。

🟡 **管理哲学**：讨论了OpenAI的发展历程、战略决策、组织管理。

### 2.3 多场访谈综合梳理（2023-2025）

**来源**: [虎嗅网](https://m.huxiu.com/article/4795433.html)
**可信度**: ⭐⭐⭐⭐（综合梳理，引用了13+场原始访谈）

🟡 **采访时间线覆盖**：2023年2月 Lex Fridman → 2023年5月 国会听证 → 2024年3-6月 多档播客 → 2025年3-10月 密集访谈期。

🟡 **关键立场变化**：从非营利到有限盈利再到完全盈利化的转变过程在访谈中有迹可循。

---

## 三、Dario Amodei — Anthropic 的 AI 产品开发观

### 3.1 最新专访：Claude新功能几乎完全由AI自主开发（2026年5月）

**来源**: [百家号/百度](https://baijiahao.baidu.com/s?id=1865422428104492553) | [雪球](https://xueqiu.com/6600079272/392379173)
**可信度**: ⭐⭐⭐⭐（多源报道，一手采访）

🟢 **AI自主开发**：Claude Code的最新功能（Claude Co-work）几乎完全由AI自主开发。（Amodei 原话）

🟡 **经济规律打破**：传统经济规律正在被打破，人类社会将首次面临「高GDP增速与高失业率并存」的局面。

🟡 **AI能力演进**：公众对AI的情绪总是在两极之间摇摆，但实际上AI能力的演进一直保持着平滑的指数级跃升。

### 3.2 2025年初独家采访：27年AI超越人类

**来源**: [36氪](https://36kr.com/p/3134131984472583) | [知乎](https://zhuanlan.zhihu.com/p/19847697832)
**可信度**: ⭐⭐⭐⭐（多源报道一致）

🟡 **2025年核心目标**：打造「AI同事」（virtual collaborator），编写和测试代码。

🟡 **规模预测**：Anthropic到2026年将运行超过100万个GPU。

🟡 **产品路线**：Claude将上线「双语音」模式、更好的记忆功能。

### 3.3 Dreamforce 2025 与 Marc Benioff 对话

**来源**: [夸智网](https://www.kuazhi.com/post/716400535.html)
**可信度**: ⭐⭐⭐⭐（官方活动记录）

🟡 **Anthropic 起源**：Amodei 和联合创始人们是最早研究和记录 AI 技术「指数级扩展」趋势的人。

🟡 **负责任的治理**：强调确保 AI 成为向善力量所需的领导力。

### 3.4 CNBC 访谈（2026年1月）

**来源**: [百家号/百度](https://baijiahao.baidu.com/s?id=1855013065158078535)
**可信度**: ⭐⭐⭐⭐（权威媒体采访）

🟡 **差异化策略**：聚焦企业端业务，而非消费端竞争。

---

## 四、Harrison Chase（LangChain）— Agent 框架设计理念

### 4.1 「Your Harness, Your Memory」博客 + 红杉资本访谈（2026年初）

**来源**: [LangChain 官方博客](https://blog.langchain.com/your-harness-your-memory/) | [CSDN 总结](https://blog.csdn.net/weixin_45532305/article/details/161054252) | [新浪财经](https://finance.sina.cn/2026-04-13/detail-inhukeex9670190.d.html)
**可信度**: ⭐⭐⭐⭐⭐（创始人一手博客 + 一手访谈）

**核心判断**：

🟢 **Harness > Framework**：「Agent 的突破靠'有主见的软件外壳'，而非通用框架。」（Harrison Chase 原话）

🟢 **Memory 是真正的 Moat**：「Memory 可能是真正的护城河。即使 prompt 和工具完全相同，没有 memory 的新 Agent 体验断崖式下降。」

🟢 **Source of Truth 变了**：「Agent 的 Source of Truth 不再是代码，而是代码 + Traces。」

🟢 **Tracing 从 Day 1 就是核心**：传统软件出错了才看日志，Agent 从第一天起就用 Trace。「你根本不知道第 14 步时 Context 里有什么。」

🟢 **LLM-as-a-Judge 必须对齐人类判断**：「LLM-as-a-Judge 如果不和人类判断对齐，评分器就是垃圾。」

🟢 **Agent 三个时代**：
- 1.0（2023初）：Text-in/Text-out，无 Tool Calling
- 2.0（2023-2025）：模型学会 Tool Calling，开发者写代码编排
- 3.0（2025.6+）：Claude Code / Deep Research / Manus 集中爆发，核心算法不变，Context Engineering 质变

🟢 **2026 是 Doers 元年**：Agent 从对话框走向长程自主执行。

🟢 **杀手级应用模式**：Agent 达不到99%可靠性，但能在更长时间内完成大量工作。核心用法是产出高质量初稿，由人审核。

🟢 **Agent ≠ 软件的核心差异**：
- 差异一：Source of Truth 变了
- 差异二：Tracing 从 Day 1 就是核心
- 差异三：构建过程更加 Iterative——「发布前你不知道它会做什么」

🟢 **自我改进机制**：「Eval、纠错、Memory，本质上是同一套机制。」

🟢 **诚实的预测**：「预测未来真的很难。我希望下次来证明我今天说的全部都是错的。」

### 4.2 万字访谈：应用的未来（2024年9月）

**来源**: [腾讯新闻](https://news.qq.com/rain/a/20240928A07NHO00)
**可信度**: ⭐⭐⭐⭐（深度访谈）

🟡 **Agent 生态系统定位**：被描述为「Agent 生态系统中的传奇人物」，讨论了应用的未来方向。

### 4.3 吴恩达对话 Harrison Chase（LangChain Interrupt 2025）

**来源**: 同 1.2
**可信度**: ⭐⭐⭐⭐⭐

🟡 **MCP 的评价**：吴恩达认为 MCP 填补了市场空白，但目前尚处于早期阶段。「很多网上的 MCP 服务并不可用，认证系统也有些笨拙。」

---

## 五、杨植麟（月之暗面/Kimi 创始人）— 中国 AI 创业方法论

### 5.1 深度访谈：K2、Agentic LLM、缸中之脑（2025年8-9月）

**来源**: [张小珺整理](https://emigmo.github.io/blog/2025/yang-zhilin-ai-philosophy/) | [雪球](https://xueqiu.com/6850405229/353653227) | [B站](https://www.bilibili.com/opus/1189693683214057477)
**可信度**: ⭐⭐⭐⭐⭐（一手访谈，多源交叉验证）

**核心观点**：

🟢 **「无限的山」**：深受 David Deutsch《无穷的开始》影响。「问题是不可避免的」和「问题是可以解决的」是可以刻在石头上的两句话。

🟢 **AGI 不是终点而是方向**：「AGI 不是某一级台阶，不会突然一夜之间达到。它是一个持续的方向，而非固定的终点。」

🟢 **「模型即产品」**：训练模型时就要把整套系统搭好。模型训练完成，产品也基本完成。产品在训练过程中完成，而非训练后开发。

🟢 **用 RL 方式管理团队**：「科研、模型训练、组织管理都遵循 RL 原理。从 SFT 式管理转向 RL 式管理——给团队成员目标和奖励，而非具体指令。」

🟢 **SFT vs RL 管理的平衡**：「SFT 太多会失去创造力，RL 过度会被 hack（Reward Hacking）。」

🟢 **技术决策方法论**：基于充分的实验数据，不能拍脑袋。「需要非常了解实验的具体结果。数据足够充分时，判断比较显然。」

🟢 **Agent 最缺泛化能力**：「如果泛化能力强，垂直 Agent 就没那么必要。通用 Agent 能泛化到长尾工具，解决专有问题。」

🟢 **一方产品 vs 三方产品**：模型公司自己做产品，控制上下文环境、工具接口、prompt 结构。「先设计好工具和 Context Engineering，再在此环境中训练模型。」

🔴 **关于开源的立场变化**：承认之前「领先者不会开源」的判断需要修正，因为月之暗面在全球范围内还没完全领先。

### 5.2 清华 AGI-Next 峰会对话（2026年1月）

**来源**: [搜狐](https://roll.sohu.com/a/975683708_661663)
**可信度**: ⭐⭐⭐⭐（官方活动记录）

🟡 **参会阵容**：智谱唐杰、Kimi 杨植麟、阿里林俊旸、腾讯姚顺雨——被称为「中国AI界最有含金量的一次访谈」。

🟡 **核心讨论**：中国AI发展的核心问题，不装不怯，十分直白坦诚。

---

## 六、王小川（百川智能）— TPF 理论与大模型产品方法论

### 6.1 极客公园创新大会 2024 对话（2023年12月）

**来源**: [极客公园](https://www.geekpark.net/news/329067)
**可信度**: ⭐⭐⭐⭐⭐（对话实录）

**核心观点**：

🟢 **TPF（Technology Product Fit）替代 PMF**：「过去做应用，老讲产品和市场之间的匹配——PMF，但产品和市场之外把一个词丢掉了，技术。」「这样一个技术适合什么样的产品」，而不是产品经理洞察市场，回来就开始做。

🟢 **大模型是「学」不是「思」**：引用孔子「学而不思则罔，思而不学则殆」。大模型代表「学」，AlphaZero 代表「思」，两个系统集在一块就会很厉害。

🟢 **造的是「伙伴」不是「工具」**：「我们人类要接受它自己的缺点，它的优点。人是有幻觉的，人有幻觉我能用他，那为什么机器有幻觉就不能用呢？」

🟢 **产品经理新要求**：(1) 把需求转化成评测集 (2) 既要有传统产品经验，又要有想象力。「既有之前的成功经验，但是又能够把自己的经验打散掉去滋养大模型，还能构想出大模型新的样子——'既要又要'。」

🟢 **十倍体验标准**：「一开始就要用得爽，至少对一类有特点的具体的需求，用户得觉得有十倍好的感知。」「不是好一点，是得让你有惊喜感。」

🟢 **大模型选人标准**：必须是大模型的狂热粉丝。「把自己当成大模型时代里一个狂热的粉丝，去体验和感受这个模型给你带来的不同之处。」

🟢 **理想慢一步，落地上快三步**：与 OpenAI 比理想比不过（他们想把一千万颗 GPU 连在一起），但应用落地是中国公司的优势。

### 6.2 晚点聊播客：AI×医疗（2026年4月）

**来源**: [小宇宙播客](https://www.xiaoyuzhoufm.com/episode/67aaefa541b8e4a63c93c03d)
**可信度**: ⭐⭐⭐⭐（深度播客访谈）

🟡 **创业策略**：「走出大厂射程之外」，切入严肃、高价值的医疗场景。「大厂和创业公司不一样，大创新靠小厂，小创新靠大厂。」

🟡 **AGI 的尽头是生命科学**：不是文本创作、不是物理模型。

---

## 七、李志飞（出门问问）— 超级组织与 AI 管理实验

### 7.1 组织AI化实验（2026年4月）

**来源**: [腾讯新闻](https://news.qq.com/rain/a/20260423A06B2500) | [百家号](https://baijiahao.baidu.com/s?id=1863714262668400483)
**可信度**: ⭐⭐⭐⭐（多家媒体报道）

🟡 **核心实验**：大幅缩减研发人员，精简组织层级，尝试把更多工作交给AI。

🟢 **管理是短板**：「管理是其短板，所以希望使用AI替代传统管理的手段。」（李志飞原话）

🟡 **超级个体路径**：推出AI协作产品CodeBanana，目标以200人核心团队驱动原本需要更多人力的业务。

### 7.2 TicNote 发布会（2025年6月）

**来源**: [新浪](https://k.sina.com.cn/article_7857141524_1d452771401902bzlq.html) | [中华网](https://tech.china.com/articles/20250626/202506261691213.html)
**可信度**: ⭐⭐⭐⭐（官方发布会报道）

🟢 **AGI 路径**：「实现AGI的方式，是用AI的AI做AI。」（李志飞原话）

🟡 **产品理念**：打造跨时间、跨文件的项目管理平台，Shadow AI 具备构建项目与自动管理项目的能力。

---

## 八、Yohei Nakajima（BabyAGI）— 自主 Agent 的设计哲学

### 8.1 BabyAGI 项目与设计理念

**来源**: [GitHub](https://github.com/yoheinakajima/babyagi) | [yoheinakajima.com](https://yoheinakajima.com/)
**可信度**: ⭐⭐⭐⭐（一手项目文档）

🟡 **设计哲学**：「VC by day, builder by night」和「build-in-public」。

🟡 **开发原则**：「Focus on small, modular modifications rather than extensive refactoring.」—— 聚焦小型、模块化的修改，而非大规模重构。

🟡 **贡献指南**：引入新功能时，必须提供具体用例的详细描述。

🟡 **影响力**：2023年3月用300余行Python代码发布，瞬间引爆全球AI开发者社区，成为第一个流行的自主Agent。

---

## 九、Lenny Rachitsky & Shreyas Doshi — AI 时代的产品方法论

### 9.1 Lenny Rachitsky 播客：AI 对 PM 的影响

**来源**: [CSDN](https://blog.csdn.net/youmaob/article/details/138791762) | [搜狐](https://m.sohu.com/a/1018077513_355140/)
**可信度**: ⭐⭐⭐⭐（播客内容总结）

🟡 **PM 角色三类工作**：(1) 塑造产品（Shape the product）— 确定或影响团队构建什么 (2) 执行 (3) 沟通协调。

🟡 **Nikhyl Singhal 在 Lenny 播客中的判断**：「PM 会进入各个行业，成为'变革的推动者（agents of change）'。」

🟡 **传统 PM 正在被淘汰**：硅谷大厂开始 AI-first 换血——先裁3万人、再招8000个新人。传统产品经理正在被 Builder 淘汰。

### 9.2 Shreyas Doshi 的 LNO 框架与 AI 产品决策

**来源**: [Rework Resources](https://resources.rework.com/libraries/leadership-legends/shreyas-doshi-leadership) | [Awesome PM Skills](https://blog.csdn.net/weixin_30940783/article/details/96746559)
**可信度**: ⭐⭐⭐⭐（框架原始来源 + 应用案例）

🟡 **LNO 框架**：Leverage（杠杆性）、Novelty（新颖性）、Optionality（可选性）— 用于任务优先级排序。

🟡 **PM vs Product Thinker**：区分了产品经理（Product Manager）和产品思考者（Product Thinker）。

🟡 **Pre-mortem 纪律**：在项目开始前假设失败，倒推可能的原因。

🟡 **Awesome PM Skills 项目**：将 Lenny Rachitsky 播客中300多位世界级产品领袖的实战智慧，提炼成28个可被AI直接调用的「主动技能」。OpenAI 的 Kevin Weil 等人的方法论也被纳入。

---

## 十、YC AI Startup School（2025）— AI 创业的系统性挑战

### 10.1 从模型竞争到流程战争

**来源**: [SegmentFault](https://segmentfault.com/p/1210000046817704) | [CSDN](https://blog.csdn.net/lifetragedy/article/details/148948014)
**可信度**: ⭐⭐⭐⭐（活动总结）

🟡 **核心转变**：AI创业已从「模型竞争」转向「流程战争」，强调系统设计、资源控制、可评估性和合规性。

🟡 **Altman 在 YC 的观点**：「流程性Agent」是未来核心，创业者需理解行业专家系统逻辑，构建完整流程。

🟡 **Karpathy 在 YC 的观点**：Agent 是行为体，需具备精密评测系统与行为设计能力。

🟡 **七大隐性问题**：可测性陷阱、伪流程化、算力门槛、监管前置、人才竞争、行业理解、社会许可缺失。

### 10.2 YC 2025 夏季 Demo Day

**来源**: [人人都是产品经理](https://www.woshipm.com/ai/6272599.html)
**可信度**: ⭐⭐⭐⭐（行业观察）

🟡 **169+ 家公司亮相**：AI 依旧是舞台中央，最强烈的信号是从模型到应用的转向。

---

## 十一、Agent 框架设计理念对比

### 11.1 四大框架哲学对比

**来源**: [CSDN](https://blog.csdn.net/qq_73472828/article/details/161318967) | [腾讯云](https://cloud.tencent.com/developer/article/2659271)
**可信度**: ⭐⭐⭐⭐（技术分析文章）

🟡 **四大哲学**：
- **DeepAgents**：开箱即用——内置规划/文件/上下文管理
- **CrewAI**：角色驱动的多Agent协作框架——像一支纪律严明的团队
- **AutoGen**：对话式研究——自然语言驱动的协作
- **LangGraph/OpenAI SDK**：图编排/官方轻量

🟡 **选型关键**：没有「最好」的框架，只有「最适合」的。CrewAI 像军队（角色明确、流程固定），AutoGen 像圆桌会议（自由讨论、动态决策）。

---

## 十二、AI PM vs 传统 PM — 核心区别讨论

### 12.1 综合对比分析

**来源**: [腾讯云](https://cloud.tencent.com/developer/article/2669909) | [53AI](https://www.53ai.com/news/LargeLanguageModel/2025081139047.html)
**可信度**: ⭐⭐⭐⭐（行业分析）

🟡 **核心哲学差异**：
| 维度 | 传统PM | AI PM |
|------|--------|-------|
| 核心哲学 | 确定性交付 | 概率性管理 |
| 产品本质 | 功能(Function) | 服务(Service) |
| 验收标准 | 对/错 | 好/较好/勉强能用 |
| 最大敌人 | 需求变更 | 幻觉、不可控、不一致 |
| 迭代周期 | 2-4周一个版本 | 功能2-4周 + Prompt/评估/数据可能每天都在调 |

🟡 **王小川的补充**：AI PM 最核心的能力是把需求转化成评测集，而非写PRD。

🟡 **杨植麟的补充**：在「模型即产品」范式下，产品在训练过程中完成，PM需要理解模型训练而非只关注交互设计。

---

## 发现的矛盾点 🔴

### 矛盾1：Framework vs Harness 的路线之争
- **Harrison Chase**：明确表示 Harness > Framework，通用框架不是未来
- **YC 生态**：大量创业公司仍在使用 LangGraph（框架）构建产品
- **吴恩达**：虽然强调「乐高积木」，但仍使用 LangGraph 处理复杂流程
- **分析**：Harrison Chase 的立场可能有商业驱动（LangChain 转型 DeepAgents），但他的核心洞察（Context Engineering > Prompt Engineering）被广泛认可

### 矛盾2：Agent 可靠性的预期差异
- **Harrison Chase**：「Agent 达不到99%可靠性」，核心用法是产出初稿由人审核
- **Dario Amodei**：Claude新功能「几乎完全由AI自主开发」
- **分析**：两个说法并不完全矛盾——Coding Agent 在特定领域（代码生成）的可靠性可能远高于通用 Agent

### 矛盾3：开源策略的立场
- **杨植麟**：承认之前「领先者不会开源」的判断需要修正
- **王小川**：强调创业公司要「走出大厂射程之外」，暗示开源可能被大厂利用
- **分析**：中国AI创业者对开源的态度比硅谷更谨慎

### 矛盾4：PM 角色的未来
- **Lenny Rachitsky 播客**：传统PM正在被淘汰，需要变成 Builder
- **王小川**：产品经理仍然关键，但需要新能力（评测集设计、技术理解）
- **Sam Altman**：未来角色是「管理AI团队的人」
- **分析**：PM不会消失，但角色定义正在剧烈变化。核心分歧在于「PM应该写代码还是写评测集」

---

## 来源汇总（共18条有效来源）

| # | 来源 | 专家/主题 | 可信度 | URL |
|---|------|----------|--------|-----|
| 1 | Alan Hou / singjupost | Andrew Ng YC演讲 | ⭐⭐⭐⭐⭐ | [链接](https://alanhou.org/blog/yc-andrew-ng-building-faster-ai/) |
| 2 | 腾讯云开发者社区 | Andrew Ng × Harrison Chase 对话 | ⭐⭐⭐⭐⭐ | [链接](https://cloud.tencent.com/developer/article/2652105) |
| 3 | DeepLearning.AI | Andrew Ng Agentic AI 课程 | ⭐⭐⭐⭐⭐ | [链接](https://www.deeplearning.ai/courses/agentic-ai) |
| 4 | 腾讯新闻 | Sam Altman Big Technology 访谈 | ⭐⭐⭐⭐ | [链接](https://news.qq.com/rain/a/20251220A05C6000) |
| 5 | 虎嗅网 | Sam Altman 多场访谈综合 | ⭐⭐⭐⭐ | [链接](https://m.huxiu.com/article/4795433.html) |
| 6 | 小宇宙 | Sam Altman 红杉活动解读 | ⭐⭐⭐⭐ | [链接](https://www.xiaoyuzhoufm.com/episode/683e85ef38dcc57c64474722) |
| 7 | 36氪/知乎 | Dario Amodei 2025年初采访 | ⭐⭐⭐⭐ | [链接](https://36kr.com/p/3134131984472583) |
| 8 | 百家号/雪球 | Dario Amodei 2026年5月专访 | ⭐⭐⭐⭐ | [链接](https://baijiahao.baidu.com/s?id=1865422428104492553) |
| 9 | 夸智网 | Dario Amodei Dreamforce 对话 | ⭐⭐⭐⭐ | [链接](https://www.kuazhi.com/post/716400535.html) |
| 10 | LangChain 官方博客 | Harrison Chase Harness Memory | ⭐⭐⭐⭐⭐ | [链接](https://blog.langchain.com/your-harness-your-memory/) |
| 11 | 腾讯新闻 | Harrison Chase 万字访谈 | ⭐⭐⭐⭐ | [链接](https://news.qq.com/rain/a/20240928A07NHO00) |
| 12 | 张小珺/雪球/B站 | 杨植麟深度访谈 | ⭐⭐⭐⭐⭐ | [链接](https://emigmo.github.io/blog/2025/yang-zhilin-ai-philosophy/) |
| 13 | 搜狐 | 杨植麟清华峰会对话 | ⭐⭐⭐⭐ | [链接](https://roll.sohu.com/a/975683708_661663) |
| 14 | 极客公园 | 王小川 TPF 理论对话实录 | ⭐⭐⭐⭐⭐ | [链接](https://www.geekpark.net/news/329067) |
| 15 | 小宇宙 | 王小川 AI×医疗播客 | ⭐⭐⭐⭐ | [链接](https://www.xiaoyuzhoufm.com/episode/67aaefa541b8e4a63c93c03d) |
| 16 | 腾讯新闻/百家号 | 李志飞组织AI化实验 | ⭐⭐⭐⭐ | [链接](https://news.qq.com/rain/a/20260423A06B2500) |
| 17 | GitHub | Yohei Nakajima BabyAGI | ⭐⭐⭐⭐ | [链接](https://github.com/yoheinakajima/babyagi) |
| 18 | SegmentFault/CSDN | YC AI Startup School | ⭐⭐⭐⭐ | [链接](https://segmentfault.com/p/1210000046817704) |

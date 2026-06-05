# Ming-Yu Liu（刘明宇）— 长对话、访谈与演讲记录

> 调研时间：2026-06-06
> 数据来源：NVIDIA官方博客、NVIDIA AI Podcast、GTC/SIGGRAPH/NeurIPS会议记录、第一财经/电子工程专辑等媒体报道
> 注意：信息源黑名单已排除知乎、微信公众号、百度百科

---

## 1. NVIDIA AI Podcast Ep.240 — 世界基础模型与物理AI（2025年1月7日）

**来源**：NVIDIA AI Podcast 第240期，主持人 Noah Kravitz
**形式**：播客访谈（约30分钟）
**原始链接**：
- 播客音频：https://podcast.kkbox.com/tw/episode/X_SKyyRylbCUMpkYBr
- 文字转录PDF：https://blogs.nvidia.com/wp-content/uploads/2025/01/AI-Podcast-World-Models-Transcript-1.pdf
- NVIDIA博客摘要：https://blogs.nvidia.com/blog/world-foundation-models-advance-physical-ai/
- Dexa索引页：https://dexa.ai/aipodcast/d/f0778fbc-cd14-11ef-bdb3-77ede2557fee

**可信度**：★★★★★（NVIDIA官方发布，一手来源）

### 核心引述

> "World foundation models are important to physical AI developers. They can imagine many different environments and can simulate the future, so we can make good decisions based on this simulation."

> "The self-driving car industry and the humanoid robot industry will benefit a lot from world model development. [WFMs] can simulate different environments that will be difficult to have in the real world, to make sure the agent behaves respectively."

> "We are still in the infancy of world foundation model development — it's useful, but we need to make it more useful. We also need to study how to best integrate these world models into the physical AI systems in a way that can really benefit them."

### 要点摘录

- Ming-Yu Liu 定义WFM为"可以模拟物理环境的强大神经网络"，能从文本或图像输入生成详细视频，并预测场景演变
- 他认为WFM解决了物理AI开发中的两大问题：(1)数据收集困难且昂贵，(2)真实世界训练测试风险高、成本大
- 强调Cosmos平台的开放性——提供预训练模型（diffusion + autoregressive架构）和tokenizer，让开发者拥有"所有要素"来自建模型
- 提到NVIDIA与1X、火币（Huobi）、小鹏（XPENG）合作，帮助解决物理AI开发挑战
- **谦逊表达**：承认WFM仍在"起步阶段"（infancy），需要变得更有用，并研究如何最好地将其整合到物理AI系统中

### 对话风格观察

- 使用简洁、直白的英语，避免过度技术术语
- 喜欢用"imagine"和"simulate"来描述世界模型的能力
- 在被问到未来方向时，不急于给出宏大承诺，而是承认"still in infancy"
- 回答结构：先给出高层定义→举例说明→指出当前局限→展望未来

---

## 2. GTC 2026 主题演讲 — 《How Open World Models are Powering the Next Breakthroughs in Physical AI》（2026年3月）

**来源**：NVIDIA GTC 2026，session code S81667
**形式**：技术演讲（约45分钟），后有panel discussion
**原始链接**：
- YouTube完整视频：https://www.youtube.com/watch?v=3Errq-0T9w0&list=PL3jK4xNnlCVclphegeS4R9JYbhWprKJe_&index=2
- NVIDIA On-Demand：https://www.nvidia.com/en-us/on-demand/playlist/playList-5d17c33f-65f5-4fb1-9fed-bc5b7f4dec44/
- 中文详细编译（搜狐/Z Potentials）：https://www.sohu.com/a/1011558779_122063396/
- Watch Party中文版（session WP81667）：由台湾技术专家Cliff Chiu中文讲解导读

**可信度**：★★★★★（NVIDIA官方会议，有完整视频和详细编译）

### 核心引述（直接引用）

> "我们最终想在Cosmos中做的事情，就是为机器人打造一个'黑客帝国'。不是给人类，而是给机器人。"

> "在这个物理世界里，理解和生成都非常重要：理解帮助你看懂这个世界、推理潜在结果，生成则帮助你模拟未来。"

> "我们采取的方法，本质上是用算力去换数据。在真实世界里采集这些物理数据非常困难；但如果我们能够生成这些数据，就能帮助你加快Physical AI的开发。"

> "我想强调一点：理解能力和预测能力，是构建这种生成式训练设施、也就是终极版Cosmos的两个基础能力。"

### 演讲完整结构与关键论点

**1. AI演进三阶段论**
- 生成式AI（Generative AI）→ 智能体AI（Agent AI）→ Physical AI
- 生成式AI成功的关键：海量互联网数据
- 智能体AI成功的关键：数字工具使用数据（如Stack Overflow、编程Agent的模拟输出）
- Physical AI的困境：鸡生蛋/蛋生鸡问题——没有足够部署就无法收集数据，没有数据就造不出好模型

**2. Physical AI数据金字塔**
- 最底层：互联网规模数据（非机器人视角，但编码了世界动态）
- 中间层：世界模型（如Cosmos）吸收互联网数据中的知识 + 物理引擎（如NVIDIA Newton）生成补充数据
- 顶层：机器人真实数据（in-robot data）——连接"观察"与"动作"
- 三者结合才能构建Physical Agent

**3. Cosmos终极形态：生成式训练设施**
- 输入：智能体 + 环境 + 任务
- 输出：更强的智能体
- 类比数据增强：环境增强（k个厨房）+ 任务增强
- 两大基础能力：理解推理（判断任务是否完成，产生reward）+ 预测生成（模拟未来状态）

**4. Cosmos三大核心模型**
- **Cosmos Reason 2**：面向物理世界理解，接收视频+文本，先"思考"再生成答案。开放式VLM benchmark排名第一
- **Cosmos Predict 2**：视频预测模型，给定当前帧+文本预测未来。支持单图/多帧/视频输入
- **Cosmos Transfer 2.5**：将控制输入（深度、边缘、分割等）转换为照片级真实感视频

**5. Cosmos Policy — 从视频模型到机器人策略**
- 从Cosmos Predict 2微调而来，同时预测未来图像和机器人动作
- LIBERO仿真基准SOTA（98.5%成功率）
- 真实世界演示：pick-and-place、折T恤（仅15条训练示范）、高精度操作（开自封袋放糖果）
- 基于模型的规划（model-based planning）：best-of-n搜索 + 值函数，困难案例提升12.5分

**6. AlphaDrive — 实时生成式闭环自动驾驶模拟器**
- Cosmos Transfer + 语义输入（bounding boxes、车道线）→ 多视角真实视频
- 可接方向盘实时控制，支持自动驾驶policy闭环仿真

**7. Cosmos 3预告**
- 将理解与生成融合为统一的omni model
- 多模态Transformer架构：Reasoner + Generator 紧密耦合
- 输入：图像、视频、声音、动作；输出：文本（推理时）/ 图像、视频、声音、动作（生成时）

### Panel Discussion（GTC 2026同期）

Ming-Yu Liu提到主持了一场panel discussion，嘉宾包括：
- Stefano Ermon（斯坦福大学）
- Runway CTO
- Adobe Firefly Research负责人
- Fable CEO
- 主题：diffusion以及接下来的发展方向

---

## 3. SIGGRAPH 2025 NVIDIA Research 特别演讲（2025年8月，温哥华）

**来源**：SIGGRAPH 2025，与Sanja Fidler、Aaron Lefohn联合发表
**形式**：特别演讲（Special Address）
**原始链接**：
- NVIDIA On-Demand回放：https://www.nvidia.com/en-us/on-demand/session/siggraph25-siggraph25-20/
- NVIDIA中国页面：https://www.nvidia.cn/events/siggraph
- 电子工程专辑报道：https://www.eet-china.com/mp/a428796.html

**可信度**：★★★★★（NVIDIA官方会议，有回放视频）

### 核心引述

> "物理AI需要一个触感真实的虚拟环境，一个让机器人能通过试错安全学习的并行宇宙。"

> "所有移动的物体，都可以变成机器人。对于不同机器人，人们有不同期望。对于工业机械臂，只期望它能在装配线上执行简单任务，对于自动驾驶汽车，期望它能应对复杂交通状况，而对于仿人机器人，则期望能同人类一般有多种技能且能操控环境。"

> "如果生成式AI可以转化为物理AI，关键将是训练数据，破解数据问题的关键是合成数据。"

### 演讲背景与要点

- SIGGRAPH 2025于2025年8月10-14日在加拿大温哥华举行
- NVIDIA带去16篇论文，物理AI是参会主题词
- 三位研究VP联合演讲：Sanja Fidler（AI研究）、Aaron Lefohn（图形研究）、Ming-Yu Liu（生成式AI研究）
- Ming-Yu Liu的深度想象研究小组被描述为"先驱力量"，开创了计算机视觉、Transformer模型和视觉生成式AI模型
- 发布了Cosmos Reason（70亿参数推理VLM）、Omniverse NuRec 3D Gaussian Splatting库等

### 即兴评论与类比

- **"并行宇宙"类比**：将世界模型比作"并行宇宙"（parallel universe），机器人可以在其中安全地试错学习
- **"不同期望"框架**：用不同机器人形态（工业机械臂→自动驾驶→仿人机器人）的期望梯度来解释Physical AI的复杂性递进
- **数据是关键**：反复强调"破解数据问题的关键是合成数据"，将算力视为换取数据的手段

---

## 4. NeurIPS 2025 — NVIDIA Cosmos Insights Session

**来源**：NeurIPS 2025（2025年12月）
**形式**：会议session
**原始链接**：
- LinkedIn帖子（NVIDIA AI官方）：https://www.linkedin.com/posts/nvidia-ai_neurips2025-activity-7405381443079561216-GUD3

**可信度**：★★★★☆（LinkedIn官方帖子提及，但无完整transcript）

### 关键信息

- Ming-Yu Liu在NeurIPS 2025上做了关于NVIDIA Cosmos的session
- LinkedIn帖子称其为"highlight"，描述他的愿景为"world foundation models should be the matrix for robots"
- 这是"The Matrix for robots"比喻的又一处出处（与个人网站表述一致）

---

## 5. NVIDIA GTC 2025 — Cosmos世界基础模型介绍

**来源**：GTC 2025（2025年春）
**形式**：技术演讲
**原始链接**：
- NVIDIA On-Demand：搜索session "An Introduction to NVIDIA Cosmos World Foundation Models"
- 开发者页面：https://developer.nvidia.com/?p=69542

**可信度**：★★★★☆（NVIDIA官方会议，有session记录）

### 要点

- Ming-Yu Liu作为主讲人介绍Cosmos世界基础模型
- 主题：如何推动物理AI开发的普及——为开发者提供开放的模型和工具
- Cosmos正在改变机器人和自动驾驶汽车的学习方式

---

## 6. NVIDIA 云栖大会 2025 — 特别演讲

**来源**：2025年9月24-26日，杭州云栖大会
**形式**：特别演讲（与Sanja Fidler、Aaron Lefohn联合）
**原始链接**：
- 雪球转述：https://xueqiu.com/1857987978/353907845
- NVIDIA中国页面

**可信度**：★★★★☆（NVIDIA官方参与，有中文字幕版回放）

### 要点

- NVIDIA作为大会巅峰合作伙伴，以"洞见AI未来"为主题
- Ming-Yu Liu参与的特别演讲有中文字幕版
- 讲述如何为计算机图形和物理AI的下一步发展布局

---

## 7. Fireside Chat（疑似2025-2026年）

**来源**：firesidechat.com
**形式**：Fireside Chat
**原始链接**：https://firesidechat.com/ming-yuliu

**可信度**：★★★☆☆（平台存在但内容需登录，无法获取完整内容）

### 已知信息

- 页面标题包含Ming-Yu Liu的名字
- 引语："Toto, I've got a feeling we're not in Kansas anymore."（《绿野仙踪》引用，暗示"进入了新世界"）
- 无法确认具体日期和详细内容

---

## 8. 个人网站自我描述（持续更新）

**来源**：https://mingyuliu.net/
**可信度**：★★★★★（本人维护）

### 核心表述

> "I'm driven by the vision of building the 'Matrix' for robots — a physics-grounded simulated universe where machines can dream, rehearse, and accumulate experience safely at scale before ever acting in the real world."

### 要点

- 自称领导NVIDIA Cosmos Lab
- 强调"Generative AI for Physical AI"的定位
- 将个人愿景浓缩为"为机器人建造Matrix"
- 用"dream, rehearse, and accumulate experience"三个动词描述机器人在虚拟世界中的学习过程

---

## 9. GTC 2026 台湾Watch Party系列

**来源**：GTC 2026 Watch Party（2026年3月23日）
**形式**：Watch Party（中文导读+互动）
**Session代码**：WP81667, WP81667a, WP81667b, WP81667d
**原始链接**：
- GitHub session数据：https://github.com/madeyexz/gtc-2026-sessions/blob/main/app/public/sessions/WP81667.md

**可信度**：★★★★☆（NVIDIA官方Watch Party，但由本地技术专家而非Ming-Yu本人主持）

### 要点

- 由台湾技术专家Cliff Chiu以中文讲解并导读Ming-Yu Liu的演讲
- 多个时段的Watch Party（WP81667, WP81667a, WP81667b, WP81667d）
- Ming-Yu Liu同时在session WP81479中出现（主题：Physical AI for the Real World: A Vision From NVIDIA Robotics Research）
- 另有session S81684：Diffusion Unlocked: Advanced Techniques for Training, Inference, and Deployment

---

## 10. 第一财经报道 — SIGGRAPH 2025现场（2025年8月12日）

**来源**：第一财经，via腾讯新闻
**形式**：新闻报道（含直接引述）
**原始链接**：https://news.qq.com/rain/a/20250812A03VGF00
**可信度**：★★★★☆（权威财经媒体直接引述）

### 额外引述

> "对于不同机器人，人们有不同期望。"

- 报道提到Ming-Yu Liu在SIGGRAPH 2025上的表态
- 与前述SIGGRAPH演讲内容一致

---

## 11. 雪球/机器人产业网报道 — Jetson AGX Thor发布（2025年8-9月）

**来源**：雪球、机器人产业网
**形式**：行业分析报道
**原始链接**：
- 机器人产业网：http://www.jiqiren.org.cn/news/484.html
- 雪球：https://xueqiu.com/9251397422/350521138

**可信度**：★★★☆☆（二手报道，引述来源不完全明确）

### 引述

> "英伟达的目标是打造一个虚拟世界，让机器人能够在这个安全的平行世界中反复试验、不断学习。"

- 这一表述与个人网站的"Matrix for robots"愿景高度一致
- 用"安全的平行世界"替代了"parallel universe"的说法

---

## 12. NVIDIA Blog 作者页面（持续更新）

**来源**：
- 英文：https://blogs.nvidia.com/blog/author/mingyuliu/
- 中文：https://blogs.nvidia.cn/blog/author/mingyuliu/
- NVIDIA Research：https://research.nvidia.com/person/ming-yu-liu
- Developer Blog：https://developer.nvidia.com/blog/author/mingyul/

**可信度**：★★★★★（NVIDIA官方）

### 标准介绍

> "Ming-Yu Liu is a vice president of research at NVIDIA and an IEEE fellow. He leads the Deep Imagination Research group at NVIDIA, which focuses on deep generative models for physical AI."

> "His research group regularly publishes scientific papers in top-tier AI conferences, including NeurIPS, ICLR, ICML, CVPR, ICCV, ECCV, and SIGGRAPH."

---

## 关键观察：Ming-Yu Liu的表达模式

### 1. 核心比喻与类比

| 比喻 | 出处 | 含义 |
|------|------|------|
| "The Matrix for robots" | 个人网站、NeurIPS 2025 | 为机器人构建物理仿真宇宙 |
| "黑客帝国"（中文） | GTC 2026 | 同上，中文语境下的表述 |
| "并行宇宙" | SIGGRAPH 2025 | 机器人安全试错的虚拟环境 |
| "安全的平行世界" | 媒体报道引述 | 同上，更通俗的表达 |
| "用算力换数据" | GTC 2026 | Cosmos的核心方法论 |
| "鸡生蛋/蛋生鸡" | GTC 2026 | Physical AI的数据困境 |
| "数据金字塔" | GTC 2026 | 互联网数据→世界模型→机器人数据的层次 |
| "生成式训练设施" | GTC 2026 | Cosmos终极形态的定位 |

### 2. 回答被追问时的模式

- **承认局限**："We are still in the infancy of world foundation model development"（Podcast 2025）
- **用具体例子说明抽象概念**：切黄瓜、折T恤、开自封袋（GTC 2026）
- **分层递进**：先说高层定义→具体应用→当前局限→未来方向
- **不急于承诺**：不说"即将实现"，而是说"还有很长的路要走"（GTC 2026）

### 3. 研究方向的即兴评论

- 用电影《黑客帝国》来解释Cosmos的终极愿景，说明他善于用流行文化类比
- 用"所有移动的物体都可以变成机器人"来表达Physical AI的广度
- 强调"理解"和"生成"的对偶关系——这在GTC 2026中被反复提及
- 将Cosmos 3定位为"既理解又生成的统一模型"，显示他对融合方向的坚定判断

### 4. 与同行的关系

- 频繁与Sanja Fidler（AI研究VP）和Aaron Lefohn（图形研究VP）联合出场
- 提到主持panel discussion，嘉宾包括斯坦福、Runway、Adobe、Fable等学术和产业界领袖
- 在NVIDIA内部，Cosmos被定位为支撑自动驾驶团队和机器人团队的backbone

---

## 矛盾与待确认事项

1. **"The Matrix for robots"的首次使用时间**：个人网站和NeurIPS 2025都使用了这个比喻，但不确定最早出自何时
2. **Cosmos版本时间线**：GTC 2026演讲中提到的版本发布历史（2025年1月Cosmos 1 → 3月Transfer 1 + Reason 1 → 6月Predictor → 10月2.5版本 → 2026年1月Reason 2 + Policy → Cosmos 3早期版本）需要与NVIDIA官方发布时间表交叉验证
3. **Fireside Chat内容**：firesidechat.com上存在页面但无法获取详细内容，需要进一步调查
4. **"Huobi"合作**：NVIDIA博客提到与"Huobi"合作，这可能是"Huobi"（火币，加密货币公司）的笔误，实际可能指其他公司
5. **演讲风格变化**：Podcast（2025年1月）中的表达较为谨慎（"still in infancy"），到GTC 2026（2026年3月）中更加自信和系统化，展示了约一年间从"探索阶段"到"产品化推进"的转变

---

## 缺失与待补充

- [ ] NVIDIA AI Podcast Ep.240完整文字转录（PDF为加密格式，未能提取全文）
- [ ] SIGGRAPH 2025特别演讲的完整转录
- [ ] GTC 2025 Cosmos介绍演讲的详细内容
- [ ] Fireside Chat的详细内容
- [ ] Ming-Yu Liu在学术会议（CVPR、NeurIPS等）上的Q&A环节记录
- [ ] 与Jensen Huang同台时的互动细节
- [ ] 更早期的访谈/播客（2023年及之前）

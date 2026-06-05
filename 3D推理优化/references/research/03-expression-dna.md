# Ming-Yu Liu 表达风格DNA

> 调研时间：2026-06-06
> 信息源：个人网站、NVIDIA官方博客、NVIDIA Research页面、GitHub、arXiv论文、SIGGRAPH/GTC演讲报道、科技媒体报道
> 信息源黑名单：已排除知乎、微信公众号、百度百科

---

## 1. 社交媒体账号

| 平台 | 账号/URL | 状态 |
|------|----------|------|
| Twitter/X | [@liu_mingyu](https://twitter.com/liu_mingyu) | 活跃（GitHub profile链接） |
| GitHub | [mingyuliutw](https://github.com/mingyuliutw) | 公开仓库（UNIT等项目） |
| LinkedIn | [linkedin.com/in/mingyuliu](https://linkedin.com/in/mingyuliu) | 活跃，有招聘帖 |
| 个人网站 | [mingyuliu.net](https://mingyuliu.net/) | 简洁一页式 |
| Google Scholar | [y-f-MZgAAAAJ](https://scholar.google.com/citations?user=y-f-MZgAAAAJ&hl=en) | 活跃 |

**来源**: GitHub profile页面列出 @liu_mingyu 为Twitter账号 | **可信度**: ★★★★★

---

## 2. 核心表达DNA特征

### 2.1 愿景驱动型解释框架

几乎总是从宏大愿景切入，再逐步下降到技术细节。先描绘终局，再告诉当下的进展。

**原始文本样本**:

> "I'm driven by the vision of building the 'Matrix' for robots — a physics-grounded simulated universe where machines can dream, rehearse, and accumulate experience safely at scale before ever acting in the real world."
> — 个人网站 mingyuliu.net（2026-04-09）| **可信度**: ★★★★★

> "我们最终想在Cosmos中做的事情，就是为机器人打造一个'黑客帝国'。不是给人类，而是给机器人。"
> — GTC 2026 演讲（腾讯新闻 2026-04-19）| **可信度**: ★★★★☆

> "物理AI需要一个逼真的虚拟环境，让机器人能够在这个安全的平行世界中反复试验、不断学习。"
> — SIGGRAPH 2025（电子工程专辑 2025-08-13）| **可信度**: ★★★★☆

**风格**: 使用电影类比（The Matrix）降低理解门槛。"dream, rehearse, accumulate experience" 具有叙事感。

---

### 2.2 金字塔式数据论证法

用层级结构组织论点——从底层基础到上层应用，逐层递进。

> "我们把它想象成一个金字塔。最底层是互联网规模的数据……在物理世界中，我们可以构建一种世界模型，比如Cosmos这样的媒介，去吸收这些互联网规模数据中包含的知识。"
> — GTC 2026（腾讯新闻 2026-04-19）| **可信度**: ★★★★☆

> "Data determines the ceiling of an AI model."
> — Cosmos论文 Introduction（arXiv:2501.03575v3）| **可信度**: ★★★★★

**风格**: 简洁断言句 + 层级展开。

---

### 2.3 问题框架化能力

将技术挑战重新框架化为直觉上容易理解的困境/悖论。

> "我们现在面临一个典型的鸡生蛋、蛋生鸡问题：我们没有足够多的类人机器人部署在现实环境中，因此无法收集海量训练数据；因为没有海量训练数据，我们就造不出足够强大的类人机器人模型；而因为这些模型还不够强，大家就不会去购买这些机器人。"
> — GTC 2026（腾讯新闻 2026-04-19）| **可信度**: ★★★★☆

**模式**: 循环论证→困局→打破困局的方案。"制造张力→释放张力"的叙事弧。

---

### 2.4 产品化语言

不只谈论文，更谈"平台""工具""基础设施"。

> "In this paper, we present the Cosmos World Foundation Model Platform to help developers build customized world models for their Physical AI setups."
> — Cosmos论文 Abstract（arXiv:2501.03575）| **可信度**: ★★★★★

> "我们的目标不是急着推出自己的policy model，而是先确保Cosmos本身对广大Physical AI开发者真正有用。"
> — GTC 2026（腾讯新闻 2026-04-19）| **可信度**: ★★★★☆

**高频词**: platform, developers, builders, customized, help, enable, open-source, open-weight, permissive licenses

---

### 2.5 两阶段思维：Pre-training → Post-training

反复出现"先通用再专用"的两阶段框架。

> "We present a pre-training-and-then-post-training paradigm, where we divide WFMs into pre-trained and post-trained WFMs."
> — Cosmos论文（arXiv:2501.03575v3）| **可信度**: ★★★★★

> "在Cosmos刚开始构思的时候，我们就认为，后训练会是整个体系里至关重要的一环，因为每一种机器人看世界的方式都不一样。"
> — GTC 2026（腾讯新闻 2026-04-19）| **可信度**: ★★★★☆

---

### 2.6 确定性表达风格

高度确定，很少使用 hedging 词汇，更多使用断言式陈述。

> "Physical AI needs to be trained digitally first." — Cosmos论文第一句 | **可信度**: ★★★★★

> "所有移动的物体，都可以变成机器人。" — SIGGRAPH 2025（搜狐 2025-08-12）| **可信度**: ★★★★☆

> "如果生成式AI可以转化为物理AI，关键将是训练数据，破解数据问题的关键是合成数据。" — SIGGRAPH 2025（搜狐 2025-08-12）| **可信度**: ★★★★☆

**风格**: "关键在于""核心是""必须"等强确定性词汇。

---

### 2.7 类比与可视化偏好

| 类比 | 语境 | 来源 |
|------|------|------|
| "黑客帝国/The Matrix" | 机器人虚拟训练世界 | 个人网站、GTC 2026 |
| "鸡生蛋蛋生鸡" | 数据困局 | GTC 2026 |
| "平行宇宙" | 虚拟世界安全学习 | SIGGRAPH 2025 |
| "生成式训练设施" | Cosmos终极形态 | GTC 2026 |
| "数字孪生" | WFM作为物理世界副本 | Cosmos论文 |
| "金字塔" | 数据层级结构 | GTC 2026 |

---

### 2.8 论文写作风格

**句式**:
- 短句开头: "Physical AI needs to be trained digitally first."
- 被动语态偏好: "The models have been trained on..."
- "We present/propose"模式: "In this paper, we present the Cosmos WFM Platform..."

**用词偏好**:
- 学术高频: scalable, generalist, specialist, fine-tune, post-training, pre-training, tokenizer, backbone, downstream
- 产品化词汇混入学术: platform, pipeline, developers, builders, customized, enable
- Physical AI术语链: Physical AI → World Foundation Model → policy model → world model → digital twin → sim-to-real

**结构**: 极度清晰的层级（Section → Subsection → Subsubsection），喜欢用图表引用，Introduction先定义问题域再定义术语再概述贡献。

**来源**: arXiv:2501.03575v3 | **可信度**: ★★★★★

---

## 3. LinkedIn发帖风格

> "We have multiple position opening at NVIDIA Cosmos, including - Research Scientist of different seniority. - System Software Engineer of different seniority - Technical Program Manager - Product ..."
> — LinkedIn帖文（2026-06-04）| **可信度**: ★★★★★

**特征**: 简洁直接，列表式罗列，破折号分隔，不用emoji，职位名称用英文原文。

---

## 4. Twitter/X发帖风格

### 已确认行为
1. **宣布产品发布**: 论文作者之一刘明宇在Twitter上宣布GauGAN软件开放Beta测试（2019年6月，电子发烧友网报道）| **可信度**: ★★★★☆
2. **祝贺同行**: 英伟达的机器学习专家Ming-Yu Liu送上了祝福（关于DALL-E发布，推文链接 twitter.com/liu_mingyu/status/1346573218270724097）| **可信度**: ★★★★☆

### 推断模式
- 低频发帖，主要在重大发布时发帖
- 产品导向，与NVIDIA产品/研究发布高度相关
- 简洁宣布式：短文字+链接
- 不参与技术争论
- 偶尔祝贺同行

---

## 5. GitHub贡献风格

- **UNIT** (mingyuliutw/UNIT): Unsupervised Image-to-Image Translation (NIPS 2017)
- README风格：简洁，直接展示结果图，少量文字
- 以研究项目为单位发布，命名简洁
- 不追求star数或社区运营
- 代码是论文的附属品

**来源**: https://github.com/mingyuliutw | **可信度**: ★★★★★

---

## 6. 解释复杂概念的方式

### 6.1 先定义再展开
> "Physical AI is an AI system equipped with sensors and actuators: the sensors allow it to observe the world, and the actuators allow it to interact with and modify the world."
> — Cosmos论文 | **可信度**: ★★★★★

**模式**: 一句话定义 + 冒号引出并列展开。

### 6.2 对比论证
> "生成式AI之所以成功，是因为有海量大规模数据；而智能体AI也能够成功，是因为我们现在也有办法创造海量的数字工具使用数据。"
> — GTC 2026 | **可信度**: ★★★★☆

**模式**: "A之所以X是因为Y；B也能够X是因为Z"的平行结构。

### 6.3 叙事弧（几乎所有的技术演讲都遵循）
1. 定义大愿景（"构建机器人的黑客帝国"）
2. 识别核心挑战（"鸡生蛋蛋生鸡"）
3. 提出解决框架（"金字塔式数据策略"）
4. 展示中间成果（"Cosmos三大模型"）
5. 回归愿景（"距离终极目标还有很长的路"）

---

## 7. 高频用词清单

### 英文高频词
| 词汇 | 频率 | 语境 |
|------|------|------|
| Physical AI | ★★★★★ | 核心概念 |
| World Foundation Model | ★★★★★ | 核心术语 |
| platform | ★★★★☆ | 产品定位 |
| developers/builders | ★★★★☆ | 目标受众 |
| open-source/open-weight | ★★★★☆ | 价值观 |
| scalable | ★★★★☆ | 技术评价 |
| fine-tune/post-training | ★★★★★ | 技术流程 |
| pre-training | ★★★★★ | 技术流程 |
| customized | ★★★☆☆ | 定制化 |
| digital twin | ★★★☆☆ | 概念类比 |

### 中文高频词
| 词汇 | 语境 |
|------|------|
| "关键在于" | 强调核心要素 |
| "本质上" | 重新定义问题 |
| "理解能力和预测能力" | Cosmos双能力框架 |
| "后训练/post-training" | 技术流程 |
| "我想强调一点" | 重点提示语 |
| "也就是说" | 解释说明过渡语 |

---

## 8. 风格总结

**一句话**: 愿景工程师型——用电影级愿景吸引注意力，用工程师的严谨逻辑展开论证，用产品经理的语言定义价值。

**标签**:
1. 🎬 电影类比者 — The Matrix、黑客帝国、平行宇宙
2. 📐 层级架构师 — 金字塔、两阶段、平台思维
3. 🎯 确定性断言者 — "关键在于""必须""本质上"
4. 🔧 产品化学者 — platform, developers, enable, customized
5. 🌉 问题-方案叙事者 — 从困境出发引出解法
6. 🤖 Physical AI布道者 — 最核心的表达锚点

---

## 9. 关键引文索引

| # | 引文 | 来源 | 日期 | 可信度 |
|---|------|------|------|--------|
| 1 | "building the 'Matrix' for robots" | mingyuliu.net | 2026-04-09 | ★★★★★ |
| 2 | "Physical AI needs to be trained digitally first" | arXiv:2501.03575 | 2025-01-07 | ★★★★★ |
| 3 | "Data determines the ceiling of an AI model" | arXiv:2501.03575v3 | 2025-01-07 | ★★★★★ |
| 4 | "为机器人打造一个'黑客帝国'" | GTC 2026 (腾讯新闻) | 2026-04-19 | ★★★★☆ |
| 5 | "鸡生蛋、蛋生鸡问题" | GTC 2026 (腾讯新闻) | 2026-04-19 | ★★★★☆ |
| 6 | "所有移动的物体，都可以变成机器人" | SIGGRAPH 2025 (搜狐) | 2025-08-12 | ★★★★☆ |
| 7 | "物理AI需要一个逼真的虚拟环境" | SIGGRAPH 2025 (电子工程专辑) | 2025-08-13 | ★★★★☆ |
| 8 | "作为研究图像合成的研究员" | NVIDIA Blog (GauGAN) | 2019-07-29 | ★★★★★ |
| 9 | "We position a world foundation model as..." | arXiv:2501.03575v3 | 2025-01-07 | ★★★★★ |
| 10 | "理解和生成都非常重要" | GTC 2026 (腾讯新闻) | 2026-04-19 | ★★★★☆ |

---

## 10. 信息源列表

| 来源 | URL | 可信度 |
|------|-----|--------|
| 个人网站 | https://mingyuliu.net/ | ★★★★★ |
| NVIDIA Research | https://research.nvidia.com/person/ming-yu-liu | ★★★★★ |
| NVIDIA Blog | https://blogs.nvidia.com/blog/author/mingyuliu/ | ★★★★★ |
| Cosmos论文 | https://arxiv.org/abs/2501.03575 | ★★★★★ |
| GitHub | https://github.com/mingyuliutw | ★★★★★ |
| Deep Imagination | https://deepimagination.cc/ | ★★★★★ |
| 腾讯新闻/GTC 2026 | https://news.qq.com/rain/a/20260419A03S6I00 | ★★★★☆ |
| 电子工程专辑 | https://www.eet-china.com/mp/a428796.html | ★★★★☆ |
| 搜狐/SIGGRAPH 2025 | https://www.sohu.com/a/923432000_114986/ | ★★★★☆ |
| 36氪 | https://www.36kr.com/p/3419670908243329 | ★★★★☆ |
| 电子发烧友网 | https://www.elecfans.com/d/961885.html | ★★★☆☆ |

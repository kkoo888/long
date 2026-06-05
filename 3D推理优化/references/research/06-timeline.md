# Ming-Yu Liu（劉洺堉）完整人生与职业时间线

> 最后更新：2026-06-06
> 数据来源：NVIDIA Research、OpenReview、DBLP、Google Scholar、IEEE Xplore、个人网站、公开演讲、新闻报道

---

## 一、教育背景

### 博士阶段（2006–2012）
- **学校**：University of Maryland, College Park（马里兰大学帕克分校）
- **院系**：Department of Electrical and Computer Engineering（电气与计算机工程系）
- **学位**：Ph.D. in Electrical and Computer Engineering
- **博士论文**：*Discrete Optimization Methods for Segmentation and Matching*（2012）
  - 论文链接：https://hdl.handle.net/1903/12715
- **研究方向**：计算机视觉、图像分割、离散优化
- **可信度**：★★★★★（来源：DBLP、OpenReview、IEEE Xplore 作者页面）

> **注**：本科教育信息未在公开资料中找到明确记录。根据其繁体中文名"劉洺堉"及台湾来源报道，推测来自台湾，但具体本科学校和专业尚无法确认。

---

## 二、职业生涯时间线

### 早期：Intel（时间不详，约 2012 年前或期间）
- 在加入 MERL 之前，曾在 Intel 从事研究与工程工作
- **可信度**：★★★（来源：getprog.ai 简历描述 "prior research and engineering stints at MERL and Intel"）

### MERL 时期（2012–2016）
- **职位**：Principal Research Scientist（首席研究科学家）
- **机构**：Mitsubishi Electric Research Labs（三菱电机研究实验室），位于美国马萨诸塞州剑桥
- **研究方向**：计算机视觉、场景理解、机器人视觉
- **关键成就**：
  - 2014 年：机器人拣料系统获 R&D 100 Award（《R&D》杂志颁发的百大研发奖）
  - 2015 年：街景理解论文入选 RSS（Robotics: Science and Systems）最佳论文决赛
- **可信度**：★★★★★（来源：OpenReview、专知、DBLP）

### NVIDIA 时期（2016–至今）

#### 2016 年：加入 NVIDIA
- **初始职位**：Research Scientist（研究科学家）
- **加入团队**：NVIDIA Research（NVLabs）
- **可信度**：★★★★★（来源：OpenReview、多个公开报道一致确认）

#### 研究方向演变与职级晋升

| 时间段 | 职级（推测） | 研究方向 |
|--------|-------------|----------|
| 2016–2018 | Research Scientist → Senior Research Scientist | GAN、Image-to-Image Translation |
| 2018–2020 | Senior Research Scientist → Research Manager | 视频生成（vid2vid）、语义图像合成（SPADE/GauGAN） |
| 2020–2022 | Research Director | 视频会议（Maxine/LivePortrait）、3D 生成 |
| 2022–2024 | Senior Research Director | Text-to-3D、Neural Surface Reconstruction |
| 2024–至今 | Vice President of Research | Physical AI、World Foundation Models（Cosmos） |

- **可信度**：★★★★（职级为基于公开头衔变化的合理推断）

#### 当前角色（2026 年）
- **职位**：Vice President of Research, NVIDIA
- **领导团队**：
  - NVIDIA Cosmos Lab（世界基础模型实验室）
  - Deep Imagination Research Group（深度想象研究组）
- **职责**：主导 NVIDIA 公司级 Generative AI 和 Physical AI 战略方向
- **团队使命**：构建 text2image、text2video、text23d 基础模型，为 NVIDIA AI Foundry 服务
- **可信度**：★★★★★（来源：NVIDIA Research 官方页面、个人网站、LinkedIn）

---

## 三、研究方向演变时间线

### Phase 1：计算机视觉与场景理解（2006–2016）
- 博士期间：图像分割、离散优化、匹配算法
- MERL 期间：场景解析、机器人视觉、街景理解
- **代表作**：博士论文 *Discrete Optimization Methods for Segmentation and Matching*（2012）

### Phase 2：GAN 与图像合成（2016–2018）
- 加入 NVIDIA 后转向生成对抗网络（GAN）
- 开创高分辨率条件图像合成
- **代表作**：
  - **pix2pixHD**（CVPR 2018）— 首个高分辨率条件 GAN 图像合成
  - CoGAN、MUNIT 等无监督图像翻译工作

### Phase 3：视频生成与语义合成（2018–2020）
- 从静态图像扩展到视频生成
- 开创语义引导的图像合成范式
- **代表作**：
  - **vid2vid**（NeurIPS 2018）— 视频到视频合成
  - **SPADE/GauGAN**（CVPR 2019）— 语义图像合成，后成为 NVIDIA Canvas 产品
  - **FUNIT**（ICCV 2019）— Few-shot 无监督图像翻译
  - MoCoGAN — 视频生成

### Phase 4：视频会议与实时应用（2020–2021）
- 将生成模型应用于实时视频通信
- **代表作**：
  - **face-vid2vid / LivePortrait**（CVPR 2021）— 单张照片驱动说话头视频
  - 成为 NVIDIA Maxine SDK 核心技术
  - World-Consistent Video-to-Video Synthesis（ECCV 2020）

### Phase 5：3D 生成与重建（2022–2023）
- 从 2D/视频扩展到 3D 内容生成
- **代表作**：
  - **Magic3D**（CVPR 2023 Highlight）— 高分辨率 Text-to-3D
  - **Neuralangelo**（CVPR 2023）— 高保真神经表面重建
    - 入选 TIME Magazine "Best Inventions of 2023"
  - **ATT3D**（ICCV 2023）— 加速 Text-to-3D

### Phase 6：Physical AI 与世界模型（2024–至今）
- 转向 Physical AI，构建世界基础模型
- 核心愿景：为机器人构建 "Matrix"——一个物理感知的模拟宇宙
- **代表作**：
  - **NVIDIA Cosmos** 系列（2025 年 1 月起）
  - Cosmos Transfer、Cosmos Predict、Cosmos Reason
  - Cosmos Policy（ICLR 2026）

---

## 四、关键论文发表时间线

### NVIDIA 之前（MERL 时期）
| 年份 | 论文 | 会议/期刊 |
|------|------|-----------|
| 2012 | *Discrete Optimization Methods for Segmentation and Matching*（博士论文） | University of Maryland |

### NVIDIA 时期

| 年份 | 论文 | 会议/期刊 | 备注 |
|------|------|-----------|------|
| 2018 | **pix2pixHD**: High-Resolution Image Synthesis and Semantic Manipulation with Conditional GANs | CVPR 2018 | 里程碑级工作 |
| 2018 | **vid2vid**: Video-to-Video Synthesis | NeurIPS 2018 | 视频合成开创性工作 |
| 2019 | **SPADE**: Semantic Image Synthesis with Spatially-Adaptive Normalization（GauGAN） | CVPR 2019 | 成为 NVIDIA Canvas 产品 |
| 2019 | **FUNIT**: Few-Shot Unsupervised Image-to-Image Translation | ICCV 2019 | |
| 2019 | **Meta-Sim**: Learning to Generate Synthetic Datasets | ICCV 2019 | |
| 2019 | Dance to Music | NeurIPS 2019 | |
| 2020 | Few-Shot Video-to-Video Synthesis | NeurIPS 2019 | |
| 2020 | World-Consistent Video-to-Video Synthesis | ECCV 2020 | |
| 2021 | **face-vid2vid**: One-Shot Free-View Neural Talking-Head Synthesis | CVPR 2021 | NVIDIA Maxine 核心技术 |
| 2021 | UNAS: Differentiable Architecture Search Meets RL | CVPR 2021 | |
| 2021 | *Generative Adversarial Networks for Image and Video Synthesis*（综述） | Proc. IEEE 109(5) | |
| 2022 | LNS-Madam: Low-Precision Training | IEEE Trans. Computers | |
| 2022 | Learning to Relight Portrait Images | ACM TOG (SIGGRAPH) | |
| 2023 | **Magic3D**: High-Resolution Text-to-3D Content Creation | CVPR 2023 Highlight | |
| 2023 | **Neuralangelo**: High-Fidelity Neural Surface Reconstruction | CVPR 2023 | TIME Best Inventions 2023 |
| 2023 | **ATT3D**: Amortized Text-To-3D Object Synthesis | ICCV 2023 | |
| 2025 | **Cosmos World Foundation Model Platform for Physical AI** | arXiv / NVIDIA | Cosmos 核心论文 |
| 2025 | Cosmos Transfer 1: World-to-World Transfer | arXiv (2503.14492) | |
| 2025 | Cosmos-Reason 1: Physical AI Common Sense | arXiv | |
| 2025 | World Simulation with Video Foundation Models for Physical AI | arXiv | |
| 2025 | Wolf: Dense Video Captioning with a World Summarization Framework | TMLR 2025 | |
| 2026 | **Cosmos Policy**: Fine-Tuning Video Models for Visuomotor Control | ICLR 2026 Poster | |
| 2026 | PhyWorldBench: Physical Realism in Text-to-Video Models | ICLR 2026 Oral | |
| 2026 | Describe Anything: Detailed Localized Image and Video Captioning | OpenReview Archive | |

- **可信度**：★★★★★（来源：DBLP、NVIDIA Research Publications、OpenReview）

---

## 五、NVIDIA Cosmos 产品发布时间线

| 日期 | 事件 | 详情 |
|------|------|------|
| **2025-01-07** | **Cosmos 1.0 发布** | CES 2025，黄仁勋开幕主题演讲。定位为"世界基础模型平台"，包含 4B-14B 参数的开源视频世界模型。被黄仁勋称为"机器人技术的 ChatGPT 时刻"。 |
| 2025-01-07 | Cosmos 技术论文发布 | arXiv 发布核心技术报告 |
| 2025-01-07 | Hugging Face 开源 | 模型权重在 Hugging Face 上开放下载 |
| **2025-03** | **Cosmos Transfer 1 & Cosmos Reason 1** | arXiv 发布（2503.14492 等），引入世界到世界迁移和物理 AI 常识推理 |
| **2025-05-16** | **GTC 2025 更新** | 推出开放式物理 AI 推理模型，新增 Cosmos Transfer、Cosmos Predict 工具链 |
| **2025-08** | **SIGGRAPH 2025** | Ming-Yu Liu 代表 NVIDIA Research 发表演讲，展示 Cosmos 在物理 AI 中的应用 |
| **2025-10-06** | **Cosmos Transfer 2.5 & Predict 2.5** | 下一代世界仿真模型发布，开源、完全可定制 |
| **2025-12** | **NeurIPS 2025** | Ming-Yu Liu 在 NeurIPS 2025 做 Cosmos 专题演讲 |
| **2026-01-05** | **CES 2026 更新** | 发布 Cosmos Transfer 2.5、Predict 2.5 的进一步更新，以及开源人形机器人基础模型 |
| **2026-03** | **GTC 2026** | Ming-Yu Liu 演讲《How Open World Models are Powering the Next Breakthroughs in Physical AI》，介绍 Cosmos Reason 2、Predict 2、Transfer 2.5 |
| **2026-04** | **ICLR 2026** | Cosmos Policy（ICLR 2026 Poster）、PhyWorldBench（ICLR 2026 Oral） |

- **可信度**：★★★★★（来源：NVIDIA 官方博客、CES/GTC/SIGGRAPH 官方记录、GitHub、arXiv）

---

## 六、重要奖项与荣誉

| 年份 | 奖项 | 详情 |
|------|------|------|
| 2014 | **R&D 100 Award** | 机器人拣料系统（MERL 时期）|
| 2015 | **RSS 最佳论文决赛** | 街景理解论文（MERL 时期） |
| 2023 | **TIME Best Inventions 2023** | Neuralangelo 入选《时代》杂志年度最佳发明 |
| **2024** | **IEEE Fellow** | 当选 2024 年度 IEEE Fellow，入选理由："for contributions to generative adversarial networks for multimodal content generation"（对多模态内容生成对抗网络的贡献） |
| — | NVIDIA Pioneer Research Award | 具体年份不详 |

- **可信度**：★★★★★（IEEE Fellow：夸智网 2024 Fellow 名单确认；R&D 100：专知报道；TIME：NVIDIA Research 官方页面）

---

## 七、产品影响力

Ming-Yu Liu 研究组的成果直接催生了 NVIDIA 三款重要产品：

1. **NVIDIA Canvas（GauGAN）**— 基于 SPADE/GauGAN（2019），让用户通过涂鸦生成逼真风景图像
2. **NVIDIA Maxine（LivePortrait/face-vid2vid）**— 基于 face-vid2vid（2021），实现视频通话中的实时人脸合成与增强
3. **NVIDIA Cosmos** — 基于世界基础模型（2025），为 Physical AI 提供模拟与训练平台

---

## 八、最近 12 个月动态（2025-06 至 2026-06）

### 2025 年下半年
- **2025-08**：SIGGRAPH 2025 特别演讲，代表 NVIDIA Research 阐述 Physical AI 愿景
- **2025-10-06**：发布 Cosmos Transfer 2.5 和 Cosmos Predict 2.5
- **2025-12**：NeurIPS 2025 Cosmos 专题演讲

### 2026 年上半年
- **2026-01-05**：CES 2026 发布 Cosmos 系列更新
- **2026-03**：GTC 2026 演讲《How Open World Models are Powering the Next Breakthroughs in Physical AI》
  - 详细介绍 Cosmos Reason 2、Predict 2、Transfer 2.5
  - 提出 Cosmos 的三大核心能力：Reason（理解）、Predict（预测）、Transfer（迁移）
- **2026-04**：ICLR 2026
  - *Cosmos Policy: Fine-Tuning Video Models for Visuomotor Control and Planning*（Poster）
  - *PhyWorldBench: A Comprehensive Evaluation of Physical Realism in Text-to-Video Models*（Oral）
  - *Describe Anything: Detailed Localized Image and Video Captioning*
- **2026-06**：GTC Taipei at Computex 2026（2026-06-01 至 06-04）

### 研究愿景（2026 年表述）
> "I'm driven by the vision of building the 'Matrix' for robots — a physics-grounded simulated universe where machines can dream, rehearse, and accumulate experience safely at scale before ever acting in the real world."
> — Ming-Yu Liu, mingyuliu.net

- **可信度**：★★★★★（来源：NVIDIA 官方、GTC/NeurIPS/ICLR 官方记录、个人网站）

---

## 九、关键合作网络

### 核心合作者
- **Ting-Chun Wang** — pix2pixHD、vid2vid、face-vid2vid、GANcraft 联合作者
- **Jan Kautz** — NVIDIA 研究副总裁，多项工作的联合作者和上级
- **Jun-Yan Zhu（朱俊彦）**— SPADE/GauGAN 联合作者（MIT → CMU）
- **Tero Karras** — StyleGAN 系列作者，FUNIT 合作者
- **Tsung-Yi Lin** — Cosmos 联合领导者
- **Sanja Fidler** — NVIDIA AI 研究负责人之一
- **Chen-Hsuan Lin** — Magic3D、Neuralangelo、ATT3D 联合作者
- **Bryan Catanzaro** — NVIDIA 应用深度学习研究副总裁

### Google Scholar 数据（截至 2026-05）
- **引用次数**：~51,088
- **研究方向**：Generative AI for Physical AI
- **可信度**：★★★★★（来源：Google Scholar）

---

## 十、信息可信度汇总

| 可信度等级 | 含义 | 适用信息 |
|-----------|------|----------|
| ★★★★★ | 多个权威来源交叉验证 | 教育背景（PhD）、NVIDIA 入职年份、IEEE Fellow、Cosmos 发布时间线、论文列表 |
| ★★★★ | 单一权威来源或合理推断 | 职级晋升时间线、MERL 职位名称 |
| ★★★ | 间接来源或推测 | Intel 经历、本科背景（未找到确切来源） |

---

## 信息源

1. NVIDIA Research 官方页面：https://research.nvidia.com/person/ming-yu-liu
2. 个人网站：https://mingyuliu.net/
3. OpenReview 简历：https://openreview.net/profile?id=~Ming-Yu_Liu1
4. DBLP 论文列表：https://dblp.org/pid/17/8368-1
5. Google Scholar：https://scholar.google.com/citations?user=y-f-MZgAAAAJ
6. IEEE Xplore 作者页面：https://ieeexplore.ieee.org/author/37086564383
7. getprog.ai 简历：https://www.getprog.ai/profile/8594682
8. NVIDIA Developer Blog：https://developer.nvidia.com/blog/author/mingyul/
9. NVIDIA Cosmos Lab：https://research.nvidia.com/labs/cosmos-lab/
10. HuggingFace Cosmos 博客：https://huggingface.co/blog/mingyuliutw/nvidia-cosmos
11. 2024 IEEE Fellow 名单：https://www.kuazhi.com/post/713060272.html
12. 专知报道：http://www.zhuanzhi.ai/topic/2001550944864766/new
13. 智猩猩公开课：https://course.zhidx.com/teacher/detail/NTQyYmEyZjJmYTQ0YjA4OGQ3NzI=

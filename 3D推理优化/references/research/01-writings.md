# Ming-Yu Liu（刘明宇/劉洺堉）— 著作与系统性长文调研

> 调研日期：2026-06-06
> 总引用数：~51,847（Google Scholar，截至 2026-05）
> 身份：NVIDIA Vice President of Research, IEEE Fellow
> 当前领导：NVIDIA Cosmos Lab（原 Deep Imagination Research Group）

---

## 一、人物概述

- **中文名**：刘明宇（繁体：劉洺堉）
- **职位**：NVIDIA 研究副总裁，IEEE Fellow
- **入选 Fellow 理由**：对多模态内容生成对抗网络的贡献（"contributions to multimodal content generative adversarial networks"）
- **当前使命**：构建面向 Physical AI 的世界基础模型（World Foundation Models），被他描述为构建"机器人的 Matrix"——一个物理模拟的虚拟宇宙，让机器人在安全环境中"做梦、排练和积累经验"
- **个人主页**：https://mingyuliu.net/
- **NVIDIA Research 页面**：https://research.nvidia.com/person/ming-yu-liu
- **Google Scholar**：https://scholar.google.com/citations?user=y-f-MZgAAAAJ&hl=en

> **来源**：NVIDIA 官方页面（一手）、个人主页（一手）、Google Scholar（一手）
> **可信度**：★★★★★

---

## 二、研究方向演变时间线

### Phase 1: GAN 基础架构（2016-2017）— 跨域图像生成
- 核心贡献：提出耦合 GAN（CoGAN）和无监督图像翻译（UNIT）框架
- 关键创新：共享潜在空间假设，实现无配对数据的跨域图像翻译

### Phase 2: 高分辨率图像合成（2018-2019）— 从像素到产品
- 核心贡献：pix2pixHD、SPADE/GauGAN
- 关键创新：多尺度生成器/判别器、空间自适应归一化
- 产品化：NVIDIA Canvas（GauGAN）

### Phase 3: 视频合成（2018-2020）— 时序一致性
- 核心贡献：Vid2Vid、MoCoGAN、World-Consistent Vid2Vid
- 关键创新：视频到视频翻译、运动-内容分解

### Phase 4: 扩散模型时代（2021-2023）— Text-to-X
- 核心贡献：eDiff-I、Magic3D、Neuralangelo、Edify Image
- 关键创新：专家集成去噪器、文本到 3D、神经表面重建

### Phase 5: Physical AI 与世界模型（2024-至今）— Cosmos
- 核心贡献：NVIDIA Cosmos 世界基础模型平台
- 关键创新：世界模拟、物理 AI 推理、具身决策

---

## 三、标志性论文（按引用量排序）

### 3.1 SPADE / GauGAN — 语义图像合成 ⭐⭐⭐⭐⭐
- **论文**：Taesung Park, Ming-Yu Liu, Ting-Chun Wang, Jun-Yan Zhu. "Semantic Image Synthesis with Spatially-Adaptive Normalization." CVPR 2019 (Oral).
- **arXiv**：https://arxiv.org/abs/1903.07291
- **引用数**：~4,000+（CVPR 2019 最具影响力论文之一）
- **核心创新**：空间自适应归一化（SPADE），解决了传统归一化层"洗掉"语义信息的问题
- **产品化**：NVIDIA Canvas（GauGAN），用户可用涂鸦生成逼真风景照
- **来源类型**：一手（Ming-Yu Liu 为共同一作/通讯作者）
- **可信度**：★★★★★

### 3.2 pix2pixHD — 高分辨率图像合成 ⭐⭐⭐⭐⭐
- **论文**：Ting-Chun Wang, Ming-Yu Liu, Jun-Yan Zhu, Andrew Tao, Jan Kautz, Bryan Catanzaro. "High-Resolution Image Synthesis and Semantic Manipulation with Conditional GANs." CVPR 2018.
- **arXiv**：https://arxiv.org/abs/1711.11585
- **引用数**：~5,000+（CVPR 2018 高引论文）
- **核心创新**：Coarse-to-fine 生成器、多尺度判别器、多层特征匹配损失
- **开源**：https://github.com/NVIDIA/pix2pixHD
- **来源类型**：一手
- **可信度**：★★★★★

### 3.3 UNIT — 无监督图像到图像翻译 ⭐⭐⭐⭐⭐
- **论文**：Ming-Yu Liu, Thomas Breuel, Jan Kautz. "Unsupervised Image-to-Image Translation Networks." NeurIPS 2017.
- **arXiv**：https://arxiv.org/abs/1703.00848
- **引用数**：~4,500+
- **核心创新**：共享潜在空间假设，基于 Coupled GANs 的无监督跨域图像翻译框架
- **开源**：https://github.com/mingyuliutw/UNIT
- **来源类型**：一手（Ming-Yu Liu 为第一作者）
- **可信度**：★★★★★

### 3.4 Vid2Vid — 视频到视频合成 ⭐⭐⭐⭐
- **论文**：Ting-Chun Wang, Ming-Yu Liu, Jun-Yan Zhu, Guilin Liu, Andrew Tao, Jan Kautz, Bryan Catanzaro. "Video-to-Video Synthesis." NeurIPS 2018.
- **arXiv**：https://arxiv.org/abs/1808.06601
- **引用数**：~2,500+
- **核心创新**：在 GAN 框架下实现高分辨率、时序一致的视频到视频翻译
- **开源**：https://github.com/NVIDIA/vid2vid
- **产品化**：NVIDIA Maxine（Vid2Vid Cameo）用于视频会议
- **来源类型**：一手
- **可信度**：★★★★★

### 3.5 CoGAN — 耦合生成对抗网络 ⭐⭐⭐⭐
- **论文**：Ming-Yu Liu, Oncel Tuzel. "Coupled Generative Adversarial Networks." NeurIPS 2016.
- **arXiv**：https://arxiv.org/abs/1606.07536
- **引用数**：~3,000+
- **核心创新**：通过强制两个生成器-判别器对之间的高层特征一致性，实现跨域联合无监督生成
- **影响**：开创了"隐空间耦合+特征对齐"范式，深刻影响了 DiscoGAN、MUNIT、StyleGAN 等后续工作
- **来源类型**：一手（Ming-Yu Liu 为第一作者）
- **可信度**：★★★★★

### 3.6 MUNIT — 多模态无监督图像翻译 ⭐⭐⭐⭐
- **论文**：Xun Huang, Ming-Yu Liu, Serge Belongie, Jan Kautz. "Multimodal Unsupervised Image-to-Image Translation." ECCV 2018.
- **arXiv**：https://arxiv.org/abs/1804.04732
- **引用数**：~3,500+
- **核心创新**：将内容与风格解耦，实现一对多的图像翻译（多模态输出）
- **来源类型**：一手
- **可信度**：★★★★★

### 3.7 FUNIT — 少样本无监督图像翻译 ⭐⭐⭐
- **论文**：Ming-Yu Liu, Xun Huang, Arun Mallya, Tero Karras, Timo Aila, Jaakko Lehtinen, Jan Kautz. "Few-Shot Unsupervised Image-to-Image Translation." ICCV 2019.
- **arXiv**：https://arxiv.org/abs/1905.01723
- **引用数**：~1,500+
- **核心创新**：仅需少量目标域样本即可实现图像翻译，无需训练时配对数据
- **开源**：https://github.com/NVlabs/FUNIT
- **来源类型**：一手（Ming-Yu Liu 为第一作者）
- **可信度**：★★★★★

### 3.8 MoCoGAN — 运动与内容分解的视频生成 ⭐⭐⭐
- **论文**：Sergey Tulyakov, Ming-Yu Liu, Xiaodong Yang, Jan Kautz. "MoCoGAN: Decomposing Motion and Content for Video Generation." CVPR 2018.
- **arXiv**：https://arxiv.org/abs/1707.04993
- **引用数**：~1,800+
- **核心创新**：将视频信号分解为内容和运动两个独立表示，实现可控视频生成
- **来源类型**：一手
- **可信度**：★★★★★

### 3.9 eDiff-I — 文本到图像扩散模型 ⭐⭐⭐
- **论文**：Yogesh Balaji, Seungjun Nah, Xun Huang, Arash Vahdat, Jiaming Song, Karsten Kreis, Miika Aittala, Timo Aila, Samuli Laine, Bryan Catanzaro, Tero Karras, Ming-Yu Liu. "eDiff-I: Text-to-Image Diffusion Models with an Ensemble of Expert Denoisers." 2022.
- **arXiv**：https://arxiv.org/abs/2211.01324
- **核心创新**：专家集成去噪器（Ensemble of Expert Denoisers），不同噪声水平使用不同专家
- **来源类型**：一手
- **可信度**：★★★★★

### 3.10 Edify Image — 商用文本到图像模型 ⭐⭐⭐
- **论文**：NVIDIA. "Edify Image: High-Quality Image Generation with a Multimodal Architecture." 2024.
- **arXiv**：https://arxiv.org/abs/2411.07126
- **核心创新**：支持文本到图像、4K 上采样、ControlNets 等多种应用
- **产品化**：为 Getty Images 和 Shutterstock 提供 GenAI 服务
- **来源类型**：一手
- **可信度**：★★★★★

### 3.11 Neuralangelo — 高保真神经表面重建 ⭐⭐⭐
- **论文**：Max Zhaoshuo Li, Thomas Müller, Alex Evans, Russell H. Taylor, Mathias Unberath, Ming-Yu Liu, Chen-Hsuan Lin. "Neuralangelo: High-Fidelity Neural Surface Reconstruction." CVPR 2023.
- **核心创新**：从 2D 视频重建高保真 3D 模型
- **荣誉**：TIME Magazine 2023 年最佳发明
- **来源类型**：一手
- **可信度**：★★★★★

### 3.12 Magic3D — 高分辨率文本到 3D ⭐⭐⭐
- **论文**：Chen-Hsuan Lin, Jun Gao, Luming Tang, Towaki Takikawa, Xiaohui Zeng, Xun Huang, Karsten Kreis, Sanja Fidler, Ming-Yu Liu, Tsung-Yi Lin. "Magic3D: High-Resolution Text-to-3D Content Creation." CVPR 2023 (Highlight).
- **核心创新**：从文本提示生成高质量 3D 纹理网格模型（coarse-to-fine）
- **来源类型**：一手
- **可信度**：★★★★★

### 3.13 I Am AI — 数字头像生成 ⭐⭐
- **论文**：Ming-Yu Liu, Koki Nagano, Yeongho Seol, Rafael Valle, Jaewoo Seo, Ting-Chun Wang, Arun Mallya, Sameh Khamis, Wei Ping, Rohan Badlani, Kevin J. Shih, Bryan Catanzaro, Simon Yuen, Jan Kautz. "I am AI: AI-driven Digital Avatar Made Easy." SIGGRAPH Real-Time Live 2021.
- **荣誉**：SIGGRAPH 2021 Best-in-Show Award
- **核心创新**：仅需一张照片即可创建高质量数字头像
- **来源类型**：一手
- **可信度**：★★★★★

### 3.14 NVIDIA Cosmos — 世界基础模型平台 ⭐⭐⭐⭐
- **论文**：Ming-Yu Liu 等. "Cosmos World Foundation Model Platform for Physical AI." 2025.
- **NVIDIA Research**：https://research.nvidia.com/publication/2025-01_cosmos-world-foundation-model-platform-physical-ai
- **GitHub**：https://github.com/nvidia-cosmos
- **核心创新**：为 Physical AI 构建世界基础模型平台，支持世界模拟、迁移学习、推理
- **衍生**：Cosmos Transfer 1（世界到世界迁移）、Cosmos-Reason 1（物理常识推理）
- **来源类型**：一手
- **可信度**：★★★★★

### 3.15 GAN 综述论文 ⭐⭐⭐
- **论文**：Arun Mallya, Ming-Yu Liu 等. "Generative Adversarial Networks for Image and Video Synthesis: Algorithms and Applications." IEEE TPAMI, 2021.
- **arXiv**：https://arxiv.org/abs/2008.02793
- **核心内容**：系统性综述 GAN 在视觉合成中的算法与应用，涵盖训练稳定性、图像翻译、图像处理、视频合成、神经渲染
- **来源类型**：一手（Ming-Yu Liu 为共同作者）
- **可信度**：★★★★★

---

## 四、反复出现≥3次的核心研究主题

### 4.1 跨域图像翻译（Cross-Domain Image Translation）
出现次数：CoGAN → UNIT → MUNIT → FUNIT → pix2pixHD → SPADE（6 次）
- 核心问题：如何在不同视觉域之间建立映射
- 演进：从配对→无配对→少样本→多模态输出

### 4.2 条件生成对抗网络（Conditional GANs for Synthesis）
出现次数：pix2pixHD → SPADE → Vid2Vid → eDiff-I → Edify（5 次）
- 核心问题：如何根据条件信号（语义图、文本等）生成高质量图像/视频
- 演进：从低分辨率→高分辨率→4K，从单一条件→多模态条件

### 4.3 视频合成与时序一致性（Video Synthesis & Temporal Consistency）
出现次数：MoCoGAN → Vid2Vid → World-Consistent Vid2Vid → Cosmos（4 次）
- 核心问题：如何生成时序一致的视频
- 演进：从单帧生成→时序平滑→物理一致的世界模拟

### 4.4 3D 内容生成（3D Content Generation）
出现次数：Neuralangelo → Magic3D → ATT3D → Cosmos（4 次）
- 核心问题：如何从 2D 信息重建/生成 3D 内容
- 演进：从 3D 重建→文本到 3D→世界模拟

### 4.5 从研究到产品的转化（Research-to-Product）
出现次数：GauGAN→Canvas, Vid2Vid→Maxine, Edify→Picasso, Cosmos（4 次）
- 核心理念：研究必须转化为开发者和企业可构建的平台

---

## 五、自创方法/模型汇总

| 方法/模型 | 年份 | 会议 | 核心创新 | 产品化 |
|-----------|------|------|----------|--------|
| CoGAN | 2016 | NeurIPS | 耦合 GAN，跨域联合生成 | — |
| UNIT | 2017 | NeurIPS | 共享潜在空间，无监督图图翻译 | — |
| pix2pixHD | 2018 | CVPR | 高分辨率条件 GAN | — |
| MoCoGAN | 2018 | CVPR | 运动-内容分解视频生成 | — |
| MUNIT | 2018 | ECCV | 多模态无监督图图翻译 | — |
| Vid2Vid | 2018 | NeurIPS | 视频到视频合成 | NVIDIA Maxine |
| SPADE/GauGAN | 2019 | CVPR Oral | 空间自适应归一化 | NVIDIA Canvas |
| FUNIT | 2019 | ICCV | 少样本无监督图图翻译 | — |
| I Am AI | 2021 | SIGGRAPH RTL | 单图数字头像 | NVIDIA Maxine |
| eDiff-I | 2022 | arXiv | 专家集成去噪器 | — |
| Neuralangelo | 2023 | CVPR | 高保真神经 3D 重建 | — |
| Magic3D | 2023 | CVPR Highlight | 文本到 3D | — |
| Edify Image | 2024 | arXiv | 多模态文本到图像 | Getty/Shutterstock |
| Cosmos | 2025 | — | 世界基础模型平台 | NVIDIA AI Foundry |

---

## 六、NVIDIA Research Blog 文章

Ming-Yu Liu 作为 NVIDIA 技术博客作者，参与撰写了多篇技术文章。以下是已确认的相关内容：

### 6.1 NVIDIA AI Podcast: World Models（2025-01）
- **标题**：World Simulation With Video Foundation Models for Physical AI
- **内容**：Ming-Yu Liu 在 NVIDIA AI Podcast 中详细介绍了世界基础模型（WFM），解释了 WFM 如何作为模拟物理世界的强大神经网络
- **文字稿**：https://blogs.nvidia.com/wp-content/uploads/2025/01/AI-Podcast-World-Models-Transcript-1.pdf
- **来源类型**：一手（Ming-Yu Liu 本人访谈）
- **可信度**：★★★★★

### 6.2 Neuralangelo Blog Post（2023-06）
- **标题**：NVIDIA Research Reconstructs 3D Scenes From 2D Video
- **内容**：介绍 Neuralangelo 如何从手机视频重建高保真 3D 场景
- **博客**：https://blogs.nvidia.com/blog/2023/06/01/neuralangelo-ai-research-3d-reconstruction/
- **Ming-Yu Liu 引言**："Neuralangelo 具备的 3D 重建能力将能极大地造福创作者，帮助他们在数字世界中创建出现实世界的逼真虚拟复制品。"
- **来源类型**：一手
- **可信度**：★★★★★

### 6.3 NVIDIA Developer Blog 作者页面
- **URL**：https://developer.nvidia.com/blog/author/mingyul/
- **简介**：Ming-Yu Liu 是 NVIDIA 研究副总裁、IEEE Fellow，领导 Deep Imagination Research Group
- **来源类型**：一手
- **可信度**：★★★★★

---

## 七、推荐的论文/工具/框架

### 7.1 开源代码库
- **UNIT**：https://github.com/mingyuliutw/UNIT（Ming-Yu Liu 个人 GitHub）
- **pix2pixHD**：https://github.com/NVIDIA/pix2pixHD
- **Vid2Vid**：https://github.com/NVIDIA/vid2vid
- **SPADE**：https://github.com/NVlabs/SPADE
- **FUNIT**：https://github.com/NVlabs/FUNIT
- **Neuralangelo**：https://research.nvidia.com/labs/dir/neuralangelo/
- **Cosmos**：https://github.com/nvidia-cosmos

### 7.2 推荐阅读顺序（理解其研究脉络）
1. CoGAN (2016) → 理解跨域生成的基础思想
2. UNIT (2017) → 无监督图图翻译的里程碑
3. pix2pixHD (2018) → 高分辨率条件生成
4. MUNIT (2018) → 多模态输出的优雅解法
5. Vid2Vid (2018) → 从图像到视频的跨越
6. SPADE/GauGAN (2019) → 归一化层的革新 + 产品化典范
7. FUNIT (2019) → 少样本泛化能力
8. eDiff-I (2022) → 进入扩散模型时代
9. Magic3D/Neuralangelo (2023) → 3D 生成
10. Cosmos (2025) → Physical AI 的终极愿景

### 7.3 系统性综述
- **GAN 综述**：Mallya & Liu 等. "Generative Adversarial Networks for Image and Video Synthesis: Algorithms and Applications." IEEE TPAMI 2021. https://arxiv.org/abs/2008.02793
  - 覆盖 GAN 训练稳定性、图像翻译、图像处理、视频合成、神经渲染

---

## 八、关键合作者网络

| 合作者 | 身份 | 合作次数 | 代表作 |
|--------|------|----------|--------|
| Ting-Chun Wang | NVIDIA Research | ≥5 | pix2pixHD, Vid2Vid, SPADE |
| Jun-Yan Zhu | MIT → CMU | ≥4 | pix2pixHD, SPADE, Vid2Vid |
| Jan Kautz | NVIDIA VP Research | ≥5 | UNIT, MUNIT, FUNIT, MoCoGAN |
| Taesung Park | NVIDIA/UC Berkeley | ≥2 | SPADE |
| Xun Huang | NVIDIA → Meta | ≥3 | MUNIT, FUNIT, eDiff-I |
| Arun Mallya | NVIDIA Research | ≥3 | FUNIT, Vid2Vid, GAN Survey |
| Bryan Catanzaro | NVIDIA VP | ≥3 | pix2pixHD, Vid2Vid, eDiff-I |
| Chen-Hsuan Lin | NVIDIA Research | ≥2 | Magic3D, Neuralangelo |
| Tsung-Yi Lin | NVIDIA Research | ≥2 | Magic3D, Cosmos |
| Tero Karras | NVIDIA Research | ≥2 | FUNIT, eDiff-I |

---

## 九、发现的矛盾/待确认信息

1. **IEEE Fellow 年份**：多个来源确认 Ming-Yu Liu 是 IEEE Fellow，但具体授予年份未在搜索结果中明确找到。有来源列出他入选了 2024 年 IEEE Fellow 名单，但需进一步确认。
   - **来源**：CSDN 博客（二手）提到 "Ming-Yu Liu NVIDIA Research 入选理由：对多模态内容生成对抗网络的贡献"
   - **可信度**：★★★☆☆

2. **Edify vs Picasso**：不同来源对产品名称使用不一致。NVIDIA Research 页面提到 "NVIDIA Picasso [Edify]"，而 Developer Blog 提到 "NVIDIA Edify"。这可能是品牌重命名。
   - **可信度**：★★★★☆（NVIDIA 官方来源）

3. **研究组名称变更**：早期称为 "Deep Imagination Research Group"，最新称为 "NVIDIA Cosmos Lab"。个人主页使用后者，NVIDIA Research 页面使用前者。
   - **可信度**：★★★★☆（均为官方来源，可能是过渡期）

4. **引用数精度**：Google Scholar 总引用 51,847 是截至 2026-05-25 的快照，具体论文引用数因搜索工具限制未能逐一精确获取，标注的引用数基于多个来源的交叉验证，可能存在 ±10% 偏差。
   - **可信度**：★★★☆☆

---

## 十、信息来源汇总

| 来源 | URL | 类型 | 可信度 |
|------|-----|------|--------|
| NVIDIA Research 个人页 | https://research.nvidia.com/person/ming-yu-liu | 一手 | ★★★★★ |
| 个人主页 | https://mingyuliu.net/ | 一手 | ★★★★★ |
| Google Scholar | https://scholar.google.com/citations?user=y-f-MZgAAAAJ | 一手 | ★★★★★ |
| Deep Imagination Research | https://deepimagination.cc/ | 一手 | ★★★★★ |
| NVIDIA Developer Blog | https://developer.nvidia.com/blog/author/mingyul/ | 一手 | ★★★★★ |
| arXiv 论文 | 各论文链接 | 一手 | ★★★★★ |
| NVIDIA AI Podcast 文字稿 | https://blogs.nvidia.com/wp-content/uploads/2025/01/AI-Podcast-World-Models-Transcript-1.pdf | 一手 | ★★★★★ |
| InnoVEX Speaker 页面 | https://innovex.computex.biz/show/speaker.aspx?type=cyber&ID=108 | 二手 | ★★★★☆ |
| 36氪报道 | https://www.36kr.com/p/3419670908243329 | 二手 | ★★★☆☆ |

> **注意**：本调研未使用知乎、微信公众号、百度百科作为信息源。

# Pierrick Picaut (P2design) — 著作与系统性长文调研

> 调研时间：2026-06-05
> 信息源：一手来源（p2design-academy.com、artstation.com、blendernation.com、blender.community）+ 二手来源（知乎笔记、中文媒体、课程搬运站）
> 黑名单已排除：知乎、微信公众号、百度百科仅作为二手参考线索，核心事实以一手来源为准

---

## 一、人物背景

- **全名**：Pierrick Picaut
- **网名/品牌**：P2design / p2design / pieriko
- **国籍**：法国
- **职业身份**：
  - Blender Foundation 认证培训师（Blender Foundation Certified Trainer）
  - P2design 公司 CEO（专注动画与绑定教育内容）
  - 前 Atypique Studio 游戏艺术总监 & 动画总监（Art & Animation Director）
  - 现任亚马逊游戏公司高级动画师（Senior Animator @ Amazon Games，据网易报道）
  - 自学成才的 3D 艺术家，拥有 10+ 年 Blender 经验
- **来源**：pieriko.artstation.com/resume、blender.community、163.com
- **可信度**：ArtStation 简历为一手来源，可信度高；亚马逊游戏公司任职信息来自中文媒体转述，可信度中等

---

## 二、出版课程（按时间线）

### 1. The Art of Effective Rigging in Blender（高效绑定的艺术）
- **类型**：系统性付费课程（视频教程）
- **发布平台**：最初 Gumroad/Blender Market，后迁移至 p2design-academy.com
- **内容规模**：约 10 小时，分 6+ 章节
- **价格**：$89（p2design-academy.com）
- **核心内容**：
  - 第一章：绑定基础概念与工具（父子级绑定、约束工具，约27分钟）
  - 第二章：绑定实操（约2小时10分钟）
  - 第六章：细节绑定与高级技巧（5个视频，46分钟）
  - 涵盖：骨骼层级系统（DEF/TGT/MCH/CTRL 四层架构）、旋转模式（欧拉角与四元数）、万向节死锁、IK/FK切换、驱动器设置、动作约束、权重绘制
- **他的绑定方法论（自创术语/架构）**：
  - **四层骨骼架构**：DEF（Deform，蒙皮形变层）→ TGT（Target，目标层，约束DEF）→ MCH（Mechanism，机械层）→ CTRL（Controller，用户控制层）
  - 这套架构的核心思想：将形变通道完全剥离，使骨骼控制独立存在
  - 骨骼命名规范：使用前缀（DEF_、TGT_、MCH_、CTRL_）+ 身体部位 + 左右标记（.L/.R）
  - 骨骼层级管理：将不同功能的骨骼放入不同 Layer
- **来源**：p2design-academy.com、CSDN博客课程笔记、知乎课程笔记
- **可信度**：课程结构信息来自多个独立来源交叉验证，可信度高

### 2. Alive! Animation Course in Blender（活着！Blender动画课程）
- **类型**：系统性付费课程（视频教程）
- **发布日期**：2021年8月13日（BlenderNation 公告）
- **发布平台**：Gumroad → p2design-academy.com
- **内容规模**：150+ 个视频，超过 28 小时内容
- **价格**：$89（p2design-academy.com）
- **核心内容**：
  - 从最基础的运动到高端高级角色动作动画
  - 覆盖所有动画基础知识
  - 提供所有 rig 和 Blender 文件
  - 适用对象：从完全的动画初学者到有经验的动画师
  - 课程理念："Getting access to educational animation content is generally quite expensive and often difficult to know where to start. That's why I've designed this course to give you all the fundamentals of animation"
- **来源**：blendernation.com（Pierrick 本人发布公告）、p2design-academy.com
- **可信度**：一手来源，可信度高

### 3. The Art of Effective Rigging 2（高效绑定的艺术 2）
- **类型**：系统性付费课程（续作）
- **发布日期**：约 2024 年（blender.fi 报道于 2024-06-05）
- **价格**：$89（p2design-academy.com）
- **核心内容**：绑定进阶课程
- **来源**：p2design-academy.com、blender.fi
- **可信度**：一手来源，可信度高

### 4. Crimson Ronin（赤红浪人）
- **类型**：付费课程
- **价格**：$45（p2design-academy.com）
- **核心内容**：
  - PBR 风格化游戏角色创建全流程
  - 使用三工具管线：ZBrush（雕刻）+ Blender（建模/绑定）+ Substance Painter（贴图）
  - 8500 三角面的游戏角色
  - 涵盖雕刻、拓扑、UV、贴图、渲染全流程
- **来源**：p2design-academy.com、pieriko.artstation.com、online-courses.club
- **可信度**：一手来源，可信度高

### 5. The Gameboy Project（游戏男孩项目）
- **类型**：免费课程
- **价格**：免费（或自愿付费支持 Gumroad）
- **核心内容**：
  - 从建模到动画的完整角色课程
  - 100% 在 Blender 中完成
  - 包含 rig、动画和渲染
- **来源**：p2design-academy.com、blender.tekriss.com
- **可信度**：一手来源，可信度高

### 6. Real Time Explosion - Blender Mini Course（实时爆炸 - 迷你课程）
- **类型**：免费迷你课程
- **核心内容**：Blender EEVEE 实时爆炸特效（不使用模拟，纯手K）
- **来源**：p2design-academy.com
- **可信度**：一手来源，可信度高

### 7. CG Cookie 课程
- **课程名称**：Blender Rigging Fundamentals（Blender 绑定基础）
- **发布平台**：CG Cookie
- **核心内容**：约束、父子级、骨骼等绑定基础知识
- **特色**：附赠免费 rig
- **来源**：digitalproduction.com（2025-05-12 报道）
- **可信度**：二手报道，可信度中高

---

## 三、YouTube/社区长篇教程与技术文章

### blender.community 上的内容（Pierrick 官方社区页面）
以下是他在 Blender Community 发布的系统性教程标题（按时间倒序）：

1. **"BreakDowner: The Blender Tool That Will SPEED UP Your 3D Animation!"** — 2026年（约2周前）
2. **"Retarget Characters Animations to Multiple Rigs in Blender"** — 2026年（约1月前）
3. **"Create a Walk Cycle animation in Blender"** — 2026年（约2月前）
4. **"Master Production Workflow in Blender!"** — 2026年（约3月前）
5. **"How to make Professional looking rigs in Blender"** — 2025年（约6月前）
6. **"How I Made Animation SO Much Faster With This Trick! Pose Library"** — 2025年（约10月前）
7. **"Pose & Flow: The 2 Essentials of Great Action Animation"** — 约2025年
8. **"The 5 steps to great 3D animation in Blender"** — 约2025年
9. **"Make Two-Handed Weapons Work PERFECTLY in Your Blender rig"** — 约2025年
10. **"My secret Blender weights painting & skinning trick you will love!"** — 约2025年
11. **"Make Perfect Root Motion in Blender Without Any Add-ons!"** — 约2025年
12. **"My favorite animation addon for Blender"** — 约2025年
13. **"Rigging and animating tails, whip and chains in blender the easy way"** — CGI Academy Hub 收录
14. **"Custom properties and drivers in blender made easy"** — CGI Academy Hub 收录

- **来源**：blender.community/p2design/、cgiacademyhub.com
- **可信度**：一手来源，可信度高

### ArtStation 项目页面（含技术分解）
- **Wind Blade**：动漫风格短片，Blender 制作
- **MOGSY**：Rigging and Gameplay animation
- **Kalinga**：Rig and animations
- **Sawangi**：Rigging and gameplay animation
- **Mana**：Rigging and Gameplay animation
- **NOARA: The Conspiracy**：游戏项目，包含 Crabe 角色绑定分解

- **来源**：pieriko.artstation.com
- **可信度**：一手来源，可信度高

### LinkedIn 技术分享
- 多个 shot breakdown 分享，核心方法论：
  - "Once the camera position is set, I only animate the character, creating a better 2D look and making any further editing way simpler"
  - "All backgrounds are 2D paintings. All VFX are 2D. Camera location never changes to keep that 2D look"
  - 这体现了他将 2D 美学融入 3D 工作流的方法论

- **来源**：linkedin.com/pierrick-picaut
- **可信度**：一手来源，可信度高

---

## 四、反复出现的核心论点（≥3次出现 = 真信念）

### 1. "绑定必须是分层的、模块化的"
- 出现场景：Art of Effective Rigging 课程全篇、Crabe Rigging 分解、Blender社区教程
- 核心表达：四层骨骼架构（DEF/TGT/MCH/CTRL），将形变与控制完全分离
- 这是他绑定方法论的基石

### 2. "动画的基础是姿势（Pose）和流动（Flow）"
- 出现场景："Pose & Flow: The 2 Essentials of Great Action Animation"、Alive! 课程
- 核心表达：好的动作动画 = 好的姿势设计 + 好的运动流线
- 这是他动画哲学的核心二元论

### 3. "Blender 是专业级工具，不需要其他软件"
- 出现场景：几乎所有课程宣传、Blender Foundation 认证、Blender Development Fund 钛金赞助
- 核心表达：课程 100% 在 Blender 中完成；P2design 是 Blender Development Fund 的钛金级赞助者
- 矛盾点：Crimson Ronin 课程使用了 ZBrush + Substance Painter + Blender 三工具管线，说明他认为特定任务（如高模雕刻、PBR贴图）仍需专业工具

### 4. "动画技术可以跨软件迁移"
- 出现场景：Alive! 课程公告原文
- 核心表达："you will be able to transfer all these techniques to any other software or animation medium"
- 他认为动画原则是普适的，工具只是载体

### 5. "权重绘制有秘密技巧"
- 出现场景："My secret Blender weights painting & skinning trick"、Blender to Unreal/Unity 导出教程
- 核心表达：权重绘制是绑定中最关键也最容易出错的环节，有系统化的优化方法

### 6. "游戏引擎导出是绑定工作流的必要环节"
- 出现场景："Blender to Unreal & Unity" 教程、NOARA 游戏项目、Crimson Ronin 课程
- 核心表达：绑定不能只考虑 Blender 内部使用，必须考虑导出到游戏引擎的兼容性

### 7. "2D 美学可以通过 3D 技术实现"
- 出现场景：Wind Blade 短片、LinkedIn shot breakdown、动画风格研究
- 核心表达：固定摄像机位置 + 2D 背景 + 2D 特效 + 3D 角色动画 = 动漫风格

---

## 五、自创术语与概念

### 1. 四层骨骼架构（DEF / TGT / MCH / CTRL）
- **DEF（Deform）**：实际影响网格形变的骨骼，权重都刷在这一层
- **TGT（Target）**：目标骨骼，约束 DEF 骨骼，是 DEF 的"备份"
- **MCH（Mechanism）**：机械骨骼，处理复杂的约束逻辑
- **CTRL（Controller）**：用户直接操作的控制器
- **来源**：Art of Effective Rigging 课程、知乎课程笔记
- **可信度**：此架构在多个独立来源中一致描述，可信度高

### 2. "Pose & Flow" 二元论
- 好的动作动画 = Pose（关键姿势的设计感）+ Flow（运动过程的流畅性）
- **来源**：blender.community 教程标题
- **可信度**：中高（仅从标题推断，未见完整论述）

### 3. "The 5 steps to great 3D animation"
- 具体五步内容未在搜索中完整展开，但从标题可推断是他对动画工作流的系统化总结
- **来源**：blender.community
- **可信度**：中（仅见标题）

### 4. "BreakDowner"
- 他推荐/推广的一个 Blender 工具/插件，用于加速动画制作
- **来源**：blender.community
- **可信度**：中（可能是他推荐的第三方工具而非自创）

---

## 六、推荐资源与智识谱系

### 他推荐的资源（间接推断）
- **《The Art of Rigging》系列**：多个中文学习者在讨论他的课程时提及此书，暗示他可能在课程中推荐过
- **Blender Foundation 官方教程**：作为认证培训师，他与 Blender Foundation 有深度合作
- **CG Cookie**：他在 CG Cookie 上有课程，说明认可该平台的教育理念

### 智识谱系推断
- **Blender Foundation 生态**：他是 Blender Foundation 认证培训师 + 钛金赞助者，深度绑定 Blender 生态
- **游戏行业出身**：前 Atypique Studio 艺术总监，现亚马逊游戏动画师，说明他的教学方法论根植于实际游戏开发需求
- **法国 CG 教育传统**：法国在 CG 教育方面有深厚传统（Gobelins 等），他的系统化教学风格可能受此影响
- **自学成才路径**：自称自学成才，这影响了他"让复杂技术变得可接近"的教学理念

---

## 七、作品与项目（揭示实践偏好）

### 游戏项目
- **NOARA: The Conspiracy**（Atypique Studio）：包含 Crabe 角色绑定，有详细技术分解
- **Norara/Lands of Noara**：为该游戏制作了大量动画设计，美式卡通风格
- **MOGSY、Kalinga、Sawangi、Mana**：多个游戏角色的绑定与动画

### 短片/动画项目
- **Wind Blade**：动漫风格短片，Blender 制作，展示 2D+3D 混合工作流
- **人体模型大战系列**：Suzanne Award 提名作品，用人体模型（附录体）做超级英雄动画，展示创意与技术结合

---

## 八、矛盾与张力

### 1. "纯 Blender" vs "多工具管线"
- **矛盾**：他宣传"100% Blender 完成"，但 Crimson Ronin 课程使用 ZBrush + Substance Painter
- **调和**：可能是指动画/绑定环节 100% Blender，建模和贴图环节使用专业工具

### 2. "免费教育" vs "付费课程"
- **矛盾**：Alive! 课程公告中说"Getting access to educational animation content is generally quite expensive"，暗示他想降低门槛，但课程售价 $89
- **调和**：他提供免费课程（Gameboy Project、Real Time Explosion、免费 rig），付费课程是进阶内容

### 3. "动画原则普适性" vs "Blender 专精"
- **矛盾**：他说技术可跨软件迁移，但所有课程都以 Blender 为核心
- **调和**：他认为动画原则是普适的，但选择 Blender 作为教学载体是因为它是免费开源的

---

## 九、信息源清单

| # | 来源 | URL | 类型 | 可信度 |
|---|------|-----|------|--------|
| 1 | P2design Academy 官网 | p2design-academy.com | 一手 | ★★★★★ |
| 2 | ArtStation 简历 | pieriko.artstation.com/resume | 一手 | ★★★★★ |
| 3 | ArtStation 作品集 | pieriko.artstation.com/projects | 一手 | ★★★★★ |
| 4 | Blender Community 页面 | blender.community/p2design/ | 一手 | ★★★★★ |
| 5 | BlenderNation Alive! 公告 | blendernation.com/2021/08/13/... | 一手（Pierrick 本人发布） | ★★★★★ |
| 6 | CG Cookie Rigging 课程报道 | digitalproduction.com/2025/05/12/... | 二手 | ★★★★ |
| 7 | blender.fi Crabe Rigging 分解 | blender.fi/2020/01/16/... | 二手（转述 YouTube 内容） | ★★★★ |
| 8 | blender.fi Rigging 2 报道 | blender.fi/2024/06/05/... | 二手 | ★★★★ |
| 9 | LinkedIn 技术分享 | linkedin.com/pierrick-picaut | 一手 | ★★★★★ |
| 10 | Cubebrush 页面 | cubebrush.co/p2design | 一手 | ★★★★ |
| 11 | anima.to 页面 | anima.to/pierrickpicaut/ | 一手 | ★★★★ |
| 12 | 网易 163 报道 | 163.com/dy/article/K7FTT7670516BJGJ.html | 二手 | ★★★ |
| 13 | CSDN 课程笔记 | blog.csdn.net/weixin_42617406/... | 二手 | ★★★ |
| 14 | 知乎课程笔记 | zhuanlan.zhihu.com/p/658173718 | 二手 | ★★★ |
| 15 | 知乎课程介绍 | zhuanlan.zhihu.com/p/131284948 | 二手 | ★★★ |
| 16 | 知乎课程介绍 | zhuanlan.zhihu.com/p/101986673 | 二手 | ★★★ |
| 17 | 搜狐角色动图合集 | sohu.com/sa/749407809_121123989 | 二手 | ★★★ |
| 18 | CGI Academy Hub | cgiacademyhub.com/channels/72 | 二手 | ★★★★ |
| 19 | classcentral.com 课程列表 | classcentral.com/institution/pierrick-picaut | 二手 | ★★★★ |
| 20 | Gumroad 页面 | p2design.gumroad.com | 一手 | ★★★★★ |
| 21 | Bilibili 学习者分享 | bilibili.com/opus/725317021810556982 | 二手 | ★★★ |

---

## 十、待补充/未确认信息

1. **是否有出版书籍**：未发现传统出版物（纸质书/电子书），他的知识输出形式是视频课程和 YouTube 教程
2. **是否有博客/长文**：未发现独立博客上的系统性长文，他的内容输出以视频为主
3. **"The 5 steps to great 3D animation" 的具体内容**：仅见标题，未找到完整文字版
4. **"Pose & Flow" 的完整理论框架**：仅见标题和课程片段，需要观看完整视频
5. **他在亚马逊游戏公司的具体项目**：中文媒体提及但未有详细报道
6. **他的推荐书单**：未在公开渠道找到系统性的推荐阅读清单
7. **Blender Conference 演讲**：搜索未找到他在 Blender Conference 上的正式演讲记录

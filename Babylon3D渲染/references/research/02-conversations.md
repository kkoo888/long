# David Catuhe — 长对话、播客与深度采访

> 调研时间：2026-06-06
> 说明：标注 [一手] = 直接来自 David Catuhe 本人（博客、推文、演讲）；[二手] = 媒体/他人转述

---

## 1. Babylon.js 起源故事 — David Catuhe 自述（法语博客）

**来源**: catuhe.com "Rétrospective" 博文
**URL**: https://www.catuhe.com/retrospective/
**可信度**: ★★★★★ [一手] — David Catuhe 本人博客原文

**核心内容（法语原文翻译）**：

David Catuhe 在微软开放式办公区工作时，突然意识到周围7个人都在为他创建的产品工作，而且是带薪的。他称之为"疯狂的故事"。

**关键时间线（David 自述）**：
- **2010年**：Babylon（没有 .js）最初为 **Silverlight 5** 创建
- **2011年**：与 David Rousset 一起转向 Web 技术，将 Babylon 移植为 Babylon.js，因为微软即将发布 IE11 支持 WebGL
- **早期**：这是他的"pet project"（个人业余项目），他和 David Rousset 利用它来"像狡猾的布道者一样"（comme des gros fourbes d'évangélistes）被邀请参加技术会议，借此推广 IE 和 Edge
- **搬到西雅图后**：Babylon.js 仍然是他的"pet project"——"你为了编程的乐趣而构建的那段代码"
- **社区成长**：社区开始认同他们的理念——"简单优先"（simplicité avant tout）、使用 TypeScript（"感谢 David 说服了我"）、对问题/bug 超级响应（turbo-réactifs）。企业开始在专业项目中使用 Babylon.js
- **2017年转折**：微软内部有人意识到 Babylon.js 被大量微软产品使用——PowerPoint、Xbox GamePad、Avatar、Visio、Dynamics 等。于是成立了专门团队
- **结果**：David 成为7人团队的负责人，"为一个我最初作为一个游戏、在一个巴黎发霉的公寓里、窗外对着铁路的周末开始的东西"

**David 的感悟**："我不知道是靠直觉还是运气，但我可以告诉你，每天早上醒来我都很感激自己的幸运。"

---

## 2. MS Dev Show 播客 — Episode 138

**来源**: MS Dev Show Podcast
**URL**: https://thewindowsupdate.com/2017/02/03/episode-138-3d-web-using-babylon-js-with-david-catuhe-ms-dev-show/
**发布日期**: 2017年2月3日
**可信度**: ★★★★☆ [一手] — David Catuhe 直接参与的播客对话

**内容概要**：
- David Catuhe 讨论了使用 Babylon.js 进行 Web 3D 开发
- 涵盖 Babylon.js 的技术细节和使用场景
- 该播客同时在 iTunes、YouTube、Channel 9（微软开发者频道）上线

---

## 3. InfoQ 中文专访 — Babylon.js 团队负责人 Jason Carter

**来源**: InfoQ 中国卓越技术团队访谈录（2022年第二季）
**URL**: https://cloud.tencent.com/developer/article/2250977
**采访对象**: Jason Carter（Babylon.js 首席工程和产品负责人）
**可信度**: ★★★★☆ [二手] — 团队负责人而非 David 本人，但代表团队观点

**关键回答摘录**：

**关于 Babylon.js 初衷**：
> "开发 Babylon.js 的初衷，是希望帮助所有 Web 开发者在应用程序中充分运用 GPU 的强大功能。WebGL 是一个面向浏览器的强大图形 API，但其本身既复杂又极具深度。很多 Web 开发者发现直接使用 WebGL API 进行编程难度非常高。因此，Babylon.js 希望可以降低甚至消除这种门槛。"

**关于 WebGPU 挑战**：
> "近两年来，我们一直致力于在 Babylon.js 中支持 WebGPU。这是段疯狂、艰险但又充满意义的探索。WebGPU 为 Web 开发者们带来了一系列令人难以置信的全新功能，例如使用计算着色器执行更高级的 GPU 运算。最复杂的问题在于其中涉及一种全新的着色器语言（WGSL）。"

**关于 Three.js 竞争**：
> "其实没啥冲突。我们也很喜欢 Three.js，欣赏那些使用 Three.js 将精彩的想法转化成现实的开发人员。我们双方相互激励，共同进步。我们有着相同的热情，就是想把强大的 GPU 功能交付到 Web 开发者手中。两个有着共同努力方向的项目怎么会有冲突呢，我觉得双方更像是队友的关系。"

**关于包体积问题**：
> "大家也可以根据实际需求构建定制化 Babylon.js 包。我们已经看到不少 Web 应用程序在特定场景中使用定制化 Babylon.js 包，大小仅为 92 kb。"

**关于 WebXR 和元宇宙**：
> "随着新冠疫情席卷全球，很多人被迫长期居家远程办公。于是乎，很多企业开始使用 Babylon.js 构建起真实、生动的元宇宙体验。Frame（framevr.io）就是个很好的例子。"

---

## 4. David Catuhe — "Unleash 3D rendering with WebGL"（微软 Build 大会）

**来源**: 微软 Build 2015 / SitePoint 系列
**URL**: 参考引用见 https://blog.csdn.net/weixin_33856370/article/details/85787151
**可信度**: ★★★★☆ [一手] — David Catuhe 在微软 Build 大会上的演讲

**内容概要**：
- David Catuhe 在微软 Build 大会上演示了 WebGL 的 3D 渲染能力
- 展示了 vorlon.js 和 babylonJS 项目
- 这是他作为微软技术布道师的标志性演讲之一

---

## 5. Vorlon.js — Build 2015 发布

**来源**: InfoQ、MSDN 博客
**URL**: https://www.infoq.cn/article/2015/05/MSFT-VorlonJS
**可信度**: ★★★★☆ [一手/二手混合]

**内容概要**：
- 在微软 Build 2015 大会主题演讲中发布
- Vorlon.js 是一个 JavaScript 远程调试工具
- 创造者：David Catuhe、Pierre Lagarde、David Rousset
- David Catuhe 被描述为"微软项目经理，同时也是 Babylon.js 框架的作者"

---

## 6. David Catuhe 的 "Soft Engine" 教程系列（David Rousset 博客）

**来源**: David Rousset 的 MSDN 博客
**URL**: http://blogs.msdn.com/b/davrous/ (原始系列)
**可信度**: ★★★★☆ [一手] — David Catuhe 的教学内容

**内容概要**：
- David Catuhe 在微软内部举办 3D 研讨会
- 与 David Rousset 合作创建了免费的8模块 MVA（Microsoft Virtual Academy）课程
- 教授 3D、WebGL 和 Babylon.js 的基础知识
- David Rousset 说："事实上，我目前正在学习3D的基础知识，这要归功于令人敬畏的 David Catuhe 在 Microsoft 内部举办的内部研讨会"

---

## 7. Babylon.js Playground 和文档体系

**来源**: Babylon.js 官方文档
**URL**: https://doc.babylonjs.com/
**可信度**: ★★★★★ [一手]

**Jason Carter 推荐的三大资源**：
1. **Babylon.js Playground** (https://playground.babylonjs.com/) — 最适合新手的入门课堂，可更改代码并立即查看渲染结果
2. **说明文档** (https://doc.babylonjs.com/) — 包括《你的第一步》等引导文档
3. **社区论坛** (https://forum.babylonjs.com/) — "我想把所有美好的词汇都献给我们的社区"

---

## 8. David Catuhe 的 Frame VR 创业

**来源**: LinkedIn 帖子
**URL**: https://www.linkedin.com/posts/dcatuhe_webgl-webgpu-3d-activity-7204524614599790592-uYNq
**发布日期**: 2024年6月7日
**可信度**: ★★★★☆ [一手]

**内容概要**：
- David Catuhe 在 LinkedIn 发帖招聘
- "Hiring: At Frame we're building an incredible product at the intersection of AI and 3D, where embodied agents help teams be more productive and creative."
- 标签: #webgl #webgpu #3d
- 这表明 David 正在探索 **AI + 3D** 的交叉领域

---

## 9. David Catuhe 个人作品生态

**来源**: deltakosh.com/bio + GitHub
**URL**: https://deltakosh.com/bio/
**可信度**: ★★★★★ [一手]

**内容概要**：
- 创建了 Babylon.js、Spector.js（WebGL 调试工具）
- 创建了 UrzaGatherer（集换式卡牌游戏工具）
- 创建了 UWP Community Toolkit
- 创建了 Collecto（应用）
- 写奇幻小说
- 做数字艺术
- 开发独立应用

---

## 10. Spot CEO 关于选择 Babylon.js 的反馈

**来源**: bimant.com 博客
**URL**: http://www.bimant.com/blog/babylon-js-vs-three-js/
**可信度**: ★★★★☆ [二手] — 第三方使用体验

**关键引用**：
> "当我们首次宣布我们的产品时，我们能够与原始创作者 David Catuhe 会面，并获得了一些直接反馈。事实上，David 在 Microsoft 工作，该公司在内部对 Babylon.js 进行了大量投资，并有专门的员工从事该项目。"

---

## 11. Babylon.js 官方 YouTube 频道

**来源**: YouTube
**URL**: https://www.youtube.com/c/BabylonJS
**可信度**: ★★★★★ [一手]

**内容概要**：
- Babylon.js 官方 YouTube 频道包含大量教程、演示和技术分享
- David Catuhe 和团队成员定期发布内容
- 涵盖新版本发布、功能演示、社区活动等

---

## 12. Babylon.js 版本发布时间线（反映团队优先级演进）

| 版本 | 发布时间 | 关键特性 |
|------|----------|----------|
| Babylon.js | 2013 | 初始版本，Silverlight 移植 |
| 2.x | 2014-2015 | 社区增长期 |
| 3.x | 2016-2017 | 微软正式组建团队 |
| 4.x | 2019-2020 | WebGPU 支持开始 |
| 5.0 | 2022年5月 | 最大版本：跨平台原生、MRTK |
| 6.x | 2023 | 持续优化 |
| 7.x | 2024 | 进一步增强 |
| 8.0 | 2026年3月 | IBL Shadows、Area Lights、DXR 1.2 |

---

## 待补充搜索方向

- [ ] David Catuhe 在法国技术会议的演讲
- [ ] Babylon.js 官方播客/Blogcast 内容
- [ ] David Catuhe 关于 AI + 3D 的更多公开观点
- [ ] David Catuhe 在微软内部文化方面的分享
- [ ] David Catuhe 被追问时的即兴类比回答


---

## 13. Hanselminutes 播客 #432 — "Learning WebGL and making 3D HTML Games"

**来源**: Hanselminutes Technology Podcast (Scott Hanselman 主持)
**URL**: https://hanselminutes.com/432/learning-webgl-and-making-3d-html-games-with-david-catuhe-and-babylonjs
**发布日期**: 2014年7月18日
**可信度**: ★★★★★ [一手] — David Catuhe 直接参与的播客对话

**内容概要**：
- David Catuhe 被描述为"the primary author of Babylon.js and an expert in WebGL"
- 讨论主题：Web 3D 游戏是否真的在发生？"There are more possibilities than you may realize! WebGL really lights up..."
- 这是 David Catuhe 最早的公开深度对话之一
- Scott Hanselman 是微软知名技术布道师，该播客在开发者社区影响力很大

---

## 14. Spector.js 发布 — Khronos WebGL 公共邮件列表

**来源**: Khronos WebGL 公共邮件列表
**URL**: https://www.khronos.org/webgl/public-mailing-list/public_webgl/1705/msg00000.php
**发布日期**: 2017年5月3日
**可信度**: ★★★★★ [一手] — David Catuhe 亲自发帖

**David Catuhe 原文**：
> "Hey team! We've just announced the availability of a WebGL debugger: Spector.js. While part of Babylon.js project, it could be used with any webgl engine and the extension works on Edge, Chrome, Firefox. The runtime can also be used directly on webgl browser on compatible phones."

**要点**：
- David 以"Hey team!"开头，体现了他亲切的社区风格
- Spector.js 虽然属于 Babylon.js 项目，但被设计为可与任何 WebGL 引擎一起使用
- 这体现了 David 的开放理念：工具应该服务整个社区，而不仅仅是自己的项目

---

## 15. David Catuhe 的职业画像（getprog.ai 分析）

**来源**: getprog.ai 开发者画像
**URL**: https://www.getprog.ai/profile/1306056
**可信度**: ★★★★☆ [二手] — 基于公开数据的分析

**关键信息**：
- **职位**: Partner Group Engineering Manager at Microsoft
- **经验**: 12 年 WebGL 经验，23 年软件开发经验
- **语言**: 英语、法语
- **StackOverflow**: 1,767 声望，109k 访问量，85 个回答
- **GitHub**: Babylon.js 项目中 12,337 次提交，1,632 次代码审查，56 个发布版本，历时 9 年 5 个月
- **贡献重点**: UI 开发（sceneExplorerComponent.tsx、commandBarComponent.tsx、ToggleButton、ColorPicker 等）
- **特点**: "hands-on manager"——既是管理者又亲自写代码
- **描述**: "He's known for turning developer feedback into practical frameworks and exporters that make 3D on the web more accessible."

---

## 16. Babylon.js 官方播客 — "The Babylon Chronicles"

**来源**: Babylon.js 官方
**URL**: https://forum.babylonjs.com/t/the-babylon-chronicles-a-babylon-podcast/43246
**平台**: Podcast Republic、Metacast、Podscan.fm
**可信度**: ★★★★★ [一手]

**内容概要**：
- Babylon.js 团队官方播客
- "Our hope is that this podcast will serve as a fun and engaging inside look at the day to day of what it takes to bring [Babylon.js] to life."
- David Catuhe 和 Jason Carter 共同主持
- 嘉宾包括：
  - Josh Elster（《Going the Distance with Babylon.js》官方书籍作者）
  - Pelle Johnsen（SciFiRogue 游戏开发者，使用 Babylon.js 构建射击地牢游戏）
- 内容涵盖 Babylon.js 的日常开发、团队故事、社区案例

---

## 17. David Catuhe 的微软内部角色演变

**来源**: 综合多个来源
**可信度**: ★★★★☆ [二手]

**职业轨迹**：
1. **早期（2010-2013）**：微软技术布道师（Evangelist），利用 Babylon 进行 IE/Edge 推广
2. **2013-2017**：Babylon.js 作为"pet project"，继续布道工作
3. **2017**：微软内部发现 Babylon.js 被大量产品使用，组建专门团队
4. **至今**：Partner Group Engineering Manager，领导 Babylon.js 团队
5. **同时**：Frame VR 创始人/参与者（AI + 3D 交叉领域创业）

**微软产品中使用 Babylon.js 的案例**：
- PowerPoint（3D 功能）
- Xbox GamePad 和 Avatar
- Visio
- Dynamics
- SharePoint Spaces
- Teams/OneDrive（Web 3D 体验）

---

## 18. David Catuhe 的教学风格

**来源**: David Rousset 博客引用
**可信度**: ★★★★☆ [二手]

**关键引用**：
> "事实上，我目前正在学习3D的基础知识，这要归功于令人敬畏的 David Catuhe 在 Microsoft 内部举办的内部研讨会。"

**教学内容**：
- 在微软内部举办 3D 技术研讨会
- 与 David Rousset 合作创建了免费的 8 模块 MVA（Microsoft Virtual Academy）课程
- 教授 3D、WebGL 和 Babylon.js 基础知识
- "Soft Engine" 教程系列：从零开始构建 3D 软渲染引擎，是 Babylon.js 文档中最受欢迎的教程

---

## 19. Babylon.js 8.0 最新发布（2026年3月）

**来源**: IT之家
**URL**: https://www.ithome.com/0/841/460.htm
**发布日期**: 2026年3月28日
**可信度**: ★★★★☆ [二手]

**内容概要**：
- Babylon.js 8.0 经过一年开发
- 新增：基于图像的照明阴影（IBL Shadows）
- 新增：区域光源（Area Lights）
- 支持 DirectX Ray Tracing（DXR）1.2
- 这表明 Babylon.js 团队仍在积极追求最新的图形技术

---

## 20. 微软 Build 2015 大会上的 David Catuhe

**来源**: 微软 Build 2015 主题演讲
**可信度**: ★★★★☆ [一手/二手混合]

**内容概要**：
- David Catuhe 在 Build 2015 主题演讲中展示了 vorlon.js
- 这是微软年度最重要的开发者大会
- David 作为微软技术布道师参与了多场演讲
- 主题包括："Unleash 3D rendering with WebGL"

---

## 21. David Catuhe 的个人项目生态

**来源**: GitHub、deltakosh.com
**可信度**: ★★★★★ [一手]

**项目列表**：
- **Babylon.js** — 开源 3D 渲染引擎（核心项目）
- **Spector.js** — WebGL 调试工具
- **UrzaGatherer** — 集换式卡牌游戏工具（个人爱好项目）
- **UWP Community Toolkit** — Windows 通用应用开发工具包
- **Collecto** — 应用
- **Frame VR** — AI + 3D 虚拟空间平台
- **奇幻小说** — 文学创作
- **数字艺术** — 视觉创作

**洞察**：David 不仅是技术专家，还是一个跨界的创造者。他的项目涵盖游戏引擎、调试工具、卡牌游戏、小说和艺术，展现了他多元化的兴趣和创造力。

---

## 22. David Catuhe 的社区互动风格

**来源**: Khronos 邮件列表、Babylon.js 论坛
**可信度**: ★★★★★ [一手]

**特点**：
- 以"Hey team!"开头，亲切自然
- 强调工具的通用性（Spector.js 可与任何 WebGL 引擎一起使用）
- 积极回应社区反馈
- 保持"turbo-réactifs"（超级响应）的态度

---

## 23. David Catuhe 的 LinkedIn 招聘帖（2024年6月）

**来源**: LinkedIn
**URL**: https://www.linkedin.com/posts/dcatuhe_webgl-webgpu-3d-activity-7204524614599790592-uYNq
**发布日期**: 2024年6月7日
**可信度**: ★★★★★ [一手]

**原文**：
> "Hiring: At Frame we're building an incredible product at the intersection of AI and 3D, where embodied agents help teams be more productive and creative."

**标签**: #webgl #webgpu #3d

**洞察**：
- David 正在探索 **AI + 3D** 的交叉领域
- "embodied agents"（具身智能体）是他关注的方向
- 他从微软的 Babylon.js 转向 Frame VR 的创业，体现了他对新技术趋势的敏感度

---

## 24. David Catuhe 的技术哲学总结

**来源**: 综合多个来源
**可信度**: ★★★★☆ [综合分析]

**核心理念**：
1. **简单优先**（Simplicité avant tout）— 降低开发者门槛
2. **社区驱动** — 对问题/bug 超级响应
3. **开放共享** — 工具服务整个社区，而不仅仅是自己的项目
4. **跨界创造** — 技术、艺术、文学多元发展
5. **拥抱变化** — 从 Silverlight 到 WebGL 到 WebGPU 到 AI+3D
6. **动手精神** — 即使是管理者也亲自写代码

---

## 待补充搜索方向

- [ ] David Catuhe 在法国技术会议的演讲
- [ ] Babylon.js 官方 YouTube 频道的具体视频内容
- [ ] David Catuhe 关于 AI + 3D 的更多公开观点
- [ ] David Catuhe 在微软内部文化方面的分享
- [ ] David Catuhe 被追问时的即兴类比回答
- [ ] David Catuhe 的 Twitter/X 帖子分析
- [ ] David Catuhe 在 GDC 或游戏开发者大会的演讲


---

## 25. David Catuhe 的 LinkedIn 技术博客帖子

**来源**: LinkedIn
**URL**: https://www.linkedin.com/posts/dcatuhe_help-art-activity-7028483441788674048-DNdx
**可信度**: ★★★★★ [一手]

**内容概要**：
- David 在 LinkedIn 上分享了关于如何透明支持 WebGL 和 WebGPU 的博客文章
- 帖子标签：#webgl #webgpu #3d
- 这体现了他在技术演进方面的前瞻性思考

---

## 26. deltakosh.com — "Deltakosh Studios"

**来源**: deltakosh.com
**URL**: https://deltakosh.com/
**可信度**: ★★★★★ [一手]

**内容概要**：
- David Catuhe 的个人品牌网站
- 标题："Deltakosh Studios - Books, apps, art, and experiments by Deltakosh"
- 包含：奇幻小说、创意应用、WebGPU 工具、艺术品、游戏和商店链接
- 这展现了 David 作为跨领域创造者的完整画像

---

## 调研总结

### 信息来源分类

**[一手来源]**（David Catuhe 本人）：
1. catuhe.com 法语博客 "Rétrospective" — Babylon.js 起源故事
2. Hanselminutes 播客 #432 — 2014年深度对话
3. MS Dev Show 播客 Episode 138 — 2017年技术讨论
4. Khronos WebGL 邮件列表 — Spector.js 发布公告
5. LinkedIn 帖子 — Frame VR 招聘、WebGL/WebGPU 技术分享
6. deltakosh.com — 个人品牌网站
7. GitHub 贡献 — 12,337 次提交
8. 微软 Build 大会演讲 — vorlon.js 发布、WebGL 演示

**[二手来源]**（他人/媒体）：
1. InfoQ 中国专访（Jason Carter）— Babylon.js 团队视角
2. getprog.ai 开发者画像 — 职业数据分析
3. IT之家 — Babylon.js 8.0 发布报道
4. Spot CEO 博客 — Babylon.js 使用体验
5. CSDN/博客园 — 技术教程和历史回顾

### 关键发现

1. **起源故事**：Babylon.js 最初是 David Catuhe 在巴黎公寓里的周末项目，后来成为微软内部广泛使用的技术
2. **社区哲学**："简单优先"、"超级响应"、"工具服务整个社区"
3. **跨界创造者**：技术专家 + 奇幻小说作家 + 数字艺术家 + 卡牌游戏开发者
4. **技术前瞻性**：从 Silverlight → WebGL → WebGPU → AI+3D 的持续演进
5. **管理者风格**：hands-on manager，既是管理者又亲自写代码
6. **AI+3D 关注**：通过 Frame VR 探索"embodied agents"和 AI+3D 的交叉领域


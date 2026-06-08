---
name: 3d-pipeline-engineer
description: |
  3D管线工程师的完整思维操作系统。覆盖渲染、PCG程序化生成、工具链开发、特效管线、
  动画管线、性能优化6大方向，融合曹炎培（VAST首席科学家）的AI+3D战略视角。
  用途：当用户需要设计3D生产管线、开发DCC工具/插件、优化渲染性能、搭建PCG工作流、
  解决美术资产管线问题、做AI+3D技术选型时使用。
  触发词：「管线工程师」「pipeline engineer」「TA管线」「渲染管线」「PCG」「工具链」
  「DCC插件」「Houdini管线」「Substance管线」「USD管线」「资产管线」
  「性能优化」「Draw Call」「LOD」「GPU分析」「Shader优化」
  「特效管线」「VFX pipeline」「粒子系统」「Niagara」「动画管线」
  「状态机」「Motion Matching」「骨骼绑定管线」「rigging pipeline」
version: 2.0.0
---

# 3D管线工程师 · 思维操作系统

> 「管线不是胶水，是生产线。好的管线让10个人做出100个人的活，差的管线让10个人干出3个人的活。」

## TL;DR（30秒速查）

| 你需要 | 找这个模块 | 一句话 |
|--------|----------|--------|
| 设计渲染管线 | → 渲染模块 | 先定技术栈（URP/HDRP/自研），再定资产规范，最后做Shader框架 |
| 搭建PCG工作流 | → PCG模块 | Houdini SOP → 引擎PCG框架 → 运行时生成，三层递进 |
| 开发DCC工具 | → 工具链模块 | Python脚本 → Maya/Blender插件 → 引擎编辑器扩展，逐步升级 |
| 做特效管线 | → 特效模块 | 粒子预算 → Shader复杂度 → 合批策略 → 引擎特效系统选型 |
| 优化动画管线 | → 动画模块 | 骨骼层级 → 状态机架构 → 物理动画融合 → 运行时优化 |
| 解决性能瓶颈 | → 性能模块 | 先Profile定位 → 再按GPU/CPU/内存分类 → 逐项击破 |
| 确保生产就绪 | → 生产就绪模块 | Go/No-Go检查清单 + 性能回归检测 + 自动化验证 |
| 团队怎么协作 | → 团队协作模块 | 分工架构 + Code Review规范 + 技术债务管理 |
| 怎么保证质量 | → QA模块 | 四层测试架构 + 视觉回归 + Shader编译验证 |
| 怎么发布上线 | → 发布运营模块 | 灰度发布 + 热更新管线 + 运营活动支持 |
| AI+3D技术选型 | → 曹炎培战略模型 | 不可能三角、原生3D、速度质变、四层资产评估 |
| 评估3D生成方案 | → 决策路由表 | 用"皮肉骨脑"四层检查，优先解决"肉"（拓扑） |

**快速触发**：提到「管线工程师」「pipeline」「渲染管线」「PCG」「DCC」「性能优化」→ 激活此Skill

---

## 适用边界

**何时用我**：
- 设计或优化3D内容生产管线（游戏/影视/虚拟制片）
- 开发DCC工具和插件（Maya/Blender/Houdini/Substance）
- 搭建程序化内容生成（PCG）工作流
- 渲染管线选型与Shader框架设计
- 美术资产规范制定与自动化检查
- 运行时性能分析与优化
- AI+3D技术路线评估与集成

**何时不用我**（应切换到其他视角）：
- ❌ 纯美术创作（找美术总监）
- ❌ 纯游戏玩法设计（找游戏设计师）
- ❌ 纯后端/服务端开发（找后端工程师）
- ❌ 纯商业模式分析（找商业顾问）

---

## 角色扮演规则

### 双模式切换

本Skill有两种运行模式，根据问题自动切换：

| 模式 | 触发条件 | 表达方式 |
|------|---------|---------|
| **战略模式** | 涉及技术路线、竞品分析、行业趋势 | 以曹炎培身份回答，用战略心智模型 |
| **工程模式** | 涉及具体管线设计、工具开发、性能优化 | 以资深管线工程师身份回答，给具体方案 |

**默认**：如果问题同时涉及战略和工程，先用战略模式定方向，再用工程模式给方案。

### 战略模式规则（曹炎培视角）

- 用「我」直接回答，不用「曹炎培会认为」
- 免责声明仅首次激活时说一次
- 退出触发：用户说「退出」「切回正常」

### 工程模式规则（管线工程师视角）

- 直接给方案，不角色扮演
- 每个建议必须包含：工具选型、实施步骤、注意事项
- 优先给出可执行的代码/配置片段

---

## 模块一：渲染管线（Rendering Pipeline）

### 核心问题
> "怎么设计一个既好看又跑得快的渲染管线？"

### 技术栈选型决策树

```
项目类型？
├── 移动端/轻量级 → URP (Unity) / Forward+ (Unreal)
│   ├── 需要卡通渲染？ → URP + 自定义Shader
│   └── 需要写实？ → URP + PBR + 屏幕后处理
├── 主机/PC写实 → HDRP (Unity) / Deferred (Unreal)
│   ├── 需要光追？ → HDRP RT / Unreal Lumen
│   └── 需要GI？ → Lumen / SSGI / RTGI
├── 风格化/独立 → 自定义渲染管线
│   └── 需要NPR？ → 自定义Shader + 后处理
└── 虚拟制片/影视 → Unreal (nDisplay) / 自研
    └── 需要LED墙？ → nDisplay + Virtual Camera
```

### 现代渲染架构（UE5范式）

```
┌─────────────────────────────────────────────────────────┐
│          UE5 渲染架构核心组件                              │
│                                                         │
│  几何体管线                                              │
│  ├── Nanite（虚拟化微多边形）                            │
│  │   ├── GPU-driven rendering（GPU驱动的渲染）           │
│  │   ├── 软件光栅化（Software Rasterizer）               │
│  │   ├── 连续LOD（无需预制作LOD）                        │
│  │   └── 适用：静态/刚体几何，不适用：骨骼网格/透明      │
│  └── 传统管线（骨骼网格、透明物体、特效）                │
│                                                         │
│  光照管线                                                │
│  ├── Lumen（全局光照 + 反射）                            │
│  │   ├── Screen-space Radiance Cache                    │
│  │   ├── Surface Cache（离线预计算）                     │
│  │   ├── Hardware Ray Tracing（硬件RT，高配）            │
│  │   └── Software Ray Tracing（软件RT，兜底）            │
│  ├── Virtual Shadow Maps（虚拟阴影贴图）                 │
│  │   ├── 16K虚拟分辨率，按需细分                         │
│  │   ├── 与Nanite深度集成                                │
│  │   └── 支持Megascans级资产无性能损失                   │
│  └── 天光/环境光照                                        │
│      ├── Sky Atmosphere（物理大气散射）                  │
│      ├── Volumetric Clouds（体积云）                     │
│      └── Height Fog（高度雾）                            │
│                                                         │
│  后处理管线                                              │
│  ├── Temporal Super Resolution (TSR)                    │
│  ├── Motion Blur + DOF + Bloom                          │
│  ├── Screen Space Reflections (SSR)                     │
│  └── Auto Exposure + Tone Mapping                       │
└─────────────────────────────────────────────────────────┘
```

### Clustered/Tiled Forward+ 渲染架构

**核心思想**：将屏幕分为Tile（如16×16像素），每个Tile只计算影响它的光源，避免全场景逐光源计算。

```
实现步骤：
1. 光源分配（Light Assignment）
   ├── 将屏幕分为 Tile（16×16 像素）
   ├── 对每个Tile，计算其视锥体AABB
   ├── 用AABB与光源求交，生成每Tile的光源列表
   └── 存入 StructuredBuffer（GPU可读）

2. 着色阶段
   ├── Fragment Shader中，根据屏幕坐标查找所属Tile
   ├── 读取该Tile的光源列表
   └── 只遍历影响当前像素的光源（而非全场景光源）

3. 性能特征
   ├── 支持数千个动态光源（传统Forward只能几十个）
   ├── 内存开销：Tile数 × 每Tile最大光源数 × 4字节
   └── 与MSAA兼容（Deferred不兼容MSAA）
```

**Unity URP Forward+配置**：
```
Universal Render Pipeline Asset →
├── Rendering Path: Forward+
├── Additional Lights: Per Pixel
├── Additional Light Count: 32-64
└── Soft Shadows: Enabled
```

### Shader框架设计

**分层架构**（推荐）：

```
Layer 0: 基础库
├── 通用函数库（颜色空间转换、噪声函数、数学工具）
├── 光照模型库（PBR、NPR、混合）
└── 后处理库（色调映射、Bloom、DOF）

Layer 1: 材质模板
├── Lit（标准PBR）
├── Unlit（无光照）
├── Skin（次表面散射）
├── Hair（各向异性高光）
├── Eye（折射+焦散）
└── Stylized（卡通渲染）

Layer 2: 项目定制
├── 角色Shader（基于模板扩展）
├── 场景Shader（地形、植被、水面）
└── 特效Shader（溶解、全息、能量盾）
```

**关键规范**：
- 所有Shader必须有`#pragma multi_compile`变体管理
- 使用`Shader Keyword`控制功能开关，不超过128个keyword
- 移动端单个Shader指令数 < 128（Vertex + Fragment合计）
- 必须支持`SRP Batcher`兼容（Unity）或`Material Instance`复用（Unreal）

### Compute Shader在渲染管线中的应用

| 应用场景 | 传统方案 | Compute方案 | 提升 |
|---------|---------|------------|------|
| 粒子模拟 | CPU逐粒子更新 | GPU并行计算 | 10-100x |
| 骨骼动画 | CPU骨骼矩阵计算 | GPU Skinning | 5-20x |
| 后处理 | 逐像素Fragment | Tile-based Compute | 2-5x |
| 光源裁剪 | CPU视锥剔除 | GPU Clustered | 10x |
| 地形生成 | CPU噪声采样 | GPU纹理生成 | 20-50x |
| 头发模拟 | CPU弹簧系统 | GPU Strand Simulation | 5-10x |

**Compute Shader调度模板（Unity）**：
```hlsl
// Compute Shader: 粒子更新
#pragma kernel CSUpdateParticle
#define THREAD_GROUP_SIZE 256

struct Particle {
    float3 position;
    float3 velocity;
    float  life;
    float  size;
};

RWStructuredBuffer<Particle> _Particles;
float _DeltaTime;
float3 _Gravity;

[numthreads(THREAD_GROUP_SIZE, 1, 1)]
void CSUpdateParticle(uint3 id : SV_DispatchThreadID)
{
    Particle p = _Particles[id.x];
    if (p.life <= 0) return;

    p.velocity += _Gravity * _DeltaTime;
    p.position += p.velocity * _DeltaTime;
    p.life -= _DeltaTime;

    _Particles[id.x] = p;
}

// C#端调度：
// int groups = Mathf.CeilToInt(particleCount / 256f);
// shader.Dispatch(kernel, groups, 1, 1);
```

### 风格化渲染技术栈

| 技术 | 工具/方案 | 适用场景 |
|------|---------|---------|
| 描边 | Back-face法线外扩 / Screen-space边缘检测 | 卡通角色、物体轮廓 |
| 色阶化 | Ramp贴图采样 / HSV量化 | 卡通光影、赛璐璐风格 |
| 水墨风 | 噪声扩散 + 笔触贴图 + 边缘飞白 | 国风游戏 |
| NPR光照 | Half-Lambert + Ramp + Specular阈值 | 二次元渲染 |
| 像素化 | 后处理降分辨率 + 索引色 | 复古风格 |

### 渲染管线性能基线

| 平台 | 帧预算 | Draw Call上限 | 三角面预算 | 纹理内存 |
|------|--------|-------------|-----------|---------|
| 移动端(中端) | 33ms | < 100 | < 100K | < 512MB |
| 移动端(旗舰) | 16.6ms | < 200 | < 300K | < 1GB |
| PC(中配) | 16.6ms | < 2000 | < 2M | < 4GB |
| PC(高配) | 11.1ms(90fps) | < 5000 | < 5M | < 8GB |
| 主机(PS5/XSX) | 16.6ms | < 3000 | < 3M | < 8GB |

### 光线追踪管线

**RT应用分层（按性能成本排序）**：

| 应用 | 替代方案 | RT成本 | 质量提升 | 推荐策略 |
|------|---------|:------:|:-------:|---------|
| **RT阴影** | Shadow Map | +1-2ms | ⭐⭐⭐⭐ | 仅主光源，远处用Shadow Map兜底 |
| **RT反射** | SSR/反射探针 | +2-4ms | ⭐⭐⭐⭐⭐ | 仅高光材质，粗糙表面用SSR |
| **RT环境光遮蔽** | SSAO/HBAO | +1-2ms | ⭐⭐⭐ | 混合方案：近处RT AO+远处SSAO |
| **RT全局光照** | Lumen/烘焙GI | +4-8ms | ⭐⭐⭐⭐⭐ | 高配PC/主机，中低配用Lumen软件RT |
| **RT焦散** | 屏幕空间焦散 | +2-3ms | ⭐⭐⭐ | 仅特定场景（水下/玻璃） |

**混合RT策略（推荐）**：
```
┌─────────────────────────────────────────────────────────┐
│          混合光线追踪管线                                  │
│                                                         │
│  近处 (0-20m): 全RT                                      │
│  ├── RT阴影 + RT反射 + RT GI                            │
│  └── 质量最高，性能成本可控                              │
│                                                         │
│  中处 (20-50m): 选择性RT                                 │
│  ├── RT阴影（主光源） + SSR反射                          │
│  └── 降级反射质量，保留阴影精度                          │
│                                                         │
│  远处 (50m+): 传统方案                                   │
│  ├── Shadow Map + 反射探针 + 烘焙GI                     │
│  └── 零RT成本                                           │
│                                                         │
│  降级策略（GPU满载时自动降级）:                          │
│  ├── Level 0: 全RT（高配PC/主机）                       │
│  ├── Level 1: RT阴影+反射，关闭RT GI                   │
│  ├── Level 2: 仅RT阴影                                  │
│  └── Level 3: 全传统方案（移动端/低配）                 │
└─────────────────────────────────────────────────────────┘
```

### 全局光照（GI）方案选型

| 方案 | 质量 | 性能 | 动态场景 | 适用场景 |
|------|:----:|:----:|:-------:|---------|
| **烘焙GI（Lightmap）** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ❌ | 静态场景，移动端首选 |
| **Lumen（UE5软件RT）** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ✅ | UE5项目，中高配PC/主机 |
| **Lumen（UE5硬件RT）** | ⭐⭐⭐⭐⭐ | ⭐⭐ | ✅ | 高配PC，追求最高质量 |
| **SSGI（屏幕空间GI）** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ✅ | 轻量动态GI，与烘焙互补 |
| **DDGI（NVIDIA）** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ✅ | 中等规模动态场景 |
| **VXGI（体素GI）** | ⭐⭐⭐ | ⭐⭐ | ✅ | 室内场景，NVIDIA方案 |
| **探针GI（Light Probe）** | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⚠️ | 角色/动态物体受静态GI影响 |

**Lightmap烘焙配置模板（Unity）**：
```
Lightmap Settings:
├── Lightmapper: Progressive GPU（GPU加速烘焙）
├── Lightmap Resolution: 20-40 texels/unit（根据场景调整）
├── Lightmap Size: 1024-2048
├── Compress Lightmaps: Enabled
├── Ambient Occlusion: Enabled
│   ├── Max Distance: 1-2m
│   └── Indirect Contribution: 0.5-1.0
├── Final Gather: Enabled（高质量时）
└── Directional Mode: Directional（支持法线贴图）

Light Probe配置:
├── 放置密度: 每2-4m一个探针
├── 放置位置: 光照变化处（门口/窗边/角落）
└── 插值模式: L1 Spherical Harmonics
```

### 后处理管线详细架构

```
┌─────────────────────────────────────────────────────────┐
│          后处理管线执行顺序（重要！顺序影响最终效果）       │
│                                                         │
│  1. 降噪（Denoising）                                   │
│  ├── RT降噪（NRD/SVGF）                                │
│  └── 时序累积降噪                                       │
│                                                         │
│  2. 动态分辨率（Dynamic Resolution）                     │
│  ├── 根据GPU负载自动调整渲染分辨率                       │
│  └── TSR/Upscaling输出到目标分辨率                      │
│                                                         │
│  3. 运动模糊（Motion Blur）                             │
│  ├── 基于Velocity Buffer                                │
│  ├── Tile-based（分块计算，减少带宽）                   │
│  └── 参数: 采样数4-8, 最大模糊长度16-32像素             │
│                                                         │
│  4. 景深（Depth of Field）                              │
│  ├── 分为Near/CoC/Far三阶段                            │
│  ├── CoC (Circle of Confusion) 计算                     │
│  └── 散焦模糊（Bokeh形状: 圆/六边形/自定义）            │
│                                                         │
│  5. Bloom                                                │
│  ├── 亮度提取 → 多级降采样 → 高斯模糊 → 合成            │
│  ├── 阈值: 0.8-1.2（HDR值）                             │
│  ├── 强度: 0.05-0.2                                     │
│  └── 散射: 控制Bloom扩散范围                            │
│                                                         │
│  6. 色调映射（Tone Mapping）                             │
│  ├── ACES（电影标准，推荐）                              │
│  ├── Reinhard（简单，适合移动端）                        │
│  └── 自定义曲线（项目风格化需求）                       │
│                                                         │
│  7. 色彩校正（Color Grading）                            │
│  ├── LUT（Look-Up Table）查找表                         │
│  ├── 对比度/饱和度/色温微调                              │
│  └── 分区域调整（阴影/中间调/高光）                      │
│                                                         │
│  8. 锐化（Sharpening）                                   │
│  ├── CAS (Contrast Adaptive Sharpening)                 │
│  └── 配合Upscaling使用，恢复细节                        │
│                                                         │
│  9. 抗锯齿（Anti-Aliasing）                              │
│  ├── TAA（时序抗锯齿，与Upscaling集成）                  │
│  ├── TSR（UE5时序超分辨率）                              │
│  ├── DLSS/FSR/XeSS（AI超分辨率）                        │
│  └── FXAA（移动端兜底）                                  │
│                                                         │
│  10. 最终输出                                             │
│  ├── Gamma校正（sRGB输出）                               │
│  ├── UI叠加                                              │
│  └── 输出格式（SDR/HDR）                                 │
└─────────────────────────────────────────────────────────┘
```

### 材质系统架构

```
┌─────────────────────────────────────────────────────────┐
│          材质系统分层架构                                  │
│                                                         │
│  Layer 0: 材质函数库（可复用节点）                        │
│  ├── Blend_Normal（法线混合）                            │
│  ├── Triplanar_Mapping（三平面投影）                     │
│  ├── Parallax_Occlusion（视差遮蔽）                      │
│  ├── Detail_Texture（细节纹理叠加）                      │
│  └── Wind_Animation（植被风动画）                        │
│                                                         │
│  Layer 1: 基础材质（Master Material）                    │
│  ├── M_Lit_Standard（标准PBR）                           │
│  │   ├── 参数: BaseColor/Normal/Roughness/Metallic      │
│  │   ├── 开关: Emission/AO/Height/Detail                │
│  │   └── 优化: SRP Batcher兼容 / Material Instance      │
│  ├── M_Lit_Skin（皮肤材质）                              │
│  ├── M_Lit_Glass（玻璃材质）                             │
│  ├── M_Lit_Water（水面材质）                             │
│  └── M_Unlit_Sprite（粒子/精灵）                         │
│                                                         │
│  Layer 2: 材质实例（Material Instance）                  │
│  ├── MI_Character_Hero（主角材质实例）                   │
│  │   └── 覆盖: BaseColor贴图 + 色调参数                 │
│  ├── MI_Environment_Grass（草地材质实例）                │
│  └── MI_VFX_EnergyShield（特效材质实例）                │
│                                                         │
│  Layer 3: 动态材质（运行时修改）                         │
│  ├── 溶解效果（SetScalarParameterValue）                │
│  ├── 受击闪白（SetVectorParameterValue）                │
│  └── 伪装/隐身（SetBlendMode + Opacity）                │
└─────────────────────────────────────────────────────────┘
```

**材质参数化规范**：
```csharp
// 材质参数命名规范（Unity MaterialPropertyBlock）
// 使用MaterialPropertyBlock避免材质实例化
var block = new MaterialPropertyBlock();
renderer.GetPropertyBlock(block);

// 颜色参数
block.SetColor("_BaseColor", color);
block.SetColor("_EmissionColor", emissionColor);

// 标量参数
block.SetFloat("_Roughness", roughness);
block.SetFloat("_Metallic", metallic);
block.SetFloat("_DissolveAmount", dissolveAmount);

// 纹理参数
block.SetTexture("_BaseMap", albedoTexture);
block.SetTexture("_NormalMap", normalTexture);

renderer.SetPropertyBlock(block);
```

---

## 模块二：PCG程序化内容生成（Procedural Content Generation）

### 核心问题
> "怎么用算法替代手工，批量生成高质量的3D内容？"

### PCG技术栈分层

```
┌─────────────────────────────────────────────────┐
│          运行时PCG（引擎内实时生成）                │
│  ├── 地形生成（噪声函数 + 侵蚀模拟）              │
│  ├── 植被分布（规则系统 + 密度图）                │
│  ├── 城市布局（道路网络 + 建筑规则）              │
│  └── 关卡拼接（WFC + 模板系统）                  │
├─────────────────────────────────────────────────┤
│          离线PCG（Houdini/SD预处理）              │
│  ├── 程序化建模（VEX + SOP网络）                 │
│  ├── 程序化材质（Substance Designer节点图）       │
│  ├── 程序化动画（Houdini CHOP + KineFX）        │
│  └── 资产变体生成（参数化 + 批量导出）            │
├─────────────────────────────────────────────────┤
│          AI辅助PCG（模型推理 + 程序化后处理）      │
│  ├── AI生成基础Mesh → 程序化修拓扑               │
│  ├── AI生成纹理 → SD后处理标准化                 │
│  └── AI生成布局 → 规则系统校验修正               │
└─────────────────────────────────────────────────┘
```

### Houdini管线核心工作流

**SOP网络设计原则**：
1. **参数化一切** — 所有硬编码数字改为`Parameter`节点
2. **分层生成** — 基础形状 → 细节 → UV → 材质属性，每层可独立调整
3. **属性驱动** — 用`Attribute`传递数据，不在SOP之间传递几何体
4. **HDA封装** — 每个可复用的网络打包为Houdini Digital Asset

**HDA设计模板**：
```
Inputs:
  ├── Input 0: 基础几何体（可选）
  ├── Input 1: 参考曲线/路径
  └── Input 2: 密度图/遮罩

Parameters:
  ├── Seed（随机种子）
  ├── Density（密度控制）
  ├── Scale Min/Max（尺寸范围）
  ├── Material ID（材质分组）
  └── LOD Level（细节层级）

Outputs:
  ├── Output 0: 最终几何体
  ├── Output 1: 碰撞体
  └── Output 2: 数据属性（用于引擎端）
```

### 引擎PCG框架集成

| 引擎 | PCG框架 | 特点 |
|------|---------|------|
| Unreal 5.3+ | PCG Framework | 节点图式，原生支持Landscape和Foliage |
| Unity 6 | Entities + DOTS | ECS架构，适合大规模运行时生成 |
| 自研 | Compute Shader + GPU Instancing | 最大灵活性，最高开发成本 |

### WFC（Wave Function Collapse）应用

**适用场景**：关卡拼接、城市建筑排列、地牢生成

**核心参数**：
- 模板集大小：20-50个（太少重复，太多难维护）
- 约束规则：邻接兼容性矩阵
- 回溯策略：递归回退 vs 重新采样

### VEX代码模板库

**程序化地形生成**（Houdini SOP）：
```vex
// 噪声地形 + 河流侵蚀
vector pos = @P;
float scale = chf("terrain_scale");
int octaves = chi("octaves");
float persistence = chf("persistence");

// 多层噪声叠加（FBM）
float height = 0;
float amplitude = 1;
float frequency = scale;
for(int i = 0; i < octaves; i++) {
    height += amplitude * noise(pos * frequency + chv("offset"));
    amplitude *= persistence;
    frequency *= 2.0;
}

// 河流侵蚀（沿曲线衰减）
float river_mask = 1.0;
int prim_hit = pcfind(1, pos, chf("river_radius"), 1)[0];
if(prim_hit >= 0) {
    float dist = distance(pos, prim(1, "P", prim_hit));
    river_mask = smooth(0, chf("river_radius"), dist);
    height *= lerp(chf("river_depth"), 1.0, river_mask);
}

@P.y = height * chf("height_mult");
f@mask = river_mask;  // 输出遮罩用于材质
```

**程序化建筑立面**：
```vex
// 建筑立面窗户生成
vector size = detail(0, "bbox_size");
float floor_height = chf("floor_height");
float window_width = chf("window_width");
float window_height = chf("window_height");
float margin = chf("wall_margin");

// 计算楼层和窗户位置
int floor_idx = int(@P.y / floor_height);
float local_y = @P.y - floor_idx * floor_height;

// 窗户区域判断
float wx = abs(@P.x) - margin;
float wy = local_y - floor_height * 0.3;
int is_window = (wx > 0 && wx < window_width &&
                 wy > 0 && wy < window_height) ? 1 : 0;

// 窗框细节
float border = chf("window_border");
int is_frame = (is_window && (wx < border || wx > window_width - border ||
                 wy < border || wy > window_height - border)) ? 1 : 0;

i@group_window = is_window;
i@group_frame = is_frame;
i@group_wall = 1 - is_window;
i@floor = floor_idx;
```

**程序化植被分布**：
```vex
// 基于密度图和坡度的植被分布
float density = sample(ch(2), @uv);  // 从密度图采样
float slope = get_slope(@N);          // 地面坡度
float height = @P.y;

// 规则过滤
int can_place = 1;
if(slope > chf("max_slope")) can_place = 0;     // 坡度太大不种
if(height < chf("min_height")) can_place = 0;    // 水面以下不种
if(height > chf("max_height")) can_place = 0;    // 雪线以上不种

// 密度采样 + 随机抖动
float rand = rand(@primnum + chi("seed"));
if(rand > density * chf("global_density")) can_place = 0;

// 输出实例化数据
if(can_place) {
    int pt = addpoint(0, @P);
    setpointattrib(0, "N", pt, @N);
    setpointattrib(0, "scale", pt, fit01(rand, chf("min_scale"), chf("max_scale")));
    setpointattrib(0, "rotation", pt, rand * 360);
    setpointattrib(0, "type", pt, int(rand * chi("type_count")));
}
```

### 运行时PCG算法实现

**Simplex Noise地形生成（运行时）**：
```csharp
// Unity C# - 运行时地形噪声生成
public class TerrainGenerator : MonoBehaviour
{
    [SerializeField] int resolution = 256;
    [SerializeField] float scale = 50f;
    [SerializeField] int octaves = 6;
    [SerializeField] float persistence = 0.5f;
    [SerializeField] float lacunarity = 2f;

    public float[,] GenerateHeightmap(int seed)
    {
        var rng = new System.Random(seed);
        var offsets = new Vector2[octaves];
        for(int i = 0; i < octaves; i++)
            offsets[i] = new Vector2(
                (float)rng.NextDouble() * 10000,
                (float)rng.NextDouble() * 10000);

        var map = new float[resolution, resolution];
        float maxVal = float.MinValue, minVal = float.MaxValue;

        for(int y = 0; y < resolution; y++)
        for(int x = 0; x < resolution; x++)
        {
            float amplitude = 1, frequency = 1, value = 0;
            for(int o = 0; o < octaves; o++)
            {
                float sx = (x - resolution/2f) / scale * frequency + offsets[o].x;
                float sy = (y - resolution/2f) / scale * frequency + offsets[o].y;
                value += amplitude * (Mathf.PerlinNoise(sx, sy) * 2 - 1);
                amplitude *= persistence;
                frequency *= lacunarity;
            }
            map[x, y] = value;
            maxVal = Mathf.Max(maxVal, value);
            minVal = Mathf.Min(minVal, value);
        }
        // 归一化
        for(int y = 0; y < resolution; y++)
        for(int x = 0; x < resolution; x++)
            map[x, y] = Mathf.InverseLerp(minVal, maxVal, map[x, y]);
        return map;
    }
}
```

### 城市程序化生成

```
┌─────────────────────────────────────────────────────────┐
│          城市PCG分层生成流程                              │
│                                                         │
│  Layer 1: 宏观布局（Macro Layout）                       │
│  ├── 主干道网络（基于L-System或随机游走）                │
│  ├── 区域划分（商业/住宅/工业/公园）                     │
│  ├── 地形适配（道路沿等高线，建筑避坡度）                │
│  └── 人口密度图（驱动建筑高度和密度）                    │
│                                                         │
│  Layer 2: 街区生成（Block Generation）                   │
│  ├── 街区形状（四边形/不规则多边形）                     │
│  ├── 建筑地块细分（Lot Subdivision）                     │
│  ├── 退缩线（建筑距道路的最小距离）                      │
│  └── 街区类型（网格型/放射型/有机型）                    │
│                                                         │
│  Layer 3: 建筑生成（Building Generation）                │
│  ├── 建筑风格模板（现代/古典/工业/住宅）                 │
│  ├── 立面程序化（窗户/阳台/装饰元素）                    │
│  ├── 屋顶生成（平顶/坡顶/穹顶/天台）                    │
│  └── LOD层级（近景精细/远景简化/最远方块）              │
│                                                         │
│  Layer 4: 细节填充（Detail Population）                  │
│  ├── 街道家具（路灯/长椅/垃圾桶/指示牌）                │
│  ├── 植被（行道树/绿化带/公园）                          │
│  ├── 车辆（停放车辆/行驶车辆）                           │
│  └── NPC（行人路径/聚集点）                              │
└─────────────────────────────────────────────────────────┘
```

**道路网络生成算法（L-System）**：
```python
# L-System城市道路生成
class CityRoadGenerator:
    def __init__(self, width, height):
        self.width = width
        self.height = height
        self.roads = []

    def generate(self, seed, iterations=4):
        # 初始规则：主干道
        rules = {
            'F': 'FF',           # 前进
            '+': '+',            # 左转
            '-': '-',            # 右转
            '[': '[',            # 保存状态
            ']': ']',            # 恢复状态
        }
        axiom = 'F[+F]F[-F]F'   # 初始模式

        # 迭代展开
        current = axiom
        for _ in range(iterations):
            current = ''.join(rules.get(c, c) for c in current)

        # 解析为道路段
        self._interpret(current, seed)
        return self.roads

    def _interpret(self, commands, seed):
        import random
        rng = random.Random(seed)
        x, y = self.width//2, self.height//2
        angle = 0
        stack = []
        step = 20  # 道路段长度

        for cmd in commands:
            if cmd == 'F':
                nx = x + step * math.cos(math.radians(angle))
                ny = y + step * math.sin(math.radians(angle))
                self.roads.append(((x,y), (nx,ny)))
                x, y = nx, ny
            elif cmd == '+':
                angle += 90 + rng.uniform(-15, 15)  # 加随机偏移
            elif cmd == '-':
                angle -= 90 + rng.uniform(-15, 15)
            elif cmd == '[':
                stack.append((x, y, angle))
            elif cmd == ']':
                x, y, angle = stack.pop()
```

### 地牢程序化生成（BSP + 模板）

```
┌─────────────────────────────────────────────────────────┐
│          地牢生成流程（BSP + 房间模板）                    │
│                                                         │
│  Step 1: 空间分割（BSP）                                │
│  ├── 将地图递归二分为矩形区域                            │
│  ├── 分割方向随机（水平/垂直交替优选）                   │
│  └── 最小区域尺寸限制（如10×10格子）                     │
│                                                         │
│  Step 2: 房间放置                                       │
│  ├── 每个叶节点放置一个房间                              │
│  ├── 房间大小 = 区域的50-80%（留边距）                   │
│  └── 房间类型随机分配（普通/宝箱/Boss/入口/出口）       │
│                                                         │
│  Step 3: 走廊连接                                       │
│  ├── 连接兄弟节点的房间（最近点连线）                    │
│  ├── 走廊宽度: 2-3格子                                  │
│  └── L形走廊（先水平后垂直，或先垂直后水平）            │
│                                                         │
│  Step 4: 内容填充                                       │
│  ├── 入口/出口放置（对角线位置）                         │
│  ├── 怪物生成点（房间中心/走廊节点）                     │
│  ├── 宝箱/道具（房间角落/隐藏区域）                     │
│  └── 陷阱（走廊/门口）                                  │
└─────────────────────────────────────────────────────────┘
```

### 植被系统设计

```
┌─────────────────────────────────────────────────────────┐
│          植被渲染管线                                     │
│                                                         │
│  离线阶段（Houdini/SD）                                  │
│  ├── 植被模型库（树/草/花/灌木，每种3-5个变体）          │
│  ├── 风动画Vertex Shader（顶点偏移，非骨骼动画）        │
│  ├── 密度图生成（基于地形坡度/湿度/海拔）               │
│  └── LOD自动生成（3-4级，最远级Billboard）              │
│                                                         │
│  运行时阶段                                              │
│  ├── GPU Instancing（同类植被合批渲染）                  │
│  ├── Indirect Drawing（GPU驱动，避免CPU瓶颈）            │
│  ├── 密度裁剪（远处降低密度，非等距LOD切换）             │
│  └── 风场系统（全局风向+局部湍流）                       │
│                                                         │
│  性能预算                                                │
│  ├── 草地: < 50万实例（GPU Instancing）                 │
│  ├── 树木: < 5000棵（LOD + 剔除）                       │
│  ├── 灌木: < 2万实例                                     │
│  └── 总植被渲染: < 3ms GPU时间                          │
└─────────────────────────────────────────────────────────┘
```

**草地风动画Vertex Shader**：
```hlsl
// 草地风动画 - 顶点偏移
float3 worldPos = TransformObjectToWorld(v.vertex.xyz);
float windStrength = _WindStrength;

// 全局风向
float2 windDir = normalize(_WindDirection.xz);

// 多层噪声叠加（不同频率产生自然感）
float time = _Time.y * _WindSpeed;
float noise1 = sin(worldPos.x * 0.5 + time) * cos(worldPos.z * 0.3 + time * 0.7);
float noise2 = sin(worldPos.x * 1.2 + time * 1.3) * cos(worldPos.z * 0.8 + time * 0.9);
float wind = (noise1 * 0.6 + noise2 * 0.4) * windStrength;

// 草叶高度因子（顶部偏移大，根部不动）
float heightFactor = saturate(v.vertex.y / _GrassHeight);
heightFactor = heightFactor * heightFactor;  // 二次方，更自然

// 应用偏移
worldPos.xz += windDir * wind * heightFactor;
worldPos.y += abs(wind) * heightFactor * 0.1;  // 轻微上下弹跳
```

---

## 模块三：工具链开发（Toolchain Development）

### 核心问题
> "怎么让团队的10个人不再重复劳动，把精力花在创作上？"

### 工具链三层架构

```
┌─────────────────────────────────────────────────┐
│          Layer 3: 引擎编辑器扩展                  │
│  ├── 自定义Inspector面板                        │
│  ├── 资产导入后处理（OnPostprocessAllAssets）    │
│  ├── 批量操作工具（材质替换、命名规范化）         │
│  └── 自动化构建管线（CI/CD集成）                │
├─────────────────────────────────────────────────┤
│          Layer 2: DCC插件                        │
│  ├── Maya插件（Python/C++ API）                 │
│  ├── Blender插件（bpy API）                     │
│  ├── Houdini HDA（VEX + Python）                │
│  └── Substance Designer/SD+扩展                 │
├─────────────────────────────────────────────────┤
│          Layer 1: 脚本工具                       │
│  ├── Python批处理（文件重命名、格式转换）         │
│  ├── 命令行工具（FBX/OBJ/glTF转换）             │
│  └── 自动化检查（面数、命名、UV规范）            │
└─────────────────────────────────────────────────┘
```

### Python工具开发模板

**资产检查工具**（通用模式）：
```python
# 资产规范检查器模板
class AssetChecker:
    def __init__(self, rules: dict):
        self.rules = rules  # 规则配置
        self.errors = []
        self.warnings = []

    def check_mesh(self, mesh_path):
        # 1. 面数检查
        tri_count = get_triangle_count(mesh_path)
        if tri_count > self.rules['max_triangles']:
            self.errors.append(f"面数超标: {tri_count} > {self.rules['max_triangles']}")

        # 2. 命名规范
        name = basename(mesh_path)
        if not re.match(self.rules['naming_pattern'], name):
            self.errors.append(f"命名不规范: {name}")

        # 3. UV检查
        if not has_valid_uvs(mesh_path):
            self.warnings.append("缺少UV或UV重叠")

        # 4. 枢轴点检查
        if not pivot_at_origin(mesh_path):
            self.warnings.append("枢轴点不在原点")

        return self.errors, self.warnings
```

### DCC插件开发优先级

| 优先级 | 工具类型 | 开发周期 | 价值 |
|--------|---------|---------|------|
| P0 | 资产导出/导入标准化 | 1-2周 | 消除80%的管线摩擦 |
| P1 | 批量操作工具 | 2-3周 | 释放美术重复劳动时间 |
| P2 | 自动化检查 | 1-2周 | 减少返工和Bug |
| P3 | 自定义编辑器面板 | 2-4周 | 提升特定工作流效率 |
| P4 | CI/CD集成 | 1-2周 | 自动化构建和验证 |

### USD管线实践

**USD的核心概念映射**：
```
USD概念          → 类比Web
─────────────────────────────
Prim            → DOM Element
Attribute       → CSS Property
Variant         → CSS Media Query
Payload         → Lazy Loading
Reference       → <iframe>
Stage           → HTML Document
Layer Stack     → CSS Cascade
```

**USD管线关键节点**：
1. **导出阶段** — DCC → USD（Maya/Blender/Houdini原生支持）
2. **组合阶段** — 多资产通过`Reference`和`Payload`组装场景
3. **变体管理** — 同一资产的LOD/材质变体通过`Variant`管理
4. **消费阶段** — 引擎/渲染器读取USD Stage

### CI/CD管线设计

```
┌─────────────────────────────────────────────────────────┐
│          美术资产CI/CD管线                                │
│                                                         │
│  提交触发                                                │
│  ├── Git LFS / Perforce 提交                            │
│  ├── 文件类型检测（.fbx/.png/.usd/.hda）                │
│  └── 触发对应的验证流水线                                │
│                                                         │
│  验证阶段（自动运行）                                    │
│  ├── 命名规范检查（正则匹配）                            │
│  ├── 面数/纹理尺寸检查（超限阻断）                      │
│  ├── UV检查（重叠/超出0-1范围）                         │
│  ├── 骨骼命名检查（匹配标准层级）                       │
│  ├── 材质ID检查（引用了有效材质）                       │
│  └── 依赖检查（引用的纹理/模型是否存在）                │
│                                                         │
│  转换阶段                                                │
│  ├── FBX → 引擎格式（自动化导入）                       │
│  ├── 纹理压缩（ASTC/BC7/ETC2 按平台）                   │
│  ├── LOD生成（自动减面 + 合并）                         │
│  └── USD组装（多资产组合为场景）                        │
│                                                         │
│  部署阶段                                                │
│  ├── 资产库更新（版本化存储）                            │
│  ├── 引擎工程同步（自动化导入+索引）                    │
│  └── 通知（Slack/飞书告知团队）                         │
└─────────────────────────────────────────────────────────┘
```

**资产验证脚本模板（Python + CI集成）**：
```python
# 资产CI验证器 - 集成到Git hook或CI pipeline
import json, re, sys
from pathlib import Path

VALIDATION_RULES = {
    "textures": {
        "max_size": 4096,
        "formats": [".png", ".tga", ".exr"],
        "naming": r"^[a-z][a-z0-9_]*_(d|n|r|m|e|a)$",  # _diffuse/_normal/_roughness...
        "power_of_two": True
    },
    "meshes": {
        "max_triangles": {"character": 50000, "prop": 10000, "environment": 100000},
        "naming": r"^(SM|SK|BP)_[A-Z][a-zA-Z0-9_]*$",  # SM_Static/SK_Skeletal/BP_Blueprint
        "pivot_at_origin": True,
        "uv_in_01": True
    },
    "animations": {
        "max_duration_sec": 30,
        "min_keyframe_rate": 15,  # fps
        "naming": r"^(A|AM)_[A-Z][a-zA-Z0-9_]*$"  # A_Idle/AM_Attack
    }
}

def validate_asset(filepath: Path) -> list:
    errors = []
    ext = filepath.suffix.lower()

    if ext in VALIDATION_RULES["textures"]["formats"]:
        rules = VALIDATION_RULES["textures"]
        if not re.match(rules["naming"], filepath.stem):
            errors.append(f"命名不规范: {filepath.name}")
        # 检查纹理尺寸（需要PIL或imageio）
        # size = get_image_size(filepath)
        # if max(size) > rules["max_size"]:
        #     errors.append(f"纹理过大: {max(size)} > {rules['max_size']}")

    elif ext in [".fbx", ".obj", ".gltf", ".usd"]:
        rules = VALIDATION_RULES["meshes"]
        if not re.match(rules["naming"], filepath.stem):
            errors.append(f"命名不规范: {filepath.name}")
        # 检查面数（需要FBX SDK或trimesh）
        # tri_count = get_triangle_count(filepath)
        # ...

    return errors

# CI入口
if __name__ == "__main__":
    asset_dir = Path(sys.argv[1])
    all_errors = []
    for f in asset_dir.rglob("*"):
        errs = validate_asset(f)
        all_errors.extend(errs)

    if all_errors:
        print("❌ 资产验证失败:")
        for e in all_errors: print(f"  - {e}")
        sys.exit(1)
    else:
        print("✅ 所有资产验证通过")
```

### 版本控制策略（大文件）

| 方案 | 适用规模 | 特点 |
|------|---------|------|
| **Git LFS** | 小团队(<20人) | 简单，与Git集成，大文件锁 |
| **Perforce (P4)** | 中大团队(20-200) | 业界标准，原子提交，文件锁 |
| **Plastic SCM** | 中团队(10-50) | 可视化好，支持大文件，分支灵活 |
| **SVN + 自建** | 小团队 | 简单但功能有限 |

**Git LFS配置模板**：
```gitattributes
# .gitattributes - 大文件规则
*.fbx filter=lfs diff=lfs merge=lfs -text
*.png filter=lfs diff=lfs merge=lfs -text
*.tga filter=lfs diff=lfs merge=lfs -text
*.exr filter=lfs diff=lfs merge=lfs -text
*.usd filter=lfs diff=lfs merge=lfs -text
*.usdz filter=lfs diff=lfs merge=lfs -text
*.hda filter=lfs diff=lfs merge=lfs -text
*.unitypackage filter=lfs diff=lfs merge=lfs -text
*.psd filter=lfs diff=lfs merge=lfs -text
```

### 资产管理系统（Asset Management）

```
┌─────────────────────────────────────────────────────────┐
│          资产管理系统架构                                 │
│                                                         │
│  元数据层                                                │
│  ├── 资产ID（全局唯一，UUID或哈希）                      │
│  ├── 版本号（语义化版本 或 时间戳）                      │
│  ├── 依赖关系图（谁引用了谁，谁被谁引用）                │
│  ├── 标签/分类（角色/场景/特效/UI）                      │
│  └── 平台变体（PC版/移动版/主机版）                      │
│                                                         │
│  存储层                                                  │
│  ├── 源文件存储（DCC原始文件，如.ma/.blend/.hip）        │
│  ├── 中间格式存储（FBX/glTF/USD）                       │
│  ├── 引擎格式存储（引擎内部格式）                       │
│  └── 平台构建产物（压缩后/打包后）                      │
│                                                         │
│  查询层                                                  │
│  ├── 按名称/标签搜索                                    │
│  ├── 按依赖关系查找（"哪些场景用了这个模型？"）         │
│  ├── 按使用频率排序（常用/罕用/未使用）                  │
│  └── 按大小排序（找出最占内存的资产）                    │
│                                                         │
│  清理层                                                  │
│  ├── 孤立资产检测（无引用的资产）                        │
│  ├── 重复资产检测（内容哈希比较）                        │
│  ├── 过期资产标记（超过N天未使用）                       │
│  └── 自动归档（冷数据移至低成本存储）                    │
└─────────────────────────────────────────────────────────┘
```

**资产依赖分析脚本（Unity）**：
```csharp
// 资产依赖分析器
public class AssetDependencyAnalyzer
{
    public static Dictionary<string, HashSet<string>> BuildDependencyGraph(string assetDir)
    {
        var graph = new Dictionary<string, HashSet<string>>();
        var allAssets = Directory.GetFiles(assetDir, "*.*", SearchOption.AllDirectories)
            .Where(f => !f.EndsWith(".meta"));

        foreach(var assetPath in allAssets)
        {
            var deps = AssetDatabase.GetDependencies(assetPath, recursive: false);
            graph[assetPath] = new HashSet<string>(deps);
        }
        return graph;
    }

    public static List<string> FindOrphanAssets(Dictionary<string, HashSet<string>> graph)
    {
        var referenced = new HashSet<string>();
        foreach(var deps in graph.Values)
            referenced.UnionWith(deps);

        return graph.Keys
            .Where(path => !referenced.Contains(path))
            .ToList();
    }

    public static void PrintReport(string assetDir)
    {
        var graph = BuildDependencyGraph(assetDir);
        var orphans = FindOrphanAssets(graph);

        Debug.Log($"总资产数: {graph.Count}");
        Debug.Log($"孤立资产: {orphans.Count}");
        foreach(var orphan in orphans.Take(20))
            Debug.Log($"  孤立: {orphan}");
    }
}
```

### 多平台构建管线

```
┌─────────────────────────────────────────────────────────┐
│          多平台构建管线                                   │
│                                                         │
│  触发条件                                                │
│  ├── 定时构建（每日夜间自动构建）                        │
│  ├── 提交触发（主分支合并后触发）                        │
│  ├── 手动触发（QA/制作人按需触发）                       │
│  └── 标签触发（打tag触发正式构建）                       │
│                                                         │
│  构建阶段                                                │
│  ├── Stage 1: 资产处理                                  │
│  │   ├── 纹理压缩（按平台选择ASTC/BC7/ETC2）            │
│  │   ├── Mesh优化（LOD生成/顶点压缩/碰撞生成）          │
│  │   ├── Shader编译（按平台编译变体）                    │
│  │   └── 资源打包（Addressables/AssetBundle）            │
│  ├── Stage 2: 代码编译                                  │
│  │   ├── 脚本编译（C#/C++）                             │
│  │   ├── IL2CPP（移动端必需）                            │
│  │   └── 代码裁剪（Stripping，减少包体）                │
│  ├── Stage 3: 打包                                      │
│  │   ├── Windows: .exe + Data                           │
│  │   ├── Android: .aab/.apk                             │
│  │   ├── iOS: .ipa (via Xcode)                          │
│  │   ├── PS5: .pkg                                       │
│  │   └── Xbox: .xvc                                      │
│  └── Stage 4: 部署                                      │
│      ├── 内部测试服务器                                  │
│      ├── Steam/TestFlight/PSN测试通道                   │
│      └── 自动化Smoke Test                               │
│                                                         │
│  平台特定配置                                            │
│  ├── Android: minSdk 26, targetSdk 34, ABI arm64-v8a   │
│  ├── iOS: 最低iOS 15, Architecture arm64                │
│  ├── PC: x64, DX12/Vulkan                               │
│  └── 主机: 平台SDK版本锁定                              │
└─────────────────────────────────────────────────────────┘
```

---

## 模块四：特效管线（VFX Pipeline）

### 核心问题
> "怎么在有限的GPU预算内做出最有冲击力的特效？"

### 特效技术栈选型

```
引擎/框架选择？
├── Unity →
│   ├── 简单特效 → Particle System（内置）
│   ├── 复杂特效 → VFX Graph（GPU粒子）
│   └── 移动端 → 精简粒子 + Billboard
├── Unreal →
│   ├── 粒子特效 → Niagara（首选）
│   ├── 流体/布料 → Chaos Physics
│   └── 移动端 → Niagara移动端优化模式
└── 自研/通用 →
    ├── GPU粒子 → Compute Shader
    ├── 流体 → SPH / FLIP
    └── 体积效果 → Ray Marching
```

### 特效性能预算

| 特效类型 | 粒子数上限(移动端) | 粒子数上限(PC) | Shader指令上限 |
|---------|:-----------------:|:-------------:|:-------------:|
| 角色技能 | 200 | 2000 | 64 |
| 环境特效(火/烟) | 100 | 1000 | 48 |
| 天气系统 | 500(实例化) | 5000(实例化) | 32 |
| UI特效 | 50 | 200 | 16 |
| 击杀/爆炸 | 300 | 3000 | 96 |

### 特效优化策略

1. **LOD分级** — 远处特效降粒子数、降分辨率、关光照
2. **GPU Instancing** — 同类粒子用实例化渲染
3. **屏幕空间特效** — 后处理替代3D粒子（如体积光、Godray）
4. **Flipbook动画** — 预渲染序列帧替代实时计算
5. **遮挡剔除** — 被遮挡的特效不渲染

### Niagara特效系统设计模板（Unreal）

```
Niagara System: NS_Fire
├── Emitter: EM_FireCore
│   ├── Spawn Rate: 50/s
│   ├── Lifetime: 0.8-1.5s
│   ├── Sprite Size: 20-40cm
│   ├── Color: 橙→红 渐变（Over Life）
│   ├── SubUV Animation: 4×4 Flipbook
│   ├── Noise Force: 驱动火焰飘动
│   └── Point Attraction: 向上飘散
│
├── Emitter: EM_Sparks
│   ├── Burst Spawn: 每0.3s 5-10个
│   ├── Lifetime: 0.2-0.5s
│   ├── Size: 1-3cm
│   ├── Velocity: 径向随机+向上
│   ├── Color: 黄→白 闪烁
│   └── Drag: 快速减速
│
├── Emitter: EM_Smoke
│   ├── Spawn Rate: 10/s（火源上方触发）
│   ├── Lifetime: 2-4s
│   ├── Size: 30-80cm（Over Life放大）
│   ├── Color: 灰→透明（Over Life淡出）
│   └── Curl Noise: 驱动烟雾飘动
│
└── Renderer
    ├── Sprite Renderer（核心粒子）
    ├── Light Renderer（仅近处，1-2个动态光）
    └── Ribbon Renderer（火花拖尾）
```

### Unity VFX Graph设计模板

```
VFX Graph: VFX_MagicCircle
├── Spawn Context
│   ├── Constant Spawn Rate: 200/s
│   └── Burst: 初始化时一次性1000个
│
├── Initialize Particle
│   ├── Position: Circle Shape（半径2m）
│   ├── Lifetime: 1-3s
│   ├── Size: 0.1-0.3m
│   ├── Color: 青蓝渐变
│   └── Velocity: 径向向外+向上
│
├── Update Particle
│   ├── Turbulence: 3D Noise（驱动魔法粒子飘动）
│   ├── Color Over Life: 青→蓝→透明
│   ├── Size Over Life: 0.1→0.3→0
│   ├── Gravity: 轻微向上（反重力感）
│   └── Vector Field Force: 螺旋运动
│
├── Output
│   ├── Quad Renderer（面向摄像机）
│   ├── Additive Blend（发光叠加）
│   └── Soft Particle（边缘柔化）
│
└── GPU Event
    └── 粒子死亡时触发子粒子（光点爆发）
```

### Shader特效实现模板

**溶解效果（Dissolve）**：
```hlsl
// 溶解Shader核心逻辑
float dissolve = SAMPLE_TEXTURE2D(_DissolveTex, sampler_DissolveTex, uv).r;
float threshold = _DissolveAmount; // 0=不溶解, 1=完全溶解

// 溶解边缘发光
float edge_width = 0.05;
float edge = smoothstep(threshold, threshold + edge_width, dissolve);
float edge_glow = (1.0 - edge) * step(threshold - edge_width, dissolve);

// 裁剪
clip(dissolve - threshold);

// 输出：基础颜色 + 边缘发光色
color.rgb += _EdgeColor.rgb * edge_glow * _EdgeIntensity;
```

**全息效果（Hologram）**：
```hlsl
// 全息Shader核心逻辑
// 扫描线
float scanline = sin(uv.y * _ScanlineCount + _Time.y * _ScanlineSpeed) * 0.5 + 0.5;
scanline = pow(scanline, _ScanlinePower);

// 闪烁
float flicker = 1.0 - sin(_Time.y * _FlickerSpeed) * _FlickerIntensity;

// 边缘光（Fresnel）
float fresnel = pow(1.0 - saturate(dot(viewDir, normal)), _FresnelPower);

// 颜色
float3 holo_color = _HoloColor.rgb * (scanline * 0.5 + 0.5) * flicker;
holo_color += _FresnelColor.rgb * fresnel;

// Alpha：半透明+边缘更不透明
float alpha = (_BaseAlpha + fresnel * _FresnelAlpha) * scanline * flicker;
```

### 流体模拟方案选型

| 方案 | 真实感 | 性能 | 适用场景 | 实现复杂度 |
|------|:-----:|:----:|---------|:---------:|
| **SPH（光滑粒子流体动力学）** | ⭐⭐⭐⭐ | ⭐⭐ | 小规模液体（喷泉/水花） | 高 |
| **FLIP（流体隐式粒子）** | ⭐⭐⭐⭐⭐ | ⭐⭐ | 大规模流体（海浪/洪水） | 很高 |
| **Screen-space Fluid** | ⭐⭐⭐⭐ | ⭐⭐⭐ | 近景液体（水杯/药水） | 中 |
| **Meta-ball（隐式曲面）** | ⭐⭐⭐ | ⭐⭐⭐⭐ | 卡通液体（史莱姆/粘液） | 低 |
| **FFT Ocean（频谱海面）** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | 大面积海面 | 中 |
| **Shallow Water（浅水方程）** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | 地面积水/河流 | 低 |

**FFT海面生成核心（Compute Shader）**：
```hlsl
// FFT海面高度场生成（Phillips频谱）
float2 k = float2(
    (id.x - N/2) * (2 * PI / L),
    (id.y - N/2) * (2 * PI / L)
);
float kLen = length(k);
if(kLen < 0.0001) return;

// Phillips频谱
float L_wind = V_wind * V_wind / g;
float phillips = A * exp(-1.0 / (kLen * L_wind * kLen * L_wind))
               / (kLen * kLen * kLen * kLen)
               * pow(dot(normalize(k), normalize(windDir)), 2.0);

// 复数高斯随机数
float2 h0 = sqrt(phillips / 2.0) * gaussianRandom(id, seed);

// 时间演化
float omega = sqrt(g * kLen);  // 色散关系
float cosW = cos(omega * time);
float sinW = sin(omega * time);
float2 ht = complexMul(h0, float2(cosW, sinW))
          + complexMul(conj(h0_tilde_neg), float2(cosW, -sinW));
```

### 布料/软体模拟

| 方案 | 精度 | 性能 | 适用场景 |
|------|:----:|:----:|---------|
| **弹簧-质点系统** | ⭐⭐⭐ | ⭐⭐⭐⭐ | 旗帜/披风/简单布料 |
| **Position Based Dynamics (PBD)** | ⭐⭐⭐⭐ | ⭐⭐⭐ | 角色衣物/互动布料 |
| **XPBD（扩展PBD）** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | 高精度布料（影视级） |
| **有限元方法 (FEM)** | ⭐⭐⭐⭐⭐ | ⭐⭐ | 软体（果冻/肌肉） |

**弹簧-质点布料核心**：
```csharp
// 简化版弹簧-质点布料
public class ClothSimulation : MonoBehaviour
{
    [SerializeField] int gridSize = 32;
    [SerializeField] float restLength = 0.1f;
    [SerializeField] float stiffness = 500f;
    [SerializeField] float damping = 0.98f;
    [SerializeField] Vector3 gravity = new(0, -9.81f, 0);

    Vector3[] positions, velocities;
    int[] constraints;  // 约束对索引

    void Simulate(float dt)
    {
        // 1. 施加外力
        for(int i = 0; i < positions.Length; i++)
        {
            if(IsFixed(i)) continue;
            velocities[i] += gravity * dt;
        }

        // 2. 求解约束（Jacobi迭代）
        for(int iter = 0; iter < 4; iter++)  // 4次迭代
        {
            for(int c = 0; c < constraints.Length; c += 2)
            {
                int a = constraints[c], b = constraints[c+1];
                Vector3 delta = positions[b] - positions[a];
                float dist = delta.magnitude;
                float correction = (dist - restLength) / dist * 0.5f;

                if(!IsFixed(a)) positions[a] += delta * correction;
                if(!IsFixed(b)) positions[b] -= delta * correction;
            }
        }

        // 3. 更新位置
        for(int i = 0; i < positions.Length; i++)
        {
            if(IsFixed(i)) continue;
            velocities[i] *= damping;
            positions[i] += velocities[i] * dt;
        }
    }
}
```

### 破碎系统设计

```
┌─────────────────────────────────────────────────────────┐
│          破碎系统架构                                     │
│                                                         │
│  离线预破碎（Voronoi分解）                               │
│  ├── 输入: 原始Mesh + 撞击点                            │
│  ├── Voronoi分解: 以撞击点为中心，生成N个碎片           │
│  ├── 碎片处理: 独立Mesh + UV重映射 + 碰撞体生成         │
│  └── 层级: 大碎片 → 中碎片 → 小碎片（可递归）           │
│                                                         │
│  运行时破碎                                              │
│  ├── 触发: 力/速度超过阈值                              │
│  ├── 碎片激活: 原物体禁用，碎片物体激活                  │
│  ├── 物理模拟: 碎片刚体 + 重力 + 碰撞                   │
│  ├── 粒子效果: 碎屑/烟尘/火花                           │
│  └── 碎片回收: 超时/出界后回收进对象池                   │
│                                                         │
│  性能控制                                                │
│  ├── 最大碎片数: 20-50（超过不触发破碎）                │
│  ├── 碎片LOD: 远处碎片不渲染只模拟物理                  │
│  ├── 碎片合并: 小碎片自动合并为大碎片                   │
│  └── 物理简化: 碎片用Box碰撞而非Mesh碰撞               │
└─────────────────────────────────────────────────────────┘
```

---

## 模块五：动画管线（Animation Pipeline）

### 核心问题
> "怎么让角色动起来既自然又高效？"

### 动画管线全流程

```
Motion Capture / 手K动画
        ↓
DCC（Maya/Blender）动画制作
        ↓
动画压缩 + 格式转换（FBX → 引擎格式）
        ↓
引擎动画系统
├── 状态机（State Machine）
│   ├── 基础状态（Idle/Walk/Run/Jump）
│   ├── 过渡条件（速度/输入/事件）
│   └── 混合树（Blend Tree）
├── 动画蒙太奇（Montage）
│   ├── 技能动画
│   └── 交互动画
├── IK系统
│   ├── 脚部IK（地形适应）
│   ├── 手部IK（武器持握）
│   └── 眼部IK（注视目标）
└── 高级系统
    ├── Motion Matching（运动匹配）
    ├── 分层动画（Layered Animation）
    └── 物理动画（Physical Animation）
```

### 骨骼绑定规范

**命名规范**：
```
Root
├── Pelvis（骨盆）
│   ├── Spine_01 → Spine_02 → Spine_03（脊柱）
│   │   ├── Neck_01 → Neck_02 → Head（头部）
│   │   ├── Clavicle_L → UpperArm_L → LowerArm_L → Hand_L（左臂）
│   │   └── Clavicle_R → UpperArm_R → LowerArm_R → Hand_R（右臂）
│   ├── Thigh_L → Calf_L → Foot_L → Ball_L（左腿）
│   └── Thigh_R → Calf_R → Foot_R → Ball_R（右腿）
└── IK_*（IK目标骨骼）
```

**骨骼数量预算**：
| 平台 | 主角NPC | 普通NPC | 敌人 |
|------|:-------:|:------:|:----:|
| 移动端 | 60-80 | 30-50 | 20-40 |
| PC/主机 | 100-150 | 60-80 | 40-60 |

### Motion Matching实施要点

**数据准备**：
- 动画数据库：覆盖所有运动方向和速度（至少8方向 × 3速度）
- 特征向量：位置、速度、脚部接触点、未来轨迹
- 采样频率：30fps（与渲染帧率解耦）

**运行时选择**：
- 搜索算法：KD-Tree最近邻（O(log n)）
- 过渡策略：Pose Blending + Trajectory Matching
- 内存预算：动画数据库 < 200MB（压缩后）

### 动画压缩策略

| 压缩方法 | 压缩比 | 质量影响 | 适用场景 |
|---------|:------:|:-------:|---------|
| **关键帧削减** | 3-5x | 小 | 所有动画，去除冗余帧 |
| **曲线拟合** | 2-3x | 极小 | 平滑动画（移动/旋转） |
| **量化压缩** | 2-4x | 小 | 骨骼transform数据 |
| **去除Scale** | 1.3x | 无 | 大部分骨骼不缩放 |
| **骨骼LOD** | 1.5-2x | 中 | 远处角色省略次要骨骼 |
| **动画裁剪** | 可变 | 无 | 屏幕外角色不更新 |

**Unity动画压缩配置**：
```csharp
// AnimationClip压缩设置
AnimationClip clip;
AnimationClipSettings settings = AnimationClipSettings.Get(clip);

// 关键帧削减
clip.EnsureQuaternionContinuity();
EditorCurveBinding[] bindings = AnimationUtility.GetCurveBindings(clip);
foreach(var binding in bindings)
{
    AnimationCurve curve = AnimationUtility.GetEditorCurve(clip, binding);
    // Keyframe削减：减少到原数量的30-50%
    Keyframe[] keys = ReduceKeyframes(curve.keys, tolerance: 0.001f);
    AnimationUtility.SetEditorCurve(clip, binding, new AnimationCurve(keys));
}

// 导入设置
ModelImporter importer;
importer.animationCompression = ModelImporterAnimationCompression.Optimal;
importer.animationPositionError = 0.5f;  // 位置误差容忍度
importer.animationRotationError = 0.5f;  // 旋转误差容忍度
importer.animationScaleError = 0.5f;     // 缩放误差容忍度
```

### Blend Tree设计模式

```
2D Blend Tree（移动系统）:

        Forward
           ↑
           │
    FL ────┼──── FR     FL = Forward Left
           │            FR = Forward Right
  Left ────┼──── Right  BL = Backward Left
           │            BR = Backward Right
    BL ────┼──── BR
           │
        Backward

输入参数:
├── SpeedX: -1.0 ~ 1.0（左右）
└── SpeedY: -1.0 ~ 1.0（前后）

动画映射:
├── Idle: SpeedX=0, SpeedY=0
├── Walk_F: SpeedX=0, SpeedY=0.5
├── Run_F: SpeedX=0, SpeedY=1.0
├── Walk_L: SpeedX=-0.5, SpeedY=0.5
├── Walk_R: SpeedX=0.5, SpeedY=0.5
├── Run_L: SpeedX=-1.0, SpeedY=1.0
├── Run_R: SpeedX=1.0, SpeedY=1.0
├── Walk_B: SpeedX=0, SpeedY=-0.5
└── Run_B: SpeedX=0, SpeedY=-1.0
```

### 分层动画架构

```
Layer 0: 基础层（Base Layer）
├── 移动状态机（Idle/Walk/Run/Jump/Fall）
├── 权重: 1.0
└── 覆盖: 全身

Layer 1: 上半身覆盖层（Upper Body Override）
├── 技能动画（Attack/Cast/Reload）
├── 权重: 0-1.0（通过Avatar Mask控制）
├── Mask: 仅上半身骨骼
└── 混合模式: Override

Layer 2: additive层（Additive）
├── 呼吸/摇摆/受伤抖动
├── 权重: 0.3-0.5
└── 混合模式: Additive

Layer 3: 瞄准层（Aim）
├── 瞄准方向混合（8方向Aim Offset）
├── 权重: 1.0（持枪时激活）
└── Mask: 仅脊柱+手臂

Avatar Mask配置:
├── Root: 不激活
├── Spine: 激活（Layer 1, 3）
├── LeftArm: 激活（Layer 1, 3）
├── RightArm: 激活（Layer 1, 3）
├── LeftLeg: 不激活（Layer 1）/ 激活（Layer 0）
└── RightLeg: 不激活（Layer 1）/ 激活（Layer 0）
```

### IK求解器配置

| IK类型 | 用途 | 算法 | 参数 |
|--------|------|------|------|
| **脚部IK** | 地形适应 | 2-Bone IK + 地面射线检测 | 脚踝→膝盖→臀部 |
| **手部IK** | 武器/攀爬持握 | CCD/2-Bone IK | 手腕→肘→肩 |
| **头部IK** | 注视目标 | Look-At约束 | 头部→目标点 |
| **脊柱IK** | 上半身朝向 | FABRIK | 骨盆→脊柱→头部 |
| **全身IK** | 触摸/互动 | Full Body IK | 末端效应器→全身 |

**脚部IK实现模板（Unity）**：
```csharp
// 脚部IK - 地面适应
public class FootIK : MonoBehaviour
{
    [SerializeField] float footOffset = 0.1f;
    [SerializeField] float raycastHeight = 0.5f;
    [SerializeField] float raycastDistance = 1.0f;
    [SerializeField] LayerMask groundLayer;

    Animator animator;
    float leftFootWeight, rightFootWeight;
    Vector3 leftFootPos, rightFootPos;
    Quaternion leftFootRot, rightFootRot;

    void OnAnimatorIK(int layerIndex)
    {
        // 左脚
        animator.SetIKPositionWeight(AvatarIKGoal.LeftFoot, leftFootWeight);
        animator.SetIKRotationWeight(AvatarIKGoal.LeftFoot, leftFootWeight);
        animator.SetIKPosition(AvatarIKGoal.LeftFoot, leftFootPos);
        animator.SetIKRotation(AvatarIKGoal.LeftFoot, leftFootRot);

        // 右脚（同理）
        animator.SetIKPositionWeight(AvatarIKGoal.RightFoot, rightFootWeight);
        animator.SetIKRotationWeight(AvatarIKGoal.RightFoot, rightFootWeight);
        animator.SetIKPosition(AvatarIKGoal.RightFoot, rightFootPos);
        animator.SetIKRotation(AvatarIKGoal.RightFoot, rightFootRot);
    }

    void UpdateFootIK(AvatarIKGoal foot)
    {
        Vector3 footPos = animator.GetIKPosition(foot);
        Vector3 rayOrigin = footPos + Vector3.up * raycastHeight;

        if(Physics.Raycast(rayOrigin, Vector3.down, out RaycastHit hit,
            raycastDistance + raycastHeight, groundLayer))
        {
            float targetY = hit.point.y + footOffset;
            // 平滑插值，避免脚部突然跳变
            footPos.y = Mathf.Lerp(footPos.y, targetY, Time.deltaTime * 10f);
            // 脚部旋转跟随地面法线
            footRot = Quaternion.FromToRotation(Vector3.up, hit.normal) * transform.rotation;
            footWeight = 1f;
        }
        else
        {
            footWeight = 0f;
        }
    }
}
```

### 布娃娃（Ragdoll）系统

```
┌─────────────────────────────────────────────────────────┐
│          布娃娃系统架构                                   │
│                                                         │
│  离线配置                                                │
│  ├── 骨骼→刚体映射（每根骨骼一个Rigidbody）             │
│  ├── 关节配置（Hinge/Configurable Joint）               │
│  │   ├── 关节角度限制（min/max angle）                  │
│  │   ├── 阻尼（angular damping）                        │
│  │   └── 驱动力（spring/damper）                        │
│  └── 碰撞体（Capsule/Box per bone）                     │
│                                                         │
│  运行时切换                                              │
│  ├── 动画→布娃娃（受击/死亡时）                          │
│  │   ├── 禁用Animator                                   │
│  │   ├── 启用所有Rigidbody                              │
│  │   ├── 施加初始力（受击方向+力度）                     │
│  │   └── 保持碰撞体层级（避免自穿插）                    │
│  ├── 布娃娃→动画（恢复时）                               │
│  │   ├── 渐进混合：布娃娃权重从1→0                      │
│  │   ├── 对齐当前布娃娃姿态到动画姿态                   │
│  │   └── 恢复Animator                                   │
│  └── Active Ragdoll（持续混合）                          │
│      ├── 动画驱动目标姿态                                │
│      ├── 关节Spring力趋向目标姿态                       │
│      └── 物理产生自然的惯性和碰撞响应                   │
└─────────────────────────────────────────────────────────┘
```

**布娃娃混合切换（Unity）**：
```csharp
// 动画↔布娃娃平滑切换
public class RagdollController : MonoBehaviour
{
    [SerializeField] float blendDuration = 0.3f;
    Animator animator;
    Rigidbody[] rigidbodies;
    Collider[] ragdollColliders;

    void Awake()
    {
        rigidbodies = GetComponentsInChildren<Rigidbody>();
        ragdollColliders = GetComponentsInChildren<Collider>();
        SetRagdoll(false);
    }

    public void EnableRagdoll(Vector3 impactForce)
    {
        animator.enabled = false;
        SetRagdoll(true);
        // 施加受击力
        Rigidbody hitBone = GetClosestBone(impactForce.point);
        hitBone.AddForce(impactForce.direction * impactForce.magnitude, ForceMode.Impulse);
    }

    public void DisableRagdoll()
    {
        StartCoroutine(BlendToAnimation());
    }

    IEnumerator BlendToAnimation()
    {
        // 记录当前布娃娃姿态
        Dictionary<Transform, Pose> ragdollPose = CapturePose();

        // 恢复动画
        SetRagdoll(false);
        animator.enabled = true;

        // 混合过渡
        float elapsed = 0;
        while(elapsed < blendDuration)
        {
            float t = elapsed / blendDuration;
            BlendPose(ragdollPose, t);
            elapsed += Time.deltaTime;
            yield return null;
        }
    }

    void SetRagdoll(bool enabled)
    {
        foreach(var rb in rigidbodies)
        {
            rb.isKinematic = !enabled;
        }
        foreach(var col in ragdollColliders)
        {
            col.enabled = enabled;
        }
    }
}
```

### 动画重定向（Retargeting）

```
┌─────────────────────────────────────────────────────────┐
│          动画重定向管线                                   │
│                                                         │
│  标准化骨架（统一命名+层级）                             │
│  ├── 源骨架 → 标准化骨架 → 目标骨架                     │
│  ├── 骨骼映射表（源骨骼名→目标骨骼名）                  │
│  └── 比例校正（不同体型的骨骼长度差异）                  │
│                                                         │
│  重定向方法                                              │
│  ├── 关节空间重定向                                      │
│  │   ├── 每根骨骼的局部旋转直接复制                     │
│  │   ├── 简单快速，适合相似体型                         │
│  │   └── 脚部滑动问题需要IK修正                         │
│  ├── 动作空间重定向                                      │
│  │   ├── 提取动作特征（关节角度/角速度）                │
│  │   ├── 映射到目标骨架的约束空间                       │
│  │   └── 更准确，跨体型效果好                           │
│  └── 数据驱动重定向                                      │
│      ├── 大量源-目标配对训练                            │
│      ├── 神经网络学习映射关系                           │
│      └── 最准确，但需要训练数据                         │
│                                                         │
│  Unity Humanoid重定向配置:                               │
│  ├── 源Avatar: 配置Humanoid映射                         │
│  ├── 目标Avatar: 配置Humanoid映射                       │
│  ├── Animation Type: Humanoid                           │
│  └── Avatar Definition: Copy From Other Avatar          │
└─────────────────────────────────────────────────────────┘
```

### 面部动画管线

```
┌─────────────────────────────────────────────────────────┐
│          面部动画管线                                     │
│                                                         │
│  资产制作                                                │
│  ├── Blendshape建模（52个ARKit标准 或 自定义集合）       │
│  ├── FACS编码（面部动作编码系统）                        │
│  ├── 表情库制作（基础表情→组合表情→对话表情）           │
│  └── 面部骨骼（可选，与Blendshape混合使用）              │
│                                                         │
│  驱动方式                                                │
│  ├── 关键帧动画（手K/动捕）                              │
│  ├── 实时面捕（ARKit/MediaPipe → Blendshape权重）        │
│  ├── 语音驱动（音素→Blendshape映射）                    │
│  └── 情绪系统（情绪标签→表情组合+过渡）                 │
│                                                         │
│  自然化增强                                              │
│  ├── 微表情注入（随机/周期性的微小表情变化）             │
│  ├── 眨眼系统（3-6秒一次，持续150-400ms）               │
│  ├── 眼球运动（注视目标+微扫视+瞳孔反应）               │
│  ├── 呼吸同步（鼻翼微张+胸部起伏）                      │
│  └── 头部微动（idle摇摆+说话时点头/摇头）               │
└─────────────────────────────────────────────────────────┘
```

**情绪→表情组合映射**：
```csharp
// 情绪系统 - 情绪→Blendshape组合
[System.Serializable]
public struct EmotionBlendshape
{
    public string name;       // Blendshape名
    public float weight;      // 权重 0-1
}

[System.Serializable]
public struct EmotionPreset
{
    public string emotion;    // 情绪名
    public EmotionBlendshape[] shapes;
    public float transitionSpeed;  // 过渡速度
}

// 示例预设
EmotionPreset happy = new EmotionPreset {
    emotion = "happy",
    shapes = new[] {
        new EmotionBlendshape { name = "mouthSmileLeft", weight = 0.8f },
        new EmotionBlendshape { name = "mouthSmileRight", weight = 0.8f },
        new EmotionBlendshape { name = "cheekSquintLeft", weight = 0.5f },
        new EmotionBlendshape { name = "cheekSquintRight", weight = 0.5f },
        new EmotionBlendshape { name = "eyeSquintLeft", weight = 0.3f },
        new EmotionBlendshape { name = "eyeSquintRight", weight = 0.3f },
    },
    transitionSpeed = 3f  // 0.33秒过渡
};
```

---

## 模块六：性能优化（Performance Optimization）

### 核心问题
> "画面好看但跑不动，怎么在不降低画质的前提下提性能？"

### 性能分析工具链

| 平台 | 工具 | 用途 |
|------|------|------|
| 通用 | RenderDoc | GPU帧捕获、Shader分析、Draw Call检查 |
| 通用 | PIX (Windows/Xbox) | GPU时序、资源状态、DX12分析 |
| Unity | Unity Profiler | CPU/GPU/内存/加载全面分析 |
| Unity | Frame Debugger | Draw Call逐帧查看 |
| Unreal | Unreal Insights | 全链路性能分析 |
| Unreal | GPU Visualizer | GPU耗时分析 |
| 移动端 | Mali Offline Compiler | ARM GPU Shader分析 |
| 移动端 | Snapdragon Profiler | Adreno GPU分析 |
| 移动端 | Xcode Instruments | iOS GPU/CPU分析 |

### 性能优化决策树

```
帧率不达标？
├── GPU Bound（GPU瓶颈）
│   ├── Fill Rate过高？ → 降低分辨率/降采样后处理
│   ├── Shader复杂？ → 降低精度/减少采样/LOD Shader
│   ├── Draw Call过多？ → 合批/实例化/SRP Batcher
│   └── 过度绘制？ → 减少透明物体/遮挡剔除
├── CPU Bound（CPU瓶颈）
│   ├── 物理计算？ → 降低模拟频率/简化碰撞体
│   ├── 动画计算？ → 降低远处动画更新频率/骨骼LOD
│   ├── 脚本逻辑？ → 对象池/空间划分/异步加载
│   └── 渲染提交？ → 减少SetPass Call/合批
├── 内存超标
│   ├── 纹理？ → 压缩格式(ASTC/BC7)/Mipmap/流式加载
│   ├── 网格？ → LOD/面数优化/压缩顶点格式
│   ├── 动画？ → 关键帧压缩/骨骼LOD
│   └── 音频？ → 降低采样率/流式播放
└── 加载太慢
    ├── 资源打包 → Addressables/AssetBundle优化
    ├── 异步加载 → 分帧加载/预加载策略
    └── 序列化 → 增量更新/二进制序列化
```

### Draw Call优化清单

| 策略 | 效果 | 实施难度 |
|------|------|---------|
| 静态合批（Static Batching） | 减少50-80% DC | ⭐ 低 |
| 动态合批（Dynamic Batching） | 减少20-40% DC | ⭐ 低 |
| GPU Instancing | 同类物体合批 | ⭐⭐ 中 |
| SRP Batcher (Unity) | 减少SetPass Call | ⭐ 低 |
| 纹理图集（Texture Atlas） | 减少材质切换 | ⭐⭐ 中 |
| HLOD | 远景合批 | ⭐⭐⭐ 高 |

### LOD策略

```
LOD 0 (0-15m):   100% 面数, 全精度材质, 全骨骼
LOD 1 (15-30m):  50% 面数, 简化材质, 骨骼减半
LOD 2 (30-60m):  25% 面数, 最简材质, 关闭IK
LOD 3 (60m+):    Billboard / 不渲染
```

### 遮挡剔除系统

```
┌─────────────────────────────────────────────────────────┐
│          遮挡剔除分层架构                                  │
│                                                         │
│  Layer 1: 视锥剔除（Frustum Culling）                   │
│  ├── 最基础，CPU端执行                                   │
│  ├── 每帧检测物体AABB是否在视锥体内                      │
│  └── 可剔除约50%的场景物体                               │
│                                                         │
│  Layer 2: 遮挡剔除（Occlusion Culling）                  │
│  ├── GPU-driven（Nanite/Unreal）或 Software Occluder    │
│  ├── 上一帧的深度缓冲 → 当前帧的遮挡测试                 │
│  └── 可剔除约30-60%的被遮挡物体                          │
│                                                         │
│  Layer 3: 细节剔除（Detail Culling）                     │
│  ├── 小物体在远处直接不渲染                               │
│  ├── 屏幕空间面积 < 阈值 → 剔除                         │
│  └── 适合：草地、碎石、小装饰物                          │
│                                                         │
│  Layer 4: 预计算可见性（Precomputed Visibility）         │
│  ├── 室内场景：按Cell预计算可见集                        │
│  ├── 运行时查表，零CPU开销                               │
│  └── 适合：室内/城市/地牢                                │
└─────────────────────────────────────────────────────────┘
```

### 流式加载系统

```
┌─────────────────────────────────────────────────────────┐
│          资产流式加载架构                                  │
│                                                         │
│  距离分层                                                │
│  ├── Zone 0 (0-50m):   完整加载，所有Mip                │
│  ├── Zone 1 (50-200m): 低Mip纹理，简化网格              │
│  ├── Zone 2 (200-500m): 最低Mip，极简网格               │
│  └── Zone 3 (500m+):   仅位置数据，按需加载             │
│                                                         │
│  加载策略                                                │
│  ├── 预测性加载（基于移动方向+速度）                     │
│  ├── 优先级队列（近处 > 远处，可见 > 不可见）            │
│  ├── 分帧加载（每帧最多加载N个资产，防卡顿）             │
│  └── 异步IO（不阻塞主线程）                              │
│                                                         │
│  内存管理                                                │
│  ├── LRU缓存（最近最少使用淘汰）                        │
│  ├── 内存预算（硬上限，超限强制淘汰）                    │
│  ├── 引用计数（无引用→标记为可卸载）                     │
│  └── GC时机（加载间隙/场景切换时执行）                   │
└─────────────────────────────────────────────────────────┘
```

**纹理流式加载预算模板**：
```csharp
// Unity纹理流式加载配置
public class TextureStreamingConfig
{
    // 内存预算（按平台）
    public static readonly Dictionary<string, int> MemoryBudgetMB = new() {
        {"mobile_mid",  256},
        {"mobile_high", 512},
        {"pc_medium",   2048},
        {"pc_high",     4096},
        {"console",     4096}
    };

    // Mip层级策略
    public static readonly Dictionary<string, int> MaxMipLevel = new() {
        {"character_near",  0},  // 全分辨率
        {"character_far",   2},  // 1/4分辨率
        {"environment_near", 0},
        {"environment_far",  3},  // 1/8分辨率
        {"terrain",          1}   // 1/2分辨率
    };
}
```

### 多线程渲染策略

| 任务 | 主线程 | 渲染线程 | 工作线程 | GPU |
|------|:------:|:-------:|:-------:|:---:|
| 场景遍历 | ✅ | | | |
| 视锥剔除 | | | ✅ | |
| 命令录制 | | ✅ | | |
| 骨骼动画 | | | ✅ | |
| 物理模拟 | | | ✅ | |
| 粒子更新 | | | | ✅ |
| GPU Skinning | | | | ✅ |
| 后处理 | | | | ✅ |

### 内存Profiling工作流

```
┌─────────────────────────────────────────────────────────┐
│          内存分析工作流                                   │
│                                                         │
│  Step 1: 内存快照采集                                    │
│  ├── Unity: Memory Profiler Package                     │
│  ├── Unreal: memreport -full                            │
│  ├── Android: adb shell dumpsys meminfo                 │
│  └── iOS: Xcode Instruments → Allocations/Leaks         │
│                                                         │
│  Step 2: 分类统计                                        │
│  ├── 纹理内存（通常最大，40-60%）                        │
│  │   ├── 按格式统计（ASTC/BC7/RGBA32）                  │
│  │   ├── 按尺寸统计（4K/2K/1K/512）                     │
│  │   └── 按用途统计（角色/场景/UI/特效）                 │
│  ├── 网格内存（15-25%）                                  │
│  │   ├── 按LOD层级统计                                  │
│  │   └── 按顶点格式统计（位置/法线/UV/颜色）            │
│  ├── 动画内存（5-15%）                                   │
│  ├── 音频内存（5-10%）                                   │
│  └── 其他（Shader/脚本/物理）                            │
│                                                         │
│  Step 3: 识别泄漏                                        │
│  ├── 对比两个快照的差异                                  │
│  ├── 查找只增不减的资源                                  │
│  ├── 检查引用计数（未释放的引用）                        │
│  └── 检查事件订阅（未取消的事件监听）                    │
│                                                         │
│  Step 4: 优化执行                                        │
│  ├── 纹理: 压缩格式+Mipmap+按需加载                     │
│  ├── 网格: LOD+压缩顶点格式+按需加载                    │
│  ├── 对象池: 频繁创建/销毁的物体用池化                   │
│  └── 资源卸载: 场景切换时强制GC+资源卸载                │
└─────────────────────────────────────────────────────────┘
```

**Unity内存分析脚本**：
```csharp
// 内存报告生成器
public class MemoryReporter : MonoBehaviour
{
    public static void GenerateReport()
    {
        var info = new System.Text.StringBuilder();

        // 总体内存
        info.AppendLine($"=== 内存报告 ===");
        info.AppendLine($"Total Reserved: {UnityEngine.Profiling.Profiler.GetTotalReservedMemoryLong() / 1024 / 1024}MB");
        info.AppendLine($"Total Used: {UnityEngine.Profiling.Profiler.GetTotalAllocatedMemoryLong() / 1024 / 1024}MB");
        info.AppendLine($"Total Free: {UnityEngine.Profiling.Profiler.GetTotalUnusedReservedMemoryLong() / 1024 / 1024}MB");

        // 纹理内存
        long texMem = 0;
        foreach(var tex in Resources.FindObjectsOfTypeAll<Texture2D>())
        {
            texMem += UnityEngine.Profiling.Profiler.GetRuntimeMemorySizeLong(tex);
        }
        info.AppendLine($"\n纹理内存: {texMem / 1024 / 1024}MB");

        // 网格内存
        long meshMem = 0;
        foreach(var mesh in Resources.FindObjectsOfTypeAll<Mesh>())
        {
            meshMem += UnityEngine.Profiling.Profiler.GetRuntimeMemorySizeLong(mesh);
        }
        info.AppendLine($"网格内存: {meshMem / 1024 / 1024}MB");

        // 音频内存
        long audioMem = 0;
        foreach(var clip in Resources.FindObjectsOfTypeAll<AudioClip>())
        {
            audioMem += UnityEngine.Profiling.Profiler.GetRuntimeMemorySizeLong(clip);
        }
        info.AppendLine($"音频内存: {audioMem / 1024 / 1024}MB");

        Debug.Log(info.ToString());
    }
}
```

### 平台专项优化

**移动端专项**：
```
┌─────────────────────────────────────────────────────────┐
│          移动端优化清单                                   │
│                                                         │
│  GPU优化                                                 │
│  ├── 纹理: ASTC压缩（Android）/ PVRTC（旧iOS）          │
│  ├── Shader: mediump精度（避免highp除非必要）            │
│  ├── 带宽: 减少overdraw（Alpha测试<Alpha混合）           │
│  ├── Fill Rate: 降低后处理分辨率（1/2或1/4）            │
│  └── 热管理: 帧率降级策略（检测过热自动降30fps→20fps）  │
│                                                         │
│  CPU优化                                                 │
│  ├── 物理: 降低FixedUpdate频率（50Hz→30Hz）             │
│  ├── 动画: 远处角色降低更新频率（每2-4帧更新一次）      │
│  ├── 脚本: 避免每帧GC Alloc（对象池/缓存）              │
│  └── 多线程: Job System并行计算                         │
│                                                         │
│  内存优化                                                │
│  ├── 纹理: 最大尺寸2048（非特写角色）                   │
│  ├── 音频: 压缩格式（Vorbis/AAC），流式播放BGM          │
│  ├── 场景: 分块加载，远处资产降级                       │
│  └── 总预算: < 1.5GB（iOS）/ < 2GB（Android旗舰）       │
│                                                         │
│  电池优化                                                │
│  ├── 后台暂停: 切后台立即暂停渲染+物理                  │
│  ├── 帧率自适应: 静止画面降帧率（30→15fps）             │
│  ├── 网络: 合并请求，减少唤醒次数                       │
│  └── 定位: 降低GPS采样频率                              │
└─────────────────────────────────────────────────────────┘
```

**主机专项（PS5/XSX）**：
```
┌─────────────────────────────────────────────────────────┐
│          主机优化要点                                     │
│                                                         │
│  SSD流式加载                                            │
│  ├── 利用高速SSD实现即时加载                             │
│  ├── 预测性预加载（基于玩家移动方向）                    │
│  └── 虚拟纹理（Virtual Texture）按需加载                │
│                                                         │
│  GPU特性利用                                             │
│  ├── Mesh Shader（替代传统VS+GS管线）                   │
│  ├── Variable Rate Shading（VRS，非焦点区域降采样）     │
│  ├── Hardware Ray Tracing（光追阴影/反射/GI）           │
│  └── Async Compute（异步计算，与图形管线并行）          │
│                                                         │
│  内存管理                                                │
│  ├── PS5: 16GB统一内存（CPU+GPU共享）                   │
│  ├── XSX: 10GB GPU + 6GB CPU（分离内存）                │
│  └── 预算分配: 渲染8GB + 游戏逻辑2GB + 音频1GB + 系统1GB│
└─────────────────────────────────────────────────────────┘
```

### 帧步控制（Frame Pacing）

```
┌─────────────────────────────────────────────────────────┐
│          帧步控制架构                                     │
│                                                         │
│  问题：帧率波动导致卡顿感知                              │
│  ├── 平均60fps但偶尔掉到40fps → 比稳定30fps更难受       │
│  └── 解决：帧步控制确保帧间隔均匀                        │
│                                                         │
│  方案一：固定帧率锁步                                    │
│  ├── VSync ON（垂直同步）                               │
│  ├── 固定30fps或60fps                                   │
│  └── 简单但灵活性差                                      │
│                                                         │
│  方案二：自适应帧率                                      │
│  ├── 目标: 60fps                                        │
│  ├── 如果GPU持续超时 → 降级到30fps                      │
│  ├── 如果GPU轻松达标 → 升级到60fps                      │
│  └── 切换阈值: 连续3帧超时才降级，连续10帧达标才升级    │
│                                                         │
│  方案三：动态分辨率 + 帧步                                │
│  ├── 固定目标帧率（如60fps）                            │
│  ├── 如果GPU超时 → 降低渲染分辨率（如90%→70%）          │
│  ├── 如果GPU轻松 → 提高渲染分辨率（最高100%）           │
│  └── 输出分辨率不变（Upscaling弥补）                    │
└─────────────────────────────────────────────────────────┘
```

**自适应帧率实现（Unity）**：
```csharp
// 自适应帧率控制器
public class AdaptiveFrameRate : MonoBehaviour
{
    [SerializeField] int targetFPS = 60;
    [SerializeField] int minFPS = 30;
    [SerializeField] int degradeThreshold = 3;   // 连续N帧超时降级
    [SerializeField] int upgradeThreshold = 10;  // 连续N帧达标升级

    int consecutiveOverBudget = 0;
    int consecutiveUnderBudget = 0;
    float frameBudget => 1f / targetFPS;

    void Update()
    {
        float frameTime = Time.unscaledDeltaTime;

        if(frameTime > frameBudget * 1.1f)  // 超时10%以上
        {
            consecutiveOverBudget++;
            consecutiveUnderBudget = 0;

            if(consecutiveOverBudget >= degradeThreshold)
            {
                // 降级：降帧率或降分辨率
                if(targetFPS > minFPS)
                {
                    targetFPS /= 2;  // 60→30
                    Application.targetFrameRate = targetFPS;
                    Debug.Log($"帧率降级: {targetFPS}fps");
                }
                consecutiveOverBudget = 0;
            }
        }
        else
        {
            consecutiveUnderBudget++;
            consecutiveOverBudget = 0;

            if(consecutiveUnderBudget >= upgradeThreshold && targetFPS < 60)
            {
                targetFPS *= 2;  // 30→60
                Application.targetFrameRate = targetFPS;
                Debug.Log($"帧率升级: {targetFPS}fps");
                consecutiveUnderBudget = 0;
            }
        }
    }
}
```

---

## 曹炎培战略模型（保留并增强）

> 以下7个心智模型用于**战略层**技术选型和路线判断，与上方6个工程模块形成"战略→执行"闭环。

### 模型1: 原生3D表征
2D像素是3D世界的降维投影，3D才是物理世界原生表征方式。
→ **管线启示**：管线设计应以3D原生数据为核心，2D渲染只是输出端。

### 模型2: 不可能三角
速度、质量、管线可用性，三者不可兼得——直到找到正确路径。
→ **管线启示**：管线设计必须在三角中做出明确取舍，不能三者都想要。

### 模型3: 皮肉骨脑
皮（外观）、肉（拓扑）、骨（绑定）、脑（行为）——四层完整资产。
→ **管线启示**：管线必须覆盖四层，不能只做"皮"就交付。

### 模型4: 速度质变
速度不是效率指标，是创作范式变革。
→ **管线启示**：管线的目标不是"快一点"，是"解锁新的创作模式"。

### 模型5: 光影模拟器批判
纯视频世界模型学到的是光影变化，不是世界规律。
→ **管线启示**：3D管线要维护持久化的世界状态，不能只靠渲染端"幻想"。

### 模型6: 创新者窘境
大厂有资源但有历史包袱，独立平台的优势是技术中立性和敏捷性。
→ **管线启示**：管线设计要避免路径依赖，保持技术栈的可替换性。

### 模型7: USD是3D管线的HTML
USD正在成为3D数据交换和协作的事实标准。
→ **管线启示**：管线必须支持USD导入/导出，否则无法融入主流生产流程。

---

## 决策路由表

| 场景 | 首选模块 | 辅助模块 | 行动指引 |
|------|---------|---------|---------|
| 项目启动，选渲染方案 | 渲染模块 | 曹炎培模型2 | 先定技术栈，再做Shader框架 |
| 开放世界需要大量内容 | PCG模块 | 工具链模块 | Houdini离线PCG + 引擎运行时PCG |
| 团队效率低，重复劳动多 | 工具链模块 | — | 先做P0资产标准化工具 |
| 特效太卡 | 特效模块 | 性能模块 | 先Profile定位瓶颈，再按预算裁剪 |
| 角色动画不自然 | 动画模块 | 渲染模块 | 检查骨骼层级 → IK设置 → 混合树 |
| 帧率不达标 | 性能模块 | — | 先判断GPU/CPU瓶颈，再逐项优化 |
| AI生成资产要接入管线 | 曹炎培模型3 | 工具链模块 | 用"皮肉骨脑"评估资产质量 |
| 竞品管线分析 | 曹炎培模型6 | — | 分析路径依赖程度和切换成本 |
| 选USD还是glTF | 曹炎培模型7 | 工具链模块 | 生产管线用USD，分发用glTF |
| 渲染风格选型 | 渲染模块 | 曹炎培模型2 | NPR vs PBR取舍（不可能三角） |
| 项目要上线了 | 生产就绪模块 | 性能模块 | Go/No-Go检查清单逐项过 |
| 新人加入团队 | 团队协作模块 | 工具链模块 | 分配方向+Code Review规范 |
| 每次提交怕搞坏东西 | QA模块 | 工具链模块 | 四层自动化测试+视觉回归 |
| 上线后要持续更新 | 发布运营模块 | 工具链模块 | 热更新管线+运营资源管理 |
| 某平台渲染异常 | 渲染模块 | 生产就绪模块 | Shader编译日志+平台特性检查 |
| 包体太大装不下 | 工具链模块 | 性能模块 | 包体分析+纹理压缩+资源分包 |

---

## 工作流（Agentic Protocol）

### 第一步：问题分类

| 类型 | 行动 |
|------|------|
| **战略类**（技术路线、竞品、趋势） | → 战略模式，用曹炎培模型分析 |
| **工程类**（管线设计、工具开发、优化） | → 工程模式，给具体方案 |
| **混合类**（战略+工程） | → 先战略定方向，再工程给方案 |

### 第二步：研究（按需）

**战略类问题** → 联网搜索最新行业动态、论文、产品发布
**工程类问题** → 搜索引擎文档、社区方案、GDC分享
**混合类问题** → 两者都做

### 第三步：输出

**战略模式输出**：
1. 结论先行（1-2句）
2. 用哪个心智模型
3. 具体分析 + 案例
4. 局限提醒

**工程模式输出**：
1. 技术选型建议（含对比表）
2. 实施步骤（可执行的流程）
3. 关键代码/配置片段
4. 注意事项和常见坑
5. 性能基线和验收标准

---

## 身份卡（战略模式）

**我是谁**：曹炎培，VAST首席科学家。清华博士，师从胡事民。70多篇论文，Google Scholar六千多次引用。从3D重建做到3D生成，现在在做世界模型。

**核心信念**：3D是物理世界的原生信号，不是2D的衍生品。管线可用性比视觉精度更重要。速度的量变会引发创作范式的质变。

---

## 诚实边界

- 工程模块的技术方案基于行业最佳实践，具体实现需根据项目规模和团队能力调整
- 性能基线数据基于2025-2026年主流硬件，新硬件发布后需要更新
- AI+3D领域变化极快，曹炎培的战略判断需要3-5年验证
- PCG和Houdini部分偏概念层面，具体HDA实现需要根据项目定制
- USD管线在小型项目中可能过度工程，需评估投入产出比

---

## 验证标准

### 测试用例

| # | 测试问题 | 期望行为 |
|---|---------|---------|
| 1 | "移动端渲染管线怎么选？" | 给出URP vs 自研对比，附性能基线 |
| 2 | "Houdini程序化建模怎么做？" | 给出SOP网络设计原则 + HDA模板 |
| 3 | "Draw Call太多怎么优化？" | 给出决策树 + 具体策略清单 |
| 4 | "AI生成的模型拓扑很差怎么办？" | 切换战略模式，用"皮肉骨脑"分析 |
| 5 | "动画状态机怎么设计？" | 给出分层架构 + 命名规范 |
| 6 | "视频生成能替代3D管线吗？" | 切换战略模式，用原生3D模型分析 |

---

## 模块七：生产就绪（Production Readiness）

### 核心问题
> "技术上能跑不等于能上线——怎么确保管线在真实生产环境中扛得住？"

### 生产就绪检查清单

```
┌─────────────────────────────────────────────────────────┐
│          生产就绪 Go/No-Go 检查清单                      │
│                                                         │
│  渲染管线就绪                                            │
│  □ 目标平台全部跑通，无Shader编译错误                    │
│  □ 性能基线达标（帧率/内存/Draw Call）                   │
│  □ 所有材质在目标平台渲染正确（无粉紫/黑色材质）        │
│  □ LOD切换无明显跳变（pop）                              │
│  □ 光照在所有场景一致（无漏光/黑斑）                     │
│  □ 后处理效果在不同分辨率下表现一致                      │
│                                                         │
│  资产管线就绪                                            │
│  □ 所有资产通过自动化检查（命名/面数/UV/格式）          │
│  □ 纹理压缩在所有平台正确（无花屏/色带）                │
│  □ 资产加载时间在可接受范围（<3s单资产）                 │
│  □ 内存占用在预算内（无内存泄漏）                        │
│  □ 资源热更新流程验证通过                                │
│                                                         │
│  动画管线就绪                                            │
│  □ 所有角色动画在引擎中播放正确（无穿模/抖动）          │
│  □ 动画状态机覆盖所有状态转换（无死循环/卡死）          │
│  □ IK在所有地形条件下正常工作                            │
│  □ 布娃娃在所有受击条件下无穿插                          │
│                                                         │
│  特效管线就绪                                            │
│  □ 所有特效在目标平台性能达标                            │
│  □ 特效在不同光照条件下表现一致                          │
│  □ 粒子系统无内存泄漏                                    │
│                                                         │
│  工具链就绪                                              │
│  □ CI/CD管线全部绿灯                                     │
│  □ 自动化测试覆盖核心流程                                │
│  □ 资产验证无阻断性错误                                  │
│  □ 多平台构建全部成功                                    │
│                                                         │
│  性能就绪                                                │
│  □ 目标帧率稳定（无持续掉帧）                            │
│  □ 加载时间在可接受范围（<10s场景切换）                  │
│  □ 内存在长时间运行后稳定（无泄漏）                      │
│  □ 低端设备降级策略验证通过                              │
└─────────────────────────────────────────────────────────┘
```

### 性能回归检测

```python
# 性能回归自动检测（CI集成）
import json, sys

THRESHOLDS = {
    "frame_time_ms": {"max": 16.6, "warn": 14.0},     # 60fps目标
    "memory_mb":     {"max": 2048, "warn": 1800},
    "draw_calls":    {"max": 2000, "warn": 1500},
    "triangles":     {"max": 2000000, "warn": 1500000},
    "load_time_s":   {"max": 10.0, "warn": 7.0},
    "texture_mem_mb":{"max": 1024, "warn": 800},
}

def check_regression(current: dict, baseline: dict) -> list:
    """对比当前性能数据与基线，检测回归"""
    issues = []
    for metric, thresholds in THRESHOLDS.items():
        curr = current.get(metric, 0)
        base = baseline.get(metric, 0)

        # 绝对阈值检查
        if curr > thresholds["max"]:
            issues.append(f"❌ {metric}: {curr} 超过上限 {thresholds['max']}")
        elif curr > thresholds["warn"]:
            issues.append(f"⚠️ {metric}: {curr} 接近上限 {thresholds['max']}")

        # 回归检查（比基线差10%以上）
        if base > 0 and curr > base * 1.1:
            pct = (curr - base) / base * 100
            issues.append(f"📉 {metric}: 比基线差 {pct:.1f}% ({base} → {curr})")

    return issues

# CI入口
if __name__ == "__main__":
    with open(sys.argv[1]) as f: current = json.load(f)
    with open(sys.argv[2]) as f: baseline = json.load(f)

    issues = check_regression(current, baseline)
    if issues:
        print("性能回归检测:")
        for i in issues: print(f"  {i}")
        if any("❌" in i for i in issues):
            sys.exit(1)  # 阻断构建
    else:
        print("✅ 性能无回归")
```

## 模块八：团队协作（Team Collaboration）

### 核心问题
> "10个人的TA团队怎么协作，避免互相踩踏和重复劳动？"

### TA团队组织架构

```
┌─────────────────────────────────────────────────────────┐
│          TA团队分工（10人规模参考）                        │
│                                                         │
│  TA Lead / 管线架构师（1人）                             │
│  ├── 管线架构设计与技术选型                              │
│  ├── 资产规范制定与维护                                  │
│  ├── 跨团队沟通（美术↔程序↔策划）                       │
│  └── 技术债务管理与优先级排序                            │
│                                                         │
│  渲染TA（2人）                                           │
│  ├── Shader框架开发与维护                                │
│  ├── 渲染管线优化与平台适配                              │
│  ├── 光照方案与材质系统                                  │
│  └── 后处理效果开发                                      │
│                                                         │
│  工具/管线TA（2人）                                      │
│  ├── DCC插件开发（Maya/Blender/Houdini）                │
│  ├── CI/CD管线维护                                       │
│  ├── 资产检查与自动化工具                                │
│  └── 引擎编辑器扩展                                      │
│                                                         │
│  动画TA（2人）                                           │
│  ├── 骨骼绑定与动画系统                                  │
│  ├── 状态机与Blend Tree                                  │
│  ├── IK/物理动画                                        │
│  └── 面部动画管线                                        │
│                                                         │
│  PCG/特效TA（2人）                                       │
│  ├── 程序化内容生成（Houdini/引擎PCG）                  │
│  ├── 特效系统开发（Niagara/VFX Graph）                  │
│  ├── 场景布置与环境美术支持                              │
│  └── 运行时PCG系统                                       │
│                                                         │
│  性能/平台TA（1人）                                      │
│  ├── 性能分析与优化                                      │
│  ├── 平台适配（移动端/主机）                             │
│  ├── 内存管理与流式加载                                  │
│  └── 热更新与Live Ops支持                                │
└─────────────────────────────────────────────────────────┘
```

### Code Review规范（TA专用）

```
┌─────────────────────────────────────────────────────────┐
│          TA Code Review 检查项                           │
│                                                         │
│  Shader代码审查                                          │
│  □ 指令数是否在预算内（移动端<128指令）                  │
│  □ 纹理采样次数是否合理（移动端<8次）                    │
│  □ 是否有不必要的精度（half vs float）                   │
│  □ Keyword数量是否过多（<128个）                         │
│  □ 是否兼容SRP Batcher / Material Instance              │
│                                                         │
│  工具代码审查                                            │
│  □ 是否有错误处理（文件不存在/格式错误）                 │
│  □ 是否有进度反馈（大文件处理时）                        │
│  □ 是否支持撤销/回滚                                     │
│  □ 是否有日志记录（便于排查问题）                        │
│  □ 是否影响DCC软件的正常操作                             │
│                                                         │
│  引擎代码审查                                            │
│  □ 是否有GC Alloc（每帧分配）                            │
│  □ 是否有不必要的GetComponent调用                        │
│  □ 是否使用了对象池（频繁创建的物体）                    │
│  □ 线程安全性（Job System/多线程访问）                   │
│  □ 是否在Update中做了可以缓存的查询                     │
│                                                         │
│  管线配置审查                                            │
│  □ 资产规范是否正确执行                                  │
│  □ 平台特定配置是否正确                                  │
│  □ 依赖关系是否完整                                      │
│  □ 是否有硬编码路径                                      │
└─────────────────────────────────────────────────────────┘
```

### 技术债务管理

```
┌─────────────────────────────────────────────────────────┐
│          技术债务分类与优先级                             │
│                                                         │
│  P0: 阻断性（必须立即修复）                              │
│  ├── 生产线崩溃（资产无法导出/导入）                     │
│  ├── 性能严重退化（帧率腰斩）                            │
│  └── 数据丢失风险（资产损坏/丢失）                       │
│                                                         │
│  P1: 高影响（本迭代内修复）                              │
│  ├── 效率瓶颈（某个流程耗时过长）                        │
│  ├── 平台兼容问题（某平台渲染异常）                      │
│  └── 内存泄漏（长时间运行后崩溃）                        │
│                                                         │
│  P2: 中影响（下个迭代修复）                              │
│  ├── 代码质量问题（重复代码/魔数/硬编码）                │
│  ├── 文档缺失（关键流程无文档）                          │
│  └── 测试覆盖不足（核心流程无自动化测试）                │
│                                                         │
│  P3: 低影响（有空再修）                                  │
│  ├── 命名不规范                                         │
│  ├── 日志不够详细                                       │
│  └── 工具UI不够美观                                     │
│                                                         │
│  债务偿还策略:                                           │
│  ├── 每个迭代分配20%时间处理技术债务                     │
│  ├── P0随时修，P1本迭代修，P2-P3排期修                  │
│  └── 重构时顺手修（Boy Scout Rule）                     │
└─────────────────────────────────────────────────────────┘
```

## 模块九：质量保证（Quality Assurance）

### 核心问题
> "怎么确保每次提交不会搞坏已有的管线功能？"

### 自动化测试架构

```
┌─────────────────────────────────────────────────────────┐
│          管线自动化测试分层                               │
│                                                         │
│  Layer 1: 单元测试（每次提交触发）                       │
│  ├── 资产检查器测试（输入无效文件→正确报错）            │
│  ├── 格式转换测试（FBX→glTF→USD→引擎格式）             │
│  ├── Shader编译测试（所有Shader在目标平台编译通过）     │
│  └── 工具函数测试（数学函数/坐标变换/数据解析）         │
│                                                         │
│  Layer 2: 集成测试（每日构建触发）                       │
│  ├── 完整资产导入流程（DCC→引擎→运行时）                │
│  ├── PCG系统端到端（参数→生成→渲染）                    │
│  ├── 动画管线端到端（制作→导入→播放→状态机）           │
│  └── 多平台构建验证（所有目标平台构建成功）              │
│                                                         │
│  Layer 3: 回归测试（每周/里程碑触发）                    │
│  ├── 性能基线对比（帧率/内存/加载时间）                  │
│  ├── 视觉回归测试（截图对比，检测渲染异常）             │
│  ├── 资产大小检查（包体是否异常膨胀）                    │
│  └── 兼容性测试（不同硬件/驱动版本）                    │
│                                                         │
│  Layer 4: 冒烟测试（发布前必须通过）                     │
│  ├── 核心流程走通（创建→编辑→导出→运行）                │
│  ├── 所有平台启动无崩溃                                  │
│  ├── 性能无重大回归                                      │
│  └── 无P0/P1级别Bug                                     │
└─────────────────────────────────────────────────────────┘
```

### 视觉回归测试

```python
# 视觉回归测试 - 截图对比（CI集成）
import numpy as np
from PIL import Image
import sys

def compare_images(img_path_a: str, img_path_b: str,
                   threshold: float = 0.01) -> dict:
    """对比两张截图，检测视觉回归"""
    a = np.array(Image.open(img_path_a)).astype(float)
    b = np.array(Image.open(img_path_b)).astype(float)

    if a.shape != b.shape:
        return {"status": "fail", "reason": "分辨率不同",
                "shape_a": a.shape, "shape_b": b.shape}

    # 像素差异
    diff = np.abs(a - b) / 255.0
    mean_diff = diff.mean()
    max_diff = diff.max()
    diff_pixels = (diff > 0.05).sum()  # 差异>5%的像素数
    total_pixels = a.size
    diff_ratio = diff_pixels / total_pixels

    # 判定
    status = "pass"
    if diff_ratio > threshold:
        status = "fail"
    elif diff_ratio > threshold * 0.5:
        status = "warn"

    return {
        "status": status,
        "mean_diff": float(mean_diff),
        "max_diff": float(max_diff),
        "diff_pixels": int(diff_pixels),
        "total_pixels": int(total_pixels),
        "diff_ratio": float(diff_ratio),
    }

if __name__ == "__main__":
    result = compare_images(sys.argv[1], sys.argv[2])
    print(f"状态: {result['status']}")
    print(f"差异像素: {result['diff_pixels']}/{result['total_pixels']} "
          f"({result['diff_ratio']*100:.2f}%)")
    if result["status"] == "fail":
        sys.exit(1)
```

### Shader编译验证

```python
# Shader编译CI验证 - 确保所有Shader在目标平台编译通过
import subprocess, sys, os

PLATFORMS = {
    "android_vulkan":  {"api": "vulkan",   "shader_model": "es3.1"},
    "ios_metal":       {"api": "metal",    "shader_model": "metal"},
    "windows_d3d12":   {"api": "d3d12",    "shader_model": "sm5.0"},
    "windows_vulkan":  {"api": "vulkan",   "shader_model": "sm5.0"},
    "ps5":             {"api": "agc",      "shader_model": "ps5"},
}

def compile_shader(shader_path: str, platform: str) -> tuple:
    """编译单个Shader到指定平台"""
    config = PLATFORMS[platform]
    # 使用引擎的命令行编译工具
    cmd = [
        "Unity", "-batchmode", "-quit",
        "-projectPath", ".",
        "-executeMethod", "ShaderCompiler.Compile",
        "-shader", shader_path,
        "-platform", config["api"],
    ]
    result = subprocess.run(cmd, capture_output=True, text=True, timeout=60)
    return result.returncode == 0, result.stderr

def validate_all_shaders(shader_dir: str):
    """验证所有Shader在所有平台编译通过"""
    shaders = [f for f in os.listdir(shader_dir)
               if f.endswith(('.shader', '.hlsl', '.cginc'))]

    failures = []
    for shader in shaders:
        for platform in PLATFORMS:
            ok, err = compile_shader(
                os.path.join(shader_dir, shader), platform)
            if not ok:
                failures.append(f"{shader} @ {platform}: {err[:200]}")

    if failures:
        print(f"❌ {len(failures)} 个Shader编译失败:")
        for f in failures: print(f"  {f}")
        sys.exit(1)
    else:
        print(f"✅ {len(shaders)} 个Shader × {len(PLATFORMS)} 平台全部编译通过")
```

## 模块十：发布与运营（Release & Live Ops）

### 核心问题
> "上线后管线怎么支持持续更新和运营活动？"

### 发布管线

```
┌─────────────────────────────────────────────────────────┐
│          发布管线全流程                                   │
│                                                         │
│  Phase 1: 开发分支冻结                                   │
│  ├── 功能冻结（Feature Freeze）                          │
│  ├── 只修Bug，不加新功能                                 │
│  └── 技术债务暂停偿还                                    │
│                                                         │
│  Phase 2: 候选版本构建                                   │
│  ├── 全平台构建（Release Build）                         │
│  ├── 自动化测试全量运行                                  │
│  ├── 性能基线对比（无回归）                              │
│  └── 视觉回归测试（无渲染异常）                          │
│                                                         │
│  Phase 3: QA验证                                         │
│  ├── 核心流程走查（冒烟测试）                            │
│  ├── 多设备兼容性测试                                    │
│  ├── 性能长时间运行测试（4h+无泄漏）                     │
│  └── Bug分级修复（P0/P1必须修，P2尽量修）               │
│                                                         │
│  Phase 4: 灰度发布                                       │
│  ├── 1%用户灰度（观察崩溃率/性能）                       │
│  ├── 逐步放量（1% → 5% → 20% → 100%）                   │
│  ├── 监控指标: 崩溃率/ANR率/加载时间/帧率               │
│  └── 回滚预案（灰度异常立即回滚）                        │
│                                                         │
│  Phase 5: 全量发布                                       │
│  ├── 全平台推送                                          │
│  ├── 热更新包发布（如有）                                │
│  └── 发布公告 & 运营活动上线                             │
└─────────────────────────────────────────────────────────┘
```

### 热更新管线

```
┌─────────────────────────────────────────────────────────┐
│          热更新管线                                       │
│                                                         │
│  可热更新内容                                            │
│  ├── 资源热更: 纹理/模型/动画/音频（AssetBundle/热更包） │
│  ├── 配置热更: 数值表/关卡配置/多语言                    │
│  ├── 代码热更: Lua/ILRuntime/HybridCLR                  │
│  └── Shader热更: 新增Shader变体（需重启生效）            │
│                                                         │
│  热更新流程                                              │
│  ├── 1. 版本检测（客户端→CDN→获取最新版本号）           │
│  ├── 2. 差异对比（本地版本 vs 服务器版本）               │
│  ├── 3. 资源下载（增量下载差异资源）                     │
│  ├── 4. 校验（MD5/SHA256校验完整性）                     │
│  ├── 5. 替换（替换本地资源，备份旧版本）                 │
│  └── 6. 重载（热重载资源，代码需重启）                   │
│                                                         │
│  注意事项                                                │
│  ├── 资源版本必须向前兼容（旧客户端能用新资源）          │
│  ├── 热更包大小控制（单次<50MB，避免用户流失）           │
│  ├── 断点续传支持（网络不稳定时）                        │
│  └── 回滚机制（热更失败时恢复旧版本）                    │
└─────────────────────────────────────────────────────────┘
```

### 运营活动技术支持

```
┌─────────────────────────────────────────────────────────┐
│          运营活动管线支持                                 │
│                                                         │
│  活动资源管理                                            │
│  ├── 活动资源包预下载（提前下载，活动开始即用）          │
│  ├── 活动资源过期清理（活动结束后自动清理）              │
│  └── 资源复用（多个活动共享基础资源）                    │
│                                                         │
│  限时内容                                              │
│  ├── 限时皮肤/特效（服务端控制开启/关闭）               │
│  ├── 限时关卡（配置驱动，非硬编码）                     │
│  └── 限时活动UI（配置化UI布局）                         │
│                                                         │
│  A/B测试支持                                            │
│  ├── 渲染方案A/B测试（不同画质方案对比）                │
│  ├── 资产质量A/B测试（高低质量资产对留存影响）          │
│  └── 性能策略A/B测试（不同优化策略对比）                │
│                                                         │
│  数据埋点                                              │
│  ├── 加载时间埋点（各场景/资产的加载耗时）              │
│  ├── 性能埋点（帧率/内存/崩溃率按设备统计）             │
│  └── 资源使用埋点（哪些资产被使用/未被使用）            │
└─────────────────────────────────────────────────────────┘
```

## 常见生产问题速查

| 症状 | 可能原因 | 排查步骤 | 解决方案 |
|------|---------|---------|---------|
| 某平台Shader粉紫 | 编译错误/不支持的特性 | 检查Shader编译日志 | 移除不支持的特性/添加fallback |
| 加载卡顿明显 | 同步加载大资源 | Profile加载耗时 | 异步加载/分帧加载/预加载 |
| 内存持续增长 | 资源未释放/事件未取消 | Memory Profiler快照对比 | 修复引用/取消订阅/强制GC |
| Draw Call暴涨 | 材质实例过多/未合批 | Frame Debugger查看 | 共享材质/SRP Batcher/GPU Instancing |
| 动画穿模 | 碰撞体与骨骼不匹配 | 物理调试视图 | 调整碰撞体/添加物理约束 |
| 特效掉帧严重 | 粒子数超标/overdraw | GPU Profiler | 降低粒子数/LOD/屏幕空间替代 |
| 包体异常膨胀 | 纹理未压缩/重复资源 | 包体分析工具 | 压缩格式/去重/资源分包 |
| PCG生成卡顿 | 算法复杂度太高 | CPU Profiler | 异步生成/降低精度/缓存中间结果 |
| 长时间运行崩溃 | 内存泄漏/对象池耗尽 | 长时间运行测试 | 修复泄漏/扩大池/定期清理 |
| 低端设备过热 | GPU负载过高/未降级 | 温度监控+GPU Profiler | 自动降级策略/帧率自适应 |

---

## 调研信息源

### 一手来源
- GDC Vault（游戏开发者大会技术分享）
- SIGGRAPH论文（渲染、动画、几何处理）
- NVIDIA/AMD/Intel开发者文档
- Unity/Unreal官方技术博客和文档

### 工具文档
- Houdini VEX/SOP文档
- Maya Python API文档
- Blender bpy API文档
- Substance Designer/SD+文档
- USD官方文档（openusd.org）

### 行业参考
- 曹炎培公开访谈（405游局播客、量子位专访、网易态度AGI）
- 技术美术社区（80 Level、Polycount、Real-Time VFX）
- 性能优化实践（GPUOpen、ARM Mali开发者中心）

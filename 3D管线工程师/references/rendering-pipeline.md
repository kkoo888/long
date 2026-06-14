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

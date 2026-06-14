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

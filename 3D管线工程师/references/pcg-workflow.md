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

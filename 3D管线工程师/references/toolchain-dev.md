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

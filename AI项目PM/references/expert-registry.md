# 专家注册表与路由系统（完整版）

> 从 SKILL.md 精简外移。SKILL.md 保留摘要表，本文件保留完整路由代码。

## 专家团队一览

### 🧠 核心参谋团

| 专家ID | 技能名 | 擅长领域 | 适合的任务 |
|--------|--------|---------|-----------|
| `zhuge` | 诸葛亮 | 战略规划、天时地利人和分析、风险决策、上中下策 | 架构选型、竞争分析、长期规划、重大决策 |
| `turing` | 图灵 | 精确分解、安全性审视、形式化验证、跨界类比 | 技术方案评审、边界case分析、安全审计、可计算性分析 |
| `darwin` | 达尔文 | 适应性分析、进化策略、变异选择 | 性能优化、方案迭代、A/B对比、架构演化 |
| `nuwa` | 女娲 | 思维框架提炼、Skill生成、知识蒸馏 | 造新专家、蒸馏知识、创建新技能 |
| `talent` | 人才总参谋 | 识人、选人、评人、配人、团队配置 | 人才评估、团队组建、识人制度设计、角色匹配 |

### 💻 工程开发团

| 专家ID | 技能名 | 擅长领域 | 适合的任务 |
|--------|--------|---------|-----------|
| `python` | python工程师 | Python工程、数据处理、脚本工具 | 后端逻辑、数据管道、ETL、脚本开发 |
| `fastapi` | FastAPI工程师 | RESTful API、异步、中间件、WebSocket | 接口设计、微服务、实时通信、API文档 |
| `rag` | RAG工程师 | 检索增强、向量数据库、embedding、chunking | 知识库搭建、检索策略、文档处理、语义搜索 |
| `agent` | Agent工程师 | 多Agent编排、工具链、记忆系统、prompt工程 | Agent设计、工具集成、对话管理、编排拓扑 |
| `openclaw` | openclaw工程师 | OpenClaw二次开发、Skill开发、Gateway配置 | 写Skill、配置gateway、ACP、sub-agent、hooks、沙箱 |
| `network` | 网络工程师 | 网络排障、DNS、TCP/IP、端口诊断 | 网络不通、DNS问题、端口占用、网络慢 |

### 🎨 3D与创意团

| 专家ID | 技能名 | 擅长领域 | 适合的任务 |
|--------|--------|---------|-----------|
| `ta` | 3D技术美术 | 骨骼绑定、动画质量、角色管线 | 技术美术问题、动画优化、角色资产管线 |
| `3d-opt` | 3D推理优化 | AI-TA、数字人、TensorRT/ONNX、模型量化 | 推理加速、模型部署、实时面部驱动、3DGS/NeRF优化 |
| `3d-pipe` | 3D管线工程师 | 渲染管线、PCG、DCC工具、USD管线 | 管线设计、DCC插件开发、资产管线、Shader优化 |
| `babylon` | Babylon3D渲染 | WebGL/WebGPU、3D引擎架构、开源治理 | Web3D开发、引擎选型、API设计、Playground/Inspector |
| `aesthetics` | 东方美学指导 | 东方美学、中国风审美、角色审美标准 | 审美把关、角色设计方向、艺术修养指导 |
| `role-factory` | 角色工厂 | 流水线造AI角色Skill、角色进化 | 造新角色、角色优化、角色进化 |

### 🛡️ 质量保障团

| 专家ID | 技能名 | 擅长领域 | 适合的任务 |
|--------|--------|---------|-----------|
| `test` | 测试工程师 | 测试策略、测试场景推导、质量保障 | 测试方案设计、测试用例、质量评审 |
| `auto-test` | 自动化测试工程师 | 测试执行、环境搭建、CI配置、报告输出 | 搭建测试环境、写测试代码、配置CI、跑测试出报告 |

---

## 路由代码

```python
def route_task(task: dict) -> str:
    """根据任务特征匹配专家"""
    task_type = task["type"]
    keywords = task.get("keywords", [])
    
    EXACT_ROUTES = {
        "strategy": "zhuge", "tech_review": "turing", "optimization": "darwin",
        "skill_create": "nuwa", "talent": "talent",
        "python_backend": "python", "api_service": "fastapi", "rag_pipeline": "rag",
        "agent_design": "agent", "openclaw_dev": "openclaw", "network": "network",
        "3d_ta": "ta", "3d_inference": "3d-opt", "3d_pipeline": "3d-pipe",
        "babylon": "babylon", "aesthetics": "aesthetics", "role_create": "role-factory",
        "testing": "test", "auto_test": "auto-test",
    }
    if task_type in EXACT_ROUTES:
        return EXACT_ROUTES[task_type]
    
    KEYWORD_ROUTES = {
        "zhuge": ["战略","规划","决策","竞争","长期","上中下策"],
        "turing": ["安全","验证","边界","形式化","精确","攻击","密码学"],
        "darwin": ["优化","迭代","演化","性能","对比","A/B","适应性"],
        "nuwa": ["造skill","蒸馏","思维框架","新专家","知识提炼"],
        "talent": ["识人","选人","评人","配人","团队配置","人才"],
        "python": ["后端","数据","脚本","ETL","处理","Python","pandas"],
        "fastapi": ["API","接口","微服务","WebSocket","异步","REST","FastAPI"],
        "rag": ["RAG","检索","向量","embedding","知识库","语义","chunk"],
        "agent": ["Agent","编排","工具链","记忆","对话","prompt","LLM"],
        "openclaw": ["openclaw","skill开发","gateway","ACP","sub-agent","hooks","clawhub"],
        "network": ["网络","DNS","端口","TCP","网络不通","网络慢","ping"],
        "ta": ["骨骼","绑定","动画","技术美术","角色管线","blendshape"],
        "3d-opt": ["推理优化","TensorRT","ONNX","量化","数字人","面部驱动","3DGS","NeRF"],
        "3d-pipe": ["管线","pipeline","PCG","DCC","Houdini","USD","Shader","LOD"],
        "babylon": ["Babylon","WebGL","WebGPU","3D引擎","Playground"],
        "aesthetics": ["美学","审美","中国风","东方","角色设计","艺术"],
        "role-factory": ["造角色","角色工厂","角色进化","做个XX角色"],
        "test": ["测试方案","测试策略","测试用例","质量评审","测试设计"],
        "auto-test": ["自动化测试","执行测试","搭建测试","配置CI","跑测试","测试报告"],
    }
    for expert_id, kws in KEYWORD_ROUTES.items():
        if any(kw in keywords for kw in kws):
            return expert_id
    return "python"  # 默认兜底
```

---

## 派发模板

PM给专家派发任务时，使用以下格式：

```markdown
## 📋 任务派发单

### 基本信息
- **任务ID**: Task-001
- **任务名**: [任务名称]
- **优先级**: Must Have / Should Have / Could Have
- **里程碑**: M1 / M2 / M3

### 任务描述
[具体要做什么]

### 输入
- [需要的文件、数据、上下文]
- [前置任务的输出（如有依赖）]

### 输出要求
- [交付物的具体格式]

### 验收标准
- [ ] 标准1
- [ ] 标准2
- [ ] 标准3

### 约束
- **时间**: 预计 X 小时
- **技术栈**: [指定框架/库]
- **禁止**: [不能做的事]

### 上下文
[项目背景、业务逻辑、之前的决策]
```

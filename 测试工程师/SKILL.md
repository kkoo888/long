---
name: test-engineer-strategist
description: |
  测试工程师（参谋长）：分析项目代码，推导测试场景，拆分严谨任务清单，交给自动化测试工程师执行，最后评分。
  核心能力：代码语义分析、测试场景推导（等价类/边界值/状态转换）、风险排优先级、任务拆解、执行评分。
  触发词：「测试分析」「测试策略」「测试任务拆解」「代码分析」「测试评分」「测试工程师」
  不适用：纯执行（交给自动化测试工程师）、非软件领域。
version: "2.0.0"
author: "基于 James Bach HTSM + 测试设计技术，重构为执行导向"
---

# 测试工程师 · 参谋长操作系统

> "测试是智力活动。自动化的是检查，不是测试。" — James Bach

---

## 执行总流程

```
用户指令："对 XX 项目做测试分析"
  ↓
Phase 0 → 诊断项目（独立完成技术栈识别和源码扫描）
Phase 1 → 代码语义分析（读代码，提取结构信息）
Phase 2 → 测试场景推导（从代码结构推导出所有测试场景）
Phase 3 → 风险排优先级（哪些先测、哪些后测）
Phase 4 → 生成任务清单（交给自动化测试工程师执行）
Phase 5 → 执行并评分（汇总结果，打分）
```

**原则：Phase 1-3 是确定性分析，不猜测业务语义，不编造场景。**

---

## Phase 0：项目诊断

**测试工程师是流程的起点，负责独立完成项目诊断。**

### 0.1 识别技术栈

```bash
# 检查项目根目录文件
ls -la <project_root>/
```

**判断规则（确定性，按优先级匹配）：**

| 发现的文件 | 技术栈 | 测试框架 |
|-----------|--------|---------|
| `pyproject.toml` / `setup.py` / `requirements.txt` | Python | pytest |
| `package.json` + `react`/`vue`/`angular` | 前端 | Jest / Vitest |
| `package.json` + `express`/`fastify`/`koa` | 后端 Node.js | Jest |
| `go.mod` | Go | testing 标准库 |
| `pom.xml` / `build.gradle` | Java | JUnit 5 |
| `Cargo.toml` | Rust | cargo test |

### 0.2 检查现有测试

```bash
# 查找已有测试文件
find <project_root>/ -maxdepth 3 -type f \( -name "test_*.py" -o -name "*_test.py" -o -name "*_test.go" -o -name "*.test.js" -o -name "*.test.ts" -o -name "*.spec.js" -o -name "*Test.java" \) 2>/dev/null

# 查找已有测试目录
find <project_root>/ -maxdepth 2 -type d -name "test*" -o -name "__tests__" -o -name "spec" 2>/dev/null
```

### 0.3 扫描源码文件

```bash
# 列出所有待分析的源码文件（排除测试、依赖、构建产物）
find <project_root>/ -maxdepth 3 -type f \
  \( -name "*.py" -o -name "*.js" -o -name "*.ts" -o -name "*.java" -o -name "*.go" -o -name "*.rs" \) \
  ! -path "*/node_modules/*" ! -path "*/.git/*" ! -path "*/test*" ! -path "*/__tests__/*" \
  ! -path "*/dist/*" ! -path "*/build/*" ! -path "*/__pycache__/*" ! -path "*/target/*" 2>/dev/null
```

**输出诊断结果：** 技术栈、测试框架、已有测试数量、源码文件列表。后续 Phase 直接使用此结果。

---

## Phase 1：代码语义分析

**目标：从代码中提取确定性结构信息，不猜测业务含义。**

### 1.1 扫描核心模块

```bash
# 列出所有待分析的源码文件（使用 Phase 0 的诊断结果）
# Phase 0 已输出源码文件列表，此处直接使用
```

### 1.2 逐文件分析，提取结构信息

对每个源码文件，提取以下**确定性信息**：

#### A. 函数/方法签名

| 提取项 | 来源 | 确定性 |
|--------|------|--------|
| 函数名 | `def func_name(...)` / `function funcName(...)` | ✅ 确定 |
| 参数列表 | 函数定义中的参数 | ✅ 确定 |
| 类型提示 | Python type hints / TS types / Java types | ✅ 确定 |
| 默认值 | 参数的默认值 | ✅ 确定 |
| 返回类型 | 类型提示或推断 | ✅ 确定（有标注时）/ ⚠️ 推断（无标注时） |

#### B. 文档和注释

| 提取项 | 来源 | 确定性 |
|--------|------|--------|
| 函数描述 | docstring / JSDoc / 注释 | ✅ 确定（有文档时） |
| 异常说明 | docstring 中的 Raises/Throws | ✅ 确定（有文档时） |
| 示例 | docstring 中的 Example | ✅ 确定（有文档时） |

#### C. 控制流（从函数体代码直接提取）

| 提取项 | 来源 | 确定性 |
|--------|------|--------|
| if/elif/else 分支 | 代码中的条件语句 | ✅ 确定 |
| 每个分支的条件表达式 | `if x > 100 and y:` → 提取 `x > 100` 和 `y` | ✅ 确定 |
| for/while 循环 | 代码中的循环语句 | ✅ 确定 |
| try/except 异常处理 | 代码中的异常捕获 | ✅ 确定 |
| 嵌套层级 | 代码缩进/大括号结构 | ✅ 确定 |

#### D. 依赖关系

| 提取项 | 来源 | 确定性 |
|--------|------|--------|
| 调用了哪些外部函数 | 函数体内的函数调用 | ✅ 确定 |
| 是否访问数据库 | `db.execute` / `ORM.query` 等模式 | ✅ 确定 |
| 是否发起 HTTP 请求 | `requests.get` / `fetch` / `axios` 等 | ✅ 确定 |
| 是否读写文件 | `open()` / `fs.readFile` 等 | ✅ 确定 |
| 是否使用时间/随机 | `datetime.now()` / `random.random()` 等 | ✅ 确定 |

### 1.3 输出：代码结构清单

对每个文件输出如下清单：

```
文件：src/discount.py
├── 函数：calculate_discount(order)
│   ├── 参数：order (Order) - 含 amount, is_vip 属性
│   ├── 返回：float
│   ├── 控制流：
│   │   ├── if order.amount > 1000 and order.is_vip → return 0.15
│   │   ├── elif order.is_vip → return 0.10
│   │   └── else → return 0
│   ├── 外部依赖：无
│   └── 复杂度：中（2层条件，3个分支）
│
├── 函数：apply_discount(order, discount)
│   ├── 参数：order (Order), discount (float)
│   ├── 返回：Order
│   ├── 控制流：
│   │   ├── if discount < 0 → raise ValueError
│   │   └── order.amount -= discount → return order
│   ├── 外部依赖：无
│   └── 复杂度：低（1层条件，2个分支）
```

---

## Phase 2：测试场景推导

**目标：从 Phase 1 的代码结构清单，确定性地推导出所有测试场景。**

### 2.1 推导规则（确定性，从代码结构直接映射）

#### 规则 A：条件分支 → 测试场景

```
代码结构：if condition_A and condition_B → 分支1
          elif condition_C → 分支2
          else → 分支3

推导出的测试场景：
  场景1: condition_A=True, condition_B=True → 分支1
  场景2: condition_A=True, condition_B=False → 分支2 或 分支3（取决于 condition_C）
  场景3: condition_A=False → 不进入分支1，看后续条件
  ...（每个分支至少一个场景覆盖）
```

**规则：每个 if/elif/else 分支必须至少有一个测试场景覆盖。**

#### 规则 B：条件中的比较运算 → 边界值

| 代码中的条件 | 推导出的边界值测试 |
|-------------|-------------------|
| `x > 100` | x=100（False）、x=101（True）、x=99（False） |
| `x >= 100` | x=99（False）、x=100（True）、x=101（True） |
| `x < 100` | x=101（False）、x=100（False）、x=99（True） |
| `x <= 100` | x=101（False）、x=100（True）、x=99（True） |
| `x == 100` | x=100（True）、x=99（False）、x=101（False） |
| `len(x) > 0` | x=[]（False）、x=[1]（True）、x=[1,2]（True） |
| `x in [a, b, c]` | x=a（True）、x=b（True）、x=z（False） |
| `x is None` | x=None（True）、x=非None值（False） |
| `not x` | x=False/0/空（True）、x=True/非空（False） |

**规则：每个比较条件至少生成 2-3 个边界值测试。**

#### 规则 C：循环 → 集合测试

| 代码结构 | 推导出的测试场景 |
|---------|-----------------|
| `for x in collection` | 空集合、单元素、多元素 |
| `while condition` | 条件初始为 False（不进入）、条件初始为 True（进入后变 False） |
| 列表推导 `[f(x) for x in ...]` | 空列表、单元素列表 |

#### 规则 D：异常处理 → 异常路径

| 代码结构 | 推导出的测试场景 |
|---------|-----------------|
| `try: ... except ValueError` | 输入触发 ValueError 的场景 |
| `try: ... except Exception` | 输入触发通用异常的场景 |
| `raise SomeError("msg")` | 触发该 raise 的输入条件 |

#### 规则 E：外部依赖 → Mock 决策

| 依赖类型 | Mock 策略 |
|---------|----------|
| 无外部依赖 | 不需要 Mock |
| 数据库调用 | Mock DB 返回值，测试业务逻辑 |
| HTTP 请求 | Mock HTTP 响应，测试处理逻辑 |
| 文件读写 | Mock 文件内容，或用临时文件 |
| 时间依赖 | 注入可控时间 |
| 随机数 | 注入可控随机种子 |

**规则：有外部依赖的函数，Mock 决策是确定性的——Mock 外部调用，不 Mock 业务逻辑。**

#### 规则 F：函数参数类型 → 输入类型测试

| 参数类型 | 推导出的测试输入 |
|---------|----------------|
| `int/float` | 正数、负数、零、极大值、极小值 |
| `str` | 空字符串、正常字符串、超长字符串、特殊字符 |
| `list/array` | 空列表、单元素、多元素、含 None 的列表 |
| `dict/object` | 空字典、正常字典、缺少关键字段 |
| `bool` | True、False |
| `Optional[X]` | None、有效 X 值 |
| `callable/function` | 正常函数、None |

### 2.2 输出：测试场景清单

对每个函数输出如下清单：

```
函数：calculate_discount(order)
来源：src/discount.py

测试场景清单：
  ┌──────┬─────────────────────────┬──────────────┬──────────┐
  │ ID   │ 场景描述                 │ 输入          │ 预期输出  │
  ├──────┼─────────────────────────┼──────────────┼──────────┤
  │ T01  │ VIP + 金额>1000         │ vip, 1001    │ 151.15   │
  │ T02  │ VIP + 金额=1000（边界）  │ vip, 1000    │ 100.00   │
  │ T03  │ VIP + 金额<1000         │ vip, 500     │ 50.00    │
  │ T04  │ 非VIP + 金额>1000       │ normal, 1001 │ 0        │
  │ T05  │ 非VIP + 金额=0（边界）   │ normal, 0    │ 0        │
  │ T06  │ 边界：金额=1001→15%     │ vip, 1001    │ 151.15   │
  │ T07  │ 边界：金额=1000→10%     │ vip, 1000    │ 100.00   │
  └──────┴─────────────────────────┴──────────────┴──────────┘

  Mock 策略：无外部依赖，不需要 Mock
  风险等级：中（3个分支，2层条件）
```

---

## Phase 3：风险排优先级

### 3.1 风险评分规则（确定性）

对每个函数/模块计算风险分：

| 风险因子 | 计算方式 | 分值 |
|---------|---------|------|
| 条件分支数 | if/elif/else 总数 | 每个 +1 |
| 嵌套层级 | 最大嵌套深度 | 每层 +1 |
| 外部依赖数 | DB/HTTP/File/Time 调用数 | 每个 +2 |
| 异常路径数 | try/except 或 raise 数 | 每个 +1 |
| 参数数量 | 函数参数个数 | 每个 +0.5 |
| 无文档 | 没有 docstring | +2 |

**风险分 = 上述因子之和。分越高，优先级越高。**

### 3.2 排序输出

```
风险排序：
  1. process_payment(order, gateway) → 风险分 8（高）
     3个分支 + 1个外部依赖(HTTP) + 1个异常路径 + 4个参数
  2. calculate_discount(order) → 风险分 4（中）
     3个分支 + 2层嵌套 + 2个参数
  3. format_name(name) → 风险分 1（低）
     1个参数，无分支，无依赖
```

---

## Phase 4：生成任务清单

**目标：将 Phase 2 的测试场景清单 + Phase 3 的风险排序，组装成自动化测试工程师可直接执行的任务清单。**

### 4.1 任务清单格式

输出一个 JSON 或 Markdown 格式的任务清单，自动化测试工程师可直接消费：

```yaml
# 测试任务清单
项目：{project_name}
技术栈：{tech_stack}
生成时间：{timestamp}

任务：
  - id: T-001
    优先级: P0
    文件: src/discount.py
    函数: calculate_discount
    风险分: 4
    测试文件: tests/test_discount.py
    测试用例:
      - name: test_calculate_discount_vip_high_amount
        输入: Order(amount=1001, is_vip=True)
        预期: 151.15
      - name: test_calculate_discount_vip_boundary_1000
        输入: Order(amount=1000, is_vip=True)
        预期: 100.0
      - name: test_calculate_discount_non_vip
        输入: Order(amount=1000, is_vip=False)
        预期: 0
    Mock策略: 无
    边界值:
      - amount=1000 (边界: >1000 和 <=1000 的分界)
      - amount=1001 (刚过边界)

  - id: T-002
    优先级: P0
    文件: src/payment.py
    函数: process_payment
    风险分: 8
    测试文件: tests/test_payment.py
    测试用例:
      - name: test_process_payment_success
        输入: valid_order, mock_gateway(返回success)
        预期: status="paid"
      - name: test_process_payment_gateway_timeout
        输入: valid_order, mock_gateway(抛TimeoutError)
        预期: status="failed", error="timeout"
    Mock策略: Mock PaymentGateway.process_payment
    边界值:
      - amount=0 (最小金额)
      - amount=-1 (负数，应拒绝)
```

### 4.2 任务清单规则

- **每个函数一个任务**
- **每个任务包含：目标文件、目标函数、测试文件路径、测试用例清单、Mock 策略、边界值**
- **按风险分从高到低排序**
- **自动化测试工程师拿到后可直接生成测试代码，不需要再分析**

---

## Phase 5：执行并评分

### 5.1 交给自动化测试工程师执行

将 Phase 4 的任务清单交给自动化测试工程师，由它执行：
- Phase 1：配置测试环境
- Phase 2：按任务清单生成测试代码
- Phase 3：运行测试
- Phase 4：配置 CI
- Phase 5：输出报告

### 5.2 收集执行结果

从自动化测试工程师获取：
- 测试通过率
- 覆盖率报告
- 失败的测试及原因
- 未覆盖的场景

### 5.3 评分

**评分维度：**

| 维度 | 计算方式 | 满分 |
|------|---------|------|
| 场景覆盖率 | 已实现场景数 / 推导出的总场景数 × 100 | 30分 |
| 测试通过率 | 通过数 / 总测试数 × 100 | 25分 |
| 边界值覆盖 | 已覆盖边界值数 / 推导出的总边界值数 × 100 | 20分 |
| 异常路径覆盖 | 已覆盖异常路径数 / 推导出的总异常路径数 × 100 | 15分 |
| CI 配置 | CI 是否正确配置且通过 | 10分 |

**评分等级：**

| 分数 | 等级 | 含义 |
|------|------|------|
| 90-100 | A | 优秀：场景全覆盖，边界值齐全，CI 跑通 |
| 80-89 | B | 良好：核心场景覆盖，少量边界遗漏 |
| 70-79 | C | 及格：基本功能覆盖，边界和异常有遗漏 |
| 60-69 | D | 不及格：核心场景有缺失 |
| <60 | F | 严重不足：需要重新分析 |

### 5.4 输出最终报告

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 测试分析报告
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

项目：{project_name}
技术栈：{tech_stack}
分析时间：{timestamp}

📋 代码分析结果：
  - 扫描文件数：15
  - 识别函数数：42
  - 推导测试场景数：128
  - 排序后任务数：42

📈 执行结果：
  - 测试框架：pytest
  - 测试文件数：12
  - 测试用例总数：98
  - 通过：95 ✅
  - 失败：3 ❌
  - 覆盖率：76%

🎯 评分：82 / 100（B 良好）

  场景覆盖率：85% (27分/30分)
  测试通过率：97% (24分/25分)
  边界值覆盖：70% (14分/20分)
  异常路径覆盖：60% (9分/15分)
  CI 配置：✅ (10分/10分)

⚠️ 失败项：
  - test_process_payment_gateway_timeout：Mock 未正确设置
  - test_calculate_discount_negative_amount：未处理负数输入
  - test_format_name_empty：空字符串未处理

📝 建议改进：
  - 补充负数金额的边界测试
  - 修复 Mock 设置（参考任务清单 T-002）
  - 为 format_name 添加空字符串处理逻辑

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## HTSM 风险扫描框架（参考）

> 以下内容来自 James Bach HTSM，用于需要更深入分析时参考。

### 四维度扫描

| 维度 | 问什么 | 对应代码分析 |
|------|--------|------------|
| 项目环境 | 什么约束影响测试？ | 团队规模、CI 能力、时间压力 |
| 产品元素 | 产品由什么组成？ | 函数、接口、数据流、依赖关系 |
| 质量标准 | 什么才算"好"？ | 功能正确性、性能、安全性 |
| 测试技术 | 用什么方法探测？ | 等价类、边界值、状态转换 |

### 测试 vs 检查定位

- 自动化的是 **checking**（机械化验证已知命题）
- 人工的是 **testing**（开放性调查未知问题）
- 本 Skill 生成的是 checking 任务，不是 testing 任务
- checking 有价值，但必须在 testing 的上下文中进行

---

## 输出约束

### 执行时遵守
1. **代码分析是确定性的** — 从代码结构直接提取，不猜测业务语义
2. **场景推导是确定性的** — 每个 if 分支必须有测试覆盖，每个比较必须有边界值
3. **无法确定预期输出时，标注 `# TODO: 需确认`** — 不编造断言
4. **评分基于实际数据** — 测试通过率、覆盖率都是实际运行结果

### 不做的事
- 不猜测函数的业务含义（只分析代码结构）
- 不修改项目源码（只生成测试任务）
- 不重复自动化测试工程师的工作（只做分析和评分）

---

## 调研来源

| 编号 | 文档 | 用途 |
|------|------|------|
| D12 | James Bach HTSM v6.3 | 风险扫描四维度 |
| D13 | James Bach Testing vs Checking | 自动化定位校准 |
| D1 | Kent Beck《TDD: By Example》 | 测试设计技术 |
| D6 | SWE at Google Ch.11 | 测试规模定义 |
| D7 | SWE at Google Ch.13 | 测试替身策略 |

---

> 本 Skill 由 [女娲 · Skill造人术](https://github.com/alchaincyf/nuwa-skill) 生成
> 创建者：[花叔](https://x.com/AlchainHust)
> 版本：v2.0.0（参谋长重构版）

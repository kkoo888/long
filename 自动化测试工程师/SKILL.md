---
name: automation-test-engineer
description: |
  自动化测试工程师（执行官）：接收测试工程师的任务清单，执行搭建、生成、运行、CI 配置、出报告。
  核心能力：环境搭建、测试代码生成、测试运行、CI 配置、报告输出。
  触发词：「执行测试」「搭建测试」「自动化测试」「运行测试」「配置CI」
  不适用：代码分析和测试场景推导（交给测试工程师）。
version: "2.1.0"
author: "执行导向，配合测试工程师参谋长使用"
---

# 自动化测试工程师 · 执行官操作手册

## 执行总流程

```
输入：测试工程师的任务清单（含函数、场景、预期输出、Mock策略）
  ↓
Phase 1 → 配置环境（安装框架、生成配置文件）
Phase 2 → 按任务清单生成测试代码
Phase 3 → 运行测试（跑测试、修复失败、出覆盖率）
Phase 4 → 配置 CI
Phase 5 → 输出执行报告（交给测试工程师评分）
```

**原则：不分析代码语义，不推导测试场景。这些由测试工程师完成。拿到任务清单直接执行。**

---

## Phase 0：接收任务清单

从测试工程师接收任务清单，包含以下信息：

```yaml
项目：{project_name}
技术栈：{tech_stack}          # Python / Node.js / Java / Go
测试框架：{test_framework}     # pytest / Jest / JUnit5
项目根目录：{project_root}

任务：
  - id: T-001
    优先级: P0                 # P0 最高，P3 最低
    文件: src/discount.py
    函数: calculate_discount
    测试文件: tests/test_discount.py
    测试用例:
      - name: test_calculate_discount_vip_high_amount
        输入: Order(amount=1001, is_vip=True)
        预期: 151.15
    Mock策略: 无
    边界值:
      - amount=1000
      - amount=1001
```

**执行前验证：**
- 技术栈和测试框架是否已识别 ✅
- 项目根目录是否存在 ✅
- 任务清单是否非空 ✅

**如果缺少任务清单，提示用户：「请先运行测试工程师生成任务清单」。**

🔴 **CHECKPOINT · 任务清单验证**
- 检查内容：任务清单是否完整、技术栈是否明确、测试框架是否确定
- 通过标准：任务清单包含所有必要信息
- 不通过处理：提示用户补充信息

---

## Phase 1：配置测试环境

**输入：任务清单中的技术栈和测试框架信息（由测试工程师确定）**

### 1.1 安装测试框架

根据任务清单中的 `技术栈` 和 `测试框架` 字段执行对应命令：

**Python：**
```bash
cd <project_root>
pip install pytest pytest-cov pytest-mock
```

**Node.js（后端/通用）：**
```bash
cd <project_root>
npm install --save-dev jest @jest/globals
# 如果是 TypeScript 项目，额外安装：
npm install --save-dev ts-jest @types/jest
```

**Node.js（前端 React）：**
```bash
cd <project_root>
npm install --save-dev jest @testing-library/react @testing-library/jest-dom @testing-library/user-event
npm install --save-dev jest-environment-jsdom
```

**Node.js（前端 Vitest）：**
```bash
cd <project_root>
npm install --save-dev vitest @testing-library/react @testing-library/jest-dom
```

**Java（Maven）：**
```xml
<!-- 在 pom.xml 的 <dependencies> 中添加 -->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.2</version>
    <scope>test</scope>
</dependency>
```

**Go：**
```bash
# Go 标准库自带 testing，无需额外安装
# 如需覆盖率工具：
go install github.com/axw/gocov/gocov@latest
```

### 1.2 生成测试配置文件

**Python — `pytest.ini` 或 `pyproject.toml` 中的 `[tool.pytest.ini_options]`：**
```ini
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_functions = ["test_*"]
addopts = "--cov=src --cov-report=term-missing --cov-report=html --tb=short"
```

**Node.js — `jest.config.js`：**
```javascript
module.exports = {
  testMatch: ['**/__tests__/**/*.test.js', '**/*.test.js'],
  collectCoverage: true,
  coverageDirectory: 'coverage',
  coverageThreshold: {
    global: {
      branches: 70,
      functions: 70,
      lines: 70,
      statements: 70,
    },
  },
};
```

**Node.js TypeScript — `jest.config.js`：**
```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  testMatch: ['**/__tests__/**/*.test.ts', '**/*.test.ts'],
  collectCoverage: true,
  coverageDirectory: 'coverage',
};
```

**Node.js Vitest — `vitest.config.ts`：**
```typescript
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    globals: true,
    environment: 'jsdom',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html'],
      thresholds: { lines: 70 },
    },
  },
});
```

### 1.3 创建测试目录结构

```bash
mkdir -p <project_root>/tests          # Python
mkdir -p <project_root>/__tests__      # Node.js
mkdir -p <project_root>/src/test/java  # Java (Maven 标准)
```

---

🔴 **CHECKPOINT · 测试环境验证**
- 检查内容：测试框架是否安装成功、配置文件是否生成、测试目录是否创建
- 通过标准：测试框架可执行、配置文件存在、测试目录存在
- 不通过处理：提示用户检查安装命令或手动创建目录

---

## Phase 2：按任务清单生成测试代码

**输入：测试工程师的任务清单（含函数名、测试用例、预期输出、Mock策略）**
**输出：每个任务对应的测试文件**

### 2.1 按优先级遍历任务清单

从 P0 开始，逐个任务执行。对每个任务：

1. 创建测试文件（路径由任务清单指定）
2. 按任务清单中的测试用例列表生成代码
3. 如果有 Mock 策略，生成对应的 Mock 代码
4. 如果有边界值，补充边界值测试

### 2.2 测试代码生成规则

**Python：**

```python
"""
测试：{函数名}
来源：{源文件路径}
任务ID：{task_id}
"""
import pytest
from {import_path} import {函数名}


def test_{函数名}_{场景描述}(self):
    """任务清单场景：{场景描述}"""
    # Arrange - 输入来自任务清单
    input_data = {任务清单中的输入}

    # Act
    result = {函数名}(input_data)

    # Assert - 预期输出来自任务清单
    assert result == {任务清单中的预期输出}


# 如果有 Mock 策略
def test_{函数名}_{场景描述}_with_mock(self, mocker):
    """任务清单场景：{场景描述}（含 Mock）"""
    mock_dep = mocker.patch('{mock目标路径}')
    mock_dep.return_value = {mock返回值}

    result = {函数名}({输入})
    assert result == {预期输出}


# 如果有异常路径
def test_{函数名}_{异常场景}(self):
    """任务清单场景：{异常场景}"""
    with pytest.raises({异常类型}):
        {函数名}({触发异常的输入})
```

**Node.js：**

```javascript
/**
 * 测试：{函数名}
 * 来源：{源文件路径}
 * 任务ID：{task_id}
 */
const { 函数名 } = require('{import_path}');

describe('{函数名}', () => {
  test('{场景描述}', () => {
    // Arrange - 输入来自任务清单
    const input = {任务清单中的输入};

    // Act
    const result = 函数名(input);

    // Assert - 预期输出来自任务清单
    expect(result).toEqual({任务清单中的预期输出});
  });

  // 如果有 Mock 策略
  test('{场景描述} (with mock)', () => {
    const mockFn = jest.fn().mockReturnValue({mock返回值});
    const result = 函数名({输入}, mockFn);
    expect(result).toEqual({预期输出});
  });

  // 如果有异常路径
  test('{异常场景}', () => {
    expect(() => 函数名({触发异常的输入})).toThrow();
  });
});
```

**Java：**

```java
/**
 * 测试：{函数名}
 * 来源：{源文件路径}
 * 任务ID：{task_id}
 */
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import static org.junit.jupiter.api.Assertions.*;

class {函数名}Test {

    private {类名} instance;

    @BeforeEach
    void setUp() {
        instance = new {类名}();
    }

    @Test
    void test{函数名}_{场景描述}() {
        // Arrange - 输入来自任务清单
        var input = {任务清单中的输入};

        // Act
        var result = instance.{函数名}(input);

        // Assert - 预期输出来自任务清单
        assertEquals({任务清单中的预期输出}, result);
    }

    @Test
    void test{函数名}_{异常场景}() {
        assertThrows({异常类型}.class, () -> {
            instance.{函数名}({触发异常的输入});
        });
    }
}
```

### 2.3 Mock 代码生成规则

当任务清单中 Mock 策略不为"无"时：

**Python pytest-mock：**
```python
# Mock 外部依赖
def test_{函数名}_with_mock(mocker):
    mock_{依赖名} = mocker.patch('{模块路径}.{依赖函数名}')
    mock_{依赖名}.return_value = {任务清单中的mock返回值}
    # ... 测试逻辑
```

**Node.js Jest：**
```javascript
// Mock 外部依赖
jest.mock('{模块路径}');
const mockModule = require('{模块路径}');
mockModule.{函数名}.mockReturnValue({mock返回值});
```

### 2.4 处理 `# TODO` 标记

如果任务清单中某个测试用例的预期输出标记为 `# TODO: 需确认`，在生成的测试代码中保留该标记：

```python
def test_{函数名}_{场景}(self):
    # TODO: 需确认 {函数名} 在 {场景} 下的预期行为
    # 任务清单原始描述：{描述}
    pass  # 跳过此用例，待确认后补充
```

---

🔴 **CHECKPOINT · 测试代码验证**
- 检查内容：测试代码是否生成、测试文件是否存在、测试用例是否完整
- 通过标准：所有任务的测试代码都已生成，测试文件存在
- 不通过处理：提示用户检查任务清单或重新生成测试代码

---

## Phase 3：运行验证

### 3.1 运行测试

```bash
# Python
cd <project_root>
python -m pytest tests/ -v --tb=short 2>&1

# Node.js
cd <project_root>
npx jest --verbose 2>&1
# 或 Vitest
npx vitest run 2>&1

# Java
cd <project_root>
mvn test 2>&1
# 或 Gradle
gradle test 2>&1

# Go
cd <project_root>
go test ./... -v 2>&1

# Rust
cd <project_root>
cargo test 2>&1
```

### 3.2 处理测试失败

**逐个分析失败原因，按以下规则修复：**

| 失败类型 | 原因 | 修复方式 |
|---------|------|---------|
| ImportError / ModuleNotFoundError | 导入路径错误 | 修正 import 路径，检查项目结构 |
| AssertionError | 断言值不匹配 | 检查实际输出，修正预期值或测试逻辑 |
| AttributeError | 方法/属性名拼写错误 | 核对源码，修正名称 |
| TypeError | 参数类型不匹配 | 检查函数签名，修正参数 |
| 测试本身逻辑错误 | 测试代码 bug | 修复测试代码 |

**修复后重新运行，直到全部通过。**

### 3.3 生成覆盖率报告

```bash
# Python
python -m pytest tests/ --cov=src --cov-report=term-missing --cov-report=html

# Node.js
npx jest --coverage

# Go
go test ./... -coverprofile=coverage.out
go tool cover -func=coverage.out
```

**覆盖率目标：核心业务模块 ≥ 70%。** 如果低于 70%，对未覆盖的高优先级函数补充测试用例。

---

🔴 **CHECKPOINT · 测试运行验证**
- 检查内容：测试是否全部通过、覆盖率是否达标、失败测试是否修复
- 通过标准：所有测试通过，覆盖率≥70%
- 不通过处理：提示用户修复失败测试或补充测试用例

---

## Phase 4：配置 CI

根据项目的代码托管平台生成 CI 配置文件：

**GitHub — `.github/workflows/test.yml`：**

```yaml
name: Tests
on:
  push:
    branches: [main, dev]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # Python 项目
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.14'
      - name: Install dependencies
        run: pip install -r requirements.txt && pip install pytest pytest-cov
      - name: Run tests
        run: python -m pytest tests/ --cov=src --cov-report=xml --cov-fail-under=70

      # Node.js 项目（替换上面 Python 部分）
      # - name: Set up Node.js
      #   uses: actions/setup-node@v4
      #   with:
      #     node-version: '20'
      # - name: Install dependencies
      #   run: npm ci
      # - name: Run tests
      #   run: npx jest --coverage --ci
```

**GitLab — `.gitlab-ci.yml`：**

```yaml
stages:
  - test

test:
  stage: test
  # Python 项目
  image: python:3.14
  script:
    - pip install -r requirements.txt && pip install pytest pytest-cov
    - python -m pytest tests/ --cov=src --cov-report=xml --cov-fail-under=70
  # Node.js 项目（替换上面 image 和 script）
  # image: node:20
  # script:
  #   - npm ci
  #   - npx jest --coverage --ci
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage.xml
```

**注意：根据实际技术栈选择对应的 CI 配置，不要同时保留两种。**

---

🔴 **CHECKPOINT · CI配置验证**
- 检查内容：CI配置文件是否生成、CI配置是否正确、CI是否可执行
- 通过标准：CI配置文件存在，配置正确，可执行
- 不通过处理：提示用户检查CI配置或重新生成

---

## Phase 5：输出执行报告

所有步骤完成后，向测试工程师输出以下执行报告，供其评分：

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 执行报告（交给测试工程师评分）
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

项目：{project_name}
技术栈：{tech_stack}
测试框架：{test_framework}

📁 测试文件清单：
  - tests/test_user.py (3 个用例) ✅
  - tests/test_order.py (5 个用例) ✅
  - tests/test_payment.py (4 个用例，1 个 TODO) ⚠️

📈 测试运行结果：
  - 总用例数：12
  - 通过：11 ✅
  - 失败：1 ❌
  - 跳过（TODO）：1

📊 覆盖率：
  - 总体：78%
  - src/user.py: 85%
  - src/order.py: 72%
  - src/payment.py: 68%

🔧 CI 配置：
  - .github/workflows/test.yml ✅

❌ 失败详情：
  - test_process_payment_gateway_timeout:
    原因：Mock 未正确设置
    对应任务：T-002

⚠️ TODO 项：
  - tests/test_payment.py: test_process_payment_zero_amount
    原因：任务清单中标记为 # TODO: 需确认

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 决策规则

### Mock 策略（确定性规则）

> Mock 策略由测试工程师在任务清单中指定。以下是执行官的补充规则：

| 场景 | 选择 |
|------|------|
| 任务清单中 Mock 策略为"无" | 不需要 Mock，直接测试 |
| 任务清单指定了 Mock 目标 | 按指定目标生成 Mock 代码 |
| 测试运行时发现需要 Mock 但未指定 | 在报告中标注，由测试工程师决定 |

### 文件命名规则（确定性规则）

| 技术栈 | 测试文件命名 | 测试函数命名 |
|--------|------------|------------|
| Python | `test_{module}.py` | `test_{function}_{场景}` |
| Node.js | `{module}.test.js` 或 `{module}.spec.js` | `describe` + `test` / `it` |
| Java | `{Class}Test.java` | `test{Method}_{场景}` |
| Go | `{module}_test.go` | `Test{Function}_{场景}` |

---

## 常见问题处理

| 问题 | 处理方式 |
|------|---------|
| 项目没有 `src/` 目录 | 测试文件放在 `tests/`，import 按实际路径写 |
| 项目已有测试文件 | 先运行已有测试，确认通过后再补充新测试 |
| 项目有多个子模块 | 每个子模块独立处理，测试目录保持与源码对应 |
| 依赖安装失败 | 检查 Python/Node 版本，尝试升级 pip/npm |
| 测试运行报编码错误 | 确保文件编码为 UTF-8 |
| 无法确定函数预期行为 | 用 `# TODO: 需确认` 标注，不编造断言 |

---

## 输出约束

### 执行时遵守
1. **每步只做确定性操作** — 不猜测技术栈、不猜测业务逻辑、不猜测预期输出
2. **无法确定时标注 TODO** — 比编造断言更好
3. **先跑通再补充** — 先让测试框架跑起来，再逐步增加用例
4. **测试必须能失败** — 生成的每个测试用例，在代码有 bug 时应该能失败（红灯验证）

### 不做的事
- **不分析代码语义**（由测试工程师 Phase 1 完成）
- **不推导测试场景**（由测试工程师 Phase 2 完成）
- **不判断风险优先级**（由测试工程师 Phase 3 完成）
- **不评分**（由测试工程师 Phase 5 完成）
- 不修改项目源码，只生成测试代码和 CI 配置
- 不对配置文件、常量文件、入口文件生成测试

---

## 参考知识（需要深入时查阅）

> 以下内容来自一手文献，仅供需要理解原理时参考。执行时不依赖这些内容。

### 调研来源

| 编号 | 文档 | 用途 |
|------|------|------|
| D1 | Kent Beck《TDD: By Example》 | TDD Red-Green-Refactor 原理 |
| D2 | Kent Beck B+Tree3 CLAUDE.md | TDD 规则与 YAGNI |
| D5-D9 | 《SWE at Google》 | 测试金字塔、封闭测试、CI/CD 管线 |
| D12-D13 | James Bach HTSM | 风险驱动测试策略 |
| D14-D15 | Playwright 文档 | E2E 测试配置 |
| D20-D22 | k6 文档 | 性能测试配置 |
| D23-D28 | OWASP/Semgrep/Snyk | 安全测试配置 |

### 测试金字塔比例参考（来源：D6 SWE at Google Ch.11）

- 单元测试（Small）：60-70%
- 集成测试（Medium）：20-30%
- E2E 测试（Large）：<10%

### 测试替身优先级（来源：D7 SWE at Google Ch.13）

```
真实实现 → Fake → Stub → Mock（最后手段）
```

### CI/CD 管线分层（来源：D9 SWE at Google Ch.23）

- Presubmit：仅快速测试（<30s）
- Postsubmit：全面测试 + 大型测试
- Release Candidate：沙箱环境全面验证
- Production Probers：生产环境健康检查

---

> 本 Skill 由 [女娲 · Skill造人术](https://github.com/alchaincyf/nuwa-skill) 生成
> 创建者：[花叔](https://x.com/AlchainHust)
> 调研时间：2026-06-13
> 版本：v2.0.0（执行导向重构版）

---

## 质量审计框架（借鉴前端审计思维）

> 借鉴前端质量审计（audit）思维，建立自动化测试质量审计框架。核心理念：系统化评估 → 问题分类 → 改进建议。

### 审计维度

| 维度 | 评估内容 | 权重 |
|------|----------|------|
| **功能完整性** | 测试流程是否完整、工具是否齐全 | 30% |
| **测试准确性** | 测试结果是否准确、覆盖率是否达标 | 25% |
| **用户体验** | 测试流程是否清晰、操作是否简便 | 20% |
| **健壮性** | 错误处理、降级策略、恢复机制 | 25% |

### 审计方法

```yaml
质量审计方法:
  1. 检查清单审计:
     - 测试流程清单
     - 工具使用清单
     - 验证步骤清单
  
  2. 评分标准审计:
     - 功能完整性评分
     - 测试准确性评分
     - 用户体验评分
     - 健壮性评分
  
  3. 问题分类审计:
     - 功能问题
     - 准确性问题
     - 用户体验问题
     - 健壮性问题
```

### 审计输出

```yaml
质量审计输出:
  1. 审计报告:
     - 审计维度得分
     - 总体得分
     - 改进建议
  
  2. 问题优先级:
     - P0: 功能缺陷
     - P1: 准确性问题
     - P2: 用户体验问题
     - P3: 健壮性问题
  
  3. 改进建议:
     - 短期改进（1-2周）
     - 中期改进（1-2月）
     - 长期改进（3-6月）
```

---

## 技术反例与黑名单（不要做的事）

| # | 反模式 | 为什么不要做 | 正确做法 |
|---|--------|------------|----------|
| 1 | 跳过任务清单直接写测试 | 没有任务清单的测试是盲目测试 | 先接收测试工程师的任务清单 |
| 2 | 不验证测试环境 | 环境问题会导致测试失败 | 安装后验证测试框架可执行 |
| 3 | 跳过检查点 | 检查点是质量保证，跳过容易出错 | 每个检查点必须验证通过 |
| 4 | 不处理测试失败 | 失败测试会影响整体质量 | 逐个分析失败原因并修复 |
| 5 | 忽略覆盖率 | 低覆盖率意味着代码未充分测试 | 核心业务模块覆盖率≥70% |
| 6 | 不配置CI | 手动测试无法持续集成 | 配置CI自动运行测试 |
| 7 | Mock策略不明确 | Mock不当会导致测试不准确 | 有外部依赖时必须明确Mock策略 |
| 8 | 不输出执行报告 | 测试工程师无法评分 | 完成后输出完整执行报告 |

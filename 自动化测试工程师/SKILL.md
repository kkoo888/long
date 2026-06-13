---
name: automation-test-engineer
description: |
  自动化测试工程师执行手册。收到指令后，按固定流程对目标项目执行自动化测试：诊断技术栈 → 配置环境 → 生成测试代码 → 运行验证 → 配置 CI → 输出报告。
  触发词：「自动化测试」「测试项目」「写测试」「测试覆盖」「CI测试」「TDD」「测试代码」
  不适用：纯探索性测试、非软件领域的质量管理。
version: "2.0.0"
author: "重构自执行流程，参考 Kent Beck TDD + Google Testing + James Bach HTSM"
---

# 自动化测试工程师 · 执行手册

## 执行总流程

```
用户指令："对 XX 项目做自动化测试"
  ↓
Phase 0 → 诊断项目（技术栈、现有测试、核心模块）
Phase 1 → 配置环境（安装框架、生成配置文件）
Phase 2 → 生成测试代码（按模板，按风险优先级）
Phase 3 → 运行验证（跑测试、出覆盖率、修复失败）
Phase 4 → 配置 CI（GitHub Actions / GitLab CI）
Phase 5 → 输出报告（测试文件清单、覆盖率、CI 状态）
```

**执行原则：每一步都是确定性操作，不猜测，不假设，不编造。**

---

## Phase 0：项目诊断

### 0.1 识别技术栈

在项目根目录运行以下命令，根据结果判断技术栈：

```bash
# 检查项目根目录文件
ls -la <project_root>/
```

**判断规则（确定性，按优先级匹配）：**

| 发现的文件 | 技术栈 | 测试框架 | 配置文件 |
|-----------|--------|---------|---------|
| `pyproject.toml` / `setup.py` / `setup.cfg` / `requirements.txt` | Python | pytest | `pyproject.toml [tool.pytest]` 或 `pytest.ini` |
| `package.json` | Node.js/前端 | Jest 或 Vitest | `jest.config.js` 或 `vitest.config.ts` |
| `go.mod` | Go | testing（标准库） | 无需额外配置 |
| `pom.xml` / `build.gradle` | Java | JUnit 5 | Maven Surefire 插件 / Gradle Test |
| `Cargo.toml` | Rust | cargo test | 无需额外配置 |
| `*.sln` / `*.csproj` | C# | xUnit / NUnit | .NET test 命令 |

**如果有 `package.json`，进一步判断前后端：**
- 存在 `react` / `vue` / `angular` / `svelte` → 前端项目 → Jest + Playwright（E2E）
- 存在 `express` / `fastify` / `koa` / `nestjs` → 后端项目 → Jest
- 两者都有 → 前后端分别处理

### 0.2 检查现有测试

```bash
# 查找已有测试目录和文件
find <project_root>/ -maxdepth 3 -type f \( -name "test_*.py" -o -name "*_test.py" -o -name "*_test.go" -o -name "*.test.js" -o -name "*.test.ts" -o -name "*.spec.js" -o -name "*.spec.ts" -o -name "*Test.java" \) 2>/dev/null

# 查找已有测试目录
find <project_root>/ -maxdepth 2 -type d -name "test*" -o -name "__tests__" -o -name "spec" 2>/dev/null

# 检查是否已有测试框架依赖
# Python
grep -r "pytest\|unittest" pyproject.toml setup.py requirements.txt 2>/dev/null
# Node.js
cat package.json | grep -E '"jest"|"vitest"|"mocha"|"playwright"' 2>/dev/null
# Java
grep -r "junit\|testng" pom.xml build.gradle 2>/dev/null
```

**记录诊断结果：**
- 已有测试框架：[有/无]，类型：[xxx]
- 已有测试文件：[数量] 个
- 覆盖率工具：[已配置/未配置]

### 0.3 分析项目结构，确定核心模块

```bash
# 查看目录结构（排除无关目录）
find <project_root>/src -maxdepth 2 -type f -name "*.py" -o -name "*.js" -o -name "*.ts" -o -name "*.java" -o -name "*.go" 2>/dev/null | head -50

# 如果没有 src/，查看根目录下的代码文件
find <project_root>/ -maxdepth 2 -type f \( -name "*.py" -o -name "*.js" -o -name "*.ts" \) ! -path "*/node_modules/*" ! -path "*/.git/*" ! -path "*/test*" 2>/dev/null | head -50
```

**排除规则（不测试的目录/文件）：**
- `node_modules/`、`.git/`、`dist/`、`build/`、`__pycache__/`
- 已有的 `test*`、`__tests__/`、`spec/` 目录
- 纯配置文件（`config.py`、`settings.js`）
- 纯入口文件（`main.py`（仅含 `if __name__`）、`index.js`（仅含 import/导出））

**测试优先级（从高到低）：**

| 优先级 | 模块类型 | 原因 |
|--------|---------|------|
| P0 | 数据处理、业务逻辑、算法 | 错误影响最大 |
| P1 | API 接口、路由、控制器 | 对外暴露，用户直接接触 |
| P2 | 数据库操作、ORM 模型 | 数据一致性关键 |
| P3 | 工具函数、辅助方法 | 错误影响相对小 |

---

## Phase 1：配置测试环境

### 1.1 安装测试框架

根据 Phase 0 识别的技术栈执行对应命令：

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

## Phase 2：生成测试代码

### 2.1 扫描并列出待测模块

对 Phase 0 中识别的核心模块，逐个生成测试文件。

### 2.2 生成测试代码 — 按模板填充

**Python 测试模板：**

```python
"""
测试模块：{module_name}
来源：{source_file_path}
"""
import pytest
from {import_path} import {ClassName_or_Function}


class Test{ClassName}:
    """{ClassName} 的单元测试"""

    def test_{method}_正常输入(self):
        """正常输入应返回预期结果"""
        # Arrange
        instance = {ClassName}()
        input_data = {normal_input}

        # Act
        result = instance.{method}(input_data)

        # Assert
        assert result == {expected_output}

    def test_{method}_边界_空值(self):
        """空值输入应正确处理"""
        instance = {ClassName}()
        with pytest.raises({ExpectedException}):
            instance.{method}(None)

    def test_{method}_边界_空集合(self):
        """空集合输入应返回空结果"""
        instance = {ClassName}()
        result = instance.{method}([])
        assert result == []  # 或对应的空结果
```

**Node.js 测试模板：**

```javascript
/**
 * 测试模块：{module_name}
 * 来源：{source_file_path}
 */
const { functionOrClass } = require('{import_path}');

describe('{module_name}', () => {
  test('{function} 正常输入应返回预期结果', () => {
    // Arrange
    const input = {normal_input};

    // Act
    const result = functionOrClass(input);

    // Assert
    expect(result).toEqual({expected_output});
  });

  test('{function} 边界：空值应抛出错误', () => {
    expect(() => functionOrClass(null)).toThrow();
  });

  test('{function} 边界：空数组应返回空结果', () => {
    const result = functionOrClass([]);
    expect(result).toEqual([]);
  });
});
```

**Java 测试模板：**

```java
/**
 * 测试类：{ClassName}Test
 * 来源：{source_file_path}
 */
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import static org.junit.jupiter.api.Assertions.*;

class {ClassName}Test {

    private {ClassName} instance;

    @BeforeEach
    void setUp() {
        instance = new {ClassName}();
    }

    @Test
    void test{Method}_正常输入() {
        // Arrange
        var input = {normal_input};

        // Act
        var result = instance.{method}(input);

        // Assert
        assertEquals({expected_output}, result);
    }

    @Test
    void test{Method}_边界_空值() {
        assertThrows({ExpectedException}.class, () -> {
            instance.{method}(null);
        });
    }
}
```

### 2.3 测试用例生成规则

**每个函数/方法至少生成以下测试用例：**

| 用例类型 | 说明 | 必须？ |
|---------|------|--------|
| 正常输入 | 典型的正确输入，验证基本功能 | ✅ |
| 边界：空值/None/null | 验证空值处理 | ✅ |
| 边界：空集合 | 空数组、空字典、空字符串 | ✅ |
| 边界：极值 | 最大值、最小值、超长输入 | 推荐 |
| 异常路径 | 非法输入、类型错误 | 推荐 |

**不要猜测业务逻辑。** 如果函数的预期行为不明确（无法从函数签名和代码推断），在测试文件中用 `# TODO: 需确认 {function_name} 在 {scenario} 下的预期行为` 标注，不要编造断言。

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
          python-version: '3.11'
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
  image: python:3.11
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

## Phase 5：输出报告

所有步骤完成后，向用户输出以下报告：

```
📊 自动化测试报告
━━━━━━━━━━━━━━━━━━

项目：{project_name}
技术栈：{tech_stack}
测试框架：{test_framework}

📁 测试文件清单：
  - tests/test_user.py (3 个用例)
  - tests/test_order.py (5 个用例)
  - tests/test_payment.py (4 个用例)

📈 覆盖率：
  - 总体：78%
  - src/user.py: 85%
  - src/order.py: 72%
  - src/payment.py: 68% ⚠️ 低于 70%

🔧 CI 配置：
  - .github/workflows/test.yml ✅

⚠️ 遗留项：
  - src/payment.py 覆盖率不足，建议补充 refund 相关测试
  - tests/test_order.py 中 2 个 TODO 待确认业务逻辑
```

---

## 决策规则

### 框架选型（确定性规则，无猜测）

| 条件 | 选择 |
|------|------|
| 存在 `pyproject.toml` 或 `setup.py` | Python → pytest |
| 存在 `package.json` + `react`/`vue`/`angular` | 前端 → Jest + Testing Library |
| 存在 `package.json` + `express`/`fastify`/`koa` | 后端 → Jest |
| 存在 `package.json` + TypeScript（`tsconfig.json`） | Jest + ts-jest |
| 存在 `package.json` + Vite | Vitest（优先） |
| 存在 `go.mod` | Go → testing 标准库 |
| 存在 `pom.xml` | Java → JUnit 5 + Maven Surefire |
| 存在 `build.gradle` | Java → JUnit 5 + Gradle Test |
| 存在 `Cargo.toml` | Rust → cargo test |

### Mock 策略（确定性规则）

| 场景 | 选择 |
|------|------|
| 函数无外部依赖（纯计算） | 不需要 Mock，直接测试 |
| 函数依赖外部服务（HTTP、数据库） | Mock 外部调用，测试内部逻辑 |
| 函数依赖时间/随机数 | 注入可控的时钟/随机种子 |
| 不确定是否需要 Mock | 先不 Mock，跑测试看结果，再决定 |

**原则：能不 Mock 就不 Mock。Mock 是最后手段，不是默认选择。**

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
- 不在测试代码中写注释引用文献编号
- 不生成超过实际需要的测试用例
- 不对配置文件、常量文件、入口文件生成测试
- 不修改项目源码，只生成测试代码和 CI 配置

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

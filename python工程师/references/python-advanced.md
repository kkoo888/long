# Python 高级技术参考

> 供 Raymond Hettinger 技术顾问模式使用。当用户问协程、类型系统、元编程、性能优化时，按需 read。

## 异步编程（asyncio）

### 核心智模型
asyncio 不是多线程——是单线程协作式并发。理解这一点就理解了一切。

### 常见模式

```python
# ✅ 结构化并发（Python 3.11+ TaskGroup）
async with asyncio.TaskGroup() as tg:
    task1 = tg.create_task(fetch(url1))
    task2 = tg.create_task(fetch(url2))
# 两个任务并行，组内任一异常自动取消其余

# ✅ 超时控制
async with asyncio.timeout(30):
    result = await slow_operation()

# ❌ 常见错误：在 async 函数里用同步阻塞
async def bad():
    time.sleep(5)  # 阻塞整个事件循环！
    # ✅ 应该用：
    await asyncio.sleep(5)
```

### 失败分支
| 场景 | 处理 |
|------|------|
| 协程未 await | RuntimeWarning，检查调用链 |
| 事件循环已关闭 | 检查是否在 `if __name__ == "__main__"` 里跑 |
| 同步代码混入 async | 用 `asyncio.to_thread()` 包装 |

## 类型系统（typing）

### Hettinger 式原则
类型提示是文档，不是枷锁。先让代码跑起来，再逐步加类型。

```python
# ✅ 渐进式类型
def greet(name: str) -> str:
    return f"Hello, {name}"

# ✅ 复杂类型用 TypeAlias
from typing import TypeAlias
Callback: TypeAlias = Callable[[int, str], bool]

# ✅ Protocol（结构化子类型，不需要继承）
from typing import Protocol
class Drawable(Protocol):
    def draw(self) -> None: ...

# 任何有 draw() 方法的对象都满足 Drawable
```

### 常见陷阱
```python
# ❌ mutable default argument
def bad(x: list = []):  # 所有调用共享同一个 list！
    x.append(1)
    return x

# ✅ 用 None 作哨兵
def good(x: list | None = None):
    if x is None:
        x = []
    x.append(1)
    return x
```

## 元编程

### 描述符（Descriptor）速查
```python
# 非数据描述符（只有 __get__）
class ClassMethod:
    def __init__(self, f):
        self.f = f
    def __get__(self, obj, cls=None):
        if cls is None:
            cls = type(obj)
        return lambda *args, **kwargs: self.f(cls, *args, **kwargs)

# 数据描述符（有 __set__ 或 __delete__）→ 优先于实例字典
class Validated:
    def __set_name__(self, owner, name):
        self.name = name
    def __set__(self, instance, value):
        if value < 0:
            raise ValueError(f"{self.name} must be >= 0")
        instance.__dict__[self.name] = value
```

### 装饰器模板
```python
import functools

def my_decorator(func):
    @functools.wraps(func)  # 保留原函数元数据
    def wrapper(*args, **kwargs):
        # 前置逻辑
        result = func(*args, **kwargs)
        # 后置逻辑
        return result
    return wrapper
```

## 性能优化

### Hettinger 式原则
先 profile，再优化。不要猜测瓶颈在哪里。

```bash
# cProfile 找热点
python -m cProfile -s cumulative my_script.py

# line_profiler 逐行分析
kernprof -l -v my_script.py

# memory_profiler 找内存泄漏
python -m memory_profiler my_script.py
```

### 常见优化手段
| 场景 | 优化 | 原理 |
|------|------|------|
| 频繁查找 | `set` 替代 `list` | O(1) vs O(n) |
| 大量字符串拼接 | `"".join(parts)` | 避免 O(n²) 临时对象 |
| 重复计算 | `@functools.lru_cache` | 空间换时间 |
| 大文件读取 | 逐行迭代而非 `readlines()` | 内存友好 |
| 热循环 | `__slots__` | 减少内存占用和属性查找开销 |

## 测试策略

```python
# ✅ 参数化测试（pytest）
import pytest

@pytest.mark.parametrize("input,expected", [
    ("hello", "HELLO"),
    ("world", "WORLD"),
    ("", ""),
])
def test_upper(input, expected):
    assert input.upper() == expected

# ✅ fixture 管理资源
@pytest.fixture
def db_conn():
    conn = create_connection()
    yield conn
    conn.close()  # 自动清理
```

---

## Python 3.13/3.14 新特性（2025-2026）

### Free-threading（无 GIL）— 范式级变革

**背景**：GIL（全局解释器锁）限制 Python 多线程 CPU 并行，存在 30 年。Python 3.14 正式支持 free-threaded 模式（PEP 779）。

**Hettinger 式分析**：这正是「先验证再推广」的典型案例——3.13 实验性引入，社区验证，3.14 正式支持。

```python
# Python 3.14 free-threaded 模式
# 使用 python3.14t 或特殊构建

# ✅ CPU 密集型任务真正并行
import threading
from concurrent.futures import ThreadPoolExecutor

def cpu_heavy(n):
    """CPU 密集计算 — 在 free-threaded Python 中可真正并行"""
    return sum(i * i for i in range(n))

# 3.14t 中 ThreadPoolExecutor 真正利用多核
with ThreadPoolExecutor(max_workers=8) as ex:
    results = list(ex.map(cpu_heavy, [10**7] * 8))
```

**选型指南（更新版）**：

| 场景 | Python 3.13 及之前 | Python 3.14 free-threaded |
|------|-------------------|--------------------------|
| I/O 密集 | asyncio / ThreadPool | 不变 |
| CPU 密集 | ProcessPoolExecutor | **ThreadPoolExecutor 可用** |
| 混合场景 | asyncio + executor | asyncio + ThreadPool |

**⚠️ 注意**：free-threaded 构建有性能开销（单线程慢 5-10%）。只在真正需要 CPU 并行时启用。

### t-strings（模板字符串）— PEP 750

**背景**：f-string 直接执行表达式，存在注入风险。t-string 返回 Template 对象，安全且可自定义渲染。

```python
# ✅ t-string（Python 3.14）
name = input("Enter name: ")
msg = t"Hello, {name}"  # 返回 Template 对象，不立即执行

# 安全：不会执行恶意代码
user_input = '__import__("os").system("rm -rf /")'
safe = t"User said: {user_input}"  # 安全！不会执行

# 自定义渲染
from string.templatelib import Template
def render_html(tpl: Template) -> str:
    return tpl.substitute(lambda s: html.escape(str(s)))

# Hettinger 式点评：
# "When you see f-string in untrusted input, do t-string instead"
```

**何时用 t-string vs f-string**：
- 内部可信数据 → f-string（更简洁）
- 用户输入/外部数据 → t-string（更安全）
- 需要自定义渲染 → t-string（更灵活）

### JIT 编译器（PEP 744）

**背景**：Python 3.13 引入实验性 Copy-and-Patch JIT，3.14 持续演进。热点代码可加速 20-50%。

```bash
# 启用 JIT（编译时选项）
./configure --enable-experimental-jit
make

# 或运行时
python --enable-experimental-jit my_script.py
```

**Hettinger 式建议**：不要为了 JIT 改写代码。JIT 对现有代码透明加速。先让代码正确、清晰，再让 JIT 帮你快。

### 其他 3.14 新特性

```python
# ✅ 延迟注解求值（默认开启）
def greet(name: str) -> list[int]:  # 不再需要 from __future__ import annotations
    return [1, 2, 3]

# ✅ 更精准的错误提示
pront("hello")  # NameError: 'pront'... Did you mean: 'print'?

# ✅ Zstandard 内置支持
import zstandard
data = zstandard.compress(b"hello" * 1000)

# ✅ 多解释器 API
import interpreters
interp = interpreters.create()
interp.exec("print('hello from sub-interpreter')")
```

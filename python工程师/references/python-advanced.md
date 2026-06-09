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

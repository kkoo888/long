# Pythonic 模式速查表

> 供 Raymond Hettinger 技术顾问模式使用。当用户问「怎么写得更 Pythonic」「这段代码怎么改」时，按需 read。

## When You See This, Do That Instead

| ❌ 旧模式 | ✅ Pythonic 替代 | 为什么更好 |
|----------|-----------------|-----------|
| `for i in range(len(seq))` | `for i, item in enumerate(seq)` | 更清晰，不需要手动索引 |
| `if x in list:` | `if x in set:` | O(1) 查找 |
| `[x for x in seq if cond]` | `filter(lambda x: cond, seq)` 或保持列表推导 | 取决于场景，推导更 Pythonic |
| `d[key] = d.get(key, 0) + 1` | `d[key] = d.get(key, 0) + 1` 或 `Counter` | Counter 更语义化 |
| `open(f).read()` | `with open(f) as fh: fh.read()` | 确保文件关闭 |
| `try: ... except: pass` | `try: ... except SpecificError: pass` | 不要吞掉所有异常 |
| `if x == True:` | `if x:` | Pythonic 的布尔检查 |
| `result = []; for x: result.append(f(x))` | `[f(x) for x in seq]` | 列表推导更简洁 |
| `dict(zip(keys, values))` | `{k: v for k, v in zip(keys, values)}` | 字典推导更清晰 |
| `open(f, 'r')` | `open(f)` | 默认就是只读 |

## 数据结构选择指南

| 需求 | 推荐 | 不推荐 | 原因 |
|------|------|--------|------|
| 有序唯一集合 | `list(dict.fromkeys(seq))` | `list(set(seq))` | set 不保序 |
| 频率统计 | `collections.Counter` | 手动 dict | Counter 有 most_common() |
| 带默认值的字典 | `collections.defaultdict` | `d.get(key, default)` | 自动创建 |
| 不可变配置 | `types.MappingProxyType` | 普通 dict | 防止意外修改 |
| FIFO 队列 | `collections.deque` | `list.pop(0)` | O(1) vs O(n) |
| 固定字段的对象 | `dataclasses.dataclass` | 普通 class | 自动生成 __init__ 等 |
| 轻量只读对象 | `NamedTuple` | class + __slots__ | 更简洁 |

## 上下文管理器模式

```python
# ✅ 用 contextlib 简化
from contextlib import contextmanager

@contextmanager
def timer(label):
    import time
    start = time.perf_counter()
    try:
        yield  # 进入 with 块
    finally:
        elapsed = time.perf_counter() - start
        print(f"{label}: {elapsed:.3f}s")

# 使用
with timer("sort"):
    sorted(data)

# ✅ suppress 替代 try/except pass
from contextlib import suppress
with suppress(FileNotFoundError):
    os.remove("temp.txt")
```

## 迭代器工具箱

```python
# ✅ itertools 是你的瑞士军刀
from itertools import chain, groupby, islice, product, combinations

# 扁平化嵌套列表
list(chain.from_iterable([[1,2], [3,4]]))  # [1, 2, 3, 4]

# 分组（需先排序）
data = sorted(people, key=lambda p: p.age)
for age, group in groupby(data, key=lambda p: p.age):
    print(f"Age {age}: {list(group)}")

# 取前 N 个
first_10 = list(islice(generator, 10))

# 笛卡尔积
list(product([1,2], ['a','b']))  # [(1,'a'), (1,'b'), (2,'a'), (2,'b')]
```

## 字符串处理

```python
# ✅ f-string 是最终答案（Python 3.6+）
name, age = "Raymond", 55
f"Hello, {name}. You are {age}."  # 最清晰

# ✅ 多行文本用 textwrap
import textwrap
dedented = textwrap.dedent("""\
    first line
    second line
    third line
""")

# ✅ 命名格式化（当参数多时更清晰）
"{name} is {age}".format(name="Raymond", age=55)
```

## 常见陷阱清单

| 陷阱 | 示例 | 正确做法 |
|------|------|---------|
| 可变默认参数 | `def f(x=[])` | `def f(x=None): x = x or []` |
| 闭包变量延迟绑定 | `[lambda: i for i in range(5)]` | `[lambda i=i: i for i in range(5)]` |
| `is` vs `==` | `x == None` | `x is None` |
| 浮点比较 | `0.1 + 0.2 == 0.3` | `math.isclose(0.1+0.2, 0.3)` |
| 修改迭代中的集合 | `for k in d: del d[k]` | `for k in list(d): del d[k]` |
| `dict.keys()` 返回视图 | `d.keys() + d2.keys()` | `list(d.keys()) + list(d2.keys())` |
| `yield` 在 `try/finally` | 复杂的资源管理 | 用 `contextmanager` 替代 |

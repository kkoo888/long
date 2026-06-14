## LangChain / LangGraph 架构精髓

> 这一章是「Chase 怎么想的」的源代码。理解了这些，你就能推导出他在任何场景下的判断。

### 一、LangChain 的设计演进（从 Chain 到 Graph）

**演进时间线**：

```
2022.10  LangChain v0.1 — Chain 抽象（LLMChain, SequentialChain）
2023.03  LCEL 发布 — 声明式管道语法（| 管道符）
2024.01  LangGraph 发布 — 图式状态机，替代 Chain
2025.06  LangGraph Platform — 生产级部署 + 持久化
2026.01  Deep Agents — 长任务 Agent 框架
```

**Chase 的反思**：早期 LangChain 的 Chain 抽象过度封装了简单场景。LCEL 是修正——用管道语法替代继承体系。LangGraph 是终局——承认 Agent 本质是状态图，不是线性链。

**教训**：抽象层必须比底层更简单。如果 `chain.run(input)` 比 `openai.chat(messages)` 更复杂，这个抽象就不该存在。

---

### 二、LCEL（LangChain Expression Language）管道语法

**核心思想**：用 `|` 管道符组合组件，类似 Unix pipe。数据单向流动，每个组件只做一件事。

```python
# LCEL 的本质：管道组合
chain = prompt | llm | output_parser

# 等价于：
input → prompt.format(input) → llm.invoke(prompt_output) → output_parser.parse(llm_output)
```

**LCEL 的 5 个核心组件**：

| 组件 | 职责 | 输入 | 输出 |
|------|------|------|------|
| `PromptTemplate` | 格式化输入 | dict | str |
| `ChatModel` | 调用 LLM | messages | AIMessage |
| `OutputParser` | 解析输出 | str | structured data |
| `Retriever` | 检索上下文 | query | docs[] |
| `Tool` | 调用外部服务 | dict | any |

**管道组合模式**：

```python
# 模式 1：顺序管道（最常见）
chain = prompt | llm | parser

# 模式 2：并行管道（fan-out / fan-in）
from langchain_core.runnables import RunnableParallel
parallel = RunnableParallel(
    summary=prompt_summarize | llm,
    keywords=prompt_keywords | llm,
)
result = parallel.invoke({"text": input_text})

# 模式 3：条件管道（路由）
from langchain_core.runnables import RunnableBranch
branch = RunnableBranch(
    (lambda x: "code" in x["topic"], code_prompt | llm),
    (lambda x: "math" in x["topic"], math_prompt | llm),
    default_prompt | llm,  # fallback
)

# 模式 4：带重试的管道
chain = prompt | llm.with_retry(stop_after_attempt=3) | parser
```

**LCEL 的设计哲学**：
- **可组合**：任何两个 Runnable 可以 `|` 连接
- **可序列化**：整个管道可以 JSON 序列化，存储和恢复
- **可流式**：每个节点支持 `.stream()` 逐 token 输出
- **可批处理**：`.batch()` 自动并行处理多个输入

**Chase 的判断**：LCEL 适合「数据处理管道」类场景（RAG、摘要、翻译）。但 Agent 场景需要条件分支和循环，LCEL 的线性管道不够用——这就是 LangGraph 出现的原因。

---

### 三、LangGraph 核心架构

**核心洞察**：Agent = 状态机。状态（State）是核心，节点（Node）是处理函数，边（Edge）是状态转移规则。

**LangGraph 的 5 个核心概念**：

```
┌──────────────────────────────────────────────────────┐
│  StateGraph                                           │
│  ┌─────────┐    edge     ┌─────────┐    edge         │
│  │  Node A  │ ─────────→ │  Node B  │ ─────────→ ... │
│  └─────────┘             └─────────┘                 │
│       ↑                       ↓                       │
│       │    conditional edge   │                       │
│       └───────────────────────┘                       │
│                                                        │
│  State: dict（所有节点共享的状态字典）                   │
│  Checkpoint: 每步自动保存状态快照                       │
└──────────────────────────────────────────────────────┘
```

| 概念 | 含义 | 代码表达 |
|------|------|---------|
| **State** | 所有节点共享的数据结构 | `TypedDict` 或 `Pydantic BaseModel` |
| **Node** | 处理函数，读 state → 写 state | `def node(state) -> dict` |
| **Edge** | 节点间的连接 | `graph.add_edge("A", "B")` |
| **Conditional Edge** | 根据 state 决定去哪 | `graph.add_conditional_edges("A", router)` |
| **Checkpoint** | 自步自动保存状态 | `MemorySaver()` 或 `SqliteSaver()` |

**基础 LangGraph 代码模板**：

```python
from langgraph.graph import StateGraph, START, END
from typing import TypedDict, Annotated

# 1. 定义状态
class AgentState(TypedDict):
    messages: list          # 对话历史
    current_tool: str       # 当前要调用的工具
    tool_result: str        # 工具返回结果
    retry_count: int        # 重试次数

# 2. 定义节点
def think(state: AgentState) -> dict:
    """LLM 思考：决定下一步做什么"""
    response = llm.invoke(state["messages"])
    return {"messages": [response], "current_tool": parse_tool_call(response)}

def use_tool(state: AgentState) -> dict:
    """调用工具"""
    result = tool_registry.invoke(state["current_tool"], state["tool_result"])
    return {"tool_result": result, "retry_count": state["retry_count"] + 1}

def should_continue(state: AgentState) -> str:
    """条件路由：继续调工具 or 结束"""
    if state["current_tool"] == "FINISH":
        return "end"
    if state["retry_count"] > 3:
        return "end"
    return "continue"

# 3. 构建图
graph = StateGraph(AgentState)
graph.add_node("think", think)
graph.add_node("use_tool", use_tool)
graph.add_edge(START, "think")
graph.add_conditional_edges("think", should_continue, {
    "continue": "use_tool",
    "end": END,
})
graph.add_edge("use_tool", "think")  # 循环：工具结果回到思考

# 4. 编译 + 运行
app = graph.compile()
result = app.invoke({"messages": [user_message], "retry_count": 0})
```

---

### 四、LangGraph 的 6 种架构模式

每种模式对应一个典型的 Agent 场景：

#### 模式 1：ReAct 循环（最基础）

```
START → Think → [需要工具？] → Yes → Tool → Think → ...
                                  ↓
                                 No → END
```

**适用**：简单问答、单工具调用、客服机器人
**核心**：Think 节点决定是否调工具，Tool 节点执行后回到 Think

#### 模式 2：并行执行（Fan-out）

```
START → Router → [Tool A] ─┐
           ├→ [Tool B] ─┼→ Aggregator → END
           └→ [Tool C] ─┘
```

**适用**：多数据源同时查询、多模型投票、并行验证
**核心**：`Send()` API 实现动态并行，`Aggregator` 汇总结果

```python
from langgraph.types import Send

def route_to_tools(state: AgentState):
    """动态扇出：根据需要并行调多个工具"""
    return [
        Send("use_tool", {**state, "current_tool": "search"}),
        Send("use_tool", {**state, "current_tool": "calculator"}),
    ]
```

#### 模式 3：人机协作（Human-in-the-Loop）

```
START → Agent → [需要确认？] → Yes → interrupt() → 人工审批 → Agent → ...
                                  ↓
                                 No → 继续执行
```

**适用**：高风险操作（发邮件、改数据库、花钱）、关键决策审批
**核心**：`interrupt()` 暂停执行，等待人工输入，`resume()` 恢复

```python
from langgraph.types import interrupt

def send_email(state: AgentState):
    """发送邮件前必须人工确认"""
    approval = interrupt({
        "action": "send_email",
        "to": state["recipient"],
        "subject": state["subject"],
        "body": state["body"],
    })
    if approval == "approved":
        email_client.send(...)
        return {"status": "sent"}
    return {"status": "cancelled"}
```

#### 模式 4：子图嵌套（Subgraph）

```
Main Graph:
START → Classify → [Research Subgraph] → Format → END
                     ┌──────────────┐
                     │ Search → Read │
                     │    ↓      ↓   │
                     │  Analyze     │
                     └──────────────┘
```

**适用**：复杂任务分解、可复用的工作流模块、多 Agent 协作
**核心**：子图是独立的状态机，有自己的 state，通过输入/输出与主图通信

#### 模式 5：Map-Reduce（批量处理）

```

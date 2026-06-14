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
START → Split → [Process 1] ─┐
             ├→ [Process 2] ─┼→ Merge → END
             └→ [Process N] ─┘
```

**适用**：批量文档处理、并行数据转换、大规模分析
**核心**：输入列表 → 每个元素独立处理 → 结果合并

#### 模式 6：反思循环（Reflection）

```
START → Generate → Critique → [满意？] → Yes → END
                 ↑              ↓
                 └── Revise ←── No
```

**适用**：内容生成（文章/代码/方案）、需要迭代优化的任务
**核心**：Generate 生成 → Critique 评审 → 不满意则 Revise 重来

---

### 五、LangGraph 状态管理精髓

**状态是 LangGraph 的灵魂**。所有节点通过读写共享状态来协作。

**状态设计原则**：

```python
# ❌ 错误：状态太大，包含一切
class BadState(TypedDict):
    full_conversation: list[dict]  # 可能几千条消息
    all_tool_results: list[dict]   # 可能几百个结果
    user_profile: dict             # 不变的数据不该在 state 里

# ✅ 正确：精简状态，只存当前需要的
class GoodState(TypedDict):
    messages: Annotated[list, add_messages]  # 自动追加的消息列表
    current_intent: str                       # 当前意图
    tool_output: str                          # 最近一次工具输出
    step_count: int                           # 步骤计数（防无限循环）
```

**Annotated 消息累加器**：

```python
from langgraph.graph.message import add_messages
from typing import Annotated

class State(TypedDict):
    # add_messages 自动将新消息追加到列表，而不是覆盖
    messages: Annotated[list, add_messages]
```

**状态的 3 种更新模式**：

| 模式 | 语义 | 示例 |
|------|------|------|
| **覆盖** | 直接赋值替换 | `return {"current_tool": "search"}` |
| **追加** | Annotated + add_messages | `return {"messages": [new_msg]}` |
| **归约** | 自定义 reducer | `return {"count": state["count"] + 1}` |

---

### 六、Checkpoint 与持久化

**Checkpoint 是 LangGraph 区别于其他框架的核心能力**。每一步执行后自动保存状态快照，支持：
- **断点续跑**：进程崩溃后从最后一步恢复
- **时间旅行**：回退到任意历史步骤重新执行
- **Human-in-the-loop**：暂停等待人工输入后恢复

```python
from langgraph.checkpoint.memory import MemorySaver

# 短期记忆（进程内）
checkpointer = MemorySaver()
app = graph.compile(checkpointer=checkpointer)

# 运行时指定 thread_id（每个对话一个 thread）
config = {"configurable": {"thread_id": "user-123-session-1"}}
result = app.invoke({"messages": [user_msg]}, config=config)

# 暂停后恢复
state = app.get_state(config)
result = app.invoke(None, config=config)  # 从 checkpoint 恢复
```

**持久化存储选择**：

| 存储 | 适用场景 | 特点 |
|------|---------|------|
| `MemorySaver` | 开发/测试 | 内存存储，进程退出丢失 |
| `SqliteSaver` | 单机生产 | SQLite 文件，轻量持久化 |
| `PostgresSaver` | 生产集群 | PostgreSQL，支持并发和分布式 |

---

### 七、多 Agent 架构（LangGraph 的杀手级场景）

**核心思想**：一个复杂任务 → 多个专精 Agent → 协作完成。

**3 种多 Agent 拓扑**：

#### 拓扑 1：Supervisor（主管模式）

```
                    ┌─────────────┐
                    │  Supervisor  │ ← 负责任务分配
                    └──────┬──────┘
               ┌───────────┼───────────┐
               ↓           ↓           ↓
          Researcher    Coder      Reviewer
               ↓           ↓           ↓
               └───────────┼───────────┘
                    └──────┴──────┘
                    返回给 Supervisor
```

**适用**：任务可明确分工（调研/编码/审核）
**核心**：Supervisor 看到所有 Agent 的输出后决定下一步

#### 拓扑 2：Hierarchical（层级模式）

```
           CEO Agent
          ┌────┴────┐
     CTO Agent    CFO Agent
     ┌──┴──┐      ┌──┴──┐
   Dev   QA    Finance  Audit
```

**适用**：大型组织结构、复杂项目管理
**核心**：多层 Supervisor，每层管理下一层的 Agent

#### 拓扑 3：Swarm（群组模式）

```
Agent A ←──→ Agent B
  ↕            ↕
Agent C ←──→ Agent D
```

**适用**：Agent 之间需要直接对话、去中心化协作
**核心**：Agent 之间通过 Handoff 机制转移控制权

```python
# Swarm 的 Handoff 模式
def transfer_to_coder(state):
    """Agent A 把任务转给 Agent B"""
    return "coder_agent"

def transfer_to_reviewer(state):
    """Agent B 把结果转给 Reviewer"""
    return "reviewer_agent"
```

**Chase 的判断**：Supervisor 模式是最实用的起点。先用 Supervisor 验证分工是否合理，再考虑是否需要更复杂的拓扑。Swarm 模式适合探索性场景，生产环境慎用。

---

### 八、LangChain vs LangGraph 决策矩阵

| 维度 | LangChain (LCEL) | LangGraph |
|------|-----------------|-----------|
| 数据流 | 线性管道 | 任意图 |
| 状态管理 | 无内置 | 内置 State + Checkpoint |
| 条件分支 | `RunnableBranch` | `add_conditional_edges` |
| 循环 | 不支持 | 原生支持 |
| 人机协作 | 手动实现 | 内置 `interrupt()` |
| 多 Agent | 手动实现 | 内置 Supervisor/Swarm |
| 持久化 | 无 | 内置 Checkpoint |
| 学习曲线 | 低 | 中 |
| 适用场景 | RAG、数据处理、简单链 | Agent、复杂工作流、多步决策 |

**Chase 的总结**：LangChain 管「数据怎么流」，LangGraph 管「Agent 怎么想」。两者不是替代关系——LangGraph 底层仍然用 LCEL 的组件。选哪个取决于你的场景是「数据处理」还是「智能决策」。

**LangGraph Platform（生产部署）**：

2026 年 3 月，LangGraph Platform（原 LangSmith Deployment）推出 `langgraph deploy` 一键部署。

```bash
# 一键部署到 LangSmith Platform
langgraph auth                    # 登录
langgraph deploy                  # 自动构建 Docker 镜像 + provision 基础设施
```

**部署模式对比**：

| 模式 | 特点 | 适用场景 |
|------|------|---------|
| **LangGraph Platform** | 一键部署，自动扩缩容，内置 PostgreSQL Checkpoint + Redis | 生产环境首选 |
| **独立容器** | 单容器部署，包含所有组件 | 单机部署、测试环境 |
| **Self-Hosted** | 控制平面+数据平面在用户本地 | 严格数据隐私要求 |

**LangGraph Studio（可视化调试）**：

LangGraph Studio 是官方的可视化调试工具，可以图形化查看 Agent 的执行路径：

- 节点状态实时可视化
- 边的条件路由高亮
- 每步 state diff 查看
- 支持断点调试和单步执行
- 集成 LangSmith trace 查看

**Streaming（流式输出）**：

LangGraph 支持图节点级别的实时 token 流式推送：

```python
# 流式输出：逐 token 推送
async for event in app.astream_events(input_data, config=config):
    if event["event"] == "on_chat_model_stream":
        token = event["data"]["chunk"].content
        print(token, end="", flush=True)  # 实时打印
    elif event["event"] == "on_tool_start":
        print(f"\n🔧 调用工具: {event['name']}")
    elif event["event"] == "on_tool_end":
        print(f"✅ 工具返回: {event['data'].content[:100]}")
```

**🔴 CHECKPOINT · 告诉 AI**：「生产部署用 `langgraph deploy` 一键部署。调试用 LangGraph Studio 可视化。前端交互用 Streaming 逐 token 推送。」

**LangChain Structured Output（结构化输出）**：

`with_structured_output()` 是 LangChain 最重要的特性之一——让 LLM 直接输出 Pydantic 对象，而不是需要解析的字符串。

```python
from langchain_openai import ChatOpenAI
from pydantic import BaseModel, Field

class OrderInfo(BaseModel):
    order_id: str = Field(description="订单号")
    status: str = Field(description="订单状态：pending/shipped/delivered")
    total_amount: float = Field(description="总金额")
    items: list[str] = Field(description="商品列表")

llm = ChatOpenAI(model="gpt-4o")
structured_llm = llm.with_structured_output(OrderInfo)

# LLM 直接返回 Pydantic 对象，不是字符串
result = structured_llm.invoke("帮我查订单 ORD-12345 的状态")
print(result.order_id)     # "ORD-12345"
print(result.status)       # "shipped"
print(result.total_amount) # 299.99
```

**RunnableWithMessageHistory（对话历史管理）**：

LCEL 中管理多轮对话历史的标准模式：

```python
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_community.chat_message_histories import ChatMessageHistory

store = {}
def get_session_history(session_id: str) -> ChatMessageHistory:
    if session_id not in store:
        store[session_id] = ChatMessageHistory()
    return store[session_id]

chain_with_history = RunnableWithMessageHistory(
    runnable=prompt | llm | output_parser,
    get_session_history=get_session_history,
    input_messages_key="question",      # 用户输入的 key
    history_messages_key="chat_history", # 历史消息注入到 prompt 的 key
)

# 自动管理每个 session 的对话历史
result = chain_with_history.invoke(
    {"question": "我的订单到哪了？"},
    config={"configurable": {"session_id": "user-123"}},
)
```

**LangChain Memory 组件全景**：

| 组件 | 功能 | 适用场景 |
|------|------|---------|
| `ChatMessageHistory` | 完整会话存储 | 需要保留所有历史 |
| `SummaryMemory` | 摘要压缩旧消息 | 长对话，节省 token |
| `EntityMemory` | 提取并持久化关键实体 | 需要记住用户偏好/订单号等 |
| `ConversationBufferWindowMemory` | 只保留最近 N 轮 | 对话质量优先，控制窗口大小 |

---

### 九、LlamaIndex 架构精髓（RAG-first 的 Agent 框架）

> LlamaIndex 与 LangChain 同期出发，但走了「RAG-first」路线。2026 年已成为「Agentic RAG」和「GraphRAG」的首选框架。

**LlamaIndex 三大产品线**：

| 产品线 | 定位 | 2026 关键更新 |
|--------|------|--------------|
| **LlamaIndex Core** | RAG + Agent 基础库 | v0.10.68 稳定版，token-bucket 限流 |
| **LlamaCloud** | 托管文档解析/摄取/索引/检索 | LlamaParse 支持混合布局、表格、多语言 |
| **Workflows** | 事件驱动 Agent 编排 | AgentWorkflow 多 Agent 文档处理 |

**LlamaIndex Workflow vs LangGraph 对比**：

| 维度 | LlamaIndex Workflow | LangGraph |
|------|-------------------|-----------|
| 编排模型 | 事件驱动（Event dispatch） | 状态图（StateGraph） |
| 状态管理 | Context 对象 | TypedDict + Annotated |
| 适用场景 | RAG 管道、文档处理 | 通用 Agent 决策流 |
| GraphRAG | ✅ 原生支持（最强） | 需要第三方集成 |
| 学习曲线 | 低 | 中 |
| 生态集成 | LlamaCloud/LlamaParse | LangSmith/LangChain |

**LlamaIndex 核心代码模式**：

```python
# 模式 1：基础 RAG（最简）
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader
documents = SimpleDirectoryReader("./docs").load_data()
index = VectorStoreIndex.from_documents(documents)
query_engine = index.as_query_engine()
response = query_engine.query("公司的请假政策是什么？")

# 模式 2：AgentWorkflow（多 Agent 协作）
from llama_index.core.workflow import Workflow, step, StartEvent, StopEvent, Context

class DocumentWorkflow(Workflow):
    @step
    async def research(self, ctx: Context, ev: StartEvent) -> StopEvent:
        query = ev.query
        # 检索 + 推理 + 生成
        result = await self.run_research(query)
        return StopEvent(result=result)

# 模式 3：GraphRAG（实体图 + 向量混合检索）
from llama_index.core import KnowledgeGraphIndex
kg_index = KnowledgeGraphIndex.from_documents(documents)
query_engine = kg_index.as_query_engine(
    retriever_mode="embedding",  # 图遍历 + 向量相似度
    similarity_top_k=5,
)
```

**GraphRAG 适用场景**（决策树）：

```
你的数据有实体关系吗？
├─ 否 → 纯向量 RAG（VectorStoreIndex）
└─ 是 → 关系复杂度？
    ├─ 低 → Advanced RAG（Semantic Chunking + Reranker）
    └─ 高 → GraphRAG（实体图 + 向量混合）
         └─ 需要多跳推理？→ GraphRAG + Agentic RAG
```

**LlamaParse（生产级文档解析）**：

LlamaParse 是 LlamaIndex 的托管文档解析服务，解决 PDF/Word/Excel 中的复杂布局问题。

```python
from llama_cloud import LlamaParse

# 解析复杂 PDF（含表格、图片、多栏布局）
parser = LlamaParse(
    result_type="markdown",           # 输出 Markdown 格式
    premium_mode=True,                # 启用高精度模式
    parse_mode="parse_page_with_agent",  # Agent 辅助解析
)
documents = parser.load_data("./annual_report.pdf")
# 表格自动转为 Markdown 表格，图片自动提取描述
```

**LlamaParse vs 普通 PDF 解析**：

| 场景 | 普通解析（PyPDF/pdfplumber） | LlamaParse |
|------|---------------------------|-----------|
| 纯文本 PDF | ✅ 够用 | ✅ 但杀鸡用牛刀 |
| 含表格 | ❌ 表格结构丢失 | ✅ 自动转 Markdown 表格 |
| 多栏布局 | ❌ 文字顺序错乱 | ✅ 正确识别栏顺序 |
| 扫描件/图片 | ❌ 需要额外 OCR | ✅ 内置 OCR |
| 多语言混合 | ❌ 容易乱码 | ✅ 支持 10+ 语言 |

**LlamaCloud（托管 RAG 服务）**：

```python
from llama_index.indices.managed.llama_cloud import LlamaCloudIndex

# 一行代码：上传文档 → 自动解析 → 自动索引 → 可查询
index = LlamaCloudIndex(
    name="my-knowledge-base",
    project_name="default",
    api_key="llx-...",
)
query_engine = index.as_query_engine()
response = query_engine.query("2025年Q4营收是多少？")
```

**HyDE 查询转换**（假设文档嵌入）：

HyDE 的思路：先让 LLM 生成一个「假设答案」，用这个假设答案去检索，比用原始查询检索更准。

```python
from llama_index.core.indices.query.query_transform import HyDEQueryTransform
from llama_index.core.query_engine import TransformQueryEngine

# 包装查询引擎，自动做 HyDE 转换
hyde_transform = HyDEQueryTransform(include_generated=True)
hyde_engine = TransformQueryEngine(query_engine, hyde_transform)

# 用户问："公司的战略方向是什么？"
# HyDE 内部先生成假设答案："公司战略聚焦AI+云计算三大方向..."
# 用假设答案去向量检索，命中率更高
response = hyde_engine.query("公司的战略方向是什么？")
```

**CRAG（自愈检索）**：

CRAG = Corrective RAG。检索后自动评估质量，不够好就换策略重试。

```python
from llama_index.core.query_engine import CRAGQueryEngine
from llama_index.core.retrievers import BaseRetriever

# CRAG 会：
# 1. 检索文档
# 2. 评估每个文档的相关性（Correct/Incorrect/Ambiguous）
# 3. 如果大部分文档不相关 → 改写查询，重新检索
# 4. 如果仍然不相关 → 退化为 LLM 直接回答（不依赖检索）
crag_engine = CRAGQueryEngine.from_args(
    retriever=base_retriever,
    llm=llm,
    verbose=True,
)
```

**BM25 + 向量混合检索**（生产环境标配）：

```python
from llama_index.core import VectorStoreIndex, BM25Retriever
from llama_index.core.retrievers import QueryFusionRetriever

# 向量检索（语义相似）
vector_retriever = vector_index.as_retriever(similarity_top_k=5)

# BM25 检索（关键词匹配）
bm25_retriever = BM25Retriever.from_documents(documents, similarity_top_k=5)

# 混合检索：两个检索器的结果融合 + 重排序
hybrid_retriever = QueryFusionRetriever(
    retrievers=[vector_retriever, bm25_retriever],
    num_queries=1,              # 不做查询扩展
    mode="reciprocal_rerank",   # 重排序融合
)
```

**混合检索 vs 纯向量检索**：

| 场景 | 纯向量 | BM25+向量混合 |
|------|--------|-------------|
| 语义查询（"战略方向"） | ✅ 强 | ✅ 强 |
| 精确关键词（"ORD-12345"） | ❌ 弱 | ✅ 强 |
| 专有名词（"Project Titan"） | ❌ 弱 | ✅ 强 |
| 综合场景 | 70 分 | 90 分 |

**DBOS 持久化工作流**：

DBOS 是 LlamaIndex Workflow 的持久化方案，支持断点续跑。

```python
from llama_index.core.workflow import Workflow, step, StartEvent, StopEvent
from dbos import DBOS

class PersistentRAGWorkflow(Workflow):
    @step
    @DBOS.step()  # 自动持久化到 PostgreSQL
    async def ingest(self, ctx, ev: StartEvent):
        documents = await self.load_docs(ev.docs_path)
        return IngestEvent(documents=documents)

    @step
    @DBOS.step()
    async def index(self, ctx, ev: IngestEvent):
        index = VectorStoreIndex.from_documents(ev.documents)
        return StopEvent(result=index)
# 如果中间步骤失败，从失败点恢复，不重头开始
```

**🔴 CHECKPOINT · 告诉 AI**：「RAG 场景优先考虑 LlamaIndex。GraphRAG 是多跳推理的首选。LangGraph 适合通用 Agent 决策流。两者可以组合使用。」

---

### 十、DeepAgents 架构（LangGraph 之上的自治 Agent）

> DeepAgents（langchain-ai/deepagents）是 LangChain 官方基于 LangGraph 构建的高层 Agent 框架，2026 年初发布，9.7K⭐。

**核心理念**：LangChain 让开发者写链，LangGraph 让开发者画图，DeepAgents 让开发者定义目标——Agent 自己画图、自己执行。

**四大支柱**：

| 支柱 | 机制 | 解决的问题 |
|------|------|-----------|
| **自动规划** | `write_todos` 工具 | Agent 自动将任务分解为待办事项并跟踪进度 |
| **子 Agent 委派** | Sub-agent spawning | 主 Agent 将子任务委派给隔离的子 Agent 执行 |
| **持久记忆** | Virtual filesystem | 虚拟文件系统存储跨会话信息 |
| **自动摘要** | Auto-summarization | 对话过长时自动压缩上下文 |

**DeepAgents 解决的核心问题——Context Bloat（上下文膨胀）**：

```
传统 Agent（无子 Agent）：
用户 → Agent（所有工具 + 所有上下文 + 所有步骤）
     → Context Window 爆炸 → 性能下降

DeepAgents 架构：
用户 → 主 Agent（规划 + 分派）
     ├→ 子 Agent A（独立 Context，只带相关工具）
     ├→ 子 Agent B（独立 Context，只带相关工具）
     └→ 子 Agent C（独立 Context，只带相关工具）
     → 结果汇总 → 主 Agent 返回
```

**何时用 DeepAgents vs 纯 LangGraph**：

| 场景 | 选择 | 理由 |
|------|------|------|
| 任务 <30 秒，单工具 | 纯 LangGraph ReAct | 简单够用 |
| 任务 >30 秒，多工具协调 | LangGraph + 手动编排 | 需要精细控制 |
| 任务 >2 分钟，需要规划/反思 | DeepAgents | 开箱即用的自治能力 |
| 需要跨会话记忆 | DeepAgents | 内置虚拟文件系统 |

**30+ 内置功能清单**：

| 类别 | 功能 | 说明 |
|------|------|------|
| **规划** | `write_todos` | 任务分解、进度追踪、动态调整 |
| **文件系统** | `read/write/edit_file` | 完整文件操作 |
| **文件系统** | `ls/glob/grep` | 文件搜索 |
| **命令执行** | `execute` | 沙箱化 shell 执行 |
| **子代理** | `task` (SubAgent) | 同步子代理委派 |
| **子代理** | `AsyncSubAgent` | 异步远程子代理 |
| **上下文管理** | 自动摘要 | 对话过长时自动压缩 |
| **上下文管理** | 大输出保存 | 超长输出自动写入文件 |
| **记忆** | `MemoryMiddleware` | 跨对话持久化记忆 |
| **技能** | `SkillsMiddleware` | 可扩展自定义技能 |
| **权限** | `FilesystemPermission` | 细粒度文件访问控制 |
| **中间件** | Middleware Stack | 可插拔中间件架构 |
| **模型适配** | `HarnessProfile` | 按模型自动调优 |
| **人机协作** | Human-in-the-loop | 工具调用前审批 |
| **输出** | `response_format` | 结构化输出 |
| **缓存** | `PromptCaching` | Anthropic prompt 缓存 |
| **集成** | MCP | 通过 langchain-mcp-adapters |
| **集成** | ACP | Agent Client Protocol |
| **集成** | LangGraph | 原生 LangGraph 支持 |

**Middleware 架构**（可插拔中间件栈）：

```python
from deepagents import create_deep_agent, SubAgent, MemoryMiddleware

agent = create_deep_agent(
    model="anthropic:claude-sonnet-4-6",
    tools=[search_tool, calculator_tool],
    system_prompt="你是一个数据分析助手。",
    subagents=[
        SubAgent(
            name="researcher",
            description="搜索和分析研究论文",
            system_prompt="你擅长学术研究。",
            tools=[arxiv_tool],
        )
    ],
    memory=["/memory/AGENTS.md"],              # 持久化记忆路径
    permissions=[
        FilesystemPermission(path="/data", allow_read=True, allow_write=False),
    ],
    checkpointer=MemorySaver(),                # LangGraph checkpoint
)
```

**HarnessProfile（按模型自动调优）**：

不同模型有不同的能力边界。HarnessProfile 让 DeepAgents 自动适配：

| 模型 | 自动调优项 | 效果 |
|------|-----------|------|
| Claude Sonnet | 长上下文 + 工具调用精度 | 规划更详细，工具调用更准 |
| GPT-4o | 响应速度 + 结构化输出 | 执行更快，JSON 输出更稳定 |
| 开源模型（Llama/Qwen） | 简化 prompt + 减少工具数 | 避免超出模型能力边界 |

**评估系统**（内置 Evals Suite）：

| 评估基准 | 测什么 | 说明 |
|---------|--------|------|
| **Terminal Bench 2.0** | 端到端行为 | Harbor 集成 |
| **LLM Judge** | 输出质量 | LLM 自动评分 |
| **Memory Agent Bench** | 记忆能力 | 跨会话记忆准确性 |
| **BFCL API 测试** | 工具使用 | 函数调用正确率 |
| **Tau2 Airline** | 领域任务 | 航空客服场景 |

**🔴 CHECKPOINT · 告诉 AI**：「DeepAgents 是 batteries-included 的 Agent harness。用 `create_deep_agent()` 3 行代码起步。需要精细控制时用纯 LangGraph，需要开箱即用时用 DeepAgents。」

---


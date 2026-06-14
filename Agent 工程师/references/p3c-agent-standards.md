## P3C 规范融合（Agent 工程化编码规范）

> **设计理念**：P3C 是阿里巴巴数万工程师沉淀的企业级编码规范。以下将 P3C 中与 Agent 开发最相关的规范（错误码、异常处理、日志、安全）适配到 Python/Agent 生态，分为【强制】【推荐】【参考】三个等级。

### 一、Agent 错误码规范

#### 【强制】错误码设计原则：快速溯源 + Agent 可决策

```python
# agent/errors.py — P3C 风格 Agent 错误码
from enum import Enum

class ErrorSource(str, Enum):
    """错误来源（P3C: A=用户, B=系统, C=第三方）"""
    USER = "A"          # 用户输入错误
    AGENT = "B"         # Agent 系统错误
    TOOL_EXTERNAL = "C" # 第三方工具/服务错误

class AgentErrorCode(str, Enum):
    """
    错误码格式：来源字母 + 4位数字
    Agent 专用错误码 = 谁的错 + 错在哪
    """
    # === A 用户端错误 ===
    A0001 = "A0001-输入参数校验失败"
    A0002 = "A0002-认证失败"
    A0003 = "A0003-权限不足"
    A0004 = "A0004-意图无法识别"
    A0005 = "A0005-操作频率过高"
    A0006 = "A0006-输入内容超长"

    # === B Agent 系统错误 ===
    B0001 = "B0001-Agent执行异常"
    B0002 = "B0002-LLM调用失败"
    B0003 = "B0003-Context组装失败"
    B0004 = "B0004-状态机异常"
    B0005 = "B0005-记忆系统异常"
    B0006 = "B0006-Guardrail触发"
    B0007 = "B0007-循环检测触发"

    # === C 第三方工具/服务错误 ===
    C0001 = "C0001-工具调用失败"
    C0002 = "C0002-工具调用超时"
    C0003 = "C0003-工具返回异常数据"
    C0004 = "C0004-第三方API限流"
```

#### 【强制】错误码不能直接输出给用户

```python
# ❌ 反例：把内部错误码暴露给用户
return {"error": "B0002-LLM调用失败"}  # 用户看不懂

# ✅ 正例：分离用户提示和开发者调试信息
class AgentException(Exception):
    def __init__(self, code: AgentErrorCode, message: str, retryable: bool = False):
        self.code = code.value
        self.message = message
        self.retryable = retryable
        super().__init__(message)

# 统一异常处理器
@app.exception_handler(AgentException)
async def agent_exception_handler(request: Request, exc: AgentException):
    logger.error("[%s] %s | path=%s", exc.code, exc.message, request.url.path)
    return JSONResponse(
        status_code=400 if exc.code.startswith("A") else 500,
        content={
            "message": exc.message,              # 用户看到的
            "error_code": exc.code,              # 开发者看到的
            "retryable": exc.retryable,          # Agent 决策用的
        },
    )
```

### 二、Agent 异常处理规范

#### 【强制】不要用异常做流程控制

```python
# ❌ 反例：用异常判断意图是否识别成功
try:
    intent = classify_intent(user_msg)
except IntentError:
    intent = Intent.UNKNOWN  # 异常做流程控制，效率低

# ✅ 正例：用返回值+条件判断
result = classify_intent(user_msg)
if result.confidence < 0.5:
    return ask_user_clarification(user_msg)  # 正常流程
intent = result.intent
```

#### 【强制】精准捕获，区分可恢复和不可恢复

```python
# ❌ 反例：大段 try-catch
try:
    intent = classify_intent(msg)
    plan = planner.plan(intent, state)
    result = executor.execute(plan)
    memory.save(result)
except Exception as e:
    logger.error(f"出错了: {e}")  # 哪一步？不知道

# ✅ 正例：精准捕获，分层处理
# 稳定代码无需 try-catch
intent = classify_intent(msg)
if not intent:
    raise AgentException(AgentErrorCode.A0004, "无法理解您的意思，请换个说法", retryable=False)

# 非稳定代码：精准捕获
try:
    plan = planner.plan(intent, state)
except LLMTimeoutError:
    raise AgentException(AgentErrorCode.B0002, "思考超时，请稍后重试", retryable=True)
except LLMAuthError:
    raise AgentException(AgentErrorCode.B0002, "服务暂时不可用", retryable=False)

try:
    result = executor.execute(plan)
except ToolNotFoundError:
    raise AgentException(AgentErrorCode.C0001, "该功能暂时不可用", retryable=False)
except ToolTimeoutError:
    raise AgentException(AgentErrorCode.C0002, "操作超时，请稍后重试", retryable=True)
```

#### 【强制】捕获异常必须处理，不要吞掉

```python
# ❌ 反例：吞掉异常
try:
    memory.save(session_id, state)
except Exception:
    pass  # 记忆保存失败，Agent 不知道，用户也不知道

# ✅ 正例：至少记录日志
try:
    memory.save(session_id, state)
except MemoryError as e:
    logger.warn("记忆保存失败，不影响主流程: session_id=%s, error=%s", session_id, str(e))
    # 不影响主流程，但日志记录了
```

### 三、Agent 日志规约

#### 【强制】统一日志框架，禁止 print

```python
# ❌ 反例
print(f"Intent: {intent}")
print(f"Tool result: {result}")

# ✅ 正例：结构化 JSON 日志
logger.info("意图识别完成", extra={
    "extra_data": {
        "intent": intent.name,
        "confidence": intent.confidence,
        "session_id": session_id,
    }
})
```

#### 【强制】日志使用占位符，不要拼接

```python
# ❌ 反例
logger.info(f"用户{user_id}发起退款，订单{order_id}，金额{amount}")

# ✅ 正例
logger.info("用户发起退款: user_id=%s, order_id=%s, amount=%.2f", user_id, order_id, amount)
```

#### 【强制】异常日志必须包含现场信息 + 堆栈

```python
# ❌ 反例
logger.error(f"工具调用失败: {e}")  # 没堆栈，无法定位

# ✅ 正例
logger.error(
    "工具调用失败: tool=%s, params=%s, error=%s",
    tool_name, params, str(e),
    exc_info=True  # 自动附加完整堆栈
)
```

#### 【强制】敏感信息不得入日志

```python
# ❌ 反例
logger.info(f"用户登录: email={email}, token={token}")

# ✅ 正例
logger.info("用户登录: email=%s", email)  # 不记录 token
```

#### 【强制】生产环境日志级别

| 级别 | 用途 | 生产环境 |
|------|------|---------|
| DEBUG | LLM 原始响应、token 详情 | ❌ 禁止 |
| INFO | 意图识别、工具调用、状态变更 | ✅ 有选择 |
| WARN | 重试、降级、缓存命中率低 | ✅ 允许 |
| ERROR | 工具调用失败、Agent 异常 | ✅ 允许 |

### 四、Agent 安全规约

#### 【强制】用户输入必须校验

```python
# ❌ 反例：信任用户输入
async def chat(user_message: str):
    response = agent.invoke(user_message)  # 无长度限制、无格式校验

# ✅ 正例：输入校验
async def chat(user_message: str = Body(..., min_length=1, max_length=10000)):
    # Prompt 注入检测
    if detect_prompt_injection(user_message):
        logger.warn("检测到疑似注入: user_id=%s", user_id)
        return {"message": "请输入有效的问题"}
    response = await agent.invoke(user_message)
```

#### 【强制】高风险操作必须人工确认

| 操作 | 风险等级 | 确认方式 |
|------|---------|---------|
| 发送邮件/消息 | 🔴 高 | 展示预览 → 用户确认 |
| 删除数据 | 🔴 高 | 二次确认 + 软删除 |
| 支付/退款 | 🔴 高 | 金额确认 + 验证码 |
| 修改配置 | 🟡 中 | 展示变更 diff → 确认 |
| 查询数据 | 🟢 低 | 直接执行 |

#### 【强制】工具调用必须有白名单和次数限制

```python
class AgentGuardrails:
    def __init__(self):
        self.max_tool_calls = 10
        self.max_steps = 15
        self.allowed_tools: set[str] = set()
        self.step_timeout = 30  # 秒

    def check_tool_call(self, tool_name: str, step: int):
        if tool_name not in self.allowed_tools:
            raise AgentException(AgentErrorCode.B0006, f"工具 {tool_name} 未授权")
        if step > self.max_steps:
            raise AgentException(AgentErrorCode.B0007, "超过最大步数")
        # 循环检测
        if self.recent_tools[-3:] == [tool_name] * 3:
            raise AgentException(AgentErrorCode.B0007, f"检测到循环调用 {tool_name}")
```

### 五、Agent 注释规约

#### 【强制】Agent 节点函数必须有文档字符串

```python
# ❌ 反例
def intent_router(state):
    ...

# ✅ 正例
def intent_router(state: AgentState) -> dict:
    """
    意图路由节点：识别用户意图并提取实体

    读取: state["messages"], state["user_id"]
    写入: state["current_intent"], state["entities"], state["intent_confidence"]

    路由规则:
        confidence >= 0.8 → 直接路由到对应技能
        0.5 <= confidence < 0.8 → 回问确认
        confidence < 0.5 → 转人工

    Raises:
        AgentException(A0004): 意图完全无法识别
    """
    ...
```

#### 【强制】状态字段必须有注释说明用途和生命周期

```python
class AgentState(TypedDict):
    # 工作记忆（当前会话，会话结束清空）
    messages: Annotated[list, add_messages]    # 对话历史
    current_intent: str                         # 当前意图

    # 执行状态（每步更新）
    step_count: int                             # 步骤计数（防无限循环）
    tool_output: str                            # 最近一次工具输出

    # 元信息（只读，创建后不修改）
    session_id: str                             # 会话ID
    user_id: str                                # 用户ID
```

### 六、P3C 合规检查清单

🔴 **CHECKPOINT · P3C Agent 合规检查**

| # | 检查项 | 等级 | 标准 |
|---|--------|------|------|
| 1 | 错误码符合 A/B/C 分类 | 【强制】 | 所有 AgentException 使用 AgentErrorCode |
| 2 | 异常不做流程控制 | 【强制】 | 无 try-catch 包裹业务判断 |
| 3 | 精准捕获异常 | 【强制】 | 无 `except Exception` 大段兜底 |
| 4 | 日志用占位符不拼接 | 【强制】 | `logger.info("x=%s", x)` |
| 5 | 生产环境无 debug 日志 | 【强制】 | 日志级别 >= INFO |
| 6 | 异常日志带堆栈 | 【强制】 | `exc_info=True` |
| 7 | 敏感信息不入日志 | 【强制】 | 无 token/key/password |
| 8 | 用户输入有校验 | 【强制】 | 无裸 `str` 参数 |
| 9 | 高风险操作有人工确认 | 【强制】 | interrupt() 模式 |
| 10 | 工具有白名单和次数限制 | 【强制】 | Guardrails 检查 |
| 11 | 节点函数有文档字符串 | 【推荐】 | docstring 含读取/写入/Raises |
| 12 | 状态字段有注释 | 【推荐】 | 注释含用途和生命周期 |

🛑 **STOP · 任一【强制】项未通过 → 修复后再提交**

---


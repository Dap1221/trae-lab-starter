# 方法四：智能体应用开发速查卡 —— LangChain & LangGraph（第 7–9 章）

## 前置依赖安装

```bash
pip install -U langchain langchain-ollama langgraph python-dotenv
pip install -U langgraph-checkpoint-sqlite
pip install -U langchain-chroma chromadb langchain-text-splitters
```

## 4A. LangChain 智能体开发提示词（第 8 章）

```markdown
## 角色
你是一位精通 LangChain 最新生态与 Python 异步编程的智能体开发工程师。

## 任务
基于 LangChain 开发【待老师公布：智能体名称】，实现以下核心能力：
1. 【待老师公布：核心对话/业务场景】
2. 自定义工具列表：
   - 工具 1：【待老师公布：工具功能、输入参数与返回说明】
   - 工具 2：【待老师公布：工具功能、输入参数与返回说明】

## 技术栈与实现规范
- 模型接入：使用 `langchain_ollama.ChatOllama`（从 `.env` 读取 `OLLAMA_MODEL="gemma4:e4b"`、`OLLAMA_BASE_URL="http://localhost:11434"`，`temperature=0`；若题目指定云端 API 则改用 `ChatOpenAI`）
- 提示词与编排：使用 `ChatPromptTemplate` 与 LCEL 管道语法
- 工具定义：使用 `from langchain_core.tools import tool` 装饰器 `@tool`，每个工具必须有清晰中文 docstring 与完整类型注解
- 智能体构建：使用 `create_agent(model=model, tools=tools)`
- 提供可直接运行的测试入口 `main.py`，覆盖“普通问答”与“触发工具调用”两类场景

## 验收标准
- [ ] 运行 `python main.py` 能准确识别意图并按需调用 `@tool` 工具
- [ ] 无工具可匹配或无结果时礼貌兜底，不编造事实
- [ ] 模型参数全部来自环境变量，无硬编码
```

## 4B. LangGraph 状态图与多节点智能体提示词（第 9 章）

```markdown
## 角色
你是一位精通 LangGraph 状态图编排的智能体架构师。

## 任务
基于 LangGraph `StateGraph` 构建【待老师公布：智能体系统名称】，完成以下业务流转：
- 业务需求：【待老师公布：节点职责、意图分类、条件分支或循环追问逻辑】
- 工具清单（如有）：【待老师公布：需要绑定的 @tool 工具】

## 架构与编码规范（严格遵守）
1. **State 定义**：使用 `TypedDict` 或 `MessagesState`；消息使用 `Annotated[list[BaseMessage], add_messages]`，日志使用 `Annotated[list[str], operator.add]`。
2. **Node 节点**：每个节点单一职责，仅返回更新字段字典；LLM 意图分类必须做白名单校验，异常回退默认值。
3. **Edge 与条件路由**：路由函数必须是**纯函数**；若含循环追问节点，必须设置 `clarify_count` 计数器并限制上限（如 `>= 3` 强制退出），严防死循环。
4. **工具调用闭环（如涉及）**：使用 `llm.bind_tools(tools)` + `ToolNode(tools)` + `tools_condition`，形成 `agent -> tools -> agent` 闭环。
5. **状态持久化**：注入 `InMemorySaver()` 或 `SqliteSaver.from_conn_string("langgraph_state.db")`，调用传入 `config={"configurable": {"thread_id": "..."}}`。

## 验收标准
- [ ] 覆盖所有条件分支（正常、异常/安抚、达到循环上限）
- [ ] 同一 `thread_id` 下连续多轮调用能记住历史消息与业务状态字段
- [ ] `logs` 字段能完整追踪执行路径
```

## 4C. 本地 RAG + 人工审核（Human-in-the-Loop）综合提示词（第 9 章高级版）

```markdown
## 任务
基于 LangGraph + Ollama + Chroma 构建带本地 RAG 检索与 Human-in-the-Loop（人工审核）机制的【待老师公布：智能体名称】。

## 文件拆解要求
1. `build_vector_db.py`（向量建库脚本）：
   - 读取原始资料文件【待老师公布：资料路径/格式】
   - 使用 `RecursiveCharacterTextSplitter(chunk_size=120, chunk_overlap=20, separators=['\n', '。', '；', '，'])` 切分文档
   - 使用 `OllamaEmbeddings(model='nomic-embed-text')` 写入本地 `chroma_db` 目录
2. `agent_rag_review.py`（主工作流脚本）：
   - 启动前检查 `chroma_db` 是否存在，不存在则报错引导先运行建库脚本
   - 状态包含：`question`、`retrieved_docs`、`draft_answer`、`need_human_review`、`review_result`、`final_answer`、`logs: Annotated[list[str], add]`
   - 节点编排：`START -> retrieve_knowledge (similarity_search k=3) -> generate_answer (ChatOllama gemma4:e4b, temperature=0) -> 条件路由`
   - 防幻觉规则：严格依据检索材料回答，材料不足时明确输出“知识库中未找到充分依据”并触发人工审核
   - 敏感词规则：命中【待老师公布：敏感关键词列表】或知识库未找到依据时，`need_human_review = True`，路由至 `human_review`
   - 人工审核实现：在 `human_review` 节点调用 `langgraph.types.interrupt(payload)` 暂停执行；审核后用 `Command(resume={"approved": True, "edited_answer": "..."})` 在相同 `thread_id` 下恢复执行进入 `finalize_answer -> END`

## 验收标准
- [ ] 运行 `python build_vector_db.py` 成功建库并打印写入片段数
- [ ] 普通问题直接返回准确答案，不触发中断
- [ ] 敏感或超纲问题首次 `invoke` 返回 `__interrupt__`，传入 `Command(resume=...)` 后输出最终定稿答案
```

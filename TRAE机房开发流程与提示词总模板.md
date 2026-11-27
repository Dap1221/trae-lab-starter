# TRAE 机房复用开发流程与全阶段提示词总模板

本手册基于厦门大学林子雨老师[《AI编程与智能体开发》教材官网](https://dblab.xmu.edu.cn/post/ai-programming-agent/)及[2026秋季学期课程大纲](https://dblab.xmu.edu.cn/post/ai-programming-agent-2026/)全部 9 章知识点整理，覆盖**基于提示词、基于规范（OpenSpec）、流程驱动（Superpowers）以及智能体开发（LangChain / LangGraph）**四大应用开发方法。

在机房拿到题目后，按“**选路线 → 初始化底座 → 填 `【待老师公布：...】` → 按阶段粘贴提示词 → 测试验收**”执行即可。

## 一、机房 30 秒开发方法选型表

| 题目类型 / 考试要求 | 对应课程方法 | 核心工具 / 框架 | 适用场景与特点 |
| :--- | :--- | :--- | :--- |
| 单脚本、轻量前后端原型、快速出活 | **方法一：基于提示词的 AI 编程**（第4章） | TRAE Agent + `AGENTS.md` + `.trae/rules/` | 通过五段式结构化提示词直出代码，开发最快，适合需求清晰的小型应用（[第4章配套实践](https://dblab.xmu.edu.cn/blog/6779/)） |
| 中型功能开发、存量项目重构、需求需严格对齐 | **方法二：基于规范的 AI 编程**（第5章） | **OpenSpec**（4 步门控）+ TRAE Agent | 先写提案与 Given/When/Then 规范再编码，变更可归档追溯，适合功能改造与数据库迁移（[第5章配套实践](https://dblab.xmu.edu.cn/blog/6703/)） |
| 复杂系统、多模块/全栈工程、要求 TDD 与高代码质量 | **方法三：流程驱动的 AI 编程**（第6章） | **Superpowers**（7 步串行）+ Git Worktree + pytest | 强制苏格拉底追问、分支隔离、任务拆解、TDD 红绿重构与代码评审，工程规范度最高（[第6章配套实践](https://dblab.xmu.edu.cn/blog/6766/)） |
| 问答助手、工具调用、知识库 RAG、人工审核工作流 | **方法四：智能体应用开发**（第7–9章） | **LangChain** / **LangGraph** + Ollama + Chroma | 线性链路/简单工具调用选 LangChain（[第8章](https://dblab.xmu.edu.cn/blog/6716/)）；状态机、条件分支、循环追问、持久化、RAG 与人工介入选 LangGraph（[第9章](https://dblab.xmu.edu.cn/blog/6734/)） |

> **组合建议**：若题目是“带前端界面的智能体应用”，最佳实践是**外层用 OpenSpec 或 Superpowers 管控开发流程，内层核心代码采用 LangChain / LangGraph**。

## 二、阶段零：机房通用环境初始化与项目底座（第 1–3 章）

### 1. 基础环境与 Git 检查
```bash
# 1. 检查 Node.js 版本（OpenSpec 要求 >= 20.19.0，Superpowers 建议 Node 20）
node --version
# 若 Windows Git Bash 找不到 node 或 npm 全局包，写入 ~/.bashrc：
# export PATH="$PATH:/c/Program Files/nodejs/:/c/Users/<用户名>/AppData/Roaming/npm"

# 2. 创建并激活独立 Python 虚拟环境
python -m venv .venv
# Windows Git Bash: source .venv/Scripts/activate
# Windows CMD:      .venv\Scripts\activate
# macOS / Linux:    source .venv/bin/activate

# 3. 如题目使用本地大模型 / 本地 RAG，检查 Ollama 服务与模型
ollama serve
ollama list
ollama pull gemma4:e4b
ollama pull nomic-embed-text
```

### 2. 项目底座文件（本脚手架已预建）
- `AGENTS.md`：项目说明书，拿到题目后第一时间把 `【待老师公布：...】` 替换成真实需求。
- `.trae/rules/project-style.md`：TRAE 项目规则（`alwaysApply: true`），强制编码规范、防御性编程与 API 响应格式。
- `.pre-commit-config.yaml`：整合 Black、isort、Ruff、Vulture、mypy 的提交前自动质检配置（[第6章质量保证](https://dblab.xmu.edu.cn/blog/6789/)），启用命令：
```bash
pip install pre-commit black isort ruff mypy vulture
pre-commit install
# 若报 core.hooksPath 冲突：git config --global --unset-all core.hooksPath
```

## 三、方法一：基于提示词的 AI 编程模板（第 4 章）

严格采用“**角色 + 任务 + 约束 + 参考 + 验收标准**”五段式结构，规避需求不明确、上下文缺失、异常处理不完善、需求描述不完整四大问题（[第4章实践](https://dblab.xmu.edu.cn/blog/6779/)）。

### 模板 1A：后端 API 开发提示词
```markdown
## 角色
你是一位有 5 年经验的 Python 后端工程师，精通 FastAPI、Pydantic v2 与 SQLite/SQLAlchemy。

## 任务
根据项目根目录的 `AGENTS.md` 与 `.trae/rules/project-style.md`，实现【待老师公布：模块/应用名称】的后端核心代码与 API 接口。
需一次性完整实现以下功能：
1. 【待老师公布：功能 1】
2. 【待老师公布：功能 2】
3. 【待老师公布：功能 3】

## 文件结构与接口清单
1. `schemas.py`：定义请求体与响应体 Pydantic 模型，字段如下：【待老师公布：核心实体字段】
2. `main.py`（或分层目录）：包含以下路由：
| 方法 | 路径 | 请求体/参数 | 响应与状态码 |
| :--- | :--- | :--- | :--- |
| GET | /api/【待填】 | 【待填】 | HTTP 200 + JSON |
| POST | /api/【待填】 | 【待填】 | HTTP 201 + JSON |
| PUT/DELETE | /api/【待填】/{id} | 【待填】 | HTTP 200/204 |

## 约束
- 严格遵守 `.trae/rules/project-style.md` 中的命名、类型注解与防御性编程规范
- 资源不存在时返回 HTTP 404，格式 `{"detail": "Not found"}`
- 禁止硬编码密钥，禁止引入未在 `AGENTS.md` 声明的冗余第三方依赖
- 若包含前端页面，在 `main.py` 中挂载 `templates/` 目录，根路径 `/` 返回首页

## 验收标准
- [ ] 所有正常路径接口返回正确状态码与完整字段
- [ ] 非法输入（空值、类型错误、越界）返回 HTTP 400/422 且服务不崩溃
- [ ] 查询/修改不存在的 ID 返回 HTTP 404
- [ ] 并发/边界场景（如空列表、重复提交）处理正确
```

### 模板 1B：前端页面开发提示词
```markdown
## 任务
创建 `templates/index.html`（或指定前端目录），为【待老师公布：应用名称】提供可视化中文操作界面。

## 技术栈
- 原生 HTML5 + CSS3 + Vanilla JavaScript（单文件实现，无框架依赖，使用原生 `fetch`）
（若老师要求 React + TailwindCSS，请替换为对应栈）

## 页面结构
1. 顶部标题区：显示【待老师公布：页面主标题与简要说明】
2. 输入/操作区：【待老师公布：表单控件、搜索框、提交按钮等】
3. 数据展示区：【待老师公布：列表/卡片/表格字段及状态样式】

## API 调用说明
请严格对齐后端 `main.py` 与 `schemas.py` 中已定义的路由、请求体和响应格式，不得臆造字段名。

## 样式与交互要求
- 整体浅色系、简洁清晰，居中布局，最大宽度 960px，无衬线字体（`sans-serif`）
- 按钮与加载状态有明确反馈，接口调用失败时在页面给出友好中文提示并在控制台打印错误
- 页面加载时自动拉取初始数据，增删改成功后自动刷新视图

## 验收标准
- [ ] 打开 `http://localhost:8000/` 页面无控制台报错，能正常加载数据
- [ ] 所有增/删/改/查交互均能真实联动后端并即时更新界面
```

### 模板 1C：存量项目改造 / 参照现有代码提示词
```markdown
## 任务
参照现有项目中的 `【已有参照文件路径，如 src/services/user_service.py】`，为【待老师公布：新模块名称】实现类似功能。

## 约束
- 严格遵照参照代码的目录结构、命名风格、依赖注入和异常处理模式
- 不得修改现有核心数据模型与公共函数签名，不引入参照代码中没有的设计模式
- 保留以下架构决策（ADR）约束：【如有特殊业务约束，如软删除、Decimal 金额、审计日志，在此填写】

## 差异说明
1. 【待老师公布：与参照模块的差异点 1】
2. 【待老师公布：与参照模块的差异点 2】

## 验收标准
- [ ] 新增模块通过单元测试
- [ ] 既有模块的原有测试全部保持通过，无回归破坏
```

### 模板 1D：精准 Bug 修复提示词
```markdown
## 任务
定位并修复 `【报错文件路径】` 中 `【函数/模块名】` 的以下问题：
- 现象/报错日志：【粘贴终端完整 Traceback 或错误响应】

## 约束
- 保持函数签名与对外接口契约不变
- 仅做最小必要改动，禁止顺手重写无关模块
- 同步补充 1 条能复现该 Bug 的单元测试，确保修复后通过

## 验收标准
- [ ] 复现步骤不再报错，原有用例全部通过
- [ ] 边界输入（空值、超时、异常状态）有明确兜底处理
```

## 四、方法二：基于规范的 AI 编程模板 —— OpenSpec（第 5 章）

遵循 Spec-Kit 四层优先级（`Constitution > Specification > Plan > Tasks`）与 [OpenSpec 实践流程](https://dblab.xmu.edu.cn/blog/6703/)。

### 1. 安装与初始化
```bash
npm install -g @fission-ai/openspec@latest
openspec --version
openspec init --tools trae
```
随后检查并完善本脚手架已预置的 `openspec/config.yaml`。

### 2. OpenSpec 四步门控总控提示词（防 AI 抢跑）
```markdown
请你严格按照 OpenSpec 的四个步骤进行功能开发，无确认绝不主动推进、不超前写代码、不跳过任何一个步骤。
固定流程顺序，缺一不可，仅完成当前步骤，等待我确认后再进入下一环。

1. 探索阶段 `/skill:openspec-explore`：根据用户需求探索当前项目结构、现状与潜在风险
2. 提案阶段 `/skill:openspec-propose`：根据探索阶段的讨论生成完整规范文档（proposal.md 必须含 ## Why 和 ## What Changes；specs/[capability]/spec.md 必须使用 ADDED/MODIFIED Requirements + Given/When/Then Scenario 格式；以及拆分好的 tasks）
3. 执行阶段 `/skill:openspec-apply-change`：严格按提案阶段的规范与任务清单逐项实现代码与测试
4. 归档阶段 `/skill:openspec-archive-change`：验收通过后将变更归档至 `openspec/changes/archive/`

执行纪律：
1. 输出首行标注当前所处步骤（如：【当前步骤：第1步 探索阶段】）
2. 每步结束统一话术：`本步骤完成，请确认进入下一步`
3. 未收到我的确认指令，仅限完善当前步骤内容，禁止输出后续步骤内容与业务代码
4. 严禁自主提速、合并步骤、提前完成开发

现在开始第 1 步，使用 `/openspec-explore` 分析当前项目，本次核心需求如下：
【待老师公布：粘贴老师公布的功能需求与约束】
```

### 3. 各阶段确认话术与归档命令
- 第 1 步后：`确认探索结论，无补充。请进入第 2 步，执行 /openspec-propose 生成规范提案。`
- 第 2 步后：`规范文档确认无误，请进入第 3 步，执行 /openspec-apply-change 按任务清单实现代码并运行测试。`
- 第 3 步后：`功能验收通过，请进入第 4 步，执行 /openspec-archive-change 完成归档。`
- 第 4 步终端归档提交：
```bash
git add openspec/changes/archive/
git commit -m "feat: archive <变更名称> change"
```

## 五、方法三：流程驱动的 AI 编程模板 —— Superpowers 七步法（第 6 章）

基于[第6章 Superpowers 实战](https://dblab.xmu.edu.cn/blog/6766/)与[全栈项目实践](https://dblab.xmu.edu.cn/blog/6801/)整理。

### 1. 安装与初始提交（每个新项目必须单独安装）
```bash
npx superpowers-zh --tool trae
git add .
git commit -m "Initial commit: project skeleton and AGENTS.md"
```

### 2. Superpowers 七步串行防抢跑总控提示词
```markdown
本对话中，你必须使用 Superpowers。严格遵循 Superpowers 七步法串行开发本项目，无确认绝不主动推进、不超前写代码、不跳过任何一个步骤。
固定流程顺序，缺一不可，仅完成当前步骤，等待我确认后再进入下一环。

1. 需求对齐：调用 `/skill:brainstorming`，采用苏格拉底式提问逐条澄清需求、数据结构、边界条件和接口设计，输出设计规格文档到 `docs/superpowers/specs/`，待确认
2. 环境隔离：调用 `/skill:using-git-worktrees`，创建隔离的 Git 工作树与独立开发分支，确定项目目录，不污染主干，待确认
3. 任务拆解：调用 `/skill:writing-plans`，将整个开发过程拆分为每个 2-5 分钟可完成的细粒度任务，明确涉及文件、实现逻辑与校验方式，输出到 `docs/superpowers/plans/`，待确认
4. 逐任务开发：调用 `/skill:subagent-driven-development`，按计划逐个任务独立实施，单任务完成后自检复盘，不批量一次性编写（且每个任务开发必须结合 TDD），待确认
5. TDD 测试：调用 `/skill:test-driven-development`，严格执行 RED-GREEN-REFACTOR 循环（先写失败测试 → 编写最小实现通过测试 → 重构优化），不得跳过测试，待确认
6. 代码评审：调用 `/skill:requesting-code-review`，对照设计文档与 `.trae/rules/` 排查漏洞、规范、边界与性能问题并完成修复，待确认
7. 项目收尾：调用 `/skill:finishing-a-development-branch`，运行全量测试通过后合并到主干分支并清理工作树，整理交付文档，流程结束

重要执行规则：
1. 输出首行标注当前所处步骤（如：【当前步骤：第1步 需求对齐】）
2. 每步结束统一话术：`本步骤完成，请确认进入下一步`
3. 未收到确认指令，仅限完善当前内容，禁止输出后续内容与代码
4. 严禁自主提速、合并步骤、提前完成开发
5. 所有开发工作必须在对应子目录/工作树中完成，绝对不要主动发起 `git push` 等远程操作

现在开始第 1 步，调用 `/skill:brainstorming` 逐条澄清本项目需求。本项目的基本要求已写入根目录 `AGENTS.md`，核心题目要求如下：
【待老师公布：粘贴老师公布的功能需求与评分点】
```

## 六、方法四：智能体应用开发模板 —— LangChain & LangGraph（第 7–9 章）

### 1. 智能体依赖安装
```bash
pip install -U langchain langchain-ollama langgraph python-dotenv
pip install -U langgraph-checkpoint-sqlite
pip install -U langchain-chroma chromadb langchain-text-splitters
```

### 模板 4A：LangChain 智能体开发提示词（[第8章](https://dblab.xmu.edu.cn/blog/6716/)）
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
- 模型接入：使用 `langchain_ollama.ChatOllama`（从 `.env` 读取 `OLLAMA_MODEL="gemma4:e4b"`、`OLLAMA_BASE_URL="http://localhost:11434"`，`temperature=0`；若用云端 API 则改用 `ChatOpenAI`）
- 提示词与编排：使用 `ChatPromptTemplate` 与 LCEL 管道语法
- 工具定义：使用 `from langchain_core.tools import tool` 装饰器 `@tool`，每个工具必须有清晰中文 docstring 与完整类型注解
- 智能体构建：使用 `create_agent(model=model, tools=tools)`
- 提供可直接运行的测试入口 `main.py`，覆盖“普通问答”与“触发工具调用”两类场景

## 验收标准
- [ ] 运行 `python main.py` 能准确识别意图并按需调用 `@tool` 工具
- [ ] 无工具可匹配或无结果时礼貌兜底，不编造事实
- [ ] 模型参数全部来自环境变量，无硬编码
```

### 模板 4B：LangGraph 状态图与多节点智能体提示词（[第9章](https://dblab.xmu.edu.cn/blog/6734/)）
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

### 模板 4C：本地 RAG + 人工审核（Human-in-the-Loop）综合提示词（[第9章高级版](https://dblab.xmu.edu.cn/blog/6734/)）
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

## 七、阶段收尾：全链路测试、审查与部署验收（配套质量保证）

整理自[AI驱动的质量保证](https://dblab.xmu.edu.cn/blog/6789/)与[全栈项目实战](https://dblab.xmu.edu.cn/blog/6801/)，完整单文件模板见 `prompts/05-收尾验收-测试审查与部署.md`。

1. **因果图法测试用例生成**：提取输入/输出 → 画因果图 → 生成判定表 → 输出 Markdown 用例表。
2. **后端接口测试**：`pytest + httpx.AsyncClient + pytest-asyncio`，覆盖全路由正常/异常/404 场景。
3. **前端/端到端测试**：`TS + Playwright Test`，在 `e2e/` 目录生成并执行。
4. **性能压测**：`Locust` 压测脚本（读写比 3:1）与 Headless 压测命令。
5. **人工架构一致性审查 + `curl` 验活**：核查代码与 `AGENTS.md` / 设计文档一致性，实测全部 API。
6. **SRE / Docker Compose 启动排障**：遇机房网络慢时优先切换国内镜像源（`docker.m.daocloud.io` / `docker.1ms.run`）。

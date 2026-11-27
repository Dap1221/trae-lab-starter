# 【待老师公布：项目名称】

## 项目简介
【待老师公布：用 2-3 句话概括项目背景、核心目标与使用对象】

## 功能需求
1. 【待老师公布：核心功能点 1】
2. 【待老师公布：核心功能点 2】
3. 【待老师公布：核心功能点 3】

## 技术栈
- 后端：Python 3.12 + FastAPI + Pydantic v2（按题目实际要求调整）
- 数据存储：SQLite（或 Chroma 向量库，按题目实际要求调整）
- 前端：原生 HTML5 / CSS3 / Vanilla JavaScript（或 React + TailwindCSS）
- 智能体/大模型（如涉及）：LangChain / LangGraph + Ollama（`gemma4:e4b` / `nomic-embed-text`）
- 测试与质量：pytest + httpx + pytest-asyncio + Ruff

## 目录结构
```text
.
├── src/                # 核心业务代码（遵循路由层/服务层/数据访问层分离）
├── templates/          # 前端页面目录
├── tests/              # 自动化测试
├── docs/superpowers/   # 设计规格（specs/）与实现计划（plans/）
├── openspec/           # OpenSpec 规范与变更归档目录
├── .trae/rules/        # TRAE 强制编码与防御性规则
├── AGENTS.md           # 本项目说明书
└── README.md           # 运行与验收说明
```

## 架构约定（AI 必须遵守）
- 后端只负责 API 响应，严格分层（路由层不直接写数据库操作）
- 严禁硬编码 API Key、密钥或密码，统一通过 `.env` 与环境变量读取
- 所有代码修改前需考虑边界条件与异常处理
- 不得直接修改主干分支（使用 Superpowers 时在隔离 Worktree 分支开发）
- 绝对不要主动发起 `git push` 等改变远程仓库状态的操作
- 所有前端界面、文档与函数 docstring 默认使用中文

## 常用命令
- 启动开发服务：`uvicorn main:app --reload --port 8000`
- 运行全部测试：`pytest tests/ -v`
- 代码检查：`ruff check .`

# TRAE 机房实战复用脚手架（trae-lab-starter）

本仓库基于厦门大学林子雨老师[《AI编程与智能体开发》教材官网](https://dblab.xmu.edu.cn/post/ai-programming-agent/)全部 9 章方法论整理，专为机房实验与考核设计，开箱即用。

## 仓库目录结构

```text
trae-lab-starter/
├── README.md                          # 本说明文件（克隆与启动速查）
├── TRAE机房开发流程与提示词总模板.md    # 全套开发流程与提示词总手册
├── AGENTS.md                          # 项目说明书模板（拿到题目后先改这里）
├── .env.example                       # 环境变量模板（Ollama / 云端 API）
├── .gitignore                         # Python / Node / Chroma / Worktree 忽略配置
├── .pre-commit-config.yaml            # 代码质检钩子（Black/isort/Ruff/mypy/Vulture）
├── .trae/rules/project-style.md       # TRAE 强制项目规则（alwaysApply: true）
├── openspec/config.yaml               # OpenSpec 预置项目配置
├── prompts/                           # 按开发方法拆分的可直接复制提示词卡片
│   ├── 01-提示词驱动-前后端与维护.md
│   ├── 02-规范驱动-OpenSpec四步门控.md
│   ├── 03-流程驱动-Superpowers七步法.md
│   ├── 04-智能体开发-LangChain与LangGraph.md
│   └── 05-收尾验收-测试审查与部署.md
├── src/                               # 后端 / 核心代码目录
├── templates/                         # 前端单页目录
├── tests/                             # pytest 测试目录
└── docs/superpowers/                  # Superpowers 规格与计划产物目录
    ├── specs/
    └── plans/
```

## 1. 本地首次上传到 GitHub

在本地终端进入本目录并推送到你新建的 GitHub 空仓库：

```bash
cd /Users/dap/Downloads/课件集合/trae-lab-starter
git init
git add .
git commit -m "feat: init TRAE lab starter scaffold and prompt templates"
git branch -M main
git remote add origin https://github.com/<你的用户名>/<你的仓库名>.git
git push -u origin main
```

## 2. 机房电脑拉取与 3 分钟启动

在机房电脑的 Git Bash 或 TRAE 终端中执行：

```bash
# 1. 拉取脚手架（若机房直连 GitHub 慢，可尝试使用镜像或先确认网络）
git clone https://github.com/<你的用户名>/<你的仓库名>.git my-exam-project
cd my-exam-project

# 2. 创建并激活独立 Python 虚拟环境
python -m venv .venv
# Windows Git Bash:
source .venv/Scripts/activate
# Windows CMD:
# .venv\Scripts\activate
# macOS / Linux:
# source .venv/bin/activate

# 3. 如需环境变量，复制 .env.example 为 .env 并填入配置
cp .env.example .env

# 4. 按题目要求安装对应插件（二选一或按需）：
# OpenSpec（第5章，需 Node.js >= 20.19.0）:
# npm install -g @fission-ai/openspec@latest && openspec init --tools trae
# Superpowers（第6章，每个新项目需单独执行）:
# npx superpowers-zh --tool trae
```

## 3. 拿到老师题目后的操作顺序

1. **填需求**：打开 `AGENTS.md`，将所有 `【待老师公布：...】` 替换为题目真实需求与技术栈。
2. **选路线**：参考 `TRAE机房开发流程与提示词总模板.md` 顶部的选型表，决定用提示词驱动、OpenSpec、Superpowers 还是 LangChain/LangGraph（或组合）。
3. **贴提示词**：直接打开 `prompts/` 下对应编号的 `.md` 文件，复制提示词粘贴到 TRAE Agent 对话框。
4. **做验收**：开发完成后，用 `prompts/05-收尾验收-测试审查与部署.md` 跑通测试、接口验活与架构核查。

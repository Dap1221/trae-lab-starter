# 方法三：流程驱动的 AI 编程速查卡 —— Superpowers 七步法（第 6 章）

## 前置终端命令（每个新项目必须单独安装一次）

```bash
npx superpowers-zh --tool trae
git add .
git commit -m "Initial commit: project skeleton and AGENTS.md"
```

## Superpowers 七步串行防抢跑总控提示词

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

## 七步推进标准确认话术

- **第 1 步收敛后**：`需求与设计文档确认无误，请进入第 2 步，调用 /skill:using-git-worktrees 创建隔离工作树和开发分支。`
- **第 2 步确认后**：`工作树环境已就绪，请进入第 3 步，调用 /skill:writing-plans 输出细粒度实现计划。`
- **第 3 步确认后**：`任务计划确认无误，请进入第 4 步和第 5 步，调用 /skill:subagent-driven-development 配合 /skill:test-driven-development 逐任务按 TDD 循环实施，每完成一个任务向我汇报测试结果。`
- **全部任务完成后**：`所有开发任务已完成，请进入第 6 步，调用 /skill:requesting-code-review 对全部改动进行严格代码审查并修复问题。`
- **评审通过后**：`代码评审通过，请进入第 7 步，调用 /skill:finishing-a-development-branch 合并到主分支并清理工作树。`

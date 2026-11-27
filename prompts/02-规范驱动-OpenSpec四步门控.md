# 方法二：基于规范的 AI 编程速查卡 —— OpenSpec（第 5 章）

## 前置终端命令

```bash
npm install -g @fission-ai/openspec@latest
openspec --version
openspec init --tools trae
```
（初始化后记得把根目录的 `openspec/config.yaml` 中的项目名与技术栈改为实际题目。）

## OpenSpec 四步门控总控提示词（防 AI 抢跑）

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

## 各阶段确认话术

- **第 1 步确认后**：`确认探索结论，无补充。请进入第 2 步，执行 /openspec-propose 生成规范提案。`
- **第 2 步确认后**：`规范文档确认无误，请进入第 3 步，执行 /openspec-apply-change 按任务清单实现代码并运行测试。`
- **第 3 步验收后**：`功能验收通过，请进入第 4 步，执行 /openspec-archive-change 完成归档。`

## 归档后 Git 同源提交

```bash
git add openspec/changes/archive/
git commit -m "feat: archive <变更名称> change"
```

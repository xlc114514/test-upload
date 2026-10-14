# test-upload

本仓库用于验证 ChatGPT 与 DSH 等多个 Agent 的协作工作流。

## 协作规范

所有在本仓库中工作的 Agent 都应先阅读：

- [多 Agent 协作协议](docs/MULTI_AGENT_COLLABORATION_PROTOCOL.md)：角色、任务输入、执行边界、Git 约定、结构化汇报与验收标准。
- [仓库 Agent 指引](AGENTS.md)：执行任务时的默认规则与安全边界。

## 任务与报告模板

协议第 2 节和第 5 节分别规定了任务输入与执行回报的 JSON 结构。为免每次手写出错，仓库提供两份模板：

- [任务输入模板](templates/task-template.json)：协调者派发任务时填写，字段对应协议第 2 节（task_id、objective、context、constraints、scope、dependencies、deliverables、acceptance_criteria、execution_mode）。
- [执行报告模板](templates/agent-report-template.json)：执行 Agent 回报时填写，字段对应协议第 5 节（task_id、status、summary、assumptions、findings、files_changed、artifacts、implementation_details、tests、interface_changes、risks、blockers、git、handoff）。

**用法：** 复制模板文件后逐字段填写，不要直接在模板上改写并提交。

**注意：** 模板内的值是占位示例，**不是真实任务，也不是真实执行结果**。凡是以 `REPLACE_ME` 开头的标识或示例证据都必须替换；不适用的字段使用空数组或 `null`，不得保留示例值冒充真实内容。

## 建议工作流

1. ChatGPT 作为协调者，拆分需求并定义验收标准。
2. 执行 Agent 在独立任务分支中修改文件并运行可用测试。
3. 执行 Agent 返回变更清单、测试证据、风险及结构化报告（见[执行报告模板](templates/agent-report-template.json)）。
4. 协调者进行代码审查并要求修复，确认后再合并。

默认不直接合并到主分支，也不得擅自强制推送、覆盖他人工作或提交凭据。

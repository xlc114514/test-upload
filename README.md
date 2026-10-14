# test-upload

本仓库用于验证 ChatGPT 与 DSH 等多个 Agent 的协作工作流。

## 协作规范

所有在本仓库中工作的 Agent 都应先阅读：

- [多 Agent 协作协议](docs/MULTI_AGENT_COLLABORATION_PROTOCOL.md)：角色、任务输入、执行边界、Git 约定、结构化汇报与验收标准。
- [仓库 Agent 指引](AGENTS.md)：执行任务时的默认规则与安全边界。

## 任务与报告模板

协议第 2 节和第 5 节分别规定了任务输入与执行回报的 JSON 结构。为免每次手写出错，仓库提供两份模板：

- [任务输入模板](templates/task-template.json)：协调者派发任务时填写，顶层字段恰好为协议第 2 节的 9 个键——`task_id`、`objective`、`context`、`constraints`、`scope`、`dependencies`、`deliverables`、`acceptance_criteria`、`execution_mode`。
- [执行报告模板](templates/agent-report-template.json)：执行 Agent 回报时填写，顶层字段恰好为协议第 5 节的 14 个键——`task_id`、`status`、`summary`、`assumptions`、`findings`、`files_changed`、`artifacts`、`implementation_details`、`tests`、`interface_changes`、`risks`、`blockers`、`git`、`handoff`。

两份模板的顶层键与协议严格一一对应，不含任何额外元数据键，可直接作为 schema 校验对象。

### 用法

1. 复制对应的模板文件到目标位置，再逐字段填写；不要直接在模板原文件上改写并提交。
2. 模板中的每个占位符都是形如 `REPLACE_ME: ...` 的字符串，**必须全部替换**。
3. 不适用的字段替换为空数组 `[]` 或 `null`，不要删除字段、不要保留占位文本。

### 注意事项

- 模板内的值全部是占位符，**不是真实任务，也不是真实执行结果**。任何以 `REPLACE_ME` 开头的标识、说明或"示例证据"都必须替换。
- **模板不得未经填写直接提交为真实任务或真实执行报告。** 未替换的占位符会污染验收链路，使协调者无法分辨真实证据与占位文本。
- `status` 只能取 `completed`、`partial`、`blocked`、`failed` 之一；模板占位值写明了这四个可选值。
- `tests[].status` 只能取 `passed`、`failed`、`not_run` 之一；**只有真实运行并观察到结果后才可填写 `passed` 或 `failed`**。未运行的测试必须写 `not_run` 并在 `evidence` 中说明原因。
- 字段值未知时写 `null`，**不得用猜测值冒充已验证状态**。例如模板中 `git.working_tree_clean` 的占位值是 `null`，因为填写时该状态尚未核验；核验后才可改为 `true` 或 `false`。
- `git.commit_sha`、`git.pull_request_url` 在对应操作尚未发生时应保持 `null`，不得填写虚构的 SHA 或链接。

## 建议工作流

1. ChatGPT 作为协调者，拆分需求并定义验收标准。
2. 执行 Agent 在独立任务分支中修改文件并运行可用测试。
3. 执行 Agent 返回变更清单、测试证据、风险及结构化报告（见[执行报告模板](templates/agent-report-template.json)）。
4. 协调者进行代码审查并要求修复，确认后再合并。

默认不直接合并到主分支，也不得擅自强制推送、覆盖他人工作或提交凭据。

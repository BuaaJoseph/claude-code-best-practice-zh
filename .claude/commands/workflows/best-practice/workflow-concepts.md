---
description: 通过研究最近几个变更日志版本更新 README CONCEPTS 部分
argument-hint: [要检查的变更日志版本数量，默认为 10]
---

# 工作流变更日志 — README 概念

您是 claude-code-best-practice 项目的协调员。您的工作是并行启动两个研究 agent，等待它们的结果，合并发现，并呈现一份关于 **README CONCEPTS 部分**（`README.md`）漂移的统一报告。

**要检查的版本：** `$ARGUMENTS`（如果为空或不是数字，默认为 10）

这是一个**先读取后报告**的工作流。启动 agent，合并结果，产生报告。只有在用户批准后才采取行动。

---

## 阶段 0：并行启动两个 Agent

**立即**使用 Task 工具在**同一条消息**中并行启动两个 agent：

### Agent 1：workflow-concepts-agent

使用 `subagent_type: "workflow-concepts-agent"` 生成。给它这个提示：

> 研究 claude-code-best-practice 项目的 README CONCEPTS 部分漂移。检查最近 $ARGUMENTS 个版本（默认：10）。
>
> 获取这 3 个外部来源：
> 1. Claude Code 文档索引：https://code.claude.com/docs/en
> 2. Claude Code 变更日志：https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md
> 3. Claude Code 功能概览：https://code.claude.com/docs/en/overview
>
> 然后读取本地 README.md（特别是 CONCEPTS 表）、CLAUDE.md 和 `reports/claude-global-vs-project-settings.md`。分析官方文档列出的 Claude Code 概念/功能与我们 README CONCEPTS 表中记录的差异。返回涵盖缺失概念、变更概念、弃用概念、URL 准确性、描述准确性和徽章准确性的结构化发现报告。

### Agent 2：claude-code-guide

使用 `subagent_type: "claude-code-guide"` 生成。给它这个提示：

> 研究最新的 Claude Code 功能和概念。我需要您找到应该记录的所有 Claude Code 概念/功能的完整列表。对于每个，提供：
> 1. 官方功能名称
> 2. 官方文档 URL
> 3. 文件系统位置（例如 `.claude/commands/`、`~/.claude/teams/`）
> 4. 简要描述（一行）
> 5. 引入时间（如果知道版本/日期）
>
> 具体检查这些可能缺失的概念：
> - **Worktrees** — 用于并行开发的 git worktree 隔离
> - **Agent Teams** — 多 agent 协调
> - **Tasks** — 跨会话的持久任务列表
> - **Auto Memory** — Claude 自己编写的项目学习
> - **Keybindings** — 自定义键盘快捷键
> - **Remote Connections** — SSH、Docker、云开发
> - **IDE Integration** — VS Code、JetBrains 扩展
> - **Model Configuration** — 模型选择和路由
> - **GitHub Integration** — PR 审查、问题分类
> - 来自近期 Claude Code 版本的任何其他概念
>
> 要彻底 — 搜索网络，获取文档，并为您找到的每件事提供具体的版本号和详细信息。

两个 agent 独立运行并返回它们的发现。

---

## 阶段 0.5：读取验证清单

**在 agent 运行期间**，如果存在，读取 `changelog/best-practice/concepts/verification-checklist.md`。此文件包含累积的验证规则。如果尚不存在则跳过此步骤 — 它将在阶段 2 中创建。

---

## 阶段 1：读取之前的变更日志条目

**在合并发现之前**，如果存在，读取文件 `changelog/best-practice/concepts/changelog.md` 以获取之前的变更日志条目。每个条目由 `---` 分隔。解析这些先前条目中的优先级操作，以便您可以将它们与当前发现进行比较。这让您能够识别：
- **重复出现的问题** — 之前出现过但仍未解决的问题
- **新解决的问题** — 之前运行中出现但现在已修复的问题
- **新问题** — 首次在此运行中出现的问题

如果文件尚不存在，所有项目都是 `NEW`。

---

## 阶段 2：合并发现并生成报告

**等待两个 agent 完成。** 获得以下内容后：
- **workflow-concepts-agent 发现** — 带有本地文件读取、外部文档获取和漂移检测的详细分析
- **claude-code-guide 发现** — 关于最新 Claude Code 功能和概念的独立研究

交叉参照两者。专门的 agent 提供 CONCEPTS 特定的漂移分析，而 claude-code-guide agent 可能会发现它遗漏的内容（例如非常近期的变化、未记录的功能或来自网络搜索的上下文）。标记两个 agent 之间的任何矛盾，供用户解决。

**执行验证清单（如果存在）：** 对于 `changelog/best-practice/concepts/verification-checklist.md` 中的每条规则，使用 agent 发现作为源数据执行检查。在报告中包含**验证日志**部分。

**如果需要则更新清单：** 如果一个发现揭示了没有现有清单规则涵盖的新类型漂移，将新规则追加到 `changelog/best-practice/concepts/verification-checklist.md`。如果文件不存在，则创建它。规则必须包括：类别、检查什么、深度级别、与什么源比较、添加日期和来源。

还要将当前发现与之前的变更日志条目（来自阶段 1）进行比较。对于每个优先级操作，将其标记为：
- `NEW` — 此问题首次出现
- `RECURRING` — 在之前的运行中出现过但仍未解决（包括首次出现的运行日期）
- `RESOLVED` — 在之前的运行中出现过但现在已修复（包括解决日期）

生成包含以下部分的结构化报告：

1. **缺失概念** — 官方文档中有但 CONCEPTS 表中缺少的功能/概念，包括：
   - 官方名称和文档 URL
   - 建议的 Location 列值
   - 建议的 Description 列值
   - 精确的 markdown 表格行，可直接粘贴
   - 引入的版本（如果知道）
2. **变更概念** — 名称、URL、位置或描述已变更的概念
3. **弃用/移除的概念** — CONCEPTS 表中有但官方文档中不再有的概念
4. **URL 准确性** — 每个概念的 URL 验证
5. **Description 准确性** — 每个概念的描述/位置验证
6. **徽章准确性** — 徽章链接验证和缺失徽章建议
7. **claude-code-guide Agent 发现** — 该 agent 独有的见解，而专门的 agent 未捕捉到。只包含添加新信息的发现。标记矛盾。

以优先的**操作项**摘要表结束：

```
Priority Actions:
#  | Type                | Action                                     | Status
1  | Missing Concept     | Add <concept> row to CONCEPTS table         | NEW
2  | Changed URL         | Update <concept> docs link                  | NEW
3  | Changed Description | Update <concept> description                | RECURRING (first seen: <date>)
4  | Deprecated Concept  | Remove <concept> row from CONCEPTS table    | NEW
5  | Broken Badge        | Fix badge link for <concept>                | NEW
```

还要包含一个**自上次运行以来已解决**部分，列出之前运行中不再是问题的任何项目。

---

## 阶段 2.5：将摘要追加到变更日志

**此阶段是强制的 — 在向用户呈现报告之前始终执行。**

读取现有的 `changelog/best-practice/concepts/changelog.md` 文件，然后**追加**（不要覆盖）一个新条目在末尾。如果文件不存在，创建它并添加状态图例表然后第一个条目。条目格式必须完全如下：

```markdown
---

## [<YYYY-MM-DD HH:MM AM/PM PKT>] Claude Code v<VERSION>

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH/MED/LOW | <type> | <action description> | <status> |
| ... | ... | ... | ... | ... |
```

**状态格式 — 必须使用以下三种格式之一：**
- `COMPLETE (reason)` — 操作已成功完成并解决
- `INVALID (reason)` — 发现不正确、不适用或是有意为之
- `ON HOLD (reason)` — 操作推迟，等待外部依赖或用户决定

`(reason)` 是强制的，必须简要解释做了什么或为什么。

**追加规则：**
- 始终追加 — 永不覆盖或替换之前的条目
- 日期和时间是命令在巴基斯坦标准时间（PKT，UTC+5）执行的时间；通过运行 `TZ=Asia/Karachi date "+%Y-%m-%d %I:%M %p PKT"` 获取。版本来自 agent 发现
- 每个条目由 `---` 分隔
- **只包含 HIGH、MEDIUM 或 LOW 优先级的项目** — 省略 NONE 优先级的项目

---

## 阶段 2.6：更新最后更新徽章

**此阶段是强制的 — 始终在阶段 2.5 后立即执行，在呈现报告之前。**

更新 `README.md` 顶部的"Last Updated"徽章（第 3 行）。运行 `TZ=Asia/Karachi date "+%b %d, %Y %-I:%M %p PKT"` 获取时间，URL 编码（空格到 `%20`，逗号到 `%2C`），然后替换徽章中的日期部分。

**不要将徽章更新记录为变更日志或报告中的操作项。**

---

## 阶段 2.7：验证所有 CONCEPTS URL

**此阶段是强制的 — 始终在阶段 2.6 后执行，在呈现报告之前。**

对于 CONCEPTS 表中的每个概念：

1. **外部文档 URL**（例如 `https://code.claude.com/docs/en/skills`）：使用 WebFetch 获取每个 URL 并验证它返回有效页面。标记任何失效或已移动的链接。
2. **本地徽章链接**（例如 `best-practice/claude-commands.md`）：使用 Read 工具验证文件存在。标记任何失效的链接。
3. **实现徽章链接**（例如 `.claude/commands/`）：验证路径存在。

在报告中包含**URL 验证日志**：

```
URL Validation Log:
#  | Concept     | URL Type  | URL                                           | Status | Notes
1  | Commands    | External  | https://code.claude.com/docs/en/skills         | OK     |
2  | Commands    | Badge     | best-practice/claude-commands.md               | OK     |
3  | Sub-Agents  | External  | https://code.claude.com/docs/en/sub-agents     | OK     |
...
```

**如果任何 URL 失效**，将它们作为 HIGH 优先级操作项添加。

---

## 阶段 3：提供行动选项

在呈现报告后（并确认变更日志已更新），询问用户：

1. **执行所有操作** — 添加缺失概念，更新变更的概念，移除弃用的概念
2. **执行特定操作** — 用户选择要执行哪些编号
3. **只保存报告** — 不做任何更改

执行时：
- **缺失的概念**：在 `README.md` 的 CONCEPTS 表中添加新行，遵循现有格式：
  ```
  | [**Name**](docs-url) | `location` | Description |
  ```
  只在相应的文件存在时添加徽章（best-practice、implemented）
- **变更的概念**：更新特定变更的列
- **弃用的概念**：确认后再移除行
- **失效的 URL**：将 URL 修复为当前有效的
- **徽章修复**：更新徽章链接为正确的文件路径
- 保持与现有表一致的字母顺序或逻辑顺序
- 所有操作后，重新验证 CONCEPTS 表的一致性

---

## 关键规则

1. **在单条消息中并行启动两个 agent** — 永不顺序
2. **在生成报告之前等待两个 agent**
3. **永不猜测**版本、URL 或日期 — 使用来自 agent 的数据
4. **缺失概念是 HIGH 优先级** — CONCEPTS 表是开发人员看到的第一样东西
5. **验证每个 URL** — 失效的链接会损害整个项目的信任
6. **不要自动执行** — 始终先呈现报告
7. **始终追加到变更日志** — 阶段 2.5 是强制的。永不跳过。永不覆盖之前的条目。
8. **与之前的运行比较** — 从变更日志读取之前的条目，并将每个操作项标记为 NEW、RECURRING 或 RESOLVED。
9. **如果存在则执行验证清单** — 读取 verification-checklist.md 并执行每条规则。如果不存在且有值得持久化规则的发现，则创建文件。
10. **始终更新最后更新徽章** — 阶段 2.6 是强制的。
11. **始终验证所有 CONCEPTS URL** — 阶段 2.7 是强制的。失效的 URL 是 HIGH 优先级。
12. **提供可直接粘贴的行** — 对于缺失概念，包含精确的 markdown 表格行，以便执行时可以直接复制粘贴。
13. **尊重现有表格式** — 匹配现有行的列结构、徽章模式和链接样式。
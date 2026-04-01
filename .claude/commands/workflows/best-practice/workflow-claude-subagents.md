---
description: 跟踪 Claude Code subagents 报告变更并确定需要更新的内容
argument-hint: [要检查的版本数量，默认 10]
---

# 工作流变更日志 — Subagents 报告

你是 claude-code-best-practice 项目的协调员。你的工作是启动一个研究 agent，等待其结果，并生成一份关于 **Subagents 参考报告**（`best-practice/claude-subagents.md`）漂移情况的报告。

此工作流检查 exactly **两种类型的漂移**：
1. **Frontmatter 字段** — 官方文档中添加或删除的任何字段
2. **官方子代理（Sub-agents）** — 添加或删除的任何内置 agent

**要检查的版本：** `$ARGUMENTS`（如果为空或不是数字，默认 10）

这是一个 **先读后报告** 的工作流。启动 agent，合并发现，然后生成报告。只有在用户批准后才采取行动。

---

## 第一阶段：启动研究 Agent

使用以下 prompt 生成 `workflow-claude-subagents-agent`：

> 研究 claude-code-best-practice 项目的 subagents 报告漂移情况。检查最近 $ARGUMENTS 个版本（默认：10）。
>
> 获取以下 2 个外部来源：
> 1. 子代理参考：https://code.claude.com/docs/en/sub-agents
> 2. Changelog：https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md
>
> 然后读取本地报告（`best-practice/claude-subagents.md`）。
>
> 检查 exactly 两件事：
> 1. **Frontmatter 字段**：将官方文档的支持的 frontmatter 字段表与报告的 Frontmatter Fields 表进行比较。标记添加或删除的任何字段。
> 2. **官方子代理（Sub-agents）**：将官方文档的内置子代理列表与报告的 official agents 表进行比较。标记添加或删除的任何 agents。

---

## 第二阶段：读取之前的变更日志条目

**在 agent 运行期间**，读取 `changelog/best-practice/claude-subagents/changelog.md` 以获取最近 25 条目。解析优先级操作以识别：
- **重复出现的问题** — 之前出现过且仍未解决的问题
- **新问题** — 首次出现的问题
- **已解决的问题** — 之前标记的问题现已修复

---

## 第三阶段：生成报告

**等待 agent 完成。** 生成包含以下部分的报告：

1. **Frontmatter 字段变更** — 官方文档与我们的报告相比添加或删除的字段
2. **官方子代理（Sub-agent）变更** — 与我们的表相比添加或删除的内置 agents

以优先级的 **操作项目** 摘要表结束。每个项目必须包含 `Status` 列，显示 `NEW`、`RECURRING (first seen: <date>)` 或 `RESOLVED`：

```
优先级操作：
#  | 类型           | 操作                              | 状态
1  | 新字段        | 将 <field> 添加到 frontmatter 表 | NEW
2  | 已删除字段   | 从表中删除 <field>                  | RECURRING (first seen: <date>)
3  | 新 Agent     | 将 <agent> 添加到 official agents 表  | NEW
4  | 已删除 Agent | 从表中删除 <agent>                  | NEW
```

还包括 **自上次运行以来已解决** 部分，列出之前运行中不再是问题的事项。

---

## 第三阶段半：追加摘要到变更日志

**此阶段是强制性的 — 在向用户展示报告之前必须始终执行。**

读取现有的 `changelog/best-practice/claude-subagents/changelog.md` 文件，然后在末尾 **追加**（不是覆盖）新条目。条目格式必须 exactly 如下：

```markdown
---

## [<YYYY-MM-DD HH:MM AM/PM PKT>] Claude Code v<VERSION>

| # | 优先级 | 类型 | 操作 | 状态 |
|---|----------|------|--------|--------|
| 1 | HIGH/MED/LOW | <类型> | <操作描述> | <状态> |
| ... | ... | ... | ... | ... |
```

**状态格式 — 必须使用以下三种格式之一：**
- `COMPLETE (原因)` — 操作已成功完成并解决
- `INVALID (原因)` — 发现不正确、不适用或故意如此
- `ON HOLD (原因)` — 操作已推迟，等待外部依赖或用户决定

`(原因)` 是强制性的，必须简要解释做了什么或为什么。

**追加规则：**
- 始终追加 — 不要覆盖或替换之前的条目
- 日期和时间是命令在巴基斯坦标准时间 (PKT, UTC+5) 执行的时间；通过运行 `TZ=Asia/Karachi date "+%Y-%m-%d %I:%M %p PKT"` 获取。版本来自 agent 发现
- 如果 `changelog/best-practice/claude-subagents/changelog.md` 不存在或为空，使用状态图例表创建它（请参阅文件顶部）然后添加第一个条目
- 每个条目用 `---` 分隔
- **只包含 HIGH、MEDIUM 或 LOW 优先级的项目** — 忽略 NONE 优先级的项目

---

## 第三阶段六：更新最后更新徽章

**此阶段是强制性的 — 在第三阶段半之后立即执行，在展示报告之前。**

更新 `best-practice/claude-subagents.md` 顶部的 "Last Updated" 徽章。运行 `TZ=Asia/Karachi date "+%b %d, %Y %-I:%M %p PKT"` 获取时间，URL 编码（空格到 `%20`，逗号到 `%2C`），然后替换徽章中的日期部分。如果徽章中的 Claude Code 版本已更改，也应更新它。

**不要将徽章更新记录为变更日志或报告中的操作项目。** 徽章同步是每次运行的常规部分，不是发现。

---

## 第四阶段：提议采取行动

在展示报告后（并确认变更日志和徽章都已更新），询问用户：

1. **执行所有操作** — 应用所有更改
2. **执行特定操作** — 用户选择要执行哪些编号
3. **仅保存报告** — 不更改

执行时：
- **新字段**：使用正确的类型、必填状态和官方文档的描述添加到 Frontmatter Fields 表
- **已删除字段**：在删除前与用户确认
- **新 agents**：使用正确的 #、名称、模型、工具和描述添加到 official agents 表
- **已删除 agents**：在删除前与用户确认

---

## 关键规则

1. **永不超过版本或日期** — 使用 agent 的数据
2. **交叉引用字段计数** — 报告字段计数必须与官方文档匹配
3. **交叉引用 agent 计数** — 报告 agent 计数必须与官方文档匹配
4. **不要自动执行** — 始终先展示报告
5. **始终追加到变更日志** — 第三阶段半是强制性的。永远不要跳过它。永远不要覆盖之前的条目。
6. **始终更新最后更新徽章** — 第三阶段六是强制性的。永远不要跳过它。
7. **与之前运行进行比较** — 读取变更日志中最近的 25 条目并将每个操作项目标记为 NEW、RECURRING 或 RESOLVED。
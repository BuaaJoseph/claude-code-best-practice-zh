---
description: 跟踪 Claude Code 命令报告变更并找出需要更新的内容
argument-hint: [要检查的版本数量，默认为 10]
---

# 工作流变更日志 — 命令报告

您是 claude-code-best-practice 项目的协调员。您的工作是启动一个研究 agent，等待它的结果，并呈现一份关于 **命令参考**报告（`best-practice/claude-commands.md`）漂移的报告。

此工作流正好检查**两种类型的漂移**：
1. **Frontmatter 字段** — 官方文档中添加或移除的任何字段
2. **官方命令** — 添加或移除的任何内置斜杠命令

**要检查的版本：** `$ARGUMENTS`（如果为空或不是数字，默认为 10）

这是一个**先读取后报告**的工作流。启动 agent，合并发现，产生报告。只有在用户批准后才采取行动。

---

## 阶段 1：启动研究 Agent

使用以下提示生成 `workflow-claude-commands-agent`：

> 研究 claude-code-best-practice 项目的命令报告漂移。检查最近 $ARGUMENTS 个版本（默认：10）。
>
> 获取这 2 个外部来源：
> 1. 斜杠命令参考：https://code.claude.com/docs/en/slash-commands
> 2. 变更日志：https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md
>
> 然后读取本地报告（`best-practice/claude-commands.md`）。
>
> 正好检查两件事：
> 1. **Frontmatter 字段**：将官方文档支持的命令 frontmatter 字段与报告的 Frontmatter Fields 表进行比较。标记任何添加或移除的字段。
> 2. **官方命令**：将官方文档的内置斜杠命令列表与报告的 official commands 表进行比较。标记任何添加或移除的命令。还要检查任何命令的标签或描述是否已更改。

---

## 阶段 2：读取之前的变更日志条目

**在 agent 运行期间**，读取 `changelog/best-practice/claude-commands/changelog.md` 以获取最后 25 个条目。解析优先级操作以识别：
- **重复出现的问题** — 之前出现过但仍未解决的问题
- **新问题** — 首次出现的问题
- **已解决的问题** — 之前标记的问题现在已修复

---

## 阶段 3：生成报告

**等待 agent 完成。** 生成包含以下部分 的报告：

1. **Frontmatter 字段变更** — 官方文档与我们报告相比添加或移除的字段
2. **官方命令变更** — 与我们的表相比添加或移除的内置斜杠命令

以优先的**操作项**摘要表结束。每个项目必须包含显示 `NEW`、`RECURRING (first seen: <date>)` 或 `RESOLVED` 的 `Status` 列：

```
Priority Actions:
#  | Type              | Action                                | Status
1  | New Field         | Add <field> to frontmatter table      | NEW
2  | Removed Field     | Remove <field> from table             | RECURRING (first seen: <date>)
3  | New Command       | Add <command> to official table        | NEW
4  | Removed Command   | Remove <command> from table           | NEW
5  | Changed Tag       | Update <command> tag from X to Y      | NEW
```

还要包含一个**自上次运行以来已解决**部分，列出之前运行中不再是问题的任何项目。

---

## 阶段 3.5：将摘要追加到变更日志

**此阶段是强制的 — 在向用户呈现报告之前始终执行。**

读取现有的 `changelog/best-practice/claude-commands/changelog.md` 文件，然后**追加**（不要覆盖）一个新条目在末尾。条目格式必须完全如下：

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
- 如果 `changelog/best-practice/claude-commands/changelog.md` 不存在或为空，创建它并添加状态图例表（参见文件顶部）然后第一个条目
- 每个条目由 `---` 分隔
- **只包含 HIGH、MEDIUM 或 LOW 优先级的项目** — 省略 NONE 优先级的项目

---

## 阶段 3.6：更新最后更新徽章

**此阶段是强制的 — 始终在阶段 3.5 后立即执行，在呈现报告之前。**

更新 `best-practice/claude-commands.md` 顶部的"Last Updated"徽章。运行 `TZ=Asia/Karachi date "+%b %d, %Y %-I:%M %p PKT"` 获取时间，URL 编码（空格到 `%20`，逗号到 `%2C`），然后替换徽章中的日期部分。如果徽章中的 Claude Code 版本已更改，也要更新它。

**不要将徽章更新记录为变更日志或报告中的操作项。** 徽章同步是每次运行的常规部分，不是发现。

---

## 阶段 4：提供行动选项

在呈现报告后（并确认变更日志和徽章都已更新），询问用户：

1. **执行所有操作** — 应用所有更改
2. **执行特定操作** — 用户选择要执行哪些编号
3. **只保存报告** — 不做任何更改

执行时：
- **新字段**：使用官方文档的正确类型、必需状态和描述添加到 Frontmatter Fields 表
- **移除的字段**：确认后再移除
- **新命令**：使用正确的 #、command、tag 和描述添加到 official commands 表。在正确的标签组中插入（表按标签排序）
- **移除的命令**：确认后再移除
- **变更的标签**：更新命令的标签并在需要时重新排序
- 任何添加或移除后，更新 `## Frontmatter Fields (N)` 和 `## ![Official](...) **(N)**` 标题中的计数

---

## 关键规则

1. **永不猜测**版本或日期 — 使用来自 agent 的数据
2. **交叉引用字段计数** — 报告字段计数必须与官方文档匹配
3. **交叉引用命令计数** — 报告命令计数必须与官方文档匹配
4. **不要自动执行** — 始终先呈现报告
5. **始终追加到变更日志** — 阶段 3.5 是强制的。永不跳过。永不覆盖之前的条目。
6. **始终更新最后更新徽章** — 阶段 3.6 是强制的。永不跳过。
7. **与之前的运行比较** — 从变更日志读取最后 25 个条目，并将每个操作项标记为 NEW、RECURRING 或 RESOLVED。
8. **保持标签排序顺序** — 官方命令表按标签（字母顺序）排序，然后在每个标签组内按命令名称排序。在添加或移除命令时保持此顺序。
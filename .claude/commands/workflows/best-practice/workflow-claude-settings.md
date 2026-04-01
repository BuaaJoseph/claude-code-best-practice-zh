---
description: 跟踪 Claude Code 设置报告变更并找出需要更新的内容
argument-hint: [要检查的版本数量，默认为 10]
---

# 工作流变更日志 — 设置报告

您是 claude-code-best-practice 项目的协调员。您的工作是并行启动两个研究 agent，等待它们的结果，合并发现，并呈现一份关于 **设置参考**报告（`best-practice/claude-settings.md`）漂移的统一报告。

**要检查的版本：** `$ARGUMENTS`（如果为空或不是数字，默认为 10）

这是一个**先读取后报告**的工作流。启动 agent，合并结果，产生报告。只有在用户批准后才采取行动。

---

## 阶段 0：并行启动两个 Agent

**立即**使用 Task 工具在**同一条消息**中并行启动两个 agent：

### Agent 1：workflow-claude-settings-agent

使用 `subagent_type: "workflow-claude-settings-agent"` 生成。给它这个提示：

> 研究 claude-code-best-practice 项目的设置报告漂移。检查最近 $ARGUMENTS 个版本（默认：10）。
>
> 获取这 3 个外部来源：
> 1. 设置文档：https://code.claude.com/docs/en/settings
> 2. CLI 参考：https://code.claude.com/docs/en/cli-reference
> 3. 变更日志：https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md
>
> 然后读取本地报告文件（`best-practice/claude-settings.md`）和 CLAUDE.md 文件。分析官方文档关于设置键、权限语法、钩子事件、MCP 配置、沙箱选项、插件设置、模型别名、显示设置和环境变量与我们报告记录的内容之间的差异。返回涵盖缺失设置、变更类型/默认值、新设置添加、弃用设置、权限语法变更、钩子事件变更、MCP 设置变更、沙箱设置变更、环境变量完整性、示例准确性、设置层次结构准确性和源有效性的结构化发现报告。

### Agent 2：claude-code-guide

使用 `subagent_type: "claude-code-guide"` 生成。给它这个提示：

> 研究最新的 Claude Code 设置系统。我需要您找到：
> 1. 当前所有支持的 settings.json 键的完整列表及其类型、默认值和描述
> 2. 近期 Claude Code 版本中引入的任何新设置键
> 3. 现有设置行为的变更（例如新的权限模式、新的钩子事件、新的沙箱选项）
> 4. 设置层次的变更（新的优先级级别、新的文件位置）
> 5. 权限语法的变更（新的工具模式、新的通配符行为）
> 6. 新的钩子事件或钩子配置结构的变更
> 7. MCP 服务器配置的变更（新的匹配字段、新的设置）
> 8. 沙箱设置的变更（新的网络选项、新的命令）
> 9. 插件配置的变更（新的字段、新的市场选项）
> 10. 环境变量的变更（新的变量、弃用的变量、变更的行为）
> 11. 模型别名或模型配置的变更
> 12. 显示/用户体验设置的变更（状态行、微调器、进度条）
> 13. 任何设置键的弃用或移除
>
> 要彻底 — 搜索网络，获取文档，并为您找到的每件事提供具体的版本号和详细信息。

两个 agent 独立运行并返回它们的发现。

---

## 阶段 0.5：读取验证清单

**在 agent 运行期间**，读取 `changelog/best-practice/claude-settings/verification-checklist.md`。此文件包含累积的验证规则 — 每条规则指定检查什么、到什么深度、以及与哪个源比较。在阶段 2 期间必须执行每条规则。清单是项目用于漂移检测的回归测试套件。

---

## 阶段 1：读取之前的变更日志条目

**在合并发现之前**，读取文件 `changelog/best-practice/claude-settings/changelog.md` 以获取最后 25 个变更日志条目。每个条目由 `---` 分隔。解析这些先前条目中的优先级操作，以便您可以将它们与当前发现进行比较。这让您能够识别：
- **重复出现的问题** — 之前出现过但仍未解决的问题
- **新解决的问题** — 之前运行中出现但现在已修复的问题
- **新问题** — 首次在此运行中出现的问题

---

## 阶段 2：合并发现并生成报告

**等待两个 agent 完成。** 获得以下内容后：
- **workflow-claude-settings-agent 发现** — 带有本地文件读取、外部文档获取和漂移检测的详细报告分析
- **claude-code-guide 发现** — 关于最新 Claude Code 设置功能和变更的独立研究

交叉参照两者。专门的 agent 提供报告特定的漂移分析，而 claude-code-guide agent 可能会发现它遗漏的内容（例如非常近期的变化、未记录的功能或来自网络搜索的上下文）。标记两个 agent 之间的任何矛盾，供用户解决。

**执行验证清单：** 对于 `changelog/best-practice/claude-settings/verification-checklist.md` 中的每条规则，使用 agent 发现作为源数据按指定深度执行检查。在报告中包含**验证日志**部分，显示每条规则的结果：

```
Verification Log:
Rule # | Category              | Depth         | Result | Notes
1      | Settings Keys         | field-level   | PASS   | All keys match
2      | Permission Syntax     | content-match | FAIL   | New tool pattern added
...
```

**如果需要则更新清单：** 如果一个发现揭示了没有现有清单规则涵盖的新类型漂移（或深度不足），将新规则追加到 `changelog/best-practice/claude-settings/verification-checklist.md`。规则必须包括：类别、检查什么、深度级别、与什么源比较、添加日期和来源（什么错误促成了此规则）。不要为不会重复的一次性问题添加规则。

还要将当前发现与之前的变更日志条目（来自阶段 1）进行比较。对于每个优先级操作，将其标记为：
- `NEW` — 此问题首次出现
- `RECURRING` — 在之前的运行中出现过但仍未解决（包括首次出现的运行日期）
- `RESOLVED` — 在之前的运行中出现过但现在已修复（包括解决日期）

生成包含以下部分的结构化报告：

1. **新设置键** — 官方文档中有但报告中缺少的键，附带引入版本
2. **变更设置行为** — 类型、默认值或描述已变更的设置
3. **弃用/移除的设置** — 报告中有但官方文档中不再有的设置
4. **权限语法变更** — 新的工具模式、通配符行为或权限模式变更
5. **MCP 设置变更** — 新的 MCP 配置键、匹配行为或服务器设置
6. **沙箱设置变更** — 新的沙箱选项、网络设置或命令排除
7. **插件设置变更** — 新的插件配置键或市场选项
8. **模型配置变更** — 新的模型别名、工作级别或模型环境变量
9. **显示和用户体验变更** — 新的状态行字段、微调器选项或显示设置
10. **环境变量完整性** — 官方文档中有但报告中缺少的变量，或报告中不再有文档的变量
11. **设置层次结构准确性** — 验证优先级级别、文件位置和覆盖行为
12. **示例准确性** — 快速参考完整示例是否反映当前设置
13. **源准确性** — 验证所有源链接有效并指向正确的文档
14. **claude-code-guide Agent 发现** — 该 agent 独有的见解，而专门的 agent 未捕捉到。只包含添加新信息的发现。如果两个 agent 之间有矛盾，标记它们供用户解决。不要列出"已确认的协议"。

> **注意：** 钩子相关分析（事件、属性、匹配器、退出代码、HTTP 钩子、钩子环境变量）**从此工作流中排除**。钩子维护在 [claude-code-hooks](https://github.com/shanraisshan/claude-code-hooks) 仓库中。

以优先的**操作项**摘要表结束。每个项目必须包含显示 `NEW`、`RECURRING (first seen: <date>)` 或 `RESOLVED` 的 `Status` 列：

```
Priority Actions:
#  | Type                  | Action                                    | Status
1  | New Setting           | Add <key> to <section> table               | NEW
2  | Changed Behavior      | Update <key> description                   | NEW
3  | Deprecated Setting    | Remove <key> from table                    | RECURRING (first seen: 2026-03-05)
4  | Permission Syntax     | Add new tool pattern syntax                | NEW
5  | Env Variable          | Add <var> to environment variables table   | NEW
7  | Example Update        | Update Quick Reference example             | NEW
```

还要包含一个**自上次运行以来已解决**部分，列出之前运行中不再是问题的任何项目。

---

## 阶段 2.5：将摘要追加到变更日志

**此阶段是强制的 — 在向用户呈现报告之前始终执行。**

读取现有的 `changelog/best-practice/claude-settings/changelog.md` 文件，然后**追加**（不要覆盖）一个新条目在末尾。条目格式必须完全如下：

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
- 如果 `changelog/best-practice/claude-settings/changelog.md` 不存在或为空，创建它并添加状态图例表（参见文件顶部）然后第一个条目
- 每个条目由 `---` 分隔
- **只包含 HIGH、MEDIUM 或 LOW 优先级的项目** — 省略 NONE 优先级的项目（不需要操作的事项）

---

## 阶段 2.6：更新最后更新徽章

**此阶段是强制的 — 始终在阶段 2.5 后立即执行，在呈现报告之前。**

更新 `best-practice/claude-settings.md` 顶部的"Last Updated"徽章。运行 `TZ=Asia/Karachi date "+%b %d, %Y %-I:%M %p PKT"` 获取时间，URL 编码（空格到 `%20`，逗号到 `%2C`），然后替换徽章中的日期部分。如果徽章中的 Claude Code 版本已更改，也要更新它。

**不要将徽章更新记录为变更日志或报告中的操作项。** 徽章同步是每次运行的常规部分，不是发现。

---

## 阶段 2.7：验证所有超链接

**此阶段是强制的 — 始终在阶段 2.6 后执行，在呈现报告之前。**

扫描 `best-practice/claude-settings.md` 中的每个超链接（无论是 markdown `[text](url)` 还是内联 URL）。对于每个链接：

1. **本地文件链接**（相对路径）：使用 Read 工具验证文件在解析路径处存在。标记任何失效的链接。
2. **外部 URL**（例如 `https://code.claude.com/docs/en/settings`）：使用 WebFetch 获取每个 URL 并验证它返回有效页面（不是 404 或重定向到错误页面）。标记任何失效或已移动的链接。
3. **锚点链接**（例如 `#section-name`）：验证目标标题在同一文件中存在。

在报告中包含**超链接验证日志**：

```
Hyperlink Validation Log:
#  | Type     | Link                                          | Status | Notes
1  | Local    | ../                                            | OK     |
2  | External | https://code.claude.com/docs/en/settings       | OK     |
3  | External | https://www.schemastore.org/claude-code-settings.json | BROKEN | 404
...
```

**如果任何链接失效**，将它们作为 HIGH 优先级操作项添加。失效的链接会降低报告的实用性，必须在任何其他更改之前修复。

---

## 阶段 3：提供行动选项

在呈现报告后（并确认变更日志和徽章都已更新），询问用户：

1. **执行所有操作** — 处理所有事项（添加缺失设置、更新描述、修复示例）
2. **执行特定操作** — 用户选择要执行哪些编号
3. **只保存报告** — 不做任何更改

执行时：
- **新设置**：使用正确的类型、默认值和描述添加到适当的章节表
- **变更的行为**：更新表中设置的描述或默认值
- **弃用的设置**：确认后再移除
- **权限语法变更**：使用新模式更新权限语法表
- **MCP 设置变更**：更新 MCP 设置部分
- **沙箱设置变更**：更新沙箱设置部分
- **插件设置变更**：更新插件设置部分
- **模型变更**：更新模型配置部分
- **显示变更**：更新显示和用户体验部分
- **环境变量变更**：在环境变量部分添加/更新/移除变量
- **设置层次结构变更**：更新设置层次结构表
- **示例更新**：更新快速参考完整示例以反映当前设置
- **失效的链接**：修复或替换失效的 URL
- 所有操作后，重新运行验证以确认一致性

---

## 关键规则

1. **在单条消息中并行启动两个 agent** — 永不顺序
2. **在生成报告之前等待两个 agent**
3. **永不猜测**版本或日期 — 使用来自 agent 的数据
4. **新设置键是 HIGH 优先级** — 它们需要表和示例更新
5. **交叉引用设置计数** — 每个表中的设置数量必须与官方文档匹配
6. **不要自动执行** — 始终先呈现报告
7. **始终追加到变更日志** — 阶段 2.5 是强制的。永不跳过。永不覆盖之前的条目。
8. **与之前的运行比较** — 从变更日志读取最后 25 个条目，并将每个操作项标记为 NEW、RECURRING 或 RESOLVED。
9. **始终执行验证清单** — 读取 verification-checklist.md 并执行每条规则。在报告中包含验证日志。当发现新类型的漂移时追加新规则。
10. **清单规则是追加-only** — 永不移除或削弱现有规则。只能添加新规则或升级深度级别。
11. **始终更新最后更新徽章** — 阶段 2.6 是强制的。永不跳过。
12. **始终验证所有超链接** — 阶段 2.7 是强制的。永不跳过。失效的链接是 HIGH 优先级。
13. **环境变量分跨两个文件** — `claude-settings.md` 拥有 `env` 可配置的变量；`claude-cli-startup-flags.md` 拥有仅启动的变量。如果变量属于 CLI 文件，不要将环境变量标记为缺失。交叉引用 `best-practice/claude-cli-startup-flags.md` 以验证所有权边界。
14. **验证设置层次结构** — 5 级覆盖链加上托管策略层必须与官方文档完全匹配。
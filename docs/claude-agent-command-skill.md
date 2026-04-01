# 子代理、命令与技能——何时使用什么

Claude Code 中三种扩展机制的对比：子代理、命令和技能。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

![Slash 菜单显示 time-skill、time-command 和 time-agent](assets/agent-command-skill-1.jpg)

---

## 一览

| | 代理 | 命令 | 技能 |
|---|---|---|---|
| **位置** | `.claude/agents/<名称>.md` | `.claude/commands/<名称>.md` | `.claude/skills/<名称>/SKILL.md` |
| **上下文** | 独立子代理进程 | 内联（主对话） | 内联（主对话） |
| **用户可调用** | 否 `/` 菜单 — 由 Claude 或代理工具调用 | 是 — `/command-name` | 是 — `/skill-name`（除非 `user-invocable: false`） |
| **Claude 自动调用** | 是 — 通过 `description` 字段 | 否 | 是 — 通过 `description` 字段（除非 `disable-model-invocation: true`） |
| **接受参数** | 通过 `prompt` 参数 | `$ARGUMENTS`、`$0`、`$1` | `$ARGUMENTS`、`$0`、`$1` |
| **动态上下文注入** | 否 | 是 — `` !`command` `` | 是 — `` !`command` `` |
| **独立上下文窗口** | 是 — 隔离 | 否 — 共享主会话 | 否 — 共享主会话（除非 `context: fork`） |
| **模型覆盖** | `model:` 前matter | `model:` 前matter | `model:` 前matter |
| **工具限制** | `tools:` / `disallowedTools:` | `allowed-tools:` | `allowed-tools:` |
| **Hooks** | `hooks:` 前matter | — | `hooks:` 前matter |
| **内存** | `memory:` 前matter（user/project/local） | — | — |
| **可预加载技能** | 是 — `skills:` 前matter | — | — |
| **MCP 服务器** | `mcpServers:` 前matter | — | — |

---

## 何时使用每个

### 使用代理当：

- 任务需要**自主多步骤** — 代理需要探索、决策和行动而不需要持续指导
- 需要**上下文隔离** — 工作不应污染主对话窗口
- 代理需要**跨会话持久化内存**（例如学习模式的代码审查者）
- 希望通过技能**预加载领域知识**而不弄乱主上下文
- 任务受益于**后台运行**或在 **git worktree** 中运行
- 需要**工具限制**或不同的**权限模式**（例如 `acceptEdits`、`plan`）

**示例**：`weather-agent` — 使用预加载的 `weather-fetcher` 技能自主获取天气数据，在独立上下文中运行并限制工具。

### 使用命令当：

- 需要**用户主动触发的入口点** — 用户明确触发的工作流
- 工作流涉及**编排**其他代理或技能
- 希望**保持上下文精简** — 命令内容在用户触发前不注入会话上下文

**示例**：`weather-orchestrator` — 用户触发它，询问 C/F 偏好，调用代理，然后调用 SVG 技能。

### 使用技能当：

- 希望 **Claude 根据用户意图自动调用** — 技能描述被注入会话上下文进行语义匹配
- 任务是一个可从多个地方调用的**可重用过程**（命令、代理或 Claude 本身）
- 需要**代理预加载** — 在启动时将领域知识烘焙到特定代理中

**示例**：`weather-svg-creator` — 当用户询问天气卡片时 Claude 自动调用它；也可从命令调用。

---

## 命令 → 代理 → 技能架构

该仓库展示了分层编排模式：

```
用户触发 /command
    ↓
Command 编排工作流
    ↓
Command 调用代理（独立上下文，自主）
    ↓
Agent 使用预加载技能（领域知识）
    ↓
Command 调用技能（内联，用于输出生成）
```

**具体示例** — 天气系统：

```
/weather-orchestrator（命令 — 入口点，询问 C/F）
    ↓
weather-agent（代理 — 自主获取温度）
    ├── weather-fetcher（代理技能 — 预加载 API 指令）
    ↓
weather-svg-creator（技能 — 内联创建 SVG）
```

---

## 前matter 对比

### 代理前matter

```yaml
---
name: my-agent
description: 当...时主动使用此代理
tools: Read, Write, Edit, Bash
model: sonnet
maxTurns: 10
permissionMode: acceptEdits
memory: user
skills:
  - my-skill
---
```

### 命令前matter

```yaml
---
description: 做一些有用的事
argument-hint: [issue-number]
allowed-tools: Read, Edit, Bash(gh *)
model: sonnet
---
```

### 技能前matter

```yaml
---
name: my-skill
description: 当用户询问...时做...
argument-hint: [file-path]
disable-model-invocation: false
user-invocable: true
allowed-tools: Read, Grep, Glob
model: sonnet
context: fork
agent: general-purpose
---
```

---

## 关键区别

### 自动调用

| 机制 | Claude 可自动调用？ | 如何防止 |
|-----------|------------------------|----------------|
| 代理 | 是 — 通过 `description`（使用"PROACTIVELY"鼓励） | 移除或软化描述 |
| 命令 | 否 — 始终由用户通过 `/` 发起 | 不适用 |
| 技能 | 是 — 通过 `description` | 设置 `disable-model-invocation: true` |

### 在 `/` 菜单中的可见性

| 机制 | 出现在 `/` 菜单？ | 如何隐藏 |
|-----------|---------------------|-------------|
| 代理 | 否 | 不适用 |
| 命令 | 是 — 始终 | 无法隐藏 |
| 技能 | 是 — 默认 | 设置 `user-invocable: false` |

### 上下文隔离

| 机制 | 在自己的上下文中运行？ | 如何配置 |
|-----------|---------------------|-----------------|
| 代理 | 始终 | 内置行为 |
| 命令 | 从不 | 不适用 |
| 技能 | 可选 | 设置 `context: fork` |

---

## 工作示例："现在几点？"

该仓库为同一任务定义了所有三种机制 — 以 PKT 显示当前时间。当用户输入**"现在几点？"**而未明确调用任何 `/` 命令时：

| 机制 |会触发？| 为什么/为什么不 |
|-----------|--------------|---------------|
| `time-command` | 否 | 命令**永远不会自动触发**。用户需要明确输入 `/time-command` 才能运行。命令没有自动发现路径 —— 它们严格由用户发起。 |
| `time-agent` | **可能** | 代理的 `description` 说*"使用此代理以巴基斯坦标准时间显示当前时间"*。Claude 匹配用户意图并可能通过代理工具生成它。但是，代理在**独立上下文窗口**中运行，使得这个简单任务变得过于重量。 |
| `time-skill` | **最可能** | 技能的 `description` 说*"以巴基斯坦标准时间（PKT，UTC+5）显示当前时间。当用户询问当前时间、巴基斯坦时间或 PKT 时使用。"* Claude 匹配并通过技能工具调用它。由于它**内联**运行且无上下文开销，是最有效的匹配。 |

### 解析顺序

当多个机制匹配同一意图时，Claude 偏好**最轻量**满足请求的选项：

```
1. 技能（内联，无上下文开销）     ← 首选
2. 代理（独立上下文，自主）    ← 如果技能不可用或任务复杂则使用
3. 命令（从不 —— 需要明确 /）   ← 仅当用户输入 /time-command
```

### 如果技能上设置 `disable-model-invocation: true` 会怎样？

那么 Claude **无法**自动调用技能。代理成为唯一可自动调用的选项，所以 Claude 会生成 `time-agent` —— 但代价是一个一行 bash 命令需要独立上下文窗口。

### 如果技能和代理都禁用了自动调用会怎样？

那么**没有什么自动触发**。Claude 会回退到自己的常识，可能会直接运行 `TZ='Asia/Karachi' date` —— 不涉及任何扩展机制。用户需要明确输入 `/time-command` 或 `/time-skill` 来使用其中一个。

![Claude 在用户询问"现在几��？"时自动调用 time-skill](assets/agent-command-skill-2.png)

---

## 来源

- [Claude Code 技能 — 文档](https://code.claude.com/docs/en/skills)
- [Claude Code 子代理 — 文档](https://code.claude.com/docs/en/sub-agents)
- [Claude Code 斜杠命令 — 文档](https://code.claude.com/docs/en/slash-commands)
- [技能最佳实践](../best-practice/claude-skills.md)
- [命令最佳实践](../best-practice/claude-commands.md)
- [子代理最佳实践](../best-practice/claude-subagents.md)
---
description: 通过并行研究所有 9 个工作流仓库来更新 DEVELOPMENT WORKFLOWS 表
---

# 工作流 — 开发工作流

通过并行研究 9 个仓库来更新 `README.md` 中的 DEVELOPMENT WORKFLOWS 表。启动 agent，合并结果，呈现更改，在批准后更新表。

---

## 9 个仓库

| # | 仓库 | 所有者 |
|---|------|-------|
| 1 | `github/spec-kit` | GitHub (John Lam / Den Delimarsky) |
| 2 | `Fission-AI/OpenSpec` | Fission-AI (@0xTab) |
| 3 | `humanlayer/humanlayer` | HumanLayer (Dex Horthy) |
| 4 | `affaan-m/everything-claude-code` | Affaan Mustafa |
| 5 | `gsd-build/get-shit-done` | Lex Christopherson |
| 6 | `obra/superpowers` | Jesse Vincent |
| 7 | `garrytan/gstack` | Garry Tan (YC CEO) |
| 8 | `bmad-code-org/BMAD-METHOD` | BMAD Code Org |
| 9 | `EveryInc/compound-engineering-plugin` | Every.to |

---

## 表格式

README 表有以下列：

```markdown
| Name | ★ | Uniqueness | Plan | <img src="!/tags/a.svg" height="14"> | <img src="!/tags/c.svg" height="14"> | <img src="!/tags/s.svg" height="14"> |
```

- **Name**： `[Short Name](github-url)` — 使用项目名称，而不是所有者/仓库
- **★**：星数四舍五入到 `k`（例如 98k、10k、4.1k）。低于 1000 显示精确数字
- **Uniqueness**：使用 `![tag](https://img.shields.io/badge/TAG-ddf4ff)` 的 2-3 个 shields.io 徽章标签。空格用下划线，连字符用 `--`，`+` 用 `%2C`，`/` 用 `%2F`
- **Plan**：Plan 实现的图标 + 链接名称。命令用 `<img src="!/tags/c.svg" height="14">`，agent 用 `<img src="!/tags/a.svg" height="14">`，skill 用 `<img src="!/tags/s.svg" height="14">`。名称链接到仓库中的实际文件
- **Agent/Command/Skill 数量**：只是数字（例如 `25`、`0`、`108+`）

**排序顺序**：按星数降序排序（最高的在前）。不要按 Plan 类型分组。

---

## 阶段 0：读取当前状态

读取这些文件：

1. `README.md` — `## ⚙️ DEVELOPMENT WORKFLOWS` 表（注意当前的星数、标签、Plan 链接、数量）
2. `changelog/development-workflows/changelog.md` — 之前的变更日志条目

---

## 阶段 1：启动 2 个研究 Agent

**立即**在**单条消息**中并行启动两个 agent。每个使用 `subagent_type: "development-workflows-research-agent"`。

### Agent 1（3 个仓库）

> 研究这 3 个 Claude Code 工作流仓库：
>
> **Repo 1: github/spec-kit** (https://github.com/github/spec-kit)
> **Repo 2: affaan-m/everything-claude-code** (https://github.com/affaan-m/everything-claude-code)
> **Repo 3: obra/superpowers** (https://github.com/obra/superpowers)
>
> 对每个仓库，返回：
>
> 1. **星数** — 使用 GitHub API `https://api.github.com/repos/{owner}/{repo}`，读取 `stargazers_count`。四舍五入到 `k`。
> 2. **Agent 数量** — 统计 `agents/` 或 `.claude/agents/` 中的 `.md` 文件。对于 obra，还要统计 skill 调度的隐式子代理。
> 3. **Skill 数量** — 统计 `skills/` 或 `.claude/skills/` 中的文件夹。
> 4. **Command 数量** — 统计 `commands/` 或 `.claude/commands/` 中的 `.md` 文件。对于 spec-kit，统计 `templates/commands/` 中的文件。
> 5. **Plan 实现** — 找到 Plan/规划 agent、skill 或 command。返回其名称、类型（agent/skill/command）和文件路径。
> 6. **独特性标签** — 2-3 个简短标签（每个 2-3 个词），描述这个工作流的独特之处。
> 7. **显著变化** — 有什么重大近期变化吗？新的 agent/skill/command？主要版本？
>
> 按仓库返回结构化报告：
> ```
> REPO: github/spec-kit
> STARS: <number>k
> AGENTS: <count>
> COMMANDS: <count>
> SKILLS: <count>
> PLAN: <name> (<type>) — <file-path>
> TAGS: <tag1>, <tag2>, <tag3>
> CHANGES: <changes or "No significant changes">
> ```

### Agent 2（6 个仓库）

> 研究这 6 个 Claude Code 工作流仓库：
>
> **Repo 1: Fission-AI/OpenSpec** (https://github.com/Fission-AI/OpenSpec)
> **Repo 2: humanlayer/humanlayer** (https://github.com/humanlayer/humanlayer)
> **Repo 3: gsd-build/get-shit-done** (https://github.com/gsd-build/get-shit-done)
> **Repo 4: garrytan/gstack** (https://github.com/garrytan/gstack)
> **Repo 5: bmad-code-org/BMAD-METHOD** (https://github.com/bmad-code-org/BMAD-METHOD)
> **Repo 6: EveryInc/compound-engineering-plugin** (https://github.com/EveryInc/compound-engineering-plugin)
>
> 对每个仓库，返回：
>
> 1. **星数** — 使用 GitHub API `https://api.github.com/repos/{owner}/{repo}`，读取 `stargazers_count`。四舍五入到 `k`。
> 2. **Agent 数量** — 统计 `agents/` 或 `.claude/agents/` 中的 `.md` 文件。对于 BMAD，统计 `src/bmm-skills/` 中的 agent-persona skill。对于 compound-engineering-plugin，统计 `plugins/compound-engineering/agents/` 所有子目录中的 `.md` 文件。
> 3. **Skill 数量** — 统计 `skills/` 或 `.claude/skills/` 中的文件夹。对于 gstack，skill 是带有 SKILL.md 的根级目录。对于 BMAD，统计 `src/bmm-skills/` 和 `src/core-skills/` 中的所有 skill。对于 compound-engineering-plugin，统计 `plugins/compound-engineering/skills/` 加上 `plugins/coding-tutor/skills/` 中的文件夹。
> 4. **Command 数量** — 统计 `commands/` 或 `.claude/commands/` 中的 `.md` 文件。对于 GSD，统计 `commands/gsd/` 中的。对于 OpenSpec，统计 `/opsx:*` 命令。对于 BMAD，数量为 0（命令在安装时生成）。对于 compound-engineering-plugin，统计 `.claude/commands/` 加上 `plugins/coding-tutor/commands/` 中的 `.md` 文件。
> 5. **Plan 实现** — 找到 Plan/规划 agent、skill 或 command。返回其名称、类型（agent/skill/command）和文件路径。
> 6. **独特性标签** — 2-3 个简短标签（每个 2-3 个词），描述这个工作流的独特之处。
> 7. **显著变化** — 有什么重大近期变化吗？新的 agent/skill/command？主要版本？
>
> 按仓库返回结构化报告：
> ```
> REPO: Fission-AI/OpenSpec
> STARS: <number>k
> AGENTS: <count>
> COMMANDS: <count>
> SKILLS: <count>
> PLAN: <name> (<type>) — <file-path>
> TAGS: <tag1>, <tag2>, <tag3>
> CHANGES: <changes or "No significant changes">
> ```

---

## 阶段 2：比较和报告

**等待两个 agent**。然后将发现与当前表比较并呈现：

```
Development Workflows — Update Report
══════════════════════════════════════

Changes Found:
  <repo>: ★ <old>k → <new>k | agents <old>→<new> | commands <old>→<new> | skills <old>→<new>
  <repo>: tags updated: <old tags> → <new tags>
  <repo>: Plan link changed: <old> → <new>
  ...

No Changes:
  <repo>: ✓ (all values match)
  ...

Action Items:
#  | Type        | Action                              | Status
1  | Star        | Update <repo> ★ from Xk to Yk       | NEW/RECURRING
2  | Count       | Update <repo> agents from X to Y     | NEW/RECURRING
3  | Tags        | Update <repo> tags                   | NEW/RECURRING
4  | Plan        | Update <repo> Plan link              | NEW/RECURRING
5  | Sort        | Move <repo> (Plan type changed)      | NEW/RECURRING
```

与之前的变更日志条目比较，并将项目标记为 `NEW`、`RECURRING` 或 `RESOLVED`。

---

## 阶段 2.5：追加到变更日志

**强制** — 在呈现给用户之前始终执行。

读取 `changelog/development-workflows/changelog.md`，然后**追加**一个新条目。如果文件不存在，创建它并添加状态图例然后第一个条目。

```markdown
---

## [<YYYY-MM-DD HH:MM AM/PM PKT>] Development Workflows Update

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH/MED/LOW | <type> | <action> | <status> |
```

通过 `TZ=Asia/Karachi date "+%Y-%m-%d %I:%M %p PKT"` 获取时间。状态必须是以下之一：
- `COMPLETE (reason)` | `INVALID (reason)` | `ON HOLD (reason)`

始终追加，永不覆盖。

---

## 阶段 2.6：更新最后更新徽章

**强制** — 在阶段 2.5 后执行。

更新 `README.md` 第 4 行的徽章。通过 `TZ=Asia/Karachi date "+%b %d, %Y %-I:%M %p PKT"` 获取时间，URL 编码，然后替换徽章中的日期。不要将此记录为操作项。

---

## 阶段 3：执行

询问用户：**(1) 执行全部** | **(2) 执行特定** | **(3) 跳过**

执行时，编辑 `README.md` 中的 `## ⚙️ DEVELOPMENT WORKFLOWS` 表：
- 按行更新星数、标签、Plan 链接、数量
- 保持排序顺序：星数降序（最高的在前）。不要按 Plan 类型分组
- 完全匹配现有格式（图标、徽章 URL、链接样式）

---

## 规则

1. **并行启动两个 agent** — 单条消息，永不顺序
2. **永不猜测** — 只使用来自 agent 的数据
3. **不要自动执行** — 先呈现报告，等待批准
4. **始终追加变更日志** 和 **始终更新徽章** — 强制
5. **按星数降序排序** — 最高的星数在前，不要按 Plan 类型分组
6. **标签使用 shields.io** — `![tag](https://img.shields.io/badge/TAG-ddf4ff)`，空格用 `_`，连字符用 `--`
7. **Plan 链接必须指向实际文件** — 不是仓库根
8. **Agent、command、skill 是不同的** — 从各自的目录计数，不要混淆
9. **一致地四舍五入星数** — `k` 后缀（98k、10k、4.1k）。低于 1000 显示精确数字
10. **与之前的变更日志比较** — 将项目标记为 NEW、RECURRING 或 RESOLVED
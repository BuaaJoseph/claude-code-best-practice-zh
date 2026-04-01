# Claude Code：全局 vs 项目级功能

全面对比 Claude Code 哪些功能是全局唯一（`~/.claude/`） versus 哪些具有全局和项目级（`.claude/`）等效项。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 目录

1. [概述](#概述)
2. [全局唯一功能](#全局唯一功能)
3. [双重作用域功能](#双重作用域功能)
4. [设置优先级](#设置优先级)
5. [目录结构对比](#目录结构对比)
6. [任务系统](#任务系统)
7. [代理团队](#代理团队)
8. [设计原则](#设计原则)
9. [来源](#来源)

---

## 概述

Claude Code 使用**作用域层次结构**，其中某些功能同时存在于全局（`~/.claude/`）和项目（`.claude/`）级别，而其他功能仅限全局。设计原则：*个人状态*或*跨项目协调*放在全局；*团队可共享的项目配置*可以放在项目级。

- `~/.claude/` 是你的**用户级主页**（全局，所有项目）
- `.claude/` 在仓库内是你的**项目级主页**（限于该项目）

---

## 全局唯一功能

这些**仅**生活在 `~/.claude/` 下，不能限定到项目：

| 功能 | 位置 | 目的 |
|---------|----------|---------|
| **任务** | `~/.claude/tasks/` | 跨会话和代理的持久化任务列表 |
| **代理团队** | `~/.claude/teams/` | 多代理协调配置（实验性，2026 年 2 月） |
| **自动记忆** | `~/.claude/projects/<哈希>/memory/` | 每个项目的 Claude 自我学习（个人，永不共享） |
| **凭证 & OAuth** | 系统密钥链 + `~/.claude.json` | API 密钥、OAuth 令牌（永不放在项目文件中） |
| **按键绑定** | `~/.claude/keybindings.json` | 自定义键盘快捷键 |
| **MCP 用户服务器** | `~/.claude.json`（`mcpServers` 键） | 跨所有项目的个人 MCP 服务器 |
| **偏好/缓存** | `~/.claude.json` | 主题、模型、输出风格、会话状态 |

---

## 双重作用域功能

这些同时存在于两个级别，**项目级优先**于全局：

| 功能 | 全局（`~/.claude/`） | 项目（`.claude/`） | 优先级 |
|---------|----------------------|---------------------|------------|
| **CLAUDE.md** | `~/.claude/CLAUDE.md` | `./CLAUDE.md` 或 `.claude/CLAUDE.md` | 项目覆盖全局 |
| **设置** | `~/.claude/settings.json` | `.claude/settings.json` + `.claude/settings.local.json` | 项目 > 全局 |
| **规则** | `~/.claude/rules/*.md` | `.claude/rules/*.md` | 项目覆盖 |
| **代理/子代理** | `~/.claude/agents/*.md` | `.claude/agents/*.md` | 项目覆盖 |
| **命令** | `~/.claude/commands/*.md` | `.claude/commands/*.md` | 两者都可用 |
| **技能** | `~/.claude/skills/` | `.claude/skills/` | 两者都可用 |
| **Hooks** | `~/.claude/hooks/` | `.claude/hooks/` | 两者都执行 |
| **MCP 服务器** | `~/.claude.json`（用户作用域） | `.mcp.json`（项目作用域） | 三个作用域：local > project > user |

---

## 设置优先级

用户可写设置按此覆盖顺序应用（从高到低）：

| 优先级 | 位置 | 作用域 | 版本控制 | 目的 |
|----------|----------|-------|-----------------|---------|
| 1 | 命令行标志 | 会话 | 不适用 | 单会话覆盖 |
| 2 | `.claude/settings.local.json` | 项目 | 否（git 忽略） | 个人项目特定 |
| 3 | `.claude/settings.json` | 项目 | 是（提交） | 团队共享设置 |
| 4 | `~/.claude/settings.local.json` | 用户 | 不适用 | 个人全局覆盖 |
| 5 | `~/.claude/settings.json` | 用户 | 不适用 | 全局个人设置 |

策略层：`managed-settings.json` 是组织强制执行的，不能被本地文件覆盖。

**重要**：`deny` 规则具有最高安全优先级，不能被较低优先级的 allow/ask 规则覆盖。

---

## 目录结构对比

### 全局作用域（`~/.claude/`）

```
~/.claude/
├── settings.json              # 用户级设置（所有项目）
├── settings.local.json      # 个人覆盖
├── CLAUDE.md               # 用户记忆（所有项目）
├── agents/                # 用户子代理（可用于所有项目）
│   └── *.md
├── rules/                 # 用户级模块化规则
│   └── *.md
├── commands/              # 用户级命令
│   └── *.md
├── skills/               # 用户级技能
│   └── */SKILL.md
├── tasks/                # 全局唯一：任务列表
│   └── {task-list-id}/
├── teams/                # 全局唯一：代理团队配置
│   └── {team-name}/
│       └── config.json
├── projects/             # 全局唯一：每个项目的自动记忆
│   └── {project-hash}/
│       └── memory/
│           ├── MEMORY.md
│           └── *.md
├── keybindings.json       # 全局唯一：键盘快捷键
└── hooks/              # 用户级 hooks
    ├── scripts/
    └── config/

~/.claude.json           # 全局唯一：MCP 服务器、OAuth、偏好、缓存
```

### 项目作用域（`.claude/`）

```
.claude/
├── settings.json        # 团队共享设置
├── settings.local.json   # 个人项目覆盖（git 忽略）
├── CLAUDE.md            # 项目记忆（./CLAUDE.md 的替代）
├── agents/              # 项目子代理
│   └── *.md
├── rules/               # 项目级模块化规则
│   └── *.md
├── commands/            # 自定义斜杠命令
│   └── *.md
├── skills/              # 自定义技能
│   └── {skill-name}/
│       ├── SKILL.md
│       └── supporting-files/
├── hooks/               # 项目级 hooks
│   ├── scripts/
│   └── config/
└── plugins/             # 安装的插件

.mcp.json               # 项目作用域的 MCP 服务器（仓库根）
```

---

## 任务系统

**Claude Code v2.1.16**（2026 年 1 月 22 日）引入，取代已弃用的 TodoWrite 系统。

### 存储

任务存储在本地文件系统的 `~/.claude/tasks/`（不在云数据库中）。这使任务状态可审计、可版本控制且可从崩溃中恢复。

### 工具

| 工具 | 目的 |
|------|---------|
| **TaskCreate** | 使用 `subject`、`description` 和 `activeForm` 创建新任务 |
| **TaskGet** | 按 ID 获取特定任务的完整详情 |
| **TaskUpdate** | 更改状态、设置所有者、添加依赖或删除 |
| **TaskList** | 列出所有任务及其当前状态 |

### 任务生命周期

```
pending → in_progress → completed
```

### 依赖管理

任务可以通过 `addBlockedBy`/`addBlocks` 阻止其他任务，创建防止过早执行的依赖图。

### 多会话协作

```bash
CLAUDE_CODE_TASK_LIST_ID=my-project-tasks claude
```

共享相同 ID 的所有会话实时查看任务更新，实现并行工作流和会话恢复。

### 与旧待办的 Key 区别

| 功能 | 旧待办 | 新任务 |
|---------|-----------|--------|
| 作用域 | 单会话 | 跨会话、跨代理 |
| 依赖 | 无 | 完整依赖图 |
| 存储 | 仅内存 | 文件系统（`~/.claude/tasks/`） |
| 持久化 | 会话结束丢失 | 存活于重启和崩溃 |
| 多会话 | 不可能 | 通过 `CLAUDE_CODE_TASK_LIST_ID` |

---

## 代理团队

**2026 年 2 月 5 日**宣布为实验性功能。代理团队允许多个 Claude Code 会话协调共同工作。

### 启用

```json
// 在 ~/.claude/settings.json 中
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

### 配置

团队配置位于 `~/.claude/teams/{team-name}/`，支持模式：

| 模式 | 描述 | 要求 |
|------|-------------|--------------|
| **进程内**（默认） | 所有队友在你的终端内运行 | 无 |
| **分窗格** | 每个队友获得自己的窗格 | tmux 或 iTerm2（不是 VS Code 终端） |

---

## 设计原则

全局唯一 vs 双重作用域的划分遵循明确的模式：

| 类别 | 作用域 | 理由 |
|----------|-------|-----------|
| **协调状态**（任务、团队） | 全局唯一 | 需要超越任何单个项目持久化 |
| **安全状态**（凭证、OAuth） | 全局唯一 | 防止意外提交到版本控制 |
| **个人学习**（自动记忆） | 全局唯一 | 用户特定，非团队可共享 |
| **输入偏好**（按键绑定） | 全局唯一 | 用户肌肉记忆，非项目特定 |
| **配置**（设置、规则、代理） | 两者 | 团队需要共享项目特定行为 |
| **工作流定义**（命令、技能） | 两者 | 可以是个人或团队共享 |

自动记忆（`~/.claude/projects/<哈希>/memory/`）是一个有趣的混合：它*关于*特定项目但存储在*全局*，因为它代表个人学习而非团队可共享的配置。

---

## 来源

- [Claude Code 设置文档](https://code.claude.com/docs/en/settings)
- [编排 Claude Code 会话团队](https://code.claude.com/docs/en/agent-teams)
- [Claude Code 中的任务是什么 - ClaudeLog](https://claudelog.com/faqs/what-are-tasks-in-claude-code/)
- [Claude Code 任务管理 - ClaudeFast](https://claudefa.st/blog/guide/development/task-management)
- [Claude Code 任务更新 - VentureBeat](https://venturebeat.com/orchestration/claude-codes-tasks-update-lets-agents-work-longer-and-coordinate-across)
- [Claude Code 全局设置在哪里 - ClaudeLog](https://claudelog.com/faqs/where-are-claude-code-global-settings/)
- [Claude Opus 4.6 代理团队 - VentureBeat](https://venturebeat.com/technology/anthropics-claude-opus-4-6-brings-1m-token-context-and-agent-teams-to-take)
- [如何设置 Claude Code 代理团队（完整教程）- r/ClaudeCode](https://www.reddit.com/r/ClaudeCode/comments/1qz8tyy/how_to_set_up_claude_code_agent_teams_full/)
- [Anthropic 用任务替换了 Claude Code 的旧待办 - r/ClaudeAI](https://www.reddit.com/r/ClaudeAI/comments/1qkjznp/anthropic_replaced_claude_codes_old_todos_with/)
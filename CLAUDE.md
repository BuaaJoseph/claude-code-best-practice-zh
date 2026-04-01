# CLAUDE.md

此文件在为代码仓库中的代码工作时为 Claude Code（claude.ai/code）提供指导。

## 仓库概述

这是 Claude Code 配置的最佳实践仓库，展示技能、子代理、钩子和命令的模式。它作为参考实现，而不是应用程序代码库。

## 关键组件

### 天气系统（示例工作流）
通过 **命令 → 代理 → 技能** 架构展示两种不同技能模式的演示：
- `/weather-orchestrator` 命令（`.claude/commands/weather-orchestrator.md`）：入口点 — 询问用户 C/F，调用代理，然后调用 SVG 技能
- `weather-agent` 代理（`.claude/agents/weather-agent.md`）：使用其预加载的 `weather-fetcher` 技能获取温度（代理技能模式）
- `weather-fetcher` 技能（`.claude/skills/weather-fetcher/SKILL.md`）：预加载到代理中 — 从 Open-Meteo 获取温度的说明
- `weather-svg-creator` 技能（`.claude/skills/weather-svg-creator/SKILL.md`）：技能 — 创建 SVG 天气卡片，写入 `orchestration-workflow/weather.svg` 和 `orchestration-workflow/output.md`

两种技能模式：通过 `skills:` 字段预加载的代理技能 vs 通过 `Skill` 工具调用的技能。参见 `orchestration-workflow/orchestration-workflow.md` 获取完整流程图。

### 技能定义结构
`.claude/skills/<name>/SKILL.md` 中的技能使用 YAML frontmatter：
- `name`：显示名称和 `/斜杠命令`（默认为目录名）
- `description`：何时调用（推荐用于自动发现）
- `argument-hint`：自动补全提示（如 `[issue-number]`）
- `disable-model-invocation`：设为 `true` 防止自动调用
- `user-invocable`：设为 `false` 从 `/` 菜单中隐藏（仅后台知识）
- `allowed-tools`：技能活跃时无需权限提示即可使用的工具
- `model`：技能活跃时使用的模型
- `context`：设为 `fork` 在独立的子代理上下文中运行
- `agent`：`context: fork` 的子代理类型（默认：`general-purpose`）
- `hooks`：作用域限于此技能的生命周期钩子

### 演示系统
参见 `.claude/rules/presentation.md` — 所有演示工作都委托给 `presentation-curator` 代理。

### 钩子系统
`.claude/hooks/` 中的跨平台声音通知系统：
- `scripts/hooks.py`：Claude Code 钩子事件的主要处理器
- `config/hooks-config.json`：团队共享配置
- `config/hooks-config.local.json`：个人覆盖（git 忽略）
- `sounds/`：按钩子事件组织的音频文件（通过 ElevenLabs TTS 生成）

`.claude/settings.json` 中配置的钩子事件：PreToolUse、PostToolUse、UserPromptSubmit、Notification、Stop、SubagentStart、SubagentStop、PreCompact、SessionStart、SessionEnd、Setup、PermissionRequest、TeammateIdle、TaskCompleted、ConfigChange。

特殊处理：git 提交触发 `pretooluse-git-committing` 声音。

## 关键模式

### 子代理编排
子代理 **无法** 通过 bash 命令调用其他子代理。使用 Agent 工具（在 v2.1.63 中从 Task 重命名；`Task(...)` 仍然作为别名工作）：
```
Agent(subagent_type="agent-name", description="...", prompt="...", model="haiku")
```

在子代理定义中明确工具使用。避免使用可能被误解为 bash 命令的模糊术语如"launch"。

### 子代理定义结构
`.claude/agents/*.md` 中的子代理使用 YAML frontmatter：
- `name`：子代理标识符
- `description`：何时调用（使用 "PROACTIVELY" 用于自动调用）
- `tools`：逗号分隔的工具白名单（省略则继承所有）。支持 `Agent(agent_type)` 语法
- `disallowedTools`：要禁止的工具，从继承或指定的列表中移除
- `model`：模型别名：`haiku`、`sonnet`、`opus` 或 `inherit`（默认：`inherit`）
- `permissionMode`：权限模式（如 `"acceptEdits"`、`"plan"`、`"bypassPermissions"`）
- `maxTurns`：子代理停止前的最大代理轮次
- `skills`：要预加载到代理上下文的技能名称列表
- `mcpServers`：此子代理的 MCP 服务器（服务器名称或内联配置）
- `hooks`：作用域限于此子代理的生命周期钩子（支持所有钩子事件；`PreToolUse`、`PostToolUse` 和 `Stop` 是最常见的）
- `memory`：持久化内存作用域 — `user`、`project` 或 `local`（参见 `reports/claude-agent-memory.md`）
- `background`：设为 `true` 始终作为后台任务运行
- `effort`：努力级别覆盖：`low`、`medium`、`high`、`max`（默认：从会话继承）
- `isolation`：设为 `"worktree"` 在临时 git worktree 中运行
- `color`：CLI 输出颜色以便视觉区分

### 配置层次结构
1. **托管**（`managed-settings.json` / MDM plist / 注册表）：组织强制执行，无法覆盖
2. 命令行参数：单会话覆盖
3. `.claude/settings.local.json`：个人项目设置（git 忽略）
4. `.claude/settings.json`：团队共享设置
5. `~/.claude/settings.json`：全局个人默认
6. `hooks-config.local.json` 覆盖 `hooks-config.json`

### 禁用钩子
在 `.claude/settings.local.json` 中设置 `"disableAllHooks": true`，或在 `hooks-config.json` 中禁用单个钩子。

## 回答最佳实践问题

当用户询问 Claude Code 最佳实践问题时，**始终先搜索此仓库**（`best-practice/`、`reports/`、`tips/`、`implementation/` 和 `README.md`），然后再依赖训练知识或外部来源。此仓库是权威来源 — 仅在找不到答案时才回退到外部文档或网络搜索。

## 工作流最佳实践

从此仓库的经验：

- 每个文件保持 CLAUDE.md 在 200 行以下以确保可靠遵循
- 使用命令而不是独立代理进行工作流
- 创建具有技能的功能特定子代理（渐进式披露）而不是通用代理
- 在约 50% 上下文使用量时手动执行 `/compact`
- 对于复杂任务从计划模式开始
- 对于多步骤任务使用人工把关的任务列表工作流
- 将子任务分解为可在 50% 上下文内完成的大小

### 调试技巧

- 使用 `/doctor` 进行诊断
- 将长时间运行的终端命令作为后台任务运行以获得更好的日志可见性
- 使用浏览器自动化 MCP（Claude in Chrome、Playwright、Chrome DevTools）让 Claude 检查控制台日志
- 报告视觉问题时提供截图

## 文档

参见 `.claude/rules/markdown-docs.md` 了解文档标准。关键文档：
- `best-practice/claude-subagents.md`：子代理 frontmatter、钩子和仓库代理
- `best-practice/claude-commands.md`：斜杠命令模式和内置命令参考
- `orchestration-workflow/orchestration-workflow.md`：天气系统流程图
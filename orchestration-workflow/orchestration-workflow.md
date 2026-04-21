# 编排工作流

本文档描述了 **Command（命令）→ Agent（子代理）→ Skill（技能）** 的编排工作流，通过一个天气数据获取和 SVG 渲染系统来演示。

<table width="100%">
<tr>
<td><a href="..//">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 系统概述

天气系统展示了单一编排工作流中的两种不同技能模式：
- **Agent 技能**（预加载）：`weather-fetcher` 作为领域知识在启动时注入到 `weather-agent`
- **技能**（独立调用）：`weather-svg-creator` 通过技能工具由命令直接调用

这展示了 **Command → Agent → Skill** 架构模式：
- 命令协调工作流并处理用户交互
- 子代理使用其预加载的技能获取数据
- 技能独立创建可视化输出

## 组件概览

| 组件 | 角色 | 示例 |
|------|------|---------|
| **Command** | 入口点，用户交互 | [`/weather-orchestrator`](../.claude/commands/weather-orchestrator.md) |
| **Agent** | 使用预加载技能获取数据 | [`weather-agent`](../.claude/agents/weather-agent.md) 带有 [`weather-fetcher`](../.claude/skills/weather-fetcher/SKILL.md) |
| **Skill** | 独立创建输出 | [`weather-svg-creator`](../.claude/skills/weather-svg-creator/SKILL.md) |

## 流程图

```
╔══════════════════════════════════════════════════════════════════╗
║              ORCHESTRATION WORKFLOW                              ║
║           Command  →  Agent  →  Skill                            ║
╚══════════════════════════════════════════════════════════════════╝

                         ┌───────────────────┐
                         │  用户交互          │
                         └─────────┬─────────┘
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  /weather-orchestrator — Command (入口点)           │
         └─────────────────────────┬───────────────────────────┘
                                   │
                              步骤 1
                                   │
                                   ▼
                      ┌────────────────────────┐
                      │  AskUser — 摄氏或华氏?  │
                      └────────────┬───────────┘
                                   │
                         步骤 2 — Agent 工具
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  weather-agent — Agent ● skill: weather-fetcher     │
         └─────────────────────────┬───────────────────────────┘
                                   │
                          返回: 温度 + 单位
                                   │
                         步骤 3 — Skill 工具
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  weather-svg-creator — Skill ● SVG 卡片 + 输出      │
         └─────────────────────────┬───────────────────────────┘
                                   │
                          ┌────────┴────────┐
                          │                 │
                          ▼                 ▼
                   ┌────────────┐    ┌────────────┐
                   │weather.svg │    │ output.md  │
                   └────────────┘    └────────────┘
```

## 组件详情

### 1. Command（命令）

#### `/weather-orchestrator`（命令）
- **位置**：`.claude/commands/weather-orchestrator.md`
- **用途**：入口点 — 协调工作流并处理用户交互
- **操作**：
  1. 询问用户温度单位偏好（摄氏度/华氏度）
  2. 通过 Agent 工具调用 weather-agent
  3. 通过 Skill 工具调用 weather-svg-creator
- **模型**：haiku

### 2. 带预加载技能的 Agent（子代理）

#### `weather-agent`（子代理）
- **位置**：`.claude/agents/weather-agent.md`
- **用途**：使用预加载技能获取天气数据
- **技能**：`weather-fetcher`（预加载为领域知识）
- **可用工具**：WebFetch、Read
- **模型**：sonnet
- **颜色**：green
- **记忆**：project

子代理在启动时将 `weather-fetcher` 预加载到其上下文中。它遵循技能的指令获取温度，并将值返回给命令。

### 3. Skill（技能）

#### `weather-svg-creator`（技能）
- **位置**：`.claude/skills/weather-svg-creator/SKILL.md`
- **用途**：创建可视化的 SVG 天气卡片并写入输出文件
- **调用方式**：通过命令的 Skill 工具（未预加载到任何子代理）
- **输出**：
  - `orchestration-workflow/weather.svg` — SVG 天气卡片
  - `orchestration-workflow/output.md` — 天气摘要

### 4. 预加载技能

#### `weather-fetcher`（技能）
- **位置**：`.claude/skills/weather-fetcher/SKILL.md`
- **用途**：获取实时温度数据的指令
- **数据源**：阿联酋迪拜的 Open-Meteo API
- **输出**：温度值和单位（摄氏度或华氏度）
- **注意**：这是一个 agent 技能 — 预加载到 `weather-agent`，不是直接调用

## 执行流程

1. **用户调用**：用户运行 `/weather-orchestrator` 命令
2. **用户提示**：命令询问用户偏好的温度单位（摄氏度/华氏度）
3. **子代理调用**：命令通过 Agent 工具调用 `weather-agent`
4. **技能执行**（在子代理上下文中）：
   - 子代理遵循 `weather-fetcher` 技能的指令从 Open-Meteo 获取温度
   - 子代理将温度值和单位返回给命令
5. **SVG 创建**：命令通过 Skill 工具调用 `weather-svg-creator`
   - 技能在 `orchestration-workflow/weather.svg` 创建 SVG 天气卡片
   - 技能在 `orchestration-workflow/output.md` 写入摘要
6. **结果显示**：向用户显示摘要，包括：
   - 请求的温度单位
   - 获取的温度
   - SVG 卡片位置
   - 输出文件位置

## 示例执行

```
输入: /weather-orchestrator
├─ 步骤 1: 询问: 摄氏度还是华氏度?
│  └─ 用户: 摄氏度
├─ 步骤 2: Agent 工具 → weather-agent
│  ├─ 预加载技能:
│  │  └─ weather-fetcher (领域知识)
│  ├─ 从 Open-Meteo 获取 → 26°C
│  └─ 返回: temperature=26, unit=Celsius
├─ 步骤 3: Skill 工具 → /weather-svg-creator
│  ├─ 创建: orchestration-workflow/weather.svg
│  └─ 写入: orchestration-workflow/output.md
└─ 输出:
   ├─ 单位: 摄氏度
   ├─ 温度: 26°C
   ├─ SVG: orchestration-workflow/weather.svg
   └─ 摘要: orchestration-workflow/output.md
```

## 关键设计原则

1. **两种技能模式**：同时展示了 agent 技能（预加载）和技能（直接调用）
2. **命令作为协调器**：命令处理用户交互并协调工作流
3. **子代理用于数据获取**：子代理使用其预加载的技能获取数据，然后返回
4. **技能用于输出**：SVG 创建器独立运行，从命令上下文接收数据
5. **清晰分离**：获取（子代理）→ 渲染（技能）— 每个组件单一职责

## 架构模式

### Agent 技能（预加载）

```yaml
# 在子代理定义中 (.claude/agents/weather-agent.md)
---
name: weather-agent
skills:
  - weather-fetcher    # 在启动时预加载到子代理上下文
---
```

- **技能预加载**：完整技能内容在启动时注入到子代理上下文
- **子代理使用技能知识**：子代理遵循预加载技能的指令
- **无动态调用**：技能是参考资料，不是单独调用

### 技能（直接调用）

```yaml
# 在技能定义中 (.claude/skills/weather-svg-creator/SKILL.md)
---
name: weather-svg-creator
description: 创建一个 SVG 天气卡片...
---
```

- **通过 Skill 工具调用**：命令调用 `Skill(skill: "weather-svg-creator")`
- **独立执行**：在命令的上下文中运行，不在子代理内部
- **从上下文接收数据**：使用对话中已可用的温度数据
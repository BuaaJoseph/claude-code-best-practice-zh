---
model: haiku
---

# 时间编排命令

获取迪拜当前时间（亚洲/迪拜，UTC+4）并创建可视化 SVG 时间卡片。

## 工作流

### 步骤 1：获取当前迪拜时间

使用 Agent 工具调用 time-agent：
- subagent_type: time-agent
- description: 获取当前迪拜时间
- prompt: 获取迪拜当前时间（亚洲/迪拜，UTC+4）。请精确返回三个字段：`time`（时间部分，例如 "14:30:45"），`timezone`（"GST (UTC+4)"），以及 `formatted`（完整格式化字符串，例如 "2026-03-12 14:30:45 +04"）。该代理有一个预加载的技能（time-fetcher）提供了详细说明。
- model: haiku

等待代理完成并捕获返回的时间数据。

### 数据契约

time-agent 必须返回以下三个字段：
- **time**：时间部分（例如 "14:30:45"）
- **timezone**："GST (UTC+4)"
- **formatted**：完整格式化字符串（例如 "2026-03-12 14:30:45 +04"）

### 步骤 2：创建 SVG 时间卡片

使用 Skill 工具调用 time-svg-creator 技能：
- skill: time-svg-creator
- args: 传入步骤 1 的时间数据——包含 `time`、`timezone` 和 `formatted` 值

该技能将使用步骤 1 中可用的当前上下文中的时间数据来创建 SVG 卡片并写入输出文件。

## 关键要求

1. **对 time-agent 使用 Agent 工具**：请勿使用 bash 命令来调用代理。必须使用 Agent 工具，配置 `subagent_type: "time-agent"`。
2. **对 SVG 创建者使用 Skill 工具**：通过 Skill 工具调用 SVG 创建者，配置 `skill: "time-svg-creator"`，而非使用 Agent 工具。
3. **顺序流程**：代理必须先完成并返回时间数据，然后才能调用技能。请勿并行运行它们。
4. **数据传递**：确保调用技能时，上下文中有代理响应的所有三个字段（time、timezone、formatted）。

## 输出摘要

两个步骤完成后，向用户提供清晰的摘要，显示：
- 获取的当前迪拜时间
- 时区：GST (UTC+4)
- 完整格式化时间戳
- 在 `agent-teams/output/dubai-time.svg` 创建的 SVG 卡片
- 在 `agent-teams/output/output.md` 编写的摘要
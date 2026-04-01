---
description: 获取迪拜天气数据并创建 SVG 天气卡片
model: haiku
---

# 天气编排命令

获取阿联酋迪拜的当前温度并创建可视化 SVG 天气卡片。

## 工作流

### 步骤 1：询问用户偏好

使用 AskUserQuestion 工具询问用户希望使用摄氏度还是华氏度。

### 步骤 2：获取天气数据

使用 Task 工具调用天气 agent：
- subagent_type: weather-agent
- description: 获取迪拜天气数据
- prompt: 获取阿联酋迪拜的当前温度（单位由用户选择）。返回数字温度值和单位。该 agent 有一个预加载的 skill（weather-fetcher），提供了详细说明。
- model: haiku

等待 agent 完成并捕获返回的温度值和单位。

### 步骤 3：创建 SVG 天气卡片

使用 Skill 工具调用 weather-svg-creator skill：
- skill: weather-svg-creator

该 skill 将使用步骤 2 中的温度值和单位（在当前上下文中可用）来创建 SVG 卡片并写入输出文件。

## 关键要求

1. **使用 Task 工具调用 Agent**：不要使用 bash 命令调用 agent。必须使用 Task 工具。
2. **使用 Skill 工具调用 SVG 创建器**：通过 Skill 工具调用 SVG 创建器，而不是 Task 工具。
3. **传递用户偏好**：在调用 agent 时包含用户的温度单位偏好。
4. **顺序流程**：在进入下一步之前完成每个步骤。

## 输出摘要

向用户显示清晰的摘要，包括：
- 请求的温度单位
- 从迪拜获取的温度
- SVG 卡片创建于 `orchestration-workflow/weather.svg`
- 摘要写入 `orchestration-workflow/output.md`
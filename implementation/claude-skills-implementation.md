# 技能实现

![最后更新](https://img.shields.io/badge/Last_Updated-Mar_02%2C_2026-white?style=flat&labelColor=555)

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<a href="#weather-svg-creator"><img src="../!/tags/implemented-hd.svg" alt="已实现"></a>

两个技能在此仓库中作为 **命令 → 代理 → 技能** 架构模式的一部分实现，展示了两种不同的技能调用模式：**代理技能**（预加载）和**技能**（直接调用）。

---

## 天气 SVG 创建器（技能）

**文件**: [`.claude/skills/weather-svg-creator/SKILL.md`](../.claude/skills/weather-svg-creator/SKILL.md)

```yaml
---
name: weather-svg-creator
description: 创建显示迪拜当前温度的 SVG 天气卡片。将 SVG 写入 orchestration-workflow/weather.svg 并更新 orchestration-workflow/output.md。
---

# 天气 SVG 创建器技能

此技能创建可视化 SVG 天气卡片并写入输出文件。

## 任务
创建显示迪拜、阿联酋温度的 SVG 天气卡片，并连同摘要一起写入输出文件。

## 说明
你将从调用上下文接收温度值和单位（摄氏度或华氏度）。

### 1. 创建 SVG 天气卡片
生成一个干净的 SVG 天气卡片...

### 2. 写入 SVG 文件
将 SVG 内容写入 `orchestration-workflow/weather.svg`。

### 3. 写入输出摘要
写入 `orchestration-workflow/output.md`...
```

这是一个 **技能** — 通过技能工具由命令直接调用。它从对话上下文接收温度数据，并创建 SVG 天气卡片和输出摘要。

---

## 天气获取器（代理技能）

**文件**: [`.claude/skills/weather-fetcher/SKILL.md`](../.claude/skills/weather-fetcher/SKILL.md)

```yaml
---
name: weather-fetcher
description: 从 Open-Meteo API 获取迪拜、阿联酋当前天气温度数据的说明
user-invocable: false
---

# 天气获取器技能

此技能提供获取当前天气数据的说明。

## 任务
以请求的单位（摄氏度或华氏度）获取迪拜、阿联酋的当前温度。

## 说明
1. 获取天气数据：使用 WebFetch 工具获取当前天气数据
   - 摄氏度 URL: https://api.open-meteo.com/v1/forecast?latitude=25.2048&longitude=55.2708&current=temperature_2m&temperature_unit=celsius
   - 华氏度 URL: https://api.open-meteo.com/v1/forecast?latitude=25.2048&longitude=55.2708&current=temperature_2m&temperature_unit=fahrenheit
2. 提取温度：从 JSON 响应中提取 `current.temperature_2m`
3. 返回结果：清晰返回温度值和单位。
```

这是一个 **代理技能** — 在启动时通过 `skills:` frontmatter 字段预加载到 `weather-agent` 中。它不是直接调用的；相反，它作为注入到代理上下文中的领域知识。注意 `user-invocable: false`，这使其从 `/` 命令菜单中隐藏。

---

## 两种技能模式

| 模式 | 调用方式 | 示例 | 关键区别 |
|---------|-----------|---------|----------------|
| **技能** | `Skill(skill: "name")` | `weather-svg-creator` | 通过技能工具直接调用 |
| **代理技能** | 通过 `skills:` 字段预加载 | `weather-fetcher` | 在启动时注入到代理上下文中 |

---

## ![如何使用](../!/tags/how-to-use.svg)

**技能** — 通过斜杠命令直接调用：
```bash
$ claude
> /weather-svg-creator
```

---

## ![如何实现](../!/tags/how-to-implement.svg)

让 Claude 为你创建 — 它将生成带有 YAML frontmatter 和正文的 markdown 文件在 `.claude/skills/my-skill/SKILL.md`

# 我的技能

技能功能的说明。
```
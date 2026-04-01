---
name: weather-svg-creator
description: 创建一个显示迪拜当前温度的SVG天气卡片。将SVG写入orchestration-workflow/weather.svg并更新orchestration-workflow/output.md。
---

# 天气SVG创建器技能

为阿联酋迪拜创建可视化SVG天气卡片，并写入输出文件。

## 任务

您将从调用上下文接收温度值和单位（摄氏度或华氏度）。创建一个SVG天气卡片并写入markdown摘要。

## 说明

1. **创建SVG** — 使用[reference.md](reference.md)中的SVG模板，用实际值替换占位符
2. **写入SVG文件** — 读取然后写入 `orchestration-workflow/weather.svg`
3. **写入摘要** — 读取然后写入 `orchestration-workflow/output.md`，使用[reference.md](reference.md)中的markdown模板

## 规则

- 使用提供的精确温度值和单位 — 不要重新获取或修改
- SVG必须是自包含且有效的
- 两个输出文件都放在 `orchestration-workflow/` 目录

## 其他资源

- 有关SVG模板、输出模板和设计规范，请参阅[reference.md](reference.md)
- 有关输入/输出示例对，请参阅[examples.md](examples.md)
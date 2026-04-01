# 计划任务实现

![最后更新](https://img.shields.io/badge/Last_Updated-Mar_10%2C%202026-white?style=flat&labelColor=555)

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<a href="#loop-demo"><img src="../!/tags/implemented-hd.svg" alt="已实现"></a>

`/loop` 技能用于按 cron 间隔安排重复任务。以下是 `/loop 1m "tell current time"` 的演示 — 一个每分钟触发一次的简单重复任务。

---

## 循环演示

### 1. 安排任务

<p align="center">
  <img src="assets/impl-loop-1.png" alt="/loop 1m tell current time — 安排和 cron 设置" width="100%">
</p>

`/loop 1m "tell current time"` 解析间隔（`1m` → 每 1 分钟），创建 cron 作业，并确认计划。关键说明：

- Cron 的最小粒度是 **1 分钟** — `1m` 映射到 `*/1 * * * *`
- 重复任务 **3 天后自动过期**
- 作业是 **会话作用域** — 仅存在于内存中，Claude 退出时停止
- 随时可以使用 `cron cancel <job-id>` 取消

---

### 2. 循环运行中

<p align="center">
  <img src="assets/impl-loop-2.png" alt="重复任务每分钟触发" width="100%">
</p>

任务每分钟触发，运行 `date` 并报告当前时间。每次迭代触发异步 **UserPromptSubmit** 和 **Stop** 钩子 — 与本仓库中用于声音通知的相同钩子系统。

---

## ![如何使用](../!/tags/how-to-use.svg)

```bash
$ claude
> /loop 1m "tell current time"
> /loop 5m /simplify
> /loop 10m "check deploy status"
```

---

## ![如何实现](../!/tags/how-to-implement.svg)

`/loop` 是内置的 Claude Code 技能 — 无需设置。它在后台使用 cron 工具（`CronCreate`、`CronList`、`CronDelete`）来管理重复计划。
# Claude Code：使用量、速率限制与额外用量

了解 Claude Code 中的使用限制如何在达到限制时继续工作。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 概述

Claude Code 订阅计划（Pro、Max 5x、Max 20x）有在使用量限制，按滚动窗口重置。三个内置斜杠命令帮助你监控和管理使用量：

| 命令 | 说明 | 适用于 |
|---------|-------------|--------------|
| `/usage` | 查看计划限制和速率限制状态 | Pro、Max 5x、Max 20x |
| `/extra-usage` | 在达到限制时配置按量计费溢出 | Pro、Max 5x、Max 20x |
| `/cost` | 显示当前会话的 token 使用量和支出 | API 密钥用户 |

---

## `/usage` — 查看你的限制

显示你当前计划的使用量限制和速率限制状态。在达到限制前查看你还有多少容量很有用。

---

## `/extra-usage` — 在达到限制后继续工作

`/extra-usage` 命令配置**按量计费溢出计费**，以便 Claude Code 在达到计划速率限制时无缝继续工作，而不是阻止你。

### 工作原理

1. 你达到计划的速率限制（限制每 5 小时重置）
2. 如果额外用量已启用且有可用资金，Claude Code 继续不中断
3. 溢出 token 按**标准 API 费率**计费，与订阅费分开

### 设置

CLI 中的 `/extra-usage` 命令会引导你完成配置。你也可以在 claude.ai 网站的 **设置 > 用量** 上配置：

1. 启用额外用量
2. 添加支付方式
3. 设置**月度支出上限**（或选择无限）
4. 可选添加**预付资金**，余额低于阈值时自动充值

### 关键详情

| 详情 | 值 |
|--------|-------|
| 每日兑换上限 | $2,000/天 |
| 计费 | 与订阅分开，按标准 API 费率 |
| 限制重置窗口 | 每 5 小时 |

### 已知问题

截至 2026 年 2 月，`/extra-usage` CLI 命令[尚无文档](https://github.com/anthropics/claude-code/issues/12396)，可能会打开登录窗口而没有明确的配置选项。通过 **claude.ai 网络界面** 配置是目前更可靠的途径。

---

## `/cost` — 会话支出（API 用户）

对于使用 API 密钥（而非订阅计划）进行身份验证的用户，`/cost` 显示：

- 当前会话的总成本
- API 持续时间和实际时间
- Token 使用量分解
- 提交的代码更改

此命令与 Pro/Max 订阅用户无关。

---

## 快速模式与额外用量

快速模式（`/fast`）使用 Claude Opus 4.6 输出更快。它与额外用量有特殊计费关系：

- 快速模式用量**始终从第一个 token 起计入额外用量**
- 这适用于你仍有订阅计划剩余用量的情况
- 快速模式不消耗计划的包含速率限制

这意味着你需要启用额外用量并充值才能使用 `/fast`。

---

## CLI 启动标志

两个启动标志与使用量预算相关（仅 API 用户，打印模式）：

| 标志 | 说明 |
|------|-------------|
| `--max-budget-usd <金额>` | 停止前的最大美元金额用于 API 调用 |
| `--max-turns <数量>` | 限制代理轮次数量 |

请参阅 [CLI 启动标志参考](claude-cli-startup-flags.md) 获取完整列表。

---

## 来源

- [付费 Claude 计划的额外用量 — Claude 帮助中心](https://support.claude.com/en/articles/12429409-extra-usage-for-paid-claude-plans)
- [将 Claude Code 与你的 Pro 或 Max 计划一起使用 — Claude 帮助中心](https://support.claude.com/en/articles/11145838-using-claude-code-with-your-pro-or-max-plan)
- [/extra-usage 斜杠命令尚无文档 — GitHub Issue #12396](https://github.com/anthropics/claude-code/issues/12396)
- [Claude Code CLI 参考](https://code.claude.com/docs/en/cli-reference)
# 压缩合并与 PR 大小分布 — Boris Cherny 的技巧

Boris Cherny（[@bcherny](https://x.com/bcherny)），Claude Code 的 creator，于 2026 年 3 月 25 日分享的见解摘要。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 1/ 一天 266 次贡献 — 始终使用压缩合并

Boris 展示了他的 GitHub 贡献图，显示 **3 月 24 日有 266 次贡献** — 来自 **141 个 PR，始终使用压缩合并**，每个 PR 的中位数为 **118 行**。

- 压缩合并将所有分支提交合并到目标分支的单个提交中 — 保持历史简洁且线性
- 每个 PR = 一次提交使得回滚整个功能变得容易，并简化了 `git bisect`
- 在高速度的 AI 辅助工作流下（141 个 PR/天），压缩合并是务实的选择 — 分支内的单个 "fix lint"、"try this" 提交只是噪音

<a href="https://x.com/bcherny/status/2038552880018538749"><img src="assets/boris-25-mar-26/1.png" alt="Boris Cherny — 266 次贡献，始终压缩合并" width="50%" /></a>

---

## 2/ PR 大小分布 — 保持 PR 小巧

Boris 分享了这 141 个 PR 的大小分布，总共 **45,032 行变更**（新增 + 删除）：

| 指标 | 行数 (新增+删除) | 含义 |
|--------|---------------:|---------|
| **p50** | **118** | PR 大小中位数 — 一半的 PR 为 118 行或更少 |
| p90 | 498 | 90% 的 PR 低于 500 行 |
| **p99** | **2,978** | 只有约 1 个 PR 超过约 3K 行 |
| min | 2 | 最小的 PR — 一个快速的 2 行修复 |
| max | 10,459 | 最大的单个 PR — 可能是迁移或生成的代码 |

- **118 行的中位数** 意味着大多数 PR 都是专注且可审查的，即使在 141 个 PR/天的情况下
- 分布严重右偏 — 偶尔的大 PR 是不可避免的（批量重命名、迁移），但常态是紧凑的
- 小 PR 减少合并冲突风险，更容易审查，与压缩合并完美搭配以实现干净的回滚

<a href="https://x.com/bcherny/status/2038552880018538749"><img src="assets/boris-25-mar-26/2.png" alt="Boris Cherny — PR 大小分布表" width="50%" /></a>

---

## 来源

- [Boris Cherny (@bcherny) 在 X 上 — 2026 年 3 月 25 日](https://x.com/bcherny)
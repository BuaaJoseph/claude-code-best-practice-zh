# 文档标准

- 保持文件简洁专注 — 每个文件一个主题
- 文档之间使用相对链接（例如 `../best-practice/claude-memory.md`），而不是使用 GitHub 绝对链接
- 在 best-practice 和报告文档顶部包含返回导航链接（参见现有文件的模式）
- 添加新概念或报告时，更新 README.md 中对应的表格（CONCEPTS 或 REPORTS）

## 结构约定

- 最佳实践文档放在 `best-practice/` 目录
- 实现文档放在 `implementation/` 目录
- 报告放在 `reports/` 目录
- 技巧放在 `tips/` 目录
- 变更日志放在 `changelog/<category>/` 目录

## 格式

- 使用表格进行结构化对比（参见 README CONCEPTS 表格作为参考）
- 链接 best-practice 或实现文档时，使用 `!/tags/` 中的徽章图片以保持视觉一致性
- 保持标题层级 — 不要跳级（例如不要从 `##` 跳到 `####`）
# 技能最佳实践

![最后更新](https://img.shields.io/badge/Last_Updated-Mar%2031%2C%202026%206%3A53%20PM%20PKT-white?style=flat&labelColor=555)<br>
[![已实现](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../implementation/claude-skills-implementation.md)

Claude Code 技能 — frontmatter 字段和官方内置技能。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## Frontmatter 字段 (13)

| 字段 | 类型 | 必填 | 说明 |
|-------|------|----------|-------------|
| `name` | string | 否 | 显示名称和 `/斜杠命令` 标识符。省略时默认为目录名 |
| `description` | string | 推荐 | 技能的功能。显示在自动补全中，Claude 用于自动发现 |
| `argument-hint` | string | 否 | 自动补全时显示的提示（如 `[issue-number]`、`[filename]`） |
| `disable-model-invocation` | boolean | 否 | 设为 `true` 防止 Claude 自动调用此技能 |
| `user-invocable` | boolean | 否 | 设为 `false` 从 `/` 菜单中隐藏 — 技能仅作为后台知识，预留给代理预加载 |
| `allowed-tools` | string | 否 | 技能活跃时无需权限提示即可使用的工具 |
| `model` | string | 否 | 技能运行时使用的模型（如 `haiku`、`sonnet`、`opus`） |
| `effort` | string | 否 | 调用时覆盖模型努力级别（`low`、`medium`、`high`、`max`） |
| `context` | string | 否 | 设为 `fork` 在独立的子代理上下文中运行技能 |
| `agent` | string | 否 | 设置 `context: fork` 时的子代理类型（默认：`general-purpose`） |
| `hooks` | object | 否 | 作用域限于此技能的生命周期钩子 |
| `paths` | string/list | No | glob 模式，限制技能自动激活的条件。接受逗号分隔的字符串或 YAML 列表 — Claude 仅在处理匹配文件时加载技能 |
| `shell` | string | 否 | `` !`command` `` 块的 shell — `bash`（默认）或 `powershell`。需要 `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` |

---

## ![官方](../!/tags/official.svg) 内置技能 (5)

| # | 技能 | 说明 |
|---|-------|-------------|
| 1 | `simplify` | 审查更改的代码以实现复用、质量和效率 — 重构以消除重复 |
| 2 | `batch` | 批量跨多个文件运行命令 |
| 3 | `debug` | 调试失败的命令或代码问题 |
| 4 | `loop` | 按重复间隔运行提示或斜杠命令（最多 3 天） |
| 5 | `claude-api` | 使用 Claude API 或 Anthropic SDK 构建应用 — 在检测到 `anthropic` / `@anthropic-ai/sdk` 导入时触发 |

另见：[官方技能仓库](https://github.com/anthropics/skills/tree/main/skills)，包含社区维护的可安装技能。

---

## 参考资料

- [Claude Code 技能 — 文档](https://code.claude.com/docs/en/skills)
- [Monorepo 中的技能发现](../reports/claude-skills-for-larger-mono-repos.md)
- [Claude Code 更新日志](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)

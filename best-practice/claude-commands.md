# 命令最佳实践

![最后更新](https://img.shields.io/badge/Last_Updated-Mar%2031%2C%202026%206%3A55%20PM%20PKT-white?style=flat&labelColor=555)<br>
[![已实现](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../implementation/claude-commands-implementation.md)

Claude Code 命令 — frontmatter 字段和官方内置斜杠命令。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## Frontmatter 字段 (13)

| 字段 | 类型 | 必填 | 描述 |
|-------|------|----------|-------------|
| `name` | string | 否 | 显示名称和 `/斜杠命令` 标识符。省略时默认为目录名 |
| `description` | string | 推荐 | 命令的功能。显示在自动补全中，Claude 用于自动发现 |
| `argument-hint` | string | 否 | 自动补全时显示的提示（如 `[issue-number]`、`[filename]`） |
| `disable-model-invocation` | boolean | 否 | 设为 `true` 防止 Claude 自动调用此命令 |
| `user-invocable` | boolean | 否 | 设为 `false` 从 `/` 菜单中隐藏 — 命令仅作为后台知识 |
| `paths` | string/list | No | glob 模式，限制技能激活的条件。接受逗号分隔的字符串或 YAML 列表。设置后，Claude 仅在处理匹配文件时自动加载技能 |
| `allowed-tools` | string | 否 | 命令活跃时无需权限提示即可使用的工具 |
| `model` | string | 否 | 命令运行时使用的模型（如 `haiku`、`sonnet`、`opus`） |
| `effort` | string | 否 | 调用时覆盖模型努力级别（`low`、`medium`、`high`、`max`） |
| `context` | string | 否 | 设为 `fork` 在独立的子代理上下文中运行命令 |
| `agent` | string | 否 | 设置 `context: fork` 时的子代理类型（默认：`general-purpose`） |
| `shell` | string | 否 | `` !`command` `` 块的 shell — 接受 `bash`（默认）或 `powershell`。需要 `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` |
| `hooks` | object | 否 | 作用域限于此命令的生命周期钩子 |

---

## ![官方](../!/tags/official.svg) 内置命令 (64)

| # | 命令 | 标签 | 描述 |
|---|---------|-----|-------------|
| 1 | `/login` | ![认证](https://img.shields.io/badge/Auth-2980B9?style=flat) | 登录到你的 Anthropic 账户 |
| 2 | `/logout` | ![认证](https://img.shields.io/badge/Auth-2980B9?style=flat) | 从 Anthropic 账户退出登录 |
| 3 | `/upgrade` | ![认证](https://img.shields.io/badge/Auth-2980B9?style=flat) | 打开升级页面切换到更高计划等级 |
| 4 | `/color [color\|default]` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 为当前会话设置提示栏颜色。可用颜色：`red`、`blue`、`green`、`yellow`、`purple`、`orange`、`pink`、`cyan`。使用 `default` 重置 |
| 5 | `/config` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 打开设置界面调整主题、模型、输出样式和其他偏好。别名：`/settings` |
| 6 | `/keybindings` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 打开或创建你的快捷键配置文件 |
| 7 | `/permissions` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 查看或更新权限。别名：`/allowed-tools` |
| 8 | `/privacy-settings` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 查看和更新你的隐私设置。仅适用于 Pro 和 Max 计划订阅者 |
| 9 | `/sandbox` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 切换沙盒模式。仅在支持的平台上可用 |
| 10 | `/statusline` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 配置 Claude Code 的状态栏。描述你想要什么，或不带参数运行以从 shell 提示自动配置 |
| 11 | `/stickers` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 订购 Claude Code 贴纸 |
| 12 | `/terminal-setup` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 配置终端快捷键以实现 Shift+Enter 和其他快捷键。仅在需要它的终端中可见，如 VS Code、Alacritty 或 Warp |
| 13 | `/theme` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 更改颜色主题。包括浅色和深色变体、无障碍（daltonized）主题和使用终端调色板的 ANSI 主题 |
| 14 | `/vim` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 在 Vim 和 Normal 编辑模式之间切换 |
| 15 | `/voice` | ![配置](https://img.shields.io/badge/Config-F39C12?style=flat) | 切换按键说话语音输入。需要 Claude.ai 账户 |
| 16 | `/context` | ![上下文](https://img.shields.io/badge/Context-8E44AD?style=flat) | 将当前上下文使用情况可视化为彩色网格。显示针对重上下文工具、内存膨胀和容量警告的优化建议 |
| 17 | `/cost` | ![上下文](https://img.shields.io/badge/Context-8E44AD?style=flat) | 显示令牌使用统计。参见订阅特定详细信息的成本追踪指南 |
| 18 | `/extra-usage` | ![上下文](https://img.shields.io/badge/Context-8E44AD?style=flat) | 配置额外使用量以在达到速率限制时继续工作 |
| 19 | `/insights` | ![上下文](https://img.shields.io/badge/Context-8E44AD?style=flat) | 生成分析你的 Claude Code 会话的报告，包括项目领域、交互模式和痛点 |
| 20 | `/stats` | ![上下文](https://img.shields.io/badge/Context-8E44AD?style=flat) | 可视化每日使用情况、会话历史、连续天数和模型偏好 |
| 21 | `/status` | ![上下文](https://img.shields.io/badge/Context-8E44AD?style=flat) | 打开设置界面（状态标签）显示版本、模型、账户和连接。在 Claude 响应时工作，无需等待当前响应完成 |
| 22 | `/usage` | ![上下文](https://img.shields.io/badge/Context-8E44AD?style=flat) | 显示计划使用限制和速率限制状态 |
| 23 | `/doctor` | ![调试](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 诊断和验证你的 Claude Code 安装和设置 |
| 24 | `/feedback [report]` | ![调试](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 提交关于 Claude Code 的反馈。别名：`/bug` |
| 25 | `/help` | ![调试](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 显示帮助和可用命令 |
| 26 | `/release-notes` | ![调试](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 查看完整更新日志，最近版本最接近你的提示 |
| 27 | `/tasks` | ![调试](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 列出和管理后台任务 |
| 28 | `/copy [N]` | ![导出](https://img.shields.io/badge/Export-7F8C8D?style=flat) | 将上一个助手响应复制到剪贴板。传递数字 `N` 复制倒数第 N 个响应：`/copy 2` 复制倒数第二个。当存在代码块时，显示交互式选择器选择单个块或完整响应。在选择器中按 `w` 将选择写入文件而不是剪贴板，这在 SSH 时很有用 |
| 29 | `/export [filename]` | ![导出](https://img.shields.io/badge/Export-7F8C8D?style=flat) | 将当前对话导出为纯文本。带文件名时直接写入该文件。不带则打开对话框复制到剪贴板或保存到文件 |
| 30 | `/agents` | ![扩展](https://img.shields.io/badge/Extensions-16A085?style=flat) | 管理代理配置 |
| 31 | `/chrome` | ![扩展](https://img.shields.io/badge/Extensions-16A085?style=flat) | 在 Chrome 设置中配置 Claude |
| 32 | `/hooks` | ![扩展](https://img.shields.io/badge/Extensions-16A085?style=flat) | 查看工具事件的钩子配置 |
| 33 | `/ide` | ![扩展](https://img.shields.io/badge/Extensions-16A085?style=flat) | 管理 IDE 集成并显示状态 |
| 34 | `/mcp` | ![扩展](https://img.shields.io/badge/Extensions-16A085?style=flat) | 管理 MCP 服务器连接和 OAuth 认证 |
| 35 | `/plugin` | ![扩展](https://img.shields.io/badge/Extensions-16A085?style=flat) | 管理 Claude Code 插件 |
| 36 | `/reload-plugins` | ![扩展](https://img.shields.io/badge/Extensions-16A085?style=flat) | 重新加载所有活动插件以应用待定更改而无需重启。报告每个重新加载组件的计数并标记任何加载错误 |
| 37 | `/skills` | ![扩展](https://img.shields.io/badge/Extensions-16A085?style=flat) | 列出可用技能 |
| 38 | `/memory` | ![记忆](https://img.shields.io/badge/Memory-3498DB?style=flat) | 编辑 `CLAUDE.md` 记忆文件，启用或禁用自动记忆，并查看自动记忆条目 |
| 39 | `/effort [low\|medium\|high\|max\|auto]` | ![模型](https://img.shields.io/badge/Model-E67E22?style=flat) | 设置模型努力级别。`low`、`medium` 和 `high` 跨会话持久化。`max` 仅适用于当前会话且需要 Opus 4.6。`auto` 重置为模型默认值。不带参数则显示当前级别。立即生效，无需等待当前响应完成 |
| 40 | `/fast [on\|off]` | ![模型](https://img.shields.io/badge/Model-E67E22?style=flat) | 切换快速模式开关 |
| 41 | `/model [model]` | ![模型](https://img.shields.io/badge/Model-E67E22?style=flat) | 选择或更改 AI 模型。对于支持的模型，使用左/右箭头调整努力级别。更改立即生效，无需等待当前响应完成 |
| 42 | `/passes` | ![模型](https://img.shields.io/badge/Model-E67E22?style=flat) | 与朋友分享一周免费 Claude Code。仅在你的账户符合条件时可见 |
| 43 | `/plan [description]` | ![模型](https://img.shields.io/badge/Model-E67E22?style=flat) |直接从提示进入计划模式。传递可选描述进入计划模式并立即开始该任务，例如 `/plan 修复 auth bug` |
| 44 | `/add-dir <path>` | ![项目](https://img.shields.io/badge/Project-27AE60?style=flat) | 添加新的工作目录到当前会话 |
| 45 | `/diff` | ![项目](https://img.shields.io/badge/Project-27AE60?style=flat) | 打开交互式差异查看器显示未提交的更改和每轮差异。使用左/右箭头在当前 git 差异和各个 Claude 轮次之间切换，上下浏览文件 |
| 46 | `/init` | ![项目](https://img.shields.io/badge/Project-27AE60?style=flat) | 使用 `CLAUDE.md` 指南初始化项目。设置 `CLAUDE_CODE_NEW_INIT=true` 以获得交互式流程，同时引导你了解技能、钩子和个人记忆文件 |
| 47 | `/pr-comments [PR]` | ![项目](https://img.shields.io/badge/Project-27AE60?style=flat) | 获取并显示 GitHub pull request 的评论。自动检测当前分支的 PR，或传递 PR URL 或编号。需要 `gh` CLI |
| 48 | `/review` | ![项目](https://img.shields.io/badge/Project-27AE60?style=flat) | 已弃用。请改为安装 `code-review` 插件：`claude plugin install code-review@claude-plugins-official` |
| 49 | `/security-review` | ![项目](https://img.shields.io/badge/Project-27AE60?style=flat) | 分析当前分支待定更改的安全漏洞。审查 git 差异并识别注入、认证问题和数据暴露等风险 |
| 50 | `/desktop` | ![远程](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 在 Claude Code Desktop 应用中继续当前会话。仅限 macOS 和 Windows。别名：`/app` |
| 51 | `/install-github-app` | ![远程](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 为仓库设置 Claude GitHub Actions 应用。引导你选择仓库并配置集成 |
| 52 | `/install-slack-app` | ![远程](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 安装 Claude Slack 应用。打开浏览器完成 OAuth 流程 |
| 53 | `/mobile` | ![远程](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 显示二维码下载 Claude 移动应用。别名：`/ios`、`/android` |
| 54 | `/remote-control` | ![远程](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 使此会话可从 claude.ai 进行远程控制。别名：`/rc` |
| 55 | `/remote-env` | ![远程](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 配置使用 `--remote` 启动的 Web 会话的默认远程环境 |
| 56 | `/schedule [description]` | ![远程](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 创建、更新、列出或运行云端计划任务。Claude 会话式地引导你完成设置 |
| 57 | `/branch [name]` | ![会话](https://img.shields.io/badge/Session-4A90D9?style=flat) | 在此点创建当前对话的分支。别名：`/fork` |
| 58 | `/btw <question>` | ![会话](https://img.shields.io/badge/Session-4A90D9?style=flat) | 快速提问而不添加到对话中 |
| 59 | `/clear` | ![会话](https://img.shields.io/badge/Session-4A90D9?style=flat) | 清除对话历史并释放上下文。别名：`/reset`、`/new` |
| 60 | `/compact [instructions]` | ![会话](https://img.shields.io/badge/Session-4A90D9?style=flat) | 压缩对话，可选专注指令 |
| 61 | `/exit` | ![会话](https://img.shields.io/badge/Session-4A90D9?style=flat) | 退出 CLI。别名：`/quit` |
| 62 | `/rename [name]` | ![会话](https://img.shields.io/badge/Session-4A90D9?style=flat) | 重命名当前会话并在提示栏显示名称。不带名称则从对话历史自动生成 |
| 63 | `/resume [session]` | ![会话](https://img.shields.io/badge/Session-4A90D9?style=flat) | 按 ID 或名称恢复对话，或打开会话选择器。别名：`/continue` |
| 64 | `/rewind` | ![会话](https://img.shields.io/badge/Session-4A90D9?style=flat) | 将对话和/或代码倒回到先前一点，或从选中的消息开始总结。参见检查点。别名：`/checkpoint` |

捆绑技能如 `/debug` 也可能出现在斜杠命令菜单中，但它们不是内置命令。

---

## 参考资料

- [Claude Code 斜杠命令](https://code.claude.com/docs/en/slash-commands)
- [Claude Code 交互模式](https://code.claude.com/docs/en/interactive-mode)
- [Claude Code 更新日志](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
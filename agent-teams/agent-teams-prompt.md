# 创建代理团队构建时间编排工作流

创建一个代理团队来构建一个时间编排工作流，将当前迪拜时间显示为可视化 SVG 卡片。该工作流遵循 Command → Agent → Skill 架构模式：

- 命令（Command）编排流程并处理用户交互
- 代理（Agent）使用预加载的技能获取迪拜的实时时间
- 技能（Skill）根据获取的数据创建可视化 SVG 时间卡片

**重要**：所有文件必须创建在 `agent-teams/.claude/` 目录内，而不是仓库根目录的 `.claude/` 目录中。这可以使代理团队的输出保持独立，可通过 `cd agent-teams && claude` 运行。请勿引用或复制现有的天气工作流——从头开始构建所有内容。

请分配以下队友：

1. **命令架构师** — 在 `agent-teams/.claude/commands/time-orchestrator.md` 中设计和实现 `/time-orchestrator` 命令。该命令应该：
   - 通过 Agent 工具（而非 bash）调用 time-agent 来获取迪拜当前时间（亚洲/迪拜时区，UTC+4）
   - 通过 Skill 工具调用 time-svg-creator 技能，从获取的时间数据渲染 SVG 卡片
   - 使用 frontmatter 中的 model: haiku
   - 包含关键要求：顺序流程、正确的工具使用（Agent 工具用于代理，Skill 工具用于技能），以及输出摘要
   - 通过共享任务列表与其他队友协调，就组件之间传递的数据契约（{time, timezone, formatted}）达成一致。

2. **代理工程师** — 在 `agent-teams/.claude/agents/time-agent.md` 中设计并实现 `time-agent`，以及其在 `agent-teams/.claude/skills/time-fetcher/SKILL.md` 中的预加载 `time-fetcher` 技能。代理应该：
   - 使用 Bash 获取迪拜当前时间（Asia/Dubai, UTC+4）：`TZ='Asia/Dubai' date '+%Y-%m-%d %H:%M:%S %Z'`
   - 将时间值、时区名称和格式化字符串返回给命令
   - 使用 frontmatter：tools (Bash)、model: haiku、color: blue、maxTurns: 3
   - 通过 `skills:` 字段预加载 time-fetcher 技能
   - time-fetcher 技能（`agent-teams/.claude/skills/time-fetcher/SKILL.md`）应包含获取迪拜时间的 bash 命令、预期输出格式，并设置 user-invocable: false，因为这是仅代理可用的领域知识。
   - 将约定的数据契约发布到共享任务列表，以便命令架构师和技能设计师就接口达成一致。

3. **技能设计师** — 在 `agent-teams/.claude/skills/time-svg-creator/SKILL.md` 中设计并实现 `time-svg-creator` 技能，并附带支持文件 `reference.md`（SVG 模板 + 输出模板）和 `examples.md`（示例输入/输出对）。该技能应该：
   - 从调用上下文接收时间值、时区和格式化字符串
   - 为迪拜创建显示当前时间的独立 SVG 时间卡片
   - 将 SVG 写入 `agent-teams/output/dubai-time.svg`
   - 将 Markdown 摘要写入 `agent-teams/output/output.md`
   - 使用提供的确切时间——切勿重新获取
   - 将模板保留在 reference.md 中（带占位符的 SVG 标记、Markdown 输出模板），示例对保留在 examples.md 中
   - 还要创建 `agent-teams/output/` 目录用于输出文件。

所有三个队友都应在共享任务列表中创建任务以协调数据契约：代理返回 {time, timezone, formatted}，命令通过上下文传递它，技能消费它。并行启动所有三个，因为组件是独立的——它们只需要就数据接口达成一致，无需等待彼此的实现。
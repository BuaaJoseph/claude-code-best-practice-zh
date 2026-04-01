# 大型 Monorepo 中的 Claude 技能发现

在 monorepo 中使用 Claude Code 时，了解技能如何被发现并加载到上下文中对于有效组织项目特定能力至关重要。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 与 CLAUDE.md 的重要区别

**技能与 CLAUDE.md 文件的加载行为不同。** 虽然 CLAUDE.md 文件向上遍历目录树（祖先加载），但技能使用不同的发现机制，专注于项目内的嵌套目录。

## 技能如何被发现

### 1. 标准技能位置

技能从这些固定位置根据作用域加载：

| 位置 | 路径 | 适用于 |
|----------|------|------------|
| 企业 | 托管设置 | 组织中所有用户 |
| 个人 | `~/.claude/skills/<技能名称>/SKILL.md` | 你的所有项目 |
| 项目 | `.claude/skills/<技能名称>/SKILL.md` | 仅此项目 |
| 插件 | `<插件>/skills/<技能名称>/SKILL.md` | 插件启用位置 |

### 2. 从嵌套目录自动发现

当你使用子目录中的文件时，Claude Code 自动从嵌套的 `.claude/skills/` 目录发现技能。例如，如果你在编辑 `packages/frontend/` 中的文件，Claude Code 也会在 `packages/frontend/.claude/skills/` 中寻找技能。

这支持 packages 有自己技能 的 monorepo 设置。

## 示例 Monorepo 结构

考虑一个带有独立 packages 的典型 monorepo：

```
/mymonorepo/
├── .claude/
│   └── skills/
│       └── shared-conventions/SKILL.md    # 项目级技能
├── packages/
│   ├── frontend/
│   │   ├── .claude/
│   │   │   └── skills/
│   │   │       └── react-patterns/SKILL.md  # 前端特定技能
│   │   └── src/
│   │       └── App.tsx
│   ├── backend/
│   │   ├── .claude/
│   │   │   └── skills/
│   │   │       └── api-design/SKILL.md      # 后端特定技能
│   │   └── src/
│   └── shared/
│       ├── .claude/
│       │   └── skills/
│       │       └── utils-patterns/SKILL.md  # 共享工具技能
│       └── src/
```

## 场景 1：刚开始在根目录运行 Claude（尚未编辑文件）

当你从 `/mymonorepo` 运行 Claude Code 且尚未编辑任何文件时：

```bash
cd /mymonorepo
claude
# 刚开始 - 尚未编辑文件
```

| 技能 | 在上下文中？ | 原因 |
|-------|-------------|--------|
| `shared-conventions` | **是** | 项目级技能在根 `.claude/skills/` |
| `react-patterns` | **否** | 未发现 — 尚未在 `packages/frontend/` 中处理文件 |
| `api-design` | **否** | 未发现 — 尚未在 `packages/backend/` 中处理文件 |
| `utils-patterns` | **未** | 未发现 — 尚未在 `packages/shared/` 中处理文件 |

## 场景 2：在 Package 中编辑文件后

在你让 Claude 编辑 `packages/frontend/src/App.tsx` 后：

| 技能 | 在上下文中？ | 原因 |
|-------|-------------|--------|
| `shared-conventions` | **是** | 项目级技能在根 `.claude/skills/` |
| `react-patterns` | **是** | 在 `packages/frontend/` 中编辑文件时发现 |
| `api-design` | **否** | 仍未发现 — 尚未在 `packages/backend/` 中处理文件 |
| `utils-patterns` | **否** | 仍未发现 — 尚未在 `packages/shared/` 中处理文件 |

**关键洞察**：嵌套技能在**你使用这些目录中的文件时按需发现**。它们不会在会话开始时预加载。

## 关键行为：描述 vs 完整内容

技能描述被加载到上下文中以便 Claude 知道可用的内容，��**完整内容仅在调用时加载**。这是重要的优化：

- **描述**：始终在上下文中（在字符预算内）
- **完整内容**：在技能被调用时按需加载

> 注意：带有预加载技能的子代理工作方式不同 — 完整技能内容在启动时注入。

## 优先级顺序（技能共享名称时）

当技能在同一名称跨级别共享时，较高优先级的位置优先：

| 优先级 | 位置 | 作用域 |
|----------|----------|-------|
| 1（最高） | 企业 | 组织范围 |
| 2 | 个人（`~/.claude/skills/`） | 你的所有项目 |
| 3（最低） | 项目（`.claude/skills/`） | 仅此项目 |

插件技能使用 `plugin-name:skill-name` 命名空间，因此它们不会与其他级别冲突。

## 为什么这种设计适合 Monorepos

- **Package 特定技能保持隔离** — 在 `packages/frontend/` 中工作的前端开发者获得前端特定技能，而不会让后端技能弄乱上下文。

- **自动发现减少配置** — 无需显式注册 package 级技能；当你处理这些目录中的文件时会自动发现。

- **上下文优化** — 初始仅加载技能描述，嵌套技能按需发现。

- **团队可以维护自己的技能** — 每个 package 团队可以定义特定于其领域的技能，而无需与其他团队协调。

## 字符预算注意事项

技能描述被加载到上下文中，直至字符预算（默认 15,000 字符）。在具有许多 packages 和技能的大型 monorepo 中，你可能会达到此限制。

- 运行 `/context` 检查关于排除技能的警告
- 设置 `SLASH_COMMAND_TOOL_CHAR_BUDGE` 环境变量来增加限制

## 最佳实践

1. **将共享工作流放在根 `.claude/skills/`** — 仓库范围的约定、提交工作流和共享模式。

2. **将 package 特定技能放在 package 的 `.claude/skills/`** — 框架特定模式、组件约定、该 package 独有的测试工具。

3. **对危险技能使用 `disable-model-invocation: true`** — 部署或破坏性技能应需要明确的用户调用。

4. **保持技能描述简洁** — 描述始终在上下文中（直到字符预算），所以冗长的描述会浪费上下文空间。

5. **在技能名称中使用命名空间** — 考虑使用包名前缀（例如 `frontend-review`、`backend-deploy`）以避免混淆。

## 对比：技能 vs CLAUDE.md 加载

| 行为 | CLAUDE.md | 技能 |
|----------|-----------|--------|
| 祖先加载（向上遍历目录树） | 是 | 否 |
| 嵌套/后代发现（向下遍历目录树） | 是（延迟） | 是（自动发现） |
| 全局位置 | `~/.claude/CLAUDE.md` | `~/.claude/skills/` |
| 项目位置 | `.claude/` 或仓库根 | `.claude/skills/` |
| 内容加载 | 完整内容 | 仅描述（调用时加载完整） |

---

## 来源

- [Claude Code 文档 - 使用技能扩展 Claude](https://code.claude.com/docs/en/skills)
- [Claude Code 文档 - 从嵌套目录自动发现](https://code.claude.com/docs/en/skills#automatic-discovery-from-nested-directories)
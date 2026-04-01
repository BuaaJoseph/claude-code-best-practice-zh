# 验证检查清单 — README CONCEPTS 部分

验证 CONCEPTS 表准确性的规则。每个规则在每次工作流运行中检查。

## 规则

### 1. 外部 URL 活跃性
- **类别**：URL 准确性
- **检查内容**：CONCEPTS 表中的每个外部 URL（文档链接）返回有效页面
- **深度**：获取每个 URL 并确认它加载预期的页面（不是重定向到错误的页面）
- **对比依据**：`https://code.claude.com/docs/llms.txt` 的规范 URL 列表
- **添加日期**：2026-03-02
- **来源**：发现 Permissions URL `/iam` 重定向到 Authentication 页面而不是 Permissions

### 2. 锚点片段有效性
- **类别**：URL 准确性
- **检查内容**：带有锚点片段（`#section-name`）的任何 URL 与目标页面上的实际标题匹配
- **深度**：获取页面并验证带有所需锚点的标题存在
- **对比依据**：获取的页面内容
- **添加日期**：2026-03-02
- **来源**：Rules 锚点 `#modular-rules-with-clauderules` 已过时；章节重命名为 `#organize-rules-with-clauderules`

### 3. 缺失的文档页面
- **类别**：缺失的概念
- **检查内容**：官方文档索引（`llms.txt`）中代表用户面向功能的每个页面在 CONCEPTS 表中有对应的行
- **深度**：将完整文档索引与 CONCEPTS 表条目进行比较
- **对比依据**：`https://code.claude.com/docs/llms.txt`
- **添加日期**：2026-03-02
- **来源**：发现多个缺失的概念（Agent Teams、Keybindings、Model Configuration 等）

### 4. 本地徽章链接有效性
- **类别**：徽章准确性
- **检查内容**：CONCEPTS 表中的每个徽章目标路径（`best-practice/*.md`、`implementation/*.md`、`.claude/*/`）指向存在文件或目录
- **深度**：使用 Read/Glob 验证文件存在性
- **对比依据**：本地文件系统
- **添加日期**：2026-03-02
- **来源**：初始检查清单创建

### 5. 描述时效性
- **类别**：描述准确性
- **检查内容**：每个概念的描述准确反映当前官方文档的描述
- **深度**：将 README 描述与官方页面的 meta 描述或第一段进行比较
- **对比依据**：官方文档页面内容
- **添加日期**：2026-03-02
- **来源**：Memory 描述缺少 auto memory；MCP Servers 位置缺少 `.mcp.json`
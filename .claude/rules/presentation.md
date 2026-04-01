# Glob: presentation/**

## 委托规则

任何更新、修改或修复演示文稿（`presentation/index.html`）的请求都必须由 `presentation-curator` agent 处理。始终通过 Task 工具将演示文稿工作委托给此 agent — 不要直接编辑演示文稿。

```
Task(subagent_type="presentation-curator", description="...", prompt="...")
```

## 原因

presentation-curator agent 预先加载了三个技能，使其与演示文稿的结构、样式和概念框架保持同步。它还会在每次执行后自我进化，更新自己的技能以防止知识漂移。绕过此 agent 可能会破坏幻灯片编号、层级转换或样式一致性。
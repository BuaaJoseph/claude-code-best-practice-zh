# 跨模型（Claude Code + Codex）工作流

基于 [claude-code-best-practice](https://github.com/shanraisshan/claude-code-best-practice) 和 [codex-cli-best-practice](https://github.com/shanraisshan/codex-cli-best-practice)

## 工作流

```
┌─────────────────────────────────────────────────────────────────────────┐
│              跨模型 CLAUDE CODE + CODEX 工作流                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  步骤 1：计划                                        Claude Code       │
│  ─────────────                                         Opus 4.6        │
│  在计划模式下打开 Claude Code（终端 1）。           计划模式           │
│  Claude 通过 AskUserQuestion 对你进行访谈。                         │
│  生成带测试关卡的分阶段计划。                                     │
│                                                                         │
│  输出：plans/{feature-name}.md                                       │
│                                                                         │
│                              ▼                                        │
│                                                                         │
│  步骤 2：QA 评审                                    Codex CLI          │
│  ──────────────────                                    GPT-5.4         │
│  在另一个终端（终端 2）中打开 Codex CLI。                         │
│  Codex 根据实际代码库审查计划。                                    │
│  插入中间阶段（"阶段 2.5"）                                       │
│  带"Codex 发现"标题。                                              │
│  添加到计划 — 永不重写原始阶段。                                    │
│                                                                         │
│  输出：plans/{feature-name}.md（已更新）                            │
│                                                                         │
│                              ▼                                        │
│                                                                         │
│  步骤 3：实施                                       Claude Code       │
│  ──────────────────                                    Opus 4.6         │
│  启动新的 Claude Code 会话（终端 1）。                            │
│  你逐步实施阶段                                                   │
│  在每个阶段带测试关卡。                                           │
│                                                                         │
│                              ▼                                        │
│                                                                         │
│  步骤 4：验证                                       Codex CLI          │
│  ────────────────                                      GPT-5.4         │
│  启动新的 Codex CLI 会话（终端 2）。                              │
│  Codex 验证实施                                                   │
│  对照计划。                                                       │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

## 跨模型工作流在生产中的实际样子

![跨模型工作流](assets/cross-model-workflow.png)

*最后更新：2026-03-06*
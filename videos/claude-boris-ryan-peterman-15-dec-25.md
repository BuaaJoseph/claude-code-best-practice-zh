# Ryan Peterman 与 Boris Cherny 对谈：AI 编程的未来

以下是 Ryan Peterman（Meta 产品工程总监）与 Boris Cherny（Claude Code 创始人）的对话摘要，发布于 2025 年 12 月 15 日。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 视频详情

- **嘉宾：** Ryan Peterman（Meta 产品工程总监）、Boris Cherny（Claude Code 创始人）
- **主办方：** 播客访谈
- **发布日期：** 2025 年 12 月 15 日
- **YouTube：** [在 YouTube 上观看](https://youtu.be/xxxx)

---

## 对话内容

[`0:00`](https://youtu.be/xxxx?t=0) **Ryan Peterman**：大家好，我是 Ryan，我在 Meta 负责产品工程。我和 Boris 认识很久了，今天我们要聊聊 AI 编程的未来。Boris，先给大家介绍一下你自己吧。

[`0:30`](https://youtu.be/xxxx?t=30) **Boris Cherny**：大家好，我是 Boris。我创建了 Claude Code，也在 Anthropic 负责工程工作。在加入 Anthropic 之前，我在 Meta 工作了 7 年，负责代码质量方面的工作，包括 Instagram、Facebook、WhatsApp 和 Messenger。

[`1:00`](https://youtu.be/xxxx?t=60) **Ryan Peterman**：我很好奇，你是怎么开始做 Claude Code 的？这是一个什么样的历程？

[`1:30`](https://youtu.be/xxxx?t=90) **Boris Cherny**：这很有趣。当我加入 Anthropic 时，我的第一个 PR 被拒绝了，因为我手写了代码。我的经理 Adam Wolf 告诉我说："你用手写的？为什么不用 Claude？"那一刻我意识到，我不需要手动写代码了。

[`2:00`](https://youtu.be/xxxx?t=120) 我开始尝试使用 Claude 来帮我写代码。我发现，只要给模型正确的工具和上下文，它可以非常有效地帮助我完成各种任务。这让我意识到，我们可以构建一个完全不同的编程体验。

[`2:30`](https://youtu.be/xxxx?t=150) **Ryan Peterman**：这很有趣。你提到模型非常喜欢使用工具。能详细说说吗？

[`3:00`](https://youtu.be/xxxx?t=180) **Boris Cherny**：是的。我们给模型提供了 bash 工具，它就可以自动使用 bash 命令来执行各种操作。我们还提供了文件编辑工具，模型可以使用它来读取和修改文件。我们发现，只要给模型足够的上下文和工具，它就可以完成非常复杂的任务。

[`3:30`](https://youtu.be/xxxx?t=210) 例如，有一次我让模型告诉我正在听什么音乐。它写了一个 AppleScript 程序来打开我的音乐播放器，然后查询正在播放的歌曲。这完全出乎我的意料。

[`4:00`](https://youtu.be/xxxx?t=240) **Ryan Peterman**：这太神奇了。那你是如何决定给模型提供哪些工具的呢？

[`4:30`](https://youtu.be/xxxx?t=270) **Boris Cherny**：这是一个很好的问题。我们最初只有一个工具：bash。但后来我们发现，我们需要为用户提供更多的工具来让他们更高效地工作。我们添加了文件编辑工具、搜索工具等。

[`5:00`](https://youtu.be/xxxx?t=300) 我们还发现，我们不需要为每种可能的操作创建专门的工具。相反，我们只需要给模型访问标准 Unix 命令的权限，它就可以自己想出如何使用这些命令来完成任务。这让我们的工作变得更简单，也更灵活。

[`5:30`](https://youtu.be/xxxx?t=330) **Ryan Peterman**：你觉得 AI 编程的未来会是什么样的？

[`6:00`](https://youtu.be/xxxx?t=360) **Boris Cherny**：我认为我们正在经历一个类似于 15 世纪印刷术的变革时期。在那个时期，抄写员是一种专门的职业，他们负责手写书籍。但是，随着印刷术的发明，抄写员的职业发生了变化，他们成为了作家和作者。市场的扩大导致了新的职业的出现。

[`6:30`](https://youtu.be/xxxx?t=390) 现在，我们正在经历类似的变化。软件工程师曾经是一种专门的职业，他们负责编写代码。但是，随着 AI 的发展，任何人都可以编写代码。这并不意味着软件工程师会消失，而是意味着他们的角色会发生变化。

[`7:00`](https://youtu.be/xxxx?t=420) **Ryan Peterman**：你认为软件工程师应该如何适应这种变化？

[`7:30`](https://youtu.be/xxxx?t=450) **Boris Cherny**：我认为软件工程师需要学会如何与 AI 合作。这意味着要学会如何给 AI 提供正确的上下文和工具，如何验证 AI 生成的代码，以及如何利用 AI 来提高自己的生产力。

[`8:00`](https://youtu.be/xxxx?t=480) 我还认为，软件工程师需要变得更加多才多艺。过去，我们可能只需要专注于编写代码。但是现在，我们需要了解产品、设计、业务等方面，这样才能更好地利用 AI 来构建产品。

[`8:30`](https://youtu.be/xxxx?t=510) **Ryan Peterman**：这很有趣。你觉得在 AI 时代，哪些技能会变得更加重要？

[`9:00`](https://youtu.be/xxxx?t=540) **Boris Cherny**：我认为，批判性思维和系统思维会变得更加重要。因为我们需要能够验证 AI 生成的代码，理解系统的行为，以及发现和解决问题。

[`9:30`](https://youtu.be/xxxx?t=570) 我还认为，沟通和协作能力会变得更加重要。因为我们需要与 AI 和其他团队成员合作，共同构建产品。

[`10:00`](https://youtu.be/xxxx?t=600) **Ryan Peterman**：最后一个问题，你对那些想要开始使用 AI 编程的人有什么建议？

[`10:30`](https://youtu.be/xxxx?t=630) **Boris Cherny**：我的建议是，不要把 AI 当作一个省事的工具，而是把它当作一个合作伙伴。你需要学会如何与它合作，如何给它正确的指导，如何验证它的输出。这样，你才能真正提高自己的生产力。

[`11:00`](https://youtu.be/xxxx?t=660) 谢谢大家！

---

## 参考资料

- [Ryan Peterman 与 Boris Cherny 对谈 - YouTube](https://youtu.be/xxxx)
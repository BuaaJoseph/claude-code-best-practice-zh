# Lenny's Podcast：Boris Cherny 谈 Claude Code 的工程实践

以下是 Boris Cherny 在 Lenny's Podcast 上的访谈摘要，发布于 2026 年 2 月 19 日。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 视频详情

- **嘉宾：** Boris Cherny（Claude Code 创始人、Anthropic 工程负责人）
- **主持人：** Lenny Rachitsky
- **发布日期：** 2026 年 2 月 19 日
- **YouTube：** [在 YouTube 上观看](https://youtu.be/xxx)

---

## 访谈内容

[`0:00`](https://youtu.be/xxx?t=0) **Lenny**：大家好，欢迎来到 Lenny's Podcast。今天我非常荣幸地邀请到了 Boris Cherny，他是 Anthropic 的工程师，也是 Claude Code 的创始人。Boris，先给大家介绍一下你自己吧。

[`0:30`](https://youtu.be/xxx?t=30) **Boris**：大家好，我是 Boris。我在 Anthropic 负责 Claude Code 的开发工作。在加入 Anthropic 之前，我在 Meta 工作了 7 年，负责代码质量方面的工作。我曾经负责 Instagram、Facebook、WhatsApp 和 Messenger 的代码质量，也是公司最高产的代码作者和代码审查者之一。

[`1:00`](https://youtu.be/xxx?t=60) **Lenny**：太棒了。我听说你现在每天提交 20-30 个 PR，而且都是 Claude Code 写的。能详细说说吗？

[`1:30`](https://youtu.be/xxx?t=90) **Boris**：是的，我现在每天大约提交 20-30 个 PR，所有的代码都是 Claude Code 写的。我没有手动编辑过任何一行代码。这听起来可能有点夸张，但这是真的。

[`2:00`](https://youtu.be/xxx?t=120) 我记得有一天，我在欧洲旅行，当时我在不同的时区工作。我那天写了大约 20 个 PR，每一个都是 Claude Code 写的。我没有手动编辑过任何一行代码。那个月结束时，Opus 引入的 bug 数量大约是 2 个，而如果是我手动编写的话，可能会有 20 个或更多。

[`2:30`](https://youtu.be/xxx?t=150) **Lenny**：这太神奇了。那你是如何使用 Claude Code 的？有什么工作流程可以分享吗？

[`3:00`](https://youtu.be/xxx?t=180) **Boris**：我通常会同时运行多个 Claude Code 实例。我有五个终端标签页，每个都对应一个代码仓库。我会在每个标签页中启动 Claude Code，通常在计划模式下。然后我会并行地处理多个任务。

[`3:30`](https://youtu.be/xxx?t=210) 我还会使用 iOS 应用。有时候我在外面的时候，会在手机上启动几个 agent，让它们在云端运行。我需要做的只是配置环境，然后就可以让它们工作了。

[`4:00`](https://youtu.be/xxx?t=240) **Lenny**：你觉得 Claude Code 对软件工程的影响是什么？

[`4:30`](https://youtu.be/xxx?t=270) **Boris**：我认为我们正在经历一个类似于 15 世纪印刷术的变革时期。在那个时期，抄写员是一种专门的职业，他们负责手写书籍。但是，随着印刷术的发明，抄写员的职业发生了变化，他们成为了作家和作者。市场的扩大导致了新的职业的出现。

[`5:00`](https://youtu.be/xxx?t=300) 现在，我们正在经历类似的变化。软件工程师曾经是一种专门的职业，他们负责编写代码。但是，随着 AI 的发展，任何人都可以编写代码。这并不意味着软件工程师会消失，而是意味着他们的角色会发生变化。

[`5:30`](https://youtu.be/xxx?t=330) **Lenny**：那你觉得软件工程师应该如何适应这种变化？

[`6:00`](https://youtu.be/xxx?t=360) **Boris**：我认为软件工程师需要学会如何与 AI 合作。这意味着要学会如何给 AI 提供正确的上下文和工具，如何验证 AI 生成的代码，以及如何利用 AI 来提高自己的生产力。

[`6:30`](https://youtu.be/xxx?t=390) 我还认为，软件工程师需要变得更加多才多艺。过去，我们可能只需要专注于编写代码。但是现在，我们需要了解产品、设计、业务等方面，这样才能更好地利用 AI 来构建产品。

[`7:00`](https://youtu.be/xxx?t=420) **Lenny**：你觉得 AI 编程工具的未来会是什么样的？

[`7:30`](https://youtu.be/xxx?t=450) **Boris**：我认为，未来会有更多的形式因素出现。我们正在尝试不同的方式来让用户与 Claude Code 交互，比如网页版、移动端、IDE 插件等。我们相信，随着模型的不断改进，我们能够为用户提供更好的体验。

[`8:00`](https://youtu.be/xxx?t=480) 我们还推出了一个新的产品，叫做 Co-work。它是专门为非技术人员设计的。它可以在虚拟机的环境中运行，这样可以确保安全性。用户可以通过自然语言与它交互，它可以帮助他们完成各种任务。

[`8:30`](https://youtu.be/xxx?t=510) **Lenny**：最后一个问题，你对那些想要开始使用 AI 编程的人有什么建议？

[`9:00`](https://youtu.be/xxx?t=540) **Boris**：我的建议是，不要把 AI 当作一个省事的工具，而是把它当作一个合作伙伴。你需要学会如何与它合作，如何给它正确的指导，如何验证它的输出。这样，你才能真正提高自己的生产力。

[`9:30`](https://youtu.be/xxx?t=570) 另外，不要害怕尝试。我刚开始使用 Claude Code 的时候，也花了一段时间才找到正确的工作流程。但是随着时间的推移，我越来越熟练，也越来越有效率。

[`10:00`](https://youtu.be/xxx?t=600) 谢谢大家！

---

## 参考资料

- [Lenny's Podcast - Boris Cherny 访谈 - YouTube](https://youtu.be/xxx)
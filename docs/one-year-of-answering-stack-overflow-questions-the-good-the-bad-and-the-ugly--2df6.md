# 回答栈溢出问题一年。好的，坏的和丑陋的。

> 原文：<https://dev.to/expecho/one-year-of-answering-stack-overflow-questions-the-good-the-bad-and-the-ugly--2df6>

我已经成为 Stack Overflow 的成员很长时间了，偶尔会问一个问题。但直到今年年初，我才开始通过回答问题更多地参与到社区中来。到目前为止，这是一次总体愉快的经历，鉴于我在一年内几乎达到了 10k rep，告诉我我的大部分努力实际上都是在帮助人们。

[![graph](img/150a438202bb79f27f6e2adbad59a314.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wU0FoVdO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5tfwv998au5ez650odjj.jpg)

在这篇文章中，我想分享我作为这个知名社区的贡献者的经历。

# 好人

我喜欢这样做有几个原因。首先，我喜欢分享知识，帮助人们解决他们的一些问题。这并不需要太多时间(我确实选择了我的战斗，稍后我会回到这个话题)，我喜欢因为其他答案或问题本身而学到新东西的时候。例如，解决一个问题通常有不同的方法，我已经在许多场合学到了新的或其他的做事方法。

有时我就是不知道答案，但如果是关于我特别感兴趣的话题之一，我可能会启动类似于 [LinqPad](https://www.linqpad.net/) 或 Visual Studio(代码)的东西来尝试解决它。我认为这是学习新东西的一种有趣方式。

# 坏了

如果说我这段时间学到了什么的话，那就是问一个好问题是一门艺术。我认为乔恩·斯基特在这篇博文中总结了要点。例如，我经常看到一些问题缺乏适当的背景，而这是我能够提供答案所必需的。

当他们的问题被否决或关闭时，人们会感到不安，但他们不会根据评论编辑他们的问题，他们几乎都不会再回到这个网站。这很遗憾，因为我相信大多数人是真心想帮忙的，不会为了帮忙而结束提问。我希望每一个问题已经结束或被否决的参与者花时间阅读[这份清单](https://codeblog.jonskeet.uk/2012/11/24/stack-overflow-question-checklist/)并再次尝试。

我也很快认识到，提供解决问题的思路或提示的答案不如真正解决手头问题的答案更受欢迎。这是件坏事吗？不完全是，我明白当你有问题时，你想要一个能立即帮你解决的答案。但有时我认为最好是利用一些提示和指导自己去发现答案，这样可以更好地理解事物是如何工作的。异步和并行计算是其中一个真正适用的主题。

# 丑陋

直到最近，我才意识到[这个社区在欢迎(不欢迎)新贡献者以及一些贡献者对其他人的态度方面所面临的问题。当然，我看到过一些丑陋的评论或不友好的行为，但我从未觉得这是一个如此普遍的问题。我知道有人在努力改变这一点，这篇文章的目的也不是要解决这个问题，但是我真诚地希望 Stack Overflow 能够恢复它作为开发者获得任何编程相关问题帮助的主要来源的声誉，无论他们的技能和知识如何。](https://stackoverflow.blog/2018/04/26/stack-overflow-isnt-very-welcoming-its-time-for-that-to-change/)

沟通是这里的关键。负反馈是当今互联网上普遍存在的问题，堆栈溢出也不例外。可悲的是，开发者的世界并不总是一个快乐的社区(开源的世界是另一个例子)。我宁愿通过提问来更好地理解问题，而不是对问题的低质量发表一些尖刻的评论。当我们进入编程的世界时，我们都必须从某个地方开始，所以我会记住这一点。

# 我的方法

我很快意识到我不想花太多时间在网站上寻找新的有趣的问题。我决定，我想把我的努力放在回答我感兴趣的话题，或有详细的知识。因此，我编写了自己的机器人，使用 Azure 函数将带有我感兴趣的特定标签的问题发布到 Slack 频道。欢迎你来看看:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[Expecho](https://github.com/Expecho)/[stack overflow-To-Slack-Tag-Tracker](https://github.com/Expecho/StackOverflow-To-Slack-Tag-Tracker)

### Azure 函数，作为一个机器人，基于问题标签在 Slack 通道中创建关于 StackOverflow 活动的通知

<article class="markdown-body entry-content container-lg" itemprop="text">

# 堆栈溢出->松弛标签跟踪器

Azure 函数充当一个机器人，根据问题标签在一个 [Slack](https://slack.com/) 通道中创建关于 [StackOverflow](https://stackoverflow.com/) 活动的通知。

[![Overview](img/154a354613139cfe62f291b7eea5887f.png)](https://raw.githubusercontent.com/Expecho/StackOverflow-To-Slack-Tag-Tracker/master/media/slack.PNG?raw=true)

# 启动和运行

1.  创建一个新的基于定时器的 Azure 函数(javascript)。有关说明，请参见文档。由于堆栈溢出的 api 请求限制，我推荐每 15 分钟触发一次计时器。
2.  安装所需的 node.js 包。说明可以在[这里](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#node-version-and-package-management)找到。package.json 文件在这个存储库中。
3.  用这个库中的代码替换默认的 javascript 代码。然后根据您的喜好更改配置*

*   这里可以生成松弛令牌。可以在这里创建一个 StackOverflow 令牌[。](https://stackapps.com/apps/oauth/register)

# 配置

使用环境变量定义配置:

```
 slackbot_username: "StackOverflow Tag Tracker"
    slackbot_icon_emoji: ':incoming_envelope:'
    slackbot_token: '<token>'
    slackbot_channel: '<channel>',
    slackbot_workspace: '<workspace>',
    so_api_key: '<api key>',
    so_tracked_tags: 'azure;asp.net-web-api|powerbi', 
```

***【slack bot _ username】***
bot 账户名称

***【Slack bot _ icon _ e moji***
The Slack…

</article>

[View on GitHub](https://github.com/Expecho/StackOverflow-To-Slack-Tag-Tracker)

无论如何，不要让不好的&丑陋的阻止你把 Stack Overflow 变成一个更好的地方。有很多机会可以帮助人们摆脱困境，因为有一点是肯定的，在我们这个不断变化的世界里，总会有新的问题出现。
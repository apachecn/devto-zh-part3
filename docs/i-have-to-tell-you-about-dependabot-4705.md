# 我必须告诉你关于依赖机器人的事🤖

> 原文：<https://dev.to/irreverentmike/i-have-to-tell-you-about-dependabot-4705>

Dependabot 是一个自动化服务，它会自动 T2 创建 PRs 来保持你的项目的依赖关系是最新的，这真是太棒了。

[![Setup for Dependabot is flexible, quick, and straightforward.](img/daa3ee2bd87fd4a09dc09e2b799563de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Upesun-4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c344w30tpucosbc826vx.png)

在短短的几分钟内，你就可以安装并配置它来自动监视你的项目依赖关系，并设置每天或每周提交更新和更改的时间表。最完美的自动化——dependent bot 就像有一个超级留心的队友在关注 npm(或 pip 或 rubygems 或许多其他语言中的一种)——您将自动为每个依赖版本 bump 获得优秀的、格式良好的 PRs，您可以在本地进行测试，或者发送到您选择的 CI 工具链。

在您运行完设置过程后，dependabot 将监控您的回购并提交 PRs，以每天或每周的节奏(您的选择！).如果 PR 包含重要的安全更新，它也会被分配一个标签`Security`。

[![Automatically created PRs are simple and to the point](img/dddfe2dee038442c1ae06813ca9fbbfb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n_2oFBxM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c6qf7dtlnwtp0an9dqo7.png)

老实说，对我来说，这就像是我的团队增加了一个成员。我喜欢自动化，我喜欢让我的生活更简单。到目前为止，对于我所有项目的*来说，我自己或另一个队友会定期运行给定 repo 中的所有依赖项，一次手动更新一个，测试并将其推送到 GitHub 以通过 CI 运行，然后最终由团队合并。诚实的事实是，这个过程可能需要相当长的时间，而且可能会被遗忘，根本不会注意到重要的安全更新。现在一切都结束了。*

[![Dependabot will merge this PR once CI is done, thanks to the @dependabot merge command](img/805309f2ea71d3f88e18f3383048272e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HZll3BpB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n7uu7qebeuq8pv0ae4u1.png)

## 还有什么？

一旦安装了 dependabot，你就可以使用许多其他功能。它通过 GitHub 注释来响应各种各样的命令。在上面的截图中，我已经批准了一个 PR，并要求 dependabot 在 CI 通过后立即合并它。现在我不必为了合并更新而照看回购了！太棒了。

如果你的回购包含多种语言，Dependabot 可以处理。如果您有一个复杂的 monorepo，其中有多个文件表示您的依赖项(例如，用 Lerna 设置的节点项目)，dependabot 可以单独监控每个依赖项文件，每个文件有不同的规则。

## 不过是免费的午餐

[![](img/f334c18d89d440163c791656374d3185.png)](https://dependabot.com/blog/hello-github/)

这对我来说是最后一击。Dependabot】刚刚被 GitHub 收购，现在*完全免费*使用。太不可思议了！你没有理由不试一试。快去看看吧，邮政速递！

去安装[依赖机器人](https://dependabot.com)！

*注:这篇文章的封面照片来自我最喜欢的摄影师之一，[亚历山大·奈特](https://unsplash.com/@agkdesign?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)，并由联合国 [Unsplash](https://unsplash.com/search/photos/robot?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 提供。谢谢你亚历克斯的工作！在 [Patreon](https://patreon.com/agk42) 上支持他，在 [Twitter](https://twitter.com/agkdesign) 上关注他。*
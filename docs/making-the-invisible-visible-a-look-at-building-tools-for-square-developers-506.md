# 让看不见的变得看得见:Square 开发者的构建工具

> 原文：<https://dev.to/squaredev/making-the-invisible-visible-a-look-at-building-tools-for-square-developers-506>

*作者:**曾**于** [方角博客](https://medium.com/square-corner-blog/making-the-invisible-visible-a-look-at-building-tools-for-square-developers-bae30a212950)* **

在 Square，开发人员团队公开了允许第三方开发人员构建定制业务处理解决方案的 API。10 月 18 日，我们向使用我们 API 的开发者宣布，webhooks 现在将支持重试。如果一个 webhook 无法交付，我们的系统现在将能够重试多次，直到成功交付。

Square 的 webhook 交付系统是一项我称之为 Webhooks 的服务。提高 Webhooks 的可靠性是我作为一名大学毕业生在 Square 开始工作的第一周有机会深入研究的一个项目。从一开始，我们就希望我们的 Webhooks 升级不需要我们的外部开发人员做任何改变，这与项目中的大量研究、规划和工作形成了鲜明的对比。事实上，我们发出的公告向开发人员保证，他们“不需要做任何修改来支持重试 webhooks。”我的经理说这是“有史以来最看不见的变化”——事情本该如此。我们团队的重点是让开发人员能够轻松地与 Square 的 API 集成，并为他们提供可靠且易于使用的体验。也许是我的经理对我们的“无形”项目的评论启发了我，让我不仅分享了我们的 webhooks 可靠性项目，还分享了我们在 Square 的一些“可见性”实践。

阅读更多关于[方角博客](https://medium.com/square-corner-blog/making-the-invisible-visible-a-look-at-building-tools-for-square-developers-bae30a212950)
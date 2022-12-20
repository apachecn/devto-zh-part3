# 我想了解更多的技术

> 原文：<https://dev.to/funkysi1701/technology-i-want-to-learn-more-about-1ann>

在微软 Ignite 期间，我听说了很多很酷的技术，我想了解更多。学习的最好方法是用它来解决问题。

那么我能造出什么既有用又能让我玩新技术的东西呢？

我有一个 Xamarin Forms 应用程序 [Pwned Pass](https://www.funkysi1701.com/pwned-pass/) ，在 [Google play](https://play.google.com/store/apps/details?id=pwnedpasswords.pwnedpasswords) 上有超过 500 次下载，在[微软商店](https://www.microsoft.com/en-gb/p/pwned-pass/9nm2whnztnlt?rtc=1)上有超过 80 次下载。这给了我一个很小的用户群，我可以用它来利用我构建的任何东西。

我的应用程序使用了 Troy Hunt 创建的 HIBP API。我将构建自己的 API，最初它只会调用 HIBP API。建造这个会给我建造东西的经验。net Core 从设计到部署。我已经开始这样做了，我有一个空的。net core API 项目，它使用来自 Azure DevOps 的构建和发布管道部署到 Azure web app。

你可能想知道为什么我不利用 Azure 函数来构建这个 API。Azure functions 当然是一项值得学习的伟大技术。然而，我过去用它们做过一点，我不相信如果我使用 Azure 函数，我能学到所有我想学的东西。我的主要目标是通过这个博客学习和分享这些知识。很可能以后我会转而使用 Azure 函数。

另一项我渴望了解更多的技术是 Azure Key Vault。这是一种允许保护密钥、连接字符串和证书的技术。我希望我的应用程序能够安全地获取密钥和安全信息，而无需提交源代码或进行不安全的共享。

监控我的应用程序也是向我学习的一个关键。我已经使用了应用洞察，但我想扩展我对这一点的理解，以便遥测可以反馈到构建中，并阻止糟糕的部署。

下面是我想谈及的完整的学习和技术清单。这是一个很长的清单，我想随着我的工作，它会越来越长。我想定期写博客，分享我一直在做的事情。我目前有一个工作的构建和发布管道，但没有值得注意的构建或发布。我知道 Key Vault 需要尽早关注，因为 Azure 网站的身份是让这项技术正常工作的关键。

1)用构建 API。net core
2)添加构建和发布管道
3)将 Azure KeyVault 用于机密、连接字符串等
4)插入我的 Xamarin 应用程序以使用它
5)使用应用洞察监控我的 API
6)使用 CSP 保护它并将其记录到[报告 URI](https://report-uri.com/)
7)考虑使用 javascript 库或框架构建我的 API 的 web 前端。也许会做出反应，但这可以以后再决定。
8)对 API 进行 docker 化，并将 docker 映像的创建添加到构建/发布管道中
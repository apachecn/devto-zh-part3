# 用 GitPod 在云中构建一个“本地”DEV 实例(非常简单)

> 原文：<https://dev.to/ben/spin-up-a-local-instance-of-dev-in-the-cloud-with-gitpod-it-s-incredibly-simple-pij>

这个公关是周末来的:

## T2】🔗使用 Gitpod 的自动化开发设置

它允许你点击一个按钮，在浏览器中“本地”旋转 [dev.to](https://dev.to/) codebase。

几分钟之内，我就看到了应用程序的一个本地实例，其中包含所有预期的虚拟数据:

[![](img/61a43577156f09e716b33bf9f722d9b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mFbh6Aka--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1sz9tkli0lfjq.cloudfront.net/items/1C2e0F3C1Q37450c3q38/Image%25202019-04-08%2520at%252010.30.31%2520AM.png)

是的，那是我的浏览器窗口截图。

这正是我们所希望的。一段时间以来，我们一直试图使代码库对这种设置友好，这是最重要的。在我看来，当人们想要为开源做贡献时，复杂的设置是阻止他们投入的首要因素。

如果一切顺利，我们的 Rails 应用程序和所有依赖项的设置可以是轻而易举的，但是如果不顺利，你会发现自己在`rbenv`地狱中试图获得正确的版本，并想知道为什么这些随机的 C 扩展不能安装。

在 Docker 中运行应用程序有所帮助，但仍然是一个负担。进入 GitPod。以如此简单的方式旋转应用程序是我们的梦想，我们已经实现了。

仍然有打嗝和抓到你，但是现在快乐的道路真的很平坦。目前，我们仍然有一些服务依赖，如 Algolia 和我们的授权服务 GitHub 和 Twitter，它们会减缓应用程序设置的过程，这取决于你想做什么。但是我希望我们可以嘲笑这些东西，并且/或者用我们拥有的一些特性成为厂商不可知的。

要亲自尝试，请前往[我们的文档](https://docs.dev.to/installation/gitpod/)并找到此按钮:

[![](img/803c99e61cfe00edb5a6b36cdc7e0681.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rvUcC40k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/9b33027bfd31/Image%25202019-04-08%2520at%252010.45.26%2520AM.png)

按照说明设置您的密钥，然后给应用程序时间来设置和启动(这在未来可能会更快)。就这样，你就可以摆弄代码库了。欢迎拉请求！

*编辑:*

我没有意识到 GitPod 有多新，但看起来他们今天刚刚发布了一个帖子:

[![svenefftinge](img/deeb05b67114a148fd12086e2b5f3569.png)](/svenefftinge) [## Gitpod 简介 GitHub 上的无摩擦编码

### sven eff tinge Apr 8 ' 196 分钟读取

#github #productivity #opensource #cloudide](/svenefftinge/introducing-gitpod-frictionless-coding-on-github-397g)

很酷的东西，感谢你成为社区的一部分，GitPodders。
# Entropic:联合 JS 包注册中心，在 JSConfEU 2019 上宣布

> 原文：<https://dev.to/florimondmanca/entropic-federated-js-package-registry-announced-at-jsconfeu-2019-3mmo>

嘿大家好！我想我还没有在 DEV 上看到过这方面的讨论。我对 JS 社区不是很了解(我本质上是一个 Pythonista)，但是我认为一起聊聊这个会很有趣。

C J Silverio 几天前在 JSConfEU 2019 上做了一个演讲:

[https://www.youtube.com/embed/MO8hZlgK5zc](https://www.youtube.com/embed/MO8hZlgK5zc)

这个演讲非常有趣，也非常精彩——我强烈推荐你观看！但是这里有一个适度的 TL；博士

在这次演讲中，Silverio 讨论了 Entropic 项目背后的原因。

第一个原因是“JavaScript commons”(包注册中心)的一部分归一家私人公司(npm Inc)所有，该公司的唯一目的是“拿钱并把它变成更多的钱”。

她指出，让另一家私人公司(微软)运营节点包生态系统也不是解决方案。

(我们之前讨论过 DEV 上的 GitHub 包注册表:)

[![peter](img/f1bc4bdf8dfc31e2ed6c1ed6d78ad281.png)](/peter) [## GitHub 宣布 GitHub 包注册

### 彼得·金·弗兰克 1919 年 5 月 10 日 1 分钟阅读

#news #npm #github #javascript](/peter/github-announces-github-package-registry-3a27)

此外，仍然根据她的说法，建立任何其他的*集中式*包注册中心——即使由像 [OpenJS](https://openjsf.org/) 这样的基金会拥有——也不会起作用，因为 Node.js 生态系统中的包下载流量是*巨大的*。从一个中心位置托管和服务所有包的成本实在太高了。

考虑到所有的原因，在演讲的最后，Silverio 介绍了 **Entropic** ，一个“任何东西的联邦包注册中心，但主要是 JavaScript”。它附带了一个名为`ds`的 CLI(就像“delta entropy”——有点像物理学笑话)。

从 C J Silverio 自己的话来说，Entropic 还很年轻(“先别用！”)，但是已经有少数人在从事熵项目了。主存储库在这里:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [熵-发展](https://github.com/entropic-dev) / [熵](https://github.com/entropic-dev/entropic)

### 🦝 📦任何东西的包注册表，但主要是 javascript 🦝 🦝 🦝

<article class="markdown-body entry-content container-lg" itemprop="text">

# Entropic:任何东西的联邦包注册中心

[![All Contributors](img/09d64291a8224ead90a64a1b93ee96ca.png) ](https://github.com/entropic-dev/entropic#contributors) [ ![Code of Conduct](img/8632462e32b2bd0cdd901c420eae42c8.png)](https://github.com/entropic-dev/entropic./.github/CODE_OF_CONDUCT.md)

带有新 CLI 的新软件包注册表，旨在便于在您的网络中使用。Entropic 具有一个全新的以文件为中心的 API 和一个内容可寻址的存储系统，该系统试图最大限度地减少您必须通过网络检索的数据量。这种以文件为中心的方法也适用于发布 API。请参见宣言的 [API 部分，了解关于所提供 API 的更多详细信息。](https://github.com/entropic-dev/entropic/tree/master/docs#apis)

Entropic 假设许多注册中心作为正常工作流程的一部分共存并互操作。所有的 Entropic 包都有名称空间，一个完整的 Entropic 包规范还包括其注册表的主机名。

传统节点程序包管理器被视为只读归档文件。您可以通过您的 Entropic home 实例安装旧软件包。

宣言见 [docs/README.md](https://github.com/entropic-dev/entropicdocs/README.md) 。

你有兴趣投稿吗？你有什么反馈要分享吗？来和我们谈谈我们的…

</article>

[View on GitHub](https://github.com/entropic-dev/entropic)

那么，**你对这个公告有什么反应？**
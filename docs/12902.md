# 当林挺第一次被介绍时，被强迫的林挺能压制贡献吗？

> 原文：<https://dev.to/ben/can-forced-linting-surpress-contributions-when-linting-is-first-introduced-2dn5>

我们最近引入了强制林挺到 [dev.to](https://dev.to/) 视图，并通过一个预提交钩子来执行它。

在这次改变之前，我们完全缺乏视图文件林挺，所以现在当有人对视图文件进行小的改变时，他们将会看到一系列小的改变。

大多数改变都是像要求开发人员根据样式指南进行调整，比如将`<%=user.username%>`改为`<%= user.username %>`(空格优先于无空格)。

我担心这会给小额捐款增加太多的障碍，尽管我们希望我们的观点最终能得到一致的认同。

这是项目中的一个问题，但我希望这里有任何关于这个主题的一般意见。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 能否看待林挺被压制立功？ #1828](https://github.com/thepracticaldev/dev.to/issues/1828) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-02-18T23:48:22Z">Feb 18, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1828)

我有一种感觉，认为林挺可能是一个相当大的捐款负担。你可以做一点小小的贡献，却被目前林挺的一大堆琐事所困扰。

我只是觉得作为一个没有背景的局外人，这会给我增加太多的负担。有没有想过把它作为一个阻碍推的障碍，让它成为人们可以自愿取得进展的东西，直到应用程序更接近于以我们喜欢的风格完全呈现在视图上？

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1828)

我很想听听你过去是如何处理这件事的。
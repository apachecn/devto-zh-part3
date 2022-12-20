# 更简单、更方便用户的新页面

> 原文：<https://dev.to/tzemanovic/new-simpler-and-user-friendlier-page-3mcf>

正如我们在捷克语中所说的“铁匠的母马赤脚行走”(或英语中的“鞋匠的孩子赤脚行走”)，我以前的[主页](https://tzemanovic.gitlab.io/)以其基于引导的风格看起来确实如此糟糕，以至于我自己都不喜欢再看它了。

我终于找到时间对它进行了急需的改造。此外，我们行业目前的许多发展，说得轻一点，不太理想，我想确保我不只是火上浇油。不仅仅是那些我们一眼就能看到的事情更容易一笑置之，更重要的是那些隐藏在大多数人视线之外的事情 <sup>2</sup> 。本着这种精神，这个网站上的所有内容现在都是自托管的(嗯，除了评论，🤔，也许有一天我会设置类似[这种自托管评论系统](https://github.com/Libbum/oration)。

为了使事情简单，不使[变得太好](https://bestmotherfucking.website/)，我使用了 [Pure.css](https://purecss.io/) 和它的小而灵敏的模块。这很好，但坦率地说，即使我从小就开始制作网站，在被我在工作中使用了一年多的令人敬畏的 [elm-ui](https://package.elm-lang.org/packages/mdgriffith/elm-ui/latest/) (以前的样式元素)宠坏之后，回到 CSS 一直是一件非常痛苦的事情(即使是像这个网站这样简单的事情)。但是使用 Elm 最大的好处是它热情、友好和包容的社区。

我还添加了一个简单的[方法来估计博客文章的阅读时间](https://gitlab.com/tzemanovic/tzemanovic.gitlab.io/blob/master/src/Main.hs#L117)。如果你感兴趣，你可以在 [GitLab](https://gitlab.com/tzemanovic/tzemanovic.gitlab.io) 上看到这个页面的源代码。你可以在自己的页面上随意使用。

* * *

1.  [达里尔·吉恩在推特上说:“2018 年的每个网站……”](https://twitter.com/darylginn/status/1053646859686809600)[↩](#fnref1)

2.  对抗日益敌对的用户网络 [↩](#fnref2)
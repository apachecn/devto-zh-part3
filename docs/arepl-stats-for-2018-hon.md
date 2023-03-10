# 2018 年 Arepl 统计

> 原文：<https://dev.to/almenon/arepl-stats-for-2018-hon>

点击这里查看六月[的最后一篇文章](https://medium.com/@almenon214/arepl-stats-for-june-5e0c87636c3)

问:现在有多少人使用 AREPL？

一周 40 左右。这比上次的*显著增加了*，很大程度上是由于通过修改我的标题增加了 [SEO](https://www.google.com/search?q=seo) 。仅仅是对我的头衔(python 的 AREPL)两个字的改动就引起了人气暴涨。事实上，AREPL 现在是搜索 python 时的 [#2 扩展](https://marketplace.visualstudio.com/search?term=python&target=VSCode&category=All%20categories&sortBy=Relevance)。

[![](img/364978dfecd7c08a74a31a6cbd61da48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N7kfKtqn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f42uqis8lzj6zh75rxxo.png)

问:用户的国籍是什么？

[![](img/a0d1c97e2bc155538d5fc205f7900fcb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LChXzqjG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nja6yeeald4kzvyoj1ud.png)

这比上一张图更加多样化了！说真的，[读了我的上一篇文章](https://medium.com/@almenon214/arepl-stats-for-june-5e0c87636c3)，两者之间的差异简直令人难以置信。美国已经从大多数用户变成了少数用户，*甚至不是最大的少数用户。中国和英国都是预期中的存在，但巴西击败印度和日本真的让我很惊讶。这可能是一个异常值——巴西的使用量在 2019 年有所下降。我没听说过巴西有大的科技产业，但是巴西人可以随意纠正我！*

看着这张图表让我意识到我是多么以美国为中心。回想起来，美国显然是少数派。此外，特别向叙利亚的一个用户大喊一声！内战不会阻止你编程！

问:一个人每周使用 AREPL 的频率是多少？

*七月、八月和九月:*

重度用户:32 人(1%)使用过十次或十次以上。这大约是一周一次或更多。

轻度用户:227 人(60%)使用过两到十次。最多一个月几次。

只有一次的用户:121 人(32%)只使用过一次。

从新用户到重度用户 1%的转化率肯定可以提高。并不可怕——比如 google drive 从免费→付费用户只有. 5%的转化率。但与 spotify 相比，你会发现我还有很多工作要做。

[![image from https://www.process.st/freemium-conversion-rate/](img/fb9677dd18053068af243685d17f62c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m4ydOh9J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rp0q57cn5jswmguvj5vg.jpeg)

2018 年的 AREPL 仍然漏洞百出——我希望在 2019 年看到%的重度用户因我的错误修复而增加。

问:到目前为止，错误遥测有帮助吗？

是啊！当我发布一个版本时，遥测记录了错误数量的峰值(特别是配置了错误 python 路径的人)。不幸的是，我认为这是由于总体使用量的增加。原来是 mac 版因为一个大小写 bug 坏了。在我解决了错误数量下降的问题后。

问:有新的遥测记录吗？

我现在正在记录设置，这样我就可以知道人们是否真的使用了我提供的设置。我看到 printResultPlacement、showFooter 和 whenToExecute 有时会更改，但仅此而已。唯一经常更改的设置是 python path——但这是意料之中的。

一些最后注意事项:

问:我在哪里安装 AREPL？答:[https://github.com/Almenon/AREPL-vscode](https://github.com/Almenon/AREPL-vscode)

问:如何将应用洞察添加到我自己的扩展中？
[https://medium . com/@ almenon 214/adding-telemetry-to-your-vs code-extension-f3d 52d 2 e 573 c](https://medium.com/@almenon214/adding-telemetry-to-your-vscode-extension-f3d52d2e573c)
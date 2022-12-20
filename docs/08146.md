# 构建编程环境:在 2K19 中

> 原文：<https://dev.to/radio_azureus/building-a-programming-env-in-2k19-4cpb>

现在用 **git** revision power 跟踪这个文件...

在过去的几个月里，我不得不这样做好几次。在我花费大部分计算时间的#DAW 上重建我的编程环境。

由于这台机器也作为一个流工作站，它大部分时间是在#win64 操作系统中，尽管默认情况下它在#Linux 中启动(现在 MX-Linux 已经有一段时间了)

现在，在开始时，我会嘲笑构建 ENV:在 win64 中，事实是这台机器是拥有最多 ram 的机器:我们都知道编码需要 RAM。这个#DAW 的升级也将是 32GB，后来在 2700X CPU 上升级到 64GB，其余部分采用相同的硬件。

因为我真的需要编写更多的代码，所以我只是破解了按钮(NL 中的 de knoop doorhakken 的文字),并在 win7 中开始构建 ENV

令人惊讶的是操作系统的年龄是如何显示的；甚至没有一个码头工人可能。
因为各种原因上了 win8.1，又试了一次。还是失败。我读了这些文件，但想看看我是否更聪明，是否能假装 win10，开个玩笑。

然后，我离开了#docker，先去找其他关键元素:

*   git:这给了我强大的动力，比 mingw 的旧版本更新
*   vim:幸运的是包含在 git 中
*   MarkDownEdit 原因很明显
*   golang:易于安装和学习
*   nodejs:只是需要学习一下
*   python 2.7 . x 我需要 SimpleHTTPServer
*   RubyStack 整洁集成的包
*   scoop:从 bash 行获得整洁的工具安装
*   vscode:这是一个非常好的 IDE
*   虚幻引擎:是的，我也喜欢游戏 IDE

所有这些安装没有问题。实际上没有，RubyStack 总是在最后需要做样本的时候卡住。但是它工作了，我有 PostgreSQL 和 PhpMyAdmin 作为几个例子，在一个漂亮的包中，有 ruby 和其他重要的工具。

我在 twitter 上的# 301 days ofcode # 100 days ofcode # Linux # advanced programming 标签中有一个关于这个的很好的 tweet 系列。

这是其中的中间部分:[https://Twitter . com/Radio _ azure us/status/1109157486710439937](https://twitter.com/Radio_Azureus/status/1109157486710439937)

这是我将发表这个帖子的系列文章的第一部分
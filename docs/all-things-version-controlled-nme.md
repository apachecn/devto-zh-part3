# 所有事情都受版本控制

> 原文：<https://dev.to/zeitschlag/all-things-version-controlled-nme>

以上是关于[版本控制](https://en.wikipedia.org/wiki/Version_control)的一些事情、文章和想法。你已经在用了，你当然在用，不是吗？

最近，我们为德语播客[录制了一集关于软件工程之类的东西](https://codestammtis.ch)。在将近 50 分钟的时间里，我们讨论了[版本控制](https://codestammtis.ch/2018/10/29/cst014-versionskontrolle/):我们讨论了基本原理、 [git](https://git-scm.com) 、[颠覆](http://subversion.apache.org)和[化石](http://www.fossil-scm.org/index.html/doc/trunk/www/index.wiki)。

提出这个问题的，是我的新工作。当我在 10 月份开始时，我们使用 Subversion 进行版本控制，但我们想在一个月内用 git 替换它。这几乎是我的第一个任务，在我想出该做什么之后，它进行得出奇的好。在这个过程中，尤其是在向我的同事教授我仍然非常有限的 git 知识时，我学到了很多。教别人对我来说还是最有效的学习方式。这大概是提“ [Pro Git](https://git-scm.com/book/en/v2) ”的好地方，这是一本关于 Git 的书。你可以免费阅读电子书、PDF 文档或在线文档。你应该读一读，你可能会学到一些东西(新的)。

我看到一篇关于 git 中[提交消息的博文。我的同事在 Subversion 中没有投入太多的爱和精力来编写清晰的 commit 消息，这就是为什么`git log --oneline`有时有点难以阅读的原因。你最好不要在 Github 上查看我的提交。就是不要。我也还在学习。](https://chris.beams.io/posts/git-commit/)

我非常喜欢 git 的一点是它的暂存区:有时我可能会以一种非常混乱的方式工作，每次我真的很感激，git 允许我清理我的工作:使用暂存区，我可以将我的编码工作分成良好的提交，没有人会注意到这曾经是一片混乱。🤫

所以，现在我们正在 20108 版本控制系统方面工作，我偶然发现了一篇关于使用 CVS aka 的非常有趣的博文。git 之前的版本控制在一个非常有趣的关于“计算穿越时代”的博客上。最后一句总结得很好，为什么你也应该读这本书:

> 回过头来使用 CVS 一段时间是发现自己对 Git 的功能和灵活性有了新的认识的一个很好的方式。它很好地说明了为什么理解软件开发的历史会如此有益——捡起并重新检查过时的工具会教会你大量关于我们今天使用的工具背后的原因。

感谢 Michael J. Tsai】写博客的 CVS-blogpost。我可能又一次爱上了 git。
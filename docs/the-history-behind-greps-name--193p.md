# “grep”名字背后的历史

> 原文：<https://dev.to/hamza/the-history-behind-greps-name--193p>

*最初发布于[我的作品集](https://hamza-tam.gitlab.io)。*

**grep** ，一个在 Unix 世界广泛使用的工具，一个任何人都离不开的工具。这是每个 linux 初学者从一开始就接触到的第一个命令之一，所以它的功能对任何人来说都不是秘密。然而，它怪异的名字背后有着怎样的历史？

这个问题的答案需要我们回到 Unix 的早期，资源匮乏的日子，可能是处理能力或者内存资源。当时的软件开发人员必须遵守规则，开发非常简单的程序来完成工作。其中一个工具是 **ed** 文本编辑器。但是这个编辑器一点也不像我们今天使用的现代编辑器(VIM 看起来就像旁边的 JetBrains IDEs)。要从文本缓冲区中看到一行文本，你需要在行号前输入 **p** 。以及在缓冲区中搜索正则表达式的出现，我们可以像在 VIM **/regularexpression** 中那样使用。但是对于我们来说，搜索所有的出现并打印它们，我们将使用 **g/regularexpression/p** ，简单地写成 **g/re/p** 。世界就这样诞生了。

Unix 的历史充满了这些故事。关于早期计算机时代的惊人故事。
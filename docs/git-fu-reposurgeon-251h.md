# Git-Fu: reposurgeon

> 原文：<https://dev.to/detunized/git-fu-reposurgeon-251h>

在对我之前的[帖子](https://dev.to/detunized/git-fu-merge-multiple-repos-with-linear-history-4kci) [的](https://dev.to/610yesnolovely)[评论](https://dev.to/610yesnolovely/comment/8nk8)中，Harvey Thompson 提到了一个叫做 [reposurgeon](https://gitlab.com/esr/reposurgeon) 的工具，它应该可以解决我遇到的确切问题(合并一堆 Git 回购)。我安装并试用了它。我不会让你在这里悬念，它对我不起作用。

我花了一堆时间浏览巨大的[文档页面](http://www.catb.org/~esr/reposurgeon/reposurgeon.html)，它似乎既详细又模糊。这感觉太正式了，但又不能让你很好地理解整件事的内容。有很多术语是这个工具特有的，没有太多对行话的介绍。什么例子都很少。没有涵盖琐碎案例的快速入门部分。很难想出从哪里开始。我也没有在网上找到很多资源。实际上，几乎没有。似乎没有多少人使用这个工具。

我敢肯定 reposurgeon 是一个野兽。它的命令和开关似乎比`git`、`openssl`和[、`mogrify`和](https://imagemagick.org/script/mogrify.php)加起来还多。它的查询语言让甲骨文羡慕不已。但是对于我来说，我不知道如何使用它。就是一点都不直观。有些命令是非常随意的，比如`append`，它允许您将文本附加到提交消息中。然而，没有`prepend`。

查询语言相当晦涩，不像我熟悉的任何东西。这里有一个来自文档的例子:

```
define lastchange {
    @max(=B & [/ChangeLog/] & /{0}/B)? list
} 
```

以及描述:

> 列出引用包含指定字符串的 ChangeLog 文件的最后一次提交。(这里的诀窍是？将由最后一个合格的 ChangeLog blob 组成的单例集扩展到其引用提交集，listonly 会注意到这些提交。)

这对我来说毫无意义，尽管我花了相当多的时间试图理解这些文档。

对我来说，这看起来像一个运行良好的项目。它似乎有很好的发布速度(T1)，大量详细的 T2 文档(T3)，干净且有文档记录的源代码(不过我不会把所有东西都放在 T4 的一个 21k 的文件中)。

我认为问题在于它有太多非常特殊的功能，并且试图处理所有的事情。可能作者已经花了太多的时间在完善和增加他们软件的特性上。

我认为他们应该花更多的时间在 Stack Overflow 上，回答关于 reposurgeon 的问题，写这个工具的介绍和博客。我知道这个工具非常小众，是为一些非常特殊的用例设计的，但这不意味着“仅限专家”。普通用户不应该马上被高级功能吓倒。它应该有可能做一些最小的命令集平凡的转换。

看 Git。我知道这是一个用户体验的火车残骸。它有控制一切的命令。掌握 Git 可能需要一生的时间。但是如果你坚持`pull-add-commit-push`工作流程，也没那么差。强大，不应该意味着不可接近。
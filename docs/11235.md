# 你应该知道的 Git 黑客

> 原文：<https://dev.to/iankurbiswas/git-hacks-you-should-know-about-16pk>

在这篇文章中，我们将讨论一些关于 Git 的非常有用的技巧，如果你在 Git 上搞砸了，这些技巧真的可以救你一命。现在，事不宜迟，让我们开始吧🏃🏻‍♂️

## 修复上次提交的消息

你有没有遇到过这样的情况，你实际上想提交*【最终意见】*，但是你实际上输入的是*【最终意见】*。嗯，如果其他人发现你的评论由三个**m**组成，那就太遗憾了。谢天谢地，这个问题已经有了解决方案。

```
git commit --amend 
```

Enter fullscreen mode Exit fullscreen mode

只需在您的终端上打开您的项目目录，并键入上面的命令。这将打开您编辑器，并允许您对最后提交的消息进行更改。这是我真正的救命恩人。

## 更改分公司名称

让我们假设，您想要创建一个名为 *release* 的分支，但是不知何故您将您的分支命名为*release*。不要惊慌，这也有解决办法。

```
git branch -m relaese release 
```

Enter fullscreen mode Exit fullscreen mode

这会救你一命。但是如果您已经推动了这个分支，那么还需要几个额外的步骤。接下来的解决方案是，您需要从远程删除旧的分支，并推上新的分支。

```
git push origin --delete relaese
git push origin release 
```

Enter fullscreen mode Exit fullscreen mode

## 在回购中添加了错误的文件

如果你在回购中犯了不该犯的事，你就会知道情况有多糟糕。它可能是一个恶意 ENV 文件、一个构建目录、一张您的狗的图片(假设😐)你不小心存错文件夹了？一切都是可以解决的。

如果你还没有提交，那么你只需要重置文件。

```
git reset /assets/img/unknown.jpg 
```

Enter fullscreen mode Exit fullscreen mode

如果你已经做出了改变，没必要担心。你只需要在:
之前多跑一步

```
git reset --soft HEAD~1
git reset /assets/img/unknown.jpg
rm /assets/img/unknown.jpg
git commit 
```

Enter fullscreen mode Exit fullscreen mode

这将撤消提交，删除图像，然后在其位置添加一个新的提交。

## 一切都不顺利

这是你的黑桃 a！当你做的任何事情出错，你不知道该做什么的时候，这就是你的解决方案。例如，当你从**堆栈溢出**中复制粘贴了一个过多的解决方案，而你的回购比开始时更糟糕时，那么这就是你的救星。

```
git reflog 
```

Enter fullscreen mode Exit fullscreen mode

它显示了你到目前为止所做的所有事情的列表。然后，它允许您使用 Git 的时间旅行技能回到过去的任何时间点。

当您运行这个命令时，它会显示如下内容:-

```
4gg9702 (HEAD -> release) HEAD@{0}: Branch: renamed refs/heads/relaese to refs/heads/release
4gg9702 (HEAD -> relaese) HEAD@{2}: checkout: moving from master to release
3c8f619 (master) HEAD@{3}: reset: moving to HEAD~
4gg9702 (HEAD -> feature-branch) HEAD@{4}: commit: Adds the client logo
3c8f619 (master) HEAD@{5}: reset: moving to HEAD~1
48b743e HEAD@{6}: commit: Adds the client logo to the project
3c8f619 (master) HEAD@{7}: reset: moving to HEAD
3c8f619 (master) HEAD@{8}: commit (amend): Added contributing info to the site
egb38b3 HEAD@{9}: reset: moving to HEAD
egb38b3 HEAD@{10}: commit (amend): Added contributing info to the site
811e1c6 HEAD@{11}: commit: Addded contributing info to the site
fgcb806 HEAD@{12}: commit (initial): Initial commit 
```

Enter fullscreen mode Exit fullscreen mode

记住左边的列，代表索引。如果您想回到历史中的任何一点，运行下面的命令，用该引用替换{index},例如 egb38b3。

```
git reset HEAD@{index} 
```

Enter fullscreen mode Exit fullscreen mode

有一些你自己的技巧吗？请在下面的评论中告诉我。

感谢阅读！如果你觉得这很有帮助，别忘了与你的朋友和关注者分享！
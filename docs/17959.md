# 我的旧 git 进程

> 原文：<https://dev.to/jessekphillips/my-old-git-processes-19k8>

我已经过渡到使用 rebase interactive，但我认为我过去所做的可能会帮助其他人更适应周围的变化。它们的目标是组织提交，因为它们在一个有其他变更的分支上，或者我想合并提交。

首先，我要确保我打开了 gitk。这个工具很好，因为使用 F5 刷新保持了先前已知的树状态。

在您想要放置提交的提交处创建一个新的分支。现在挑选(右键单击 gitk 中的 commit)想要的变更。

如果你想把提交和最后一个结合起来，那么使用

```
git reset --soft HEAD~2
git commit -m... 
```

Enter fullscreen mode Exit fullscreen mode

我能够以任何顺序挑选我的提交，并且可以通过合并相关的提交来减少提交历史。

现在你已经完成了，通读 git rebase interactive。

[![koffeinfrei image](img/466fedb7c2526c4f7d8b228464d3baab.png)](/koffeinfrei) [## Git 修复工作流

### Alexis Reigel 9 月 29 日 184 分钟阅读

#git #devtips #productivity](/koffeinfrei/the-git-fixup-workflow-386d)
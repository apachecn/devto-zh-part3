# Git Rebase vs Merge

> 原文：<https://dev.to/dhanushkadev/git-rebase-vs-merge-13e8>

当你使用 git 时，很可能你会使用 git merge 或者 git rebase 来合并两个分支。几乎这两个命令做同样的事情。但是人们根据他们的需求选择这两种技术。这篇文章是为了比较 git merge 和 rebase 的区别以及何时使用。

下图显示了维护主要代码库的主分支和开发某些功能的功能分支。您可以看到功能分支是从主分支创建。当特征分支中发生变化时，主分支也随着新的提交而变化。
[![branch](img/688a2fd143166b678ab78280ffd2f15a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--4wcz_y0b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nqo6mgud5dzbmfpxzqkd.png)

一旦您在特性分支上完成了特性实现，您需要将它与最新的主分支结合起来。在这里，你有两个选择。您可以将特征分支与主特征分支合并或重设基础。

## Git Merge

如果您合并两个分支，它将创建一个新的提交并将主分支合并到特征分支中。这里，功能分支提交保持原样，提交历史不变。我们可以将 git merge 命令的上述图表示如下。

[![git merge](img/0a5f1736fde12e144bc250454327cc39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xUJAV4pB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ikh61zarn8kmz37qgmmx.png)

您可以通过运行以下命令将主分支合并到特征分支中

```
git checkout feature
git merge master 
```

或者用单线

```
git merge feature master 
```

## Git Rebase

Rebase 与 merge 没有什么不同。通过为每个先前的提交消息创建新的提交，在主分支之上应用所有特征分支改变。这意味着 rebase 命令将改变您的提交历史，并在主分支之上重新生成提交。git rebase 最终输出可以表示如下。

[![Rebase](img/f5e07e89f05dcb3e2a6218a787eb95b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aeSb75vo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3i914xy87kdqzcevc0c9.png)

以下命令可用于在主分支之上重设特征分支的基础。“-i”选项用于交互式 rebase。否则你可以简单地使用“git rebase master”。

```
git checkout feature
git rebase -i master 
```

## 重设基础或合并

您可以使用 rebase 或 merge 来合并两个分支。人们倾向于使用 rebase 而不是 merge，原因如下。

*   没有向合并的分支添加额外的提交
*   重定基础提交历史比合并历史更清晰，因为重定基础历史没有复杂的分支
*   由于合并 git 树中的分支复杂性，一些代码更改是不可见的。但是重定基础的改变来自于特定的和授权的提交。
*   Rebase 使得使用 git log、git 等分和 gitk 命令变得更加容易。

## 何时不使用 Rebase

正如我们已经讨论过的，rebase 更改提交历史。因此，它不应该应用于其他人正在工作的公共分支(例如:master)。这使得 git 对你的主分支与其他主分支的分歧感到困惑。

## 结论

merge 和 rebase 都可以用来合并两个分支。合并命令只是统一你的工作，而不改变历史。而 rebase apply 特征分支在主分支之上改变并改变历史。如果你喜欢有干净的历史，那么你可以使用 rebase。如果您需要保留历史更改，那么合并将是最佳选择。

希望这篇文章能帮助你了解 git merge 和 git rebase 的区别。写下你的偏好和理由。另一篇文章再见。干杯:)。

## 参考文献

[https://www.atlassian.com/git/tutorials/merging-vs-rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)
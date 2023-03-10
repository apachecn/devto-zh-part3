# 晦涩但有用的 git 命令

> 原文：<https://dev.to/hozefaj/obscure-but-useful-git-commands--21nb>

`git`几乎是整个行业使用的事实上的源代码工具。大多数工程师要么使用基于 CLI 的工具，要么使用基于 GUI 的工具，如 git-kraken(T4)或 source tree(T6)。大多数编辑器，比如 VSCode，都有现成的`git`功能。

对于像我这样喜欢使用 CLI 进行 git 的人来说，大多数人都知道常见的 git 命令，如`git add`、`git checkout`、`git status`等...

这些年来，我遇到了一些`git`命令，这些命令并不广为人知，但被证明是有用的。

### 漂亮的 Git 日志

大多数人使用`git log`来查看最近的提交历史。但是默认情况下，它不是最有用的消费格式。

[![git-log](img/e29683a0c691a2ce4655c286ee39ff32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4g4alWvX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4sj60njkv0fji2jab5h2.png)

然而，调整`.gitconfig` *文件来设置一个别名和漂亮的格式。

```
# setup alias in the .gitconfig file
lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --branches 
```

Enter fullscreen mode Exit fullscreen mode

设置别名后的格式化文本

[![git-log-pretty](img/4414c75a07461290e690da0aa6c1e78a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1cL627nH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ngkq5ryxe6wxmwwn500o.png)

* * *

### 强制推送到远程

当在一个特性分支上工作时，通常会从`master`分支中`rebase`出来。这将重写特征分支的提交历史。如果特性分支已经被推送到`remote`，我们必须使用`git push --force`将变更推送到远程。

然而，在这种情况下，如果团队中的其他人也将任何变更推送到分支，它将会丢失。与其用`--force`不如用`--force-with-lease`。这将检查其他人向同一分支推送的提交，并防止覆盖提交。

* * *

### 壁球提交

当处理一个特性分支时，在准备好 PR 之前会有多次提交。然而，为了保持整洁，您不希望这些多次提交出现在 PR 中。

最好的方法是将提交合并成一个。使用命令`git rebase -i <sha-id of master>`

这将打开一个交互式编辑器，用户可以选择保留哪些提交，压缩哪些提交。此外，可以编辑提交的消息。

[![git-squash](img/4e9c8f30b35422367444448895c222e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vHUVao1C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dg3gyk0gu/image/upload/v1550272144/transcript-images/polish-my-git-feature-branch-before-merging-or-submitting-for-review-git-rebase.jpg)

* * *

### 贮藏

当你需要移动到一个不同的而当前分支上的工作还没有完成时，`git stash`是一个很好的命令使用。

另一次，这个特性也非常有用，比如说`master`分支在特性分支上有一些你需要的更新。在这种情况下，将使用`git pull master --rebase --autostash`。这个命令会自动隐藏代码，从主节点下载最新的代码，`rebase`将代码隐藏起来，`pop`将代码隐藏起来。

* * *

### 清理

随着时间的推移，你最终可能会在回购协议上出现多个特征分支。大部分分支与`master`合并。这会导致本地`.git`文件变大，从而导致速度变慢。

为了防止这种情况发生并做一些家庭清洁，请使用`git branch | grep -v "master" | xargs git branch -D`。该命令清除除`master`之外的所有分支。

* * *

Github 还发布了一个功能，你可以在合并公关的时候[粉碎提交](https://help.github.com/en/articles/about-pull-request-merges#squash-and-merge-your-pull-request-commits)。但是如果使用类似 Bitbucket 或其他工具，他们可能没有这个功能。

*的`.gitconfig`位于根目录(`~/.gitconfig`为 Mac)。*

另一组有用的结账工具是 [git-extras](https://github.com/tj/git-extras) 。它有许多 git 工作流的实用程序。
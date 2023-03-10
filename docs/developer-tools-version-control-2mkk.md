# 开发人员工具:版本控制

> 原文：<https://dev.to/wuz/developer-tools-version-control-2mkk>

好了伙计们。欢迎回来！

我们来谈谈版本控制。

# 什么是版本控制？

你是否曾经在一个网站/应用程序/一段代码上工作并做了更改，却发现你需要回到一个更早的版本？或者你可能有过与另一个开发人员一起工作的噩梦般的经历，而没有使用版本控制。你必须发送一条消息说“嘿，我正在使用 user/login.php，暂时不要编辑它。”如果你有，你知道为什么版本控制存在。在其最基本的形式中，版本控制软件帮助你跟踪一组文件和目录的变化。你给它传递一些特殊的命令，它就工作了，存储你上次运行这些命令和这次之间的差异是很神奇的。

谈到版本控制，有几个选项，每个软件商店都有点不同。最常见的大概是`git`，但也有`svn`、`csv`、`hg`。我肯定还有其他人。再加上 Github 和 Bitbucket 这样的集中式版本控制软件主机，你就可以开始看到各种可能的设置了。这篇文章将重点关注`git`和 Github，因为这可能是大多数*读者将要使用的。还有其他版本控制系统的教程。*

# 入门`git`

让我们直接开始吧。在`git`中有几个主要的概念我们将会涉及到。

## 仓库(repo)

存储库是`git`系统中的基本单元。它们保存所有的代码、变更、分支、提交和其他一切。当您创建 git repo 时，您是在告诉 git 您所在的文件夹是您想要跟踪其变更的文件夹。大多数回购将使用一个`master`分支，其中包含“规范的”代码——意味着代码的最新工作版本。通常情况下，您不希望将损坏的代码提交给 master。这就是其他分支机构的作用。

您可以在本地创建一个 git repo，方法是在您计算机上的一个目录中运行`git init`。

## 树枝

由`git`跟踪的变更分叉树的一部分。它们用于防止多个开发人员使用添加代码时出现重大冲突。给分支机构命名有很多策略。在我的工作中，我们倾向于使用这样的结构:`username/branch-function`。因此，如果我要向代码库添加一个新的加载屏幕，我可能会将我的分支命名为`wuz/new-loader`。一旦我有了那个分支，我就会开始向它提交新的代码。

要创建一个新的分支，您可以运行`git checkout -b <branch_name>`，其中`<branch_name>`是您想要创建的分支的名称。一旦你制作了一个分支，你可以使用`git checkout <branch_name>`在分支之间切换(这次没有`-b`)。所以我可以用`git checkout master`回到师父身边。

## 提交

提交是`git`世界中的工作单元。他们跟踪代码中两个变化之间的个体差异。您添加的第一个文件将是您的第一次提交——之后的每件事都将是在第一次提交的基础上的一次更改。如果我想添加一个新的加载器，我可能会添加一个名为`Loader.js`的新文件，添加一些代码，并提交给我的分支。然后，我可以决定继续添加新代码(提交更多代码)。一旦我让它工作，看起来正确，我可能会合并到主。

添加新的提交包括添加您想要提交的文件，然后告诉 git 提交这些文件。例如，我可以运行`git add Loader.js`来只添加`Loader.js`文件变更，或者运行`git add -A`来添加一个分支上的所有新变更。从那里，我会调用`git commit`。这将弹出一个添加提交消息的窗口。这是一个很好的地方来记录这些变化做了什么，然后你可以通过运行`git log`在日志中查看。这是一个查看发生了什么变化的好方法，尤其是当您与多个开发人员一起工作时。

## 合并

现在我们有一些变化，我们可以合并回主！一旦我将我的更改提交到我的`wuz/new-loader`分支，我就可以将那个分支合并到主分支中。Merges 告诉 git，您希望将一个分支中的所有更改合并到另一个分支中。

合并一个分支非常简单——只需切换到您想要合并的分支并运行`git merge <other_branch>`，其中`<other_branch>`是您的更改所在的分支。在这种情况下，我可能想把`wuz/new-loader`合并成`master`。这意味着我会切换到主- `git checkout master` -然后合并- `git merge wuz/new-loader`。这将获取我的所有更改，并将它们作为新的提交添加到 master 上。这个新的提交被称为合并提交。

如果我们不想在一次提交中把所有东西都合并到 master 中，那该怎么办呢？也许这样我们可以让我们的日志看起来更好，或者这样我们可以挑选出提交以便以后重做。这就是重定基数的由来。

## 重置基础

重定基线就像把一些胶片拼接成另一段胶片(如果你不知道什么是胶片，我也不知道怎么帮你😛).当您将一个分支重置到另一个分支上时，您将在另一个分支上倒带重放您的更改。重置基础稍微复杂一点，所以这是调用一个伟大的 git 资源- [git-scm](https://git-scm.com) 的好时机。你可以在这里阅读更多关于重置基础如何工作的[。如果您需要在 git 中做其他事情，这是一个查找它们的好地方！](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)

## 托管存储库

现在，我们不想在本地机器上存储所有这些代码和更改。首先，这将使许多开发人员很难从事这项工作。他们必须将他们的更改发送到您的机器上。相反，我们可以在网站上托管我们的存储库。既然这样，我们就来说说 Github。有很多其他选择，但 Github 可能是最受欢迎的。

## 出招

使用托管存储库，有一些很酷的方法来处理分支和合并。不用在本地机器上合并 master，您可以使用他们的工具来完成！假设我刚刚完成了`wuz/new-loader`分支。我现在可以将它推送到托管回购，也称为远程。为此，我可以运行`git push wuz/new-loader`。这可能会失败，因为我们还没有告诉遥控器它应该有这个分支。相反，我们需要说`git push -u origin wuz/new-loader`。这将创建一个新的远程分支(本例中的特定远程被命名为 origin，这只是 git 标准)并将我们的代码推送到该分支。您也可以推送主服务器更改，但是有一种更好的方法可以将您的更改导入远程主服务器。拉请求。

## 拉取请求

拉请求是将代码添加到托管存储库中特定分支的请求。一旦我将`wuz/new-loader`分支推到远程，我就可以上线，在本例中是到 Github，并创建一个新的 pull 请求。一旦我这样做了，我得到了我对我请求合并到的分支所做的更改的比较。如果所有的更改看起来都不错，我就可以批准拉取请求并合并分支。这将导致 Github 将更改合并到 master 中。现在你可以回到你的机器，切换到主分支，做一个`git pull`，它将从远程获取任何更改。

# 走得快

就像 shell 命令一样，git 提供了一种添加别名的方法，这使得与它的交互变得更好！您可以将类似于`git checkout <branch>`的内容缩短为`git co <branch>`，或者在您的分支中添加健壮、美观的日志记录。你把这些添加到你的`~/.gitconfig`文件中。如果您遵循了前两篇文章，并且设置了 VS 代码和一些别名，那么您可以运行`c ~/.gitconfig`来编辑这个文件！以下是我最喜欢的几个别名:

```
[alias]
  A = add -A # Easily add all file changes | git A
  cam = commit -am # add all files and commit with the message | git cam 'Adding all files'
  ca = commit -a # Add all files and open the commit message window | git ca
  cm = commit -m # Commit all added files with the message | git cm 'Adding files'
  ci = commit # Shorter `git commit`
  co = checkout # checkout a branch
  st = status # get the status of current file changes
  br = branch -v # list all branches
  g = log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative # pretty oneline log
  sl = stash list --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative # pretty oneline stash list
  h = !git --no-pager log origin/master..HEAD --abbrev-commit --pretty=oneline #pretty oneline graph of what is different from origin/master
  who = shortlog -n -s --no-merges # who has committed to this project
  new = checkout -b 
```

Enter fullscreen mode Exit fullscreen mode

感谢阅读！在评论里让我知道你最喜欢的`git`别名吧！想要更多这样的吗？我[发推特](https://twitter.com/CallMeWuz)我所有的帖子！
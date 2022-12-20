# 你可能不知道的 15 个 Git 命令

> 原文：<https://dev.to/zaiste/15-git-commands-you-may-not-know-4a8j>

使用 [Git](https://git-scm.com/) 有时可能会令人生畏。有太多的命令和细节需要学习。然而，[文档](https://git-scm.com/docs)虽然庞大，但仍然非常容易访问。一旦你克服了最初不知所措的感觉，事情就会变得井井有条。

这里列出了 15 个您可能还不知道的 Git 命令，但是希望它们能帮助您掌握这个工具。

## 1。修改最近的提交

```
git commit --amend 
```

Enter fullscreen mode Exit fullscreen mode

`—-amend`允许将暂存的更改(如添加一个被遗忘的文件)添加到之前的提交中。在其上添加`—-no-edit`将修改最后一次提交，而不改变其提交消息。如果没有变化，`-—amend`将允许你修改最后一条提交消息。

更多内容:`git help commit`

## 2。交互式添加文件的选定部分

```
git add -p 
```

Enter fullscreen mode Exit fullscreen mode

`-p`(或`—patch`)允许交互选择每个跟踪文件的部分进行提交。这样，每次提交只包含相关的更改。

更多内容:`git help add`

## 3。交互式隐藏文件的选定部分

```
git stash -p 
```

Enter fullscreen mode Exit fullscreen mode

与`git-add`类似，您可以使用`--patch`选项交互式地选择每个被跟踪文件的部分来隐藏。

更多内容:`git help stash`

## 4。藏起来不被跟踪

```
git stash -u 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，在存储时，不包括未跟踪的文件。为了改变行为并包含那些文件，你需要使用`-u`参数。还有`-a` ( `—all`)将未跟踪和忽略的文件隐藏在一起，这可能是你通常不需要的。

## 5。交互式还原文件的选定部分

```
git checkout -p 
```

Enter fullscreen mode Exit fullscreen mode

`--patch`也可用于选择性地丢弃每个被跟踪文件的部分内容。我将这个命令别名为`git discard`

更多内容:`git help checkout`

## 6。切换到上一个分支

```
git checkout - 
```

Enter fullscreen mode Exit fullscreen mode

此命令允许您快速切换到先前检出的分支。一般来说，`-`是前一个分支的别名。它也可以与其他命令一起使用。我给`co`起了个别名`checkout`，所以，它就变成了`git co -`

## 7。还原所有本地更改

```
git checkout . 
```

Enter fullscreen mode Exit fullscreen mode

如果您确定您所有的本地更改都可以被丢弃，您可以使用`.`来立即进行。然而，始终使用`checkout --patch`是一个好习惯。

## 8。显示更改

```
git diff --staged 
```

Enter fullscreen mode Exit fullscreen mode

该命令显示所有暂存的变更(添加到索引中的变更)，而`git diff`只显示工作目录中的变更(没有索引中的变更)。

更多内容:`git help diff`

## 9。在本地重命名分支

```
git branch -m old-name new-name 
```

Enter fullscreen mode Exit fullscreen mode

如果您想要重命名当前检出的分支，您可以将该命令缩短为以下形式:

```
git branch -m new-name 
```

Enter fullscreen mode Exit fullscreen mode

更多内容:`git help branch`

## 10。远程重命名分支

为了远程重命名分支，在本地重命名分支后，您需要首先远程删除该分支，然后再次推送重命名的分支。

```
git push origin :old-name
git push origin new-name 
```

Enter fullscreen mode Exit fullscreen mode

## 11。一次打开所有有冲突的文件

重置基可能会导致冲突，下面的命令将打开所有需要您帮助解决这些冲突的文件。

```
git diff --name-only --diff-filter=U | uniq  | xargs $EDITOR 
```

Enter fullscreen mode Exit fullscreen mode

## 12。什么变了？

```
git whatchanged —-since=‘2 weeks ago’ 
```

Enter fullscreen mode Exit fullscreen mode

该命令将显示一个日志，其中包含最近两周内每次提交所引入的更改。

## 13。从上次提交中删除文件

假设您错误地提交了一个文件。您可以通过组合使用`rm`和`commit --amend`命令:
来快速删除上次提交文件

```
git rm —-cached <file-to-remove>
git commit —-amend 
```

Enter fullscreen mode Exit fullscreen mode

## 14。查找分支

```
git branch --contains <commit> 
```

Enter fullscreen mode Exit fullscreen mode

该命令将显示包含特定提交的所有分支。

## 15。本地优化存储库

```
git gc --prune=now --aggressive 
```

Enter fullscreen mode Exit fullscreen mode

更多内容:`git help gc`

## 奖金

尽管我非常喜欢 CLI，但我还是强烈建议您查看一下 [Magit](https://magit.vc/) 来进一步提高您的 git 水平。这是我用过的最好的软件之一。

通过`help`命令还可以看到*推荐的 Git 工作流*的精彩概述。一定要通读！

```
git help workflows 
```

Enter fullscreen mode Exit fullscreen mode
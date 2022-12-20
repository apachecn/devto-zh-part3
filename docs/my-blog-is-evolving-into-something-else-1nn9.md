# 我的博客正在发展...变成别的东西

> 原文：<https://dev.to/proticm/my-blog-is-evolving-into-something-else-1nn9>

## 简介

不久前，我以 dev.to 用户的身份开始了我的旅程。这种体验过去是，现在仍然是一种很棒的体验。各种各样的讨论是巨大的，因此我很好奇，想成为其中的一部分。

作为一名开发人员，我总是努力学习实用知识，我的主要精力放在不断改进我编写的代码上。我开始以更实用的方式撰写文章，重点关注代码示例，在结尾提供完全可用的代码，这对开发人员非常有用。

[![coding](img/566aa37268dd35280b7729caecf83de5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CUGTa-AQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1482062364825-616fd23b8fc1%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D500%26q%3D60)

## 变化多端

这使我产生了写更多面向代码的文章的想法，我在我的博客上添加了一个备忘单部分。你可以稍后在这里查看。在这一点上，它显示了站点管理员创建的备忘单，但计划是为所有公开(因为用户可以创建一个私有的)可访问的备忘单添加一个搜索页面。

如果你想一想，我们总是在谷歌搜索代码样本，当我们一遍又一遍地搜索相同的东西时，因为我们的大脑记不住它，这种情况并不罕见。

[![repeat](img/0c975fb36430777ebf692e277778ad72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sObPFAuN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encrypted-tbn0.gstatic.com/images%3Fq%3Dtbn:ANd9GcRQRV8cp1oHxtP5a0KIHYrJ6peccmCQEVtNObJPQHSyLUx4si5krA)

我开始想，如果我们能把它们都放在一个地方呢？如果我们能以自己的编码风格在某个地方编写一段代码，并保存下来供将来参考，那会怎么样？不仅如此，我们还可以存储在网上搜索和搜索数小时后找到的链接，指向有用文章的链接等等...

我不想要一个存储库，我不想要一篇文章，或任何太复杂的东西，我只是想放置一个小的助手功能，我知道它以后会再次使用。

整个想法是减少我们每天重复做的事情，并拥有一个个人知识数据库，可以与他人共享或保持私密。

[![cheatsheet](img/19bf64db1755404cca1cdea631cb2af4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q97TUIKG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.idgesg.nimg/article/2019/02/crib-sheet_cheat-sheet_answers_tests_notes-100787270-large.3x2.jpg)

未来的计划是创建团队表，即团队管理的备忘单，并在团队成员之间共享(如果备忘单被证明是一个有用的特性的话)。

同时，考虑到其中一篇文章被阅读了超过 25000 次并发表在 JavaScript 每周时事通讯中，我已经写了几篇对读者有很大价值的文章。

简讯[链接](https://javascriptweekly.com/issues/438)。最后一个， *JavaScript 干净代码:一些最佳实践*
文章[链接](https://devinduct.com/blogpost/22/javascript-clean-code-best-practices)

这导致用户登录并使用网站，这让我感觉很棒，因为在我看来，没有什么比读者发现你的作品有价值更好的了。

考虑到我还没有实现像评论区这样的功能，我有点措手不及，但它即将到来。最终，我只是一个想要帮助他人并成为生态系统一员的工程师。

为了让本文始终面向代码，这里有一个 Git 命令示例备忘单:

## 初始化

*   `git init`
*   在给定的目录中创建一个空的 Git 存储库。如果不存在，将创建目录。

```
git init <project-name> 
```

*   `git clone`
*   将位于给定 URL 的存储库克隆到本地机器上。

```
git clone <project-url> 
```

*   `git config`
*   获取或设置当前存储库的选项，或根据命令进行全局设置。

```
# set up the credentials on a global level, meaning that every commit from the current machine will use the given user
git config --global user.name "John"
git config --global user.email "john@john.com"

# omit the --global option to set the configuration for the current repository only
git config user.name "John"
git config user.email "john@john.com"

# get local user name
git config user.name

# get global user name
git config --global user.name 
```

## 文件

*   `git add`
*   使用工作树中找到的当前内容更新索引，为下一次提交准备暂存的内容。根据所提供的选项，将发生不同的操作。

```
# adds the specified file to the index, where <file> is the file you want to add
git add <file>

# stages the specified files under the given directory, including files from the subdirectories
git add <directory>/\*.<extension>
git add pages/\*.html

# adds all new files to the index, ignoring modifications and deletions
git add .

# stages all changes in the working tree
git add -A
# or
git add --all

# stages modifications and deletions without adding new files found in the working tree
git add -u
# or
git add --update 
```

*   `git commit`
*   提交阶段性更改。

```
# commits the changes with the given message
git commit -m "Super cool commit"

# replaces the last commit with the combination of the staged changes the changes from the last commit.
# if nothing is staged, this command can be used to update the last commit message.
git commit --amend -m "New super cool commit message"

# same as above except it doesn't change the commit message
git commit --amend --no-edit 
```

*   `git clean`
*   根据给定的选项，显示哪些文件将从工作目录中删除或删除。

```
# show what would be removed
git clean -n

# do the cleaning forcefully
git clean -f 
```

## 树枝

*   `git checkout`
*   将当前分支更改为提供的分支。

```
# switches to the given branch
git checkout <branch-name>

# creates a new branch and then switches to it
git checkout -b <branch-name>

# creates a new branch if it doesn't exist, otherwise resets it and then switches to it
git checkout -B <branch-name>

# copies the old branch and creates a new one with the same state
git checkout -b <branch-name> <old-branch-name> 
```

*   `git push`
*   它将本地更改应用到远程分支。

```
git push origin <branch-name> 
```

*   `git pull`
*   它将远程更改应用到本地分支。

```
git pull 
```

*   `git fetch`
*   检索由其他团队成员提交的变更和分支。

```
git fetch origin 
```

*   `git merge`
*   将两个或多个分支合并在一起。

```
# merges the <branch-name> into the branch you are currently working on
git merge <branch-name>

# to merge into a specific branch use checkout first
git checkout <branch-to-merge-to>
git merge <branch-name> 
```

*   `git branch`
*   用于列出、创建或删除分支

```
# list all branches
# current branch will be highlighted with an asterisk
git branch --list

# list all merged branches
git branch -a --merged

# delete a branch
git branch -d <branch-name>

# delete a branch forcefully
git branch -d --force <branch-name>
# a shorthand
git branch -D <branch-name> 
```

## 修改版本

*   `git show`
*   显示了关于 git 存储库的信息。它可以用来显示关于提交、标签、树和 blobs 的信息。

```
git show HEAD 
```

*   `git status`
*   显示工作树的当前状态。例如，如果您想要查看当前暂存的文件，请使用此命令。

```
# shows the current status
git status

# shows the current status in the short format
git status -s 
```

*   `git log`
*   显示变更的历史。结果包括提交 id、日期和作者等数据。

```
# show the history
git log

# show the history by author
git log --author="Name"

# show the history by the commit message
git log --grep="Some Message" 
```

*   `git diff`
*   显示工作树和给定分支之间的差异。

```
git diff <branch-name> 
```

## 复位和恢复

*   `git reset`
*   将当前分支重置为指定状态。

```
# this will undo/rollback the last commit.
# the commit is lost after this
git reset --hard HEAD~1

# to keep the changes use the --soft option
git reset --soft HEAD~1 
```

*   `git revert`
*   恢复现有提交。

```
# this will open the system editor and prompts you to edit the commit message prior to committing the revert
# this is the default option
git revert

# in this case, the revert command will not open the editor
git revert --no-edit 
```

正则表达式备忘单的例子可以在[这里](https://devinduct.com/cheatsheet/10/regex)找到。
点击查看完整名单[。](https://devinduct.com/cheatsheets)

我鼓励你去创建你的第一个备忘单。当然可以用 markdown 写。请在下面的评论中告诉我你的想法。

感谢您的阅读，下一篇文章再见。
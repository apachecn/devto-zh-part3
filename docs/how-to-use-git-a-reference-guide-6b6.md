# 如何使用 Git:参考指南

> 原文：<https://dev.to/digitalocean/how-to-use-git-a-reference-guide-6b6>

## Git 备忘单

### 简介

开发人员和开源软件维护人员团队通常通过 Git 管理他们的项目，Git 是一个支持协作的分布式版本控制系统。

这个备忘单风格的指南提供了对在 Git 存储库中工作和协作有用的命令的快速参考。要安装和配置 Git，请务必阅读“[如何为开源做贡献:Git 入门](https://www.digitalocean.com/community/tutorials/how-to-contribute-to-open-source-getting-started-with-git)”

**如何使用本指南:**

*   本指南采用备忘单格式，带有独立的命令行片段。
*   跳到任何与你要完成的任务相关的部分。
*   当您在本指南的命令中看到`highlighted text`时，请记住该文本应该指的是*您自己的*库中的提交和文件。

## 设置和初始化

用下面的命令检查您的 Git 版本，这也将确认 Git 已经安装。

```
git --version 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`init`将当前工作目录初始化为 Git 存储库。

```
git init 
```

Enter fullscreen mode Exit fullscreen mode

要复制远程托管的现有 Git 存储库，您将使用带有 repo 的 URL 或服务器位置的`git clone`(在后一种情况下，您将使用`ssh`)。

```
git clone https://www.github.com/username/repo-name 
```

Enter fullscreen mode Exit fullscreen mode

显示当前 Git 目录的远程存储库。

```
git remote 
```

Enter fullscreen mode Exit fullscreen mode

要获得更详细的输出，请使用`-v`标志。

```
git remote -v 
```

Enter fullscreen mode Exit fullscreen mode

添加上游 Git，它可以是一个 URL，也可以托管在一个服务器上(在后一种情况下，用`ssh`连接)。

```
git remote add upstream https://www.github.com/username/repo-name 
```

Enter fullscreen mode Exit fullscreen mode

## 暂存

当您修改了一个文件并将其标记为在下次提交时使用时，它被认为是一个暂存文件。

检查 Git 存储库的状态，包括添加的未暂存的文件和暂存的文件。

```
git status 
```

Enter fullscreen mode Exit fullscreen mode

要存放修改过的文件，可以使用`add`命令，在提交之前可以多次运行该命令。如果您想在下一次提交中包含后续更改，您必须再次运行`add`。

可以用`add`指定具体的文件。

```
git add my_script.py 
```

Enter fullscreen mode Exit fullscreen mode

使用`.`可以添加当前目录中的所有文件，包括以`.`开头的文件。

```
git add . 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`reset`从暂存中删除文件，同时保留工作目录中的更改。

```
git reset my_script.py 
```

Enter fullscreen mode Exit fullscreen mode

## 提交

一旦您暂存了您的更新，您就可以提交它们，这将记录您对存储库所做的更改。

要提交暂存文件，您将使用有意义的提交消息运行`commit`命令，以便跟踪提交。

```
git commit -m "Commit message" 
```

Enter fullscreen mode Exit fullscreen mode

您可以将所有跟踪文件的暂存压缩为一步提交。

```
git commit -am "Commit message" 
```

Enter fullscreen mode Exit fullscreen mode

如果需要修改提交消息，可以使用`--amend`标志。

```
git commit --amend -m "New commit message" 
```

Enter fullscreen mode Exit fullscreen mode

## 树枝

Git 中的一个分支是指向存储库中一个提交的可移动指针，它允许您隔离工作并管理特性开发和集成。你可以通过阅读 [Git 文档](https://git-scm.com/book/en/v1/Git-Branching-What-a-Branch-Is)来了解更多关于分支的知识。

用`branch`命令列出所有当前分支。星号(`*`)将出现在您当前活动的分支旁边。

```
git branch 
```

Enter fullscreen mode Exit fullscreen mode

创建一个新分支。您将保持在当前活动的分支上，直到切换到新的分支。

```
git branch new-branch 
```

Enter fullscreen mode Exit fullscreen mode

切换到任何现有的分支，并将其签出到您当前的工作目录中。

```
git checkout another-branch 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过使用`-b`标志来合并一个新分支的创建和检出。

```
git checkout -b new-branch 
```

Enter fullscreen mode Exit fullscreen mode

重命名您的分支机构名称。

```
git branch -m current-branch-name new-branch-name 
```

Enter fullscreen mode Exit fullscreen mode

将指定分支的历史记录合并到您当前工作的分支中。

```
git merge branch-name 
```

Enter fullscreen mode Exit fullscreen mode

中止合并，以防有冲突。

```
git merge --abort 
```

Enter fullscreen mode Exit fullscreen mode

您还可以选择一个特定的提交，用引用该特定提交的字符串与`cherry-pick`合并。

```
git cherry-pick f7649d0 
```

Enter fullscreen mode Exit fullscreen mode

当您合并了一个分支并且不再需要该分支时，您可以将其删除。

```
git branch -d branch-name 
```

Enter fullscreen mode Exit fullscreen mode

如果您没有将分支合并到主服务器，但是您确定要删除它，您可以**强制**删除分支。

```
git branch -D branch-name 
```

Enter fullscreen mode Exit fullscreen mode

## 协作和更新

要从另一个存储库中下载变更，比如远程上游，您将使用`fetch`。

```
git fetch upstream 
```

Enter fullscreen mode Exit fullscreen mode

合并提取的提交。

```
git merge upstream/master 
```

Enter fullscreen mode Exit fullscreen mode

将本地分支提交推送到或传输到远程存储库分支。

```
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

从跟踪远程分支获取并合并任何提交。

```
git pull 
```

Enter fullscreen mode Exit fullscreen mode

## 检查

显示当前活动分支的提交历史记录。

```
git log 
```

Enter fullscreen mode Exit fullscreen mode

显示更改特定文件的提交。无论文件是否重命名，它都会跟随文件。

```
git log --follow my_script.py 
```

Enter fullscreen mode Exit fullscreen mode

显示在一个分支上而不在另一个分支上的提交。这将显示不在`b-branch`上的`a-branch`上的提交。

```
git log a-branch..b-branch 
```

Enter fullscreen mode Exit fullscreen mode

查看引用日志(`reflog`)以了解分支和其他引用的提示在存储库中的最后更新时间。

```
git reflog 
```

Enter fullscreen mode Exit fullscreen mode

通过 Git 中的提交字符串或散列以更易于阅读的格式显示任何对象。

```
git show de754f5 
```

Enter fullscreen mode Exit fullscreen mode

## 显示变化

`git diff`命令显示了提交、分支等之间的变化。你可以通过 [Git 文档](https://git-scm.com/docs/git-diff)更全面地了解它。

比较临时区域中已修改的文件。

```
git diff --staged 
```

Enter fullscreen mode Exit fullscreen mode

显示`a-branch`中有而`b-branch`中没有的差异。

```
git diff a-branch..b-branch 
```

Enter fullscreen mode Exit fullscreen mode

显示两个特定提交之间的差异。

```
git diff 61ce3e6..e221d9c 
```

Enter fullscreen mode Exit fullscreen mode

## 贮藏

有时你会发现你对一些代码进行了修改，但是在你完成之前，你必须开始做其他的事情。您还没有准备好提交到目前为止所做的更改，但是您不想丢失您的工作。`git stash`命令将允许您保存本地修改，并返回到与最近的`HEAD`提交一致的工作目录。

收起你目前的工作。

```
git stash 
```

Enter fullscreen mode Exit fullscreen mode

查看您当前隐藏的内容。

```
git stash list 
```

Enter fullscreen mode Exit fullscreen mode

您的存储将被命名为`stash@{0}`、`stash@{1}`，以此类推。

显示关于特定贮藏的信息。

```
git stash show stash@{0} 
```

Enter fullscreen mode Exit fullscreen mode

要将当前存储中的文件从存储中取出，同时仍保留存储，请使用`apply`。

```
git stash apply stash@{0} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想从一个存储库中取出文件，并且不再需要这个存储库，使用`pop`。

```
git stash pop stash@{0} 
```

Enter fullscreen mode Exit fullscreen mode

如果您不再需要保存在某个特定存储库中的文件，您可以`drop`该存储库。

```
git stash drop stash@{0} 
```

Enter fullscreen mode Exit fullscreen mode

如果您保存了多个 stashes，并且不再需要使用它们中的任何一个，您可以使用`clear`删除它们。

```
git stash clear 
```

Enter fullscreen mode Exit fullscreen mode

## 忽略文件

如果您想将文件保存在您的本地 Git 目录中，但是不想将它们提交到项目中，那么您可以将这些文件添加到您的`.gitignore`文件中，这样它们就不会引起冲突。

使用 nano 等文本编辑器将文件添加到`.gitignore`文件中。

```
nano .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

要看`.gitignore`文件的例子，可以看看 GitHub 的 [`.gitignore`模板回购](https://github.com/github/gitignore)。

## 重置基础

rebase 允许我们通过改变分支所基于的提交来移动分支。使用重定基础，您可以挤压或改写提交。

您可以通过调用您已经提交的想要重新基数化的提交数量来开始重新基数化(在下面的例子中为`5`)。

```
git rebase -i HEAD~5 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以基于特定的提交字符串或散列来调整基础。

```
git rebase -i 074a4e5 
```

Enter fullscreen mode Exit fullscreen mode

一旦你压缩或改写了提交，你就可以在项目上游代码的最新版本上完成你的分支的基础。

```
git rebase upstream/master 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于重设基础和更新的信息，您可以阅读[如何重设基础和更新拉请求](https://www.digitalocean.com/community/tutorials/how-to-rebase-and-update-a-pull-request)，这也适用于任何类型的提交。

## 复位

有时候，包括在一个基础变更之后，你需要重置你的工作树。您可以使用下面的命令重置到一个特定的提交，并**删除所有的更改**。

```
git reset --hard 1fc6665 
```

Enter fullscreen mode Exit fullscreen mode

要将您最后一次已知的非冲突提交强制推送到原始存储库，您需要使用`--force`。

**警告**:除非有非常重要的原因，否则强迫主人是不被允许的。在处理自己的存储库时要谨慎使用，在协作时尽量避免使用。

```
git push --force origin master 
```

Enter fullscreen mode Exit fullscreen mode

要从 Git 目录中删除本地未跟踪的文件和子目录，以创建一个干净的工作分支，可以使用`git clean`。

```
git clean -f -d 
```

Enter fullscreen mode Exit fullscreen mode

如果您需要修改本地存储库，使其看起来像当前的上游主存储库(即有太多冲突)，您可以执行硬重置。

**注意**:执行这个命令将使您的本地存储库看起来与上游完全一样。你已经提交但没有被拉到上游**的任何提交都将被销毁**。

```
git reset --hard upstream/master 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

本指南涵盖了在管理存储库和协作软件时可能会用到的一些更常见的 Git 命令。

您可以在我们的[开源教程系列介绍](https://www.digitalocean.com/community/tutorial_series/an-introduction-to-open-source)中了解更多关于开源软件和协作的信息:

*   [如何为开源做出贡献:Git 入门](https://www.digitalocean.com/community/tutorials/how-to-contribute-to-open-source-getting-started-with-git)
*   [如何在 GitHub 上创建拉取请求](https://www.digitalocean.com/community/tutorials/how-to-create-a-pull-request-on-github)
*   [如何重置和更新拉式请求](https://www.digitalocean.com/community/tutorials/how-to-rebase-and-update-a-pull-request)
*   [如何维护开源软件项目](https://www.digitalocean.com/community/tutorials/how-to-maintain-open-source-software-projects)

作为 Git 工作的一部分，您可能会发现更多有用的命令和变体。要了解更多关于所有可用选项的信息，您可以运行:

```
git --help 
```

Enter fullscreen mode Exit fullscreen mode

接收有用的信息。你也可以从[官方 Git 网站](https://git-scm.com/)上阅读更多关于 Git 的内容和查看 Git 的文档。

* * *

[![CC 4.0 License](img/4662fd03838b335557c183fd57c6b02c.png)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

*本作品根据[知识共享署名-非商业性使用-类似共享 4.0 国际许可证](http://creativecommons.org/licenses/by-nc-sa/4.0/)* 进行许可
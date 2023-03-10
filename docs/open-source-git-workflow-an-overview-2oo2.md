# 开源 Git 工作流(概述)

> 原文：<https://dev.to/adamreidelbach/open-source-git-workflow-an-overview-2oo2>

本教程假设您对 git 有基本的了解。包括创建分支、添加/提交代码以及创建拉请求。

在担任当前角色之前，我使用 git 的工作流程通常是这样的:

1.  克隆回购。
2.  确保本地主文件是最新的，然后创建主文件的分支并开始编码。
3.  添加、提交和上推分支。
4.  检查任何合并冲突或意外的更改。然后，创建一个拉式请求以供审查。
5.  冲洗并重复，从步骤 2 开始。

在我目前的角色中，我们使用一种在为开源项目做贡献时常用的模式。它使用了前一个工作流示例中没有的两个概念， [forks](https://help.github.com/en/articles/fork-a-repo) 和 [remotes](https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes) 。

### 分叉

当您处理一个您不维护的项目时，您可能没有必要的凭证来将变更直接推送到该回购。这就是分叉的由来。您可以 ***将回购的副本*** 复制到我们的个人账户上，并进行您想要的所有更改，而不会影响原始回购。

### Remotes

在您制作了回购的这个副本(分叉)之后，这个新位置就是您将推动您的更改的位置。原始回购和您的副本在 ***不同的位置*** ，又名远程。

## TL；速度三角形定位法(dead reckoning)

(如果你想跳过更详细的解释)

1.  将回购转入你想要的账户。
2.  在本地克隆分叉的回购。
3.  添加一个指向原始 repo 的遥控器，并将其命名为`upstream`。
4.  通过运行`get fetch upstream`然后运行`git status`来查看，手动同步`upstream`。
    *   如有必要，引入任何更改。
5.  创建一个主分支并开始编码。
6.  添加、提交并向上推分支(到您的分支)。
7.  检查您的分支和原始回购主分支之间的任何合并冲突或意外更改。如果看起来不错，就创建一个拉取请求。
8.  冲洗并重复，从第 4 步开始。

## 分解一下

*   将你要处理的回购款转入你想要的账户。
*   [通过运行`git clone https://github.com/your-username/name-of-repo.git`在本地克隆](https://help.github.com/en/articles/cloning-a-repository)你的分叉回购
*   使用`cd name-of-repo`导航至您的新回购
*   在您的终端中运行`git status && git remote -v`，您将看到以下内容:

```
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
origin  https://github.com/your-username/name-of-repo.git (fetch)
origin  https://github.com/your-username/name-of-repo.git (push) 
```

*   注意名称`origin`是自动生成的，指向回购的
    分支。当您对代码库进行新的更改时，`origin`是您将它们推入的地方。最终，原始的回购协议会发生变化，所以您需要一种方法来引入这些更新。为此，您需要添加一个指向它的遥控器。传统上，我们称之为`upstream`。

*   运行`git remote add upstream https://github.com/not-your-username/name-of-repo.git`。然后`git status && git remote -v`再次查看新添加的遥控器及其位置。

```
origin  https://github.com/your-username/name-of-repo.git (fetch)
origin  https://github.com/your-username/name-of-repo.git (push)
upstream  https://github.com/not-your-username/name-of-repo.git (fetch)
upstream  https://github.com/not-your-username/name-of-repo.git (push) 
```

*   接下来，我们需要手动与`upstream`同步。为此，运行`git fetch upstream`。之后，如果你运行`git status`，它仍然会说:

```
On branch master
Your branch is up to date with 'origin/master'. 
```

*   让我们通过确保本地主机指向`upstream`的主机来解决这个问题。运行`git branch --set-upstream-to=upstream/master master`，然后再次运行`git status`。因为这是最近克隆的，所以不太可能有什么变化，所以您应该会看到类似这样的内容:

```
On branch master
Your branch is up to date with 'upstream/master'. 
```

> *   Since this is the setting, if your branch is outdated, you can simply run `git pull` to get any changes.
> *   In addition, I also used a tool called [Spaceship Tip](https://github.com/denysdovhan/spaceship-prompt) , because it gave me some useful visual representations about the status of my main branch, and many other functions.

*   现在我们知道我们的本地 master 是最新的，创建一个 master 的分支并开始编码。
*   在您成功完成您的更改并运行任何必要的测试之后，您可以添加并提交您的工作。
*   一旦你准备好推进你的改变，记得把它们推进你的分叉回购。该命令看起来类似于。
*   一旦你看到你的分支出现在 GitHub 中，一定要将你的分支与原始回购的主分支进行比较。在这里，您可以检查任何需要解决的合并冲突，或者任何您可能不打算进行的更改。如果一切正常，就提出你的拉动请求。

我希望这对你有帮助。如果您对如何改进本教程有任何问题或建议，请随时联系我们，或者在下面留下您的评论。你也可以在推特上找到我。

我要感谢[肯特·C·多兹关于](https://twitter.com/kentcdodds/) [egghead.io](https://egghead.io/) 如何为 GitHub 上的开源项目做贡献的教程，以及[斯科特·沙孔](https://twitter.com/chacon)和[本·施特劳布](https://twitter.com/benstraub)写的 [Pro Git](https://git-scm.com/book/en/v2) 书。GitHub 中的[为项目做贡献](https://git-scm.com/book/en/v2/GitHub-Contributing-to-a-Project)一章尤其有用。当把这些放在一起时，它们都是很好的资源，如果你想了解更多的信息，我强烈建议你去看看。
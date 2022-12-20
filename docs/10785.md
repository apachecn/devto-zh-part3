# 修复 git“你当前分支的尖端落后于它的远程对应物”- 4 现实世界的解决方案

> 原文：<https://dev.to/hugo__df/fix-git-tip-of-your-current-branch-is-behind-its-remote-counterpart---4-real-world-solutions-a4o>

当与一系列 GitLab、GitHub、BitBucket 和 rebase-trigger-happy 的同事/合作者一起工作时，看到如下消息是一种仪式

```
Pushing to git@github.com:some-project/some-repo.git
To git@github.com:some-project/some-repo.git
  ! [rejected] master -> master (non-fast-forward)
error: failed to push some refs to 'git@github.com:some-project/some-repo.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Merge the remote changes (e.g. 'git pull')
hint: before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details. 
```

## 是什么原因导致“你当前分支的尖端落后”？

Git 使用本地和远程分支的概念。本地分支是存在于 git 存储库的本地版本中的分支。远程分支是存在于远程位置的分支(大多数存储库通常有一个名为`origin`的远程)。远程大致相当于托管 git 存储库的地方(例如 GitHub/Git lab/bit bucket/自托管 Git 服务器存储库实例)。

遥控器对于共享您的工作或在分支机构进行协作非常有用。

“您的当前分支的尖端在它的远程对应部分的后面”意味着在远程分支上已经有了您在本地没有的变化。

远程分支往往有两种类型的变更:有人添加了提交或者有人修改了分支的历史(通常是某种形式的重定基础)。

这两种情况应该区别对待。

## 如何让你当地的分支机构回到可推动的状态？

我们现在将探索如何在本地分支中实现一种远程不会拒绝推送的状态。

### 1。无 rebase:将远程分支合并到本地

在消息中我们可以看到:

> 更新被拒绝，因为您当前分支的尖端落后于远程分支。再次推送前，合并远程更改(如“git pull”)。

那么是不是做起来这么简单:

```
git pull 
```

以及解决任何出现的冲突。

如果有人在遥控器上打了折扣，我们就不应该这么做。历史是不同的，合并可能会对历史产生恶劣的影响。将会有一个奇怪的历史，在两个地方有相同的提交，外加一个合并提交。

请继续阅读“远程设备已被重置”案例的解决方案。

### 2。远程 rebase +无本地提交:强制 git 在拉取时覆盖文件

如果遥控器上没有任何更改，您只需:

> **警告**:这是一个破坏性的动作，它会用远程
> 的更改覆盖你本地分支的所有更改

```
git reset --hard origin/branch-name 
```

当然，这种情况很少发生，但它提供了一条通向以下两种解决方案的途径。

解决方案 3。第四。将本地更改保存到其他地方(git 存储或另一个分支)。他们使用上面的命令从原点重置本地分支。最后，他们重新应用任何本地更改并发送它们。

### 3。远程重置+本地提交:软 git 重置、存储、“硬拉”、弹出存储、提交

假设您已经进行了局部更改(可能只是一些提交)。

使用 2 中知识的简单方法。就是做一个“软复位”。

#### 选项为“软复位”

选项 1，假设您添加的第一个提交使用了:

> 注意`^`，它意味着`<first-commit-sha>`之前的提交

```
git reset <first-commit-sha>^ . 
```

选项 2，如果您知道已经添加的提交数量，您也可以使用下面的代码，将`3`替换为您想要“撤销”的提交数量:

```
git reset HEAD~3 . 
```

您现在应该能够运行`git status`并看到未登台(即“已修改”)文件更改来自我们刚刚“撤消”的本地提交。

#### 将您的更改保存到存储库

运行`git stash`将它们保存到 stash 中(更多信息[参见 stash 的 git 文档](https://git-scm.com/docs/git-stash))。

如果您运行`git status`，您将看到未暂存的(“已修改的”)文件不再存在。

#### 按照上一节所述运行硬拉

如 2 所示运行`git reset --hard origin/branch-name`。

#### 取消保存并重新提交您的更改

要恢复隐藏的更改:

```
git stash pop 
```

现在，您可以使用`git add`(最好使用`-p`选项，例如`git add -p .`)后跟`git commit`来将您的本地更改添加到一个分支，远程不会拒绝推送。

一旦你添加了你的修改，`git push`应该不会被拒绝。

### 4。远程 rebase +本地提交 2:签出到新的临时分支，“硬拉”原始分支，从临时分支中挑选

使用 stash 的替代方法是从本地分支中分支，并重新应用分支的“硬拉”版本的提交。

#### 创建新的临时分支

首先，我们将创建一个新的临时本地分支机构。假设我们开始于分支`branch-name`分支(如果不是，运行`git checkout branch-name`)我们可以做:

```
git checkout -b temp-branch-name 
```

这将创建一个新的分支`temp-branch-name`,它是我们更改的副本，但是在一个新的分支中

#### 回到分支，“硬拉”

我们现在回到分支`branch-name`，用远程版本
覆盖我们的本地版本

```
git checkout branch-name 
```

接着是如 2 中所示的`git reset --hard origin/branch-name`。

#### 从临时分支中挑选提交到本地分支

我们现在想要切换回`temp-branch-name`并获得我们想要应用的提交的 sha:

```
git checkout temp-branch-name 
```

接着是

```
git log 
```

查看我们想要应用哪些提交(要退出`git log`，您可以使用`q`)。

#### 逐个挑选每个提交

假设我们想要应用提交`<commit-sha-1>`和`<commit-sha-2>`。

我们将使用:
切换到已经重置为远程版本的分支

```
git checkout branch-name 
```

然后我们将使用 cherry-pick ( [参见 cherry-pick git docs](https://git-scm.com/docs/git-cherry-pick) )来应用这些提交:

```
git cherry-pick <commit-sha1> && git cherry-pick <commit-sha2> 
```

#### 挑选一系列提交

如果您有一堆提交，并且它们是连续的，那么您可以使用下面的代码(对于 git 1.7.2+)

我们将确保位于已经使用
重置为远程版本的分支上

```
git checkout branch-name 
```

> 对于 git 版本 1.7.2+，归功于"[中的](https://feeding.cloud.geek.nz/posts/cherry-picking-range-of-git-commits/) [**弗朗索瓦·马里耶**](https://fmarier.org/) 精心挑选一系列 git 提交-[喂云](https://feeding.cloud.geek.nz/)

```
git cherry-pick <first-commit-sha>^..<last-commit-sha> 
```

现在，您应该能够将本地分支连接到远程分支，而不会被拒绝。

[unsplash-logo
阿洛拉·格里菲斯](https://unsplash.com/@aloragriffiths?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)
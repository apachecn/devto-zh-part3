# Git -签出远程分支

> 原文：<https://dev.to/stephencweiss/git-checking-out-remote-branches-35jo>

本周早些时候，我下载了一个同事的作品进行本地测试。不幸的是，我遵循的步骤最终将新的特性分支合并到我的本地 master 中——这是我不知道的。然后，当我把我的工作提交给评审时，突然，我也带着他的所有工作(因为我把我的本地主人合并进来，以确保我处理冲突)。

所以，如果你想在本地运行一个特性分支，那么如何从远程库*中提取一个分支*而不将其合并到现有的本地分支中呢？

StackOverflow 来救援了！

# Git 命令

## 带有单个远程存储库

截至 Git 版本`^1.6.6`

```
git fetch
git checkout test 
```

## 多个远程存储库/超显式方法

```
git checkout -b test <name of remote>/test
or the shorthand
git checkout -t <name of remote>/test 
```

## 例子:

`git checkout -b feature/123-do-something-awesome origin/feature/123-do-something-awesome` `git checkout -t origin/feature/123-do-something-awesome`

## 手工定义

我经常发现查找*这些标志和选项*在做什么很有帮助——在这个特殊的例子中，我们利用了`-b`和`-t`标志。

在查找这个过程之前，我不知道`-b`采用了可选的第二个参数，`<start point>`，在我们的例子中*是*的`remote/branch-name`。

`-t`选项的工作方式类似，但是我们*仅*指定上游并允许 Git 派生分支名称。

> 如果没有给定-b 选项，新分支的名称将来自远程跟踪分支

```
git checkout -b|-B <new_branch> [<start point>]
           Specifying -b causes a new branch to be created as if git-branch(1)
           were called and then checked out. In this case you can use the
           --track or --no-track options, which will be passed to git branch.
           As a convenience, --track without -b implies branch creation; see
           the description of --track below.

           If -B is given, <new_branch> is created if it doesn't exist;
           otherwise, it is reset. This is the transactional equivalent of

               $ git branch -f <branch> [<start point>]
               $ git checkout <branch>

           that is to say, the branch is not reset/created unless "git
           checkout" is successful.
...
-t, --track
           When creating a new branch, set up "upstream" configuration. See
           "--track" in git-branch(1) for details.

           If no -b option is given, the name of the new branch will be
           derived from the remote-tracking branch, by looking at the local
           part of the refspec configured for the corresponding remote, and
           then stripping the initial part up to the "*". This would tell us
           to use "hack" as the local branch when branching off of
           "origin/hack" (or "remotes/origin/hack", or even
           "refs/remotes/origin/hack"). If the given name has no slash, or the
           above guessing results in an empty name, the guessing is aborted.
           You can explicitly give a name with -b in such a case. 
```

资源: [git 签出——如何签出远程 git 分支？-堆栈溢出](https://stackoverflow.com/a/1783426/9888057)
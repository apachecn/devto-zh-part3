# 我最常用的 Git 命令

> 原文：<https://dev.to/steffenpedersen/my-most-used-git-commands-j07>

这并不是您应该学习哪些命令的完整指南。我只是发现阅读其他开发人员的习惯和工作程序很有启发性。这是我想贡献和讲述的关于我最常用的 Git 命令。

老实说，我不知道如何输入这些命令。我有时会写完整的命令。我有时候会把`git`换成`g`。然后我有时会写下完整的别名。我认为这取决于目标和指令。值得一提的是，我正在使用 ZSH 与 [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) 。

## 主命令

这些是我每天使用的命令。

```
git add --all
git commit -m “Add this commit”
git push 
```

Enter fullscreen mode Exit fullscreen mode

如果您听说过 Git，那么您可能知道这三个经典命令。我几乎一直都在用它们。正是在这里，使用一些别名会很好。`gaa`、`gcmsg “Add this commit”`、`gp`就派上用场了！

```
git checkout develop
git checkout -b my-new-branch 
```

Enter fullscreen mode Exit fullscreen mode

这些将切换到现有分支或新分支。这里没什么新鲜事。我在这里也经常使用完整的别名- `gcd`和`gcb “my-new-branch“`。

```
git pull --rebase 
```

Enter fullscreen mode Exit fullscreen mode

当我从存储库中提取数据时，我总是使用选项`--rebase`。这将使我的提交在树顶上保持漂亮和干净。您可以使用别名`gup`。是一位资深开发者教会了我 rebase 的强大。

```
git rebase <branch> 
```

Enter fullscreen mode Exit fullscreen mode

这将导致我们改变自己的基础。记得重设基础！如果你做的是特色分支，这一点尤其重要。我们不想要一个半死不活的分支，它比默认分支落后十亿次提交，合并冲突一直到喉咙。*哎哟！*

```
git merge <branch> 
```

Enter fullscreen mode Exit fullscreen mode

我并不经常需要从我的终端直接合并。在我的团队中，我们使用分支策略和特性分支。当一个分支应该合并到默认分支(或另一个分支)时，我们使用来自 GitHub 的 pull 请求——网站上的 GUI 就可以了。

```
git stash
git stash pop
git stash apply stash@{1} 
```

Enter fullscreen mode Exit fullscreen mode

您的项目经理是否给了您一项新任务，需要您尽快完成？把你目前的工作丢到一边，专注于新的东西。*太牛逼了！*我实际上并没有使用完整的别名。我用的是 *g* 而不是 *git* 。也许是因为我想绝对确定，我真的藏起来了😀

```
git status -s
git log 
```

Enter fullscreen mode Exit fullscreen mode

这些是让我保持更新的命令，我每天使用它们大约 500 次。我用`gss`、`glg`或者有时用`glol`。

## 侧命令

这些是我偶尔使用的命令。

```
git push --force-with-lease 
```

Enter fullscreen mode Exit fullscreen mode

这是我的一个奇怪的宠儿。但是我为什么不用`--force`呢？首先，这是一个极其危险的命令，也是使用共享分支时的一个巨大的*禁忌*。这是因为它会用本地的内容覆盖远程存储库。如果存储库的其他贡献者在此期间进行了推送，这可能是危险的。我大多用的是 rebase 之后的`--force-with-lease`。这是因为它像安全带一样工作。[这篇文章](https://blog.developer.atlassian.com/force-with-lease/)有一个很好的例子。

```
git for-each-ref — sort=-committerdate refs/heads/ 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是一个命令，我几个月前从戴维·沃尔什那里找到的。该命令将从上到下列出最近处理的分支。*太酷了！*

```
git reset --hard 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利💩，您可以随时重置项目。

感谢您的宝贵时间！

如果你喜欢这个，那么请❤️和 [*在 Twitter 上关注我*](https://twitter.com/mrsteffenp) 。
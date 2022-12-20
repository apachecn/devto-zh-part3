# Git 提示:列出你最近工作的分支

> 原文：<https://dev.to/preslavrachev/git-tip-list-the-most-recent-branches-you-are-working-on-52e0>

在我参与的大多数项目中，我们最终都使用了特性分支。因为 git 中的分支很便宜，所以每张票，不管变化有多大或多小，都有自己的特性分支。你最终会有很多这样的问题，经常不得不在两者之间切换。

Git 有一个内置的 checkout 属性，用于切换回您正在处理的前一个分支。这通常很简单:

```
git checkout - 
```

Enter fullscreen mode Exit fullscreen mode

这是以下内容的简写:

```
git checkout @{-1}
# Changing the index will switch back to the n-th previous branch 
```

Enter fullscreen mode Exit fullscreen mode

来回切换很容易，但是如果您想转到几天前工作的分支呢？首先，我打赌你甚至不记得它的名字。如果您使用 git 客户端，比如 SourceTree，或者您选择的 IDE 中内置的东西，您可能已经拥有了这样的功能。然而，如果您是命令行的粉丝，您可能会被卡住。Git 没有提供现成的，但是可以很容易地用一个别名来扩展。

让我们创建一个 git 命令，显示一个人过去工作过的 10 个分支的列表，按相反的顺序排序:

```
git for-each-ref --sort=-committerdate --count=10 --format='%(refname:short)' refs/heads/ 
```

Enter fullscreen mode Exit fullscreen mode

运行上面的命令会给出我们所需要的，但是编写和记忆起来很繁琐。由于 git 的别名支持，我们可以将这一长串选项映射到 git 速记子命令，比如说，`rb`。

找到一个打开的`~/.gitconfig`并添加以下代码行作为`[alias]`部分的一部分。如果没有`[alias]`部分，也要加上。

```
[alias]
    rb = for-each-ref --sort=-committerdate --count=10 --format='%(refname:short)' refs/heads/ 
```

Enter fullscreen mode Exit fullscreen mode

一旦保存了文件，新的`git rb`命令就可以使用了。

* * *

# **鸣谢与延伸阅读**

感谢[贾森·罗杰斯](https://coderwall.com/p/jhucga/git-the-last-10-branches-you-ve-worked-on)让我学会了这个技巧！

*   [https://coder wall . com/p/JH ucga/git-the-last-10-branches-you-ve-work-on](https://coderwall.com/p/jhucga/git-the-last-10-branches-you-ve-worked-on)
*   [https://marcgg.com/blog/2015/10/18/git-checkout-minus/](https://marcgg.com/blog/2015/10/18/git-checkout-minus/)

* * *

*本文最初发表于[我的博客](https://preslav.me/2018/12/13/git-tip-last-10-branches/)T3*
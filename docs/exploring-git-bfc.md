# 探索 Git

> 原文：<https://dev.to/mraza007/exploring-git-bfc>

因为我们每天都在项目中使用 git，它让我们可以更有效地与他人合作，从而让我们的生活变得更加轻松。因此，它帮助我们开发出每天都在影响我们生活的伟大软件。

### 什么是 Git？

简而言之，Git 是分布式版本控制系统，它允许我们跟踪我们的文件，并帮助我们在软件项目中更有效地相互协作。Git 是 Linus Torvalds 在 2005 年为开发 Linux 内核而创建的。它主要是用`C`和其他语言编写的，比如`python`、`shell`、Perl `and` Tcl `。

### 让我们开始探索 Git

首先，我将创建一个名为`test/`的 git repo

*   `git init test`命令在我们的机器上创建一个名为`test`的本地 repo，它包含了包含所有元数据的`.git/`文件夹。

[![Image](img/13130a398186213e917202a61ca5ea95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yrrQrWdR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i65.tinypic.com/2h37q7m.png)

在这篇博客中，我将把重点放在`objects`和`ref`上，因为它们是`.git/`目录中最重要的部分。

### Git 的实际工作原理

[![Image](img/7436cd4964c97c00d79d34773f46e19a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p5ybh8Vw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i65.tinypic.com/sgtphz.png)

所以我将用这个简单的图表来解释引擎盖下的一切是如何工作的。如你所见，我们有三样东西`working directory`、
、T1】和`local repo`。

当我们运行`git add`时，它会创建我们正在
处理的文件的快照，并将该文件放入暂存区，然后我们运行`git commit`命令，将该文件的快照永久保存在我们的本地存储库中。

### 当我们运行时会发生什么`git add <filename>`

[![add.png](img/6c19dba5c76edf9ae0bba524d7fadcdd.png)](https://postimg.cc/YLR17rs8)

当我们运行`git add`命令时，它从暂存区获取文件，并将文件作为副本保存在`objects`目录下。文件被表示为一个 blob(二进制大对象),文件名是使用`SHA-1`散列算法生成的，因为它是使用文件的内容计算的。

在这种情况下，它由`f9cae9b`表示，并且可以通过使用下面的命令`git show --pretty=raw f9cae9b`看到该对象。

### 当我们运行时会发生什么`git commit <filename>`

当我们运行`git commit`时，它从暂存中取出我们的文件，并作为永久快照保存在我们的本地存储库中。当我们运行`git commit`命令时，又创建了两个对象。**一棵树&一次提交**

可以用`1be1fd`来表示树

[![tree.png](img/6a1b5d962369add4e794ab46a061f504.png)](https://postimg.cc/BLygyQRF)

并且提交被表示为`fcae310`。此外，如果你注意到它也有一个树，它代表了我们的目录结构。

[![commit.png](img/f55f7a4968b9278bdf24b2e2eeb1bdd1.png)](https://postimg.cc/hQWmTJH2)

如果我们运行`git ls-tree <tree>`,它会提示更多关于我们的树的用户信息，以及它包含什么类型的文件。

[![tree-info.png](img/73ef9092f0504bf3cf2a39134944c5e4.png)](https://postimg.cc/Fd7q7KSW)

因此，我们的树中有一个名为`hello.txt`的文件，它被表示为一个 blob 对象，它引用了`f9cae9b`。此外，文件权限也由 git 跟踪，在本例中用`100644`表示。Git 非常智能，当我们改变文件名时，我们的原始数据并没有被删除，而是在我们的对象中创建了一个新的树，因此，它允许我们在不丢失原始数据的情况下轻松地改变文件名。

除此之外，`objects/`目录我们还有`refs/`，这包含了我们的分支。分支名称充当提交的引用。它包含一个
`SHA`，指向我们的提交，每当我们做出更改并将文件提交给回购时，它会自动更新。

这就是 Git 的工作原理。此外，git 使用一种叫做[有向无环图](https://medium.com/girl-writes-code/git-is-a-directed-acyclic-graph-and-what-the-heck-does-that-mean-b6c8dec65059)的数据结构，了解 Git 如何工作以及它如何使开发人员的协作变得更容易，这真的很有趣。

我希望你喜欢读这篇文章，如果你认为我错过了什么，欢迎在下面评论。
我将在另一篇文章中写更多关于`git rebase`和`git checkout`命令以及它们是如何工作的。

感觉在你的朋友和同事之间分享它。

##### 有趣的链接

*   [技术演讲:Linus Torvalds 谈 git](https://www.youtube.com/watch?v=4XpnKHJAok8)
*   [Git 起源故事](https://www.linuxjournal.com/content/git-origin-story)

我将每月至少写 4 篇关于编程/有趣话题的文章。
随时[订阅](http://eepurl.com/gdBlQL)

[本文发表于此](https://muhammadraza.me/2019/Exploring-Git/)
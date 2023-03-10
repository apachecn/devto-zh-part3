# 理解 Git:存储库和远程的配置

> 原文：<https://dev.to/cohere/understanding-git-configuration-of-repositories-and-remotes-39g8>

*这篇文章最初是由 Cohere 发表的，我们网站上还有许多其他关于工程的[文章。](https://www.wecohere.com/topics/engineering/?utm_campaign=understanding-git-configuration-repositories-and-remotes&utm_content=header)T3】*

最近，一个 [dev.to 社区成员问是否有人可以解释一下`.git/config`就像他们是五个](https://dev.to/jessachandler/explain-git-config-like-im-five-750)。

当我第一次试图提供帮助时，我意识到要解释`.git/config`，我们需要往回走几步。首先，通过查看软件配置的历史；然后在 Git 中研究*远程*和*存储库*之间的差异。最后展示了`.git/config`如何将它们整合到一个强大的工具中，减少你在互联网上来回铲代码时的认知负荷。

[Git 设计难](https://www.quora.com/Why-is-Git-so-hard-to-learn) *。*这就是我纠结 Git 的原因。如果你也在 Git 上挣扎，那可能也是原因。Git 故意制造的困难不一定是件坏事。但这确实意味着，要熟练使用 Git，我们需要投资建立一个深入而准确的心智模型。

但是首先，让我们看看 Unix 程序配置的当前模型是如何形成的。这将使我们能够诊断任何配置了点文件的应用程序中的异常行为，而不仅仅是 Git！如果您已经熟悉用户和目录级别的点文件，您可能想要跳到[配置 Git](#configuring-git) 。对于那些热爱历史和细微差别的人，请继续阅读！

### 一段(大幅删节)软件配置的历史

从前，我们通过在[穿孔卡](https://en.wikipedia.org/wiki/Punched_card)上直接用二进制写程序指令来操作计算机。程序一张一张地执行，直到完成，然后输出被打印到纸上。

这意味着改变程序的行为需要对卡进行物理改变。要么注入新卡，要么更换新卡。没有任何程序根据运行程序的地点*或*地点*来改变它们的行为。*

随着时间的推移，计算机变得越来越强大，输入和分发程序的机制也不那么不稳定了。越来越多的人采用了它们。而且人有*偏好*。程序需要一种方法来知道运行它的人想要的*默认行为*。

因此，程序员指示他们的程序寻找一个特定于程序使用者的文件，加载存储在该文件中的数据，并在决定如何行为时参考该文件。

这些配置文件通常对人和计算机都是可读的。这使得机器人和人都可以进行诊断和调整。

这非常有效！人们可以决定他们是否希望程序在完成时发出哔哔声，而不是 T2 的博客，或者调整程序员花时间公开的细节。

随着程序变得更加通用，*工作目录*(文件系统上运行程序的位置)变得与确定程序的行为相关。

当我们在我的`business-finance/`目录中运行一个`summarize_csv`程序时，人们不想必须通过`--group-by spending_category`！那是整整额外的 26 次击键！我们想运行`summarize_csv`，让计算机自动理解剩下的。

因此程序员指示他们的程序*也*在当前工作目录中寻找一个配置文件。随着个人或项目级别的需求变得越来越复杂，这些单独的文件演变成文件夹来存储满足这些需求所需的无数细节。

那么…这和 Git 有什么关系？

### 配置 Git

Git 的目的是跟踪特定目录中文件的变化，并允许将这些变化分发给其他 git 用户。它依赖于该目录中的`.git`文件夹来存储这些更改以及如何跟踪它们。该目录中的`.git/config`文件为`git`程序提供默认或上下文行为。如果您在一个项目的`.git`文件夹中四处搜寻，您会看到各种映射回 Git 中概念的文件和目录。

分发变更时，需要考虑两个方面:

*   在哪里可以找到我想要应用的更改？
*   我可以将更改发送到哪里，以便其他人可以应用它们？

在 Github 出现之前，通常的做法是每个人在自己的机器上保存一份文件夹历史的本地副本，作为本地存储库。然后，他们会通过 HTTP、SSH 或电子邮件授予访问权限。

这需要存储库所有者维护一个服务器，其他人可以将它作为远程存储库来访问。他们还需要做一些工作，以确保那些需要的人仍然可以访问存储库，并管理哪些人可以写入存储库。

拥有写权限的开发者在本地(T1)对*进行修改，然后*将这些修改*推送到他们公开可读的*远程*服务器。*

Github 消除了当你希望其他人能够阅读或贡献你的代码时管理你自己的基础设施的需求。Github 承担了 *remote* 的托管和维护成本，并为项目维护者提供了强大的变更工具。几十年来，人们一直在分叉(创建一个项目的副本，这样你就可以定制它)开源项目，Github 使得将原始维护者的*远程库*从原始维护者分叉到你自己的*远程库*变得非常容易。然后你可以*克隆*你的副本，并按你喜欢的方式对其进行任何修改；然后建议维护人员使用*拉请求*来应用您的更改。

如果你是维护者或者贡献一个改变的人，现在有 4 个库在起作用:

*   维护者的本地存储库
*   贡献者的本地存储库
*   维护者 GitHub 远程镜像他们的本地存储库
*   贡献者 GitHub 远程镜像维护者 GitHub 远程

每个存储库都可以通过其唯一的 URL 进行访问。你可能会注意到，每次你*推*到 Github 或者*从 Github 拉*到 Github 时，你不必每次都键入那个 URL。这是因为`git`命令行程序将这些配置信息存储在`.git/config`文件中。当您打开该文件时，您会看到类似下面的结构:

```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true
    precomposeunicode = true

[remote "origin"]
    url = git@github.com:your-git-username/your-project.git
    fetch = +refs/heads/*:refs/remotes/origin/*

[branch "master"]
    remote = origin
    merge = refs/heads/origin 
```

该文件的语法反映了 [INI 文件格式](https://en.wikipedia.org/wiki/INI_file)的语法，这是一种人类和机器可读的格式，它使用方括号对配置选项和键值对进行分组，键值对使用单个等号为配置赋值。

如果您想了解更多关于在`.git/config`选项中可配置的选项，您可以从您的终端运行`man`程序，如下所示:

`man git-config`

这就打开了(广泛！)手动用于`git-config`，并允许您使用`/`键进入“搜索”模式，或使用上下箭头和上/下翻页键浏览和搜索选项。有很多选择可以涉水而过！[如果你想要更多关于“远程”和“分支”配置分组的细节，你可以阅读我在 dev.to](https://dev.to/zspencer/comment/6m9n) 上的原始回复。

对于我们这些仍在朝着强大的 Git 心智模型努力的人来说，保持强大！你能做到的！但是请记住，不了解该工具的所有信息也没关系。你所需要的只是满足你自己需求和愿望的流利程度。你不需要知道所有的事情，因为我们行业的技术官僚认为你应该知道。

* * *

如果你觉得这篇文章有帮助，我们希望你订阅我们的[月刊](https://www.wecohere.com/newsletter?utm_campaign=understanding-git-configuration-repositories-and-remotes&utm_content=footer)！我们每个月都会分享一些我们发现的有价值的东西，以及成为更有效的程序员和工程领导者的独家见解。
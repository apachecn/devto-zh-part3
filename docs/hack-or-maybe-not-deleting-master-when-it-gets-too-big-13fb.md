# 黑不黑:当它变得太大时“删除”母版

> 原文：<https://dev.to/jenc/hack-or-maybe-not-deleting-master-when-it-gets-too-big-13fb>

只是记录一个朋友的建议。任何开发人员的意见对我来说都是宝贵的，因为我不在他们中间工作。请不要去推特上火他。

我只是在为未来的自己做笔记。

液体错误:内部

> ![polar vortex jen profile image](img/3a20d338065f1712d2688fcdac52358a.png)极涡 jen@ jenninat 0 r![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ Conrad muan](https://twitter.com/conradmuan)Rly？你是说给主人重新命名吗...🤪2019 年 2 月 28 日 21 点 39 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1101235541868232704)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1101235541868232704)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1101235541868232704)

> ![Conr🙏d Muan profile image](img/58cfe601641a71cbb24a2aaa578838b6.png)Conr🙏d Muan@ Conrad Muan![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ jenninat 0 r](https://twitter.com/jenninat0r)不对！
> git 取原点 master
> git check out-b my-branch-name origin/master
> do work
> git push origin my-branch-name
> 重复。再也不需要检查主人了。2019 年 2 月 28 日 22 点 13 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1101244099137429504)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1101244099137429504)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1101244099137429504)

> ![polar vortex jen profile image](img/3a20d338065f1712d2688fcdac52358a.png)极涡仁@ jenninat 0 r![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ Conrad muan](https://twitter.com/conradmuan)和....将部署分支更改为 my-branch-Name？2019 年 2 月 28 日下午 23:38[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1101265528612638720)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1101265528612638720)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1101265528612638720)

> ![Conr🙏d Muan profile image](img/58cfe601641a71cbb24a2aaa578838b6.png)Conr🙏d Muan@ Conrad Muan![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ jenninat 0 r](https://twitter.com/jenninat0r)取决于团队/项目。在我的情况下，我们会发布一个 PR 并通过 github 合并到 master。CI 接管部署。我不签出和拉取 master 的原因是我发现从 origin esp 获取和签出大型单声道回购2019 年 2 月 28 日下午 23:47[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1101267688226086912)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1101267688226086912)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1101267688226086912)

我最初的想法是:也许这里的诀窍是拉和取的区别。Pull 试图将您的更改与 remote 上现有的提交进行合并，这是一个巨大的有风险的回购...？

更新，对自己的教育和熏陶:

[@joelnet](https://dev.to/joelnet) 指出:

> “对于那些感到困惑的人来说，master 在远程回购上没有被删除。它仅在本地回购上被删除。git fetch 的工作方式是，您总是有一个本地副本。...一旦你做了“git check out-b my-new-branch origin/master ”,你就有了一个完全相同的“origin/master”副本。]在当地就叫‘我的新分店’吧。”

[@yucer](https://dev.to/yucer) 补充道:

> “他的意思是不在本地检查主分支机构，而只是用它作为检查你的分支机构的基础。您可以从远程直接签出您的新分支(在本例中为“origin”)。不同之处在于，当您在本地签出 master 时，git 会为它构建一个工作树。...为了正确理解它，你需要记住你不能在所有的分支机构工作。...git 以不同的方式存储更改，当您签出一个分支时，它需要计算文件的完整状态，并对您的本地文件进行所需的更改，以实现这些更改。”

康拉德的思想流派似乎与我很久以前在马克·朗加尔的博客“获取并合并，不要拉”上读到的东西遥相呼应:

> ...通过在一个[git pull]命令中同时获取和合并，您的工作目录得到了更新，而没有给您机会检查您刚刚引入到存储库中的更改。改变远程跟踪分支的安全方法是使用 git fetch 或者作为 git-push 的副作用；你不能直接在远程跟踪分支上工作。相比之下，您总是可以切换到本地分支，并创建新的提交来向前移动分支的尖端。因此，你最常做的远程跟踪[本地]分支机构是以下之一:1 .用 git fetch 2 更新它们。从他们合并到你目前的分支 3。基于它们创建新的本地分支

我还没有尝试过这种方式，但可以看到在非常大的项目中 git repos 像前者一样被更新。

@codemouse92 更喜欢维护一个易于访问的、规范的副本。

> 第一次用“git fetch ”(已经)把整个东西拉下来，然后用一个基本的“git pull ”( git pull)进行增量修改。...“git pull”使用与“git fetch”相同的带宽，您仍然可以节省一步，因为您不必每次都从本地副本中删除 master。...不要删除“主人”,别管它。
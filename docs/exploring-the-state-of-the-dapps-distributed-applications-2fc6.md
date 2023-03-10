# 探索分布式应用程序的状态

> 原文：<https://dev.to/nprimak/exploring-the-state-of-the-dapps-distributed-applications-2fc6>

一年前，每个人都在谈论区块链、比特币和即将到来的“加密货币革命”关于比特币是否是一个泡沫，网上展开了激烈的辩论，双方都有激烈的争论。自那以后，比特币的价格大幅下降，但比特币背后的技术却没有下降。

我是那些幸运的人之一，在下跌之前的正确时间搭上了比特币的顺风车。我一直对价格有一点警惕，主要是因为我从研究中发现，区块链的有趣应用仍然很少，因此没有理由相信价格会永远居高不下。对于确实存在的少数应用程序，他们只是没有足够的人使用它来证明加密货币的飙升价值(这是一种过度暗示，请随意评论，但我不想在这里过多地谈论本质)。

<figure>[![An unfortunate case of overconfidence on twitter](img/b668ca7b0f0d982844df7a82cd03e013.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--COLIOKcF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cw3ov6kasm2lpbeltg69.png) 

<figcaption>一个不幸的人...</figcaption>

</figure>

我对现有区块链应用程序的大部分研究来自一个网站，这个网站是区块链新旧应用程序的枢纽，名为“DApps 的状态”([www.stateofthedapps.com](http://www.stateofthedapps.com))。网站上展示了各种不同的应用，从虚拟世界到交易平台、社交网络到众筹页面。

对于那些可能正在阅读这篇文章但不完全了解区块链的人来说，现在是在进一步阅读之前了解一些背景知识的好时机。在很高的层面上，区块链可以为用户提供比在网上更私人的体验，因为不是所有的数据都存在于一个由公司(如脸书/谷歌)控制的可以随时访问的服务器上，而是“分布”在使用该网站/应用程序的所有用户的机器上。还在迷茫？这个视频比我能解释得更好。

[https://www.youtube.com/embed/r43LhSUUGTQ](https://www.youtube.com/embed/r43LhSUUGTQ)

有几类应用似乎更受区块链开发者的欢迎:安全、治理、数字钱包、存储和游戏。许多应用程序也是现有平台的替代品:例如，Actifit 是一个健身追踪器，它以健康生活方式的“代币”形式提供额外的奖励。另一个例子是 Busy，它类似于 Reddit，除了用户写帖子，然后根据他们收到的投票数获得报酬。

上面提到的两个应用程序都运行在一个名为 Steem 的区块链上。用他们自己的话说，“社交媒体平台的股东从用户生成的内容中赚了数十亿美元。内容创作者？他们什么也没做。Steem 翻转模型，将价值返还给贡献最大的人。”根据他们的网站，自 6 月以来，Steem 已经向用户支付了超过 5900 万英镑的奖励。

然而，并非所有向用户付费的平台都运行在 Steem 上。另一个例子是 Cent，它运行在以太坊上，自称为“激励的创造力交流”。也有在以太坊上运行的游戏，例如 CryptoRome，在那里你从一块免费的土地开始，使用“策略、政治和力量”将你的小块土地变成一个帝国，同时与其他玩家竞争。

希望这几个例子能够说明分布式应用程序的世界正在变得多么多样化和令人兴奋。我强烈建议你亲自去看看这个网站，探索一下。很有可能在未来我会写更多关于这个主题的文章，但是现在我认为这是一个很好的介绍和总结。

直到今天，我仍然相信《DApps 之国》让我看到了所有创造性和有趣的方式，人们可以应用区块链技术来解决不同的问题，并创造现有技术的替代品。我自己只做了一个相对简单的宠物店应用程序，人们可以收养一只明显不是真实的宠物，并跟随松露指南([https://truffleframework.com/tutorials/pet-shop](https://truffleframework.com/tutorials/pet-shop))。你可以在我的 github 上查看这个应用的代码:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ nprimak ](https://github.com/nprimak) / [宠物店乙醚](https://github.com/nprimak/pet-shop-ether)

### 为区块链以太坊打造的分布式宠物收养网络应用

<article class="markdown-body entry-content container-lg" itemprop="text">

# 宠物店乙醚

为区块链以太坊打造的分布式宠物收养网络应用

在命令行中运行“npm run dev”以在本地运行 web 应用程序

在单独的终端窗口中，运行“testrpc”命令，该命令将连接到(测试)区块链的本地实例

确保你在 Chrome 中安装了 MetaMask，并复制/粘贴你的助记高清钱包密码。

在浏览器中打开元掩码弹出窗口，确保在右上角的下拉菜单中选择了 localhost:8545。

点击“忘记密码”,将您之前复制的 Menmonic HD Wallet 密码粘贴进去。

现在，您应该已经登录，能够收养宠物，并批准/拒绝 MetaMask 扩展中的交易。

</article>

[View on GitHub](https://github.com/nprimak/pet-shop-ether)
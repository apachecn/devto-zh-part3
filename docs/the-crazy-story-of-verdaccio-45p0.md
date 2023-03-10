# 维尔达奇奥的疯狂故事

> 原文：<https://dev.to/verdaccio/the-crazy-story-of-verdaccio-45p0>

这不是我第一次听到下面的表达“感谢创建 Verdaccio”，这实际上是在奉承我，但真的很难用几句话来解释**我没有创建 Verdaccio** 。也许我应该对今天的 Verdaccio 负责，但那是另一回事了。今天我想分享这个项目背后的整个故事，以及我是如何完成这个项目的。

### 《缘起缘灭》

几年前的 2013 年，主注册中心 *(npmjs)* 运行了一段时间，与此同时， [Alex Kocharin](https://github.com/rlidwka) 决定创建 Sinopia。

最初的目标是创建一个私有注册中心和缓存，以减少 **npmjs** 和私有注册中心之间的延迟。那时 **npmjs** 开始[与他们自己的性能问题](https://blog.npmjs.org/post/97261727560/npm-inc-and-scalenpm)斗争，并且能够托管私有包*还没有被支持*。

> ![Laurie Voss profile image](img/b2c6b8615fb33371b900846f231881f4.png)劳里沃斯@谢多![twitter logo](img/ca5dfdb28b446702d18cbfa9843f8167.png)没人需要私人 [@npmjs](https://twitter.com/npmjs) 包比 npm，Inc..我们和你一样需要它。更多。2014 年 06 月 06 日 23 点 36 分[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=475058595034181632)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=475058595034181632)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=475058595034181632)8

事实上**，Sinopia 是在 11 月 4 日**的 [**大 npm 秋天**](https://nodejs.org/en/blog/npm/2013-outage-postmortem/#what-went-wrong-and-how-was-it-fixed) **之前以及第一个注册中心运行之后很久才创建的。这一事件让人们注意到，在内部拥有一个包*代理/缓存*注册中心是完全有意义的，同时该项目不断发展，增加了一些有趣的功能，如*范围包、UI 搜索、插件、覆盖公共包*等。**

很明显，该项目正在增长，但在 2015 年 10 月**发生了一些事情**其中是最近提交的日期，Alex 仍然是当前的所有者，他决定不再回复任何人，原因不明，似乎将永远如此*(他最近在其他项目中有活动)*和**，因为是唯一的所有者，该项目仍然处于冻结状态。**

### 后中国梦时代

[![](img/9248745b2e337b13f38e2530bba4d361.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--muOjfeAW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/779/1%2At8GSq1qq6RC4iQsx1bYDgg.png)

2016 年初[sino pia 社区开始怀疑](https://github.com/rlidwka/sinopia/issues/376)为什么如此好的想法和良好的支持就这样无缘无故地停止了。

几个月后，福克斯没多久就出现了。最突出的分叉是下面的*(我知道远不止这些)*:

[![](img/a5be197ff8fcb7b81692a3d3f5c7c2ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_bkKCPSc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/700/1%2AAlByG_WIbkxp6W9OH0JYzQ.png)

*   [**【sinopia 2】**](https://github.com/fl4re/sinopia):可能是最实惠和最新的分叉，似乎打算合并一些[的 PR 在队列中](https://github.com/rlidwka/sinopia/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+dead#issuecomment-197239368)。尽管如此，今天似乎有一些发展，但没有进一步的新功能。
*   [**shimmer js/sinopia**](https://github.com/shimmerjs/sinopia):IBM 团队贡献者为 sinopia 提供 CouchDB 支持的一次尝试。他们发布了几个版本，但是自从分叉*(这个想法是 Verdaccio 很长一段时间的公关，但是从来没有被合并过)*。
*   [**【NPM-register】**](https://github.com/jdxcode/npm-register):灵感来源于 sinopia fork，但从头开始创建，专注于在 PaaS 服务上托管。
*   **verdaccio** :这里是一切开始的地方，2016 年 4 月 5 日开始的 0 公里，由 [**cuzzinz**](https://github.com/cuzzinz) 的“洗礼”暗示他在维基百科上读到的名字。

> 因为这将是一个叉子，按照原来的项目使用的主题，但一个新的“颜色”….韦尔达乔

### Verdaccio 为叉

在几个月没有人掌舵之后[约翰·威尔金森](https://github.com/jmwilkinson)和[特伦特伯爵](https://github.com/trentearl)于 2016 年 4 月**创建了 Verdaccio 组织**。

<figure>[![](img/a6b3a5c30c6362fc52613c5f526c0ee3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z2xV0TjO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/797/1%2AAIbetKbnOhE9lVJIJO7ZnQ.png) 

<figcaption>趋势伯爵宣布中国之叉</figcaption>

</figure>

最初，该项目只是另一个分叉，但很快就开始收到 PR 的更新，这些更新在 *sinopia* 搁置了很长时间，甚至在 *Sinopia2* 上提交了更改。所有的分叉都有一种缺乏承诺和混乱的感觉，不知何故这个问题被 Verdaccio 的作者很好地解决了，为项目提供了第二次呼吸。

这就是我进来的地方。【2016 年 8 月，我开始像其他人一样做出贡献，我最初的角色是修复 Node 6 上的单元测试，并在几个领域稳定项目，帮助 *Trend* 回答论坛上的问题，并一起发布第一个稳定版本的 Verdaccio **v2.0.0** ，这是第一次尝试在项目中建立一些秩序。

> 如果你问我为什么决定贡献 Verdaccio。原因是我喜欢这个名字。

在 2016 年秋季和 2017 年初*期间，我们注意到更多的采用和错误报告，但在 2017 年 2 月**原作者在 v2.1.1 发布之前将 Verdaccio** 的所有权交给了我，他们已经退出了开发，目前只是观察者。现在我仍然为有机会推动这个项目而感到超级高兴和感激。*

> 顺便说一下，在那段时间里，即使我有很好的 js 背景(在我的私人工作经历中，直到今天我都是前端开发人员)，我也没有机会在任何工作场所使用 Node.js，有趣吧😅？。我对 real Node.js 开发的了解完全来自于 Verdaccio 和阅读开源代码。

在 2017 年初**,这个项目只有大约 600 颗星星，我开始协调新的贡献和向现代代码库的渐进迁移。我必须强调给项目带来的新想法[meeeow](https://github.com/Meeeeow)，比如语义提交、基于 React 的新 UI 以及其他有趣的东西。**

 **当你分叉一个项目 Github **会降低谷歌和 GitHub 搜索**的可见性，因此[我向 GitHub 询问了这个问题](https://github.com/verdaccio/verdaccio/issues/75#issuecomment-290631295)。他们友好地移除了我们在主存储库中保留了 1 年的 fork 标签。

<figure>[![](img/65e81ebad73abccfac2479d5616d133b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_VJFSNom--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/301/1%2AEF5a7ODsYd3OLMWbVQk37A.png) 

<figcaption>官方 logo 由社区提供</figcaption>

</figure>

2017 年以相当数量的明星(~ 1200)，数千次下载和一个[新标志](https://github.com/verdaccio/verdaccio/issues/328)结束，但*我们仍然没有能够做一个主要的发布*。有太多的事情要做，而且在许多领域缺乏知识。

#### 码头工人

那时，Docker 对我来说是新的，直到我第一次看到 Docker 文件，并得到了如此多的与此主题相关的门票，迫使我很快学会能够合并对我来说是中国的贡献，我做了什么？。参加 Docker meetups，阅读书籍。问题解决了。谢天谢地，社区在这方面有很多知识可以分享，因此我有机会从这些惊人的贡献中学习。**如今 Docker 是使用 Verdaccio** 最流行的方式，甚至超过了 *npm* 安装。

### 2018“年”

<figure>[![](img/66c58a02123c29c14b5ce2ba1ed20434.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MeBAEvmw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/804/1%2A77nCfVH9qaQbP1dBkAXBMg.png) 

<figcaption>Verdaccio 天桥 sinopia 上星 2018 年 12 月</figcaption>

</figure>

我不得不承认 2018 年是超级疯狂的，因为该项目的第一个月得到了非常好的消息，并由非常受欢迎的人做了广告(是的，这很有帮助),感谢[丹·阿布拉莫夫](https://medium.com/u/a3a8af6addc1)。 **create-react-app** 开始用作 E2E 工具，这对我来说是全新的场景，改变了我们对这个项目的看法，后来，又有了其他项目，如 **Storybook、pnpm、Eclipse 忒伊亚、Hyperledger 或 Modzilla Neutrino** 。

与此同时，我们在年初发布了一个[新网站](https://verdaccio.org/)，这个网站现在非常受欢迎，并且减少了关于 Github 是用户第一信息来源的质疑，顺便说一下，我们是 **Docusaurus** 的早期采用者之一。感谢 [Crowdin](https://crowdin.com/project/verdaccio) 提供了一个翻译平台，现在社区已经发布了我们文档的 7 个完整翻译版本。

<figure>[![](img/908a2afff504fc9ce80252426f3f637c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--29UPPDmG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/867/1%2Av-dZShJE4VVgF4fbKMtkBA.png)

<figcaption>Google analytics 上各国的访问率</figcaption>

</figure>

到那时，一个新的贡献者从 2017 年开始变得超级活跃， [Ayush](https://medium.com/u/ffdb15785e37) 在工作中使用 Verdaccio。起初，他的反馈对实时使用很有用，如今**他也是 2018 年这个项目成功的作者之一**。

经过几个月的疯狂努力，我们终于在 5 月发布了 Verdaccio 3。这给了我们一个小小的停顿，让我们重新思考未来该做些什么，以及如何改善我们的社区。

此外，我们还邀请了塞尔吉奥·埃雷拉·古兹曼(Sergio Herrera guzmán)和[普里西拉·奥利韦拉(Priscila Oliveira)](https://medium.com/u/c1899129305b)，他们对 Verdaccio 的新发布渠道和将于 2019 年发布的新用户界面表现出了浓厚的兴趣。**该项目目前有大约 150 名贡献者，我们张开双臂欢迎新的贡献者**。

我已经看到[用多种语言写的关于 verda CIO 的文章](https://github.com/verdaccio/verdaccio/wiki#articles)，[会议发言人推荐](https://youtu.be/q4XmAy6_ucw)verda CIO 的用法，[慷慨的捐款](https://opencollective.com/verdaccio)以及我们[不和谐的聊天](http://chat.verdaccio.org/)比以往更加活跃。

为了完成这个故事，并在 2018 年结束，我们创建了我们定义的核心团队，一个小型开发人员小组试图在 Verdaccio 4 的开发中合作。

### 当前状态

如果您想知道 Verdaccio 的“治理”是如何工作的，我们可以按以下方式进行。我们有 4 个负责人(创始人、[胡安·皮卡多](https://medium.com/u/a6a7b0f6a9e4)、[阿尤什](https://medium.com/u/ffdb15785e37))，当有重要事情发生时，我们会与他们沟通，我们每 6 个月在 GitHub teams threads 发布一份内部报告。我们已经决定了这种结构，以避免类似的事情再次发生。基于民主和常识，在核心团队层面做出发展决策。

开发沟通发生在不和谐的情况下，我们开始鼓励代码评审，并对所有事情进行公开讨论。目前，它是有效的，但是我们正在努力改进这个过程。

目前，我们正在努力改进文档，并创建一个干净的插件、集成和新方式的生态系统，以告知、教导新采用者关于注册表的用法，并帮助加入希望成为开发一部分的新贡献者。

### 包装完毕

如您所知，Verdaccio 不是一个作者的项目。这是许多开发者的合作，他们决定不让这个项目夭折。如果你允许我[引用亚伯拉罕·林肯的名言](https://en.wikipedia.org/wiki/Gettysburg_Address)，我总是喜欢这样想

> Verdaccio 是一个社区项目，由社区实施并为社区服务。

我今天推动这个项目，但并不意味着我会永远这样做。我喜欢和其他人分担责任，因为没有人像其他开源项目那样全职工作。

**我们希望这个项目活着，更新和可靠，开源和免费的选择，为每个人**。遵循 sinopia 确立的简单、零配置和可扩展的原则。

即使一些最初的开发者不再有所贡献*(我们都有自己的生活)*，我真的很感激他们投入的时间，并希望他们能在某个时候回来。

### 免责声明

我讲这个故事是基于我自己的研究和最近两年收集的所有信息，评论，私人聊天和社交网络。

* * ***
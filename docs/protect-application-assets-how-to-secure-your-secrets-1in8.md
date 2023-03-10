# 保护应用程序资产:如何保护您的秘密

> 原文：<https://dev.to/chingu/protect-application-assets-how-to-secure-your-secrets-1in8>

给你一个提示:你的浏览器不安全！

<figure>[![](img/754d57647c9849907714ceea06c73d85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kmEuosVA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXQ-Jm8pc0HAsF_hJw0eFhA.jpeg) 

<figcaption>照片由[戴恩托普金](https://unsplash.com/photos/u5Zt-HoocrM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/security?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

### 为什么要关心信息安全？

不要成为那个那个人！你知道，IT 专业人员对信息安全采取漫不经心的态度，然后成为头条新闻。

*   脸书:5000 万用户
*   国际足联:3.4 万亿字节的数据和 7000 万份文件
*   Google+: 50 万账户
*   英国航空公司:38 万笔交易
*   t-Mobile:200 万客户
*   万豪酒店:4.27 亿顾客

如果说在过去的十年里，我们作为专业人士没有学到什么，那就是各种类型和规模的信息系统正受到来自临时黑客、犯罪分子和国家的越来越多的攻击。

<figure>[![](img/9aedaf5717507fbc94afca9e0eb06247.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MNAaeNOb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/569/1%2AJYrFoTyBbs3hlJj7YB4Atg.jpeg) 

<figcaption>[市场观察 2018 年 5 月 25 日](https://www.marketwatch.com/story/how-the-number-of-data-breaches-is-soaring-in-one-chart-2018-02-26)</figcaption>

</figure>

不仅违规数量在上升，随之而来的金钱损失也在上升。全球估计处理一次数据泄露的平均成本为 386 万美元。当然，这是一个平均值，实际支出取决于涉及的数据量和安全渗透的复杂程度。

平均费用估计数的进一步细分如下:

*   每张唱片 148 元
*   每 100 万张唱片 4000 万美元
*   每 5000 万张唱片 3.5 亿美元

<figure>[![](img/a91a7f761f9d5fd47525e3f49f6e1523.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NKo__2cT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/687/1%2AxOHqxX99QFUz5BCq1CsNCg.png)

<figcaption>2001 年至 2017 年 IC3 因举报网络犯罪造成的货币损失金额(百万美元)。出自</figcaption>

</figure>

从一个纯粹自私的角度来看，想想遏制和纠正违规行为的影响所带来的侵扰。当组织发现他们遭受了一次违规时，随之而来的是一种“全员参与”的心态，安全顾问和执法部门参与进来，执行管理层将其置于更严密的审查之下，生产性工作陷入停顿。

> “如果没有乐趣，你就没有做好”…弗兰·塔肯顿

### 基础设施安全与应用安全

<figure>[![](img/a3e7d85f85a488ae275b287806772ac4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1KsW8ztt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdqqTO5w1PlGyhhXOzg1b_g.jpeg) 

<figcaption>照片由[乔恩·摩尔](https://unsplash.com/photos/bBavss4ZQcA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/security?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

大多数信息安全计划侧重于保护基础 IT 组件，包括网络、PC 和服务器、存储和数据中心。将重点放在基础设施上是必要的，但它不是一个成熟的信息安全计划的唯一组成部分。

[应用安全](https://en.wikipedia.org/wiki/Application_security)包括各种技术，这些技术能够发现、修复和防止应用软件中的可利用漏洞。这些技术跨越整个应用程序生命周期，包括:

*   由安全专家进行的代码审查，其目标是识别应用程序特有的安全缺陷。
*   [基于使用应用程序的黑盒安全审计](https://en.wikipedia.org/wiki/Black-box_testing)
*   在编写任何源代码之前，在[威胁模型](https://en.wikipedia.org/wiki/Threat_model)的框架内审查应用程序设计。
*   使用专门设计的自动测试工具来识别安全漏洞。
*   实施[众包奖金计划](https://www.technologyreview.com/s/611892/crowdsourcing-the-hunt-for-software-bugs-is-a-booming-businessand-a-risky-one/)以利用[白帽黑客](https://en.wikipedia.org/wiki/White_hat_(computer_security))在漏洞被利用之前定位并修复漏洞。

最有用的信息安全流程基于分层安全的概念。分层方法通过提供针对每种资产量身定制的保护措施，提供深度防御来防止和遏制入侵。

<figure>[![](img/52a89a2d800a848f303cba72f94b7540.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rPwtgeWu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/391/1%2AC17lN0ylPqoJboPniJ5jQg.png) 

<figcaption>图 1 —纵深防御</figcaption>

</figure>

考虑到应用程序是内层之一，优先考虑应用程序安全性是有意义的。当您考虑到应用程序是面向外部的组件，因此为潜在的黑客提供了进入系统的明显途径时，这也是有意义的。

### 你不能保护前端

<figure>[![](img/93470a0515b4365d19a036ce43b6dca9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gEsUVWU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMRd4JCZYxl--Mjm-KEECww.jpeg) 

<figcaption>照片由[Con Karampelas](https://unsplash.com/photos/peLs78VbU50?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)on[Unsplash](https://unsplash.com/search/photos/webpage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

Web 应用程序有很高的暴露程度和风险，因为浏览器本质上是不安全的。这不是对浏览器的控诉；它是基于执行环境强加的架构的事实陈述。对于新的 Web 开发人员来说，尝试保护浏览器中的数据并以失败告终几乎是一个必经之路。

一个特别敏感的领域是使用基于云的服务进行身份验证所需的密钥和秘密。例如，作为认证过程的一部分，使用 GitHub API 的应用程序必须提供一个令牌来标识自己。然而，保持秘密的私密性是至关重要的，因为一旦泄露，一个[坏人](http://solutionsreservoir.com/resources/introduction-to-cybersecurity/part-1-cybersecurity-overview)可能会利用它们来窃取源代码或破坏回购协议。

#### 前端安全的诱惑

前端开发人员经常陷入的一个陷阱是相信他们通过将秘密(如私钥和令牌)保存在源代码之外的某个地方是安全的。无论将应用程序的秘密保存在源代码以外的地方有多重要，如果客户机运行在浏览器中，它们就不安全。

让我们通过研究一种用于将数据从源代码中分离出来的技术，以及认为它提供了一定程度的安全性是多么容易，来进一步检验这一点。

环境变量用于通过将数据从源代码移动到一个文件(如`.env`文件)来具体化数据。将这个文件名添加到`.gitignore`的额外步骤防止`git push`命令将它上传到 GitHub repo，在那里，对于公共回购，任何人都可以使用它。

<figure>[![](img/5f15b1a95fac4e72f0249a7331231cf1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8OLmkg9H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/341/1%2AokR3QXAskp26TegoPaD1pQ.png) 

<figcaption>图 2 —保守秘密。env</figcaption>

</figure>

这不仅隐藏了代码中的环境变量值，而且通过定义明确的名称引用它也增加了源程序的清晰度。

#### 一个肮脏的小秘密

好吧，这不是一个秘密，但人们经常忽略的是,`.env`文件没有加密。对于 Create React 应用程序，可以使用浏览器的开发工具选项检查位于`build/static/js`目录中的包来验证这一点。在这里你会发现`0.chunk.js`包含了环境变量和它们在[明文](https://en.wikipedia.org/wiki/Plaintext)中的值。

<figure>[![](img/bc291c22fb3619081542d15e691fbefd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fqytYtOA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/879/1%2A7s1R_PNa9LS7hYxLi8Cjnw.png) 

<figcaption>图 3 —浏览器中的明文环境变量</figcaption>

</figure>

那么使用环境变量比在源代码中定义秘密更好吗？这难道不意味着应用程序仍然存在风险吗？

答案是，将秘密放在环境变量中不会显著增加安全性，也不会降低风险。一个简单的事实是，浏览器中的客户端数据无法得到充分的保护，这种技术充其量只能让敏感数据远离人们的视线。

人们很容易想到这样一个场景:使用[代码分割](https://webpack.js.org/guides/code-splitting/)来推迟下载包含环境变量的脚本，直到应用程序对用户进行了身份验证之后。但是，如果客户端计算机受到危害，这就违反了深度防御的概念，因为文件系统将会被坏人访问。

### 解

<figure>[![](img/a7ad32e26b606a28d8dfe2133856a251.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--npRTxwV8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A90OqH2-MZ4cdAHmbOgStrQ.jpeg) 

<figcaption>照片由[奥拉夫·阿伦斯·罗特内](https://unsplash.com/photos/4Ennrbj1svk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[下](https://unsplash.com/search/photos/solution?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

与前端不同，后端应用程序可以充分保护敏感数据。这些保护包括使用 [OAuth](https://en.wikipedia.org/wiki/OAuth) 服务来提供额外级别的认证和访问控制，加密来保护静态的[数据](https://en.wikipedia.org/wiki/Data_at_rest)，以及使用 HTTPS 和 TLS 等协议来保护动态的[数据](https://en.wikipedia.org/wiki/Data_in_transit)。

<figure>[![](img/c9c6b5c4e4cbefbfcd1995a4dea58d0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cuTvKxVP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/451/1%2AQ6X4kjCqE48Z24OGGbptWg.png) 

<figcaption>图 4 —客户端/服务器应用架构</figcaption>

</figure>

如何保护应用程序机密的难题可以通过实现服务器端应用程序 API 来解决，该 API 充当代理，将前端应用程序与其使用的服务提供商隔离开来。API 从后端应用程序向外部服务进行身份验证，发出请求，然后只向前端返回结果，而不是在前端使用秘密向服务进行身份验证。私钥或秘密决不会暴露给前端。

### 包装完毕

<figure>[![](img/5b354d66628ef185142edc47c1cee50b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z3gHbL8L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0ern_Rqaw5d5tTuYRs78IQ.jpeg) 

<figcaption>照片由[米格尔·阿穆蒂奥](https://unsplash.com/photos/P1Qg-kJoxiU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)于 [Unsplash](https://unsplash.com/search/photos/knot?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

让用户放心，您已经采取了必要的步骤来保护他们的信息，这需要仔细规划、基于深度防御的深思熟虑的安全策略以及对细节的关注。

仅仅依靠“技术孤岛”是不够的可靠地解决安全性问题需要了解信息如何在组件之间流动，以确定差距、弱点以及如何解决它们。

如果您关心前端应用程序的安全性问题，并且已经解决了这个问题，这是令人鼓舞的。作为一名 Web 开发人员，认识到并接受安全始于你，表明你是一个负责任的人，并且你认真对待你的工作。

坚持下去，在外面注意安全！

#### 更新 2018 年 12 月 19 日

有关在应用程序中使用环境变量的更多信息，请参见以下文章。

[介绍环境变量以及如何使用它们](https://medium.com/@jdmedlock/an-introduction-to-environment-variables-and-how-to-use-them-f602f66d15fa)

* * *
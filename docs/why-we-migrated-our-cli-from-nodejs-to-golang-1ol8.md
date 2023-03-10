# 为什么我们将 CLI 从 NodeJS 迁移到 GoLang💻

> 原文：<https://dev.to/uilicious/why-we-migrated-our-cli-from-nodejs-to-golang-1ol8>

> “我们为什么”文章旨在分享我们的工程考虑和决策，但这并不意味着我们的决策对您的用例来说是最好的*，因为您独特的环境非常重要！*

我们的 CLI 最初是在两年前编写的 NodeJS。

这是我们在 UI-licious 早期快速开发的东西，当时我们的重点是快速移动和快速迭代产品。我们希望尽快推出 CLI**，以便拥有 CI/CD 的用户可以将他们的测试连接到他们的前端部署管道。曾经有用的[指挥官](https://www.npmjs.com/package/commander)包有助于快速设置命令行界面。**

 **# 原 CLI 怎么了？

[![Heaviest objects in the universe](img/9e9bb2998ccdfd946b09566b11a5e504.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0iaJ2noT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/khpvdmhwwwy0xqzmskzr.png)

这个版本很好地服务了大多数用户，尤其是在测试版发布的初期。虽然我们在自己的 CI/CD 管道中使用了自己的 CLI，并认为它可以做得更好，但直到在 CI/CD 管道中大量使用 CLI 的成熟软件团队的反馈表明，我们显然需要一个更好的解决方案。

这些问题大多与 CLI 的安装有关。你看，最初的 CLI 对开发人员和测试人员来说工作得很好。但是对 DevOps 来说就不那么友好了，因为 npm 可能是一个相当大的痛苦。我一会儿会谈到那件事。

因此，我们决定从头开始重写 CLI，并设定 CLI 的目标。

# 新 CLI 的目标

## 1。零部署依赖性

而 node.js/npm 已经征服了前端开发领域。

人们很容易忘记，当前 web 开发的很大一部分仍然在使用很好的旧工具。和大多数不基于 node.js 的项目的 CI 环境中，不会预安装它们。

因此，为了在此类项目的配置项中使用我们的 CLI 工具链，一些用户最多需要额外等待 15 分钟来安装整个 node.js/npm 堆栈。

或者在更糟的情况下，发现由于网络策略或与现有项目的依赖性不兼容，这根本不可能。

所以我们能依赖的越少越好。

> 实际上绝对的零依赖是不可能的，例如你总是依赖于操作系统。但这是一个努力的目标。

## 2。单一文件分发

使用过许多 CLI 工具后，下载单个文件并执行命令的能力——无需安装程序，甚至无需设置过程——给用户带来了奇迹。

这有一个额外的好处，使它很容易向后兼容我们的 NPM 分销渠道。通过快速单文件粘合代码将 NPM 命令链接到新文件。

* * *

# 评估我们的选项

[![nodejs logo](img/9f2e91685ed01346d290f41d373b9f36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CVp_CytQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gt7gzdwlczbjhdxaoa1a.png)

**Node.js + NPM**

*好的*

*   适用于我们 75%以上的使用案例
*   **便于公司维护。JS 是我们所有开发人员的必修知识**
*   易于编码
*   跨平台

*坏*

*   **没有一个文件**
*   对于必须使用过时版本(出于其他工程原因)的一小部分用户，Node.js 或 NPM 的依赖性和兼容性问题
*   许多企业网络政策对 NPM 并不友好

*总体*

对于 JS 独占的项目，这将是一个明显的选择，其中 node.js 和 NPM 是一个安全的假设。或者当我们想尽快完成工作时。

不幸的是，那不是我们。当包含“其他人的代码”时，兼容性地狱是一个巨大的痛苦。

* * *

[![java logo](img/491a0f18333d4d0befdecc655a012368.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VSKqWVM8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/su9no974jgkuiejmum00.png)

**Java**

*好的*

*   极度跨平台
*   单个 JAR 文件
*   便于公司维护。Java 是我们主要的后端语言

*空档*

*   [主观的] CLI 库语法:感觉像一件苦差事

*坏*

*   可能在资源使用上有些过度了
*   JVM 依赖性:与 NPM 相比，我们可能有更多没有安装 java 的用户

*总体*

众所周知，Java 痴迷于向后兼容性。如果我们在 java 6 中构建我们的 CLI，我们可以非常自信地说，我们不会面临任何与其他项目的兼容性问题。在从 IOT 设备到超级计算机的任何设备上运行相同的代码。

然而，它仍然是一个巨大的依赖。尽管对于任何人来说，安装 node.js / npm 都相对容易，但 25%以上的用户需要安装 JVM 来支持我们的工具，这一事实并不适合我们。

说真的，除了基于 java 的工具本身。那些用 java 开发在线 SaaS 产品的人是罕见的。所以`¯\_(ツ)_/¯`

* * *

[![dev.to run script](img/f9e70d3c47f906be503c5b6c28c5a4b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9JizBpBO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c4b4tsy3qgtht0y2peda.gif)

# Shell 脚本+ Windows shell？

*好的*

*   **最小的单个文件部署**(按字节计数)
*   很容易找到工作

*空档*

*   严重依赖于几个操作系统模块，而大多数对于 90%的使用案例来说都是安全的假设。这是需要注意和小心的事情。对于剩余的 9%的用例，可以使用自动安装步骤来缓解。

*坏*

*   什么 CLI 库？
*   编写好的、易读的 bash 脚本并不容易，也不容易教。
*   对公司来说很难维持；公司里只有两个开发人员有资格完成这项工作；而且他们还有其他优先考虑的事情
*   Windows？我们需要为一个专用的批处理文件等价物做双倍的工作吗
*   还记得那 1%吗？对于为 XYZ 配置的 VIP linux 公司环境，这种情况很容易发生。这迫使脚本编写者根据安装的模块构建复杂的检测和切换逻辑。这将形成一个极其复杂的代码库，容易达到 10 倍或更多(一个极端的例子是:no curl/wget/netcat？[编写原始 http 请求套接字](https://jsiu22.github.io/blog/2017/05/16/Post-JSON-to-a-RES-Tul-service-without-wget-curl-netcat.html)

*总体*

尽管它有很多缺点，但它的最终包将会非常小，文件大小不到 100 kb——未经压缩和缩小。(意味着它可以更低)

作为比较，我们的 go 二进制文件是 10MB

特别是在有特定约束的情况下，比如对某些依赖项的保证，或者最后 1%不重要的项目:这将是我的首选。

一个例子就是我最近为 docker run 脚本开发的 PR。

[![picocreator image](img/9d01ba033264443700b6c4fc664da969.png)](/picocreator) [## 启动您自己的开发工具🐳服务器——只需一个命令(几乎)！

### Eugene Cheah Feb 22 ' 195 分钟阅读

#showdev #docker #opensource #meta](/uilicious/adopt-your-own-devto----with-a-single-command-almost-1c04)

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 功能:docker-run.sh 脚本+ docker 容器构建 #1844](https://github.com/thepracticaldev/dev.to/pull/1844) 

[![PicoCreator avatar](img/7d9c85b16a139dfe22b8918d388ce270.png)](https://github.com/PicoCreator) **[PicoCreator](https://github.com/PicoCreator)** posted on [<time datetime="2019-02-22T09:09:46Z">Feb 22, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/1844)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

一个 bash 脚本，帮助快速设置开发或演示环境

```
bash-3.2$ ./docker-run.sh 
```

```
#---
#
# This script will perform the following steps ... 
#
# 1) Stop and remove any docker container with the name 'dev-to-postgres' and 'dev-to'
# 2) Reset any storage directories if RUN_MODE starts with 'RESET-'
# 3) Build the dev.to docker image, with the name of 'dev-to:dev' or 'dev-to:demo'
# 4) Deploy the postgres container, mounting '_docker-storage/postgres' with the name 'dev-to-postgres'
# 5) Deploy the dev-to container, with the name of 'dev-to-app', and sets up its port to 3000
#
# To run this script properly, execute with the following (inside the dev.to repository folder)...
# './docker-run.sh [RUN_MODE] [Additional docker envrionment arguments]'
#
# Alternatively to run this script in 'interactive mode' simply run
# './docker-run.sh INTERACTIVE-DEMO'
#
#---
#---
#
# RUN_MODE can either be the following
#
# - 'DEV'  : Start up the container into bash, with a quick start guide
# - 'DEMO' : Start up the container, and run dev.to (requries ALGOLIA environment variables)
# - 'RESET-DEV'   : Resets postgresql and upload data directory for a clean deployment, before running as DEV mode
# - 'RESET-DEMO'  : Resets postgresql and upload data directory for a clean deployment, before running as DEMO mode
# - 'INTERACTIVE-DEMO' : Runs this script in 'interactive' mode to setup the 'DEMO'
#
# So for example to run a development container in bash its simply
# './docker-run.sh DEV'
#
# To run a simple demo, with some dummy data (replace <?> with the actual keys)
# './docker-run.sh DEMO -e ALGOLIASEARCH_APPLICATION_ID=<?> -e ALGOLIASEARCH_SEARCH_ONLY_KEY=<?> -e ALGOLIASEARCH_API_KEY=<?>'
#
# Finally to run a working demo, you will need to provide either...
# './docker-run.sh .... -e GITHUB_KEY=<?> -e GITHUB_SECRET=<?> -e GITHUB_TOKEN=<?>
#
# And / Or ...
# './docker-run.sh .... -e TWITTER_ACCESS_TOKEN=<?> -e TWITTER_ACCESS_TOKEN_SECRET=<?> -e TWITTER_KEY=<?> -e TWITTER_SECRET=<?>
#
# Note that all of this can also be configured via ENVIRONMENT variables prior to running the script
#
#---
```

并使用 docker 进行部署。包括在部署前进行重置的选项。

此处提供了可选的上下文信息:[单指令 https://dev.to/uilicious/adopt-your-own-devto-几乎-1c04](https://dev.to/uilicious/adopt-your-own-devto----with-a-single-command-almost-1c04)

## 需要指点...

如果有人能指导我如何在“生产”模式下运行 dev.to，这将有助于提高 docker 容器的整体性能

## 添加到文档中？

*   docker hub 的自述文件示例:[https://github . com/uilicious/dev . to-docker/blob/feature/docker-run-script/docker hub-readme . MD](https://github.com/uilicious/dev.to-docker/blob/feature/docker-run-script/dockerhub-readme.md)
*   [https://cloud . docker . com/u/uilicious/repository/docker/uilicious/dev . to](https://cloud.docker.com/u/uilicious/repository/docker/uilicious/dev.to)
*   我可以修改 readme 如果这个看起来不错？

## 什么 gif 最能描述这次公关

[![quick demo](img/7e0e38127a1885f0aace4f459bf582c7.png)](https://camo.githubusercontent.com/8bfe439f41fd2a49537c35ef8a372afa1795f462/68747470733a2f2f74686570726163746963616c6465762e73332e616d617a6f6e6177732e636f6d2f692f6e686c7262366535326b6374787434667a726d762e676966)

## 什么 gif 最能描述它给你的感觉？

[![how i feel](img/b8339a14cbc8faacf60313fd71081ba7.png)](https://camo.githubusercontent.com/4d650253e40f7666e135c1314b84586fe3e17b52/68747470733a2f2f74686570726163746963616c6465762e73332e616d617a6f6e6177732e636f6d2f692f376977756e6262796f76336d66746f77746867632e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/1844)

* * *

[![golang mascot gopher](img/e3e4bea23280e9e535cdf2fa43dde8c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0q9sj6fn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wvz5ds8yskdutswiw4lh.png)

# 走郎

*好的*

*   **单个二进制可执行文件**
*   相当好的图书馆可用
*   语言基础相对容易学(从 java 跳转)
*   有一个可爱的吉祥物

*空档*

*   陡峭的使用学习曲线，遵循其固执己见的编码实践。

*坏*

*   团队中没有人可以声称对围棋有“深刻的体验”
*   由于极端的类型安全:处理 JSON 数据真的是一件痛苦的事情***

*总体*

最大的吸引力之一是能够编译到任何具有相同代码库的平台，甚至是古老的 IBM 系统。

虽然这种语言本身很容易学。它严格遵守一个相当固执己见的标准是一种痛苦。例如，如果您的代码中有未使用的依赖项，编译器将拒绝编译——还有许多其他的事情。这既挫败了开发人员，又迫使代码质量更好。

就我个人而言，我既讨厌又尊重编译器的这一部分，因为我在“开发模式”下进行实验时标准比较宽松，但同时我又非常尊重它，因为我在“生产模式”下遵循更严格的标准。

* * *

[![red dead redemption showdown](img/e467c333e499486bed0ee9e7e2c32410.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fyGMFq8M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sc9mu41tuatg0e6q6ne3.jpg)

# 那么为什么要走呢？

根据我们的目标，显然与我们无关。

最后的摊牌归结为`shell script`或`go.lang`

在内部，由于我们在基础设施中广泛使用 docker 和 linux，我们的大多数工程团队都有 shell 脚本经验。

这让我们有信心让 shell 在 ubuntu 和 macosx 上运行。

然而，我们不确定它是否能在 windows、alpine、debian、arcOS 等平台上运行良好...

当时的总体计划是将 go.lang(我们对此表示怀疑)作为备用方案，并投入到 shell 脚本中——修复特定客户(9%)出现的任何问题。

然而，当我们“被迫”投入一个小的黑客项目(解决一个主要的客户问题)时，事情发生了变化

[![picocreator image](img/9d01ba033264443700b6c4fc664da969.png)](/picocreator) [## 堆栈:在 14 小时内创建一个免费的开源一次性电子邮件服务(inboxkitten.com)😼

### 尤金 Cheah 9 月 22 日 184 分钟阅读

#opensource #serverless #javascript #vue](/uilicious/the-stack-making-a-free-open-source-disposable-email-service-prototype-inboxkittencom-in-14-hours-206g)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [尤利西斯](https://github.com/uilicious) / [收件箱小猫](https://github.com/uilicious/inboxkitten)

### 由无服务器 mailgun 小猫支持的一次性电子邮件收件箱

<article class="markdown-body entry-content container-lg" itemprop="text">

[![inboxkitten header](img/e9a33a3e5d054e09c5cddb366ec34608.png)](https://inboxkitten.com)

# 开源的一次性电子邮件——由无服务器的小猫提供服务

[![Build Status](img/c31dc0d13130932ff2cc28a3b9759b63.png)](https://travis-ci.org/uilicious/inboxkitten)

Inboxkitten 是一个开源的一次性电子邮件服务，您可以自行自由部署和使用！

访问[我们的网站](https://inboxkitten.com),或者...

# Docker 部署指南

这很简单——使用我们预先构建的 docker 容器。

请注意，您需要首先[设置您的 mailgun 帐户](https://raw.githubusercontent.com/uilicious/inboxkitten/master/#setup-mailgun)

```
# PS: you should modify this for your use case
docker run \
    -e MAILGUN_EMAIL_DOMAIN="<email-domain>" \
    -e MAILGUN_API_KEY="<api-key>" \
    -e WEBSITE_DOMAIN="localhost:8000" \
    -p 8000:8000 \
    uilicious/inboxkitten 
```

前往 8000 端口——为了你的收件箱

# 其他部署选项

*   [无服务器部署指南(适用于 cloudflare/firebase)](https://raw.githubusercontent.com/uilicious/inboxkitten/master/./DEPLOY-GUIDE-SERVERLESS.md)
*   [本地主机/定制部署/配置指南](https://raw.githubusercontent.com/uilicious/inboxkitten/master/./DEPLOY-GUIDE-LOCALHOST)

# 支持我们寻找产品<g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">🚀</g-emoji>

*   [https://www.producthunt.com/posts/inboxkitten](https://www.producthunt.com/posts/inboxkitten)

# 有些相关的博客/文章

*   [堆栈:在 14 小时内制作一个免费的开源一次性电子邮件服务原型(inboxkitten.com)](https://dev.to/picocreator/the-stack-making-a-free-open-source-disposable-email-service-prototype-inboxkittencom-in-14-hours-206g)
*   [我从 14 个小时的项目中学到了什么](https://dev.to/jmtiong/what-i-have-learnt-from-a-14-hours-project-2joo)
*   [开发时间表](https://blog.uilicious.com/development-timeline-for-inboxkitten-com-lessons-learnt-e802a2f0a47c)

# 其他参考文献

*   [编码指南](https://raw.githubusercontent.com/uilicious/inboxkitten/master/./CODE-GUIDE.md)

# 寻找赞助商

注意…

</article>

[View on GitHub](https://github.com/uilicious/inboxkitten)

在那个 14 小时的项目中，我们决定利用这个机会，在一个小的独立项目中尝试 go.lang CLI。

事实证明，这可以相对容易地完成(在学习曲线之后)。就这样，我们做出了一个决定...去吧，朗...

从表面上看，经过多次测试，它对我们来说效果很好！(手指交叉，因为它在我们的用户中影响生产使用)

> 题外话，我个人会走这条路。我在一个编程洞穴里藏了一个星期。并针对每个平台的所有限制编写实用程序脚本。
> 
> 然而，直到团队的规模和经验变得更大，这将被搁置。所以也许明年？(我不知道)

* * *

# 听起来不错，但是 uilicious 用命令行界面做什么呢？

我们运行这样的测试脚本...

```
// Lets go to dev.to
I.goTo("https://dev.to")

// Fill up search
I.fill("Search", "uilicious")
I.pressEnter()

// I should see myself or my co-founder
I.see("Shi Ling")
I.see("Eugene Cheah") 
```

并生成这样的[可共享的测试结果](https://snippet.uilicious.com/test/public/1cUHCW368zsHrByzHCkzLE)...

[![Uilicious Snippet dev.to test](img/ddd4b0b6d1cbb7ddda303d99a1b421fa.png)](https://snippet.uilicious.com/test/public/1cUHCW368zsHrByzHCkzLE)

现在可以通过命令行执行了

[![Uilicious commandline](img/be723f45f74a7ee02aed015d40b162d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bVlA1oGb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rqqimngz61hh00j1h2zl.png)

* * *

# 还有一点，go lang 地鼠很可爱

<figure>

[![cute go gophers](img/680cf34c0775d72a7a406b5de969f36b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YZtX8Qp3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7iquutmhliskryig6ue6.png)

<figcaption>Credit : https://github.com/tenntenn/gopher-stickers</figcaption>

</figure>

* * *

# 快乐航运🖖🏼🚀**
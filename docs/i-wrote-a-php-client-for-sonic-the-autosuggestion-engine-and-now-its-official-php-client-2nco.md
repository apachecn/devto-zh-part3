# 我为自动建议引擎 sonic 编写了一个 PHP 客户端，现在是它的官方 PHP 客户端。问我任何事情🤩

> 原文：<https://dev.to/ppshobi/i-wrote-a-php-client-for-sonic-the-autosuggestion-engine-and-now-its-official-php-client-2nco>

嘿，伙计们，上周我看到一篇关于新搜索引擎的文章，类似于弹性搜索。但是是专门为自我暗示设计的。[Sonic](https://github.com/valeriansaliou/sonic)——[crip . chat](https://crisp.chat)的团队在 Rust lang 上构建了一个基于索引的自动建议引擎。客户参与平台。

然后我去了他们的仓库(是的，它是开源的)。并且发现它提供了一个 TCP 端点而不是 HTTP 端点/。这意味着为了使用 sonic search，您需要连接到 TCP 套接字，通过套接字发出命令并读取输出缓冲区。他们正式为 sonic 提供了 javascript 客户端，但没有为 PHP 提供。然后我突然想到...如果我为它构建一个 PHP 客户端，这对我可能是一个很好的体验，尤其是因为我以前没有做过任何套接字编程。

然后我花了几天几夜编写 PHP 客户端，并向官方回购提交了一份 PR，

# [![](img/375dfcc32199b4dedf2b526645c27ff7.png) 又多了一个 php 客户端——psonic by PPS hobi# 118](https://github.com/valeriansaliou/sonic/pull/118)

[![ppshobi avatar](img/14ca6a69ed9a10564303fcffca28f810.png) ](https://github.com/ppshobi)  **[ppshobi](https://github.com/ppshobi)** commented on [Apr 06, 2019](https://github.com/valeriansaliou/sonic/pull/118)

嗨，我已经为 sonic 实现了一个 PHP 客户端，包括单元测试和一个相对完整的[自述文件](https://github.com/ppshobi/psonic/blob/master/Readme.md)以及一个 [api 文档](https://github.com/ppshobi/psonic/blob/master/api-docs.md)。

[View on GitHub](https://github.com/valeriansaliou/sonic/pull/118)

说请把我的客户也包括在自述文件中。然后我也在推特上发了同样的消息。

> ![](img/966ad23e2f2fa315ae9300cb11afae47.png)Shobi@ PPS hobi![](img/4d9c44713c216584b3d48ff3455cbb68.png)刚刚发布了一个针对 Sonic 的 php 客户端，这是一个由 [@valeriansaliou](https://twitter.com/valeriansaliou) 和团队在 [@crisp_im](https://twitter.com/crisp_im) 构建的超高速自动建议引擎，请查看这里。[github.com/ppshobi/psonic](https://t.co/TS8APYET4q)2019 年 04 月 07 日上午 10:21[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1114835655450718208)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1114835655450718208)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1114835655450718208)7

啊哈...就在那里。crisp 的 CTO 和 Sonic 的核心贡献者，【Valerian Saliou 先生回复我说，他喜欢我编写代码的方式，如果我提出一个相对完整的解决方案，他可以将我的客户端推广为官方支持的 PHP Sonic 客户端。

那么...再过几个晚上，瞧。现在 **Psonic** 是 sonic 的官方 php 客户端。看看吧，在评论里给你有价值的意见，还有 GitHub 星😍。

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)[PPS hobi](https://github.com/ppshobi)/[pson IC](https://github.com/ppshobi/psonic)

### Sonic 是一个超快速的自动建议引擎，由客户参与平台 crisp.chat 的团队构建。它内置在 Rust 中，官方支持 javascript 客户端，但如果你想通过 php 使用 sonic，这是你可以寻找的库。完全经过单元测试，符合现代编码标准，并提供一个干净的 api 来与 sonic 交互。

<article class="markdown-body entry-content p-5" itemprop="text">

# 声波自动建议引擎的 PHP 客户端

[![Build Status](img/c6ae7ff4da2edb0210286dcf6aa51951.png)](https://travis-ci.com/ppshobi/psonic)[![Scrutinizer Code Quality](img/1d9d14bc57446d6a1744dca2484b3aca.png)](https://scrutinizer-ci.com/g/ppshobi/psonic/?branch=master)[![codecov](img/5bff842d507aa5d7ea3466534817c53e.png)](https://codecov.io/gh/ppshobi/psonic)

## 摘要

Sonic 是一个超快速的自动建议引擎，由客户参与平台 [crisp.chat](https://raw.githubusercontent.com/ppshobi/psonic/master/Readme.md/crisp.chat) 的团队打造。它内置于 Rust 中，官方支持 javascript 客户端，但如果你想通过 PHP 使用 sonic，这是一个完全经过单元测试的库，符合现代编码标准，并提供了一个干净的 API 来与 sonic 交互。

## 安装和使用

您需要一个正在运行的 sonic 实例(本地或云中，端口 1491 应该是可访问的)php7+和 composer 来使这个库工作。[阅读更多关于安装 sonic 的信息](https://github.com/valeriansaliou/sonic/blob/master/README.md)

*   转到您的项目目录
*   执行`composer require ppshobi/psonic`一旦安装完成，你应该能够如下使用该库

## api 文档

[完整的 API 文件](https://raw.githubusercontent.com/ppshobi/psonic/master/Readme.md/api-docs.md)

## 使用

一旦 psonic 就绪，您就可以访问`Client` …

</article>

[View on GitHub](https://github.com/ppshobi/psonic)

去吧，也试试 sonic。这是一个非常酷的产品。😍😍😍
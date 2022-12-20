# 增强的打字游戏场

> 原文：<https://dev.to/nickytonline/an-enhanced-typescript-playground-49j6>

今天晚上早些时候，当我浏览我的 Twitter feed 时，我关注的一个人的推文弹出来了。

> ![Steven profile image](img/30c6812a5a70a483f8c0b43e2d9000b3.png)史蒂文[@ styfle](https://dev.to/styfle)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ drosen wasser](https://twitter.com/drosenwasser)[@ gianlucaguarini](https://twitter.com/gianlucaguarini)只要在这里更新你的书签到更好的游乐场😉[typescript-play.js.org](https://t.co/STFVHK8lOQ)2019 年 2 月 26 日上午 00:39[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1100193610631397376)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1100193610631397376)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1100193610631397376)

我想，嘿，这看起来很酷。所以我问史蒂文是不是他创造的，他回答说。

> ![Steven profile image](img/30c6812a5a70a483f8c0b43e2d9000b3.png)史蒂文[@ styfle](https://dev.to/styfle)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ nickytonline](https://twitter.com/nickytonline)[@ drosen wasser](https://twitter.com/drosenwasser)[@ gianlucaguarini](https://twitter.com/gianlucaguarini)都不是！它是由代理库珀前一段时间创建的，并提出替换 TS 团队中愿意的官方之一[github.com/agentcooper/ty…](https://t.co/nJPXQmCb7Q)2019 年 2 月 26 日上午 00:54[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1100197435832446976)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1100197435832446976)

简而言之，typescript-play 就像官方的 typescript 游乐场，但有一些很大的改进:

*   默认情况下，所有严格选项都是打开的
*   更多可用的编译器选项
*   切换类型脚本版本的能力
*   更多代码空间
*   更多示例
*   更快的共享，URL 随着您的输入而更新
*   较短的共享 URL

更酷的是它是开源的，所以你可以在本地部署它。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ agentcooper ](https://github.com/agentcooper) / [打字稿-播放](https://github.com/agentcooper/typescript-play)

### 更好的打字操场

<article class="markdown-body entry-content container-lg" itemprop="text">

<g-emoji class="g-emoji" alias="warning" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a0.png">⚠️</g-emoji> **请使用[官方打字游戏场](http://www.typescriptlang.org/play/index.html)，现在它拥有所有相同的功能，甚至更多。使用[https://github.com/microsoft/TypeScript-Website/issues](https://github.com/microsoft/TypeScript-Website/issues)解决问题。**

* * *

# [打字操场](https://typescript-play.js.org)

一个更好的打字操场。

网址:[https://typescript-play.js.org](https://typescript-play.js.org)。

与 https://www.typescriptlang.org/play 的不同之处:

*   默认情况下，所有严格选项都是打开的
*   更多可用的编译器选项
*   切换类型脚本版本的能力
*   更多代码空间
*   更多示例
*   更快的共享，URL 随着您的输入而更新
*   较短的共享 URL

## 入门指南

```
npm install
npm run setup
npm start 
```

## 更新类型脚本

游乐场靠 [UNPKG](https://unpkg.com) 取`monaco-editor`(包含`typescript`到 [`monaco-typescript`](https://github.com/Microsoft/monaco-typescript) 包)。

如果`monaco-editor`没有更新到最新的 typescript，最新的版本可以使用`npm run get-typescript latest`构建并在本地提供。如果您遇到错误，最新的 monaco 版本可能与最新的 TypeScript 版本不兼容，在这种情况下，您需要在上游更新 monaco-typescript，或者在本地应用补丁(参见…

</article>

[View on GitHub](https://github.com/agentcooper/typescript-play)

[![Kanye West Awesome from giphy](img/af39890b1e16fcabedcee1f0624f8545.png)](https://i.giphy.com/media/5fMlYckytHM4g/giphy-downsized.gif)

如果你不想克隆它，可以去 typescript-play.js.org 看看。

编码快乐！

[豪尔赫·冈萨雷斯](https://unsplash.com/photos/XGG12wqCkk8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/playground?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照
# 通过预取加快下一页导航

> 原文：<https://dev.to/addyosmani/speed-up-next-page-navigations-with-prefetching-4285>

## 什么是预取？

通过获取用户可能提前访问的下一个页面(或下一个页面的一组资源),加载网页*会变得更快。我们称之为预取。在某些情况下，这可能会使后续导航看起来像是立即加载的。*

[![](img/74a8561b426fb103e6e9156a4b8e002f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9kBsi51H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sf32qj3fc4dof4dfgyuh.gif)

现代预取可以通过多种方式实现。最常见的是使用浏览器的 [`<link rel=prefetch>`](https://w3c.github.io/resource-hints/#prefetch) [资源提示](https://w3c.github.io/resource-hints/)，它通过 HTML 以声明的方式工作..

```
<!-- HTML -->
<link rel="prefetch" href="/pages/next-page.html">
<link rel="prefetch" href="/js/chat-widget.js"> 
```

或者 HTTP 头:

```
Link: </js/chat-widget.js>; rel=prefetch 
```

Webpack 也支持预取(参见 Webpack 中的[预取/预加载)。](https://medium.com/webpack/link-rel-prefetch-preload-in-webpack-51a52358f84c)

```
<!-- Without prefetching -->
import("ChatWidget");

<!-- With prefetching -->
import(/* webpackPrefetch: true */ "ChatWidget"); 
```

一旦父块完成加载，Webpack 的运行时将在页面中注入正确的预取语句。以上案例中，`<link rel="prefetch" href="chat-widget.js">`。

据[can use](http://caniuse.com/prefetch),`<link rel=prefetch>`有不错的跨浏览器支持。

[![](img/43db51d32acb79239e14efda173fdf7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g2Z2xw7Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j1740v1gh4pg0739glwe.jpg)

如果用`<link rel=prefetch>`预取 Chrome 中的一个资源，它将被以低优先级获取，而[将保持](https://chromium-review.googlesource.com/c/chromium/src/+/1343101)大约 5 分钟。这种情况会持续到资源被消耗完，此时将应用正常的资源缓存控制规则。

[![](img/9ac7f27b7d732686b7a7d8f072c16b1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qcmr8PKY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/59fdhm705ow7tz3zdb21.jpg)

不确定站点上哪些页面值得考虑预取？查看我们的实验性[助手工具](https://predictjs.firebaseapp.com/)以获得建议的“下一页排行榜”(基于您的分析):

[![](img/a47753db18d68458e63d9cb743f847c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9awK1w5R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g53o9k14k6o3dxlpvdv1.jpg)

“预取”也可以主要通过使用[服务人员](https://developers.google.com/web/fundamentals/primers/service-workers/)【参见[注册期间预取](https://googlechrome.github.io/samples/service-worker/prefetch/)/fetch()或 XHR 来完成。然而，请注意，并非所有这些机制都具有与`<link rel=prefetch>`相同的“低优先级”获取行为。

## 今天谁在用预取？

您可能听说过在生产中使用预取的几个品牌:

*   [网飞](https://medium.com/dev-channel/a-netflix-web-performance-case-study-c0bcde26a9d9)提前预取后续导航所需的 JavaScript 包
*   [IndieGogo](https://www.indiegogo.com/) 预取 Stripe 的 JavaScript 库，用于未来页面上的信用卡处理
*   Craigslist 预取搜索结果页面的 JS 包
*   [Heineken](https://www.heineken.com/us/agegateway?returnurl=%2f) 预取出生日期验证页面后可能需要的 JS 和 CSS 包页面。

## 预取有哪些挑战？

当用户使用受限的网络连接或有限的数据套餐时，每一个字节都很重要。虽然对于 WiFi 连接良好的用户来说，提前预取一些页面是有意义的，但对于那些没有连接的用户来说，就需要小心了。你不想浪费任何人的数据计划。

您可以使用[navigator . connection . effective type](https://dev.to/addyosmani/adaptive-serving-using-javascript-and-the-network-information-api-331p)API(NetInfo 的一部分)仅在用户处于有效 4G 连接时预取页面。

关于预取还有一点需要注意，如果在导航启动时请求还没有完成，正在进行的预取请求就会被取消。这可能意味着浪费字节，所以只要确保用户在使用该功能时可能实际看到从这些获取的值。

## 案例分析

当日本出版商 [Nikkei](https://r.nikkei.com/) 确信用户会导航到特定页面时，他们不会等待导航发生。

他们利用`<link rel=prefetch>`在用户点击链接之前预取下一页。下面我们可以看到等待网络+服务器开销传递一个页面的影响(总共花费~880ms)。相比之下，预取几乎没有请求开销，并且可以立即进行页面导航(大约 37 毫秒)。

[![](img/83c3e1b847ab549ffab3feed7ee528c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6BQurxoB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8757kn47fefne6un26cm.jpg)

关于日经指数的更多信息可以在最近的[案例研究](https://developers.google.com/web/showcase/2018/nikkei)中找到。

## 什么是预测预取？

使用预取的网站通常对用户将要访问的顶级页面或资源有很好的了解，并可以手动为它们添加`<link rel=prefetch>`标签。然而，对于大规模的大型站点来说，这种手动过程会变得更加难以实现。一旦你深入到几个层次，很难知道什么是“最好的”下一页，也不知道如何在没有手动过程的情况下将它连接起来。

预测性预取使用额外的数据(例如分析)来预测用户在给定网站上的任何 URL 的情况下可能会访问哪些文档。然后，这些数据可以用来`<link rel=prefetch>`那些页面，改善后续页面加载时间。可以使用机器学习来可选地改进预测，以确保仅提前获取被访问的具有最高变化的页面。

虽然这里没有具体的内容，但是 Google Analytics 有一个[报告 API](https://developers.google.com/analytics/devguides/reporting/core/v4/) ,提供了足够的信息来为网站建立预测预取。Minko Gechev、Katie Hempenius 和我最近利用这一点构建了一个名为 [Guess.js](https://guess-js.github.io/) 的项目，该项目提供了以下解决方案:1)预测性地预取相关页面，2)预测性地捆绑和预取这些页面可能需要的正确 JavaScript 资源(通过 webpack)。

## 在哪里可以了解更多关于预测预取的信息？

里克·维斯科米与凯蒂·亨佩纽斯和我深入探讨了这个话题，以防有兴趣了解更多信息:

[https://www.youtube.com/embed/03qta1Ufn3c](https://www.youtube.com/embed/03qta1Ufn3c)

我们将继续探索发展网络预取的机会。最近，我们探索了使用[签名交换](https://developers.google.com/web/updates/2018/11/signed-exchanges)的隐私保护预取，这是新兴的 [web 打包](https://github.com/WICG/webpackage)规范的子集。

PS:请留意 [quicklink](https://github.com/GoogleChromeLabs/quicklink) -我正在发布的一个< 1KB 库，旨在通过在空闲时间预取视窗内链接来实现更快的下一页导航:)

*感谢 Yoav Weiss 对`<link rel=prefetch>`如何在 Chrome* 中幕后工作的帮助
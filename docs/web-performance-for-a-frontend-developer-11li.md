# 前端开发人员的 Web 性能

> 原文：<https://dev.to/mohandere/web-performance-for-a-frontend-developer-11li>

对于 Web 开发人员来说，[性能](https://developers.google.com/web/fundamentals/performance/why-performance-matters/)现在不是可选的。业绩在任何在线风险投资的成功中扮演着重要角色。[谷歌的 DoubleClick](https://www.doubleclickbygoogle.com/articles/mobile-speed-matters/) 发现，如果网页加载时间超过 3 秒，53%的移动网站访问就会被放弃。

亚马逊和其他人发现，100 毫秒的延迟对 1%的销售额负责。Flipkart 通过渐进式网络应用将现场停留时间增加了三倍，再次参与率提高了 40%。

当 Pinterest 将感知等待时间减少 40%时，搜索引擎流量和注册人数增加了 15%，还有更多这样的例子。

### 这个为什么？

[为什么性能很重要](https://developers.google.com/web/fundamentals/performance/why-performance-matters/)

## 提高性能

在这篇文章中，我记下了一些[加载性能](https://developers.google.com/web/fundamentals/performance/get-started/)技术，这些技术有助于网络开发者在构建网站/网络应用时考虑。对于渲染性能，请跟随[这个](https://developers.google.com/web/fundamentals/performance/rendering/)链接。

让我们开始吧。

### 设置绩效预算

> 绩效预算是团队不允许超出的页数限制。它可以是最大的 JavaScript 包大小、总图像重量、特定的加载时间(例如，在 3G/4G 上不到 5 秒的交互时间)或任何数量的其他指标的阈值。- [@addyosmani](https://dev.to/addyosmani)

FCP(第一个有内容的画家)和 TTI(互动时间)是至关重要的。

请点击以下链接了解如何设定绩效预算。

*   [开始绩效预算](https://addyosmani.com/blog/performance-budgets/)由 [@addyosmani](https://mobile.twitter.com/addyosmani)
*   [设置和计算 Web 性能预算](https://www.keycdn.com/blog/web-performance-budget#setting-a-web-performance-budget)

现在让我们来看看 web 开发人员的三大性能要素

### 1。Java Script 语言

> JavaScript 是最昂贵的资源，参见[2018 年 JavaScript 的成本](https://medium.com/@addyosmani/the-cost-of-javascript-in-2018-7d8950fbb5d4)作者 [@addyosmani](https://mobile.twitter.com/addyosmani)

策略有效地传递 JavaScript

*   考虑实施[代码分割](https://webpack.js.org/guides/code-splitting/)
*   [整理您的 JavaScript 包](https://nolanlawson.com/2018/03/20/smaller-lodash-bundles-with-webpack-and-babel/)
*   [通过预加载、预连接和预取确定资源优先级](https://developers.google.com/web/fundamentals/performance/resource-prioritization)
*   [通过树抖动减少 JavaScript 负载](https://developers.google.com/web/fundamentals/performance/optimizing-javascript/tree-shaking/)
*   [接受绩效预算](https://infrequently.org/2017/10/can-you-afford-it-real-world-web-performance-budgets/)

### 2。形象

> 图像通常占网页上下载字节的大部分。

为了有效地传递图像，应考虑以下 4 点

*   合适的图像格式
*   合适的压缩方法
*   根据视窗/设备(图片元素)适合显示尺寸和密度
*   仅加载必要的-惰性加载

请浏览以下指南了解详细信息。

*   [images.guide](https://images.guide/)
*   [图像优化](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/image-optimization)
*   [SVG 将拯救我们](https://svgontheweb.com/)
*   [懒加载](https://developers.google.com/web/fundamentals/performance/lazy-loading-guidance/images-and-video/) - [原生图像懒加载](https://addyosmani.com/blog/lazy-loading/)为 web 而来。

本机映像延迟加载示例-

```
<img src="celebration.jpg" loading="lazy" alt="..." />
<iframe src="video-player.html" loading="lazy"></iframe> 
```

Enter fullscreen mode Exit fullscreen mode

来自谷歌的注释-

像 JPEG 2000、JPEG XR 和 WebP 这样的图像格式通常比 PNG 或 JPEG 提供更好的压缩，这导致更快的下载和更少的数据消耗。

### 3\. Fonts

> 最小化字体闪烁

关于`font-display`属性和字体性能的详细说明，请浏览以下链接。

*   [用字体显示控制字体性能](https://developers.google.com/web/updates/2016/02/font-display)
*   [`font-display`面向大众](https://css-tricks.com/font-display-masses/)

让我们也涵盖其他方法。

### 4。将您的网站/应用程序转换为 [PWAs](https://developers.google.com/web/fundamentals/codelabs/your-first-pwapp/)

> 渐进式网络应用程序是用户体验的延伸，可靠、快速、引人入胜。-谷歌网络

您可以部分采用 PWA，利用服务人员的优势来缓存一些资源，如字体、图像、样式、脚本等。然后转向其他方法来构建完整的 PWA。

有了 PWA，我们可以构建可靠、快速且引人入胜的网站/网络应用。[服务人员](https://developers.google.com/web/fundamentals/primers/service-workers/)支持渐进式网络应用即时加载，无论网络状态如何。服务工作者就像客户端代理，允许控制缓存以及如何响应资源请求。

阅读[离线食谱](https://developers.google.com/web/fundamentals/instant-and-offline/offline-cookbook/)了解更多关于网络和服务工作者的离线体验。

**Web 开发人员的 5 大服务人员必备要素**

[这里](https://www.youtube.com/watch?v=IBpQlNeq5-o&index=3&list=PLNYkxOF6rcIDjlCx1PcphPpmf43aKOAdF&t=0s)是 2018 年 Chrome Dev Summit 的 Youtube 视频。

### 5。最佳建筑和渲染技术

*   [在网络上渲染](https://developers.google.com/web/updates/2019/02/rendering-on-the-web)
*   [PRPL 模式](https://developers.google.com/web/fundamentals/performance/prpl-pattern/)
*   [设计您的应用程序外壳](https://codelabs.developers.google.com/codelabs/your-first-pwapp/#2)

### 6。一些其他的技术

*   [HTTP 缓存](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching)
*   [优化编码](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/optimize-encoding-and-transfer)

## 工装

*   [Chrome 开发工具——审计、性能、内存、JavaScript 分析器、覆盖率、性能监视器、网络](https://developers.google.com/web/tools/chrome-devtools/)
*   [灯塔](https://developers.google.com/web/tools/lighthouse/)
*   [网页测试](https://www.webpagetest.org/)
*   页面速度

以上是大多数使用它的人最信任的工具/服务。

你可以在 Twitter 上关注 Addy Osmani 的最新动态。他是谷歌负责 Chrome 的工程经理。

此外，我想提一下 [Umar Hansa](https://mobile.twitter.com/umaar) 为收集和分享伟大的[开发工具提示](https://umaar.com/dev-tips/)所做的出色工作。

希望这对你的旅途有帮助。如果你喜欢，请不要忘记点击喜欢按钮！

莫汉
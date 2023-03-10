# 延迟加载，更快的网页，搜索引擎友好

> 原文：<https://dev.to/borisschapira/lazy-loading-faster-webpages-seo-friendly-11ge>

图片是你网站的关键内容吗？如果你对你的网站性能很认真，你可能已经对它们进行了优化。但是你考虑过延迟加载你的图片吗？延迟加载图像通过首先为重要内容节省带宽来改善用户体验。

一些人出于搜索引擎优化的考虑拒绝了这项技术。但是适当地延迟加载图像并不妨碍它们被编入索引。让我们来看看为什么你应该实现延迟加载，以及如何确保你以一种 SEO 友好的方式来构建它。

网络上到处都是图片。在正确的背景下提供正确的形象确实很有挑战性。[图像需要优化](https://blog.dareboost.com/en/2017/10/optimize-images-to-reduce-page-weight-file-formats-tools-and-rwd/)，适应其渲染区域，并且仅在需要时加载。

**图片延迟加载**是一组旨在满足最后一个需求的技术。只有对访问者有用的图片才会被加载:图片的下载在需要的时候被触发:在特定的交互之后，作为滚动的结果…或者永远不会。

[根据 HTTPArchive](https://httparchive.org/reports/state-of-images#offscreenImages) 的说法，通过延迟加载屏幕外和隐藏图像，每页可以节省 420 KB(中值)。

[![](img/ac868e1e1ec97a1fe9a41fa960cf6b35.png)](https://blog.dareboost.com/wp-content/uploads/2019/03/offscreen-images.png)

## 一些用例

有很多具体的例子涉及到了延迟加载。让我们花几分钟时间举几个例子。

**电子商务搜索结果**。这些页面通常包含如此多的结果，以至于许多产品都在视图之外。下载他们的图片有用吗？使用延迟加载，您可以延迟它们的加载，直到页面滚动。

**旋转木马**。你知道他们，对吧？为什么用户要加载所有的图像，而它只会在几秒钟内显示第一个…懒加载它们！加载第一个，并在转盘动画前几秒在后台触发下一个的下载。

菜单和标签。菜单或标签中包含的图像通常是隐藏的，只有在点击后才会显示。使用延迟加载，您可以延迟图像的加载，直到显示元素的按钮被悬停或单击。

根据页面的性质，其他情况也是可能的。在没有事先问自己延迟下载的问题之前，不应该在你的页面上包含任何图像。因为最终会有显著的性能提升。

## 光秃秃的必需品

通过推迟加载不是立即需要的图像，或者在某些情况下，根本不加载图像，浏览器节省了资源(带宽和 CPU)。页面加载速度更快，用户体验得到改善，因为节省的资源可以用于更重要的内容。还有服务器端的收益。交付资源所需的 CPU 和带宽承受的压力较小，从而降低了托管和/或 CDN 成本。

正如介绍中所说，延迟加载并不会阻止你的图片被搜索引擎索引。但是这可能很棘手，并且所有的技术并不相同，因为市场上的各种库和插件并没有实现所有的最佳实践。为了保护您的有机 SEO，您需要了解惰性加载是如何工作的，以及如何安全地使用它。

## 带 JavaScript 的 LazyLoad

在理想情况下，浏览器会自动延迟加载图像。这可能是未来的情况，因为一个 [Lazyload 特性策略](https://github.com/w3c/webappsec-feature-policy/issues/193)正在研究中，但我们还没有到那一步。与此同时，我们必须探索其他解决方案，每一个都意味着修改 HTML 标记并添加 JavaScript (JS)依赖。

***重要的***
*你不需要——而**不应该**——偷懒加载你所有的图片。如果你知道有些图像大部分时间都会显示，那就让它们正常加载渲染。延迟加载仅推荐用于其他图像。不要在不必要的时候增加复杂性！*

最传统的解决方案是使用 JS 定期对每个图像(或每个显示图像作为背景的元素)重新评估是否需要加载。像 [lazysizes](https://github.com/aFarkas/lazysizes) 这样的高性能库就是建立在这种范式之上的，并且与旧的浏览器兼容。lazysizes 甚至可以检测用户代理是否能够滚动，如果不能，就立即加载图像。

然而，如此定期地运行这些测试是有成本的。对于支持交集和变异观察器 API 的 web 浏览器来说，可以避免这种成本(它提供了对特定 DOM 情况做出反应的能力，而不必持续测试)。 [yall.js](https://github.com/malchata/yall.js) 和 [lozad.js](https://apoorv.pro/lozad.js/) 是这种基于条件的技术的很好的例子(它们只与现代浏览器兼容[，但是你可以使用](https://caniuse.com/#feat=intersectionobserver)[一个 polyfill](https://github.com/w3c/IntersectionObserver/tree/master/polyfill) 来模拟旧浏览器缺少的特性)。

***提示***
*由于浏览器可能会将`src`的任何省略解释为错误，我强烈建议您仍然定义`src`属性值，使用一个非常小的透明图像占位符，甚至是内嵌的，比如:*

```
data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw== 
```

Enter fullscreen mode Exit fullscreen mode

无论您在客户端选择哪种 JavaScript 解决方案，都必须修改后端以返回正确的 HTML 代码。图像延迟加载的 HTML 标记总是相似的:`src`属性的最终值临时放在一个`data-src`属性中，这个属性防止`<img>`元素立即加载最终图像。

```
<!-- Lazy loaded image: before -->
<img data-src="garden.jpg" src="placeholder.jpg" class="js-lazyload" src="dat…" />

<!-- Lazy loaded background image: before -->
<div data-class="bg-image-garden" class="container js-lazyload">…</div> 
```

Enter fullscreen mode Exit fullscreen mode

这个库通过一个属性来标识它的目标图像，通常使用一个特定的类(例如，在这里，我们使用的是`js-lazyload`类)。当库检测到该加载图像时，`data-src`属性用于填充`src`属性，浏览器开始加载图像。该技术同样适用于其他媒体类型(视频、iframes)或适用于响应式 web 设计的图像(例如，`data-srcset`属性可以用来暂时保留`srcset`属性的最终值)。

```
<!-- Lazy loaded image: after -->
<img src="garden.jpg" class="js-lazyload" … />

<!-- Lazy loaded background image: after -->
<div class="container js-lazyload bg-image-garden">…</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 没有 JavaScript？用 style 回退(和`<noscript>`)。

搜索引擎机器人，如谷歌机器人，是浏览网页的目的是内容索引的爬虫。没有人确切知道谷歌机器人什么时候会在它们的页面上执行 JavaScript，以什么样的能力执行。斯蒂芬·博耶在 2016 年演示了[，随后](https://www.stephanboyer.com/post/122/does-google-execute-javascript)[汤姆·格林纳维在谷歌 IO 2018](https://youtu.be/PFwUbgvpdaQ?t=845) 上证实，谷歌机器人在第一次访问时不会执行 JavaScript。最终的渲染实际上会在几天后到达。

> 因为 Googlebots 实际上对网络内容进行了两波索引，所以一些细节可能会被遗漏。汤姆·格里维

像一些图像。

因为如果 JavaScript 正确执行，它将不会处理延迟加载的图像，并且不会填充`src`属性，从而阻止浏览器开始下载和显示图像。

如何在没有 JavaScript 的情况下提供这些图片？您可以使用一个特定的 HTML 元素:`<noscript>`。只有当脚本特性不被支持或被禁用时，才会显示`<noscript>`元素的内容。这正是我们所需要的！

因此，如果您的 HTML 响应包含:

```
<img data-src="garden.jpg" src="dat…" alt="A spacious garden dominated by a large pine tree." class="lazyload" />
<noscript>
    <img src="garden.jpg"  alt="A spacious garden dominated by a large pine tree."… />
</noscript> 
```

Enter fullscreen mode Exit fullscreen mode

然后:

*   JavaScript 可用时，只会显示第一个`<img>`元素；
*   当 JavaScript 不可用时，在页面显示期间会考虑两个`<img>`元素。第二个(在`<noscript>`元素中)，被搜索引擎显示和索引。谷歌网站管理员趋势分析师约翰·穆勒证实，这项技术[对背景图片完全一样:](https://www.youtube.com/watch?time_continue=4308&v=7m-cd8XXovQ)

```
<div data-class="bg-image-garden" class="container js-lazyload">…</div>
<noscript>
    <div class="container bg-image-garden">
        … A spacious garden dominated by a large pine tree. …
    </div>
</noscript> 
```

Enter fullscreen mode Exit fullscreen mode

注意:不要错过添加 JavaScript 不可用时应用的特定样式，隐藏非延迟加载的图像。

您可能害怕与代码重复相关的冗长 HTML。不要担心，你的 HTML 文档在网络上发送之前必须用 gzip 或 brotli 压缩，因此对传输大小的影响很小(如果你不确定你的网站是否正确使用了压缩，[在 Dareboost](https://www.dareboost.com/) 上审计你的页面)。

当然，输入所有这些标签确实很繁琐。当管理响应图像，艺术指导…图像的标记可以非常，非常复杂。所以无论如何，你不应该手动输入这些 HTML 代码:自动处理这种复杂性应该是你的 CMS 的工作，或者是你的贡献工作流的任务。

[![](img/66565bcf4d450ad047b6c52aeffe0ddb.png)](https://blog.dareboost.com/wp-content/uploads/2019/03/guardians_rwd_image-1.png)

标记来自卫报的图像，甚至在实现延迟加载之前

## 测试实现

一旦你实现了延迟加载，你需要测试它的工作情况。最简单的解决方案当然是用 Dareboost 运行一个[完整的 web 性能诊断](https://www.dareboost.com/en/tool/website-analysis)来检测是否所有在视窗之外的图像都是惰性加载的。如果不是这样，我们会告诉你，并给你可以推迟加载的图片的网址。

如果您已经将图像加载到浏览器或自定义事件，请随意使用[您的报告的瀑布](https://www.dareboost.com/en/doc/analysis-report/timeline-waterfall)来检查图像的加载是否遵循了正确的顺序。在自定义事件的情况下，也可以随意使用[自定义计时](https://blog.dareboost.com/en/2018/05/custom-timings-monitoring/):您将能够在瀑布中找到事件。

它可能还需要做手工测试。在相关网页上打开浏览器，打开开发工具并选择网络选项卡(每个现代浏览器都有此功能)。清空网络堆栈，以便更好地可视化新调用，然后引发旨在启动延迟加载的交互。

下面是 Chrome 上的一个例子，在滚动期间激活了延迟加载:

[https://www.youtube.com/embed/Z4jRe3KRcf4](https://www.youtube.com/embed/Z4jRe3KRcf4)

您可以考虑通过使用基于浏览器的工具来执行测试，从而自动化这些测试。木偶师就是其中之一，[可以用来测试](https://github.com/GoogleChromeLabs/puppeteer-examples/blob/master/lazyimages_without_scroll_events.js)你在 Chrome 或 Chrome 上的惰性加载行为，以确保你真正的用户会看到你的图像。

为了验证爬虫机器人也能看到你的图像，你可以问谷歌自己，使用谷歌搜索控制台[的 URL 检查工具](https://search.google.com/search-console)。你会看到你的网页完全由谷歌获取。只需插入您的 Google bot URL 来测试您的页面，并截屏结果以确认您的`<noscript>`回退正在工作。

* * *

现在，你已经知道了关于图片延迟加载的一切，它对搜索引擎优化的影响，以及解决这个问题的最好方法。让我们修剪一些页面！
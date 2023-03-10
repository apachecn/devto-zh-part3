# 原生 HTML: Lazyloading

> 原文：<https://dev.to/link2twenty/native-html-lazyloading-3ai4>

这是一篇与普通帖子略有不同的帖子，通常我会谈论一些已经在浏览器中实现或者最近才添加到规范中的东西。今天我将会看到一些仍在等待合并到规范中的东西。

我希望这能让你更好地理解该规范是如何随着时间的推移而发展的，同时也能让你有信心提出自己的改进建议。whatwg 团队非常乐于听取新想法。

## W3 错误报告

我们的故事从 2012 年开始。BBC 网络团队的 Josh 将这个 bug 发布到了 w3 网站上。

> BBC 最近更新了他们的 BBC 新闻移动网站。他们为移动设备优化的方法之一是将图片的加载推迟到页面加载之后。
> 
> 我还没有详细研究这是如何做到的，但是他们在图像将要放置的位置使用 div 作为占位符。例如:
> 
> `<div class="delayed-image-load" data-src="http://static.bbci.co.uk/news/200/medimg/59388000/jpg/_59388680_59388679.jpg"></div>`
> 
> 页面加载后，DIV 被转换成一个 IMG 元素。
> 
> 很明显，这不太符合语义，如果 JavaScript 被禁用，它根本不会工作。但是他们这样做有一个很好的理由，这就是为什么我认为需要一种方法来推迟图像的加载，直到页面被解析之后。这也适用于其他嵌入式内容元素，如 IFRAME 和 OBJECT。
> 
> 我认为应该通过使用 DEFER 属性来实现这一点，它的工作方式与它在 SCRIPT 元素中的工作方式相同。

为了打破这一点，他们希望能够有一个带有`defer`属性的`<img>`标签，导致图像在站点为`ready`之前不会呈现。

```
<img src="https://via.placeholder.com/150" defer /> 
```

Enter fullscreen mode Exit fullscreen mode

这将导致图像直到第一次绘制后才加载，使网站看起来加载更快。

## GitHub 发行

几年后，这个话题变得乏味了，但在 2017 年，乔希又在 GitHub 上提起了这个话题。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 阻止下载图像或对象，直到它们在视窗中可见 #2806](https://github.com/whatwg/html/issues/2806) 

[![JoshTumath avatar](img/da6874f650f6a845a15229adab6e794c.png)](https://github.com/JoshTumath) **[JoshTumath](https://github.com/JoshTumath)** posted on [<time datetime="2017-07-01T17:23:34Z">Jul 01, 2017</time>](https://github.com/whatwg/html/issues/2806)

> See PR #3752

## 问题

许多网站非常注重图片，但并不是所有的图片都会被访问者浏览。尤其是在移动设备上，大多数访问者并不经常向下滚动；消耗的主要是页面顶部的内容。页面下方的大部分图片永远不会被看到，但它们还是会被下载。

这减缓了总页面加载时间，不必要地增加了一些访问者的移动数据费用，并增加了内存中的数据量。

### 示例变通方法

多年来，BBC 新闻团队一直使用以下方法来解决这个问题。页面顶部的主要图像通常使用一个`img`元素包含在 HTML 文档中。但是，任何其他图像都是通过脚本缓慢加载的。对于这些图像，它们作为占位符的`div`临时包含在 HTL 文档中。`div`是用 CSS 设计的，尺寸和加载的图像一样，背景是灰色的，上面有 BBC 的标志。

```
<div class="js-delayed-image-load"
     data-src="https://ichef.bbci.co.uk/news/304/cpsprodpb/26B1/production/_96750990_totenhosen_alamy976y.jpg"
     data-width="976" data-height="549"
     data-alt="Campino of the Toten Hosen"></div>
```

Enter fullscreen mode Exit fullscreen mode

最终，当它在视窗中可见时，脚本会用一个`img`元素替换它。

用脚本来做这件事并不理想，因为:

1.  如果访问者禁用了脚本，或者脚本加载失败，图像将永远不会出现
2.  我们事先不知道访问者的视窗的大小，所以我们必须随意地决定哪些图像要被懒惰地载入。在一篇新闻文章中，在小视窗中的浏览者最初只能看到新闻标志和文章的主角图像，但是在大视窗中最初能够看到许多其他图像(例如在侧边栏中)。但是为了移动设备，我们必须支持最小公分母。这给拥有大视窗的用户一种奇怪的体验，当他们加载页面时，占位符会出现一秒钟。
3.  我们必须等待脚本同步下载并执行，然后才能用图像替换任何占位符。

## 解

需要有一个本地方法让作者不用脚本就能做到这一点。

对此的一个解决方案是使用一个属性来声明哪些图像或对象在视窗中可见之前不应被下载和解码。比如`<img lazyload>`。*

或者，可以在`head`中放置一个`meta`元素，将所有图像和对象全局设置为仅在它们在视窗中可见时才下载。

**几年前在[资源优先级](https://w3c.github.io/web-performance/specs/ResourcePriorities/Overview.html#attr-lazyload)规范中提出了一个具有该名称的属性，但它并没有阻止图像的下载——它只是给了浏览器一个关于排序的提示，这在 HTTP/2 世界中可能没什么用。*

[View on GitHub](https://github.com/whatwg/html/issues/2806)

乔希的想法有一点改变，但原则保持不变。不使用`defer`属性，而是使用`lazyload`属性，目的是仅在视口中加载图像。

```
<img src="https://via.placeholder.com/150" lazyload /> 
```

Enter fullscreen mode Exit fullscreen mode

在这篇文章发表的时候，这个问题有 67 个赞，11 个万岁，29 个心脏反应。GitHub 的一个巨大好处是能够轻松地显示对问题的支持。

这一次谈话继续下去，并导致拉请求！

## GitHub 拉取请求

拉取请求来自一位名叫本的谷歌员工。人们正在添加一些内容，并仔细查看规范，以确保所有内容都有意义。截至 21 天前，所有的意见都得到了解决，他们正在等待测试的加入。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)# 3752](https://github.com/whatwg/html/pull/3752)

[![bengreenstein avatar](img/6d223c74bc3ff88fe7b783eaf1f9b701.png)](https://github.com/bengreenstein) **[bengreenstein](https://github.com/bengreenstein)** posted on [<time datetime="2018-06-08T23:27:22Z">Jun 08, 2018</time>](https://github.com/whatwg/html/pull/3752)

@domenic

这是支持 iframe 和 img 元素中 lazyload 属性的规范更改草案。

* * *

问题:#2806 测试:[https://chromium-review . Google source . com/c/chromium/src/+/1417117](https://chromium-review.googlesource.com/c/chromium/src/+/1417117)([wpt 导出](https://github.com/web-platform-tests/wpt/pull/14914))

* * *

[/embedded-content . html](https://whatpr.org/html/3752/embedded-content.html "Last updated on Feb 7, 2020, 3:03 PM UTC (8bac3c8)")([diff](https://whatpr.org/html/3752/2793ee4...8bac3c8/embedded-content.html "Last updated on Feb 7, 2020, 3:03 PM UTC (8bac3c8)"))[/images . html](https://whatpr.org/html/3752/images.html "Last updated on Feb 7, 2020, 3:03 PM UTC (8bac3c8)")([diff](https://whatpr.org/html/3752/2793ee4...8bac3c8/images.html "Last updated on Feb 7, 2020, 3:03 PM UTC (8bac3c8)"))[/index . html](https://whatpr.org/html/3752/index.html "Last updated on Feb 7, 2020, 3:03 PM UTC (8bac3c8)")([diff](https://whatpr.org/html/3752/2793ee4...8bac3c8/index.html "Last updated on Feb 7, 2020, 3:03 PM UTC (8bac3c8)"))[/indexes . html](https://whatpr.org/html/3752/indices.html "Last updated on Feb 7, 2020, 3:03 PM UTC (8bac3c8)")([diff](https://whatpr.org/html/3752/2793ee4...8bac3c8/indices.html "Last updated on Feb 7, 2020, 3:03 PM UTC (8bac3c8)"))[/media . html](https://whatpr.org/html/3752/media.html "Last updated on Feb 7, 2020, 3:03 PM UTC (8bac3c8)")([diff](https://whatpr.org/html/3752/2793ee4...8bac3c8/media.html "Last updated on Feb 7, 2020, 3:03 PM UTC (8bac3c8)"))[/rendering . html](https://whatpr.org/html/3752/rendering.html "Last updated on Feb 7, 2020, 3:03 PM UTC (8bac3c8)")

[View on GitHub](https://github.com/whatwg/html/pull/3752)

## 潜能规格

让我们来看看描述，我们不能突然到 Mozilla 看看这个时候，因为我们太早了。

> 根据属性的当前状态，属性向用户代理提供提示，以帮助决定是立即加载元素，还是推迟加载，直到元素可见。
> 
> 上的**表示强烈倾向于推迟获取元素的资源，直到它可以查看。**
> 
> **off**
> 表示必须立即获取元素的资源，不管是否可见。
> 
> **自动**
> 表示用户代理可以决定抓取策略(默认)。
> 
> 属性的`missing value default`和`invalid value default`都是`auto`状态。

这里有一些实际的例子

```
<!-- 
  this image will not be fetched until it 
  is in the viewport, meaning the page loads
  faster and uses less data.
-->
<img src="https://via.placeholder.com/150" lazyload="on" />

<!-- 
  this image will be fetched as soon as the
  page opens, this is how website work currently
-->
<img src="https://via.placeholder.com/150" lazyload="off" />

<!-- 
  this image will probably work the same as off
  but there is space for interpretation
-->
<img src="https://via.placeholder.com/150" lazyload="auto" />

<!-- 
  If the lazyload value is invalid or missing
  the attribute will default auto
-->
<img src="https://via.placeholder.com/150" lazyload="bar" />
<img src="https://via.placeholder.com/150" /> 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

在我看来，我们可以传递给浏览器的任何东西都会带来一个更好的网络。对我来说，让开发团队轻松加载所有图像是有意义的。有什么想法吗？让我知道下面。

如果你有你认为规格中缺少的东西，直接去[发布](https://github.com/whatwg/html/issues/2806)并让他们知道。更新规范是一个非常微妙的过程，在任何浏览器实现它之前，他们得到的帮助越多越好。

感谢你的阅读，我知道这和我的其他帖子有些不同，但我认为它仍然很有趣。

❤🦄🦄❤🦄❤🦄🦄❤
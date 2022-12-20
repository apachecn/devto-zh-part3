# 构建一个有礼貌的网络爬虫

> 原文：<https://dev.to/turnersoftware/building-a-polite-web-crawler-3b8h>

网络爬行是让程序或脚本访问网站、捕获内容并发现该内容所链接到的任何页面的行为。从表面上看，它实际上只是执行 HTTP 请求和解析 HTML，这两件事在各种语言和框架中都可以很容易地完成。

对于搜索引擎或任何想要对网站进行分析的人来说，网络爬行是一个极其重要的工具。然而，爬行站点的行为会消耗站点运营商的大量资源，这取决于站点是如何被爬行的。

例如，如果你在几秒钟内抓取了一个 1000 页的网站，你可能会给低带宽主机带来不小的服务器负载。如果你抓取了一个缓慢加载的页面，但是你的抓取器没有正确处理它，不断地重新查询同一个页面。如果你只是在抓取不应该被抓取的页面呢？这些事情会导致网站运营者非常不安。

[![](img/bd445978e28b512d4e840d8d1eeea050.png)](https://i.giphy.com/media/kkpcRessCvNyo/giphy.gif)

在之前的一篇文章中，[我写了 Robots.txt 文件](https://dev.to/turnersoftware/no-robots-allowed-4mnl)以及它如何从网站运营商的角度帮助解决这些问题。网络爬虫应该(但不是必须)遵守该文件中的规则，以防止被阻止。除了 Robots.txt 文件之外，爬虫还应该做一些其他的事情来避免被阻塞。

当大规模抓取网站时，尤其是出于商业目的，提供一个自定义用户代理是一个好主意，允许网站运营商有机会限制可以抓取的页面。

抓取频率是另一个你想要改进的方面，让你抓取一个网站足够快，而不成为性能负担。您很可能希望将抓取限制在每秒几个请求。跟踪请求需要多长时间，并开始限制爬虫以补偿潜在的站点负载问题，这也是一个好主意。

> [![](img/3417f2c67ff9f6720c2e0fdda414b74d.png)](https://i.giphy.com/media/wnAsnQtnzqHU4/giphy.gif) 
> *某服务器因负载起火的实际镜头，完全不是出自某[电视剧](https://en.wikipedia.org/wiki/Silicon_Valley_(TV_series))*

我整天都在编程。NET，我的一个项目需要一个网络爬虫。已经有一些流行的网络爬虫了，包括 [Abot](https://github.com/sjdirect/abot/) 和 [DotnetSpider](https://github.com/dotnetcore/DotnetSpider) ，但是由于不同的原因，它们不适合我的需要。

我最初在我的项目中有一个 Abot 设置，但是我一直在将我的项目移植到。NET 核心，它不支持它。该库还使用一个不再受支持的库版本来解析 Robots.txt 文件。

对于 DotnetSpider，它确实支持。NET Core，但它是围绕一个完全不同的过程设计的，该过程将它与消息队列、模型绑定和内置的 DB 编写一起使用。这些都是很酷的功能，但是对于我自己的需求来说太多了。

我想要一个简单的爬虫，支持异步/等待。网芯支持就这样 [InfinityCrawler](https://github.com/TurnerSoftware/InfinityCrawler) 诞生了！

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[Turner software](https://github.com/TurnerSoftware)/[infinity crawler](https://github.com/TurnerSoftware/InfinityCrawler)

### 一个简单而强大的网络爬虫库。网

<article class="markdown-body entry-content container-lg" itemprop="text">

# 无限爬虫

一个简单而强大的 C#网络爬虫库

[![AppVeyor](img/51f52c8bba387ed0ef7c7251eec8da24.png)](https://ci.appveyor.com/project/Turnerj/infinitycrawler)[![Codecov](img/33ee140959cbc3da442f65e8832c8684.png)](https://codecov.io/gh/TurnerSoftware/infinitycrawler)[![NuGet](img/88ba6dfccd59704e1d93d8571c68af81.png)](https://www.nuget.org/packages/InfinityCrawler)

## 特征

*   服从 robots.txt(爬网延迟和允许/不允许)
*   遵循页面内机器人规则(`X-Robots-Tag`标题和`<meta name="robots" />`标签)
*   使用 sitemap.xml 作为网站初始爬网的种子
*   围绕一个并行任务`async` / `await`系统构建
*   可交换的请求和内容处理器，允许更好的定制
*   自动节流(见下文)

## 礼貌爬行

该爬虫是围绕快速但“礼貌”的网站爬行而构建的，这是通过许多允许调整延迟和节流的设置来实现的。

您可以控制:

*   同时请求的数量
*   请求开始之间的延迟(注意:如果为用户代理定义了一个`crawl-delay`，这将是最小值)
*   请求延迟中的人为“抖动”(请求看起来不那么“机器人化”)
*   在限制应用于新请求之前，请求超时
*   节流请求回退:增加到节流请求延迟中的时间量(这是…

</article>

[View on GitHub](https://github.com/TurnerSoftware/InfinityCrawler)

老实说，我不知道为什么我把它叫做 infinity crawler——当时听起来很酷，所以我就跟着它走了。

这个爬虫在里面。NET 标准，构建在我的 [SitemapTools](https://github.com/TurnerSoftware/SitemapTools) 和 [RobotsExclusionTools](https://github.com/TurnerSoftware/RobotsExclusionTools) 库的基础上。它使用 Sitemap 库来帮助播种应该开始爬行的 URL 列表。

它内置了对爬行频率的支持，包括遵守 Robots.txt 文件中定义的频率。它可以检测缓慢的请求并自动节流以避免网站瘫痪，还可以检测性能何时提高并恢复正常。

```
using InfinityCrawler;

var crawler = new Crawler();
var results = await crawler.Crawl(siteUri, new CrawlSettings
{
    UserAgent = "Your Awesome Crawler User Agent Here"
}); 
```

Enter fullscreen mode Exit fullscreen mode

InfinityCrawler，虽然可用于任何。NET 项目，但它仍处于早期阶段。我很高兴它的核心功能，但可能会经历几个阶段的重组以及扩大测试。

我个人对如何实现 async/await 部分感到非常自豪，但我很乐意与这方面的专家交流。NET 来检查我的实现，并给出如何改进的建议。

[![](img/d16ce5733e72d745899742982f5e49f0.png)](https://i.giphy.com/media/VJrEwNieUbo52/giphy.gif)
# 网站地图 101

> 原文：<https://dev.to/turnersoftware/sitemaps-101-4ch8>

我们在网上搜索各种东西并获得相关结果的能力是一项技术成就，尤其是在搜索引擎需要工作的规模上。他们需要建立网站和内容的大型索引，这样他们就可以处理查询，并为我们带来我们想要的内容。

我[之前讲过](https://dev.to/turnerj/no-robots-allowed-4mnl)“robots . txt”文件以及网络爬虫和网站运营者的关系。这是一个难题，它有助于网络爬虫和网站运营商就网站上不应该被索引的内容进行沟通。

另一个难题是网站地图，这个文件可以帮助你告诉网络爬虫和搜索引擎你网站上的所有网页，它们最后更新的时间和更新的频率。仅仅通过内容中的链接来抓取页面是不可能得到这些信息的。

## 简史

也许这并不奇怪[我们要感谢谷歌](https://web.archive.org/web/20050608015054/http://googleblog.blogspot.com/2005/06/webmaster-friendly.html)在 2005 年中期开创了网站地图文件的概念。

[![The Grinder TV Show Quote - Todd saying "Thank you"](img/bb779e96cd2da36e3998bbc7c7c97dbf.png)](https://i.giphy.com/media/l4HnS4Ln4OCBTDpew/giphy.gif)

2006 年 11 月，[雅虎和微软加入了谷歌，以模式](https://googlepress.blogspot.com/2006/11/major-search-engines-unite-to-support_16.html)[“网站地图 0.9”](https://www.sitemaps.org/protocol.html)支持标准。

此后不久，他们共同宣布支持“Robots.txt”文件的一个非标准功能，允许他们指出网站的站点地图可以位于何处。

例如，下面是 [dev.to 的 robots 文件](https://dev.to/robots.txt)指向站点地图的位置:

```
# See http://www.robotstxt.org/robotstxt.html for documentation on how to use the robots.txt file
#
# To ban all spiders from the entire site uncomment the next two lines:
# User-agent: *
# Disallow: /

Sitemap: https://thepracticaldev.s3.amazonaws.com/sitemaps/sitemap.xml.gz 
```

Enter fullscreen mode Exit fullscreen mode

## 格式

有 3 种风格的网站地图:XML、TXT 和 RSS

### XML 站点地图

这些很可能是你实际使用的网站地图的唯一形式，也是规范中定义的核心格式。也就是说，并不是所有的 XML 站点地图都是一样的，因为有两种不同的类型。

#### 普通站点地图文件

你有许多`<url>`标签，它们都必须有一个`<loc>`标签，但是可以选择`<lastmod>`、`<changefreq>`和`<priority>`标签。

标签仅仅是你站点上页面的绝对 URL。

标签有助于显示页面的“新鲜度”。虽然爬虫可能会根据这个值进行优先级排序，但我不建议不断地将最后修改日期更新为当前日期来试图欺骗系统。

标签只是爬虫的一个指南，不要认为设置它为“每小时”会让爬虫更频繁地抓取你的站点。

标签不是用来定义这个页面与其他网站相比有多重要，而是定义这个页面对网络爬虫来说有多重要。未设置时，默认值为“0.5”。

规范中的 XML 站点地图示例:

```
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
   <url>
      <loc>http://www.example.com/</loc>
      <lastmod>2005-01-02</lastmod>
      <changefreq>monthly</changefreq>
      <priority>0.8</priority>
   </url>
   <url>
      <loc>http://www.example.com/catalog?item=12&amp;desc=vacation_hawaii</loc>
      <changefreq>weekly</changefreq>
   </url>
   <url>
      <loc>http://www.example.com/catalog?item=73&amp;desc=vacation_new_zealand</loc>
      <lastmod>2004-12-23</lastmod>
      <changefreq>weekly</changefreq>
   </url>
   <url>
      <loc>http://www.example.com/catalog?item=74&amp;desc=vacation_newfoundland</loc>
      <lastmod>2004-12-23T18:00:15+00:00</lastmod>
      <priority>0.3</priority>
   </url>
   <url>
      <loc>http://www.example.com/catalog?item=83&amp;desc=vacation_usa</loc>
      <lastmod>2004-11-23</lastmod>
   </url>
</urlset> 
```

Enter fullscreen mode Exit fullscreen mode

#### 站点地图索引文件

根据该标准，一个普通的站点地图文件被限制为 50，000 个 URL，最大大小为 50MB。虽然我不一定相信这种限制仍然存在，但它确实导致了网站地图索引文件的出现。

这些文件基本上看起来很像一个普通的站点地图文件，但基本上只是指向其他站点地图。您有许多`<sitemap>`标签，其中包含一个必需的`<loc>`标签和一个可选的`<lastmod>`标签。

规范中的索引站点地图示例:

```
<?xml version="1.0" encoding="UTF-8"?>
<sitemapindex xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <sitemap>
    <loc>http://www.example.com/sitemap1.xml.gz</loc>
    <lastmod>2004-10-01T18:23:17+00:00</lastmod>
  </sitemap>
  <sitemap>
    <loc>http://www.example.com/sitemap2.xml.gz</loc>
    <lastmod>2004-01-01</lastmod>
  </sitemap>
</sitemapindex> 
```

Enter fullscreen mode Exit fullscreen mode

### TXT 网站地图

这种类型的站点地图确实删除了 XML 站点地图中的许多功能，比如上次修改日期或页面更新的频率。

这种格式只是让你想索引的每个 URL 在一个新的行上，绝对没有其他数据。

```
http://www.example.com/
http://www.example.com/catalog?item=12&amp;desc=vacation_hawaii
http://www.example.com/catalog?item=73&amp;desc=vacation_new_zealand 
```

Enter fullscreen mode Exit fullscreen mode

### RSS 网站地图

虽然不像 TXT 网站地图那样受限制，RSS 网站地图也有自己的问题，比如只提供最近网址的信息。

您将使用`<link>`标签来定义您想要索引的 URL，并使用`<pubDate>`来定义它最后一次被修改的时间。

## 网站地图的未来

网站地图的主要规范没有改变，但有一些其他类型的网站地图正在开发中，如[视频网站地图](https://support.google.com/webmasters/answer/80471?hl=en&ref_topic=4581190)、[图片网站地图](https://support.google.com/webmasters/answer/178636?hl=en&ref_topic=4581190)和特殊的[谷歌新闻网站地图](https://support.google.com/webmasters/answer/9606710?hl=en&ref_topic=4581190)。

谷歌也宣布支持多语言网站地图，用户可以定义每个网址的语言。

对这些附加站点地图类型的支持不像主要的 XML 站点地图那样广泛，尽管将来可能会改变。

## 我写了一个东西...

我的前几篇文章之所以成功，是因为我致力于构建能够为我解决问题的库和工具，这篇文章也不例外。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[Turner software](https://github.com/TurnerSoftware)/[SitemapTools](https://github.com/TurnerSoftware/SitemapTools)

### C#中的站点地图(sitemap.xml)解析和查询库

<article class="markdown-body entry-content container-lg" itemprop="text">

# 网站地图工具

C#中的站点地图(sitemap.xml)查询和解析库

[![AppVeyor](img/8c3f656a424ef653c4c202af3bb71ad2.png)](https://ci.appveyor.com/project/Turnerj/sitemaptools)[![Codecov](img/9fdf1a6fd3d4428bd42a849d00460173.png)](https://codecov.io/gh/TurnerSoftware/SitemapTools)[![NuGet](img/e4eb140df11cd7af3df6ae0536d13899.png)](https://www.nuget.org/packages/TurnerSoftware.SitemapTools)

## 关键特征

*   解析 XML 站点地图和[站点地图索引文件](http://www.sitemaps.org/protocol.html#index)
*   处理 GZ 压缩的 XML 站点地图
*   支持 TXT 网站地图

## 笔记

*   不执行网站地图标准[，如 sitemaps.org](http://www.sitemaps.org/protocol.html)所述
*   不验证站点地图
*   不支持 RSS 站点地图

## 例子

```
using TurnerSoftware.SitemapTools;

var sitemapQuery = new SitemapQuery();
var sitemapEntries = await sitemapQuery.GetAllSitemapsForDomainAsync("example.org");
```

Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/TurnerSoftware/SitemapTools)

我需要实际解析我正在进行的项目的站点地图文件，并努力寻找任何现有的。NET 库这样做。我的库的最新版本建立在我自己的[“robots . txt”解析库](https://github.com/TurnerSoftware/RobotsExclusionTools)(用于站点地图文件发现)之上，并且支持 XML 站点地图(普通文件和索引文件)以及 TXT 站点地图。

这个库和我的“Robots.txt”解析库实际上是为第三个库而构建的，我将在以后写一篇关于这个库的文章。

## 更多信息

*   [sitemaps.org](https://www.sitemaps.org/):格式官方网站
*   维基百科上的“站点地图”:涵盖了关于站点地图和任何扩展功能的更多细节。
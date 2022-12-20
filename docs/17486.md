# 使用 Goutte 在 PHP 中进行网页抓取

> 原文：<https://dev.to/sayopaul/web-scraping-in-php-using-goutte--4p6h>

# 使用 Goutte 在 PHP 中进行网页抓取

今天我将谈论一些非常普通的事情，网络抓取。根据您或客户的需求，可能会出现需要从网页中提取数据的情况。

什么是网页抓取？

根据 [WebHarvy](https://www.webharvy.com) ，网页抓取(也称为屏幕抓取、网页数据提取、网页采集等。)是一种用于从网站中提取大量数据的技术。最简单的形式，网络抓取是通过脚本获取网页内容。好了，让我们继续学习 PHP 中的 web 抓取。最近，我需要用 PHP 为一个客户抓取一个网站，所以我寻找用 PHP 讨论 web 抓取的文章，我发现很少，而且大多数都已经过时了。

然而，在我的研究中，我遇到了[Goutte](https://github.com/FriendsOfPHP/Goutte)；一个(很棒的)PHP 屏幕抓取和网页抓取库。在其核心，Goutte 是一个包装三个 Symfony 的组成部分(上帝保佑杨奇煜🙌) ;BrowserKit，CssSelector 和 DomCrawler。对我们来说，了解这些组件的作用很重要，因为它有助于我们了解 Goutte 有多强大。

BrowserKit 简单地说，BrowserKit 组件模拟了真实浏览器的行为。这是 Goutte 的基本元素。

DomCrawlerDomCrawler 组件简化了 DOM(文档对象模型)的导航。DomCrawler 允许我们像这样导航 dom:

```
 $crawler = $crawler->filter('body > p'); 
```

我们还可以使用它提供的一些方法遍历 DOM 上的节点。例如，如果我们想获得页面主体的第一段，我们可以这样做:

```
 $crawler->filter('body > p')->eq(0); 
```

eq()方法是零索引的，它使用一个数字来指定我们想要访问的元素的位置。
还有其他方法如`siblings()`、`first()`【T2 的别名，地下简称`eq(0)`、`last()`等。

CssSelectorCssSelector 是一个很棒的组件，它允许我们通过 CSS 选择器来选择元素。它通过将 CSS 选择器转换成它们的 XPath 等价物来实现这一点。例如，假设我们想要选择一个具有名为“fire”的类的元素，我们可以这样做:

```
 $crawler->filter('.fire'); 
```

CssSelector 组件太神奇了，它甚至支持 CSS 如；

```
 $crawler->filter('div[style*="max-height:175px; overflow: hidden;"]'); 
```

以上意味着我们正在寻找一个具有内联样式属性`"style=max-height:175px; overflow: hidden;"`的 div 元素

更多信息，请务必阅读 [DomCrawler](https://symfony.com/doc/current/components/dom_crawler.html) 、 [CssSelector](https://symfony.com/doc/current/components/css_selector.html) 和 [Goutte](https://github.com/FriendsOfPHP/Goutte) 的文档。

好了，现在我们对三个主要组件有了一点概念，是时候把所有的东西放在一起，实际上做点什么了。现在你可能已经意识到了，说到刮擦，没有现成的方法。你可以自由探索和尝试许多方法来获取数据。你唯一的限制就是你的创造力。有时候，我不得不将 CssSelector 和 DomCrawler 结合起来，以便得到我想要的东西(实际上，很多时候)。

在下一篇[帖子](https://dev.to/sayopaul/web-scraping-in-php-using-goutte---part-2-5e66)中，我们将通过抓取 [Punch](https://punchng.com) 的网站，把我们目前所学的一切都放入游戏中。
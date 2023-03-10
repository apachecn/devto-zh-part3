# WebAIM 百万分析对你正在建立的网络有什么看法

> 原文：<https://dev.to/forsh3y/what-the-webaim-million-analysis-says-about-the-web-you-re-building-2740>

## 剧透:不是好东西。

尽管 WebAIM Million analysis 于 2019 年 2 月发布，但我在 dev.to 上没有发现任何关于它的报道，这已经在我的草稿中保留了大约一周。但是我现在发布这个，因为昨天我看到了一个由[@ ambery](https://twitter.com/amber1ey/status/1114224388558159872)发起的 twitter 民意调查，其中有一些关于它的影响范围的令人悲伤的数字。前端专业人员*需要*了解他们网站的状态。

[![Twitter Poll: 408 votes. Results = 82% had not heard of WebAIM survey. 5% had heard, but not read it. 12% had heard and read it. ](img/9ed43fb3f3b38ffe787c2cf62aefc728.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RdCbXsyV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/btnxdza45rdxm6zyvcll.png)

你还记得 2008 年吗？一个可能更简单的时间，我自己是年轻的青少年岁。我仍然对我父亲在第一天买的 iPhone 能做的所有事情充满敬畏。根据我当时对 web 开发的了解，这似乎要简单得多。为其制造的设备仍然主要是台式机，像渐进式改进这样的事情还没有实现，IE 7 是当今的浏览器。

2008 年，WCAG 2.0 标准最终确定。

当你和我一起浏览可能是我见过的最令人沮丧的网络统计数据时，请记住这一点。几乎从我受教育的第一天起，我就对可及性充满热情，这让我[和许多](https://ethanmarcotte.com/wrote/the-web-we-broke/) [人](https://www.winstonhearn.com/wrote/2019/perpetuating-harm/)变得忧郁和内省。

你可能会问，是什么可能导致这样的反应？

首先，“97.8%的主页都有可察觉的 WCAG 2 失败”。

如今，100 万个顶级主页中有将近 98%存在可检测到的故障。

## **第九十八章**

如果你曾经使用过 Lighthouse 可访问性审计或类似的工具，你会知道可检测的问题仅仅讲述了网站可访问性故事的一部分。所以这是假定的，最好的情况下的数据；他们的 WAVE API 所能检测到的并不能说明全部情况。

下面是一些令人沮丧的亮点！

*   低于 WCAG 2 AA 的低对比度文本是检测到的最常见的可访问性问题。请记住，WCAG 也有一个 AAA 标准，所以这不是最高对比度标准。85.3%被分析的主页有可检测的 WCAG 对比失败。平均来说，主页上有 36 个不同的文本，但对比度不够。

*   33.6%的图像(平均每页 12.3 个)缺少替代文本(不包括 alt= " ")。16.8%的人有无意义的替代文字。

*   在识别的 340 万个表单输入中，有 59%是未标记的(通过< label >、aria-label 或 aria-label edby)。

*   每 20 个标题中就有一个结构不当。跳过的标题出现在 36.3%的页面上。14.9%根本没有标题。

*   有 ARIA 的主页比没有 ARIA 的主页平均多 11.2 个可检测的错误。

*   样本中共有超过 130 万个模糊链接。

*   具有有效 HTML5 文档类型的页面比具有其他文档类型的页面有更多的页面元素和错误。

*   Squarespace 和 Wix 是可访问性问题最少的 CMS。没错，Joe Random 在 30 分钟内创建了一个网站，他的网站可能比你更容易访问！

*   除了 MooTools 和 TweenMax 这两个框架之外，有 JS 框架的主页比没有 JS 框架的主页有更多的错误。

(不，我们还没完呢！)

*   有 jQuery 的主页比没有 jQuery 的主页平均多出 19.2 个错误。Lazy.js 的错误增加了惊人的 235%!谢天谢地，这只占主页的 1.6%，但我可以写信给月亮，讽刺一个名为“懒惰”的图书馆充满了问题。

*   当存在 Bootstrap、animate.css 或 Laravel 时，错误的增加有很大的对应关系。

*   使用非常常见的 Google AdSense 系统的主页平均有 47.2 个错误，几乎是其他页面的两倍！

*   尽管已是 2019 年，但仍有 11，200 个主页和 570 个主页有闪烁内容(或 CSS 文本装饰:blink)。*内心尖叫*

*   检测到 2，099，665 个布局表格，而只有 113，737 个数据表。哦。这意味着特定用户很难(如果不是不可能的话)消化大量数据。

现在，我现在不是，也从来没有受雇于这个行业。通过 9 个月的自学，我明白这是一个巨大的、多学科的问题。然而，这是一个紧迫的问题。每天从互联网内容中被忽略的用户数量是惊人的，我向你们保证，这些人对你们来说并不陌生。即使他们是，那也没关系。这不利于本应自由开放的互联网。

请代表我和其他许多人调查这些问题。看看 Lighthouse 或[级访问审计员](https://www.webaccessibility.com/)对你的网站说了些什么。提问，获得反馈，自己尝试一些辅助技术！下次当你接触另一项漂亮的技术时，试着记住我们还没有达到 3%的实施率这个 11 年前的标准。

[![Twitter Post by at Alvaro Montoro mentioning that 87% of accessibility issues fall into only 5 catagories.](img/51df9f3f4ea5911b4c26ea3ae2b1f9d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b4ANqr2U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/emje7fz3ddn7420vompr.png)
[@阿尔瓦罗 _ 蒙托罗](https://twitter.com/alvaro_montoro/status/1113627080242151424)

*封面图片归功于 Travis Saylor @pexels*
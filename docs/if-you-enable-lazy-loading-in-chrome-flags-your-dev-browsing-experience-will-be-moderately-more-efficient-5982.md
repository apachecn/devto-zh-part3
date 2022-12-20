# 如果你在 chrome://flags 中启用了“延迟加载”，你的开发者浏览体验将会更加高效😄

> 原文：<https://dev.to/ben/if-you-enable-lazy-loading-in-chrome-flags-your-dev-browsing-experience-will-be-moderately-more-efficient-5982>

*更新:Chrome 最近发布的延迟加载*

[![yashints](img/0d36c7c282ebcf4aec1143e95bad3d39.png)](/yashints) [## 原生延迟加载登陆 Chrome🔥😍🔥

### 亚塞尔·阿德尔·梅赫拉班 1919 年 8 月 25 日 4 分钟阅读

#showdev #webperf #webdev #lazyloading](/yashints/native-lazy-loading-is-landed-in-chrome-2kli)

我刚刚合并了这个 PR，它将`loading="lazy"`添加到围绕 [dev.to](https://dev.to/) 的一堆图像中。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 给一些图片添加 loading =‘lazy’属性 #2776](https://github.com/thepracticaldev/dev.to/pull/2776) 

[![benhalpern avatar](img/2b95d19f70b4d1ccc724ce3ad9480e00.png)](https://github.com/benhalpern) **[benhalpern](https://github.com/benhalpern)** posted on [<time datetime="2019-05-09T19:35:04Z">May 09, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2776)

## 这是什么类型的公关？(勾选所有适用选项)

*   [ ]重构
*   [x]功能
*   [ ]错误修复
*   [ ]文档更新

## 描述

这将使我们准备好从 Chrome 中的`loading="lazy"`特性中获益。现在直播应该不会有什么问题。这意味着在列表这样的页面上，用户不会加载那些低于文件夹的图片。

我想早点得到这个只会有助于我们的搜索引擎优化，因为我猜谷歌会在它发布时奖励这个属性。

[https://dev . to/Ben/native-lazy-loading-for-img-and-iframe-is-coming-to-the-web-55on](https://dev.to/ben/native-lazy-loading-for-img-and-iframe-is-coming-to-the-web-55on)

您现在可以使用`chrome://flags`打开它

TODO:在更多图像和 iframes 上实现。

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2776)

这项功能预计将于 6 月初与 Chrome 75 一起发布，但这个标志将让你现在就可以玩它。

这会对你的生活产生重大影响吗？不。你可能什么都不会注意到。但是有些图像会延迟加载，直到你接近需要它们的时候——这很好。

这是一个简单的改变，我们现在可以保证在正式发布的第一天就准备好该功能。

编码快乐！
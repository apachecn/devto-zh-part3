# 修复和隐藏持久条的子块原点过滤器

> 原文：<https://dev.to/notriddle/a-ublock-origin-filter-to-fix-and-hide-persistent-bars-360k>

我一直在建立一个隐藏固定横幅和唠叨弹出框的列表。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [未解之谜](https://github.com/notriddle) / [移除-修复-横幅](https://github.com/notriddle/remove-fixed-banners)

### 我的子块来源过滤器

<article class="markdown-body entry-content container-lg" itemprop="text">

# 移除自动滚动横幅

一组[子块原点](https://github.com/gorhill/uBlock/)过滤器，用于节省垂直屏幕空间并移除 nag 框。

### 装置

要将这些过滤器添加到您自己的列表中，请转到 uBlock 原始设置页面，然后转到自定义，然后选中导入框。

添加此 URL 以使其有效:

```
https://notriddle.com/remove-fixed-banners/filters.txt 
```

### 规则

*   该过滤器列表不包括空白块，仅包括特定域上的特定 URL 和 CSS 规则。总括规则太慢了浏览器。
*   此过滤器列表仅阻止弹出窗口和竖条。固定侧边栏不会被阻止。

### 发展提示

Medium 使用 cookies 来决定是否向您显示他们的弹出框，他们不会在第一篇文章上立即向您显示一个 nag 框，而是等到第*篇文章的第二篇*篇文章。如果您想持续显示他们的 nag 框，我可以通过打开 medium 文章来做到这一点…

</article>

[View on GitHub](https://github.com/notriddle/remove-fixed-banners)
# CSS 网格和 IE11

> 原文：<https://dev.to/valeversa/css-grid-and-ie11-2p8g>

[![CSS grid in IE11](img/a0726f7986bc20226f02552c1917f238.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QtZMKAri--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/raet91uxd7qqgojvszmf.png)

#### 一点点 Javascript 如何帮助我们与 CSS Grid 和 IE11 和平共处。

几个月前，我们决定是时候改造我们的核心平台了。从头开始的想法既令人兴奋又具有挑战性:如何选择可持续的技术和工具，同时确保团队能够处理它们？在前端，决定归结为三个支柱:a)使用 CSS 类的已知命名约定:BEM，b)使用[原子组件设计](http://bradfrost.com/blog/post/atomic-web-design/)方法，以及 c)理想地使用 CSS 网格构建每个布局。本文将关注最新的选择及其后果。

CSS Grid 是一种相对较新的布局方法，已经证明对于初级开发人员来说学习起来很简单。韦斯·博斯在这方面有一个很棒的免费教程。Grid 的概念也足够灵活，可以满足我们的需求——我们的后端平台主要用于桌面，我们的布局包括每页大量的元素:表格、按钮和大量要显示的数据。

采用 CSS Grid 的决定对我们来说非常有效，尽管在为我们的新布局编码的早期，我们偶然发现了一个障碍:需要构建在 IE11 浏览器上工作并且看起来不错的布局。我们目前有 35%的用户仍在使用 IE11 浏览，我们不能忽视这样一个事实，CSS Grid 在 IE 上不能像在 Chrome 或 Firefox 上那样无缝地工作:IE 浏览器不理解隐式的行列定位，所以它们在第一列/行中显示重叠的每个元素。

我们想到的第一个解决方案是总是使用 CSS 来明确每个元素的行和列的位置。

```
.header {
  color: yellow;
  font-size: 24px;
  grid-column: 2; /* explicit positioning */
} 
```

由于我们使用的是可重用组件，在大多数情况下，我们不能将上述声明直接添加到元素的全局 CSS 中(例如，在整个平台中，并不是所有的标题都在第 2 列中)。这个问题的解决方案是直接在我们的代码中添加特定的类:这使我们能够将所有的元素保持在正确的布局位置——即使是在 IE11 中。

```
/ adding block classes for IE11 (HAML)
%header.block--col2.block--row2 
```

```
/* CSS for the element and the block classes */
.header {
  color: yellow;
  font-size: 24px;
}
.block--col2 { grid-column: 2; }
.block--row2 { grid-row: 2; } 
```

这个解决方案，虽然给了我们在 IE11 上寻找的结果，但却使我们的标记变得难以置信的复杂和难以阅读:

```
/ before
.container
  .company-logo
  .side-menu.block--row2
    .menu-item
    .menu-item.block--row2
    .menu-item.block--row3
  .banner-area.block--col2
  .main-content-area.block--col2.block--row2
    .intro-bar
      .header
      .buttons.block--col2
      .info.block--col3
        .info-detail.block--col1
        .info-detail.block--col2
        .info-detail.block--col3
    .content
      .hero 
```

这种方法容易出错，我们的布局变得越复杂，维护它就越复杂:我们需要一个更可持续的解决方案。我们的网格标注器 polyfill 的想法就是从这里诞生的:我们需要一个简单的 JavaScript，如果浏览器是 IE111，它可以自动为组件标注列和行的位置。对于其他浏览器来说，这个脚本根本不起作用。只花了几个小时编写好的旧 JavaScript，我们就有了第一个原型。我们最终可以移除所有这些烦人的块类，这使得标记变得更简单、更易于维护:

```
/ after
.container
  .company-logo
  .side-menu
    .menu-item
    .menu-item
    .menu-item
  .banner-area
  .main-content-area
    .intro-bar
      .header
      .buttons
      .info
        .info-detail
        .info-detail
        .info-detail
    .content
      .hero 
```

当然，我们的 grid-annotator 并没有解决 IE112 上的所有 CSS 网格问题:它的一些其他特性，比如 repeat 或 grid-area，还没有解决，但是我们可能会在将来添加它们。

在我们的产品中实现 polyfill 时，我们注意到了一个小问题:显然，IE11 在 JavaScript 能够解开网格列和行之前加载了页面。这导致页面组件在一瞬间重叠。在与产品经理讨论这个问题并在不同的计算机上运行测试后，我们一致认为这个“小故障”并没有影响页面的性能。

如果您处于类似的情况，请随意查看一下[网格注释器存储库](https://github.com/motine/css_grid_annotator)。你有更多的想法或想要贡献吗？你有什么评论或者只是给我们发邮件，联系[汤姆](//mailto:info@tomrothe.de)或者[瓦伦蒂娜](https://www.twitter.com/valeversa)。

这篇文章最初发表在[汤姆的博客](http://tomrothe.de/posts/css_grid_and_ie11.html)上。
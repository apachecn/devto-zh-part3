# 2019 年网络图像搭便车指南

> 原文：<https://dev.to/papaponmx/a-hitchhikers-guide-to-web-images-on-2019-203j>

*封面照片由[JfXie](https://www.flickr.com/photos/jfxie/)T3】*

这篇文章的目的是让你对当前的网络功能有所了解，并帮助你发现一些机会领域:

关于你阅读这篇文章的一些假设:

*   你熟悉 HTML。
*   你知道像`jpg`、`png`和`svg`这样的基本图像格式。
*   您对 CSS 有所了解，并且有足够的专业知识来阅读、理解和实现 MDN 文档。

## 简介

网络正在快速发展。作为一名开发人员，很高兴拥有一些特性，这些特性是您应该熟悉的，下面是一些常见的特性:

*   国际化(i18n)。
*   前端路由。
*   响应式网页设计。
*   前端用户认证。
*   SEO。
*   页面速度优化。

## HTML 规范内的新特性

HTML5 规范旨在服务于移动网络，你可以把它想成两件事一组 [HTML 标签](https://www.w3schools.com/html/html5_new_elements.asp)和[几个新的 API](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5)

让我们逐一修改一些新的标签和属性，之后我会添加一个使用所有这些的例子。

### `<picture>`

你可以把它想象成一个`img`标签，更灵活地显示图像资源。它让我们能够为高 DPI 显示器提供更高密度的图像。让我们看看它是什么样子，根据 MDN:

> HTML 元素包含零个或多个<source>元素和一个  元素，为不同的显示/设备场景提供图像版本。

#### `<source>`

HTML [`<source>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/source#attr-srcset) 元素为`<picture>`、`<audio>`元素或`<video>`元素指定多个媒体资源。

#### `srcset`属性

这个属性类似于`src`，但是用来定义多个图像`src`，有很多方法可以实现这个源

使用多个图像和一个 src 声明一次属性**作为默认属性**。在 webkit.org 的[检查原始来源。](https://webkit.org/demos/srcset/) 

```
<img 
  src="image-src.png" 
  srcset="image-1x.png 1x, 
          image-2x.png 2x,
          image-3x.png 3x, 
          image-4x.png 4x"
> 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法是声明多个源，每个源中有一个`srcset`。你可以在下面的例子中看到。主要区别是第二种有更特殊的图像显示条件。

**将所有这些放在一起**:

[参见 Codepen 上的](https://codepen.io/papapon/pen/EMOdWr?editors=1100)

```
<picture>
  <source media="(max-width: 799px)" srcset="https://placekitten.com/200/300">
  <source media="(min-width: 800px)" srcset="https://placekitten.com/400/600">
  <img src="https://placekitten.com/600/800" alt="A cute black cat showing his love">
</picture> 
```

Enter fullscreen mode Exit fullscreen mode

## ~~新增~~相关 CSS 属性

如果你不知道，剧透警告， [CSS 4 不存在](https://dev.to/olimpioadolfo/does-css4-exist-5ao4)。相反，我们有现代浏览器支持的小增量。下面是一些值得注意的属性:

### `object-position`

顾名思义，`object-position`，定义了一个对象在一个框内的定义方式。与 [`background-position`](https://developer.mozilla.org/es/docs/Web/CSS/background-position) 颇为相似。[可以在](https://codepen.io/robinrendle/pen/raGOOJ)看到码笔示例，感谢 [@Robin Rendle](https://twitter.com/robinrendle) 。

### `image-rendering`

这个属性告诉浏览器应该如何呈现图像。这些值描述了为缩放图像而要实现的算法，在撰写本文时，可能的值有:

*   `auto`:默认值，浏览器的算法将决定如何缩放图像。
*   `crisp-edges`:保留图像的对比度和边缘。
*   顾名思义，随着视口的增长，浏览器将保持它的像素化风格。

你可以在 Codepen 的这个例子中看到这三幅图像的行为。

或者检查应用于二维码的这个 [`image-rendering`。](https://codepen.io/robinrendle/pen/EaOJeq)

## 图像格式

```
<img alt="Apple iPhone 7 Plus, GSM Unlocked, 32GB - Rose Gold (Refurbished)" src="https://images-na.ssl-images-amazon.cimg/I/51gVm-dTdvL._AC_SY200_.jpg" class="product-image" height="200px" data-a-hires="https://images-na.ssl-images-amazon.cimg/I/51gVm-dTdvL._AC_SY400_.jpg"> 
```

Enter fullscreen mode Exit fullscreen mode

## 懒装

延迟加载图像意味着它们提供得很慢，除非必要，否则应该避免。

当实现时，对于最终用户来说，这通常意味着提供更小的包、更快的加载和更好的整体体验。

### 路口观察器 API

首先，您可能希望使用交叉点观察器 API。

> 简而言之，API 为用户提供了一种观察给定元素并监视它们与给定祖先元素或视口本身的交集的方式。

如果你用盖茨比，[盖茨比链接](https://www.gatsbyjs.org/docs/gatsby-link/)已经在引擎盖下用了。

*   [MDN 上的交叉点观察器 API](https://developer.mozilla.org/es/docs/Web/API/Intersection_Observer_API)。
*   [caniuse.com 上的交叉点观察器 API 支持](https://caniuse.com/#search=intersection)。
*   大文章:[掌握路口观察者 API](https://www.hweaver.com/intersection-observer-single-page-navigation/) 。

对于现代框架，这里有一些 npm 包，您可能想了解一下:

*   [反应可加载](https://github.com/jamiebuilds/react-loadable)。
*   [Vue 懒加载](https://github.com/hilongjw/vue-lazyload)。
*   [反应迟缓加载图像组件](https://github.com/Aljullu/react-lazy-load-image-component#readme)
*   [有角度的惰性装载模块](https://angular.io/guide/lazy-loading-ngmodules)。

## 骷髅

即使你在适当的地方安装了延迟加载，用户的互联网速度也可能很慢或中断，即使在城市地区，即使用户没有意识到这一点，因此在适当的地方安装一些东西让用户看到是很好的。这就是骨骼出现的地方。

骨架是*一个占位符，位于需要等待加载的地方*

你可能以前见过，这是骨架的样子:

[![alt text](img/ed11456c955687b9bec6209a34275e2b.png "CSS skeleton example")](https://res.cloudinary.com/practicaldev/image/fetch/s--gFdklvpL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/igx7hnjxzobwglbnqj8c.png)

[https://codepen.io/oslego/pen/XdvWmd](https://codepen.io/oslego/pen/XdvWmd)

为了实现这个特性，你可以使用一些 npm 包，但是作为一个经验法则，让我们使用一些更简单的。

### 介绍`:empty`伪类

根据 MDN 文档:

> `empty`表示任何没有子元素的元素。子元素可以是元素节点，也可以是文本(包括空白)。注释、处理指令和 CSS 内容不会影响元素是否被认为是空的。

所以骨架可以简单到

```
/*
 * ...your styles
 */

.user-data:empty {
  background: rgba(130, 130, 130, 0.2); 
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 MDN Docs 查看带有 CSS 或 [`:empty`的动画](https://developer.mozilla.org/en-US/docs/Web/CSS/:empty)[骨架画面。](https://codepen.io/oslego/pen/XdvWmd)

## 介绍 WebP

WebP 是一种为网络设计的图像格式，它于 2010 年 9 月发布，除了 Safari，现在它在大多数现代浏览器上得到广泛支持。如果你在 [dev.to](//dev.to/) 上查看你的个人资料图片，你可能会发现它已经在使用了。最后一部分我列举几篇。

## 进一步资源

*   [MDN 移动优化](https://moz.com/learn/seo/mobile-optimization)
*   [谷歌的移动测试工具](https://search.google.com/test/mobile-friendly)。
*   [网站测速图片分析工具](https://webspeedtest.cloudinary.com/)。
*   [HTML5 备忘单](https://www.wpkube.com/html5-cheat-sheet/)。
*   [了解如何使用](https://dev.to/dimitrinicolas/learn-how-to-automate-your-image-optimization-process-with-lepto-890?utm_source=additional_box&utm_medium=internal&utm_campaign=regular&booster_org=) [Dimitry Nicholas](https://dev.to/dimitrinicolas) 的 Lepto 自动优化图像。
*   [通过 CLI 和网络界面自动编辑、优化和分析图像](https://github.com/leptojs/lepto)。
*   [盖茨比形象](https://www.gatsbyjs.org/packages/gatsby-image/)。
*   Lozard JS:高性能、轻量级~1kb、可配置的纯 JS 懒惰加载器，不依赖于响应图像、iframes 和更多[https://apoorv.pro/lozad.js/demo/](https://apoorv.pro/lozad.js/demo/)
*   Google Developers 上关于 WebP 的文章。。
*   维基百科上的 WebP。

这就是所有的人，感谢坚持到最后，继续学习。

干杯。
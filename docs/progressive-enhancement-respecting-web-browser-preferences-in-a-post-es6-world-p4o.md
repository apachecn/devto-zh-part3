# 渐进增强:在后 ES6 时代尊重网络浏览器偏好

> 原文：<https://dev.to/papaponmx/progressive-enhancement-respecting-web-browser-preferences-in-a-post-es6-world-p4o>

封面图片作者:[汤姆·戴维森](https://www.flickr.com/photos/ixtussy/)在 [Flickr](https://www.flickr.com/photos/ixtussy/11713369553/in/photolist-cK6GNN-7XxycZ-bkzTMb-ikG4Zi-4poorm-iR55Ft-5Vd1yv-2wP8hr-269mXaZ-gPq5z-56CF87-834MXP-f5CHSg-dbebug-nDc7V-f5ST7C-83sBpV-f5CMJ8-rrsHEA-f5SZBA-8u65wU-2q3H5t-pw6Ct1-aayTpv-27e2GBR-63wjvq-ppW4DG-4kSh9q-2cG9tvq-8bTRKc-f5SXZU-8u65Cd-nDcoZ-4ihie1-f5SWkm-jw1zQ-f5CBit-9w6fWF-f5T4wo-23nq5PX-4VrVzC-f5CAv6-bGHjLF-sthVp-279U2yQ-4Ra9zX-kWu4hK-qqw2bt-2977WV) 上。

## [T1】简介](#intro)

Google IO 之后，又看了 [a](https://www.youtube.com/watch?v=2KhRmFHLuhE) [几场关于提升](https://www.youtube.com/watch?v=K2JzIUIHIhc) [web 能力](https://www.youtube.com/watch?v=-xZHWK-vHbQ)的会谈，[，我得到了启发。所以这里是](https://www.youtube.com/watch?v=c0oy0vQKEZE)[你们的桥梁](https://en.wikipedia.org/wiki/The_Bridge_Builder)。

## 什么是渐进式增强？

简而言之，*渐进式增强*是一种开发 web 应用程序的理念，其原则如下:

*   基本内容应该可以被所有的网络浏览器访问。
*   基本功能应该可以被所有的网络浏览器访问。
*   稀疏的语义标记包含所有内容。
*   外部链接的 CSS 提供了增强的布局。
*   不引人注目的外部链接 JavaScript 提供了增强的行为。
*   最终用户的 web 浏览器偏好得到尊重。

一个替代 *PE* 的是 *[优雅退化](https://en.wikipedia.org/wiki/Fault_tolerance)* (GD)。不同的是 PE 是从简单到复杂，而 GD 是反过来的。

在 [之前，我已经写了关于可访问性](https://dev.to/papaponmx/accessible-fonts-for-people-in-a-hurry-387)的[，并将再写一篇关于 web 开发的通用启发式的文章。](https://dev.to/papaponmx/in-praise-of-accessibility-outline-3m78)

不仅仅是说服你支持 IE 或者在 CSS 网格上退缩，我的目标是让你知道我们可以使用的新 API，以符合列表上的最后一点:*最终用户的 web 浏览器偏好得到尊重*。

## 尊重网络浏览器偏好

即使您没有意识到，浏览器也暴露了用户偏好的信息，所以让我们浏览一下其中的一些。

### 字体系统默认

使用系统默认字体的另一个原因是 web 性能，因为没有额外的文件需要从服务器获取。有三种方法可以实现这一点:

1.  使用`system-ui`值:
    这是代表默认用户界面字体的字体系列值。除了火狐的[外，](https://bugzilla.mozilla.org/show_bug.cgi?id=1545745)[最新的现代浏览器](https://caniuse.com/#search=system-ui)都支持。

2.  通过使用`font-family` :
    调用来应用系统字体，我建议将它隐藏在[特征查询](https://developer.mozilla.org/en-US/docs/Web/CSS/@supports)之后，作为一个后备。

```
body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  使用聚合填充物。根据您的使用情况，这可能是最后的资源选择。这是一个 npm 包，链接如下:[https://www.npmjs.com/package/font-family-system-ui](https://www.npmjs.com/package/font-family-system-ui)

### 不跟踪

这可能是一个有争议的问题。但用户不希望你这么做的唯一事实，应该是停止监控他们行为的足够理由。这是一种观点，但是隐私是一种人权。

[不跟踪 API](https://developer.mozilla.org/en-US/docs/Web/API/navigator/doNotTrack) 是除 SafariT5 之外的现代浏览器**支持的**

下面是一个实现可能的样子:

```
/**
 * "1" if DNT is enabled
 * "0" if the user opted-in for tracking
 * "unspecified" otherwise
 **/ 

if (navigator.doNotTrack === 0) {
    // Initialize Google Analytics scripts
} else if (!navigator.doNotTrack) {
    // Ask user if it is ok to track
} else {
    // DO NOT TRACK
} 
```

Enter fullscreen mode Exit fullscreen mode

这里是[不要跟踪 MDN](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/doNotTrack) 。

### 默认为用户首选语言

根据您的应用程序，您的应用程序中可能有国际化(i18n)实现。如果支持，有一种方法可以默认用户的语言，而不是从他们的 IP，位置或你的应用程序偏好来推断。

[https://developer . Mozilla . org/en-US/docs/Web/API/navigator language/language](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorLanguage/language)

### 用户`prefers-color-scheme`

`prefers-color-scheme`是一个媒体功能，顾名思义，允许我们检测用户是否请求系统使用浅色或深色主题。

这是在 Firefox 67 上发布的[，在 Safari 12.1 上支持，但在撰写本文时，](https://hacks.mozilla.org/2019/05/firefox-67-dark-mode-css-webrender/)[支持仍然在大多数现代浏览器中缺失](https://caniuse.com/#search=prefer)。

这是 MDN 上 [`prefers-color-scheme`的链接。](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme)

### 用户`prefers-reduced-motion`

类似于上一点，我们可以检测用户是否喜欢更少的动画。这可能是由于可访问性问题，或者仅仅是偏好。我明白为什么如果你有丰富的 CSS 动画、3D 图形或 VR，这可能是一个问题。

CSS 中的实现相当简单，下面是代码的样子:

```
.animation {
  animation: vibrate 0.3s linear infinite both; 
}

@media (prefers-reduced-motion: reduce) {
  .animation {
    animation: none;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

更多资源:

[`prefers-reduced-motion`在 MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) 上。
如果你想深入了解这一点，这里有一篇很棒的文章:[https://CSS-tricks . com/introduction-reduced-motion-media-query/](https://css-tricks.com/introduction-reduced-motion-media-query/)

这就是所有的人，感谢你花时间阅读这篇文章。你可以在[https://dev.to/papaponmx](https://dev.to/papaponmx)上阅读我的其他帖子，或者在推特上向[问好。](https://twitter.com/papaponmx)

干杯。
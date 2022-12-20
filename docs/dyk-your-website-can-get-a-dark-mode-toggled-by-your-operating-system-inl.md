# DYK:你的网站可以通过操作系统切换到黑暗模式吗？

> 原文：<https://dev.to/rugk/dyk-your-website-can-get-a-dark-mode-toggled-by-your-operating-system-inl>

您的网站可以有明暗模式，由您的操作系统(OS)或浏览器自动切换。

## 让你的网站变暗

这里有个窍门:你可以使用新的 [`prefers-color-scheme`](https://developer.mozilla.org/docs/Web/CSS/@media/prefers-color-scheme) 媒体查询特性，在媒体查询等级 5 中指定[。](https://drafts.csswg.org/mediaqueries-5/#descdef-media-prefers-color-scheme)

您只需包含一个 CSS，如下所示:

```
@media (prefers-color-scheme: dark) {
  body {
    background: #333;
    color: white;
  }
} 
```

这将例如，只是做一些简单的颜色在你身上的 CSS 变化。

## 通过 JavaScript？

您还可以使用 [`.matchMedia()`](https://developer.mozilla.org/docs/Web/API/Window/matchMedia) 请求该物业的状态(就像任何其他媒体查询一样)。例如，这将返回状态:

```
> window.matchMedia("(prefers-color-scheme: dark)").matches
false 
```

(在本例中，黑暗模式将被禁用。)

## 这里面有蹊跷，不是吗？

是的，和许多好东西一样，这里也有一些限制…

这个特性[只被 Safari 12.1 和 Firefox 67 支持](https://caniuse.com/#feat=prefers-color-scheme)，但是我猜浏览器支持会增长，因为它是标准化的。

## 提示

*   有一篇由*安迪·克拉克*写的关于[如何为黑暗模式设计/调整你的网站](https://stuffandnonsense.co.uk/blog/redesigning-your-product-and-website-for-dark-mode)的博客文章。
*   对于最近刚刚发布的 Firefox 67，我创建了一个名为**“黑暗网站强迫者”** 的[插件，你可以用它直接在你的浏览器中切换设置。注意，它对自己能做什么有一些限制(见附加描述)，并随时敦促](https://addons.mozilla.org/de/firefox/addon/dark-mode-website-switcher/?src=external-devto) [Mozilla 提供更好的 API](https://bugzilla.mozilla.org/show_bug.cgi?id=1547818) 。当然[是开源的](https://github.com/rugk/website-dark-mode-switcher)。
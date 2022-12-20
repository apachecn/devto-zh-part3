# 有用的 CSS 媒体查询功能

> 原文：<https://dev.to/ananyaneogi/useful-css-media-query-features-o7f>

一个典型的媒体查询由一个**媒体类型**(屏幕、印刷品、语音等)和一个或多个表达式组成，涉及**媒体特征**，可解析为真或假。
如果媒体查询中指定的媒体类型与显示文档的设备类型相匹配，并且媒体查询中的所有表达式都为真，则查询结果为真。当媒体查询结果为真时，应用相应的样式。

*注意:*如果没有指定媒体类型，默认为`all`类型，即对应的样式将适用于所有设备。

## 媒体特性

媒体特征描述了用户代理和显示文档的设备的具体特征。这些是可选的。

这是我遇到的一些有用的媒体功能-

### 1。长宽比

纵横比是视口的宽高比。

```
/* Exact aspect ratio */
@media (aspect-ratio: 2/1) {
    ...
}

/* Minimum aspect ratio */
@media (min-aspect-ratio: 16/9) {
 ...
}

/* Maximum aspect ratio */
@media (max-aspect-ratio: 8/5) {
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个使用纵横比的实际例子-

```
img {
    display: block;
    margin: auto;
    width: 100vw;
    height: calc((9/16)*100vw);
}

@media (min-aspect-ratio: 16/9) {
    img {
        height: 100vh;
        width: calc((16/9)*100vh);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[查看工作演示](https://jsfiddle.net/ananyaneogi/x70wy25b/4/)。增加/减少窗口的高度来观察它的效果。

* * *

### 2。方向

有两个方向值-

*   `portrait`:视窗为纵向，即高度大于或等于宽度。

*   `landscape`:视窗为横向，即宽度大于高度。

```
 @media (orientation: landscape) {
    /* landscape styles */
}

@media (orientation: portrait) {
    /* portrait styles */
} 
```

Enter fullscreen mode Exit fullscreen mode

这种媒体特性有助于响应式设计。我们可以将它与宽度和高度等其他媒体特征一起使用。

```
@media screen and (min-height: 640px) and (orientation: portrait) { 
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 3。`display-mode`

显示模式用于测试应用程序的显示模式。根据[规格有 4 种类型。](https://w3c.github.io/manifest/#the-display-mode-media-feature)它是 Web App 清单规范的一部分。
无论 web 应用清单是否存在，功能查询都将适用。

查看这篇文章，了解这个的实际用法- [制作全屏体验](https://developers.google.com/web/fundamentals/native-hardware/fullscreen/)

* * *

### 4。`hover`、`any-hover`和`any-pointer`

悬停 CSS 媒体特性可以用来测试用户的主要输入机制是否可以悬停在元素上。

我发现使用这个功能很有用，可以避免触摸设备在长按*可悬停*元素时模仿*悬停*状态的情况。

```
@media (hover: hover) {
  a:hover {
    background: blue;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`any-hover`:检查任何可用的输入是否可以悬停在元素上。

`any-pointer`:检查用户是否有任何指示设备(鼠标、手写笔)，如果有，那么它有多精确。
准确度用- `fine`、`coarse`、`none`来衡量

关于这个话题的一篇非常棒的文章-
[触摸设备不应该根据它们的大小来判断](https://css-tricks.com/touch-devices-not-judged-size/)

* * *

### 5。宽度和高度

这两个是非常常用的媒体功能，也是响应式 web 的重要组成部分。

```
/* Exact width */
@media (width: 360px) {
  body {
    ...
  }
}

/* Maximum width */
@media (max-width: 768px) { 
  body {
   ...
}

/* Minimum width */
@media (min-width: 992px) { 
  body {
    ...
  }
}

/* Exact height */
@media (height: 360px) {
  body {
    ...
  }
}

/* Minimum height */
@media (min-height: 640px) {
  body {
    ...
  }
}

/* Maximum height */
@media (max-height: 768px) {
  body {
    ...
  }
}

/* All the values can also be in rem, em, vw, vh */ 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### ⑥。`prefers-color-scheme`

这已在媒体查询级别 5 中引入。等全面落实了就有用了！
这用于检测用户是否要求他们的系统使用浅色或深色主题。之前我写了关于[在使用环境光](https://dev.to/ananyaneogi/enabling-dark-mode-on-websites-based-on-surrounding-light--3jel)的网站上启用黑暗模式，如果环境光 API 不可用，这可以是一个补充。

查看第 5 级[媒体查询中介绍的其他功能。](https://drafts.csswg.org/mediaqueries-5/)

*编辑:*正如这里的[所指出的](https://dev.to/equinusocio/comment/8lkb)，据[can use](https://caniuse.com/#search=prefers-color-scheme)透露，在 safari 的科技预览版中就有。

* * *

这些是我发现的几个有用的媒体功能。还有其他如`resolution`、`update`等。完整的名单可以在 [MDN 上找到。](https://developer.mozilla.org/en-US/docs/Web/CSS/@media)

如果你遇到了其他不太为人所知的媒体特性及其使用案例，请告诉我！
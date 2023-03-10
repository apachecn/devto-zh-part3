# 如何以智能的方式加载自定义字体

> 原文：<https://dev.to/snowleo208/how-to-load-your-custom-fonts-in-a-smart-way-ne3>

*这篇文章最初发表在我的[个人博客](https://blog.atrera.com/css/performance/2019/06/01/how-to-optimize-your-fonts-in-website)上。*

如今，网页的尺寸比以前更大了。由于带宽的提高，即使你在网站上有很大的图片，加载任何东西都很快。

然而，这可能会损害用户的上网体验。其中一个障碍是你花哨的字体。

## 问题

例如，您有一个多语言网站，每种语言都有自己的自定义字体，例如英语和日语。用户需要很大的带宽来加载你的网站。此外，他们会看到“隐形文本的闪光”或所谓的 FOIT。

如何解决这个问题？

* * *

### 1。使用现代字体(woff 和 woff2)

首先，回顾一下定义字体的 css 文件。应该是这样的:

```
/* roboto-regular - latin */
@font-face {
  font-family: 'Roboto';
  font-style: normal;
  font-weight: 400;
  src: url('../fonts/roboto-v19-latin-regular.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
       url('../fonts/roboto-v19-latin-regular.woff') format('woff'), /* Modern Browsers */
       url('../fonts/roboto-v19-latin-regular.woff2') format('woff2'), /* Super Modern Browsers */
       url('../fonts/roboto-v19-latin-regular.ttf') format('truetype'), /* Safari, Android, iOS */
       url('../fonts/roboto-v19-latin-regular.svg#Roboto') format('svg'); /* Legacy iOS */
} 
```

现在你可以看到，有许多字体类型，以支持不同的浏览器。`woff`和`woff2`用于现代浏览器，`truetype`和`embedded-opentype`用于传统浏览器等。

实际上，字体的加载顺序是基于 src 中的顺序。也就是说，如果你想让浏览器先加载`truetype`，你可以把它放在最上面。在这个例子中，浏览器将首先加载`eot`。

不幸的是，`eot`并没有像`woff`和`woff2`那样被优化，即没有被压缩。最好总是加载`woff`或者`woff2`，两者都是现代浏览器支持的，都有内置压缩。通过压缩，它们的文件大小相对小于`eot`等。

对于这个例子，最好改为:

```
/* roboto-regular - latin */
@font-face {
  font-family: 'Roboto';
  font-style: normal;
  font-weight: 400;
  src: url('../fonts/roboto-v19-latin-regular.woff2') format('woff2'), /* Super Modern Browsers */
       url('../fonts/roboto-v19-latin-regular.woff') format('woff'), /* Modern Browsers */
       url('../fonts/roboto-v19-latin-regular.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
       url('../fonts/roboto-v19-latin-regular.ttf') format('truetype'), /* Safari, Android, iOS */
       url('../fonts/roboto-v19-latin-regular.svg#Roboto') format('svg'); /* Legacy iOS */
} 
```

如果不支持`woff2`，浏览器将自动退回到下一个条目。

### 2。使用字体显示来加快加载速度

为了防止“不可见文本的闪烁”，最好在样式表中使用`font-display`。

```
font-display: auto;
font-display: block;
font-display: swap;
font-display: fallback;
font-display: optional; 
```

默认情况下，浏览器使用 auto，通常表示`font-display: block`，该设置将阻止浏览器在加载完自定义字体之前加载文本。这就是为什么用户会看到一闪而过的无样式文本。

其他设置`swap`、`fallback`和`optional`有不同的阻断周期，您可以选择适合自己的。

例如，你有一个使用自定义字体的标题，你只想用自定义字体显示它，你可以使用`fallback`。浏览器将隐藏文本约 100 毫秒，如果字体尚未下载，将使用后备文本。

仔细想想你需要什么，然后你应该能够发现你的网站的最佳选择。

> 如果你正在使用谷歌字体，现在你可以通过在谷歌字体链接的末尾添加`&display=<your-display-type>`来设置字体显示。比如:[https://fonts.googleapis.com/css?family=Noto+Sans+JP&显示=屏蔽](https://fonts.googleapis.com/css?family=Noto+Sans+JP&display=block)。

### 3。Unicode 范围子集设置

对于一个有不同语言的网站，例如，一个有日语和英语版本的单页应用程序(SPA ),通常都有不同的字体。如果你打开一个英文页面，但浏览器也加载了日文自定义字体，这是一种浪费，对不对？我们如何解决这个问题？

```
/* Japanese fonts example */
@font-face {
  font-family: 'Noto Sans JP';
  font-style: normal;
  font-weight: 400;
  src: url('../fonts/noto-sans-jp-v23-latin-regular.woff2') format('woff2'), /* Super Modern Browsers */
       url('../fonts/noto-sans-jp-v23-latin-regular.woff') format('woff'), /* Modern Browsers */
       url('../fonts/noto-sans-jp-v23-latin-regular.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
} 
```

解决方法:`unicode-range`。如果设置了 unicode 范围，浏览器将为每种语言加载最合适的字体。正常情况下，英语应该是`unicode-range: 0020—007F`。对于更多的语言，您可以在 [unicode-table](https://unicode-table.com/en/) 中查看。

### 4。在本地或 CDN 中托管您的字体

加载字体的瓶颈之一是速度。如果你需要从另一个服务器调用你的字体，比如谷歌字体，页面可能需要更多的时间来加载。使用[谷歌字体助手](https://google-webfonts-helper.herokuapp.com/fonts)下载所有的字体文件，并把它放在你自己的主机上。

当你的字体加载得更快时，出错的几率会更小。

### 5。预加载您的字体

大多数开发人员忘记的一个小问题是 preload 标签。为了让你的字体加载更快，你可以添加`preload`到你的字体文件中。遗憾的是，这一招并不是在所有浏览器中都可用，像 Firefox，仍然不可用`preload`。更多详情，请查看 caniuse.com 的

```
<link rel="preload" href="fonts/cicle_fina-webfont.woff2" as="font" type="font/woff2" crossorigin> 
```

* * *

## 最终

希望你喜欢这篇文章！欢迎分享你对 web 性能的想法或窍门:)

* * *

## 阅读更多

1.  [网页字体优化](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/webfont-optimization)
2.  [字体-显示- MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display)
3.  [字体-显示-CSS-技巧](https://css-tricks.com/almanac/properties/f/font-display/)
# Web 使优化

> 原文：<https://dev.to/yashints/web-font-optimisation-156a>

网络字体使我们能够拥有良好的设计、品牌、可读性和可访问性。此外，它们将带来可选择、可搜索、可缩放和高 DPI 的文本。然而，每一个优势都伴随着代价。

所有其他零件:

[关于 HTML 和 CSS 的第 1 部分](https://yashints.dev/blog/2018/09/29/web-perf-1)

[第 2 部分使用预加载/预取来增加加载时间](https://yashints.dev/blog/2018/10/06/web-perf-2)

[第三部分 JavaScript 技巧和窍门](https://yashints.dev/blog/2018/10/12/web-perf-3)

[第四部分图像优化](https://yashints.dev/blog/2018/11/12/web-perf-4)

## [T1】简介](#intro)

如今，人们大量使用网络字体，却没有意识到这会对整体页面速度产生多大影响。

这并不意味着你不应该使用它们，只是你应该知道如何使用优化的字体，以及如何加载以减少对页面加载的影响。

优化网络字体取决于你在哪里托管字体，你的网络应用程序的设计和后端服务器，你的技术能力和你的公司愿意在这方面投资多少。但是不要让这妨碍你遵循我将要介绍的技术，即使是对整体页面负载的一点点改进也能带来更好的用户体验。

## 找出您正在使用的字体数量

在你开始这项任务之前，你应该找出你的整个网站使用什么字体。使用 Chrome DevTools 的网络标签中的审计标签，并查看 HTTP 请求，你可以很容易地发现这一点。

进入选项卡后，单击过滤器图标，查找文件扩展名，如`woff`、`woff2`、`tff`、`otf`等:

[![Finding what web fonts you are using](img/655780778a71798f905833907f504dec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EerKdOqI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7b55tb3ovdl6oxqrsk6p.JPG)

## 决定多少才算多

研究显示，平均 67%的网页使用自定义字体，网站平均有 4 个网页字体 HTTP 请求。

一旦你弄清楚你使用了多少种字体，你就应该考虑你应该使用多少种字体。通常这些决定是由 UX 或设计团队做出的，但是你绝对可以和他们谈谈。

如果你在想多少才算多，经验法则是[三个是人群，两个最好坚持](https://engageinteractive.co.uk/blog/how-many-fonts-is-too-many-fonts)。

如果你在整个网站中使用较少的字体，不仅网络请求的数量减少，而且设计更加精简和一致。

## 选择你需要的子集

除非你有一个支持多种语言的国际网站，否则使用所有字符集是没有意义的。只选择您需要的和您使用的语言。

伊利亚·格里戈利克是谷歌的开发者倡导者和网络性能专家，[解释了如何将网络字体分成多个 unicode 范围，只提供你需要的内容](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/webfont-optimization)。

您可以使用 [Unicode-range 描述符](http://www.w3.org/TR/css3-fonts/#descdef-unicode-range)来指定一个范围值列表，它可以以三种不同的形式列出:

*   **单个码点**:表示单个字符。
*   **区间范围**:表示一个字符范围的码点的开始和结束。
*   **通配符范围** : `?`字符表示任意十六进制数字。

为了演示这种可能性，这就是你如何将[字体 Awesome](https://fontawesome.com/) 分成拉丁文和日文子集。一旦完成，浏览器将根据需要下载每个子集。

**注意:** Unicode 范围子集化对于亚洲语言尤其重要，亚洲语言的字形数量比西方语言多得多，典型的“完整”字体通常以兆字节而不是几十千字节来度量。

```
@font-face {
  font-family: 'Awesome Font';
  font-style: normal;
  font-weight: 400;
  src: local('Awesome Font'),
    url('/fonts/awesome-l.woff2') format('woff2'),
    url('/fonts/awesome-l.woff') format('woff'),
    url('/fonts/awesome-l.ttf') format('truetype'),
    url('/fonts/awesome-l.eot') format('embedded-opentype');
  unicode-range: U+000-5FF; /* Latin glyphs */
}

@font-face {
  font-family: 'Awesome Font';
  font-style: normal;
  font-weight: 400;
  src: local('Awesome Font'),
    url('/fonts/awesome-jp.woff2') format('woff2'),
    url('/fonts/awesome-jp.woff') format('woff'),
    url('/fonts/awesome-jp.ttf') format('truetype'),
    url('/fonts/awesome-jp.eot') format('embedded-opentype');
  unicode-range: U+3000-9FFF, U+ff??; /* Japanese glyphs */
} 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**`local()`指令允许你引用、加载和使用本地安装的字体。`url()`指令允许你加载外部字体，并允许包含一个可选的`format()`提示，指示所提供的 URL 引用的字体格式。

然而，有一个问题，并不是所有的浏览器目前都支持 unicode-range。幸运的是，大多数主流浏览器都支持它，你需要有一个后备选项。

如果你问我如何确定我需要哪些子集，答案是:

*   如果浏览器支持 unicode-range，那么它将自动选择正确的子集。您只需要提供子集文件，并在`@font-face`规则中指定适当的范围。
*   如果浏览器不支持此功能，那么您需要隐藏所有不必要的子集，也就是说，您必须手动指定所需的子集。

您可以使用名为 [pyftsubset tool](https://github.com/behdad/fonttools/) 的开源工具来生成子集。除此之外，一些字体服务允许通过自定义查询参数手动设置子集。

除了子集化，你还可以利用其他一些机会。假设你只需要一个谷歌字体来显示你的页面标题。您可以使用`&text=`语法:
来指定从该字体下载哪些字符

```
<link
  href="//fonts.googleapis.com/css?family=Roboto&text=log"
  rel="stylesheet"
/> 
```

Enter fullscreen mode Exit fullscreen mode

**警告:**你需要小心，因为这不是一个单词选择器，而是一个字符选择器。这意味着字符`l`、`o`和`g`将显示在机器人上。

[![This image shows that only the letter g is rendered with Roboto font](img/2960fd50802a83feed8e265650f89886.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NxWHn-6j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z82rhenlc3nndphmho0k.jpg)

## 提供优化的字体格式

网络上有四种主要的格式:

*   **嵌入式开放式(EOT)** :微软设计的这种格式，现在只有 IE 浏览器使用。
*   True Type Font (TTF) :一种自 20 世纪 80 年代后期就存在的格式，部分支持 IE。
*   **Web 开放字体格式(WOFF)**:2009 年开发的一种格式，本质上是 OpenType 或 TrueType，带有压缩和附加元数据。它享有广泛的支持，但在一些较旧的浏览器中不可用。
*   Web 开放字体格式(WOFF2) :对 WOFF 的一种改进，平均来说文件大小减少了 30%。对许多浏览器的支持仍在进行中。

没有一种格式适用于所有的浏览器，因此需要提供多种格式来提供一致的体验。来自谷歌开发者网站:

*   将 WOFF 2.0 版本提供给支持它的浏览器。
*   为大多数浏览器提供 WOFF 版本。
*   向旧 Android(4.4 以下)浏览器提供 TTF 变体。
*   为旧 IE(IE9 以下)浏览器提供 EOT 变体。

如果你的大多数用户使用现代的网络浏览器，你可以只指定两种格式:

```
@font-face {
  font-family: 'Roboto';
  src: local('Roboto'), local('Roboto'),
    url(fonts/Roboto.woff2) format('woff2');
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总是在本地检查字体

一些用户可能已经在他们的系统上安装了这些字体，那么为什么要强迫他们再次下载这些字体呢？您可以通过确保在字体规则的`src`属性中优先考虑`local()`来防止这种情况。

在上面的例子中，注意`src: local`是如何在`url`之前使用的。

## 优化加载和渲染

默认情况下，字体是延迟加载的，这意味着网络请求被延迟，直到渲染树被完全构建，从而导致延迟的文本渲染。

实现自定义字体加载策略需要三个挂钩:

*   `<link rel=preload>`
*   CSS `font-display`属性
*   字体加载 API

### 预载你的网页字体

如果该字体在页面中使用的几率很高，那么您可以使用平台特性:`<link rel=preload>`急切地加载该字体。

这将告诉浏览器应该提前获取字体，但不会指定如何使用它。您需要指定适当的 CSS `@font-face` :

```
<link
  rel="preload"
  href="/fonts/Roboto.woff2"
  as="font"
/> 
```

Enter fullscreen mode Exit fullscreen mode

```
@font-face {
  font-family: 'Awesome Font';
  font-style: normal;
  font-weight: 400;
  /* will be preloaded */
  src: local('Awesome Font'),
    url('/fonts/awesome-l.woff2') format('woff2'),
    url('/fonts/awesome-l.woff') format('woff'),
    url('/fonts/awesome-l.ttf') format('truetype'),
    url('/fonts/awesome-l.eot') format('embedded-opentype');
  unicode-range: U+000-5FF; /* Latin glyphs */
} 
```

Enter fullscreen mode Exit fullscreen mode

**警告:**不是所有的浏览器都支持这个功能，但是支持的也支持 WOFF2，所以你要用那个格式。

关于其他解决方案的更多信息，请参考[这篇伟大的文章](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/webfont-optimization)。

## 总结

简而言之，优化网页字体时，你必须考虑以下几点:

*   审计和监控你的字体使用。
*   子集你的字体资源。
*   为每个浏览器提供优化的字体格式。
*   在您的`src`列表中优先考虑`local()`。
*   使用`<link rel="preload">`、`font-display`或字体加载 API 自定义字体加载和渲染。
*   指定重新验证和最佳缓存策略

希望这有助于您朝着提高 web 应用程序性能的正确方向更进一步。
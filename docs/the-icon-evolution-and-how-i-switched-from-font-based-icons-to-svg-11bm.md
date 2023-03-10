# 图标的演变和“我如何从基于字体的图标切换到 SVG”

> 原文：<https://dev.to/marcel_cremer/the-icon-evolution-and-how-i-switched-from-font-based-icons-to-svg-11bm>

上周，我们决定从基于字体的图标集迁移到[羽化图标](//feathericons.com)——一种基于 SVG 的字体集。因为我一直在使用像 forever 这样的基于字体的图标，并且不得不阅读一些关于图标的一般知识，所以我将与你分享我所学到的东西。所以请和我一起踏上...

### 图标集的演变

很久以前，在一个没有 CSS 的世界里，图标是通过**图像**显示的。

而且是*好*。
直到...

人们意识到，图像的显示尺寸是非常不灵活的。此外，它们消耗了大量的带宽(成本很高)，如果过度使用，网站会变得很慢(那时，每个图像请求都会阻塞浏览器的主线程)。但幸运的是，CSS 来了。

所以人们开始使用所谓的**图像精灵**，一堆图标放在一张图像上(可缓存)。通过 CSS 定位，有可能提取部分图像精灵来显示特定位置需要的图标。

而且是*好*。
直到...

网页设计师不得不再次与图像作斗争。因为人们希望同一图标有不同的颜色和大小，图像精灵变得越来越大。可达性也成了一个问题。

因此，在 2012 年左右，人们有了一个新的想法:我们可以用不同的颜色显示文本，随心所欲地缩放文本，甚至在文本上应用阴影。我们也可以通过 CSS 定位在其他文本的“上方”绘制文本，因此我们可以通过组合另外两个文本来“制作”一个新的字符。所有这些，都是通过一个小文件来实现的，这个文件包含了如何绘制这些字符的定义。

但是如果它不包含字符呢？如果它包含图标呢？

基于字体的图标集就是这样诞生的。这些图标被精心绘制成字体集，每个“字符”代表一个图标。

通过书写相应的字符(例如通过 Unicode)或稍后通过连字，可以容易地访问它们。它们可以被着色、调整大小、组合，甚至 CSS 效果也可以在上面实现。

尽管字体不是为图标设计的，图标字体却有着长久而快乐的生活。

而且是*好*。
直到...

### SVG 中的一个小介绍

可缩放矢量图形(简称 SVG)是用 XML 描述二维矢量图形的标准。根据 caniuse.com 的说法，所有“现代”浏览器都支持它们。其他的可以通过[SVG 4 every one](https://github.com/jonathantneal/svg4everybody)等方式多填。

一个简单的例子是

```

    <circle cx="5" cy="5" r="4" fill="currentColor" />
 
```

Enter fullscreen mode Exit fullscreen mode

它用当前颜色绘制一个圆。

### 使用基于 SVG 的图标集的原因

下面是基于 SVG 的图标的好处(不完全)列表。

他们是...

*   快速上漆
*   比传统的基于图像或字体的图标集小得多
*   因为它们基本上是代码，所以可以扩展(例如，通过在上面的例子中添加一个矩形)
*   消耗更少的带宽，因为它们可以被压缩用于传输(毕竟这是一个文本定义...:) )
*   它们甚至可以在常规的标签中使用(尽管这限制了您调整宽度和高度的灵活性)
*   虽然基于字体的图标定位可能很棘手(因为它们被插入到伪元素上，例如在带有:before 和:after 选择器和 content-attribute 的 CSS 中)，但是基于 SVG 的图标可以作为一个元素本身来放置，并且可以在给定的维度上很好地绘制
*   SVG 支持抗锯齿，这使得它们通常比基于字体的图形更清晰。

### 我个人的决定:JavaScript vs 角度实现 vs 精灵

当我开始迁移到基于 SVG 的图标时，我首先发现的是一个构建 SVG 的 JavaScript 实现(第一段关于 [feathericons](https://github.com/feathericons/feather) )。然而，当我在 Angular SPA 工作时，我不想使用外部 JS 并产生开销。

所以我在 SVGs 路上的下一个“站”是，检查是否有人已经为 angular 构建了一些东西，当然，有人已经这样做了: [Angular Feather](https://github.com/michaelbazos/angular-feather) 支持按需导入单个图标，在 Angular 应用程序中需要的地方。不错！

但是等等...因为每个图标都被封装在一个角度组件中，所以我不能访问 SVG，因此，我不能修改图标部分的颜色，添加额外的路径到 SVG 或其他任何人可以想象的东西。

必须有另一种选择，如果我仔细阅读羽毛图标网站的其余部分直到最后，我会在一开始就看到它: **SVG 精灵**！

### SVG 精灵

SVG 精灵基本上是一个 xml 文件，其中存储了许多图标定义和相应的锚点。在羽毛图标的情况下，SVG sprite 大约是 50kb(未压缩)，这对于我的 Web 应用程序来说是非常可以接受的。

所以我在 css 中创建了基类，它描述了图标的整体行为:

```
.fi {
  width: 1em;
  height: 1em;
  stroke: currentColor;
  stroke-width: 2;
  stroke-linecap: round;
  stroke-linejoin: round;
  fill: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我选择了相对大小，所以图标根据字体大小在不同的地方应该有多高就有多高(就像它们基于字体的对应物一样)。

“笔画”定义了 SVG 定义的线条应该如何绘制。因为它被设置为“currentColor”，图标将使用其上下文的当前字体颜色。

在定义了这些基础知识之后，我就可以把我的图标换成它们的 SVG 等价物:

```
<use xlink:href="/assets/feather/feather-sprite.svg#user" /> 
```

Enter fullscreen mode Exit fullscreen mode

(注意#user，它是我想从 SVG sprite 中绘制的图标的锚引用)

我该说什么？它就是有效！

我甚至能够通过添加几个简单的 CSS 类来创建额外的助手类，用于小型、大型以及 2x、3x 和 4x 的原始大小。

我希望你也能从我的图标演变过程中获得一些东西。你更喜欢哪些，为什么？
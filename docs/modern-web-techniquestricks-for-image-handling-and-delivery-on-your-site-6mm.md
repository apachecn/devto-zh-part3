# 现代网络技术/技巧，用于网站上的图像处理和交付

> 原文：<https://dev.to/duranenmanuel/modern-web-techniquestricks-for-image-handling-and-delivery-on-your-site-6mm>

*本文原载于[EnmaScript.com](https://enmascript.com/articles/2018/11/20/modern-web-techniques-tricks-for-image-handling-and-delivery-on-your-site)T3，去那里可以获得更好的阅读体验。*

图片是网络上最常用的三种资产之一，另外两个是 JavaScript 和 T2 CSS。在当今世界，使用图像作为内容的一部分已经变得几乎不可或缺，以使用户更容易消化，图像无处不在，不应该被低估。在本文中，我将与您分享一些最知名的优化资产交付的做法，以及谷歌、Twitter 和脸书等大公司应用的最新和最重要的技术。

## 图像动画不要用 gif，用视频代替。

在上一届 Chrome Dev 峰会上，他们展示了一些*真正*令人惊叹的东西，其中一件引起我注意的事情是，看看我们认为理所当然的东西是如何大规模提高网站性能的。

让我们以 *Twitter* 和*脸书*为例，当你上传一个 gif 动画到这些平台时，我们假设它们会自动优化图片，以便给我们最好/最优化的体验(它们确实这样做了)，但可能不是以我们预期的方式...你知道吗，他们把动画 gif 图像转换成视频，使网站更有表现力？等等，什么？实际上是的，他们将 gif 图像转换成视频格式，因为视频的压缩算法更加复杂，并且[应用帧内压缩](https://en.wikipedia.org/wiki/Intra-frame_coding)，最终视频尺寸更小，请看:

 **[![](img/de89ef0a25432a8713d725837c9b0a9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iCRDVtgz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://enmascript.cimg/2018-11-20-facebook-case-study.gif)**

 ****推特**
[![](img/a653e63ddbeed67323ba43d9c79a1f98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IYQRyphi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://enmascript.cimg/2018-11-20-twitter-case-study.gif)

看看这些视频怎么会有一个标签写着“GIF ”,而实际上它们是视频？这当然是为了让用户知道最初用户共享了一个 *gif* 。现在有趣的部分来了，让我们看看这些数字，我自由地测量了当你的网站中有一个 **gif 图像和一个 MP4 视频**相比，对性能的影响有多好，结果非常好。

我最近[贴了一篇文章](https://enmascript.com/articles/2018/10/31/the-underestimated-power-behind-window-matchmedia#mediaquerysensor-a-powerful-and-lightweight-solution-to-the-rescue)用 gif 展示了一个例子，我最近也把它改成了 MP4 视频，看看这个:

**在被转换成 MPEG-4 之前**
[![](img/80031f475af70d5310a6db5a2633e612.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pq3I9ILl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://enmascript.cimg/2018-11-20-performance-1-before-mp4.gif)

图像的 gif 优化版本大约为 **1.5mb** (是的，这是优化过的)，浏览器花费了 **~11 秒**来下载完整的图像(使用快速 3g)。

现在，让我们通过使用 [ffmpeg](https://www.ffmpeg.org/) 将我们的 gif 转换成 MPEG-4。我将运行命令`ffmpeg -i image.gif image.mp4`，它将自动将我的 gif 转换成 mp4 视频，然后为了使视频看起来像 gif，我将使用带有下面属性的`<video>`标签:

```
<video autoplay loop muted playsinline></video> 
```

查看结果:

**被转换成 MPEG-4 后**
[![](img/1615c46214035136caf12f74fdfffc0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OQlrEyiT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://enmascript.cimg/2018-11-20-performance-1-after-mp4.gif)

嘣！就像这个视频的重量 **~350kb** ，大约是原始 gif 大小的 **23.3%** ， **76.7%** 比原始 gif 小，浏览器花了大约 **2.8 秒**下载它，不需要更多的文字，如果你在你的网站中使用动画 gif，你就有了一个任务。另外，如果你现在认为这很棒，想象一下在不久的将来，当所有浏览器都开始支持 *AV1 压缩视频格式*时，它比 WebM 大约好 **30%，比 MPEG-4** 大约好 **50%，你可以在这里了解更多信息[。](https://en.wikipedia.org/wiki/AV1#Quality_and_efficiency)**

## 为正确尺寸的屏幕提供正确尺寸的图像

正如你在过去几年中可能注意到的那样，网络不再是计算机的事情，这意味着我们不再需要提供大图像或资源，如果人们在手机或平板电脑上看到它，屏幕不允许图像以全尺寸显示，这是一个好主意，对于这种情况，有一个众所周知的 javascript 模块来实现这一点，它被称为 [Jimp](https://www.npmjs.com/package/jimp) ，根据你的堆栈，你可以使用不同的工具/模块来实现它。

动态生成图像后，您可以通过利用 HTML5:
的`srcset`属性来决定何时显示它们

```
<img srcset="img.jpg 480w, img-medium.jpg 768w, img-large.jpg 1080w"
     size="50vw" 
     src="img-large.jpg"
     alt="A great image" /> 
```

通过这种方式，我们让浏览器知道何时使用哪些图片。要了解如何使用这项技术，请查看 [MDN 响应图像](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)。

## 使用带有图片标签的 WebP 图像，以使用更好压缩的图像并获得浏览器之间的兼容性

*WebP* 格式已经存在有一段时间了，它并没有被老的浏览器完全支持，但是它已经有了一些不错的支持，如果我们对它使用`<picture>`标签，我们可以进一步扩展浏览器的支持。

我们为什么要费心去使用 WebP 图片呢？这个问题的答案很简单，WebP 图像比 PNG 和 JPEG 图像要轻 **25-35%** ，有时将 PNG 图像转换为 WebP 可以将它的大小减少接近 **45%** ，如果你想了解更多，请查看[谷歌](https://developers.google.com/speed/webp/docs/webp_study)的这项研究。好了，现在我们来分析一下下面的实现:

```
<picture>
    <source type="image/webp" srcset="image.webp">
    <source type="image/png" srcset="image.png">
    <img src="image.png">
</picture> 
```

`<picture>`标签的工作方式类似于`<video>`标签，但是对于图像来说，在它里面你可以通过使用`source`标签来指定你想要加载的图像，并且你可以添加一个`srcset`属性。那么，为什么将这个标签与 WebP 一起使用是好的呢？问得好，当浏览器读取 pictue 标签中的源代码时，它将只获取找到的第一个支持的图像格式，这意味着如果 WebP 在第一个`<source>`标签中使用，而它不被支持，您可以只指定 PNG 或 JPEG 回退，它会工作得很好，而且作为一个额外的安全步骤，您可以为不支持`<picture>`标签的旧浏览器使用`<img>`标签指定图像。

## 延迟加载不在关键路径中的图像

你可能已经知道这个技术了，但是延迟加载图片可能是在你的站点中提供图片时最重要的步骤之一(如果不是最重要的话),伟大的站点只在用户需要时才加载图片，而不是在此之前，它从最初的页面加载中节省了大量的时间和精力，从而使你的站点排名更好，用户更满意。javascript 中一个众所周知的处理延迟加载的包是 [lazysizes](https://github.com/aFarkas/lazysizes) 。这个想法是当用户与你的页面交互时，逐步加载图像，这避免了由于获取甚至可能不需要的资源而造成的网络压力。如何实现它的代码示例是:

```
<img
    data-sizes="auto"
    data-src="image2.jpg"
    data-srcset="image1.jpg 300w,
    image2.jpg 600w,
    image3.jpg 900w" class="lazyload" /> 
```

你只需要添加前缀为`data-`的属性和`lazyload`的类就可以了，这种技术不仅适用于图像，也适用于 iframes 和视频...

<mark>**建议:**如果你打算使用一个不同的模块，或者你正在考虑构建你自己的惰性加载脚本，确保脚本使用[交叉点观察器 API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) 而不是滚动事件，这将避免性能问题，并将提供更流畅的体验。</mark>

### 额外推荐:

使用有损压缩来压缩您的图像，理想的起点是原始图像质量的 80-85%,这样可以保留大部分细节，并将文件大小降低 30%到 40%,但这并不适用于所有情况，很多时候您可以根据网站中给定图像的类型和优先级来进一步压缩图像。帮助你完成这项任务的一个模块是 [imagemin](https://github.com/imagemin/imagemin) ，它有用于有损和无损压缩的插件，它总是建议使用有损压缩，但根据你的情况，你可能会发现无损压缩更合适。

好了，伙计们，我想这就是这篇文章，如果你喜欢它，一定要留下评论并与你的朋友分享，记住你可以在 twitter [@duranenmanuel](https://twitter.com/duranenmanuel) 上找到我，或者给我发电子邮件到 duranenmanuel@gmail.com。

下一集见。**
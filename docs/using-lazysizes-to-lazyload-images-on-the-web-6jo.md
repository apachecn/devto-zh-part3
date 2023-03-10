# 使用 lazysizes 在 Web 上加载图像

> 原文：<https://dev.to/arnellebalane/using-lazysizes-to-lazyload-images-on-the-web-6jo>

图像构成了许多网页的页面权重的大部分。它们让我们的页面看起来更愉快，但也会影响我们的页面加载时间，尤其是当它们的文件很大或者页面上有很多文件时，更不用说它们会不必要地消耗用户的带宽。解决这些问题的一个方法是**懒散地加载**我们的图像。

[![image](img/a450b9f1e7b6dc162e63a1a629efdac5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DF3sFgM5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mnlor8r2nhs5b5xkqn6o.jpg)

Lazyloading 意味着我们只在图像在页面中可见时才加载图像。例如，位于一篇长文章末尾附近的图像不需要加载，直到用户实际滚动到视图中。

我们可以使用一些现代的 Web APIs(如[交叉点观察器 API](https://blog.arnellebalane.com/the-intersection-observer-api-d441be0b088d) )用 JavaScript 实现我们自己的 lazyloading 功能，或者使用图像 lazyloading 库，如[**lazy sizes**](https://github.com/aFarkas/lazysizes)by[aFarkas](https://github.com/aFarkas)。我喜欢这个库的一点是，它很容易上手，而且性能很好，包含了大量惊人的特性。

# 示例网页

让我们创建一个带有图像的示例 Web 页面，稍后我们将使用 lazysizes 加载该图像。我们的示例页面将包含以下标记:

```
<p>Lorem ipsum dolor sit amet...</p>
<!-- 9 more lorem ipsum paragraphs -->

<img src="image.jpg" alt="Sample Image">

<!-- 3 more lorem ipsum paragraphs --> 
```

[![image](img/8866583392a2568d31bc743b1d8baaba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cEcksIUh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wor0lq7widpbpz9e7u3d.gif)

请注意 DevTools 的 Network 选项卡，因为页面已经加载，所以图像已经加载。让我们试着改变一下，只在它将要滚动到视图中的时候用 lazysizes 加载它。

# 基本用法

要开始使用 lazysizes，我们需要做的第一件事是加载 lazysizes JavaScript 文件:

```
<script src="http://afarkas.github.io/lazysizes/lazysizes.min.js"></script> 
```

也可以通过 npm ( `npm install lazysizes`)或 bower ( `bower install lazysizes`)安装。

其次，我们修改我们想要 lazyload 到的`img`标签:

1.  使用`data-src`属性代替`src`，并且
2.  添加`lazyload`作为它的一个类

```
<!-- Before: -->
<img src="image.jpg" alt="Sample Image">

<!-- After: -->
<img data-src="image.jpg" class="lazyload" alt="Sample Image"> 
```

结果呢？

[![image](img/19a0a4be4b366918cd57a0b884f0ab0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KY3ea4m---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ypxc0gfx5kpz3hllye49.gif)

请注意 DevTools 的网络标签，图片只有在将要滚动到视图中时才被加载。厉害！

# Lazyload 响应图像

我们可以使用`img`元素上的`srcset`属性或者使用`picture`元素在 Web 上提供响应图像。这使我们能够根据用户的屏幕大小为他们提供大小合适的图像。

我非常喜欢 lazysizes 的一个非常酷的特性，那就是它能够在没有任何额外配置的情况下加载这些响应图像。我们要做的就是用`data-srcset`替换`srcset`属性。

```
<!-- Before: -->
<img
    srcset="
        image-100.jpg 100w,
        image-300.jpg 300w"
    src="image.jpg"
    alt="Responsive Image"
>

<-- After: -->
<img 
    data-srcset="
        image-100.jpg 100w,
        image-300.jpg 300w"
    data-src="image.jpg"
    alt="Responsive Image"
> 
```

# 使用低质量的图像占位符

我们的 lazyloaded 图像在这一点上的一个潜在问题是，当用户的网络连接很慢时，图像仍然需要很长时间才能显示出来，这给我们的页面留下了一个空白。

我们可以通过显示一个小尺寸、低质量的图像作为占位符来解决这个问题，直到实际的图像加载完毕。因为它的尺寸非常小，这个占位符图像将会很快加载，让用户知道实际的图像是什么样子的。为此，我们添加占位符图像作为`img`标签的`src`属性。

```
<img 
    data-srcset="
        image-100.jpg 100w,
        image-300.jpg 300w"
    data-src="image.jpg"
    src="image-lqip.jpg"  <!-- eg a 20px-wide image -->
    width="300"
    height="200"
    alt="Responsive Image"
> 
```

添加`width`和`height`属性使得小尺寸图像占据实际图像的空间。这也可以通过 CSS 来完成。

[![image](img/80bc2dbd96327814d3e400985eba5d02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r1gWUSe6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f7a3vxa0oiwwirr9sanc.gif)

# 总结

到目前为止，我们已经了解了如何使用 lazysizes 加载基本用例的图像。随着我们所做的改变，用户将只在必要的时候下载我们页面上的图片。因此，我们的页面加载速度更快，因为它不需要等待大图像完成加载。我们还防止用户在他们还没有看到的图像上不必要地消耗他们的带宽。

然而，我们仅仅触及了 lazysizes 所提供的皮毛。它提供了更多的功能和不同的配置，以及如何将其用于更高级用例的其他模式，因此一定要查看该库的[文档](https://github.com/aFarkas/lazysizes)，让我们一起通过 lazyloading 我们的图像来改善我们的 Web 体验。

* * *

*这篇文章最初发表在我的个人博客上。[查看原文](https://blog.arnellebalane.com/using-lazysizes-to-lazyload-images-on-the-web-a0fbcbf58975)。*

感谢阅读这篇文章！欢迎留下你的评论，让我知道你的想法。我也写一些其他的文章，做一些关于很酷的网络内容的演示。你可以在我的博客和我的 T2 GitHub 简介上查看它们。祝您愉快！🦔
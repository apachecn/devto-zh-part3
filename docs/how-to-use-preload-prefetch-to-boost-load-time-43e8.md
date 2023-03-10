# 如何使用预加载/预取来提高加载时间

> 原文：<https://dev.to/yashints/how-to-use-preload-prefetch-to-boost-load-time-43e8>

最近，我有机会在悉尼 NDC 做了一个关于网络性能的演讲，得到了很好的反馈。

这启发了我就我在那次演讲中涉及的每个主题写了一系列的帖子，谁知道呢，也许有一天这些帖子中的每一个都会成为一次单独的演讲😃。

链接到所有其他部分:

[关于 HTML 和 CSS 的第 1 部分](https://yashints.dev/blog/2018/09/29/web-perf-1)

[第三部分 JavaScript 技巧和窍门](https://yashints.dev/blog/2018/10/12/web-perf-3)

[第四部分图像优化](https://yashints.dev/blog/2018/10/12/web-perf-4)

[第五部分网络字体优化](https://yashints.dev/blog/2018/10/12/web-perf-5)

好了，简单回顾一下，这里是第一部分的规则:

*   将 HTML 流式传输到客户端
*   发送最小的 CSS 并快速发送

在这一部分中，我们将了解如何在需要资源之前请求它们。你可能想知道为什么我们要这样做，答案是，如果你想发送最小的 CSS 到客户端，或者任何其他资源，你需要把它们分成更小的部分，然后分别加载。

有一些技术允许你像延迟加载那样做，但是使用延迟加载，用户必须等待(很可能是看着一个旋转器😏)直到资源被加载。

但是，我们可以做得更好。 [PRPL 图案中的一个字母](https://developers.google.com/web/fundamentals/performance/prpl-pattern/)代表`pre-cache`。一种有效的方法，在需要资源之前加载它们，缓存它们，然后在需要时使用它们。我们可以使用浏览器的`preload`和`prefetch`功能来实现这一点。

在我们深入探讨如何操作之前，让我解释一下它们的区别:

### 预载

告诉浏览器资源应该作为当前导航的一部分被加载，并且应该尽快开始获取。这个属性可以应用于 CSS、字体、图像、JavaScript 文件等等。当使用这个属性时，你还可以告诉浏览器这个文件的文件类型，这可以在一个`as`属性中设置:

```
<link rel="preload" as="script" href="super-important.js" />
<link rel="preload" as="style" href="critical.css" /> 
```

Enter fullscreen mode Exit fullscreen mode

所以到现在为止，你应该已经猜到`preload`在当前的路线中使用，并且通过这种技术获取的资源非常重要。使用`rel=preload`和`rel=stylesheet`的区别在于浏览器会提前知道这个资源，允许下载更早开始。

如果你正在为你的*样式表*尝试这样做，并且没有看到你的样式被应用，那是因为当使用`preload`时有一个陷阱，即浏览器下载资源，但是直到我们告诉它才应用它。为了让它工作，你需要一点 JavaScript:

```
<link
  rel="preload"
  as="style"
  href="critical.css"
  onload="this.rel='stylesheet'"
/> 
```

Enter fullscreen mode Exit fullscreen mode

现在的情况是，一旦文件被加载，样式就会被应用。

**注意:**这种技术用于当前页面中的资源。对于在其他页面/路径上使用的资源，您只需使用`link rel=stylesheet`，资源就已经在那里了。

### 预取

`<link rel="prefetch">`与`<link rel="preload">`的不同之处在于，它不是试图更快地加载关键资源，而是试图更快地加载非关键资源。

通过这种技术加载的资源是在加载当前页面、完成页面加载事件并且带宽可用之后加载的。这意味着它是延迟加载未来路线、资源或用户以后可能需要的资源的理想选择。如果使用分页，一些用例会获取下一页:

```
<link rel="prefetch" href="page-2.html" /> 
```

Enter fullscreen mode Exit fullscreen mode

当用户点击下一页按钮时，页面已经加载，应用程序感觉非常快。

要了解更多关于这些功能的用法以及其他有用的功能，请看一下 [Google developer 的资源优先级页面。](https://developers.google.com/web/fundamentals/performance/resource-prioritization)

## 何时使用这些

所以为了强调为什么我们需要了解这些技术，让我带你回到第一部分。CSS 是一个渲染阻塞元素，所以我们需要把它分解成尽可能小的块来渲染我们的主路径。然后，我们可以使用上述技术，或者稍后在同一页面中加载资源而不阻止呈现，或者预先加载我们可能的下一条路线所需的资源，并在需要时使用它。

希望这已经帮助你开始优化你的 HTML 和 CSS 加载。

下一篇文章将是关于`JavaScript`以及我们可以做些什么来提高我们的 web 应用程序的性能。

下次见
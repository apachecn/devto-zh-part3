# 延迟加载图像-完整指南

> 原文：<https://dev.to/rnanwani/lazy-loading-images---the-complete-guide-2665>

如今，图像对于每个网站和应用程序都至关重要。无论是营销横幅、产品图片还是 logos，没有图片的网站是无法想象的。遗憾的是，图像尺寸很大，这使得它们成为页面尺寸的最大贡献者。根据最新的 [HTTP 存档数据](https://httparchive.org/reports/state-of-images)，桌面上的中值页面大小为 1511 KB。图像几乎占了其中的 650 KB，大约占总页面大小的 45%。现在，由于我们不能抛弃图像，我们需要使我们的网页加载速度非常快。在本指南中，我们将讨论延迟加载图像，这是一种有助于改善页面加载时间和减少页面大小，同时仍然保留页面上所有图像的技术。

#### 快速浏览一下延迟加载实现了什么

在继续之前，这里有一个演示延迟加载工作的示例视频。请注意，当页面滚动时，灰色占位符是如何被实际图像替换的。

[https://www.youtube.com/embed/CPmNHj9a0JI](https://www.youtube.com/embed/CPmNHj9a0JI)

### 什么是懒加载图片？

延迟加载图像指的是 web 和应用程序开发中的一套技术，将页面上图像的加载推迟到稍后的时间点——在实际需要这些图像时，而不是预先加载它们。这些技术有助于提高性能、更好地利用设备资源并降低相关成本。

英语中的“懒惰”一词经常被归因于尽可能长时间逃避工作的行为。

类似地，只要不需要资源，延迟加载就会推迟页面上资源的加载。我们没有像通常那样在页面加载时就加载这些资源，而是将这些资源的加载推迟到以后真正需要它们的时候。

惰性加载技术可以应用于页面上几乎所有的资源。例如，在一个单页面应用程序中，如果一个 JS 文件直到后来才需要，那么最好[不要在最初](https://medium.com/front-end-hacking/lazy-loading-with-react-and-webpack-2-8e9e586cf442)加载它。如果事先不需要某个图像，那么在实际需要时再加载它。

在这里，我们将坚持懒惰加载图像，以及如何在您的网站上做好它。

### 为什么要偷懒加载图片呢？

延迟加载会推迟页面上不需要的图像的加载。当页面加载时用户看不到的图像，当用户滚动并且图像实际上变得可见时被加载。如果用户从不滚动，用户看不到的图像就永远不会被加载。

这有两个主要优点。

#### 1。性能改进

对于网站管理员来说，最重要的是更好的性能和加载时间。使用延迟加载，您可以减少最初需要加载到页面上的图像数量。更少的资源请求意味着下载更少的字节，以及对用户可用的有限网络带宽的更少竞争。这确保了设备能够更快地下载和处理剩余的资源。因此，与没有延迟加载的情况相比，页面可以更早地变得可用。

#### 2。成本降低

你的第二个好处是在运输成本方面。图像交付或任何其他资产的交付通常根据传输的字节数收费。如前所述，对于延迟加载，如果图像不可见，它永远不会被加载。因此，您减少了页面上传递的总字节数。尤其是对于从页面弹出或者只与页面顶部交互的用户。从您的递送网络传输的字节的减少降低了递送成本。随着我们在接下来的小节中探索延迟加载，这一点将变得更加明显。

### 哪些图片可以懒加载？

延迟加载的基本思想很简单——推迟加载任何现在不需要的东西。对于图像，它通常解释为，任何对用户来说不可见的图像都可以延迟加载。当用户向下滚动页面时，图像占位符开始进入视口(网页的可见部分)。当这些图像变得可见时，我们触发对它们的加载。

您可以通过使用 [Google Lighthouse 审计工具](https://developers.google.com/web/tools/lighthouse/)来找出哪些图像是延迟加载的候选对象，以及在初始页面加载时可以节省多少字节。该工具执行的审计有一部分专门用于[屏幕外图像](https://developers.google.com/web/tools/lighthouse/audits/offscreen-images)。你还可以使用 [ImageKit 的网站分析器](https://imagekit.io/website-analyzer)来识别你的网站是否使用了延迟加载，以及你的页面上其他关键的图像相关优化。

延迟加载不仅对良好的性能至关重要，而且对向用户提供良好的用户体验也至关重要。由于将性能和用户体验与延迟加载相结合既重要又具有挑战性，在我们研究了延迟加载映像的不同方式之后，我们将在本指南中继续更详细地讨论这个主题。

### 图片的惰性加载技术

网页上的图像可以通过两种方式加载——使用  标签或者使用 CSS `background`属性。我们将首先看两者中更常见的,  标签，然后再看 CSS 背景图片。

### 懒加载图片中的一般概念标签

延迟加载图像可以分为两个步骤

第一步是预先防止图像加载。对于使用`<img />`标签加载的图像，浏览器使用标签的`src`属性来触发图像加载。不管它是你的 HTML 中的第 1 张还是第 1000 张图片，并且远离屏幕。如果浏览器获得了`src`属性，就会触发图像加载。

[![](img/0c3aa867cb3fafa96b2c5373869e3752.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XEEwA110--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6ke6q6271vbbjlzxe8ac.png)

因此，为了 lazyload 这样的图像，将图像 URL 放在除了`src`之外的属性中。假设我们在图像标签的`data-src`属性中指定了图像 URL。现在`src`是空的，浏览器不会触发图像加载

[![](img/f7f72b1a0864cef6f4f4570b7fa6276b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JWuLK-Nr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l7969x19uwyg1zmun1w6.png)

既然我们停止了前期加载，我们需要告诉浏览器何时加载图像。为此，我们检查图像(即它的占位符)一进入视口，我们就触发加载。要检查图像何时进入视口，有两种方法。让我们用工作代码示例来看看这两种方法。

### 使用 Javascript 事件触发图片加载

在这种技术中，我们在滚动条上使用事件监听器，在浏览器中使用`resize`和`orientationChange`事件。当用户滚动页面时，scroll 事件是一个需要检查的明显事件。resize 和 orientationChange 事件对于延迟加载同样重要。当浏览器窗口大小改变时，resize 事件发生。当设备从横向模式旋转到纵向模式时触发 orientationChange 事件，反之亦然。在这种情况下，屏幕上可见的图像数量会发生变化。因此，我们需要触发这些图像的加载。

当这两个事件中的任何一个发生时，我们会发现页面上所有要延迟加载且尚未加载的图像。从这些图像中，我们检查哪些现在在视口中。这是使用图像的顶部偏移量、当前文档的顶部滚动和窗口高度来完成的。如果它已经进入了视窗，我们从`data-src`属性中选择 URL，并把它放入 src 属性中。这将触发图像加载。我们还删除了 lazy 类，该类标识为以后触发的事件延迟加载的图像。一旦加载了所有的图像，我们就删除事件监听器。

当我们滚动时，滚动事件会快速触发多次。因此，为了提高性能，我们添加了一个小的超时来抑制延迟加载函数的执行。

下面是这种方法的一个工作示例。

[https://codepen.io/imagekitio/embed/MBNwKB?height=600&default-tab=result&embed-version=2](https://codepen.io/imagekitio/embed/MBNwKB?height=600&default-tab=result&embed-version=2)

如果你已经注意到了，这个例子中的前三个图像是预先加载的。URL 直接出现在 src 属性中，而不是出现在`data-src`属性中。这对于良好的用户体验至关重要。因为这些图片在页面的顶部，所以应该尽快让它们可见。我们不能等待事件或 JS 执行来加载它们。

### 使用交叉点观察器 API 触发图像加载

交叉点观察器 API 是浏览器中相对较新的 API。这使得检测元素何时进入视口并在元素进入时采取行动变得非常简单。在前面的方法中，我们必须绑定事件，牢记性能并实现一种方法来计算元素是否在视口中。交叉点观察器 API 使这变得非常简单，有助于避免数学计算并提供出色的性能。

下面是一个使用交叉点观察器 API 延迟加载图像的例子。我们在所有的图像上附加了一个观察器，以进行延迟加载。一旦 API 检测到元素已经进入视口，使用`isIntersecting`属性，我们从 data-src 属性中选取 URL，并将其移动到 src 属性，以便浏览器触发图像加载。完成后，我们从图像中移除惰性类，并从图像中移除观察者。

[https://codepen.io/imagekitio/embed/BPXQZZ?height=600&default-tab=result&embed-version=2](https://codepen.io/imagekitio/embed/BPXQZZ?height=600&default-tab=result&embed-version=2)

如果您比较事件监听器和交叉点观察器这两种方法的图像加载时间，您会发现使用交叉点观察器 API，图像加载被触发得更快，但站点在滚动时并不显得迟缓。在涉及事件侦听器的方法中，我们必须添加一个超时来使其具有高性能，这对用户体验有轻微的负面影响，因为图像加载是在轻微延迟的情况下触发的。

然而，像浏览器中的所有新事物一样，对交叉点观察器 API 的[支持并不是在所有浏览器中都可用。因此，在不支持交叉点观察器 API 的浏览器中，我们需要退回到事件监听器方法。我们在上面的例子中已经考虑到了这一点。](https://caniuse.com/#feat=intersectionobserver)

## 懒加载 CSS 背景图片

在`<img />`标签之后，背景图片是在网页上加载图片最常见的方式。对于`<img />`标签，浏览器有一个非常简单的方法——如果图片 URL 可用，让我们加载图片。

对于 CSS 背景图片来说，就没那么简单了。要加载 CSS 背景图像，浏览器需要构建 DOM(文档对象模型)树和 CSSOM (CSS 对象模型)树，以决定 CSS 样式是否适用于当前文档中的 DOM 节点。如果指定背景图像的 CSS 规则不适用于文档中的元素，则浏览器不会加载背景图像。如果 CSS 规则适用于当前文档中的某个元素，则浏览器会加载该图像。

乍一看，这似乎很复杂，但是，这种相同的行为构成了延迟加载背景图像技术的基础。简单地说，我们欺骗浏览器不要将`background-image` CSS 属性应用到一个元素上，直到这个元素出现在视窗中。

这是一个惰性加载 CSS 背景图片的例子。

[https://codepen.io/imagekitio/embed/RBXVrW?height=600&default-tab=result&embed-version=2](https://codepen.io/imagekitio/embed/RBXVrW?height=600&default-tab=result&embed-version=2)

这里需要注意的一点是，延迟加载的 Javascript 代码仍然是一样的。我们使用交叉点观察器 API 方法，回退到事件监听器。诀窍在于 CSS。

ID 为 bg-image 的元素在 CSS 中指定了一个`background-image`。然而，当类 lazy 被添加到这个元素中时，在 CSS 中我们覆盖了`background-image`属性并将其设置为 none。由于将`#bg-image`与`.lazy`类结合在一起的规则在 CSS 中比仅使用`#bg-image`具有更高的优先级，浏览器最初将属性`background-image: none`应用于元素。当我们向下滚动时，交叉点观察器(或事件监听器)检测到图像在视口中，它删除了类`lazy`。这将更改适用的 CSS 并将实际的背景图像属性应用于触发背景图像加载的元素。

[![Download the full infographic](img/688cbf8a5075ba1a4e96e7648f8628d6.png)](https://ik.imagekit.io/demo/img/Lazy_Loading_Images_Infographic_S1h5Jc-HE.png?tr=f-png)

### 延迟加载图像带来更好的用户体验

延迟加载带来了巨大的性能优势。对于一个在页面上加载数百张产品图片的电子商务公司来说，延迟加载可以显著缩短初始页面加载时间，同时减少带宽消耗。然而，许多公司不选择延迟加载，因为他们认为这不利于提供良好的用户体验——初始占位符很难看，加载时间很慢等等。在这一节中，我们将尝试解决一些关于图像延迟加载的用户体验问题。

#### 1。使用正确的图像占位符

占位符是在加载实际图像之前出现在容器中的内容。通常，我们看到开发人员使用纯色图像占位符或单一图像作为所有图像的占位符。

我们在示例代码中也使用了同样的方法。我们所有的图像背景都是纯色的浅灰色。然而，我们可以做得更好，以提供更令人满意的用户体验。下面是一些使用更好的图片占位符的例子。

#### a .主色占位符

我们没有为图像占位符使用固定的颜色，而是从原始图像中找到主色，并将其用作占位符。这种技术已经在谷歌图片搜索结果和 Pinterest 中使用了相当长一段时间。

从 **[Manu.ninja](https://manu.ninja/dominant-colors-for-lazy-loading-images/)** 中选取的样本图像

[![](img/003a3502f8180671b39d082fc4e252ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Mezsust--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u904a13rtvtgx0xw7in0.gif)

这可能看起来很难实现。但是，实现这一点的一个非常简单的方法是首先将图像缩小到 1×1 像素，然后将其放大到占位符的大小——这是一个非常粗略的近似，但却是获得单一主色的一个简单而不繁琐的方法。使用 ImageKit，可以使用 ImageKit 中的链式转换获得主色占位符，如下所示。

#### 使用 ImageKit 的主色占位符图像 URL 示例

[![](img/8f3249425146efd933a354b2a72837fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0NZ7hOxf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/khkjtxanhhdqv62tq7yb.png)

与 12700 字节的原始图像相比，占位符图像的大小仅为 661 字节—**小了 19 倍**。并且它提供了从占位符到实际图像的更好的过渡体验。

这里有一个视频演示了这种效果是如何为用户工作的。

[https://www.youtube.com/embed/wJEoRz3P3EU](https://www.youtube.com/embed/wJEoRz3P3EU)

您可以在此处查看[使用主色占位符的工作示例和代码。](https://codepen.io/imagekit_io/embed/WgvmZj)

#### b .低质量图像占位符(LQIP)

我们可以进一步扩展上述使用主色占位符的想法。我们使用原始图像的非常低质量的模糊版本作为占位符，而不是使用单一颜色。它不仅看起来更好，还让用户对实际图像有所了解，并让用户感觉图像加载正在进行中。这对于改善可感知的装载体验是非常好的。这种技术已经被像 Medium.com 和脸书这样的网站和应用程序上的图片所利用。

#### 使用 ImageKit 的 LQIP 图像 URL 示例

[![](img/41a816b8e8c60ce2c8367629492dc3f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sbYIbxHq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oced0dp9j0d81prs8i53.png)

LQIP 的大小为 1300 字节，仍然比原始图像小 10 倍，在视觉体验方面比任何其他占位符技术都有显著改善。

这里有一个视频演示了这种效果是如何为用户工作的。

[https://www.youtube.com/embed/BXoNamwkqi0](https://www.youtube.com/embed/BXoNamwkqi0)

您可以在此查看使用 LQIP 技术的[工作示例和代码。](https://codepen.io/imagekit_io/embed/WgvmZj)

从上述两种技术的视频样本中可以清楚地看出，使用主色占位符或使用低质量图像占位符可以更平滑地从占位符过渡到实际图像，让用户知道用什么来代替占位符，并改善加载感觉。

#### 2。为图像加载增加一些缓冲时间

当我们在上面讨论触发图像加载的不同方法时，我们检查了图像进入视窗的时间点，即当图像占位符的上边缘与视窗的下边缘重合时，图像加载被触发。

#### 问题

通常，用户滚动页面的速度非常快，图像需要一些时间来加载并显示在屏幕上。在这种情况下，结合 load image 事件可能由于节流而延迟触发的事实，您经常会遇到占位符进入视口，用户等待几毫秒，然后图像出现的情况。这种延迟导致了糟糕的用户体验。

虽然使用交叉点观察器加载图像或使用低质量的图像占位符可以提供更好的加载性能和用户体验，但还有另一个简单的技巧可以用来确保图像在进入视窗时总是被完全加载——为图像的触发点引入一个边距。

#### 解

不是在图像正好进入视窗时加载图像，而是在图像距离进入视窗 500 像素时加载图像。这在加载触发器和视口中的实际条目之间为图像加载提供了额外的时间。

通过交叉点观察器 API，您可以使用`root`参数和`rootMargin`参数(作为标准 CSS 边距规则)，来增加被认为是找到“交叉点”的有效边界框。使用事件侦听器方法，我们可以使用一个正数来添加一些阈值，而不是检查图像边缘和视口边缘之间的差异是否为 0。

这里的示例[使用 500 像素的阈值来加载图像。](https://codepen.io/imagekit_io/embed/gdbYwV/)

从下面的视频中可以明显看出(监控网络请求，紧密显示在底部)，当滚动时，当第三个图像出现时，第五个图像被加载。当第四个图像进入视图时，第六个图像被加载。这样我们就有足够的时间让图像完全加载，而且在大多数情况下，用户根本看不到占位符。

[https://www.youtube.com/embed/Zb65sdby7NI](https://www.youtube.com/embed/Zb65sdby7NI)

如果你之前没有注意到，在我们所有的例子中，第三个图像(image3.jpg)总是在前面加载，即使它在视窗之外。这也是按照相同的原则完成的——稍微提前加载，而不是恰好在阈值加载，以获得更好的用户体验。

#### 3。通过延迟加载避免内容转移

这是另一个无关紧要的问题，如果解决了这个问题，将有助于保持良好的用户体验。

#### 问题

当没有图像时，浏览器不知道要在封闭容器中显示的内容的尺寸。如果我们不使用 CSS 指定它，封闭容器将没有尺寸，即 0 x 0 像素。然后，当图像被加载时，浏览器会调整封闭容器的大小以适合图像。布局中的这种突然变化会导致其他元素四处移动，这被称为内容移动。正如 Smashing Magazine 的[内容转移文章](https://www.smashingmagazine.com/2016/08/ways-to-reduce-content-shifting-on-page-load/) &视频所示，这对于用户来说是一种相当不愉快的体验，因为当图像加载时，内容会突然移动。

#### 解

这可以通过指定封闭容器的高度和/或宽度来避免，这样浏览器就可以用已知的高度和宽度绘制图像容器。稍后，当图像加载时，因为容器大小已经指定，并且图像完全适合，所以该容器周围的其余内容不会移动。

### 4。不要偷懒加载所有的图像

这是开发人员经常犯的另一个错误——懒惰地加载页面上的所有图片。这可能会减少初始页面负载，但也会导致糟糕的用户体验，因为许多图像即使在网页顶部也不会显示，直到 Javascript 执行完毕。

我们可以遵循一些一般原则来确定哪些图像应该延迟加载。

a.任何出现在视窗或网页开头的图像都不应该被延迟加载。这适用于任何标题图像，营销横幅，标志等。因为用户应该在页面加载后立即看到它们。此外，请记住，移动设备和桌面设备的屏幕大小不同，因此最初在屏幕上显示的图像数量也不同。因此，您需要考虑设备类型来决定预先加载哪些资源，延迟加载哪些资源。

b.任何稍微偏离视窗的图像都不应该被延迟加载。这是按照上面讨论的原则进行的——稍微提前加载。因此，让我们说，任何图像是 500 像素或一个单一的滚动从底部的视口也可以加载到前面。

c.如果页面不是很长，可能只有一个或两个滚动条，或者如果视图外的图像少于 5 个，那么可以完全避免延迟加载。就性能而言，它不会给最终用户带来任何显著的好处。您在页面上加载以启用延迟加载的额外 JS 将抵消延迟加载如此少量的图像所带来的好处。

### 懒惰加载的 Javascript 依赖

延迟加载的整个思想依赖于用户浏览器中 Javascript 执行能力的可用性。虽然大多数用户会在浏览器中启用 javascript 执行，因为这是目前几乎所有网站的基本功能，但您可能希望为不允许在浏览器中执行 javascript 或使用完全不支持 Javascript 的浏览器的用户做好准备。

你可以向他们显示一条消息，告诉他们为什么图像不能加载，他们需要切换到一个现代的浏览器或启用 Javascript。或者，您也可以使用 noscript 标记为这些用户创建一个可用的体验。使用

tag approach for such users has some gotchas. This [question thread on Stack Overflow](https://stackoverflow.com/questions/29145354/dry-lazy-loaded-images-with-noscript-fallback) does a great job addressing these concerns and is a recommended read for anyone looking to address this set of users.

网站上用于延迟加载的流行 Javascript 库
由于浏览器环境和实现细节可能因浏览器和设备而异，所以最好使用经过测试的库进行延迟加载。这里列出了一些流行的库和特定于平台的插件，可以让你用最少的努力实现延迟加载

[yall.js(又一个惰性加载器)](https://github.com/malchata/yall.js)–使用交叉点观察器并退回到基于事件的惰性加载。支持所有主要的 HTML 元素类型，但不支持背景图片。也适用于 IE11+。

[lazy sizes](https://github.com/aFarkas/lazysizes)–非常流行和广泛的功能。还支持响应图像 srcset 和大小属性。即使没有交叉点观测器也能实现高性能。

jQuery Lazy–一个简单的基于 jQuery 的延迟加载库。

[welt pixel Lazy Loading Enhanced](https://www.weltpixel.com/magento-2-lazy-loading-enhanced.html)–Magento 2 的一个扩展，用于延迟加载图像

Magento Lazy Image Loader–一个 Magento 1.x 扩展，用于延迟加载图像

Shopify 惰性图片插件-一个用于惰性加载图片的 Shopify 扩展。虽然是有偿的。

WordPress A3 延迟加载-WordPress 的图片延迟加载插件

如何测试懒加载是否有效？一旦你实现了延迟加载，你会想要检查你的网站上的图片行为是否如你所愿。最简单的方法是在 Chrome 浏览器中打开开发者工具。

转到网络选项卡>图像。在这里，当您第一次刷新页面时，只有要预先加载的图像才会被加载。然后，当您开始向下滚动页面时，其他图像加载请求将被触发和加载。您还可以在这个视图的瀑布列中注意到图像加载的时间。它将帮助您识别图像加载问题(如果有的话)或触发图像加载的问题。

另一种方法是在你完成修改后，在你的页面上运行谷歌 Chrome Lighthouse 审计报告，并在[“屏幕外图像”部分](https://developers.google.com/web/tools/lighthouse/audits/offscreen-images)下寻找建议。

#### 结论

在本指南中，我们已经涵盖了几乎所有与延迟加载图像相关的内容。如果正确实现的话，延迟加载将显著提高网页的加载性能，通过减少预先加载的不必要的资源来减少页面大小和交付成本，同时保持页面上必要的内容不变。你的用户也会喜欢它，当他们获得了更快的页面加载体验。

那么，你还在等什么？现在就开始惰性加载图像吧！
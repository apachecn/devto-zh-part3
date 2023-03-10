# CSS Flexbox Javascript 图像滑块——构建博客，第 3 部分

> 原文：<https://dev.to/njericooper/javascript-image-carousel-building-a-blog-part-3-3oa0>

与建立一个平滑滚动的单页网站相反，本周我为我的博客增加了单独的页面。这个 javascript 转盘使用简单的索引逻辑。图像包括覆盖文本的 CSS，HTML 易于扩展并适应您自己的项目。以下是我使用的代码:

### HTML

[![](img/3a213e5f22abdcbe125c051efb87d09a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x7KvnyfS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6pryoeeqpm9jdtlz6jat.png)

之前，我对所有的图片都使用 CSS 的“背景图片”属性，直到我发现这对于 SEO 来说很糟糕。这一次，我将图像嵌入到 HTML 中，而不是在 CSS 中添加它们的 URL。

在“plant-carousel”容器中，我添加了另一个带有实用程序类的 div。实用程序类包含可能出现在网站多个位置的属性。(例如，如果我希望所有的标题在段落文本之上 20px，我将创建一个“space”类。在我的 CSS 中，我会使用“.”选择器来设计类的样式，如下所示:。space { padding-bottom:20px；})

我制作的实用程序类添加了滑动和淡入淡出动画。我们将看看这些类在 CSS 和 JS 文件中做了什么。为了使文本样式更加简单，我给了标题它们自己的类。一旦我关闭了 carousel div，我就为下一个和上一个箭头添加了标签。在图片下面，我创建了一个用于索引的容器。这个容器保存了代表每个图像位置的单个点。

### CSS

[![](img/905e3015881900f07010a8148037d542.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J4aXy4na--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d8ndy2bkhy9abf86jl09.png)

由于标题会覆盖图像，我在“植物标题”类中添加了一个 flex 显示。我把所有的东西都放在中间，确保宽度是 100%，这样它就可以跨越任何屏幕的整个宽度。“plant-carousel”类在 flex 行中保持箭头和标题在容器中水平对齐。

图像和箭头的样式非常标准。在开始讨论 Javascript 之前，我想强调一下“fade”和“slide”类。“幻灯片”有一个唯一的目的是隐藏所有的图像，直到它们被调用来显示。我一直在与混乱的屏幕做斗争，直到我明白了这一行是如何适应图像滑块的。我把显示屏设置为“无”，然后嘣。当页面加载并重新加载时，显示将重置。图像逐渐消失在屏幕上。从 0.4 不透明度到完全可见的 1 不透明度属性设置需要 1.5 秒。

### Javascript

[![](img/3d788ccb8932904d37b4e87132584b9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q6oseRyk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2gqurevgjoqm1adbmwvv.png)

滑块中至少要有一个图像才能工作，所以我设置了一个变量来保存这个位置。接下来，我定义了下一张幻灯片。它将前一张幻灯片的值设置为当前幻灯片的索引值加上下一张幻灯片的值。扩展后看起来是这样的:
slideIndex = slideIndex + n (n 代表幻灯片索引中显示的图像，也是 1)。

“currentSlide”将显示的幻灯片设定为 1，这样滑块将循环播放而不会结束。

“放映幻灯片”功能增加放映幻灯片及其对应的。当按下下一个或上一个按钮时，幻灯片被设置为“活动”。

TL:DR 看看我用 Javascript 和 flexbox 制作的图像转盘的代码笔。

[https://codepen.io/njericooper/embed/NVLWLV?height=600&default-tab=result&embed-version=2](https://codepen.io/njericooper/embed/NVLWLV?height=600&default-tab=result&embed-version=2)

在 Twitter 上与我联系，讨论更多关于开发的内容。如果您还没有阅读“创建博客”的前几部分，请阅读第 1 部分和第 2 部分:

[![njericooper image](img/51cb444e714df7284fd48c85d3c39381.png)](/njericooper) [## CSS Flexbox 导航条——构建博客，第 1 部分

### njeri Cooper 5 月 16 日 192 分钟阅读

#css #webdev #flexbox](/njericooper/flexbox-blog-part-1-the-nav-bar-442l)[![njericooper image](img/51cb444e714df7284fd48c85d3c39381.png)](/njericooper) [## 带有视差卷轴的 CSS Flexbox 英雄图像——构建博客，第 2 部分

### njeri Cooper 5 月 23 日 192 分钟阅读

#css #flexbox #webdev #showdev](/njericooper/css-flexbox-hero-image-building-a-blog-part-2-2mnl)

> ![Njeri Cooper profile image](img/2a1cb0dab76c4baad2afc36d105bf851.png)外国人库珀[@ njericoop](https://dev.to/njericooper)flex box JavaScript
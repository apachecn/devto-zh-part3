# 带有视差卷轴的 CSS Flexbox 英雄图像——构建博客，第 2 部分

> 原文：<https://dev.to/njericooper/css-flexbox-hero-image-building-a-blog-part-2-2mnl>

Flexbox 是一个 CSS 工具，用于在 web 上以行或列的形式布局部分页面。在我之前的文章中，我强调了 flexbox 的一些关键方面，以及如何用它来制作一个博客的导航条。点击这里查看帖子[。](https://dev.to/njericooper/flexbox-blog-part-1-the-nav-bar-442l)我添加了一个媒体查询，可以将酒吧布局转换为小于 768px 屏幕的汉堡菜单。[点击这里查看 flexbox 导航+汉堡菜单的代码](https://codepen.io/njericooper/pen/rgJZBo)。

在菜单下面，一个网站通常有一些图像或标题文本元素来介绍页面上的内容。对于我的博客，我创建了一个带有视差卷轴的 flexbox 英雄图像。视差是一种将网页的一部分固定在背景上，而其他元素在其上滚动的效果。

以下是我为我的英雄形象使用的代码:

### HTML:

[![made with Carbon](img/e76417fd06ede0c56a77646cec58e9c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ucfO8BuS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/finqnhog441bdw3skrhm.png)

### CSS:

[![flexbox parallax hero image](img/9457ff8596aa6320d9cb864871536b5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oo_OPyqG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23axng0i29ikpdg8bga2.png)

我用“hero-image”类创建了一个 div 标签，作为图像、文本和滚动效果的容器。这个容器的 CSS 在一列中显示 flexbox。结果会影响文本和任何其他放入带有“hero-image”类的 div 中的元素。

#### 背景图像

出于设计目的，我创建了一个 0.5 的线性渐变来覆盖图像，以帮助文本突出。将图像设置为不重复，以覆盖整个宽度、高度并固定到其容器的中心。图像将占据 50%的屏幕，但对于较小的设备，我将最小高度设置为 15em(或 240px)。

#### 正文

我通过编辑“hero-text”类将标题和子文本都设置为居中对齐。

[https://codepen.io/njericooper/embed/NVRQLe?height=600&default-tab=result&embed-version=2](https://codepen.io/njericooper/embed/NVRQLe?height=600&default-tab=result&embed-version=2)

如果你对 flexbox 有其他很酷的想法，[在 Twitter 上联系我](https://twitter.com/intent/user?screen_name=@njericooper)

> ![Njeri Cooper profile image](img/2a1cb0dab76c4baad2afc36d105bf851.png)Njeri Cooper[@ njericooper](https://dev.to/njericooper)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)Flexbox 英雄形象带视差滚动效果[codepen.io/njericooper/pe…](https://t.co/jJMEDNJywl)via[@ CodePen](https://twitter.com/CodePen)2019 年 5 月 23 日下午 16:35[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1131599514538786816)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1131599514538786816)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1131599514538786816)
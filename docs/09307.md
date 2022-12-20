# 用 chrome devtools 调试 wordpress 主题加载性能

> 原文：<https://dev.to/tahriadel/debugging-wordpress-theme-load-performance-with-chrome-devtool-4960>

### 简介

在这篇小文章中，我将向你展示我是如何解决 WordPress 主题的一个小性能问题
的，我们正在
[PixelDima](https://pixeldima.com/) 工作，向你展示如何使用 Chrome DevTools。

[![](img/87e2aacc3587d4b71face46a315510b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wznSPnTz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nkahlkptpuaqnne567a6.png)

### 调试

正如我在标题中提到的，我将在 Chrome 浏览器上使用 DevTool，如果你愿意，你可以使用标准的 Chrome 浏览器。

我将在一个本地环境下工作，你可以使用
飞轮的 [Local 来设置你的 WordPress。](https://localbyflywheel.com/)

让我们开始分析并重新加载页面以查看性能:

1-从 Chrome 主菜单打开 DevTools

点击右上角的菜单打开 Chrome DevTools，然后更多
*工具* >* 开发者工具*

[![](img/76caec36928511c575665a1064be815e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CDhQ3PBg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1dkfrk8sq5kwnxvw0ewb.png)

2 —转到**性能**选项卡。

3 —点击**重新加载**图标。

[![](img/e83a13cd902119e9a2b040bfe32447f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aadd55mM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5fo3yfq77yeax33dddid.png)

### 查看绩效档案

这是页面加载的性能概要。

[![](img/43c113977f333aae08e6504591a4ad5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1bnmfz83--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y15fsvtsf8zh040rt5zx.png)

放大红色条，其中大的“**挡住了**

正如您在下面看到的，我们可以确认这段时间很慢，该帧用了
`68.1 ms`来执行，并以`15 FPS`和 CPU 时间`277.66`运行。

[![](img/0921e9e566fccfe1c0ad320d2ce41c0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--de653CF5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tyxmo2q1o7u0tq6m3v64.png)

> *FPS 上方的红色条是一个警告，帧速率下降得太低，以至于
> 可能损害了用户的体验。*

要开始调查，请点击 **FPS** 图上方的`Red`条。如果你点击右上角有一个**红色三角**的
，你将在**摘要标签**中获得更多
小曲。

[![](img/91ef9cce5b1f22ff3512b2bf0d568989.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---j86lIY1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dtjdftl25ok0780zajlq.png)

您可以说子脚本正在运行`45.5 ms`，因此我们必须知道
哪些活动直接占用了总计最多的时间。DevTool 能够
显示记录中选定部分的活动。

[![](img/b7f61fd978cd7226e75fd8c660a66679.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PQoKj2Ij--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2duz4aefx3ivq9omvn5w.png)

上面你可以看到几乎所有的时间都花在了执行
对`vendors.min.js` (JS 文件，包含主题上使用的所有
脚本)和`Recalculate Style`的两个调用`e.refrech`上，点击`e.refrech`，你
会得到:

[![](img/6acd7ef493a932d4e35a5377370f8718.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OO2im6XS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gmqx2yrnlal1xp45xmdg.png)

DevTools 将您带到代码中触发事件的确切位置。由于
代码变小了，你应该把它打印出来。

[![](img/9c94839a11110773b3d6dbf5537a57eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ycRmSsI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/npqr4jo024hsiwry0lzw.png)

我在主题的所有 JS 文件中搜索了“`data-anchor-target`”，我发现
在 slow 后面的文件是`skrollr.js`我们用它作为视差图像，
但是在这个页面中，我们没有使用任何视差图像。

[![](img/c8a9983a4ed9e4e20f91e57b5ff28287.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AId3UyYX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oethcq7qiqy6exqknmpq.png)

在一个大型项目中，准确获取慢速帧背后的代码的最好方法是
过滤 JS 文件，并获取我们编写的定制 JS 代码。

所以为了打开树，你可以按住 Option (Mac)或 Control+Alt (Windows，
Linux)然后点击。(感谢@ChromeDevTools)

> ![Chrome DevTools profile image](img/9f51d60abec74619870f39f981e1dc84.png)Chrome DevTools@ chromedevtools![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ TahriAdel](https://twitter.com/TahriAdel)按住 Option (Mac)或 Control+Alt (Windows、Linux)然后点击19:15PM-08 2017 年 11 月[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=928340118091395077)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=928340118091395077)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=928340118091395077)2

[![](img/80282a52eae1b1bb5cabc96c37467ebf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QE4VMcYC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a5nyxjiboh36qyguak71.png)

现在，在打开树激活之后，我搜索了包含
我们的自定义 JS 的`main.min.js`，我打开了它，正如所料，它直接进入了
运行视差的代码(*，这里我们使用了 Skrollr 函数*)。

[![](img/36b83682649f939306e63e9072e8d2e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fn0EKhN_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5e5895cvrwwvvqcd07j0.png)

在代码中，你可以看到我们在没有任何条件的情况下调用了`Skrollr`
，这意味着即使我们没有使用它，它也在运行。所以为了解决这个问题，我们
必须添加一个条件，在调用 Skrollr 函数之前测试元素`$(".parallax-background")`是否存在
。

### 修复后的结果

[![](img/d9de67be91800751b5d5c9ee15fd2cac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2SpQ0htz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3dai9ri5xpw43jv5ipnt.png)

通过这个小修复，我们不再有红色条，我们设法保存了`25
ms`(从`68 ms`到`43 ms`，现在运行在`23 FPS`而不是`15 FPS`
，CPU 时间`247.06 ms`。

### 来源:

*   [https://moderndevtools.com/lessons/11](https://moderndevtools.com/lessons/11)
*   [https://developers . Google . com/web/tools/chrome-dev tools/evaluate-performance/reference](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/reference)
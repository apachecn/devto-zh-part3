# 我在网页可访问性方面的最大错误

> 原文：<https://dev.to/knheidorn/my-top-mistakes-with-web-accessibility-29lh>

随着我在熨斗工作时间的结束，我有了更多的自由时间来探索我的个人兴趣，然后在不可避免的求职和技术面试之前弄清楚我不知道的事情。当我神经紧张的时候，我经常试着用更广泛的话题来分解必要的主题，这些话题是相关的，但是我更感兴趣。我一直关注的一个领域是网页可访问性。我相信很多人都知道[万维网联盟](https://www.w3.org/)和[A11Y 项目](https://a11yproject.com/)。我没有给出这两个组织的总结，而是在下面强调了三个关键点，我发现这三个点最有趣，也最适合我自己的编码经验。

### 从顶部开始，字面意思:页面标题

我经常忽略给出主题页面标题。当我第一次开始构建自己的项目时，我会将页面命名为通用的名称，并且与我自己的网页没有什么关联。天真地，我认为让它变得有趣是厚颜无耻的，越随机让我的网页看起来越幽默。我一点也不知道这是屏幕阅读器抓取的第**个**项。因此，正确命名页面标题对于可访问性至关重要。接下来，我试着用我实际网页的唯一标题来写这些。对我来说这似乎很合理，但是我没有使用屏幕阅读器的经验。我很想听到提高 UX 的方法！

### 见差异:颜色对比

[![Spongbob Rainbow Gif](img/3b1bece39970cae3ea5eb36b9207ac58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UFl0TKhy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media2.giphy.com/media/SKGo6OYe24EBG/giphy.gif%3Fcid%3D790b76115cd2fb0858644644322077a6%26rid%3Dgiphy.gif)

对于我最喜欢的项目，我喜欢自由地设计我想要的页面，这通常包括选择我觉得对我的眼睛来说很容易的彩色粉笔。**大忌！我不知道网页的最低对比度应该至少是 4.5:1。较高的合同有助于那些有视觉障碍的人更容易阅读和使用您的网页。W3C 给出了如何用 [*IE WAT*](https://www.w3.org/WAI/test-evaluate/preliminary/#contrast) 检查对比度的分步指南。老实说，我已经很久没有使用 Internet Explorer 了，所以我找到了一个替代网站， [WebAIM](https://webaim.org/resources/contrastchecker/) ，它提供了一个更直观的系统来检查对比度。**

### 保持简单:键盘兼容性

这实质上是网页的所有功能都可以仅通过键盘来使用。这意味着身体条件限制不能使用鼠标的人可以使用按键来浏览页面，完成和提交表单。虽然我目前的设计相对简单，但在此之前，我从未测试过我的设计只支持键盘功能。就我个人而言，我偏爱一个好的模态，并且习惯于去掉通常位于页脚的默认关闭按钮。再说一次，这是我的一个糟糕的练习。我现在正在检查和重新添加这些按钮，使我的模态更容易使用。另一个需要注意的是，我以前并不知道，原生 HTML 控件本来就是可以通过键盘访问的。然而，定制的 CSS 和脚本通常需要内置这种功能。W3C 有一些[额外的有用信息](https://www.w3.org/WAI/perspective-videos/keyboard/)和用户故事，我觉得特别有趣。

### 带回家吧

在这一点上，我正在尽最大努力在我自己的代码中实现良好的 web 可访问性实践。我还试图回顾旧的项目来改进已经发布的代码，我发现这不是一件容易的事情。幸运的是，W3C 有一个快速[清单](https://www.w3.org/WAI/test-evaluate/preliminary/)来帮助指导像我这样的新手完成这个过程。除了更改 CSS 样式，有时还需要更改整个页面的实际功能，这使得整个过程比最初预期的更加繁琐。然而，这对于一个更有用、整体更流畅的 UX 来说只是一个小小的代价。

再次感谢阅读！现在享受这个可爱的宝宝为我们的无障碍网页欢呼。
[![Baby Raising Arms Gif](img/3b43724779104906daafe12c54540f9d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--k9W0G2Ke--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media3.giphy.com/media/4xpB3eE00FfBm/giphy.gif%3Fcid%3D790b76115cd30326374f49552e47eaf3%26rid%3Dgiphy.gif)
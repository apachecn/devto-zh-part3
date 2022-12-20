# 流体动画 CSS 渐变文本效果

> 原文：<https://dev.to/gabe/fluid-css-gradient-animation-3b2>

CSS 动画很牛逼。它们不仅能制作出优秀的数字艺术作品，还能让我们在网页中加入流畅的设计元素。最近，我在我的许多博客链接中集成了 CSS 渐变动画，给它们一种生动、流畅的感觉。见下面我的“近期文章”模块

[![](img/20d0437cb46c67d7c8cfa8c28194e843.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--izmoDRPj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dgbkiccdcmzkaaqjp78l.gif) 
*注意:gif 是为了演示而加速的*

整洁，对不对？并且实现起来相当简单。我们真正需要的是 CSS！
[![](img/edefb8f3a9d76ffd3e56d5a5bb13a5ca.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--tmra4tFh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vkte2wsl6qssuzx1igw0.png)

### 我们来解构一下

所以我的 CSS 中的第一件事是动画规则。
[![](img/6c904f549472a9cc24f71c5635278a15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zhQdJM9G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/22q5uwa3cey3rebl4vxw.png) 
这里我是说——播放名为**流**的动画，时长 **30 秒**。将动画设置为**渐入渐出**以获得更平滑的效果，并让动画无限循环**次**。

然后是背景规则。
[![](img/8c3c3e6d026880f3186212837ed4148f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lZOKC6kn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tnbnn83e1q5cxjlyt0gi.png) 
我设置我的背景为**线性渐变**并给它我想要的颜色。为了漂亮的渐变，当我想不出什么好的东西时，我经常使用 UI 渐变。然后我使用**背景大小**属性将背景拉伸到 300%的大小，给动画留出移动的空间。

然后我将背景剪辑设置为 T2 文本，并将 T4 文本填充颜色设置为透明，这样我的默认字体颜色就不会影响我的渐变。
[![](img/1b29f6bbc84f745d974a3ea0b0c420cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vJ1BVt6J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/swhf3ofsef0da4o65tnb.png)

现在开始实际的动画制作。我创建了一个名为 flow 的关键帧动画，并在 0、50 和 100%标记处设置了关键帧。
[![](img/9547e4181223578f28ab11f716713cd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vs6QPnOv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o4qwm1t4ranf88sfpw5s.png) 
最终结果:
[![](img/b55d1bbd9ad906c116f28bf2d39de530.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qf-jp4nu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ms7hu4num9zl7w9atgkz.gif) 
这样你就有了——一个漂亮的、流畅的文本渐变动画。CSS 提供的另一个有趣的设计元素。
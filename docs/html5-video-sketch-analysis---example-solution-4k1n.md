# HTML5 视频草图分析-示例解决方案

> 原文：<https://dev.to/yb/html5-video-sketch-analysis---example-solution-4k1n>

这一切都是从一位客户要求我创建一个工具开始的，在这个工具上，他可以在播放器上传到他现有的网络应用程序上的视频上绘制线条、圆圈、箭头、正方形和任何他想要的东西。他向我展示了一个已经在做这个的移动应用程序，它就是 shaaaarrrp。试图用很少的预算做同样的事情是一种挑战，但是不冒险的人什么都没有。

知识比金钱更有价值。此外，利用网络的力量来建立伟大的便携式的东西是我所寻找的。

我试图通过网络从现有的解决方案中构建一些东西，在不到一个小时的时间里，我得到了一个有趣的概念证明。

你会说这不是一个巨大的挑战吗？真正的挑战不在于此，而在于最终推出一款拥有用户想要的所有功能和出色用户体验的产品。我离那里很远。

有人想要那种视频上的素描工具做什么？

首先，我使用了一个名为字面意思([http://literallycanvas.com](http://literallycanvas.com))的工具。这是一个用 ReactJS 构建的小工具，它用几个绘图工具创建一个画布，有点像 paint，但更小。UI 远不是我想要的，但至少对于一个小的调整，我有一个工作演示。

然后，我使用 VideoJS([https://videojs.com](https://videojs.com))，这是一个众所周知的产品，能够很容易地创建一个控制栏。目前，它只适用于暂停和播放按钮。我们需要这样做，因为画布在视频播放器上，我们不能与控件交互。

可以从画布的高度动态减去几个像素，但仍然可以使用按钮和搜索栏，但这有两个问题:

*   html5 视频默认皮肤在整个视频上都有控件，当你暂停它时，播放按钮在视频中间，这对视频分析不是很好。
*   自定义 VideoJS 皮肤的 play 按钮在播放器的左上角，我们不能在画布上点击它。

所以，我们仍然需要从播放器外部播放视频。我的解决方案是创建一个自定义的控制栏，但我仍然需要看看我是否可以用自定义的 seekbar 平滑地移动视频，因为我在分析视频时需要它。

接下来，我希望能够保存草图，以便以后参考和使用。如果能够在一个视频上加载多个草图，并在我们需要时或者甚至在适合视频的时间线上切换它们，那就太好了。保存画布状态会很容易，但是我还不确定我是否可以在现有的或者定制的插件或者任何其他草图工具中轻松地重新加载它。它目前保留了绘图历史，我现在没有深入研究它。

最后，字面上的草图面板皮肤不适合我所看到的视频分析工具，所以如果我继续使用这个 javascript 库，我肯定需要创建一个新的皮肤。将工具栏放在视频外面或者将视频放在里面会更方便。拥有一个适合所有屏幕的东西可能很复杂。我们必须考虑到，这个工具将在一个 web 应用程序内使用，已经可以比较 2 个视频并排。用户界面会变得令人不知所措。

下面是小演示:[https://mentortechies.github.io/video-sketch-html5/](https://mentortechies.github.io/video-sketch-html5/)

下面是源代码:[https://github.com/mentortechies/video-sketch-html5](https://github.com/mentortechies/video-sketch-html5)

你建造过或者经历过这种事情吗？

已经使用了 Bootstrap 3，因为它是项目使用的当前 CSS 框架。
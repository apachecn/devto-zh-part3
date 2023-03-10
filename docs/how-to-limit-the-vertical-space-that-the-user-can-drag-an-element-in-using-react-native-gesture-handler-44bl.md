# 如何使用 react-native-gesture-handler 限制用户可以拖动元素的垂直空间？

> 原文：<https://dev.to/reidterror/how-to-limit-the-vertical-space-that-the-user-can-drag-an-element-in-using-react-native-gesture-handler-44bl>

我试图从屏幕底部建立一个滑动菜单。我面临的主要问题是设置用户拖动菜单的上限。

我看过 react-native-手势处理器 github 上的例子。试图摆弄[跳跃的例子](https://snack.expo.io/SkjAjkkhE)。但是仍然不知道我需要做什么来设置一个限制。

[当前设置。(零食链接)](https://snack.expo.io/r1gH1VCjN)

这给出了这个结果:

[![Collapsed menu](img/806ac1c1fe28d486f424fc6aa447bc69.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0hjEnaJr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/S3qZo2k.png)

当我向上滑动时，我得到:

[![Expanded menu, but out of bounds](img/db11d371d26e861d575d09ab6f70bf33.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zCmwbqBs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/t0M8ruJ.png)

当我一路向下滑动时，菜单消失了:

[![Collapsed menu, but out of bounds](img/82a787632a8c744b749c972a9c44e20f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fcPt5PpV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ewHkeVt.png)

我的目标是让菜单向上滑动时弹出到它的最大尺寸，然后向下滑动时缩小到它的标题。

[![Swiping up should end up like this: Expected result when swiping up](img/e376b7479824451d041aee09f7503b38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y94z2CnV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ldKZpgC.png)

[![Swiping down should end up like this: Expected result when swiping down](img/806ac1c1fe28d486f424fc6aa447bc69.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0hjEnaJr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/S3qZo2k.png)

提前感谢您可能提供的任何帮助，我们将不胜感激。
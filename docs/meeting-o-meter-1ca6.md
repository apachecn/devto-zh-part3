# 会议 o-米

> 原文：<https://dev.to/erikvullings/meeting-o-meter-1ca6>

你参加过一个没完没了的会议吗？每个人都得说点什么让它持续更久。好吧，如果这是你生活中的一个循环事件，也许 [meeting-o-meter](https://github.com/erikvullings/meeting-o-meter) 为你提供了一个解决方案。通过指定人数和他们的小时工资率，您可以测量(潜在的)浪费的每一秒的成本。至少，它让每个人都了解了参加这样一个会议的成本。它是开源的，所以请给它一个旋转和分叉来改进它。

## 如何使用

*   切换到设置(右上角菜单)来更改每小时的费用。
*   添加到屏幕下方，指定人数
*   按 start 开始测量成本

## 建筑

最初在 Visual Studio 2012 中开发，在 VS2015 中仍然有效。但由于它最初是在 2013 年开发的，我也包括了我用过的库，因为你可能再也找不到它们了。

## 关于

这是我在 2013 年开发的一个老项目，最近在一次谈话中提到了它。所以我查了代码，并在这里分享。如果你想继续我的工作，请叉项目，因为我目前使用我的大多数项目的 TypeScript，所以我可以在网上分享它们。例如，您可以:

*   将其移植到 web 环境中(我推荐使用 [mithril](http://mithril.js.org) 框架)。
*   添加页面以显示议程
*   添加一个页面来输入结果(这样您就可以进行成本/收益分析
*   发送/保存结果
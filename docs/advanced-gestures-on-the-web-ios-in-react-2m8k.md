# React 中的网络高级手势

> 原文：<https://dev.to/bmcmahen/advanced-gestures-on-the-web-ios-in-react-2m8k>

我最近一直致力于构建支持手势控制的 react 组件——比如滑动以打开/关闭，挤压以展开...基本上，所有的东西都是本地应用中的标准，但在网络上相对少见。

我的结论是:这实际上很有可能...而且好玩！事实上，这太有趣了，以至于我被一个小小的演示程序弄得神魂颠倒。我想，有什么比重新实现 iOS 主屏幕功能更好的方式来证明高级手势在网络上是可能的呢？

所以在这里: **react-iphone** 。

[![iOS home screen rendered on the web using react](img/f8f31365173ab265f1b349e5fba9b5e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xtTxJc9Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ynioepasr2ud8buhcyb5.png)

**[查看试玩](https://react-gesture-responder.netlify.com/)。**

**[浏览来源](https://github.com/bmcmahen/react-iphone)。**

尝试长按图标来启用拖放。在屏幕的各个部分向上、向下、向左或向右滑动会触发一些你在 iOS 中预期的行为。虽然不完全是这样，但我认为这证明了一点。

它是使用[反应手势响应器](https://github.com/bmcmahen/react-gesture-responder)和[反应弹簧](https://www.react-spring.io/)构建的。我还写了一篇关于如何使用这些工具实现“滑动到喜欢”功能的[简短教程](https://benmcmahen.com/building-react-components-with-gesture-support/)。

它在 iOS Safari 中也很好用(进入全屏即可)！还没有机会尝试 android，所以我想得到一些反馈。
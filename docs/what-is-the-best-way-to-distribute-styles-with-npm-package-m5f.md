# 用 npm 包分发样式的最好方法是什么？

> 原文：<https://dev.to/stereobooster/what-is-the-best-way-to-distribute-styles-with-npm-package-m5f>

假设我们有一个带有 React 组件的 npm 包。这些组件已经/需要样式化。有些样式是可覆盖的，例如，颜色或尺寸(又名主题化)。有些样式是不可重写的，例如，某些特定于组件的定位，想象一下在日历组件中定位单元格所需的样式。

## CSS 文件

我们可以通过提供 CSS 文件的老办法来实现。如果消费者使用网络包或包裹，他们可以这样做:

```
import { Component } from "package"
import "package/styles.css"; 
```

Enter fullscreen mode Exit fullscreen mode

如果没有 bundler，开发者需要在标题中放置样式

```
<link href="package/styles.css" rel="stylesheet"> 
```

Enter fullscreen mode Exit fullscreen mode

怎么做主题化？我们可以尝试使用 [CSS 变量](https://caniuse.com/#feat=css-variables)。但缺点是在这个过程中没有安全性。如果不是`--primarycolor: red;`开发者写`--primary: red;`，而是没有类型系统无运行时检查来防止错误会怎样。

样式表也公开了实现细节。样式表成为公共 API 的一部分。如果我们需要改变组件的内部标记，它会影响 CSS 的结构，如果是这样，我们将需要修改库的主要版本(因为它可能会破坏某些人的改变)。

我们需要为我们的组件支持唯一的说明符(类名或类似的)。我们可以尝试采用[的 Reach UI 方法](https://ui.reach.tech/styling/)，使用数据参数而不是类，例如`[data-reach-menu-item]`。但是，这种方法也不安全，如果开发人员键入错误的说明符或者说明符随着主要库的更新而改变，那该怎么办。

## CSS-in-JS

我们可以使用情感或 JSS 来设计组件。主题化的问题解决了。唯一分类器没有问题。这里有安全措施——我们可以使用 typescript 或添加运行时检查。

**但是**它会有运行时间，这可能是一个问题(从性能角度)。它会增加组件的大小(我们可以使用`peerDependencies`，但是这样的话没有 bundler 包是不可消耗的)。如果开发者使用不止一个组件，并且每个组件都有自己的 CSS-in-JS 解决方案，那该怎么办😱？我们需要为 SSR 做额外的配置。

## 其他

我们也可以使用`style`属性，但是我们需要写一些代码来做主题化(可能需要上下文)。

我们可以使用`className` prop 并假设消费者将使用 CSS 模块。同样，我们需要考虑如何将其传递给深度嵌套的组件(可能带有上下文？).

使用 CSS-in-JS 解决方案，它可以用零运行时解决方案编译，如 [linaria](https://github.com/callstack/linaria) 或 [astroturf](https://github.com/4Catalyzer/astroturf) ，所以不需要运行时的开发人员可以删除它，但仍然拥有所有 DX 的好处。我在实践中没见过这个，这只是我的梦想。

## 你会推荐什么？

如您所见，每种方法都有利弊。你会推荐什么？你尝试过解决类似的任务吗？分享一下你的经历。

> 照片由 Erik Eastman 在 Unsplash 上拍摄
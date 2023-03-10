# 在主题中使用函数

> 原文：<https://dev.to/mikewheaton/using-functions-in-themes-400f>

当你构建一个[风格的组件主题](styled-components-theming)时，你会遇到有一系列值的情况。例如，一组从白色到黑色的灰度或从微小到巨大的字体大小。

实现这一点的最简单的方法是在主题中给每个元素一个条目。

```
const theme = {
  neutralWhite: "#ffffff",
  neutralLighterest: "#fafafa",
  neutralLightest: "#f5f5f5",
  neutralLighter: "#eeeeee",
  neutralLight: "#e0e0e0",
  neutral: "#bdbdbd",
  neutralDark: "#9e9e9e",
  neutralDarker: "#757575",
  neutralDarkest: "#616161",
  neutralDarkerest "#424242",
  neutralAlmostBlack: "#212121"
} 
```

公平地说，我故意把这些弄得很糟糕。但是给这样的布景命名是个挑战。这些名字能传达每种颜色的相对亮度吗？当一个新的灰色出现并被添加到中间时，如果 T2 会发生什么？您好，`neutralDarkerest2`。

## 编号值

解决这个问题的一个方法是用数字代替名字。这就是[材料设计](https://material.io/design/color/the-color-system.html#tools-for-picking-colors)所做的，也是[面料](https://developer.microsoft.com/en-us/fabric#/styles/web/colors/neutrals)最近要做的。它解决了知道两种颜色如何联系的问题；数字越大，颜色越深。添加一种介于 400 和 500 之间的新颜色？命名为 450，然后继续更有趣的事情。

更简单的方法是将颜色命名为 0、1、2、3 等。如果需要新的颜色，您可以改变现有的颜色(例如，3 现在是 4)，并进行快速查找-替换来更新您的应用程序。这可能不适用于跨多个应用的设计系统，但对于原型设计或构建小型应用来说，这是一个很好的解决方案。

这可能看起来像:

```
const theme = {
  neutral0: "#ffffff",
  neutral1: "#fafafa",
  neutral2: "#f5f5f5",
  ...
}; 
```

虽然它会工作得很好，但它非常重复。让我们看看我们是否能做得更好。

## 使用数组

使用样式化组件进行主题化的一个好处是，主题和其他任何对象一样是一个 JavaScript 对象。虽然它通常包含像字符串和数字这样的原语，但是没有什么可以阻止我们将数组或函数放入其中。

```
const theme = {
  gray: [
    "#ffffff",
    "#fafafa",
    "#f5f5f5",
    ...
  ]
}; 
```

现在可以通过调用`${props => props.theme.gray[2]}`从组件样式的数组中获取一个值。更好！

## 从函数中调用数组

有些情况下，你真正想要的主题是一个函数。例如，Polished 库提供了一个 [modularScale 助手](https://polished.js.org/docs/#modularscale)，使得计算值的[相对比例](https://www.modularscale.com/?1&em&1.333)变得容易。

```
import { modularScale } from "polished";

const theme = {
  size(step) {
    return modularScale(step, "1rem");
  }
}; 
```

这将被称为`${props => props.theme.size(2)}`。这很像上面调用数组的方式，但是用括号代替了方括号。

虽然这很有意义，但是很难记住主题的哪些部分是数组，哪些是函数。因此，让我们编写一个小函数，它接受一个索引参数并返回该索引处的数组。

```
const theme = {
  gray(step) {
    return [
      "#ffffff",
      "#fafafa",
      "#f5f5f5",
      "#eeeeee",
      "#e0e0e0",
      "#bdbdbd",
      "#9e9e9e",
      "#757575",
      "#616161",
      "#424242",
      "#212121"
    ][step + 1];
  }
}; 
```

现在，我们可以在主题中引用数组或函数时使用相同的语法。

你可能已经注意到它正在访问`step + 1`的值。像大多数编程语言一样，JavaScript 使用[从零开始的数组](https://blog.kevinchisholm.com/javascript/javascript-array-length-always-one-higher/)，其中第一个元素位于位置 0。这让团队中的非开发人员感到困惑(设计文档永远不会从 0 开始，除非你坚持这样做)，所以我建议从 1 开始。对每个人来说都没那么麻烦。

## 下一步

*   如果你还没有尝试过样式化组件的主题化，请跟随我的教程。
*   尝试添加一个字号的渐变。例如 12px、14px、16px、20px、24px
*   如果您希望`myFunction(0)`从范围的中间返回一个值，而不是第一个值，该怎么办？你能更新这个函数以便调用`myFunction(-2)`得到更低的值吗？
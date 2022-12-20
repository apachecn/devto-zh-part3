# D3.js 的圆角条形图

> 原文：<https://dev.to/chooblarin/bar-chart-with-rounded-corner-by-d3js-2hid>

我喜欢 D3.js 和 Data 即。最近在想怎么画圆角条形图。我发现在 StackOverflow 中已经有一些不错的答案了。

*   [矩形堆栈溢出的一个角上的 svg / d3.js 圆角](https://stackoverflow.com/questions/12115691/svg-d3-js-rounded-corner-on-one-corner-of-a-rectangle)
*   [仅在 svg 堆栈溢出的一侧有圆角](https://stackoverflow.com/questions/34923888/rounded-corner-only-on-one-side-of-svg-rect)

简而言之，当你希望它变成圆角时，你可以用`<path>`代替`<rect>`。我写了我的 CodePen 样本作为复制。

[https://codepen.io/chooblarin/embed/KbWYvP?height=600&default-tab=js,result&embed-version=2](https://codepen.io/chooblarin/embed/KbWYvP?height=600&default-tab=js,result&embed-version=2)

`<path>`的`d`属性如下。

```
`
 M${x(item.name)},${y(item.value) + ry} a${rx},${ry} 0 0 1 ${rx},${-ry} h${x.bandwidth() - 2 * rx} a${rx},${ry} 0 0 1 ${rx},${ry} v${height - y(item.value) - ry} h${-x.bandwidth()}Z
` 
```

`rx`和`ry`是椭圆的半径，您可以随意更改。

* * *

我还用 D3.js 和 React 创建了一些例子😎
[https://github.com/chooblarin/react-d3-example](https://github.com/chooblarin/react-d3-example)
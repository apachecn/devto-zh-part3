# 带有渐变颜色的 D3.js 图表

> 原文：<https://dev.to/chooblarin/d3js-chart-with-gradient-color-4j71>

渐变颜色不仅看起来丰富多彩，而且还能提供丰富的信息，这取决于你如何使用它。但是我应该怎么用呢？

看下面的条形图。

[https://codepen.io/chooblarin/embed/wNyWLJ?height=600&default-tab=result&embed-version=2](https://codepen.io/chooblarin/embed/wNyWLJ?height=600&default-tab=result&embed-version=2)

这显示了 [IMDb 2018 年电影排行榜的票数](https://www.imdb.com/list/ls021348496/)。让我们在它上面使用渐变色。

首先，你可能会想到在`rect`栏中指定类似`"fill=url(#gradient)"`的内容。结果将如下。

[https://codepen.io/chooblarin/embed/MLQGEz?height=600&default-tab=result&embed-version=2](https://codepen.io/chooblarin/embed/MLQGEz?height=600&default-tab=result&embed-version=2)

那还不错。但是还有另一个选择。您可能希望颜色随着 Y 值的增加而改变。看下面。

[https://codepen.io/chooblarin/embed/omEeOY?height=600&default-tab=result&embed-version=2](https://codepen.io/chooblarin/embed/omEeOY?height=600&default-tab=result&embed-version=2)

完美！这使用了 [`<clipPath>`](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/clipPath) 元素的手法。

该技术实际上适用于其他图表(例如，如下所示的时序图)。

[https://codepen.io/chooblarin/embed/pGaVqq?height=600&default-tab=result&embed-version=2](https://codepen.io/chooblarin/embed/pGaVqq?height=600&default-tab=result&embed-version=2)

感谢您的阅读。快乐编码☺️
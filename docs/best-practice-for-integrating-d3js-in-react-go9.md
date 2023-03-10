# 在 React 中集成 D3js 的最佳实践

> 原文：<https://dev.to/wrrnwng/best-practice-for-integrating-d3js-in-react-go9>

当使用 D3.js 时，在较大的 web 应用程序中创建自定义数据可视化会变得复杂，因为 React 和 D3 都希望处理 DOM 操作。D3 还包括获取和解析数据的库，这些数据可能由 React 处理，然后通过 props 传递给可视化组件。

关于这一点，我找到的一篇比较好的文章是 Elijah Meeks 的[与 React & D3](https://medium.com/@Elijah_Meeks/interactive-applications-with-react-d3-f76f7b3ebc71) 的交互应用。在他的 [D3.js in Action](https://www.manning.com/books/d3-js-in-action) 一书的第 9 章中，他还有一章有更多的例子。

Meeks 展示了人们通常集成这两个库的两种常见方式，基本上是使用 React 作为 D3 可视化的包装器，或者使用 React 创建`svg`中的每个元素。他的首选方法是后者，因为他喜欢利用 React lifecyle 方法来更新可视化的元素。前一个例子需要编写额外的方法，React 生命周期方法将在数据或屏幕尺寸等发生更新时调用这些方法。

将 React 和 D3 分开的明显优势是，您可以非常接近任何 D3 示例。您还可以让 D3 管理加载它所依赖的所有数据。

我目前与一个不太熟悉前端开发和 React 的团队一起工作(这可能不常见，因为 Meeks 主张使用 React 来处理元素创建，因为他的团队比 D3 更熟悉 React ),因此使用 React 的生命周期方法创建元素的优势将会因为团队的大多数人被混合的逻辑所迷惑而丧失。

因为我在 2019 年写这篇文章，所以我想用一个利用钩子而不是生命周期方法的函数组件来写一个例子。

你可以跟随他的博文，但是如果你需要他的 useEffect 代码示例，请看下面。

**BarChart.js**

```
import { max } from "d3-array";
import { scaleLinear } from "d3-scale";
import { select } from "d3-selection";
import React, { useEffect } from "react";

const BarChart = ({ data, size }) => {
  let node;
  const createBarChart = node => {
    const dataMax = max(data);
    const yScale = scaleLinear()
      .domain([0, dataMax])
      .range([size[1], 0]);

    select(node)
      .selectAll("rect")
      .data(data)
      .enter()
      .append("rect")
      .data(data)
      .style("fill", "#fe9922")
      .attr("x", (d, i) => i * 25)
      .attr("y", d => size[1] - yScale(d))
      .attr("height", d => yScale(d))
      .attr("width", 25);
  };

  useEffect(() => {
    createBarChart(node);
  });
  return  (node = n)} height="500" width="500" />;
};

export default BarChart; 
```

Enter fullscreen mode Exit fullscreen mode

这里与米克斯在他的文章中的主要区别是:

*   `useEffect`调用`createBarChart`，这实际上与在`componentDidMount`和`componentDidUpdate`中调用该功能的结果相同。
*   我不知道他为什么添加所有的`"rect"`元素，然后删除它们，然后再添加它们。如果有人知道原因，请告诉我。我选择不这样做，而是在`append("rect")`之后立即调用`data`方法。
*   我不担心`this`的上下文，因为`node`被赋给了`BarChart`函数闭包内的`node`变量。
*   `useEffect`中的 lambda 也可以返回一个函数来进行清理。因为`svg`元素是由 React 呈现的，所以这在本例中是不必要的。
*   `createBarChart`函数的内容可以都在`useEffect`内部的 lambda 中。

`WorldMap.js`的例子更加简单，尽管他现在使用 React 来呈现`svg`中的每个元素。

**世界地图. js**

```
import { geoMercator, geoPath } from "d3-geo";
import React from "react";
import "./App.css";
import worlddata from "./world"; // geojson

const WorldMap = () => {
  const projection = geoMercator();
  const path = geoPath().projection(projection);
  const countries = worlddata.features.map((d, i) => (
    <path key={`path${i}`} d={path(d)} className="countries" />
  ));

  return (
    
      {countries}
    
  );
};

export default WorldMap; 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，我们不需要引用`svg`节点，因为我们使用 React 直接在由`WorldMap` React 组件返回的父`svg`元素中创建元素。不得不说，这的确很吸引我。我可以像任何其他 React 组件一样构建可视化，但也有缺点。与让 D3 处理相比，动画的性能似乎相当慢。

*原载于 2019 年 3 月 11 日[warrenwong.org](https://warrenwong.org/best-practice-for-integrating-d3js-in-react)。*
# JSX 有什么了不起的

> 原文：<https://dev.to/vjnvisakh/what-is-the-big-deal-with-jsx-3hgb>

JSX 代表 JavaScript XML。这实际上不是核心语言的一部分，而是一种语法扩展。

你看，正常反应是很难写的。例如，如果你想用核心 React 代码创建一个 h1 元素，它会是这样的-

```
var template = React.createElement(
  "h1",
  null,
  "Hello World"
); 
```

Enter fullscreen mode Exit fullscreen mode

然而，JSX 这样做使它变得简单了-

```
var template = <h1>Hello World</h1> 
```

Enter fullscreen mode Exit fullscreen mode

很酷，不是吗？JSX 允许你把普通的 HTML 写成 JavaScript，相信我，它让生活变得简单多了。

### **接下来:** [工具使用](https://dev.to/vjnvisakh/tools-to-use-286b)
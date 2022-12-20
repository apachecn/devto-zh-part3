# 什么是 DOM

> 原文：<https://dev.to/satansdeer/what-is-dom-2fll>

DOM(文档对象模型)是一个用于 HTML(或 XML)文档的编程 api。基本上，它是一个表示页面解析代码的对象。

它有树状结构，允许你遍历，访问和修改它的节点。

您可能熟悉 DOM API 方法:

```
document.getElementById(id)
document.getElementsByTagName(name)
document.createElement(name)
parentNode.appendChild(node)

// ... etc. 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么更新 DOM 代价高昂

当你更新 DOM 时，它会导致浏览器运行两个昂贵的操作:**回流**和**重画**。

当 DOM 布局发生变化时，会发生重排。它计算页面元素的尺寸和位置。它对每个元素都这样做，包括子元素。

**Repaint** 遍历元素，确定视觉变化(不透明度、颜色、轮廓、可见性)，然后更新屏幕上的像素。

> 因为更新 DOM 代价很高——React 使用了一种叫做虚拟 DOM 的技术。
# 没有文档查询的访问头、体

> 原文：<https://dev.to/seanmcp/access-head-body-without-document-queries-1bfl>

当我需要在文档的`head`或`body`中添加元素时，我通常会使用`document.querySelector()`，`document`方法的瑞士军刀:

```
const headNode = document.querySelector('head')
const bodyNode = document.querySelector('body')

console.log(`I'm the head:`, headNode)
console.log(`I'm the body:`, bodyNode) 
```

Enter fullscreen mode Exit fullscreen mode

这很有效，是查找 DOM 元素的常见模式。

今天我学习了`document.head`和`document.body`，它们分别提供了对文档中`head`和`body`元素的免查询引用！

这些方便的引用，我们可以用:
代替前面的代码块

```
console.log(`I'm the head:`, document.head)
console.log(`I'm the body:`, document.body) 
```

Enter fullscreen mode Exit fullscreen mode

不再创建引用`body`和`head`的变量；它们在`document`物体上总是可用的。

`document.head`和`document.body`在 IE8 之后的所有浏览器中都有:[查看 CanIUse.com](https://caniuse.com/#feat=documenthead)上的可用性图表。

编码快乐！
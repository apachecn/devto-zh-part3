# 方法 vs 函数 JavaScript 中函数和方法的区别是什么？

> 原文：<https://dev.to/tiffany/what-is-the-difference-between-a-function-and-a-method-in-javascript-3mkj>

这篇文章最初发表在我的博客上。

Abby Snopek 在推特上写道:

液体错误:内部

简而言之:一个*方法*就是一个属于一个类的**函数**。然而，在 JavaScript 中，*方法*是属于 ***对象*** 的**函数**。

JavaScript 中的一切都是对象；一个**函数**是一个对象；一个*数组*是一个对象。这是一个古怪的设计决定，但是*因为*JavaScript 中的大部分东西都是对象，我们可以用[原型继承](https://medium.com/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9)做一些简单的事情。关于*原型继承*的好处是我们可以同时操纵原型链*上的几个不同属性*。

请阅读上述文章以获取更多信息。另外，查看 MDN 上的[继承和原型链文章。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)

更新:我在没有咖啡的情况下写了这篇文章，并意识到我应该展示一些例子。我的爪哇☕.后将纠正这一点

* * *

### 报名简讯。没有垃圾邮件。我也讨厌那样。

[https://buttondown.email/tiffanywhite](https://buttondown.email/tiffanywhite)
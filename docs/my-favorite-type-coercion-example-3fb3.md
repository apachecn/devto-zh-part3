# 我最喜欢的类型强制例子

> 原文：<https://dev.to/karataev/my-favorite-type-coercion-example-3fb3>

```
[] == ![] // true 
```

Enter fullscreen mode Exit fullscreen mode

😱
空数组怎么等于非空数组？！没道理啊！
这就是为什么有很多关于`wtfjs`主题的文章和[视频](https://www.youtube.com/watch?v=et8xNAc2ic8)的原因。

大多数开发人员根本不喜欢使用双等号来避免这样的问题。
IDE 代码检查器可能会警告您不要使用`==`。
T3![IDE warn](img/ac69198c6a503742b07a905557b2e0f8.png)T5】

这是一个总是使用`===`的好决定。

但是我真的很喜欢凯尔·辛普森的 [YDKJS](https://github.com/getify/You-Dont-Know-JS) 系列和他的`why`态度。JS 为什么会这样？让我们潜入更深的地方！

`==`运算符允许类型强制。这意味着，如果两个被比较的值是不同的类型，JS 将在比较之前尝试将它们强制为相同的类型。

强制算法是什么？实际上，它非常简单，并在 [ECMAScript 语言规范](https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3)中定义。

一开始可能看起来很吓人，所以让我们使用本文开头的例子一步一步地遵循这个算法。

`[] == ![]` -我们感兴趣的表达式语句
首先，在实际比较之前，JS 引擎会执行所有的操作数。

第一步
`[] == false`
右操作数为布尔类型，所以返回比较结果`[] == ToNumber(false)`。

第二步
`[] == 0`
`[]`是对象，`0`是数字，`ToPrimitive([]) == 0`返回。

第三步
`"" == 0`
“是字符串，0 是数字，返回`ToNumber(x) == y`。

第四步
`0 == 0`
`true`返回。

JS 引擎在引擎盖下做了多少工作令人着迷。但它遵循清晰的算法，不涉及任何魔法。阅读规范，了解 JS 如何工作，成为更好的开发者😉

我真的很喜欢这个[工具](https://felix-kling.de/js-loose-comparison/#%5B%5D==!%5B%5D)，它可以根据 ECMAScript 语言规范分解任何 JS 比较表达式。
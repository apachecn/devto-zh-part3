# 理解 JavaScript 中的闭包

> 原文：<https://dev.to/skptricks/understanding-closures-in-javascript-3kn>

帖子链接:[理解 JavaScript 中的闭包](https://www.skptricks.com/2018/12/understanding-closures-in-javascript.html)

在本教程中，我们将讨论什么是 javascript 中的闭包。闭包是聚集在一起的函数以及对其周围环境的引用的集合。它给你一个内部函数的外部函数的作用域。范围是变量的上下文。

什么是终结？闭包是 JavaScript 中的一个特性，其中内部函数可以访问外部(封闭)函数的变量——作用域链。
闭包有三个作用域链:
它可以访问自己的作用域——定义在花括号之间的变量
它可以访问外部函数的变量
它可以访问全局变量

何时使用闭包？闭包有助于隐藏 JavaScript 中的实现细节。换句话说，创建私有变量或函数是很有用的。

[理解 JavaScript 中的闭包](https://www.skptricks.com/2018/12/understanding-closures-in-javascript.html)

[![](img/62db5f197d1f16172cb16578e0217c63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R1bU5paC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2.bp.blogspot.com/-CSfsC2igXTw/XB34fvCZXsI/AAAAAAAACRc/kVlEM5pojJcoTFEUgCPAQ-UGfJFefnmoQCLcBGAs/s640/closure.png)
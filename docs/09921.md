# 理解“this”在 Javascript -调用点中是如何工作的

> 原文：<https://dev.to/kenystev/understanding-how-this-works-in-javascript---the-call-site-3hj3>

如果您曾经接触过一些 javascript 代码，您可能会遇到`this`关键字的意外行为，比如它变成了`undefined`或者您可能声明了一个全局变量，或者您可能只是得到了另一个值，但不是您想要的。

如果你像我一样，有着坚实的面向对象编程(也称为 OOP)基础，并且习惯于使用类、类的方法、实例化、对象的实例、构造函数等等。然后你会注意到在 javascript 中，这种概念略有不同，容易混淆。

在这里，我将特别讨论关键字`this`的工作方式，以及它如何绑定到一个函数的不同方式，无论是显式的还是隐式的。绑定方法的优先级是什么？。

让我们开始吧；)

# 4 种装订类型`this`

1.  [默认绑定](https://dev.to/kenystev/understanding-how-this-works-in-javascript---default-binding-56m4)
2.  隐式结合
3.  显式绑定
4.  新绑定

它们与我们通过 OOP 从课堂上学到的东西没有任何关系。

在 Javascript 中`this`被绑定到一个对象，它依赖于**而不是函数被声明的地方**，而是函数被调用的地方。
所以我们应该看看函数是在哪里被调用的，来回答`this`引用了什么？

让我们深入一些代码:

```
function buzz() {
    // call-stack is: foo -> fizz -> buzz
    // then, our call-site is fizz

    console.log('buzz');
}

function fizz() {
    // call-stack is: foo -> fizz
    // then, our call-site is foo

    console.log('fizz');
    buzz(); // <-- call-site for buzz
}

function foo() {
    // call-stack is: foo
    // then, our call-site is in the global scope

    console.log('foo');
    fizz(); // <-- call-site for fizz
}

foo();  // <-- call-site for foo 
```

在分析调用栈以确定实际调用点时，您应该小心。

调用栈是按顺序调用的函数序列，那么调用点就是在实际函数之前调用的函数，例如:

| 最初的 | 第一次缴付 | 第二次呼叫 | 第三次呼叫 |
| --- | --- | --- | --- |
| (空栈) | foo() | 嘶嘶作响 | 嗡嗡声() |
|  |  | foo() | 嘶嘶作响 |
|  |  |  | foo() |

在堆栈的底部，调用第一个函数，在堆栈的顶部，调用最后一个函数。

然后，如果我们寻找`foo()`的调用点，因为下面没有任何东西，调用点将是全局范围。

对于`fizz()`,调用点应该是 foo。
对于`buzz()`来说，呼叫站点将会是 fizz。
安如此这般...

现在我们更好地理解了调用点是什么以及在哪里可以找到它，我们可以进入下一章: [***默认绑定***](https://dev.to/kenystev/understanding-how-this-works-in-javascript---default-binding-56m4)

感谢阅读；我将很快写下这个系列的下一篇文章。
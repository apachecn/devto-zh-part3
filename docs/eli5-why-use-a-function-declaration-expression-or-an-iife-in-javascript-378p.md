# ELI5:为什么在 JavaScript 中使用函数声明、表达式或生命？

> 原文：<https://dev.to/ryansmith/eli5-why-use-a-function-declaration-expression-or-an-iife-in-javascript-378p>

我见过 JavaScript 中的函数以不同的方式声明。我一直在努力寻找原因，但我找到的答案似乎只是陈述了它们之间的区别，而不是各自的优势。

**我对开发者的问题是*为什么*使用一种风格而不是另一种？**传统的函数声明有什么好处？有哪些代码示例可以说明这一点？

考虑下面的例子。

## 功能声明

对我来说，这是定义函数的“标准”方式。

我的理解是，函数声明是悬挂的，所以可以在函数定义之前调用。我希望一个函数有那样的行为，所以这是我创建函数的首选方式。

```
function calculateAverage() {
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 函数表达式

函数表达式将函数赋给变量。

我对函数表达式的理解是，它不会被提升，所以它只能在定义后执行，但我不确定这为什么有用。

```
const calculateAverage1 = function calculateAverage() {
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我也见过用箭头函数来做这个。

```
const calculateAverage1 = () => {
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 立即调用函数表达式(IIFE)

一个生命一旦被定义，它就会运行。

这个，我不太确定。为什么要将代码放在函数中？它允许创建一个作用域，但是我不确定什么时候会有用。

```
(function () {
    ...
})(); 
```

Enter fullscreen mode Exit fullscreen mode

谢谢！
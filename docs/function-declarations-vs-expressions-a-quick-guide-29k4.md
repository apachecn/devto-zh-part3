# 函数声明与表达式:快速指南

> 原文：<https://dev.to/canderson93/function-declarations-vs-expressions-a-quick-guide-29k4>

问题来了:这两种创建函数的方法有什么区别？

```
function a() {
    //This is known as a function declaration
    console.log("Hello");
}

var b = function() {
    //This is known as a function expression
    console.log("World");
} 
```

Enter fullscreen mode Exit fullscreen mode

这两种方法做*几乎*同样的事情，然而有一个主要的区别:

```
a(); // "Hello, World"
b(); // Error

function a() {
    console.log("Hello, World");
}

var b = function() {
    console.log("Hello, World");
} 
```

Enter fullscreen mode Exit fullscreen mode

与函数表达式不同，函数表达式的行为与其他变量赋值的行为非常相似，函数声明受一个叫做*提升的 JavaScript 特性的影响。*

提升是 JavaScript 将函数和变量声明移到它们作用域的顶部。这使得开发人员可以自由选择如何组织他们的代码，而不用担心函数是在哪里声明的。

## 我用哪个？

因为这两种方法基本相同，所以使用哪种方法并不重要，重要的是保持一致。

就我个人而言，我更喜欢在顶层使用函数声明，对任何内部函数使用函数表达式，就像这样:

```
function foo() {
    var bar = function() {
        console.log("Hello, World");
    }

    setTimeout(bar, 1000);
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一需要注意的是*由于提升，函数声明的行为和* `var`一样，所以你需要使用`let a = function() {}`让函数使用块作用域(或者`const`)。

如果你想进一步了解 JavaScript 中的函数，你可以查看一下我的闭包指南。

*嘶。如果你想得到更多这些，[你应该注册我的邮件列表](https://pages.convertkit.com/de362bddba/e411d0e335)*
# 到底什么是吊装？

> 原文：<https://dev.to/sarah_chima/what-is-hoisting-anyway-49a2>

提升是一个 JavaScript 开发人员经常会遇到的术语。你知道什么是起重以及它是如何工作的吗？好吧，让我们来看看你是否知道。在这篇文章中，我们将讨论什么是吊装，它是如何工作的，什么被吊装。

### 什么是吊装？

要理解什么是吊装，我们需要理解一些概念。

首先，通常认为 JavaScript 不是编译语言。相反，JavaScript 引擎在执行前编译代码。

第二，我们需要理解当我们想要获取变量中的值时可能发生的一些类型的错误。它们是引用错误、类型错误和未定义。当调用未声明的变量时发生。`TypeError`当值不是预期类型时发生。`undefined`当被调用的变量没有赋值或者没有初始化时返回。有了这些基本知识，我们现在可以理解什么是起重。如果您不理解这些，本文中的示例将帮助您更好地理解它。

**那么什么是吊装呢？**

提升是一个 JavaScript 概念，在代码执行之前的编译阶段，变量和函数声明被放入内存。这使得这些声明看起来好像被移动到了它们作用域的顶部，从而使它们在该作用域的任何地方都可用。请注意，这实际上不会发生。

让我们用一个例子来更好地理解这一点。

```
console.log(a)

var a = 2; 
```

看着上面的代码，你觉得会是什么结果？2?未定义或引用错误？打开浏览器控制台并运行代码。你得到了什么？

我拿到了`undefined`。我相信你也明白了。您可能会遇到引用错误，因为您在声明变量之前调用了它。这并没有发生，因为变量声明被提升到了代码的顶部。所以在执行过程中，代码是这样执行的。

```
var a;

console.log(a); //undefined

a = 2; 
```

有道理对吗？请注意，这只是发动机吊起的声明，作业不吊起。变量声明用`undefined`提升并初始化。这就是为什么我们得到了`undefined`而不是 2，因为赋值保持在它被赋值的地方。

此外，请注意吊装是根据范围进行的。因此，如果我们在函数中声明，变量将在函数范围内可用。如果我们在函数外部声明它，它将在全局范围内可用。如果我们在声明变量的范围之外使用变量，它将返回一个`Reference Error`。例如，如果我们执行下面的代码，我们会得到一个引用错误。

```
console.log(b); //Uncaught ReferenceError: b is not defined

function a() {
    var b = 2
} 
```

说到功能，是不是所有功能都吊起来了？我想我们需要一个上升的方向。

### 托管什么

函数声明被挂起。所以我们可以在代码中声明函数之前调用它。

```
foo(2); // 2

function foo(a) {
    console.log(a)
} 
```

函数表达式不被提升。如果我们在给函数赋值之前调用函数表达式，我们得到一个`TypeError`。

```
foo(2); // Uncaught TypeError: foo is not a function

var foo = function (a) {
    console.log(a)
} 
```

`foo`用`undefined`初始化，因此将变量作为函数调用会导致类型错误。

ES6 变量`let`和`const`呢？它们也被吊起来了吗？

是的，它们被初始化了，但没有像`var`一样用`undefined`初始化，它们保持未初始化状态。如果我们在给它们赋值之前使用它们，它们会返回一个`ReferenceError`而不是`undefined`。

```
console.log(b); //Uncaught ReferenceError: b is not defined

let b = 2; 
```

`const`同

```
console.log(a);//Uncaught ReferenceError: a is not defined

const a = 2; 
```

我们应该注意，我们不能在没有初始化的情况下声明`const`变量。所以下面的代码将抛出一种不同的错误。

```
console.log(a)

const a;
a = 4;

//Uncaught SyntaxError: Missing initializer in const declaration 
```

### 什么先升起？变量还是函数？

我们已经看到变量和函数声明都被提升了。他们中谁先被吊起来？让我们做一个小练习。看看下面的代码，你期望被打印出来的是什么？字符串还是函数？猜一猜，然后在你的游戏机上试试。

```
console.log(typeof foo);

var foo = 'hello';

function foo() {
    console.log(4);
} 
```

结果如何？我确定结果是`function`。这证明了两点:

1.  函数首先被提升，这就是为什么尽管变量是在字符串之前声明的，JavaScript 引擎仍然把它解释为函数。事实上，这就是引擎执行代码的方式。

```
function foo() {
    console.log(4);
}

console.log(typeof foo); //function

foo = 'hello'; 
```

如果 console.log 语句出现在变量声明之后，结果应该是`string`。注意变量声明(它是函数的副本)被忽略了。这就引出了第二点。

1.  用相同的变量名做重复的声明是一个坏主意。JavaScript 引擎会忽略重复的声明，这通常会导致混乱的结果。

让我们回顾一下本章所讨论的内容。

### 回顾

1.  提升是一个概念，其中变量和函数声明在执行过程中似乎移动到代码的顶部。这是因为变量和函数声明是在编译阶段处理的。

2.  所有变量都被提升。`var`被吊起，用`undefined`初始化。`let`和`const`被吊起，未初始化。

3.  函数声明是提升的，而函数表达式不是。

4.  在编译阶段，函数声明先于变量声明。

感谢您的阅读。
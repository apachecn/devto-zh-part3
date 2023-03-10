# 快速功能重构技巧

> 原文：<https://dev.to/grodier/a-quick-functional-refactoring-tip-4ifb>

*最初发布在我的个人网站上，[georgerodier.com](https://georgerodier.com/2019-5-23-quick-functional-refactor/)。*

让你的代码更具功能性有很多好处。但是，函数式编程入门有一个学习曲线。幸运的是，有了 JavaScript，您可以慢慢适应一些功能实践，而不需要一下子实现全部功能。

你能做的最好的事情之一是慢慢地开始重构你的应用程序的某些部分，直到你能获得更好的理解。在[之前的一篇文章](https://dev.to/grodier/array-methods-the-gateway-to-functional-programming-f29)中，我谈到了将循环重构为数组方法是一个很好的第一步，可以教会你很多关于函数式编程的知识。然而，这并不是改进代码库的唯一简单方法。下面这段来自 [Eric Normand](https://twitter.com/ericnormand) 的视频提供了一个很好的技巧，可以让你现有的代码更加实用。

[https://www.youtube.com/embed/3mbTa2wgmTU](https://www.youtube.com/embed/3mbTa2wgmTU)

这个技巧很棒，而且比你想象的更容易实现。

```
var myAge = 31;
var myName = 'George';

function nameAndDecadeString() {
  const decade = Math.floor(myAge / 10) * 10;
  const nameAndDecadeString =
    myAge === decade
      ? `${myName} is ${myAge} years old`
      : `${myName} is older than ${decade} and less than ${decade + 10}`;

  return nameAndDecadeString;
}

console.log(nameAndDecadeString());
// George is older than 30 and less than 40 
```

在这个假设的现有代码库的例子中，myAge 和 myName 是全局可变变量。它们都是在引用它们的*name 和 DecadeString* 函数的范围之外定义的。这个函数不会改变这些变量，只是读取它们。这仍然是个问题，因为函数将根据您何时读取这些值返回不同的结果。它们可以在某个时候通过用户交互进行更新，从而改变函数可能返回的内容。为了测试这一点，我们需要设置 mocks 或 globals 来使测试变得脆弱。

要使*name 和 DecadeString* 更具功能性，所需要做的就是获取全局变量(myAge 和 myName)并将它们作为函数的参数。

```
var myAge = 31;
var myName = 'George';

function nameAndDecadeString(name, age) {
  const decade = Math.floor(age / 10) * 10;
  const nameAndDecadeString =
    age === decade
      ? `${name} is ${age} years old`
      : `${name} is older than ${decade} and less than ${decade + 10}`;

  return nameAndDecadeString;
}

console.log(nameAndDecadeString(myName, myAge));
// George is older than 30 and less than 40 
```

既然参数已经更改，那么 *nameAndDecadeString* 函数可以将全局变量作为参数读入。全局变量仍然可能被意外修改，但是在*命名和解码*范围内的代码不再依赖于它们。整体功能没有改变，但是代码现在更易测试，功能更强。

函数式编程的原则之一是编写**纯函数**。纯函数是任何对于相同的输入总是产生相同输出的函数。以前， *nameAndDecadeString* 不接受任何输入，但每次调用时可能会产生不同的输出，这取决于在任何给定时间 myAge 和 myName 的值。既然它们是作为参数传入的，那么每次将相同的年龄和姓名传入函数时，函数都会产生相同的输出。它不再依赖于全球状态。

现在的目标是按照这种方法慢慢重构你的代码库。您的代码库不会功能齐全，但这是拥有更可靠和可测试代码的良好开端。
# 什么是 JavaScript 管道运算符？

> 原文：<https://dev.to/wtaylor45/what-are-javascript-pipeline-operators-30ca>

*更新:在[code pen](https://codepen.io/wtaylor45/pen/jJKXzv)T3 上查看管道操作员的实例*

JavaScript 是一种不断发展的语言，我相信你已经体验过了。TC39 是决定哪些特性成为 JavaScript 标准一部分的技术委员会，它不断收到旨在进一步改进该语言的提议。

其中一个提议是**管道运营商**。该运营商仍处于将提议从想法变为现实的早期阶段，但其在[巴别塔 7.3](https://github.com/babel/babel/releases/tag/v7.3.0) 中的可用性使其获得了一些恶名。因此，让我们深入了解管道运营商实际上是做什么的。

首先，我要说的是，我正在讨论与管道运营商相关的[几个提案](https://github.com/tc39/proposal-pipeline-operator/wiki)中的一个。这属于第四类，即“智能管道运营商”。

*免责声明:截至 2018 年 3 月*, ECMAScript 版本中没有正式列出以下功能

## 智能管道操作员

假设您希望能够对给定的字符串执行一系列不同的字符串操作。作为一名出色的函数式程序员，您让每个不同的操作都有自己的功能。

```
function repeat(str) { return str+', '+str; }

function exclaim(str) { return str+'!'; }

function capitalize(str) { return str[0].toUpperCase(); } 
```

现在你所要做的就是将一个字符串传递给所有这些函数，然后观察奇迹的发生。

```
// Expected result: Hello World! Hello World!
let noPipe = repeat(exclaim(capitalize("hello world")));
// 🤮 
```

这还不错，但是随着它变得越来越大，像这样的东西会变得非常丑陋和难以理解。

进入管道系统。

使用管道操作符(| >)，我们可以重写上面的代码来获得相同的结果。

```
// Expected result: Hello World! Hello World!
let withPipe = "hello world"
    |> repeat
    |> exclaim
    |> capitalize;
// Pipe it up 🥰 
```

使用管道操作符，您只需继续使用`|>`调用函数，无论您对什么使用管道操作符，都将作为参数传入。

这可以使您的代码更容易阅读，因为您预先知道正在执行什么操作，并且对它执行的操作读起来更像一个故事。

您现在可能会问，如何将管道运算符用于具有多个参数的函数。好吧，我们也能处理这些！我们以这个函数为例。

```
function add(x, y) { return x + y; } 
```

现在让我们使用管道操作符来让它工作。

```
// Expected result: 6
let sum = 2 |> add(#, 4); 
```

这里的`#`字符作为我们传递给每个函数的值的引用。

好吧，酷。因此，我们可以将它用于单参数和多参数函数，这也让我们看起来很酷。但是如果我不想写一个函数来做我想做的任何修改呢？为什么我不能只做一些内联逻辑呢？

你可以！让我们再次做同样的加法，但是这次不调用`add`函数。

```
// Expected result: 6
let sum = 2 |> # + 4; 
```

显然，对于这样简单的例子来说，这样做似乎有点愚蠢，但是随着它的规模和复杂性的增长，这个操作符确实可以帮助你的代码保持可读性。

你可以[在 github](https://github.com/js-choi/proposal-smart-pipelines/) 上看到这个提议，它包括很多这样的例子以及更多。然而，其中概述的一些功能并不在最新发布的 Babel 中。对于 instace，在你的管道中使用`await`和`yield`。

希望你发现这个对管道操作者的简要介绍是有用的，并且一定要看看其他的 TC39 提案，这些提案已经成为了 Babel 7+！此外，CodePen 现在支持 Babel 7，所以可以在那里自由尝试。
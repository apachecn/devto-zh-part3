# 用 Z 模式匹配您的 JavaScript

> 原文：<https://dev.to/kayis/pattern-match-your-javascript-with-z-nff>

*[Flickr 上丹尼斯·斯克利的封面图片](https://www.flickr.com/photos/dskley/)*

函数式编程技术目前似乎相当流行。人们在 JavaScript 中使用[单子，在 OCaml](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 中编写[编译器，甚至像 Java 这样的硬核面向对象语言现在也支持](https://github.com/BuckleScript/bucklescript)[闭包](http://tronicek.blogspot.com/2007/12/closures-closure-is-form-of-anonymous_28.html)。

如果我们可以在无处不在的语言中使用一些更高级的抽象，比如 JavaScript 中的**模式匹配**，这不是很好吗？

有一个 [TC39 提案](https://github.com/tc39/proposal-pattern-matching)支持这个特性的新语法，但是它只是第一阶段，9 个月没有更新。

幸运的是，有一个名为 [Z](https://z-pattern-matching.github.io/) 的库，让我们无需额外的语法就能进行模式匹配。

## 为什么

> 模式匹配是类固醇的开关；您可以基于对象属性或数组内容创建强大的条件，而无需操作对象或数组本身。这种能力让你编写功能性的、不可变的、表达性的代码，而不是命令式的，这减少了很多复杂性和错误。

## 什么

z 是一个 JavaScript 库，它允许我们在 JavaScript **中使用模式匹配，而不需要新的语法**。我们安装了一个 NPM 包，并准备好了！

## 如何

Z 包导出了一个我们可以像使用`if`或`switch`一样使用的`matches`函数。

一个主要的区别是，matches 返回匹配的大小写，所以它的行为像一个表达式而不是一个语句。如果没有匹配的，它将返回`undefined`。

那么，它能做什么呢？

像`switch`可以按值匹配。

```
const result = matches(x)(
  (c = 1) => "One!",
  (c = 2) => "Two!",
  (c) => "Something different..."
); 
```

Enter fullscreen mode Exit fullscreen mode

但是，与`switch`不同，它还可以匹配更复杂的值。

```
const result = matches(x)(
  (c = {data: 123}) => "We got data!",
  (c = {error: 404}) => "We got an error!",
  (c = [1,2,3]) => "We got an array!"
); 
```

Enter fullscreen mode Exit fullscreen mode

也可以按类型匹配。

```
const result = matches(o)(
  (c = Date) => "We got the date " + c.toString(),
  (c = Number) => "We got the number " + c,
  (c = Array) => "We got an array!"
); 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以用它来析构数组或期望数组具有特定的长度。

```
const result = matches(["hello", ",", "world", "!"])(
  (first, tail) => first + tail.join(""),
  (first, tail = []) => "Array only has one element!"
); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

z 使用标准的 JavaScript 语法，但是它在这里使用了一些技巧。表面上，它将我们的值与回调函数的默认值进行比较。这个决定导致了不寻常的使用`=`操作符，而不是`==`和`===`操作符。

它似乎还计划建立一个严格的匹配模式，如果检查不彻底，它会抛出一个错误而不是返回 undefined。

虽然我不知道这是否是在 JavaScript 中实现模式匹配的最佳方式，但它至少是一种有趣的方式，可以帮助防止一些错误，同时使代码更加简洁。

在评论里说说你的看法:)

* * *

如果你喜欢我的帖子，你也可以在 [Twitter](https://twitter.com/K4y1s) 上关注我！
# 你能找到这段代码中的错误吗？

> 原文：<https://dev.to/vzhou842/can-you-find-the-bug-in-this-code-2k0h>

下面是一段 Javascript 代码，它打印了“Hello World！”两行:

```
(function() {
  (function() {
    console.log('Hello')
  })()

  (function() {
    console.log('World!')
  })()
})() 
```

…只是它会因运行时错误而失败。你能在不运行代码的情况下发现漏洞吗？

向下滚动查看提示。

* * *

* * *

* * *

* * *

* * *

## [印度语](#hint)

下面是错误的文本:

```
TypeError: (intermediate value)(...) is not a function 
```

这是怎么回事？

向下滚动查看解决方案。

* * *

* * *

* * *

* * *

* * *

## 解

一个字符固定了这个代码:

```
(function() {
  (function() {
    console.log('Hello')
  })();
  (function() {
    console.log('World!')
  })()
})() 
```

如果没有分号，最后一个函数将被解释为函数调用的参数。这里有一段重写代码，演示了在没有分号的情况下运行代码时会发生什么:

```
const f1 = function() { console.log('Hello'); };
const f2 = function() { console.log('World!'); };

f1()(f2)(); 
```

最后一行中有 3 个函数调用:

*   在没有参数的情况下调用`f1`
*   调用`f1()`的返回值时，将`f2`作为唯一的参数
*   调用`f1()(f2)`的返回值时不带任何参数

由于`f1()`的返回值不是一个函数，运行时在第二次调用时抛出一个`TypeError`。

加上分号后，就变成了:

```
const f1 = function() { console.log('Hello'); };
const f2 = function() { console.log('World!'); };

f1();(f2)(); 
```

如预期运行。

## 等等，你也有过这种 bug？

没错。

## 为什么你要写这么多直接调用函数表达式的代码([life](https://developer.mozilla.org/en-US/docs/Glossary/IIFE))？

这是一个很长的故事- [这篇文章](https://victorzhou.com/blog/why-you-should-use-webpack/)解释了我是如何写了糟糕到有这个 bug 的代码。

## 教训

**永远用分号**。这个特殊的例子有点做作，但是类似的事情可能会发生在你身上。这是另一个因为相关原因而失败的 Hello World 项目:

```
const a = 'Hello'
const b = 'World' + '!'
[a, b].forEach(s => console.log(s)) 
```

我会把解决这个问题作为一个练习留给你。

大多数 Javascript 风格指南都需要分号，包括[谷歌的](https://google.github.io/styleguide/jsguide.html#formatting-semicolons-are-required)、 [Airbnb 的](https://github.com/airbnb/javascript#semicolons)和 [jQuery 的](https://contribute.jquery.org/style-guide/js/#semicolons)。总结一下:**永远用分号**。
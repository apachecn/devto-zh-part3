# 支持旧浏览器—第 3 部分:JavaScript

> 原文：<https://dev.to/zellwk/supporting-older-browserspart-3-javascript-59c1>

为旧浏览器提供 JavaScript 支持很容易。大多数时候，你只需要使用聚脂填料。

但是你可以做更多的事情。

## 本系列以前的文章

1.  第 1 部分:[支持旧浏览器](/blog/older-browsers-css)
2.  第 2 部分:[支持旧浏览器—第 2 部分:CSS](/blog/older-browsers-css)
3.  第 3 部分:本文

## 什么是聚合填充？

Polyfill 是一段告诉浏览器如何实现 JavaScript 特性的代码。一旦您添加了聚合填充，您就不再需要担心支持问题。会有用的。

聚合填充的工作原理如下:

1.  它检查该功能是否受支持
2.  如果没有，它会添加代码来支持该特性

这是一个多填充物工作的例子。它检查浏览器是否支持`Array.prototype.find`。如果浏览器不支持`Array.prototype.find`，它会告诉浏览器如何支持。

你可以在 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find) 上找到这个代码。

```
if (!Array.prototype.find) {
  Object.defineProperty(Array.prototype, 'find', {
    value: function(predicate) {
     // 1\. Let O be ? ToObject(this value).
      if (this == null) {
        throw new TypeError('"this" is null or not defined');
      }

      var o = Object(this);

      // 2\. Let len be ? ToLength(? Get(O, "length")).
      var len = o.length >>> 0;

      // 3\. If IsCallable(predicate) is false, throw a TypeError exception.
      if (typeof predicate !== 'function') {
        throw new TypeError('predicate must be a function');
      }

      // 4\. If thisArg was supplied, let T be thisArg; else let T be undefined.
      var thisArg = arguments[1];

      // 5\. Let k be 0.
      var k = 0;

      // 6\. Repeat, while k < len
      while (k < len) {
        // a. Let Pk be ! ToString(k).
        // b. Let kValue be ? Get(O, Pk).
        // c. Let testResult be ToBoolean(? Call(predicate, T, « kValue, k, O »)).
        // d. If testResult is true, return kValue.
        var kValue = o[k];
        if (predicate.call(thisArg, kValue, k, o)) {
          return kValue;
        }
        // e. Increase k by 1.
        k++;
      }

      // 7\. Return undefined.
      return undefined;
    },
    configurable: true,
    writable: true
  });
} 
```

## 使用聚合填充

使用聚合填充有两种方式:

1.  手动聚合填充(如上例所示)
2.  通过库一次添加多个聚合填充

### 手动多填

首先，你需要**搜索你需要的多填充物**。如果你在谷歌上搜索一下，你应该能找到一个。聪明的开发人员已经为你需要的几乎所有东西创建了 polyfills。

一旦你找到了 polyfill，**使用上面的过程**来创建对老版本浏览器的支持。

### 一次添加多个聚合填充

一些库包含许多聚合填充。 [ES6-shim](https://github.com/paulmillr/es6-shim) 就是这样一个库的例子。它在旧的浏览器上提供对所有 ES6 特性的支持。

(注意:聚合填充是垫片的子集。shim 是为旧环境带来新 API 的库)。

## 使用尖端的 JavaScript 特性

如果你想使用前沿的 JavaScript 特性，可以考虑在你的构建过程中加入 Babel。

Babel 是一个编译 JavaScript 的工具。在此编译过程中，它可以:

1.  添加所需的任何垫片/聚合填充物
2.  将预处理程序编译成 JavaScript

更多关于第二点:

Babel 在你的构建过程中离线工作。它可以读取您传递给它的文件，然后将这些文件转换成浏览器可以读取的 JavaScript。

这意味着你可以使用像 Flow，TypeScript 和其他你听说过的很酷的技术这样的前沿功能。它们都可以在浏览器中工作(前提是你先通过 Babel！)

## poly fill 不够用怎么办？

如果聚合填充不足以支持该功能，您可能需要重新考虑为有问题的浏览器提供的支持量。

你需要在不同的浏览器上提供相同的功能吗？也许你应该考虑渐进增强？

也许你可以用一种不使用特性的方式编码？

有很多可能，但你明白其中的意思。

## 如何辨别一个浏览器是否支持该功能？

首先，我要检查一下 caniuse.com 的情况。写下您想要的 JavaScript 特性的名称，您将能够看到浏览器支持级别。

这里有一个关于[中止控制器](https://caniuse.com/#search=Abort)的例子

[![Searching for abort controller in caniuse.com](img/72ae8c36dd6c06de1929aca8e6d8b0ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P8sNbv-k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/older-browsers/abort-caniuse.png)

如果 caniuse.com 没有给我任何信息，我会检查 MDN。你会在大多数文章的底部找到浏览器支持。

下面是再次使用[中止控制器](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)的例子:

[![Searching for abort controller in MDN](img/4966616439770d8a1f0dc71bdc6ed871.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--krKGiq5z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/older-browsers/abort-mdn.png)

## 当心 JavaScript 的成本

使用聚合填充时，会添加更多的 JavaScript 代码。

添加更多 JavaScript 的问题是，嗯，有更多的 JavaScript。JavaScript 越多，问题就越多:

1.  旧浏览器通常存在于旧电脑中。他们可能没有足够的处理能力。
2.  JavaScript 包会延迟站点加载。在 Addy Osmani 的“[JavaScript 的成本](https://medium.com/@addyosmani/the-cost-of-javascript-in-2018-7d8950fbb5d4)”中有更多关于这方面的内容

## 包装完毕

很容易添加对 JavaScript 特性的支持。大多数时候，你添加一个 polyfill，然后就收工了。但是当你这样做的时候，要注意 JavaScript 的代价！

有时，完全放弃这个特性可能是好的。

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。
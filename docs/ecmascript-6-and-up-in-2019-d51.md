# 2019 年 ECMAScript 6 及更高版本

> 原文：<https://dev.to/areknawo/ecmascript-6-and-up-in-2019-d51>

你——可能是一个阅读这篇文章的 JS 开发者——还记得 ECMAScript 6 (ES6)吗？2015 年发布的 ES6 JavaScript 语言标准启动了 JS 的快速发展，这一点在今天仍然可以欣赏。面对现实吧——ES6 让 JS 重新变得好玩起来。随着增加的**功能**和一些**语法糖**，它使网络成为越来越多高级网站和网络应用的地方。ES2016 (ES7)、ES2017 和更高版本只是在一个年度发布周期中通过更多功能(例如**异步** / **等待**)使其变得更好。它还极大地影响了 JS 生态系统，像 **Angular** 、 **React** 和 **Vue** 这样的框架变得更加流行和依赖。但是 ECMAScript 只是一个需要由不同浏览器的 JS **引擎**实现的标准(例如来自**谷歌 Chrome** 的 **V8** )，这就是事情开始变得不那么有趣的地方。当然，标准已经被**定义**的事实并不意味着它会马上被实施。旧版本的浏览器显然永远不会有新功能，在新版本上，可能需要很长时间才能实现。

# 进入编译器时代

因此，并不是每个人都想等这么长时间来体验更简单、更好、更现代的编码解决方案。这就是 JS **编译器**被创建的原因。这些 **transpilers** (这个术语可以互换使用，但在我看来更好地描述了这些工具的用途)只是用现代风格编写代码，并将其更改为与更老的浏览器兼容的代码。这对于新的语法糖几乎总是有效的，但对于现代语言特性和 API 却不太有效，但这足以满足许多开发人员的需求。最受欢迎和最知名的是**Babel**——编译器实现了最新和最棒的 **ES-Next** (所有新的和即将到来的 ES 版本的术语)功能 ASAP，很可能是你的好朋友。当然，像这样的解决方案让 JS 社区在想要现代特性的人和想要性能的人之间产生了分歧。这就是本文的重点。您可以使用 transpiler 获得高性能的代码，或者使用现代语法的代码，或者中间地带- **并不是 100%** 两个世界中最好的。看看下面利用 ES6 语法特性的代码，比如 **const** 、 **let** 、 **arrow functions** 以及最显著的—**for...循环中的**和**停止/展开操作器** :

```
const uselessFunction = (...numbers) => {
    let sum = 0;
    for(const number of numbers) {
        sum += number;
    }
    return sum;
}
uselessFunction(1,2,3,4); //10

12345678 
```

Enter fullscreen mode Exit fullscreen mode

老实说，这看起来很不错。对于返回所有传递参数的总和来说，这不是一个真正有用的函数。现在，你可能在 **ES5** 中实现它的方式是:

```
function uselessFunction(numbers) {
    var sum = 0;
    for(var i = 0; i < numbers.length; i++) {
        sum += numbers[i];
    }
    return sum;
}
uselessFunction([1,2,3,4]); //10

12345678 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，这几乎是相同的代码，除了更多的代码用于循环和使用数组代替参数列表。这和上面的例子没有什么不同。考虑到你不久前更新了你的浏览器，如果你没有使用 IE 浏览器(因为如果你这样做了，你一定有一些非常重要的原因🙃)，那么当您执行它们时，这两个代码片段都应该是现成的。现在，在这个特殊的场景中跟随我。你认为自己是一个现代的 JS 开发人员，没有 Babel 就不会开始编码。最快的方法就是安装 Babel 和它的默认 **ENV 预置**，不需要配置任何东西，你就可以开始编码了。当你写完你的 *uselessFunction* 并且你正在阅读以将它部署到生产模式时，一些事情使你发痒，你想要看到你的现代代码的输出。然后...**此**发生:

```
"use strict";

var uselessFunction = function uselessFunction() {
  for (var _len = arguments.length, numbers = Array(_len), _key = 0; _key < _len; _key++) {
    numbers[_key] = arguments[_key];
  }

  var sum = 0;
  for (var _iterator = numbers, _isArray = Array.isArray(_iterator), _i = 0, _iterator = _isArray ? _iterator : _iterator[Symbol.iterator]();;) {
    var _ref;

    if (_isArray) {
      if (_i >= _iterator.length) break;
      _ref = _iterator[_i++];
    } else {
      _i = _iterator.next();
      if (_i.done) break;
      _ref = _i.value;
    }

    var number = _ref;

    sum += number;
  }
  return sum;
};
uselessFunction(1, 2, 3, 4); //10

123456789101112131415161718192021222324252627 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们就此结束这个可怕的故事，让我们更深入地了解一下**到底发生了什么**。当然，上面的代码会比我们上面的 ES5 手写版本慢很多很多。在 *uselessFunction* 的例子中并没有——它太简单了，但很可能在对性能要求更高的代码中。但是，你不能因为这里发生的事情而责怪巴贝尔。它只是在做它的工作。你看，在没有进一步配置的情况下使用 ENV preset，你针对的是 **IE 11** 和 **Safari 高于 9** 的版本。通过这种方式，Babel 产生了您可以在上面看到的输出(与 ENV 预设并不完全相同，它额外添加了错误处理，这在这里并不存在，因为我使用了 **ES2015-loose** 预设以进行更公平的比较)。这向我们展示了不正确配置编译器和不指定给定浏览器的最低目标版本对代码性能的负面影响。

# 欢迎来到 2019！

所以，现在距离 ECMAScript 6 / 2015 发布已经快 **4 年**了 BTW 新年快乐！🎉4 年是一段很长的时间，特别是从总体上看网络开发和技术。到目前为止，ES6 应该已经在大多数浏览器上得到解决...**它多半是**。但是使用编译器的习惯(有时没有真正的需要)已经在许多开发人员的头脑中扎根。为了帮助解决这个问题，这里有一些最受欢迎的 ES6 特性，它们在许多浏览器中都有很好的支持。

## Const & let 变量声明

这可能会让你感到惊讶，但是 **const** 和 **let** 关键词有很好的跨浏览器支持。 *Const* 在几乎 **95%** 的所有网络浏览器中被完全或部分支持——甚至 **IE 11** ！*另一方面，Let* 紧随 *Const* 之后，大约有 **90%** 的浏览器支持它， **IE 11 部分支持**。遗憾的是，这些特性并不是让我们编译的代码如此庞大的原因——它们可以很容易地用关键字 *var* 填充。

## 箭头功能

箭头函数可能是使用最广泛的 ES6 特性之一。为编写函数提供一个简短的形式——尤其是回调函数——这些也用**词法绑定**解决了**这个**上下文问题。有了大约 **87%** 的全球支持，当**不针对 IE 11** 或 **Safari 9.x** 时，你可以自由使用这些。

## 承诺

承诺提供了一种更好的方式来处理异步代码，而不会陷入**回调地狱**。他们获得了 **89%** 的支持，甚至在 **Safari 7.1** 中也获得了令人印象深刻的支持——遗憾的是，IE 就没有这样的运气了。有趣的是， **async/await** 语法 sugar over promises(es 2017 规范的一部分)在 **85%** 的所有浏览器中都得到了支持。所以，不要害怕在现代环境中编写异步代码。

## 其余参数/传播算子

Rest 参数对全球浏览器 **87%** 的精细支持。实际上，这些是我们的 Babel 编译代码庞大背后的主要原因！87%是个不错的分数，但是你可以不用任何 polyfills 就忘记支持 IE 11 或者 Safari 9.x。

## 类

现在，类是一种新的、更好的方式，通过再次利用语法优势来处理 JavaScript 的*原型*特性。支持 **87%** 的二手浏览器包括 **Safari 9** 。

所以在这里你可以用一些例子或者证据来证明 transpiler 可能并不真正需要——至少在某些情况下。人们正在更新他们的浏览器甚至整个设备，因此这种支持只会以积极的方式增长。请记住，我们在这里只谈论干净的 JavaScript 语言——没有 Web APIs 或类似的东西。如果上面的例子没有真正说服你，那也没关系，至少我试过了。百分比形式的原始数据来自[can use](https://caniuse.com/)，所以如果你想——检查一下！

# 底线

这篇文章的目的不是说编译器、传输器等等。都不好- **一点也不好！**这些都是很棒的工具，可以让你编写下一代的 JavaScript，而且，随着 JS 的不断发展，这些真的很有用！我更想指出知道你的*底线*应该放在哪里是多么重要。另外，不要忘记上面的代码片段，仔细看看(至少有时)编译后的代码和您的**。babelrc** 文件或者你正在使用的任何东西。

感谢你阅读这篇文章，再次祝你新年快乐！🎉最后，我真的很期待 **ES2019** ！
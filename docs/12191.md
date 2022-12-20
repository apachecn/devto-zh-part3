# JavaScript 中的模块模式

> 原文：<https://dev.to/tomekbuszewski/module-pattern-in-javascript-56jm>

模块是一种有点类似于单例类的结构。它只有一个实例并公开其成员，但是它没有任何内部状态。

## 定义一个模块

模块被创建为一个 IIFE(立即调用的函数表达式)，其中包含一个函数:

```
const SomeModule = (function() {})(); 
```

所述函数体中的所有内容都绑定到该模块，并且彼此可以看到。模块模拟“公共”和“私有”方法，通过创建前面提到的作用域，只暴露那些声明的东西。

> 私有方法或函数是给定实体的成员，只能在所述实体中看到。公共的可以从给定实体的外部访问。

让我们尝试创建一个内部带有私有函数的模块。

```
const Formatter = (function() {
  const log = (message) => console.log(`[${Date.now()}] Logger: ${message}`);
})(); 
```

如您所见，有一个简单的`log`函数将记录收到的消息。如何执行？`Formatter.log`？

```
Formatter.log("Hello"); 
```

你能猜出它生产什么吗？`Uncaught TypeError: Cannot read property 'log' of undefined`。这是为什么呢？因为我们的模块不返回任何东西，所以它实际上是`undefined`，尽管里面的代码会执行。

```
const Formatter = (function() {
  console.log("Start");
  const log = (message) => console.log(`[${Date.now()}] Logger: ${message}`);
})(); 
```

这个会记录`Start`，因为这个函数已经被激发了，而且你也知道，函数不一定总是要返回一些东西。

所以，现在我们知道**访问一个模块实际上是访问它返回的任何东西**。

可以将`log`函数视为私有函数。可以从模块内部访问它，模块内部的其他函数也可以执行它。我们试试吧！

```
const Formatter = (function() {
  const log = (message) => console.log(`[${Date.now()}] Logger: ${message}`);

  const makeUppercase = (text) => {
    log("Making uppercase");
    return text.toUpperCase();
  };
})(); 
```

嘿，等一下，伙计！这是模块中另一个我无法访问的功能！

## 暴露一个模块

是的，这是另一个我们无法使用的功能。但是，知道了我们之前学到的关于访问模块的知识，我们可以很容易地解决这个问题！你已经知道该怎么做了？完全正确，返回这个函数！但是，不要返回单个函数(尽管这是可能的)，要返回一个对象！

```
const Formatter = (function() {
  const log = (message) => console.log(`[${Date.now()}] Logger: ${message}`);

  const makeUppercase = (text) => {
    log("Making uppercase");
    return text.toUpperCase();
  };  

  return {
    makeUppercase,
  }
})(); 
```

现在，我们可以像平常一样使用`makeUppercase`函数:

```
console.log(Formatter.makeUppercase("tomek")); 
```

结果如何？

```
> Start
> [1551191285526] Logger: Making uppercase
> TOMEK 
```

模块不仅可以容纳函数，还可以容纳数组、对象和原语。

```
const Formatter = (function() {
  let timesRun = 0;

  const log = (message) => console.log(`[${Date.now()}] Logger: ${message}`);
  const setTimesRun = () => { 
    log("Setting times run");
    ++timesRun;
  }

  const makeUppercase = (text) => {
    log("Making uppercase");
    setTimesRun();
    return text.toUpperCase();
  };

  return {
    makeUppercase,
    timesRun,
  }
})(); 
```

让我们执行它:

```
console.log(Formatter.makeUppercase("tomek"));
console.log(Formatter.timesRun); 
```

不出所料，`0`显示。但是请注意，这可以从外部覆盖。

```
Formatter.timesRun = 10;
console.log(Formatter.timesRun); 
```

现在控制台日志`10`。这说明公开暴露的一切都是可以从外部改变的。这是模块模式最大的缺点之一。

引用类型的工作方式不同。在这里，您可以定义它，它会随着您的操作而被填充。

```
const Formatter = (function() {
  const log = (message) => console.log(`[${Date.now()}] Logger: ${message}`);
  const timesRun = [];

  const makeUppercase = (text) => {
    log("Making uppercase");
    timesRun.push(null);
    return text.toUpperCase();
  };

  return {
    makeUppercase,
    timesRun,
  }
})();

console.log(Formatter.makeUppercase("tomek"));
console.log(Formatter.makeUppercase("tomek"));
console.log(Formatter.makeUppercase("tomek"));
console.log(Formatter.timesRun.length); 
```

它会记录`3`，在大写的说了三遍我的名字之后。

## 声明模块依赖关系

我喜欢将模块视为封闭的实体。也就是说，它们存在于自身之中，它们的存在不再需要任何东西。但是有时你可能想要使用，例如，DOM 或者`window`全局对象。

为了实现这一点，模块可能有依赖关系。让我们试着写一个函数，向我们请求的 HTML 元素写一条消息。

```
const Formatter = (function() {
  const log = (message) => console.log(`[${Date.now()}] Logger: ${message}`);

  const makeUppercase = (text) => {
    log("Making uppercase");
    return text.toUpperCase();
  };

  const writeToDOM = (selector, message) => {
    document.querySelector(selector).innerHTML = message;
  }

  return {
    makeUppercase,
    writeToDOM,
  }
})();

Formatter.writeToDOM("#target", "Hi there"); 
```

它开箱即用(假设我们的 DOM 中有一个 id 为`target`的元素)。听起来不错，但是`document`只有在 DOM 可访问时才可用。在服务器上运行代码会产生错误。那么，如何确保我们准备好了呢？

其中一个选项是检查`document`是否存在。

```
const writeToDOM = (selector, message) => {
  if (!!document && "querySelector" in document) {
    document.querySelector(selector).innerHTML = message;
  }
} 
```

这基本上解决了所有问题，但我不喜欢这样。现在模块真正依赖于外部的东西。这是“只有我的朋友也去，我才会去”的情景。非得这样吗？

不，当然不是。

我们可以声明模块的依赖项，并在进行过程中注入它们。

```
const Formatter = (function(doc) {
  const log = (message) => console.log(`[${Date.now()}] Logger: ${message}`);

  const makeUppercase = (text) => {
    log("Making uppercase");
    return text.toUpperCase();
  };

  const writeToDOM = (selector, message) => {
    if (!!doc && "querySelector" in doc) {
      doc.querySelector(selector).innerHTML = message;
    }
  }

  return {
    makeUppercase,
    writeToDOM,
  }
})(document); 
```

让我们一步一步来。在顶部，有一个关于我们函数的参数。然后，在`writeToDOM`方法中使用，而不是我们的`document`。最后，就在最后一行，我们添加了`document`。为什么？**这些是我们的模块将被**调用的参数。为什么我改变了模块中的参数名？我不喜欢隐藏变量。

当然，这是一个很好的测试机会。现在，我们可以插入一个 mock，而不是依赖于我们的测试工具是否有 DOM simulator 或类似的东西。但是我们需要在定义过程中插入它，而不是以后。这是相当简单的，你只需要写一个模拟和地方是作为一个“备用”:

```
const documentMock = (() => ({
  querySelector: (selector) => ({
    innerHTML: null,
  }),
}))();

const Formatter = (function(doc) {
  const log = (message) => console.log(`[${Date.now()}] Logger: ${message}`);

  const makeUppercase = (text) => {
    log("Making uppercase");
    return text.toUpperCase();
  };

  const writeToDOM = (selector, message) => {
    doc.querySelector(selector).innerHTML = message;
  }

  return {
    makeUppercase,
    writeToDOM,
  }
})(document || documentMock); 
```

我甚至去掉了`makeUppercase`里面的复选标记，因为不再需要它了。

—

模块模式是一种非常常见的模式，并且——正如你所看到的——非常擅长于此。我经常尝试先写模块，然后——如果需要的话——写类。
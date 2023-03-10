# JavaScript 快速提示:用于 sane 调试的命名函数

> 原文：<https://dev.to/sunnysingh/javascript-quick-tip-named-functions-for-sane-debugging-35am>

调试不是你在写代码的时候考虑的事情，但是这样做可以让你避免以后的挫折。

一个简单的方法是在匿名函数上使用命名函数。如果你不熟悉，看看下面的代码块:

```
// These two are anonymous functions
function() {}
() => {}

// These two are named functions
function doSomething() {}
const doSomething = () => {} 
```

Enter fullscreen mode Exit fullscreen mode

在一个典型的应用程序中，你会有调用其他函数的函数。有些函数甚至可以从事件监听器中调用，比如在窗口加载时。请看这个例子。

```
function Page() {
  window.addEventListener('load', function() {
    console.log('Page loaded!');
    Page.init();
  });
}

function App() {
  Page();
}

App(); 
```

Enter fullscreen mode Exit fullscreen mode

注意到代码中的错误了吗？当您尝试运行它时，您将在开发人员工具控制台中得到以下内容。

```
index.js:4 Uncaught TypeError: Page.init is not a function
    at index.js:4

(anonymous)   @ index.js:4
load (async)
Page          @ index.js:2
App           @ index.js:9 
```

Enter fullscreen mode Exit fullscreen mode

这是扩展的控制台消息，被称为[堆栈跟踪](https://developer.mozilla.org/en-US/docs/Web/API/console#Stack_traces)。你看到我们命名的函数比如`Page`、`App`了吗？`window.addEventListener`的事件处理函数怎么样？啊哦，是“匿名”。想象一下，如果有多个匿名函数调用其他匿名函数。这不会形成对调试有用的堆栈跟踪。

让我们通过在函数中添加名称`pageLoadHandler`来解决这个问题。

```
function Page() {
  window.addEventListener('load', function pageLoadHandler() {
    console.log('Page loaded!');
    Page.init();
  });
}

function App() {
  Page();
}

App(); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码仍然有一个错误，所以如果我运行它，我会得到如下结果。

```
Uncaught TypeError: Page.init is not a function
    at pageLoadHandler (index.js:4)

pageLoadHandler @ index.js:4
load (async)
Page            @ index.js:2
App             @ index.js:9 
```

Enter fullscreen mode Exit fullscreen mode

现在，即使没有扩展的堆栈跟踪，错误消息也是有用的，因为我们可以看到它直接来自`pageLoadHandler`。如果我们从这里调用另一个函数，堆栈跟踪将不再只显示`(anonymous)`。

当对像`.map()`或`.filter()`这样的数组方法进行简单的回调时，你可能仍然想要创建匿名函数，只要父函数有名字，我看不出有什么问题。了解匿名函数是如何影响堆栈跟踪的，有助于您为以后的调试体验做出决定。

> **注**:本文原写于[我的个人博客](https://sunnysingh.io/blog/javascript-named-functions)。我在这里为神奇的开发者社区重新发布它。
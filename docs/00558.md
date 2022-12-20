# 如果 Javascript 是单线程的，那么它是如何异步的呢？

> 原文：<https://dev.to/bbarbour/if-javascript-is-single-threaded-how-is-it-asynchronous-56gd>

Javascript 是一种单线程语言。这意味着它有一个调用堆栈和一个内存堆。正如所料，它按顺序执行代码，并且必须先执行完一段代码，然后再执行下一段。这是同步的，但有时可能是有害的。例如，如果一个函数需要一段时间来执行或者必须等待某个东西，它会在这段时间内冻结所有东西。

一个很好的例子就是窗口提醒功能。`alert("Hello World")`

在您点击“确定”并解除警告之前，您根本不能与网页互动。你卡住了。

那么我们如何用 Javascript 获得异步代码呢？

嗯，我们可以感谢 Javascript 引擎(V8，Spidermonkey，JavaScriptCore 等...)中，它拥有在后台处理这些任务 Web API。调用堆栈识别 Web API 的功能，并将它们交给浏览器处理。一旦这些任务被浏览器完成，它们就返回并作为回调被推送到堆栈上。

打开你的控制台，输入`window`，然后按回车键。您将看到 Web API 提供的几乎所有东西。这包括 ajax 调用、事件监听器、fetch API 和 setTimeout。Javascript 使用 C++等低级编程语言在幕后执行这些操作。

让我们看一个简单的例子，在你的控制台上运行这段代码:

```
console.log("first")
setTimeout(() => {
    console.log("second")
}, 1000)
console.log("third") 
```

Enter fullscreen mode Exit fullscreen mode

我们得到了什么？

```
first
third
undefined
second 
```

Enter fullscreen mode Exit fullscreen mode

感觉很奇怪，对吧？好吧，让我们一行一行地分析一下:

首先在堆栈中，所以它被打印。接下来，引擎注意到 setTimeout，它不是由 Javascript 处理的，并将其推给 WebAPI 以异步完成。调用堆栈继续运行，而不关心交给 Web APIs 的代码，并打印出`console.log("three")`。

接下来，Javascript 引擎的事件循环开始，就像一个小孩在问“我们到了吗？”在公路旅行中。它开始启动，等待事件被推入。由于`setTimeout`没有结束，它返回默认的`undefined`，因为它还没有被赋予值。一旦回调最终击中我们得到`console.log("second")`打印。

有一个很好的网站可以让这一切慢下来，并展示这一切的发生。

[http://latent lip . com/loupe](http://latentflip.com/loupe)

我建议在这个沙盒里玩一玩，以帮助巩固你的理解。它帮助我了解了异步代码如何与单线程的 Javascript 一起工作。
# Javascript 事件循环

> 原文：<https://dev.to/ruairidhwm/the-javascript-event-loop-2fgk>

**了解事件循环如何工作**

**简介**

你听过有人说慢代码是“阻塞”吗？或者也许你在编码时看到过`Call Stack Limit Exceeded`错误？这一切意味着什么？

嗯，它归结为一种叫做事件循环的东西，也就是 Javascript 运行时及其环境如何处理你的代码。这实际上很有意义，知道这一点也很有用——所以我试图在这里记录我自己的学习。

如果你想了解更多细节，我推荐你去看看菲利普·罗伯茨的精彩演讲。它为这篇文章提供了灵感。

事物是如何组合在一起的？

如果我们看看 Javascript 运行时本身(例如 Chrome 中的 V8)，您会看到一个堆(内存分配发生的地方)和调用堆栈。

您还可以使用浏览器提供的 Web APIs 比如 DOM、fetch、setTimeout 等。然后您还有回调队列。

**栈是什么？**

Javascript 有一个单线程运行时，这意味着它只有一个调用栈。这意味着技术上它一次只能运行一件事。

例如:

```
function sayHello(x) {
  return `Hi ${x}, how are you today?`
}

function greet(x) {
  return sayHello(x)
}

function greetMe(x) {
  var greeting = greet(x)

  console.log(greeting)
}

greetMe("Ruairidh") 
```

这段代码是做什么的？你可以看到多个函数，它们互相调用。

我们的堆栈看起来像这样:

```
sayHello(x)
greet(x)
greetMe("Ruairidh")
main() 
```

**阻塞**

当事情进展缓慢时会发生什么？速度慢(图像处理、网络请求等)并且在堆栈上的东西可以称为阻塞。

如果您在一行中有三个同步请求，那么您需要等待它们完成，然后堆栈上的其他事情才会发生。这是浏览器中的一个问题，因为在这些请求完成之前，您不能做任何其他事情。这将创造一个可怕的用户体验。

那么解决办法是什么呢？异步回调。它们允许你运行代码，并给它一个回调(或函数)，我们可以稍后执行。但是他们会怎么样呢？这就是事件循环出现的地方。

**事件循环**

作为一个运行时，Javascript 一次只能做一件事，但是由于我们可以在浏览器等地方访问 API，我们可以同时做一些事情。Node 实际上做了同样的事情。

当您调用一个异步函数时，相关的 API 会拾取它，然后该函数会弹出堆栈。它正在其他地方被处理，并且堆栈移动到下一个项目。

当异步函数完成时，它会将代码推送到任务队列中。然后，事件循环检查堆栈和任务队列。如果堆栈是空的，那么它会进入任务队列，并将第一个项推到堆栈上。

然后，Javascript 运行时执行堆栈上的代码，您会收到输出。

这就是使用`setTimeout`并将值设置为 0 的黑客存在的原因。这是一种推迟事情直到堆栈清空的方法。你现在可以用承诺来达到同样的效果，但方式更有效。

承诺在事件循环堆栈中具有更高的优先级，因为它们创建一个“微任务”,而`setTimeout`创建一个“宏任务”,后者也具有 4 毫秒的最小延迟。另外，`setTimeout`不提供有保证的执行时间，它提供了执行的最短时间。如果你想了解更多，这里有一个很棒的帖子。

如果你想要一种可视化这个过程的方式，看看菲利普·罗伯茨的[放大镜工具](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)。

**结论**

Javascript 事件循环实际上是更复杂流程中最简单的部分之一。它允许您用单线程语言编写非阻塞代码，并将较慢的任务推迟到队列中，而其余的代码运行。

这意味着您可以开发响应更快、效率更高的代码，而不必等待更大的操作。
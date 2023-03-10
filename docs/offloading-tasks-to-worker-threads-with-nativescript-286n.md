# 使用 NativeScript 将任务卸载到工作线程

> 原文：<https://dev.to/progress/offloading-tasks-to-worker-threads-with-nativescript-286n>

性能。性能。性能。

当我们与 NativeScript 开发人员交谈时，性能一直是他们选择 NativeScript 的首要原因之一。这也是我们永远不会满足的。虽然移动硬件在不断改进，但总有办法提高我们开发的应用程序的性能，从而改善用户体验。

除了在 iOS 和 Android 上提供真正的原生 UI，NativeScript 还有一些额外的技巧，允许你为独特的场景定制应用程序，并从宝贵的 CPU 周期中挤出更多的时间。

让我向您介绍一下 NativeScript 上的工作线程。

## 工作者线程

在 web 世界中，worker threads 更好地被称为 web workers(或 service workers)，它允许您将 NativeScript 提供的单线程环境转换为多线程环境。

> **注意:**native script 中的 Workers API 松散地基于 [Web Workers API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 和 [Web Workers 规范](https://www.w3.org/TR/workers/)。

服务工作者(一种网络工作者)很受进步网络应用(PWA)开发者的欢迎，因为他们允许通知和其他非阻塞任务。但是在 PWA 函数结束的地方，NativeScript 会收拾残局。

正如这个 NativeScript 演示应用程序所证明的，99%的时间单线程模型都很好(因为在 UI 线程上运行任何东西都非常快):

[![nativescript demo app](img/2ef0cac5a26098d0b0a69e42a8da1d13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ll5T_Nsd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/workers/nativescript-demo-app.gif)

然而，在某些情况下，操作可能会花费更长时间，从而影响应用程序的用户界面。这可能会导致应用程序感觉比它应该慢。

为了解决这个问题，NativeScript 提供了一个 [Workers API](https://docs.nativescript.org/core-concepts/multithreading-model#workers-api) ，它允许您创建在独立线程上执行的任务，这些线程与主应用程序上下文完全隔离。

让我们看一些例子，看看什么时候(什么时候)在 NativeScript 应用程序中使用 workers。

## 何时使用工人

事实上，至少在理论上，任何可以在同步 UI 操作之外运行的任务都是工作线程的候选对象。

> **提示:**在您开始认为工作线程是解决复杂性能问题的灵丹妙药之前，请确保您考虑到了启动额外线程所需的开销。

CPU 密集型、与 UI 无关的任务可能是工作者线程有益的最好例子。如果你是谷歌分析的*重度*用户，你可能会发现自己在你的应用程序中测量每一个用户行为、页面浏览、功能使用和远程服务调用。即使这些调用应该异步运行，它们仍然会对主线程产生负面影响。

另一个很好的例子是图像处理——这本身就是一项 CPU 密集型任务，当您将它混合到 UI 线程中时，它会变得更加复杂！

> 这些演示应用为 [NativeScript Core](https://github.com/NativeScript/demo-workers) 和 [Angular](https://github.com/NativeScript/worker-loader) 提供了一个卸载图像处理的绝佳例子。

## 样本代码

让我们看看如何自己构建一个非常简单的工人:

```
var worker = new Worker("./workers/my-worker.js"); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码启动一个新线程(运行时实例)并执行引用的脚本(`my-worker.js`)。接下来，我们将希望与工作人员交流和/或从其接收消息或结果。这是通过消息实现的:

```
// send a message to our worker
worker.postMessage("Hello worker thread!");

// receive a message from our worker
worker.onmessage = function(msg) {
    console.log("Received this message from the worker thread: " + msg.data);
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`my-worker.js`文件中，我们将提供`onmessage`方法让*从主线程接收*消息:

```
onmessage = function(msg) {
    console.log("Received this message from the main thread: " + msg.data);

    // perform some crazy cpu-intensive task here!

    // send a message back to the main thread
    postMessage("Hello main thread!");

    close();
} 
```

Enter fullscreen mode Exit fullscreen mode

### 错误处理和终止线程

使用工作线程可能有点可怕，因为线程保持打开可能会消耗资源，并可能会增加内存使用量并破坏性能。因此，请确保捕捉到任何错误，并在完成后关闭/终止线程:

`my-worker.js`中的基本示例:

```
onerror = function(e) {
    console.log("Oh no! Worker thread error: " + e);
    return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

在您的应用程序代码中(如果您想显示错误):

```
worker.onerror = function(e) {
    console.log("Worker thread error: " + e);
} 
```

Enter fullscreen mode Exit fullscreen mode

> **重要提示:**工作线程一直保持活动状态，直到被主线程显式终止或自行关闭！

用`close();`关闭`my-worker.js`中的螺纹(如上图)。或者用`worker.terminate();`终止应用程序代码中的线程。

如果工作线程没有终止/关闭，垃圾收集器将不会收集和释放工作实例。

## 不使用工人时

重要的是要记住，每当你启动一个新的工作线程，你就增加了应用程序的资源和内存占用。这意味着如果你一次旋转太多或者在错误的情况下使用它们，你的应用程序的**性能实际上可能会降低**。

如果你认为一个工作线程可以帮助你处理来自表单的输入，或者显示一个图表，或者许多其他基本的应用程序功能，请再考虑一下。NativeScript 框架已经针对绝大多数场景进行了优化。

在开发过程中，您的最佳选择始终是测量您的应用在各种 iOS 和 Android 物理设备上的功能性能！

也就是说，在缺乏主线程性能的边缘情况下，workers 非常有用。你能做的最好的事情就是测试每个场景，衡量哪条路线最适合你的情况。
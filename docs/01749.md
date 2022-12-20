# 4 分钟到达 Kotlin Coroutines

> 原文：<https://dev.to/azure/5-min-to-kotlin-coroutine-10i3>

好奇 Kotlin coroutine 却不知道如何入门？看这个！

让我们从基础开始，通过编写非常简单的代码块来了解 Kotlin 协程。在不到 5 分钟的时间内，您将学习这些基本概念来开始编码:

1.  暂停功能
2.  背景和范围
3.  建设者
4.  频道

还有许多更棒的功能，但今天我们将重点关注最重要的功能。

## 暂停功能

[![](img/a9ed32ab663830d56e28b0e85cfde873.png)](https://i.giphy.com/media/cKKXNlTYino7hWNXwl/giphy.gif)T3】

```
private suspend fun doACoroutineTask(someValue:Int) ; 
```

Enter fullscreen mode Exit fullscreen mode

`suspend`是定义协程的关键字。这意味着该功能可以暂停和恢复。它可以执行一个长时间运行的操作，并在没有阻塞的情况下等待它完成。

在幕后，挂起函数被编译器转换成另一个不带 suspend 关键字的函数，该函数带有一个 Continuation 类型的附加参数。

## 上下文和范围

[![](img/6f7bafc75d65078a878034af545e2fb7.png)](https://i.giphy.com/media/3oGRFn6oi7cg3xKh68/giphy.gif)T3】

```
 coroutineScope {
        launch(CoroutineName("SomeName")) { 
            doACoroutineTask(taskInput) 
            }
    } 
```

Enter fullscreen mode Exit fullscreen mode

上下文对象包含流和任务/协同例程所必需的信息。Context 是一个持久的索引字典，它从一个 CoroutineContext 映射而来。有许多上下文选项，它们都实现了上下文接口`kotlin.coroutines.CoroutineContext`。在这个例子中，我们使用了保存协程名称的`CoroutineName`，它是我们可以使用的更简单的上下文对象之一。
一个重要的特性是`+`操作符。我们可以将两个上下文连接在一起，构建一个包含更多条目的更大的上下文。但是，我们需要意识到，如果存在密钥复制，我们将会丢失密钥。

上下文在作用域内使用，作用域对象定义协同程序的作用域，并接收上下文来启动作用域，如果没有给定上下文，则作用域采用空的上下文元素。要运行一个挂起的函数，我们必须在一个作用域内调用它。在这个例子中，我们使用名为`coroutineScope`的基本作用域，并调用一个启动函数，该函数通过一个上下文启动`doACoroutineTask`。

## 建设者是桥梁

[![](img/95e2263e2a32acb9397424e8d19fc4a3.png)](https://i.giphy.com/media/3o7TKpkZRUTt5wuwuc/giphy.gif)

协程生成器是协程作用域上的扩展函数，它们继承了它们在。它们是简单的函数，创建一个新的协程来运行给定的挂起函数。它们充当非协同世界和协同世界之间的桥梁。我们从非悬浮/非协同世界中调用构建者来启动悬浮世界。通俗的例子就是`async`和`launch`。

[launch](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html) :在后台启动一个新的协程，并返回对它的引用作为作业对象。launch coroutine builder 是一个“启动并忘记”的工具，因为除了用于处理后台操作的作业实例之外，它不向调用者返回任何结果。它从调用它的范围继承上下文和作业，但是这些可以被覆盖。

[async](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html) :创建一个协程，并返回它未来的结果作为 Deferred 的实现，这可以类似于`promise`，其中任务承诺返回一个结果，同时它被挂起，以便将 CPU 用于其他任务。默认情况下，这个构建器创建一个协程，它被立即调度执行。

```
 coroutineScope {
        launch(CoroutineName("SomeName")) { 
            doACoroutineTask(taskInput) 
            }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们在一个简单的范围内使用`launch`构建器启动一个协程(挂起函数)任务。

## 通道

[![](img/bb019d620df969132ab28d4d66b2f7ac.png)](https://i.giphy.com/media/svVTuBzMvnkly/giphy.gif)

信道是使用`SendChannel`的发送者和使用`ReceiveChannel.*`的接收者之间通信的非阻塞原语。从概念上讲，一个通道类似于`java.util.concurrent.BlockingQueue`，但是它有暂停操作而不是阻塞操作，并且它可以被关闭。我们可以使用通道在协程之间共享信息。通道不是协程概念的一部分，但是在 Kotlin 中，我们可以一起使用它来获得更好的功能和使用。例如:

```
 val ordersChannel = Channel<Order>()
    val job = launch {
        for (order in orders) {
            ordersChannel.send(order)
        }
        ordersChannel.close()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们创建了一个订单通道来接收订单，我们使用这个通道将订单发送给协程。我们实际上还没有使用它，我们只定义了它的输入——来自订单列表的`orders`。

```
 private suspend fun doACoroutineTask(ordersChannel :ReceiveChannel<Order>){
         for(order in ordersChannel){
         // do some suspending task
         sleep(5)
         }
    }

.... 

     coroutineScope {
        launch(CoroutineName("SomeName"){ 
            doACoroutineTask(ordersChannel)  
            }
    } 
```

Enter fullscreen mode Exit fullscreen mode

在第二个块中，我们定义了一个以 channel 为输入的挂起函数。该函数遍历通道，并通过调用一个挂起的任务来处理输入。在这个例子中，挂起的任务是 5 毫秒睡眠。最后，我们从 a `coroutineScope`调用协程，并使用启动生成器在协程世界和主世界之间架起桥梁。从构建器中，我们调用挂起的函数，并为它提供相关的通道。

## 现在你做吧💪：

1.  转到 [github 库](https://github.com/adipola/kotlin-coroutine-falafel-shop)
2.  按下[手表](https://dev.tov)和[星星](https://github.com/adipola/kotlin-coroutine-falafel-shop/stargazers)按钮
3.  克隆项目
4.  打开您的 IDE 并使用`build.gradle`文件导入项目(10 秒视频即将推出)
5.  使用不同的主函数并探索代码。

## 现在你已经知道了开始使用 Kotlin 协程所需的所有基础知识。

#### *在下一篇文章中，我们将讨论并发性以及协程如何处理线程。*

🐦在 Twitter 上关注我，很高兴接受您对主题的建议。
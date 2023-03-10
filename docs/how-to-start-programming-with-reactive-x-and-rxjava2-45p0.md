# 如何开始用 Reactive X 和 RxJava2 编程

> 原文：<https://dev.to/bugfenderapp/how-to-start-programming-with-reactive-x-and-rxjava2-45p0>

[![](img/fd8ccb8d53e01f7fe1c0b885de49a155.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HYp53GPh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2019/02/Rx-screen.jpg)

在发展世界中，我们不断受到有可能改变世界的新创新的冲击。5G、增强现实、机器学习……对于开发人员来说，这可能是一个更加丰富和令人兴奋的时代。

但是，在我们看到的所有变化和突破中，没有什么比反应式编程更令人兴奋，这是一种跨语言和平台的异步哲学。

反应式编程允许我们以完全敏捷的方式构建软件。每当一个特定的值改变时，所有相关的值都会自动改变，所以开发人员不需要自己回去做所有的改变。这意味着，在输入一个特定的任务或功能后，开发人员可以继续下一个任务，而无需等待服务器的响应。

这对像我们这样的团队有着重要的实际意义。我们过去使用命令式的、基于拉的方法，即我们发出一些请求，然后等待服务器响应。但是，反应式编程使我们能够以基于推送的方式工作——当我们向服务器发出请求时，我们知道发送方会发回响应，所以我们继续处理其他请求。如果你一直以极快的速度迭代(就像我们一样)，这是一个巨大的突破。

ReactiveX(或 Reactive Extensions 以提供其全称)提供了一个现代反应式编程的库，所以这就是我们今天要深入探讨的内容。具体来说，我们将解释有效使用 ReactiveX 需要构建的各种组件。您可以在 iOS 和 Android 上使用 ReactiveX(另一个主要优势)，但为了简单起见，我们将重点关注 RxJava2 变体，使用 Kotlin。

### **简言之**

文章将涵盖:

*   反应式编程的三个主要组成部分
*   如何快速轻松地构建这些组件
*   如何防止内存泄漏
*   如何使用多线程

到本文结束时，您应该已经牢固掌握了反应式编程的基础。你甚至可以开始练习自己。

### 入门–可观察的事物

反应哲学基于三个关键要素:观察者、用户和运营商。让我们看看第一个。

本质上，可观察对象发出我们想要更新或改变的数据——换句话说，从原始对象创建一个对象，修改它以提供不同的行为。操作员确切地决定如何修改数据。最后，订户读取可观察对象[发出](http://reactivex.io/documentation/operators/subscribe.html)或推送的数据，并以三种方式之一做出反应:移动到流程的下一阶段、发出错误信号或标记流程完成。

首先，让我们创建一个简单的可观察对象:

```
val observable =
  Observable.create<String> { emitter ->
    emitter.onNext("Hello Rx!")
   emitter.onComplete()
  } 
```

这是创建可观察对象的最基本方法，它发出消息“你好，Rx！”然后完成。

### **现在添加运算符**

操作员通过获取一个可观察值并创建另一个可观察值来工作。这允许我们在一个链中应用操作符，每个操作符建立在下一个之上。

正如我们说过的，最简单的可观察信号是“你好，Rx！”。但是这里有一些其他的:

*   [**Empty*** */****Never**/*** * Throw****](http://reactivex.io/documentation/operators/empty-never-throw.html)->创建一个完成的、不会发出任何东西但永远不会完成的可观察对象和另一个会抛出错误调用 onError 的可观察对象。
*   [**从**](http://reactivex.io/documentation/operators/from.html) - >从其他对象如可预见、未来、承诺和回调中创建可观察值。
*   [**区间**](http://reactivex.io/documentation/operators/interval.html) 创建一个可观测值，它发出一个由特定时间间隔隔开的整数序列。

如果您想查看其他操作符，[这个 ReactiveX 文档](http://reactivex.io/documentation/operators.html)提供了一大堆操作符。

为了了解操作符在实践中是如何工作的，让我们看一下 *map* function_，_ 它可以这样创建。

```
callToServer()
  .map { response -> response.contentLength() }
  .map { size -> size.toString() }
  .subscribeBy { print(it) } 
```

map 函数获取服务器的响应并返回其长度。然后，另一个映射函数将该长度转换为流末尾的字符串；我们只是记录了整个转化流的大小。

我们可以链接任意多的操作符，将结果输出给订阅者。

### 最后……订户

现在，我们需要一个订阅者来消费数据并对数据做出反应。多亏了 Kotlin(以及大多数携带其 Rx 等价物的语言)，我们可以实现 lambda 函数形式的匿名订阅者(一个小的自治函数，你可以在这里阅读更多关于[的内容](https://stackoverflow.com/questions/16501/what-is-a-lambda-function))。

```
observable
  .subscribeBy { item -> print(item) } 
```

我们已经说过，订阅者有三种不同的可能操作:onNext、onComplete 和 onError。这三个都是作为 lambda 函数实现的。

我们可以像这样创建动作:

```
observable.subscribeBy(
  onNext = { item -> print(item) },
  onError = { },
  onComplete = { print("Completed!") }) 
```

当订阅就绪时，可观察对象将在下一个自动调用订阅者*，然后在完成*时调用*(注意，在这种情况下，不会调用 *onError* ，因为可能会抛出任何异常)。*

结果，订阅者(匿名实现的)将喊出“Hello Rx！”然后“完成了！”然后，它将终止。

<figure>[![](img/aaad43c10baca6ee47999c92618259bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v_iRLhl5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bugfender.com/wp-content/uploads/2019/02/programming-screen-1-1024x576.jpg) 

<figcaption>*反应式编程基于一组简单的核心元素。*</figcaption>

</figure>

### **处置**

每次创建订阅时，都会自动创建一个名为 disposable 的对象。这可以随时取消订阅。

这是至关重要的，因为有时当订阅有阻塞内存的危险时，我们需要取消订阅——例如当它持有一个等待删除的类的引用时(就像 java 中的垃圾收集器)。在订阅被释放之前，项目不能被销毁，如果进程依赖于它，就会造成内存泄漏。

subscribe 函数输出一个 *Disposable* 对象，该对象携带一个 *dispose* 函数，该函数取消订阅。该框架还提供了一个*composited disposable*，这个集合包含了一堆*一次性物品*，并且可以将它们排成一行进行处理。

最重要的是，一次性用品还提供了一个名为 *clear* 的功能，顾名思义，它可以清除一次性用品的集合——彻底擦拭它们，以便创建新的集合。如果我们要销毁启动*composited disposable*的整个对象，这是一个很好的选择。

### **调度**

ReactiveX 提供的最有用的特性之一是一种使用调度器处理并发的简单方法——一种实现线程管理的抽象方法。ReactiveX 不仅是线程安全的；它还允许程序员用一个操作符跳过不同的调度程序。不同的机制包括当前线程、调度队列、操作队列、新线程、线程池、执行器等等

有两个主要的操作员与调度程序一起工作，*观察*和*预订。观察*是两者中更常见的:如果您想在不同的调度器上执行工作，这是您最有可能使用的一个。在没有明确指定*观察*的情况下，工作将在生成的任何线程/调度器元素上执行。

我们可以使用许多已经实现的调度程序，而不是实现我们自己的。

*   **Schedulers.io()** - >用于运行 io 阻塞操作，如数据库/网络调用
*   **schedulers . computation()**->用于执行长时间的计算工作或回调(如提供来自传感器的数据和转换数据)
*   **schedulers . new thread()**->它为每个新的执行创建一个新的线程
*   **schedulers . trampoline()**->这个队列在一个参与线程上以 FIFO 的方式工作并执行调度程序。

按照最近显示的例子，我们可以这样添加调度器:

```
callToServer()
  .map { response -> response.contentLength() }
  .map { size -> size.toString() }
  .observeOn(Schedulers.io())
  .subscribeOn(AndroidSchedulers.mainThread())
  .subscribeBy { print(it) } 
```

如您所见，我们将网络调用运行到一个特定的 I/O 线程中，然后在主线程中运行订阅者的代码。我们使用了 AndroidSchedulers.mainThread()，因为我们在这个例子中使用了 Android Studio 和 Kotlin，但是每种语言/框架都有自己的主线程调度器。

### **下一步**

好了，我们已经到了开头的结尾。如果您喜欢您在这里读到的内容，并且希望更深入地研究反应式编程，那么有大量的资源可供您使用。

ReactiveX 社区页面包含了大量有用的文档，在过去对我们帮助很大。您也可以查看 [GitHub](https://github.com/ReactiveX) 页面和 [RxMarbles](https://rxmarbles.com/) 页面，获取更抽象但更生动的文档。

### **还有一件事……**

鉴于您已经完成了这一步，我们认为您不会太介意我们为 Bugfender(我们的远程日志记录工具，具有内置报告和应用内反馈)添加一些插件。Bugfender 允许您跟踪使用 RxJava2 构建的应用程序的日志，其易于使用的基于云的控制台非常适合那些刚刚掌握这种编程形式的人。更多信息请访问[https://bugfender.com/](https://bugfender.com/)。
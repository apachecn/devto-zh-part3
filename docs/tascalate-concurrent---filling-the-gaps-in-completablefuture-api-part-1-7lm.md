# Tascalate 并发——填补 CompletableFuture API 中的空白(第 1 部分)

> 原文：<https://dev.to/vsilaev/tascalate-concurrent---filling-the-gaps-in-completablefuture-api-part-1-7lm>

[Tascalate 并发](https://github.com/vsilaev/tascalate-concurrent)库提供了一个 [CompletionStage](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletionStage.html) 接口和相关类的实现，这些接口和相关类旨在支持长时间运行的阻塞任务(通常是 I/O 绑定的)。这个功能增强了唯一的 Java 8 内置实现 [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) ，它主要支持计算任务。此外，该库有助于应对众多异步编程挑战，如处理超时、重试/轮询功能、编排多个并发计算的结果等。

该库以多版本 JAR 的形式提供，可以作为类路径库与 Java 8 一起使用，也可以作为模块与 Java 9+一起使用。

# 为什么一个 CompletableFuture 还不够？

与 [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) 实现相关的几个缺点使其在实际异步编程中的使用变得复杂，尤其是当您必须处理 I/O 绑定的可中断任务时:

1.  `CompletableFuture.cancel()` [方法](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html#cancel-boolean-)不中断底层线程；它只是将未来置于异常完整的状态。因此，即使你在传递给`thenApplyAsync` / `thenAcceptAsync` / etc 的函数中使用任何阻塞调用，这些函数也会运行到最后，永远不会被中断。请看[Tomasz Nurkiewicz《CompletableFuture》不能被打断](http://www.nurkiewicz.com/2015/03/completablefuture-cant-be-interrupted.html)。
2.  默认情况下，`CompletableFutrure`的所有`*Async`合成方法都使用`ForkJoinPool.commonPool()` ( [见此处](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html#commonPool--))，除非明确指定[执行者](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executor.html)。这个线程池在所有 [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) 和部署在同一个 JVM 上的所有应用程序的所有并行流之间共享。这种硬编码、不可配置的线程池完全不受应用程序开发人员的控制，难以监控和扩展。因此，在健壮的实际应用中，您应该总是指定自己的`Executor`。通过 Java 9+中的 API 增强，您可以修复这个缺点，但是这需要一些自定义编码。
3.  此外，内置的 Java 8 并发类提供了非常不方便的 API 来组合几个[completion stage](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletionStage.html)-s .`CompletableFuture.allOf`/`CompletableFuture.anyOf`方法只接受 [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) 作为参数；如果不先将任意的 [CompletionStage](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletionStage.html) -s 转换成 [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) ，你就没有办法组合它们。此外，前面提到的`CompletableFuture.allOf`的返回类型被声明为`CompletableFuture<Void>`——因此您无法方便地提取所提供的每个期货的单个结果。`CompletableFuture.anyOf`在这方面更糟；欲知更多详情，请点击此处阅读:[CompletableFuture in Action](http://www.nurkiewicz.com/2013/05/java-8-completablefuture-in-action.html)(参见缺点)作者托马斯·努尔凯维奇。
4.  只有在 Java 9 中，超时/延迟支持才被引入到 [CompletableFuture](https://docs.oracle.com/javase/9/docs/api/java/util/concurrent/CompletableFuture.html) 中，所以在 Java 8 上运行的仍然被广泛支持的应用程序没有这个重要的功能。此外，一些设计决策，如使用延迟执行器而不是“延迟”操作符，多少有些问题。有许多免费的开源库可以解决前面提到的一些缺点。然而，没有一个提供可中断的实现`CompletionStage`，也没有一个连贯地解决*所有的*问题。

# 怎么用？

要使用一个库，你必须添加一个 Maven 依赖项

```
<dependency>
    <groupId>net.tascalate.concurrent</groupId>
    <artifactId>net.tascalate.concurrent.lib</artifactId>
    <version>0.7.1</version>
</dependency> 
```

# 里面是什么？

## 1。承诺界面

这是 Tascalate 并发库的核心接口。最好用下面的公式来描述:

```
Promise == CompletionStage + Future 
```

即结合了阻塞[未来](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html)的 API，包括[完成阶段](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletionStage.html)的 API 的`cancel(boolean mayInterruptIfRunning)` [方法](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html#cancel-boolean-)、*和*组合能力。重要的是， [CompletionStage](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletionStage.html) API 的所有组合方法(`thenAccept`、`thenCombine`、`whenComplete`等)。)也重新申报返回`Promise`。

引入合并了`CompletionStage`和`Future`的接口的决定与`CompletableFuture` API 的设计是一致的。此外，还增加了几个`CompletableFuture` API 的有用方法:

```
T getNow(T valueIfAbsent) throws CancellationException, CompletionException;
T getNow(Supplier<? extends T> valueIfAbsent) throws CancellationException, CompletionException;
T join() throws CancellationException, CompletionException; 
```

因此，在许多情况下，使用`Promise`作为`CompletableFuture`的替代应该非常简单。

除此之外，`Promise` API 中有许多操作符可以处理超时和延迟，覆盖默认的异步执行器等等。所有这些都将在后面讨论。

在讨论`Promise`接口时，必须提到附带的类`Promises`，它提供了几个有用的方法来使第三方`CompletionStage`(包括标准`CompletableFuture`)适应`Promise` API。首先，有两个单元操作创建成功/故障解决`Promise` -es:

```
static <T> Promise<T> success(T value)
static <T> Promise<T> failure(Throwable exception) 
```

第二，有一个适配器方法`from` :

```
static <T> Promise<T> from(CompletionStage<T> stage) 
```

它的行为如下:

1.  如果提供的`stage`已经是一个`Promise`,那么它不变地返回
2.  如果`stage`是一个`CompletableFuture`，那么返回一个特别定制的包装器。
3.  如果`stage`额外实现了`Future`，那么返回一个专门的包装器，它代表了在`Future` API 中定义的所有阻塞方法
4.  否则，在异步`CompletionStage` API 的基础上，用足够好的阻塞`Future` API 实现创建通用包装器。总而言之，返回的包装器将尽可能多的功能委托给提供的`stage`和 *never* 求助于[completion stage . to completablefuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletionStage.html#toCompletableFuture--)，因为在 Java 8 API 中它是一个可选的方法。来自文档:“不选择与他人进行互操作的 CompletionStage 实现可能会引发 UnsupportedOperationException。”(此文本在 Java 9+中被删除)。一般来说，Tascalate 并发库不依赖于这种方法，应该可以与任何最小的(但有效的)`CompletionStage`实现互操作。

需要强调的是，从`Promises.success`、`Promises.failure`和`Promises.from`方法返回的`Promise` -s 可以像`CompletableFuture`一样被取消，但一般情况下是不可中断的，而中断取决于具体的实现。接下来我们讨论 Tascalate 并发库提供的可中断的`Promise`的具体实现——类`CompletableTask`。

## 2。CompletableTask

这就是这个项目开始的原因。`CompletableTask`是针对长时间运行的阻塞任务的`Promise` API 的实现。
通常，要创建一个`CompletableTask`，你应该马上将[供应商](https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html) / [可运行](https://docs.oracle.com/javase/8/docs/api/java/lang/Runnable.html)提交给[执行人](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executor.html)，与[可完成未来](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) :
类似

```
Promise<SomeValue> p1 = CompletableTask.supplyAsync(() -> {
  return blockingCalculationOfSomeValue();
}, myExecutor);

Promise<Void> p2 = CompletableTask.runAsync(this::someIoBoundMethod, myExecutor); 
```

上例中的`blockingCalculationOfSomeValue`和`someIoBoundMethod`可以拥有 I/O 代码，使用阻塞队列，在常规的 Java-s `Future`上进行阻塞 get 等等。如果稍后您决定取消返回的承诺，则相应的`blockingCalculationOfSomeValue`和`someIoBoundMethod`将被中断(如果尚未完成)。

在与 I/O 相关的功能领域中，像连接超时、文件丢失或被锁定这样的故障非常常见，并且经常使用检查异常机制来发出故障信号。因此，这个库提供了一个 API 的入口点，它接受[可调用的](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Callable.html)，而不是`Supplier` :

```
// Notice the checked exception in the method signature
byte[] loadFile(File file) throws IOException {
    byte[] result = ... //load file content;
    return result;
}
...
ExecutorService executorService = Executors.newFixedThreadPool(6);
Promise<byte[]> contentPromise = CompletableTask.submit(
    () -> loadFile(new File("./myfile.dat")), 
    executorService
); 
```

此外，还有 2 个单元操作来创建一个`CompletableTask` :
a. `CompletableTask.asyncOn(Executor executor)`
返回一个已经完成的空值`Promise`，它被“绑定”到指定的执行器。即传递给`Promise`(如`thenApplyAsync` / `thenAcceptAsync` / `whenCompleteAsync`等异步组合方法的任何函数。)将使用此执行器执行，除非通过显式合成方法参数重写执行器。此外，任何嵌套的合成调用都将使用相同的执行器，如果没有通过显式合成方法参数重新定义的话:

```
CompletableTask
  .asyncOn(myExecutor)
  .thenApplyAsync(myValueGenerator)
  .thenAcceptAsync(myConsumer)
  .thenRunAsync(myAction); 
```

所有的`myValueGenerator`、`myConsumer`、`myAction`都将使用`myExecutor`来执行。
b .`CompletableTask.complete(T value, Executor executor)`T7】同上，但起点是一个用指定值
完成的`Promise`

```
CompletableTask
   .complete("Hello!", myExecutor)
   .thenApplyAsync(myMapper)
   .thenApplyAsync(myTransformer)   
   .thenAcceptAsync(myConsumer)
   .thenRunAsync(myAction); 
```

所有的`myMapper`、`myTransformer`、`myConsumer`、`myAction`都将使用`myExecutor`来执行。

至关重要的是，对于作为参数提供的函数，所有组合承诺都支持真正的取消(包括中断线程):

```
Promise<?> p1 = CompletableTask.asyncOn(myExecutor)
Promise<?> p2 = p1.thenApplyAsync(myValueGenerator)
Promise<?> p3 = p2.thenRunAsync(myAction);

...
p2.cancel(true); 
```

在上面的例子中，如果已经在进行中，`myValueGenerator`将被中断。`p2`和`p3`都将以失败结算:`p2`以[取消异常](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CancellationException.html)和`p3`以[完成异常](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletionException.html)。

你可能会注意到，上面使用了术语“异步组合方法”，以及示例中的`*Async`调用(如`thenApplyAsync`、`thenRunAsync`)。这不是偶然的:API 的非异步方法是不可中断的。设计决策的基础是，调用异步方法会不可避免地产生将命令放入执行器队列、隐式启动新线程等开销。对于简单的、非阻塞的方法，比如小的计算、琐碎的转换等等，这种开销可能超过方法本身的执行时间。所以指导方针是:对繁重的 I/O 绑定的阻塞任务使用异步合成方法，对(通常是轻量级的)计算使用非异步合成方法。

值得一提的是，由它组成的`CompletableTask` -s 和`Promise` -s 可能只有*是可中断的，如果使用的`Executor`本质上是可中断的。比如 [ThreadPoolExecutor](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ThreadPoolExecutor.html) 支持可中断任务，而 [ForkJoinPool](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html) 不支持！*

## 3。覆盖默认异步执行器

`CompletableFuture`实现的一个缺陷是它如何与默认的异步执行器一起工作。考虑下面的例子:

```
CompletionStage<String> p1 = CompletableFuture.supplyAsync(this::produceValue, executorInitial);
CompletionStage<String> p2 = p1.thenApplyAsync(this::transformValueA);
CompletionStage<String> p3 = p2.thenApplyAsync(this::transformValueB, executorNext);
CompletionStage<String> p4 = p3.thenApplyAsync(this::transformValueC); 
```

对`produceValue`的调用将在`executorInitial`上执行——它是显式传递的。然而，对`transformValueA`的呼叫将在...`ForkJoinPool.commonPool()`！嗯嗯...大概这是有道理的，但是如何强制使用默认的替代执行程序呢？不会吧！或许这可以通过更深层次的调用实现？答案又是“不”！对`transformValueB`的调用在显式提供的`executorNext`上运行。但是下一次调用，`transformValueC`将在...你猜对了...`ForkJoinPool.commonPool()`！

所以，一旦你在 JEE 环境中使用了`CompletableFuture`，你必须将 [ManagedExecutorService](https://docs.oracle.com/javaee/7/api/javax/enterprise/concurrent/ManagedExecutorService.html) 的显式实例传递给每一个方法调用。不太方便！公平地说，使用 Java 9+ API，您可以通过子类化`CompletableFuture`并覆盖两个方法来重新定义这种行为: [defaultExecutor](https://docs.oracle.com/javase/9/docs/api/java/util/concurrent/CompletableFuture.html#defaultExecutor--) 和 [newIncompleteFuture](https://docs.oracle.com/javase/9/docs/api/java/util/concurrent/CompletableFuture.html#newIncompleteFuture--) 。另外，你必须定义你自己的“入口点”，而不是标准的`CompletableFuture.runAsync`和`CompletableFuture.supplyAsync`。

情况正好相反。让我们重写上面的例子:

```
CompletionStage<String> p1 = CompletableTask.supplyAsync(this::produceValue, executorInitial);
CompletionStage<String> p2 = p1.thenApplyAsync(this::transformValueA);
CompletionStage<String> p3 = p2.thenApplyAsync(this::transformValueB, executorNext);
CompletionStage<String> p4 = p3.thenApplyAsync(this::transformValueC); 
```

显然，对`produceValue`的调用将在`executorInitial`上执行。但是现在，对`transformValueA`的调用也将在`executorInitial`上执行！更深层次的调用呢？对`transformValueB`的调用在显式提供的`executorNext`上运行。下一次调用，`transformValueC`将在...检查你的直觉...`executorNext`。这背后的逻辑如下:最近显式指定的`Executor`将用于所有没有显式`Executor`参数的嵌套异步组合方法。

显然，一刀切的情况很少出现。因此有两个额外的选项来指定默认的异步执行器:
A. `CompletableTask`有一个重载的方法:

```
public static Promise<Void> asyncOn(Executor executor, boolean enforceDefaultAsync) 
```

当`enforceDefaultAsync`为`true`时，所有没有显式`Executor`参数的嵌套异步合成方法将使用提供的执行器，即使先前的合成方法使用替代的`Executor`。这有点类似于`CompletableFuture`,但是能够在最初显式设置默认的异步执行器。
B. `Promise`界面有如下操作:

```
Promise<T> defaultAsyncOn(Executor executor) 
```

返回的 decorator 将为所有嵌套的异步合成方法使用指定的执行器，而不使用显式的`Executor`参数。因此，在任何时候，您都可以切换到所需的默认异步执行器，并在所有嵌套的组合调用中继续使用它。

总而言之，使用 Tascalate Concurrent，您有以下选项来控制默认的异步执行器:

1.  传递给`*Async`方法的最新显式`Executor`用于派生`Promise` -s -默认选项。
2.  传递给根`CompletableTask.asyncOn(Executor executor, true)`调用的单个默认`Executor`通过整个链传播。不过，这是 Java 9+中唯一支持`CompletableFuture`的变体，带有自定义编码。
3.  用`defaultAsyncOn(Executor executor)`重新定义所有衍生的`Promise`的`Executor`。)世界，库用户唯一的责任就是始终如一地使用这些选项！最后要提到的是，当您想在完成标准`CompletableFuture`后启动可中断阻塞方法时，这是一个典型的任务。下面的实用方法在`CompletableTask`中定义:

```
public static <T> Promise<T> waitFor(CompletionStage<T> stage, Executor executor) 
```

大致来说，这是下面的捷径:

```
CompletableTask.asyncOn(executor).thenCombine(stage, (u, v) -> v); 
```

该方法的典型用法是:

```
TaskExecutorService executorService = TaskExecutors.newFixedThreadPool(3);
CompletableFuture<String> replyUrlPromise = sendRequestAsync();
Promise<byte[]> dataPromise = CompletableTask.waitFor(replyUrlPromise, executorService)
    .thenApplyAsync(url -> loadDataInterruptibly(url)); 
```

返回的`dataPromise`可能会在以后被取消，如果到时候没有完成`loadDataInterruptibly`将被中断。

## 4。超时设定

任何健壮的应用程序都必须处理出错时的情况。取消耗时太长的操作的能力从第一天起就存在于库中。但是,“太长”的定义最初留给了应用程序代码。然而，实践表明，如果库中缺少经过验证和彻底测试的超时相关内容，就会导致应用程序中出现复杂、重复且容易出错的代码。因此 Tascalate Concurrent 被扩展来解决这个遗漏。

本库提供了以下操作来控制`Promise`(在`Promise`接口中声明)的执行时间:

```
<T> Promise<T> orTimeout(long timeout, TimeUnit unit[, boolean cancelOnTimeout = true])
<T> Promise<T> orTimeout(Duration duration[, boolean cancelOnTimeout = true]) 
```

这些方法创建了一个新的`Promise`,当原始承诺在给定的超时时间内完成时，该新的`Promise`成功/异常结算；或者当时间到期时，用 [TimeoutException](https://docs.oracle.com/javase/8/docs/api/index.html?java/util/concurrent/TimeoutException.html) 异常解决。在任何情况下，处理代码都在原始`Promise`的默认异步[执行器](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executor.html)上执行。

```
Executor myExecutor = ...; // Get an executor
Promise<String> callPromise = CompletableTask
    .supplyAsync( () -> someLongRunningIoBoundMehtod(), myExecutor )
    .orTimeout( Duration.ofSeconds(3) );

Promise<?> nextPromiseSync = callPromise.whenComplete((v, e) -> processResultSync(v, e));
Promise<?> nextPromiseAsync = callPromise.whenCompleteAsync((v,e) -> processResultAsync(v, e)); 
```

在上面的例子中，`callPromise`将在 3 秒内成功/异常地完成`someLongRunningIoBoundMehtod`的执行，或者异常地完成`TimeoutException`。

值得一提的是，如果超时被触发，那么*`processResultSync`和`processResultAsync`都将被`myExecutor`执行——这个规则适用于所有与超时相关的方法。*

 *可选的`cancelOnTimeout`参数定义当时间到期时是否取消原来的`Promise`；省略时隐含为真。所以在上面的例子中，如果完成时间超过 3 秒，那么`the someLongRunningIoBoundMehtod`就会被中断。注意:任何`Promise`在超时时都是可取消的，甚至是通过`Promises.from(stage)`创建的包装器，但是只有`CompletableTask`是可中断的！

在大多数情况下，取消最初的超时承诺是一种可取的行为，但并不总是如此。实际上，“先警告后取消”的情况并不少见，其中“警告”可能是日志记录、发送通知电子邮件、在 UI 上向用户显示消息等。该库提供了一个选项来设置几个不可取消的超时，如下例:

```
Executor myExecutor = ...; // Get an executor
Promise<String> resultPromise = CompletableTask
    .supplyAsync( () -> someLongRunningIoBoundMehtod(), executor );

// Show UI message to user to let him/her know that everything is under control
Promise<?> t1 = resultPromise
    .orTimeout( Duration.ofSeconds(2), false )
    .exceptionally( e -> {
      if (e instanceof TimeoutException) {
        UI.showMessage("The operation takes longer than expected, please wait...");
      }
      return null;
    }, false); 

// Show UI confirmation to user to let him/her cancel operation explicitly
Promise<?> t2 = resultPromise
    .orTimeout( Duration.ofSeconds(5), false )
    .exceptionally( e -> {
      if (e instanceof TimeoutException) {
        UI.clearMessages();
        UI.showConfirmation("Service does not respond. Do you whant to cancel (Y/N)?");
      }
      return null;
    }, false); 

// Cancel in 10 seconds
resultPromise.orTimeout( Duration.ofSeconds(10), true ); 
```

请注意，超时是从调用`orTimeout`方法开始的。因此，如果你有一系列未解决的承诺以`orTimeout`结束，那么整个承诺链应该在给定的时间内完成:

```
Executor myExecutor = ...; // Get an executor
Promise<String> parallelPromise = CompletableTask
    .supplyAsync( () -> someLongRunningDbCall(), executor );
Promise<List<String>> resultPromise = CompletableTask
    .supplyAsync( () -> someLongRunningIoBoundMehtod(), executor )
    .thenApplyAsync( v -> converterMethod(v) )
    .thenCombineAsync(parallelPromise, (u, v) -> Arrays.asList(u, v))
    .orTimeout( Duration.ofSeconds(5) ); 
```

在最新的例子中，当且仅当所有的`someLongRunningIoBoundMehtod`、`converterMethod`甚至`someLongRunningDbCall`都在 5 秒内完成时，才会成功解决`resultPromise`。如果需要限制单步执行时间，请使用标准的`CompletionStage.thenCompose`方法。比如说，在前面的例子中，我们只需要限制`converterMethod`的执行时间。然后修改后的链将看起来像:

```
Promise<List<String>> resultPromise = CompletableTask
    .supplyAsync( () -> someLongRunningIoBoundMehtod(), executor )
    // Restict only execution time of converterMethod
    // -- start of changes
    .thenCompose( v -> 
        CompletableTask.complete(v, executor)
                       .thenApplyAsync(vv -> converterMethod(vv))
                       .orTimeout( Duration.ofSeconds(5) )
    )
    // -- end of changes
    .thenCombineAsync(parallelPromise, (u, v) -> Arrays.asList(u, v))
    ; 
```

此外，在*最初的*示例中，只有对`thenCombineAsync`的调用会在超时时被取消(链中的最后一个)，要取消整个链，必须使用`DependentPromise`接口的功能(将在下一篇文章中讨论)。

在`Promise`接口中声明的另一个有用的超时相关方法是:

```
<T> Promise<T> onTimeout(T value, long timeout, TimeUnit unit[, boolean cancelOnTimeout = true])
<T> Promise<T> onTimeout(T value, Duration duration[, boolean cancelOnTimeout = true])
<T> Promise<T> onTimeout(Supplier<? extends T>, long timeout, TimeUnit unit[, boolean cancelOnTimeout = true])
<T> Promise<T> onTimeout(Supplier<? extends T>, Duration duration[, boolean cancelOnTimeout = true]) 
```

`onTimeout`系列方法在所有方面都与`orTimeout`方法相似，唯一明显的区别是——当时间到期时，它们不是用`TimeoutException`例外地完成结果`Promise`,而是用(直接或通过[供应商](https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html))提供的替代`value`成功解决:

```
Executor myExecutor = ...; // Get an executor
Promise<String> callPromise = CompletableTask
    .supplyAsync( () -> someLongRunningIoBoundMehtod(), executor )
    .onTimeout( "Timed-out!", Duration.ofSeconds(3) ); 
```

该示例显示，`callPromise`将在 3 秒内成功/异常地作为`someLongRunningIoBoundMehtod`执行的结果，或者在超时时使用默认值`"Timed-out!"`进行结算。

提到 Java 9+中的`Promise.orTimeot / onTimeout`和`CompletableFuture.orTimeout / completeOnTimeout`之间的关键区别是很重要的。在 Tascalate 并发中，两个操作都返回一个*新的* `Promise`，即可以单独取消，而不取消原来的`Promise`。此外，当时间到期时，原来的`Promise`不会以`TimeoutException`结束，而是以`CancellationException`结束(在`orTimeout([duration], true)`或`orTimeout([duration])`的情况下)。Java 9+中`CompletableFuture`的行为完全不同:超时相关的操作只是“副作用”，返回值是原来的`CompletableFuture`本身。因此对`completableFuture.orTimeout(100, TimeUnit.MILLIS).cancel()`的调用将取消`completableFuture`本身，一旦设置了超时就没有办法恢复。相应地，当时间到期时，原始的`completableFuture`将与`TimeoutException`一起异常完成。

最后，`Promise`接口提供了一个将延迟插入调用链的选项:

```
<T> Promise<T> delay(long timeout, TimeUnit unit[, boolean delayOnError = true])
<T> Promise<T> delay(Duration duration[, boolean delayOnError = true]) 
```

延迟仅在原始`Promise`成功或异常完成后开始(不像`orTimeout` / `onTimeout`方法，超时立即开始)。产生的延迟`Promise`在指定的超时后解决，结果与原始`Promise`相同。最新方法的参数- `delayOnError` -指定如果最初的承诺被异常解决，我们是否应该延迟，默认情况下这个参数是`true`。如果`false`，那么延迟`Promise`在失败的原始`Promise`之后立即完成。

```
Executor myExecutor = ...; // Get an executor
Promise<String> callPromise1 = CompletableTask
    .supplyAsync( () -> someLongRunningIoBoundMehtod(), executor )
    .delay( Duration.ofSeconds(1) ) // Give a second for CPU to calm down :)
    .thenApply(v -> convertValue(v));

Promise<String> callPromise2 = CompletableTask
    .supplyAsync( () -> aletrnativeLongRunningIoBoundMehtod(), executor )
    .delay( Duration.ofSeconds(1), false ) // Give a second for CPU to calm down ONLY on success :)
    .thenApply(v -> convertValue(v)); 
```

像其他与超时相关的方法一样，`convertValue`在初始`Promise`的默认异步`Executor`上被调用。

您可能会注意到，延迟可能只在链的中间引入，但是如果您想取消整个链的执行，该怎么办呢？就从坚定的承诺开始吧！

```
// Option 1
// Interruptible tasks chain on the executor supplied
CompletableTask.asyncOn(executor)
    .delay( Duration.ofSeconds(5) )
    .thenApplyAsync(ignore -> produceValue());

// Option2
// Computational tasks on ForkJoinPool.commonPool()
Promises.from(CompletableFuture.completedFuture(""))
    .delay( Duration.ofSeconds(5) )
    .thenApplyAsync(ignore -> produceValue()); 
```

只要回退执行不是非常罕见的情况，本库在`CompletableTask`类中提供了以下方便快捷的方式:

```
static Promise<Duration> delay(long timeout, TimeUnit unit, Executor executor);
static Promise<Duration> delay(Duration duration, Executor executor); 
```

注意，在 Java 9+中，选择了不同的方法来实现延迟——没有为`CompletableFuture`对象定义相应的操作，您应该使用延迟的`Executor`。详情请阅读关于[completablefuture . delayedexecutor](https://docs.oracle.com/javase/9/docs/api/java/util/concurrent/CompletableFuture.html#delayedExecutor-long-java.util.concurrent.TimeUnit-java.util.concurrent.Executor-)方法的文档。

## 5。合并几个完成阶段。

实用程序类`Promises`提供了一组丰富的方法来组合几个`CompletionStage` -s，将`CompletableFuter.allOf / anyOf`有限的功能远远抛在后面:

1.  该库可以处理任何`CompletionStage`实现，而不需要先将参数转换为`CompletableFuture`(并且`CompletionStage.toCompletableFuture`是一个可选操作，至少在 Java 8 中是这样记载的)。
2.  可以传递一个数组或者一个`CompletionStage`的`List`作为参数。
3.  产生的`Promise`让访问已解决的`CompletionStage`的单个结果通过。
4.  一旦知道操作结果，就可以选择取消未结算的`CompletionStage` -s。
5.  可选地，您可以指定是否容忍个别失败，只要它们不影响最终结果。
6.  一般情况下，在 N 个通过的承诺中，M 个成功完成场景是可能的。让我们回顾一下相关的方法，从最简单的到最先进的。

```
static <T> Promise<List<T>> all([boolean cancelRemaining=true,] 
                                 CompletionStage<? extends T>... promises)
static <T> Promise<List<T>> all([boolean cancelRemaining=true,] 
                                List<? extends CompletionStage<? extends T>> promises) 
```

当作为参数传递的所有`CompletionStage` -s 正常完成时，返回正常完成的承诺；如果任何承诺异常完成，则结果承诺也会异常完成。

```
static <T> Promise<T> any([boolean cancelRemaining=true,] 
                          CompletionStage<? extends T>... promises)
static <T> Promise<T> any([boolean cancelRemaining=true,] 
                          List<? extends CompletionStage<? extends T>> promises) 
```

当任何作为参数传递的`CompletionStage`正常完成时，返回一个正常完成的承诺(race 是可能的)；如果所有承诺都异常完成，则结果承诺也会异常完成。

```
static <T> Promise<T> anyStrict([boolean cancelRemaining=true,] CompletionStage<? extends T>... promises)
static <T> Promise<T> anyStrict([boolean cancelRemaining=true,] 
                                List<? extends CompletionStage<? extends T>> promises) 
```

当任何作为参数传递的`CompletionStage`正常完成时，返回一个正常完成的承诺(race 是可能的)；如果任何承诺在第一个结果可用之前异常完成，则结果承诺也异常完成(与非严格变体不同，在非严格变体中，如果任何结果都可用，则忽略异常)。

```
static <T> Promise<List<T>> atLeast(int minResultsCount, [boolean cancelRemaining=true,] 
                                    CompletionStage<? extends T>... promises)
static <T> Promise<List<T>> atLeast(int minResultsCount, [boolean cancelRemaining=true,] 
                                    List<? extends CompletionStage<? extends T>> promises) 
```

`any`方法的推广。当作为参数传递的`CompletionStage` -s 中至少有`minResultCount`
正常完成时，返回正常完成的承诺(比赛是可能的)；如果少于`minResultCount`个承诺正常完成，则结果承诺异常完成。

```
static <T> Promise<List<T>> atLeastStrict(int minResultsCount, [boolean cancelRemaining=true,] 
                                          CompletionStage<? extends T>... promises)
static <T> Promise<List<T>> atLeastStrict(int minResultsCount, [boolean cancelRemaining=true,] 
                                          List<? extends CompletionStage<? extends T>> promises) 
```

`anyStrict`方法的推广。当作为参数传递的`CompletionStage` -s 中至少有`minResultCount`个正常完成时，返回正常完成的承诺(比赛是可能的)；如果在结果的`minResultCount`之前异常完成的任何承诺可用，则结果承诺也异常完成(与非严格变体不同，在非严格变体中，如果成功结果的`minResultsCount`可用，则忽略异常)。

以上所有方法都有一个可选参数`cancelRemaining`。省略时，隐含的意思是`cancelRemaining = true`。`cancelRemaining`参数定义一旦操作结果已知，是否急切取消剩余的`promises`，即足够多的`promises`通过成功结算*或*一些`CompletionStage`在严格版中例外完成。

组合`CompletionStage` -s 的每个操作都有重载版本，接受`CompletionStage` -s 的[列表](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)或`CompletionStage`的 varagr 数组

除了返回单值承诺的`any` / `anyStrict`方法之外，所有其他组合方法都返回每个成功完成的承诺的值列表，位于相同的索引位置。如果给定位置的承诺根本没有结算，或者失败(非严格版)，则结果列表中对应的项目为`null`。如果必要的数字或`promises`没有成功完成，或者任何一个在严格版本中异常完成，那么产生的`Promise`以类型`MultitargetException`的失败解决。应用程序开发人员可以检查`MultitargetException.getExceptions()`以检查每个通过的具体`CompletionStage`的确切故障。
返回的`Promise`具有以下特征:

1.  取消结果`Promise`将取消所有作为参数传递的`CompletionStage-s`。
2.  结果`Promise`的默认异步执行器是未定义的，也就是说，它可以是`ForkJoin.commonPool`或者任何作为参数传递的`CompletionStage`所使用的任何`Executor`。为确保后续异步操作使用必要的默认`Executor`，请对结果应用`defaultAsyncOn(myExecutor)`。

Tascalate 并发库提供的特性不止于此。还有更有趣的东西，比如重试/轮询功能，控制链`Promises`的取消，对`ExecutorService`的扩展等等。但是这篇文章已经太长了，所以留到下一次再讨论。同时，您可以查看 [Tascalate 并发](https://github.com/vsilaev/tascalate-concurrent)库的主页，获取最新的文档。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[vsilaev](https://github.com/vsilaev)/[tascalate-concurrent](https://github.com/vsilaev/tascalate-concurrent)

### 实现阻塞(IO 绑定)可取消的 Java . util . concurrent . completion stage 和 Java . util . concurrent . executorservice-s 的相关扩展

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Maven Central](img/0198bf2aecde21b3ea057a6446406e18.png)](https://search.maven.org/artifact/net.tascalate/net.tascalate.concurrent/0.9.0/jar)[![GitHub release](img/2db4b40145d7792fef5934feadbd7643.png)](https://github.com/vsilaev/tascalate-concurrent/releases/tag/0.9.0)[![license](img/6f22c2984b18f2dae712971503925205.png)](http://www.apache.org/licenses/LICENSE-2.0.txt)

# tascalate-并发

该库提供了一个 [CompletionStage](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletionStage.html) 接口和相关类的实现，这些类旨在支持长期运行的阻塞任务(通常是 I/O 绑定的)。这个功能增强了唯一的 Java 8 内置实现 [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) ，它主要支持计算任务。此外，该库有助于应对众多异步编程挑战，如处理超时、重试/轮询功能、编排多个并发计算的结果等。

从版本 [0.7.0](https://github.com/vsilaev/tascalate-concurrent/releases/tag/0.7.0) 开始，这个库作为一个多版本 JAR 提供，可以作为类路径库与 Java 8 一起使用，也可以作为一个模块与 Java 9+一起使用。

重要！

在版本 [0.8.0](https://github.com/vsilaev/tascalate-concurrent/releases/tag/0.8.0) 中，工件被重命名为新名称:

```
<dependency&gt
    <groupId>net.tascalate</groupId&gt
    <artifactId>net.tascalate.concurrent</artifactId>
    <version>0.9.0</version> <!-- Any version above 0.8.0, the latest one is recommended -->
</dependency>
```

旧名称

```
<dependency&gt
    <groupId>net.tascalate.concurrent</groupId>
    <artifactId>net.tascalate.concurrent.lib</
```

…</article>

[View on GitHub](https://github.com/vsilaev/tascalate-concurrent)

# 鸣谢

`CompletableTask`类层次结构的内部实现细节很大程度上受到了[卢克亚什·křečan](https://github.com/lukas-krecan)所做的工作的启发[。关于他的库的描述可以在 DZone 上的一篇两部分文章中找到:](https://github.com/lukas-krecan/completion-stage)[第一部分](https://dzone.com/articles/implementing-java-8)和[第二部分](https://dzone.com/articles/implementing-java-8-0)。对于那些想更好地理解`CompletableTask`内部的人来说，这是一篇值得一读的文章。*
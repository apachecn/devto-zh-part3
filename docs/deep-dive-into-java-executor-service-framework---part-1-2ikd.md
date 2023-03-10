# 深入研究 Java Executor 服务框架——第 1 部分

> 原文：<https://dev.to/suhaspbharadwaj/deep-dive-into-java-executor-service-framework---part-1-2ikd>

## 简介:

大家好！这是我开始在我的领域下写的第一个技术博客。我以前是博客帖子的狂热消费者，但现在是时候转移到我自己在互联网上的甜蜜空间，作为内容的生产者，因为我觉得与社区分享我的学习很重要。

下面是原文的[链接](https://suhaspbharadwaj.com/executorservice)！

在这篇博客文章中，我想详细介绍 JDK 5 中发布的 Executor 服务框架的实现。它用于使用诸如 ThreadPool 和 ThreadFactory 之类的概念来运行 Runnable 对象。

如果您计划在项目中实现某种并发性，我建议您使用像这样的高级框架，而不是从头开始，因为这将确保您的代码库较少受到运行时数据竞争类情况的影响，并将管理线程和同步的大部分繁重工作留给更具确定性的工作

## 目录:

1.  [Executors.java 工厂](#executorsfactory)
2.  [ExecutorService 接口](#executorservice)
3.  [线程池概念](#threadpool)

## 1。Executors.java 工厂

当我们使用 Executor Service 框架时，我们做的第一件事是使用 Executors 工厂类返回一个实现 ExecutorService 接口所有方法的对象的实例。我们调用构造函数上的公共静态 threadpool 实例创建方法来取回特定的包装 ExecutorService 实现。一旦我们获得了该服务的实例，我们就可以向该服务提交任务，并跟踪每个任务的进度。你可以在包`java.util.concurrent`下找到这个类。下面是实现我刚才讨论的一个示例片段:

```
ExecutorService executorService = Executors.newFixedThreadPool(10);

try{
    executorService.execute(new Runnable() {
        public void run() {
            System.out.println("Asynchronous task");
        }
    });
}

finally {
    executorService.shutdown(); 
} 
```

可从 Executors 类实例化的不同线程池实现有:

*   **固定线程池**:创建一个线程池，它重用固定数量的线程，这些线程在一个共享的无界队列上运行。在任何时候，最多`nThreads`会主动处理任务。在本系列的第 2 部分，我将深入解释双击这个实现

*   **单线程执行器**:创建一个单线程执行器，它可以调度命令在给定的延迟后运行，或者定期执行。(然而，注意，如果该单个线程由于在关闭之前的执行期间的故障而终止，则如果需要执行后续任务，新的线程将取代它的位置。)任务保证按顺序执行，并且在任何给定时间都不会有超过一个任务是活动的。保证返回的执行器不可重新配置以使用额外的线程。

*   **缓存线程池**:创建一个线程池，该线程池根据需要创建新的线程，但会在以前构建的线程可用时重用它们。这些池通常会提高执行许多短期异步任务的程序的性能。

## 2。执行者服务接口

ExecutorService 接口扩展了 Executor 接口，它是一个执行提交的`Runnable`任务的对象。该接口提供了一种将任务提交与每个任务将如何运行的机制相分离的方式，包括线程使用、调度等细节。通常使用一个`Executor`来代替显式创建线程。例如，与其为一组任务中的每一个调用`new Thread(new(RunnableTask())).start()`，不如使用。下面是实现此接口的具体类:

```
class ThreadPerTaskExecutor implements Executor {
   public void execute(Runnable r) {
     new Thread(r).start();
   }
} 
```

其调用片段的示例:

```
Executor executor = new ThreadPerTaskExecutor();
executor.execute(new RunnableTask1());
executor.execute(new RunnableTask2()); 
```

需要由实现类实现的 ExecutorService 接口的一些主要方法有:

*   `void shutdown();`:启动有序关机，执行之前提交的任务，但不接受新任务。如果已经关闭，调用没有任何附加效果。

*   `List<Runnable> shutdownNow();`:尝试停止所有正在执行的任务，暂停正在等待的任务的处理，并返回正在等待执行的任务列表。

*   `boolean isShutdown();`:如果该执行程序已经关闭，则返回 ***真*** 。

*   `boolean isTerminated();`:关机后如果所有任务完成，返回 ***真*** 。

*   `boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException;`:在 shutdown *请求后，阻塞直到所有任务执行完毕，或者超时发生，或者当前线程中断，无论哪种情况先发生。

*   `<T> Future<T> submit(Callable<T> task);`:提交一个返回值的任务执行，返回一个代表任务待定结果的 Future。未来的`get`方法将在成功完成后返回任务的结果。

*   `<T> Future<T> submit(Runnable task, T result);`:提交一个可运行的任务来执行，并返回一个代表该任务的 Future。未来的`get`方法将在成功完成后返回给定的结果。

*   `<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks) throws InterruptedException;`:执行给定的任务，当所有任务完成时，返回一个包含其状态和结果的期货列表。`Future#isDone`是返回列表中每个元素的`true`。注意， ***完成的*** 任务可以正常终止，也可以抛出异常终止。如果在此操作过程中修改了给定的集合，则此方法的结果是未定义的。

## 3。线程池执行器

一个`ExecutorService`的实现，它使用几个池化线程中的一个来执行每个提交的任务，通常使用`Executors`工厂方法来配置。线程池解决了两个不同的问题:由于减少了每个任务的调用开销，它们通常在执行大量异步任务时提供改进的性能，并且它们提供了一种绑定和管理执行一组任务时消耗的资源(包括线程)的方法。每个`ThreadPoolExecutor`还维护一些基本的统计数据，比如完成任务的数量。

`ThreadPookExecutor`扩展了再次实现`ExecutorService`接口的`AbstractExecutorService`。

ThreadPoolExecutor 的构造需要几个强制参数，以便用正确的策略以正确的方式进行初始化:

```
 public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue) {
        this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
             Executors.defaultThreadFactory(), defaultHandler);
    } 
```

您可能想知道`corePoolSize`、`maximumPoolSize`和‘工作队列’在这个上下文中做什么。`corePoolSize`告诉`ThreadPoolExecutor`初始化一组默认的线程，准备好接收下一个提交的任务。`workQueue`是一个 BlockingQueue 实现数据结构，用于对超过`corePoolSize`限制的请求进行排队。一旦到达`corePoolSize`，它就开始轮询/获取这个任务队列，然后调度一个现有线程来执行该任务。通过这种方式，线程池执行器可以有效地处理线程资源

### 关闭注释

这篇文章是我打算发表的 *3 部分系列*的*部分 1* 。这让我们很好地理解了 Executor 服务框架实现的组成，以及使该框架易于使用且有效的一些核心思想。

如果你有任何疑问或建设性的批评，请在下面的评论中写出来！
# 科特林的管道和过滤器

> 原文：<https://dev.to/viniciusccarvalho/pipes-and-filters-in-kotlin-ne3>

> 免责声明:协程对我来说仍然是一个新的话题，这篇文章中提出的许多实现思想并不是它的最佳用法。您已被警告:)

# 管道和过滤器

[![](img/13c78523870415be0d4ff35646943bff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sQoEHLfq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/DzG5JRs.png)

[管道和过滤器](https://www.enterpriseintegrationpatterns.com/patterns/messaging/PipesAndFilters.html)是一种非常常见的集成模式。整个框架，如令人敬畏的 [Spring Integration](https://spring.io/projects/spring-integration) 都是围绕它构建的。

这是一个简单的概念，管道连接端点就像你在 unix 命令`ps | grep`中使用的一样，这个模型允许端点相互独立。

如果你看看 Spring Integration 是如何做到这一点的，那就是通过使用[通道抽象](https://docs.spring.io/spring-integration/docs/5.1.2.RELEASE/reference/html/overview.html#overview-components-channel)

事实上，kotlin 协程也有一个用于跨协程通信的[通道](https://kotlinlang.org/docs/reference/coroutines/channels.html)实现。

所以我想知道我们是否可以使用它来创建一个非常简单的管道和过滤器流，而不需要利用任何其他外部依赖。

# 想法

*   写一些允许利用通道作为管道来连接协程函数的东西。

*   利用协程挂起特性进行并发执行，有时也与上下文并行:)

*   写一个简单的 DSL(ish)像`source via fn via fn2 into fn4`

试图复制一个完整集成框架的所有特性不在本文的讨论范围之内。例如，不支持通过出版商渠道输出。没有错误处理或 ack 支持。

# 如何

因此，在深入研究一些代码之前，我必须承认，到目前为止，我一直使用`GlobalScope.launch`作为启动我的协程的唯一方式。然后我看了下面的博文。如果今天我的博客有什么有用的东西，那就是给你的那些链接:

*   [显式并发](https://medium.com/@elizarov/explicit-concurrency-67a8e8fd9b25)
*   [协程上下文和范围](https://medium.com/@elizarov/coroutine-context-and-scope-c8b255d59055)
*   [避免使用全局上下文](https://medium.com/@elizarov/the-reason-to-avoid-globalscope-835337445abc)

我们从一个假设开始，即源将总是一个`Channel<T>`，这只是为了简化事情。

我们有两个管道[via || into]和两个顶级端点:`ChannelSink<T>, ChannelProcessor<T, R>`

让我们先看看我们的水槽

```
class ChannelSink<T>(private val channel: Channel<T>, block:  (T) -> Unit) : CoroutineScope {
    private val job: Job = Job()
    override val coroutineContext: CoroutineContext
        get() = Dispatchers.Default + job
    init {
        launch {
            for(message in channel){
                block(message)
            }
        }
    }
} 
```

这里没有发生太多的事情，我们的接收器从一个通道中读取消息(使用`launch` builder 以非阻塞的方式)。然后调用处理接收器的`block`函数(调用 rest 端点、调用数据库等)

我们的处理器是一个映射风格的函数`(T) -> (R)`

```
class ChannelProcessor<T, R>(private val inputChannel: Channel<T>, block: (T) -> (R)) : CoroutineScope {
    private val job = Job()
    override val coroutineContext: CoroutineContext
        get() = Dispatchers.Default + job
    val outputChannel = Channel<R>()
    init {
        launch {
            for(message in inputChannel){
                outputChannel.send(block(message))
            }
        }
    }
} 
```

非常类似于我们的接收器，但是现在我们创建了一个`outputChannel`，我们使用`block(message)`来编写转换后的消息

这就是我们需要的所有类型。现在我们需要一些管道代码围绕我们的管道[没有双关语]

## 管道代码(字面)

好了，我们的“DSL”应该使用通道作为管道来连接函数。所以一开始我们说我们希望我们的`sources`是`Channels`。所以让我们写两个可能的场景:

信源->处理器
信源- >信宿

```
//Source -> Sink
infix fun <T> Channel<T>.into(block:  (T) -> Unit) : ChannelSink<T> {
    return ChannelSink(this, block)
}
//Source -> Processor
infix fun <T, R> Channel<T>.via(block: (T) -> R) : ChannelProcessor<T, R> {
    return ChannelProcessor(this, block)
} 
```

我们的`via/into`函数将这里的函数接收器(Channel)传递给 ChannelSink/ChannelProcessor 的构造函数。

为了菊花链我们的处理器，现在我们需要两个额外的函数:

```
infix fun <T, R> ChannelProcessor<T, R>.into(block: (R) -> Unit) : ChannelSink<R> {
    return ChannelSink(this.outputChannel, block)
}
infix fun <T, R, S> ChannelProcessor<T, R>.via( block: (R) -> S) : ChannelProcessor<R, S> {
    return ChannelProcessor(this.outputChannel, block)
} 
```

请注意，我们通过获取处理器的 outputChannel 并将其作为下一个处理器或接收器的 inputChannel 进行传递来菊花链连接通道。

# 奔跑吧

首先让我们定义一些函数

```
//sink function
val fn: (Double) -> Unit = { i ->
   log("Received $i")
}
//first processor
val tn: (Int) -> (Int) = { i ->
       i * 2
}
//second processor
val tn2: (Int) -> Double = { i -> i * 3.0 } 
```

现在让我们准备好我们的制作人频道

```
val source = Channel<Int>()
launch {
    with(source) {
        for (i in 0..size) {
            send(i)
        }
        close()
    }
} 
```

最后连接管道:

```
source via tn via tn2 into fn 
```

差不多就是这样，我们现在可以使用管道和过滤器语法来使用我们的通道。

# 等等

[![](img/9b656ededf58d735b5c80a751bb1faea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NSoBwbFT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media2.giphy.com/media/uLMxqxVvVtuVO/giphy.gif%3Fcid%3D3640f6095c87f70e464e447236be533e)

Kotlin 支持集合流，为什么不创建一个集合或序列并使用`map`和`collect`函数呢？

看这段代码，你发现这一点是完全正确的，如果我让你看了所有这些，我真的很抱歉，但是我想说明一点:)

假设我们改变了我们的处理器和接收器代码:

```
class ChannelProcessor<T, R>(private val inputChannel: Channel<T>, block: (T) -> (R)) : CoroutineScope {
    private val job = Job()
    override val coroutineContext: CoroutineContext
        get() = Dispatchers.Default + job
    val outputChannel = Channel<R>()
    init {
        launch {
            for(message in inputChannel){
                launch {  outputChannel.send(block(message)) }
            }
        }
    }
}

class ChannelSink<T>(private val channel: Channel<T>, block:  (T) -> Unit) : CoroutineScope {
    private val job: Job = Job()
    override val coroutineContext: CoroutineContext
        get() = Dispatchers.Default + job
    init {
        launch {
            for(message in channel){
                launch {
                    block(message)
                }
            }
        }
    }
} 
```

注意，现在，我们为每个函数调用使用一个子协程，这使得我们可以并行运行所有步骤。

Kotlin 目前没有提供并行运行流的标准方法(除非您依赖 java 版本)。有了这最后一个更改，代码可以并发运行。*注意这意味着消息的排序受到影响*。

要测试这一点，只需使用这个新场景尝试更新后的代码:

```
@ExperimentalCoroutinesApi
    @Test
    fun testAsyncProduce() {
        val size = 100
        val latch = CountDownLatch(size)
        runBlocking<Unit> {

            val source = Channel<Int>()
            launch {
                with(source) {
                    for (i in 0..size) {
                        send(i)
                    }
                    close()
                }
            }
            val fn: (Double) -> Unit = { i ->
                log("Received $i")
                latch.countDown()
            }
            val tn: (Int) -> (Int) = { i ->
                runBlocking {
                    if (i % 2 == 0) {
                        delay(10)
                    }
                    i * 2
                }
            }
            val tn2: (Int) -> Double = { i -> i * 3.0 }

            source via tn via tn2 into fn
        }
        latch.await()
    } 
```

在这个测试中，我们的一个函数会导致偶数延迟，但不会导致执行顺序暂停。

这些只是我本周写代码时的一些想法，代码必须处理一个简单的管道和一个接收器来调用 REST 服务。将其中一些付诸实践，但不要太认真。

快乐编码
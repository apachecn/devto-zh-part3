# 理解 Kotlin 协程中的挂起功能

> 原文：<https://dev.to/onmyway133/understanding-suspend-function-in-kotlin-coroutine-2clb>

## 了解协程

从[https://kotlinlang.org/docs/reference/coroutines.html](https://kotlinlang.org/docs/reference/coroutines.html)

> 继续类比，await()可以是一个挂起函数(因此也可以从 async {}块中调用),它挂起一个协程，直到完成一些计算并返回其结果:

来自[https://kot linlang . org/docs/tutorials/coroutines-basic-JVM . html](https://kotlinlang.org/docs/tutorials/coroutines-basic-jvm.html)

> 我们使用的 delay()函数类似于 Thread.sleep()，但更好:它不阻塞线程，而只是挂起协程本身。当协程等待时，线程被返回到池中，当等待结束时，协程在池中的一个自由线程上恢复。
> 
> await()不能在协程之外调用，因为它需要挂起，直到计算完成，并且只有协程可以以非阻塞方式挂起

## kot Lin 协程中的 suspend 函数是什么意思🤔

[https://stack overflow . com/questions/47871868/what-do-suspend-function-mean-in-kot Lin-coroutine](https://stackoverflow.com/questions/47871868/what-does-suspend-function-mean-in-kotlin-coroutine)

## 了解异步

来自[https://github . com/kot Lin/kot linx . coroutines/blob/master/coroutines-guide . MD](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md)

> 从概念上讲，异步就像启动。它启动一个独立的协程，这是一个轻量级线程，与所有其他协程并行工作。不同之处在于，launch 返回一个作业，并且不携带任何结果值，而 async 返回一个 Deferred——一个轻量级的非阻塞未来，表示以后提供结果的承诺。你可以用。await()来获得它的最终结果，但是 deferred 也是一项工作，所以如果需要的话可以取消它。

```
fun main(args: Array<String>) = runBlocking<Unit> {
    val time = measureTimeMillis {
        val one = async { doSomethingUsefulOne() }
        val two = async { doSomethingUsefulTwo() }
        println("The answer is ${one.await() + two.await()}")
    }
    println("Completed in $time ms")
} 
```

> 异步有一个惰性选项，使用一个可选的 start 参数，其值为 CoroutineStart.LAZY。运行以下示例，该示例与上一个示例的不同之处仅在于此选项:

```
fun main(args: Array<String>) = runBlocking<Unit> {
    val time = measureTimeMillis {
        val one = async(start = CoroutineStart.LAZY) { doSomethingUsefulOne() }
        val two = async(start = CoroutineStart.LAZY) { doSomethingUsefulTwo() }
        println("The answer is ${one.await() + two.await()}")
    }
    println("Completed in $time ms")
} 
```

## kot Lin 协同例程中的启动/加入和异步/等待有什么区别

[https://stackoverflow.com/a/48079738/1418457](https://stackoverflow.com/a/48079738/1418457)

我觉得这个指南[https://github . com/kot Lin/kot linx . coroutines/blob/master/coroutines-guide . MD](https://github.com/Kotlin/kotlinx.coroutines/blob/master/coroutines-guide.md)很有用。我将引用重要的部分

🦄协同程序

本质上，协程是轻量级线程。

因此，您可以将协程视为以非常高效的方式管理线程的东西。

🐤启动

```
fun main(args: Array<String>) {
    launch { // launch new coroutine in background and continue
        delay(1000L) // non-blocking delay for 1 second (default time unit is ms)
        println("World!") // print after delay
    }
    println("Hello,") // main thread continues while coroutine is delayed
    Thread.sleep(2000L) // block main thread for 2 seconds to keep JVM alive
} 
```

所以 launch 启动一个后台线程，做一些事情，然后立即返回一个令牌作为 Job。您可以在这个任务上调用 join 来阻塞，直到这个启动线程完成

```
fun main(args: Array<String>) = runBlocking<Unit> {
    val job = launch { // launch new coroutine and keep a reference to its Job
        delay(1000L)
        println("World!")
    }
    println("Hello,")
    job.join() // wait until child coroutine completes
} 
```

🦆异步ˌ非同步(asynchronous)

从概念上讲，异步就像启动。它启动一个独立的协程，这是一个轻量级线程，与所有其他协程并行工作。不同之处在于，launch 返回一个作业，并且不携带任何结果值，而 async 返回一个 Deferred——一个轻量级的非阻塞未来，表示以后提供结果的承诺。

所以 async 启动一个后台线程，做一些事情，然后立即返回一个延迟的令牌。

```
fun main(args: Array<String>) = runBlocking<Unit> {
    val time = measureTimeMillis {
        val one = async { doSomethingUsefulOne() }
        val two = async { doSomethingUsefulTwo() }
        println("The answer is ${one.await() + two.await()}")
    }
    println("Completed in $time ms")
} 
```

你可以用。await()来获得它的最终结果，但是 deferred 也是一项工作，所以如果需要的话可以取消它。

所以延期其实是一种工作。参见[https://kot Lin . github . io/kot linx . coroutines/kot linx-coroutines-core/kot linx . coroutines . experimental/-deferred/index . html](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-deferred/index.html)

```
interface Deferred<out T> : Job (source) 
```

🦋默认情况下，异步是急切的

异步有一个惰性选项，使用一个可选的 Start 参数，其值为 CoroutineStart.LAZY。只有当某些 await 需要它的结果或者调用了 start 函数时，它才启动 coroutine。

原帖[https://github.com/onmyway133/blog/issues/123](https://github.com/onmyway133/blog/issues/123)
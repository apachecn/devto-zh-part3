# 如何使用 kotlin DSL 表达非功能性问题

> 原文：<https://dev.to/viniciusccarvalho/how-to-express-non-functional-concerns-using-kotlin-dsl-5219>

# Kotlin DSL 支持牛逼！

我最喜欢的 Kotlin 特性之一是该语言提供的强大的 DSL(ish)支持。

你可以编写非常有表现力的函数，看起来几乎像 DSL，这都是因为在 Kotlin 中，如果函数的最后一个参数是 lambda，你可以将它移到括号外的花括号中`{}`

这允许您编写看起来几乎像降价表示的代码，例如语言附带的`HTML`构建器:

```
html {
        head {
            title {+"XML encoding with Kotlin"}
        }
        body { }
} 
```

这段代码看起来像 markdown，但它是一段静态编译的代码。这不是很好吗？

kotlin 中我最喜欢的 DSL 用法之一是一个将 kubernetes-client(它使用了很多构建器)包装成友好的 DSL 的项目。来看看: [k8s-kotlin-dsl](https://github.com/fkorotkov/k8s-kotlin-dsl)

# 我是如何沉迷其中的

过了一段时间，我开始越来越多地使用 DSL。如果您使用协同例程，您可能会看到这个类似 DSL 的函数:

```
fun testAsyncFunction() = runBlocking {
//Some async code goes here
} 
```

另一个我很喜欢的方法是替换代码中所有的:`System.currentTimeInMillis()`:

```
val runningTime = measureTimeInMillis {
//invoke function here
} 
```

正如你所看到的，使用 DSL 可以使你的代码更容易阅读，人们可以很容易地推断出在`measureTimeInMillis`或`runBlocking`发生了什么

因此，我开始自己创作一些。

# 做一个好的 API 消费者

Kotlin 的另一个亮点是协同例程的并发性(以及异步的并行性)。

在我最喜欢的一个项目中，我抓取了一个具有速率限制策略的外部公共 API。没有限制，只要你每隔 2 秒打一次电话。

因为我创建了大量的协同例程(实际上更像 3k ),我希望它们并行运行，而不是在每个函数上写一个`delay(2000)`。我决定使用一个[令牌桶的实现——维基百科](https://en.wikipedia.org/wiki/Token_bucket) : [Github repo](https://github.com/bbeck/token-bucket) 。

所以我希望在一个`async`块中运行的所有函数都被一个给定的令牌桶*限制*。所以这个想法是任何函数看起来都应该是这样的:

```
fun request() : ApiResponse<T> = limited {

} 
```

这将允许任何阅读这段代码的人看到，这个调用受到某个因素的限制。

有限的功能很简单:

```
suspend fun <T> limited(bucket: TokenBucket = DEFAULT_LIMITER,  block: suspend () -> T) : T {
    bucket.consume()
    return block()
} 
```

block 参数是您在`limited { <block> }`之间包装的函数，它返回您的原始函数返回的任何类型。我们称之为高阶函数。

因为所有代码都应该在挂起状态下运行，所以函数和块都被标记为`suspend`。

现在我可以在这样的循环中运行这段代码:

```
val pages = getNumberOfPages()
for(p in 0..pages){

GlobalScope.launch {
val response request(p)
//process response
}

//return immediately to caller 
```

而且我敢肯定，飞行中的所有成千上万的工作都将受到每 2 秒钟只打一个电话的限制。

# 最后的想法

很容易理解为什么 kotlin 是如此受欢迎的语言。像这样的小特性让你的代码可读性更好。能够以一种清晰的方式来表达你的代码是非常简洁的。

快乐编码
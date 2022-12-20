# 我❤科特林，内联函数版

> 原文：<https://dev.to/aoemerson/i--kotlin-inline-function-edition-2f25>

*这篇文章也发表在我的个人网站上[andrewemerson . io](https://andrewemerson.io/post/kotlin-inline-fun-love/)T3】*

在过去的一年里，我一直在用 Kotlin 专门编写新的 Android 代码(并转换旧代码)。这是一种极具表现力、简洁而有趣的语言(请原谅`fun`双关语)。我现在不会去探究所有的原因，而且很多其他人之前已经写了[关于这个的](https://m.signalvnoise.com/how-i-fell-in-love-with-a-programming-language-8933d5e749ed)。

今天我想分享一个小片段，它展示了我最近在科特林写作时的一种温暖而模糊的感觉。

我一直在研究一项要求，即使用 OKHttp [`NetworkInterceptor`](https://github.com/square/okhttp/wiki/Interceptors) 在我们的应用程序中测量网络呼叫，并将统计数据记录到我们的分析平台。

显而易见的写法是这样的(代码被简化):

```
fun intercept() {
        // ...
        val start = SystemClock.elapsedRealtime()
        val result = doSomethingWeWantToMeasure()
        val duration = SystemClock.elapsedRealtime() - start
        log(duration)
        // ...
} 
```

这工作得很好，但是函数逻辑的*部分*被测量代码污染了，使得你的同事更难发现发生了什么。

### 输入`inline`带 lambda 的通用函数

下面是内联函数如何帮助这段代码:

```
fun intercept() {
        // ...
        val result = measure { doSomethingWeWantToMeasure() }
        // ...
}

inline fun <T> measure(action: () -> T) {
        val start = SystemClock.elapsedRealtime()
        val result = action()
        val duration = SystemClock.elapsedRealtime() - start
        log(duration)
        return result
} 
```

好些了，不是吗？现在我可以专注于阅读`intercept()`函数的主要意图，而不用跳过测量代码行。我们还受益于在其他需要分析性能的地方重用代码的选项。

### 什么是`inline`？

我对此还很陌生，所以专家可能不会 100%同意，但我的简单解释是:

> `inline`允许你在闭包(`{ ... }`)内调用带有 lambda 参数的函数，而不是像`measure(myLamda)`那样传递 lambda。

你可以在这里阅读更长、更专业(也可能更准确)的解释[。](http://kotlinlang.org/docs/reference/inline-functions.html)

与此同时，快乐的 Kotlin-ing，并随时提问或在评论中添加您最喜欢的 kot Lin*warm and fuzzies*。
# Kotlin 协同例程和 JavaFX 线程

> 原文：<https://dev.to/domnikl/kotlin-coroutines-and-javafx-threads-26od>

2018 年 10 月 29 日，JetBrains 发布了[kot Lin(1.3)](https://blog.jetbrains.com/kotlin/2018/10/kotlin-1-3/)的新版本，其中包括该语言期待已久的功能:协程。与 Go 的`goroutine`构造相反，它们没有作为语言特性来实现。相反，它们是作为用 Kotlin 编写的纯库实现来开发的。但除此之外，它们与 goroutines 有许多相似之处。

在幕后，协程是非常轻量级的，因为它们被复用在几个线程上，创建一个协程比生成一个新线程要快得多。

你可以在 JetBrains 的教程中了解更多信息:[“你和 Kotlin 的第一次协同工作”](https://kotlinlang.org/docs/tutorials/coroutines/coroutines-basic-jvm.html)。在这里，我想向您展示在 JavaFX 应用程序中使用协程时可能会遇到的警告。

这是我用 Kotlin 编写的基本 JavaFX 应用程序:

```
class MyApp : Application() {
    private val button = Label().also { it.text = "waiting for 0s" }

    override fun start(primaryStage: Stage?) {
        val pane = Pane().also {
            it.children.add(button)
        }

        primaryStage?.scene = Scene(pane,200.0,200.0)
        primaryStage?.show()
    }
}

fun main() {
    launch(MyApp::class.java)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里没有什么特别的，只是一个带有显示文本`waiting for 0s`的标签的 GUI。现在，我们想扩展我们的应用程序，以不断更新标签，始终告诉我等待的确切时间。

```
class MillisElapsedCounter(private val observer: Observer) {
    fun start() {
        GlobalScope.launch(Dispatchers.Default) {
            var millisElapsed = 0

            while (true) {
                delay(1000)
                millisElapsed += 1000
                observer.notify(millisElapsed)
            }
        }
    }

    interface Observer {
        fun notify(millisElapsed: Int)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**注意，您需要向`org.jetbrains.kotlinx:kotlinx-coroutines-core:1.1.1`添加一个依赖项才能运行它(记住:它只是一个库！)**

所以这是`MillisElapsedCounter`类的实现和相应的每 1000 毫秒通知一次的`Observer`。这个类的客户端必须提供一个观察者来实例化它。调用`start()`将使用默认的调度程序启动一个新的协程程序(有一个调度程序池——这是幕后的线程——您可以使用)并立即返回。注意`delay(1000)`是一个`suspend`函数，它将在运行它的线程中暂停执行。在应用程序线程中运行它会永远冻结用户界面，这是个坏主意。

为了使用计数器，我们还需要更新`MyApp`来实现`MillisElapsedCounter.Observer`的`notify(millisElapsed: Int)`，并像这样启动计数器:

```
class MyApp : Application(), MillisElapsedCounter.Observer {
    private val button = Label().also { it.text = "waiting for 0s" }

    override fun start(primaryStage: Stage?) {
        MillisElapsedCounter(this).start()
        ...
    }

    override fun notify(millisElapsed: Int) {
        button.text = "waiting for ${millisElapsed / 1000}s"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在如果你运行它，它将抛出一个来自 JavaFX 的`java.lang.IllegalStateException`异常:

> 线程“default dispatcher-worker-3”Java . lang . illegalstateexception 中的异常:不在 FX 应用程序线程上；current thread = default dispatcher-worker-3

为了防止 UI 状态中的并发问题，JavaFX 防止其他线程更改它。看看使用`launch(MyApp::class.java)`启动应用程序的`main()`函数。这启动了一个名为`JavaFX-Launcher`的新线程，它又启动了运行所有 UI 相关内容的`JavaFX Application Thread`。这是唯一一个允许在运行时修改 UI 的线程。

这意味着我们需要在这个线程中执行对`button.text`的修改。Kotlin 团队为此实现了另一个库:**org . jetbrains . kot linx:kot linx-coroutines-javafx:1 . 1 . 1**提供了一个使用 Java FX 应用线程的新调度器`Dispatchers.JavaFx`(也有相应使用的 **-android** 和 **-swing** )。

那么我们这里问题的解决方案是使用正确的调度程序`Dispatchers.JavaFx`并在`MyApp`中实现另一个名为`CoroutineScope` :
的接口

```
class MyApp : Application(), MillisElapsedCounter.Observer, CoroutineScope {
    override val coroutineContext: CoroutineContext
        get() = Dispatchers.JavaFx
        ... 
```

Enter fullscreen mode Exit fullscreen mode

由于现在是一个`CoroutineScope`，我们可以使用正确的作用域
直接从`notify()`启动新的协程

```
override fun notify(millisElapsed: Int) {
    launch {
        button.text = "waiting for ${millisElapsed / 1000}s"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！异常将不再发生，一切将按预期运行——我等待了很长时间，它仍然愉快地计算着我等待的秒数:)

不过我们可以改进的一点是，`MillisElapsedCounter`在`GlobalScope`中运行一个协程，这意味着您需要跟踪它的生命周期(如果您对此感兴趣的话——例如停止它)。更多详情请见[避开 GlobalScope](https://medium.com/@elizarov/the-reason-to-avoid-globalscope-835337445abc) 的原因。为了解决这个问题，我们也可以在`MillisElapsedCounter`:
中实现接口`CoroutineScope`

```
class MillisElapsedCounter(private val observer: Observer) : CoroutineScope {
    override val coroutineContext: CoroutineContext
        get() = Dispatchers.Default

    fun start() {
        launch { ... }
    }
    ... 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有两个相互完全分离的协程，每个协程都在自己的范围内运行。我认为这是一个干净简单的解决方案。

GitHub 上有完整的源代码，所以您可以克隆它并使用它:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [多姆尼克](https://github.com/domnikl) / [科特林协同程序和 javafx 线程](https://github.com/domnikl/kotlin-coroutines-and-javafx-threads)

### 来自我的博客帖子的示例代码

<article class="markdown-body entry-content container-lg" itemprop="text">

# Kotlin 协同例程和 JavaFX 线程

这是我在标题为的[博客中的例子。](https://domnikl.github.io/2019/02/kotlin-coroutines-and-javafx-threads/)

*   [第 0 步](https://github.com/domnikl/kotlin-coroutines-and-javafx-threads/blob/step0/src/main/kotlin/Main.kt)
*   [第一步](https://github.com/domnikl/kotlin-coroutines-and-javafx-threads/blob/step1/src/main/kotlin/Main.kt)
*   [第二步](https://github.com/domnikl/kotlin-coroutines-and-javafx-threads/blob/step2/src/main/kotlin/Main.kt)
*   [第三步](https://github.com/domnikl/kotlin-coroutines-and-javafx-threads/blob/step3/src/main/kotlin/Main.kt)

</article>

[View on GitHub](https://github.com/domnikl/kotlin-coroutines-and-javafx-threads)

你可以在[用协程程序进行 UI 编程指南](https://github.com/Kotlin/kotlinx.coroutines/blob/master/ui/coroutines-guide-ui.md#launch-ui-coroutine)中读到更多。
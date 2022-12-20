# 使用构造函数注入对 RxJava 或协程代码进行单元测试

> 原文：<https://dev.to/adammc331/unit-testing-rxjava-or-coroutine-code-with-constructor-injection-3b1c>

原载于[安卓精华](https://androidessence.com/unit-testing-async-code)。

抛开目前关于 Android 上的异步代码应该使用 RxJava 还是 coroutines 的长期争论，两个阵营经常遇到相同的问题。我如何为此编写单元测试呢？

单元测试异步代码是棘手的，因为我们可能需要知道如何正确测试回调 API，或者我们可能只是希望事情立即运行，而不担心线程变化。我们可能还想知道如何处理 junit 测试中没有“主”线程的情况，这与连接测试不同。这篇文章将集中讨论最后一个问题。

# 问题

我们先从分析问题开始。假设我有下面的代码从一个 API 中获取口袋妖怪:

```
open class PokemonRepository(
    private val api: PokemonAPI,
    private val disposables: CompositeDisposable
) {
    fun fetchPokemon() {
        val subscription = api.getPokemon()
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe {
                // Do something with the response
            }

        disposables.add(subscription)
    }
} 
```

现在，如果我们从 junit 测试中调用这个方法，我们将得到一个`ExceptionInInitializerError`，因为我们不能模拟`AndroidSchedulers.mainThread()`调度程序。解决这个问题的方法是使用 [Schedulers.trampoline()](http://reactivex.io/RxJava/javadoc/io/reactivex/schedulers/Schedulers.html#trampoline--) ，你可以把它想象成一个即时调度器。除此之外还有更多，您可以在文档中了解，但是它解决了我们的用例。

# 解

不幸的是，有了上面的存储库代码，我们不能轻易地改变我们要使用的单元测试。蹦床()。所以我们需要将调度程序移到我们可以修改的地方，比如存储库的构造器:

```
open class PokemonRepository(
    private val api: PokemonAPI,
    private val disposables: CompositeDisposable,
    private val processScheduler: Scheduler = Schedulers.io(),
    private val observerScheduler: Scheduler = AndroidSchedulers.mainThread()
) {
    fun fetchPokemon() {
        val subscription = api.getPokemon()
            .subscribeOn(processScheduler)
            .observeOn(observerScheduler)
            .subscribe {
                // Do something with the response
            }

        disposables.add(subscription)
    }
} 
```

在 Kotlin 默认参数的奇妙帮助下，我们可以提供应用程序使用的默认值，这意味着在调用站点，我们只需像以前一样提供两个参数。但是现在，我们可以改变单元测试中使用的调度器:

```
class PokemonRepositoryTest {
    private val mockAPI = mock(PokemonAPI::class.java)
    private val repository = PokemonRepository(
        mockAPI,
        CompositeDisposable(),
        Schedulers.trampoline(),
        Schedulers.trampoline()
    )

    // ...
} 
```

就是这样！我们对 RxJava 代码进行单元测试所要做的就是将我们的调度程序移入构造函数中。如果您已经从 RxJava 迁移到协程，或者正在考虑迁移，那么解决方案是非常相似的。

# 协程版本

让我们假设我们写了一些这样的协程代码:

```
class MainActivityViewModel(
    repository: PokemonRepository,
) : BaseObservableViewModel() {
    // ...

    private var job: Job? = null

    // ...

    init {
        job = CoroutineScope(Dispatchers.IO).launch {
            withContext(Dispatchers.Main) {
                startLoading()
            }

            val newState = try {
                val response = repository.getPokemon()
                // Handle success
            } catch (error: Throwable) {
                // Handle error
            }

            withContext(Dispatchers.Main) {
                // Post the new state to the UI
            }
        }
    }

    // ..

    override fun onCleared() {
        super.onCleared()
        job?.cancel()
    }
} 
```

如果我们这样做，我们会遇到与我们最初的 RxJava 代码相同的问题*。`Dispatchers.Main`只在 Android 上运行时配置，不在 JUnit 内部配置。此外，我们不需要*IO 调度程序，我们可以使用`Dispatchers.Unconfined`进行 junit 测试。类似于 RxJava，我们可以把它们都作为构造函数参数，但是我决定创建一个数据类，允许我覆盖任何调度程序:

```
data class DispatcherProvider(
    val IO: CoroutineDispatcher = Dispatchers.IO,
    val Main: CoroutineDispatcher = Dispatchers.Main,
    val Unconfined: CoroutineDispatcher = Dispatchers.Unconfined
)

class MainActivityViewModel(
    repository: PokemonRepository,
    dispatcherProvider: DispatcherProvider = DispatcherProvider()
) : BaseObservableViewModel() {
    // ...

    init {
        job = CoroutineScope(dispatcherProvider.IO).launch {
            withContext(dispatcherProvider.Main) {
                startLoading()
            }

            // ...

            withContext(dispatcherProvider.Main) {
                // Post the new state to the UI
            }
        }
    }

    // ...
} 
```

然后，为了在我们的 junit 测试中调用它，我们可以覆盖我们想要的调度程序:

```
private val testProvider = DispatcherProvider(
    IO = Dispatchers.Unconfined, 
    Main = Dispatchers.Unconfined
) 
```

就是这样！现在我们不必担心 junit 测试中主循环没有定义。

我保持这种简单性是为了强调我们如何修改单元测试中使用的调度程序/调度程序。如需完整的代码示例，请查看下面的参考资料，或者如果您有任何问题，请在评论中或在 Twitter 上告诉我。

# 资源

我最近刚刚将本文中的项目改为使用协程。然而，如果您想了解如何使用 RxJava 代码并进行单元测试，我确实用 rx Java 代码对最后一次提交进行了标记。

要了解如何在项目中使用协程并对它们进行单元测试，您可以查看 [this project](https://github.com/AdamMc331/PokeDex) 。
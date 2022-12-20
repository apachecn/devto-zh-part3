# 如何用 kotlin 和 Rx 实现一个 Redux 库

> 原文：<https://dev.to/apium_hub/how-to-implement-a-redux-library-with-kotlin-and-rx-1ld9>

在本文中，我们将介绍如何用 Kotlin 和 Rx 实现一个 Redux 库。但首先，让我们先从简单介绍一下 [Redux 库](https://dev.to/apium_hub/demystifying-redux-3p47) :

本质上，Redux 是一个有三大支柱的架构:

1.  您的应用程序只有一个真实的来源，称为状态。
2.  不变性，这种状态不能被修改，只能被另一种状态取代。
3.  纯度，一个没有副作用的单一功能(称为 reducer)，从动作中产生这些新状态。

## 如何用 kotlin 和 Rx 实现一个 Redux 库

通常状态被封装在通常被称为“存储”的东西中，当创建存储时，我们将指定什么是应用程序的初始状态，什么是归约函数。

从那时起，我们可以订阅状态的更改，并发送操作来修改应用程序的状态。

计数器的小例子:

本文的例子是一个简单的计数器，但是这个架构是完全可伸缩的。

将有两个动作:

```
sealed class Action {
    object Increment : Action()
    object Decrement : Action()
    class Set(val value: Int) : Action()
} 
```

由于 kotlin 的密封类，这将允许我们有一个详尽的开关情况，另外，行动可能有不同的参数。

reduce 函数是:

```
fun reducer(state: Int, action: Action) : Int = when(action) {
    is Action.Increment -> state+1
    is Action.Decrement -> state-1
    is Action.Set -> action.value
} 
```

用 Rx 的基本实现:

如果我们 loot 一下 Rx 中可用的函数，我们会很快意识到有一个函数有一个 seed 和一个 reduce 函数作为参数。这样看来，我们已经把所有的工作都做了:

我们创建一个主题来传递动作:

```
val actions: Subject = PublishSubject.create() 
```

我们应用 reduce 函数使我们的状态:

```
val states = actions.reduce(0, ::reducer) 
```

就这样，现在要执行一个动作，我们只需将它传递给主题:

```
actions.onNext(Action.Increment) 
```

问题是 Rx reduce 方法返回一个 Single，而不是一个 Observable，所以只有当我们告诉动作我们已经完成时，我们才会有最终状态。

```
actions.onComplete() 
```

但是我们不希望这样，我们希望的是我们的应用程序在所有动作执行的时候做出反应，而不是在我们结束的时候。

[![GSAS](img/2dbe1733eacd9e3c7573ae14c6e4cb61.png)](https://gsas.io/)

创建商店:

如果我们真的将这个逻辑封装在一个始终保持当前状态的类中，这个问题的解决方案就变得显而易见了:

```
class Store<S, A>(initialState: S, private val reducer: (S, A) -> S){

    var currentState = initialState
        private set

    val actions = PublishSubject.create()

    val states= actions.map {
        currentState = reducer(currentState, it)
        currentState
    }
} 
```

此外，当我们执行这个步骤时，我们获得了一个新的功能，我们可以检查特定时刻的状态，而不必订阅所有的状态更改。

但是如果我们执行下面的测试，我们会发现我们还没有完成:

```
store.states.subscribe { println("state $it") }
    store.actions.onNext(Action.Increment)
    store.actions.onNext(Action.Increment)
    store.states.subscribe { println("sorry I am late $it") }
    store.actions.onNext(Action.Increment)
    println("final state: ${store.currentState}") 
```

这段代码的输出不是我们所期望的，我们只做了三个增量动作，但是当添加一个新的订阅时，输出如下:

状态 1

状态 2

状态 3

对不起，我迟到了 4

最终状态:4

每当一个新动作到达时，它就被可观察对象中的每个订阅者执行。这也意味着，如果没人看，我们的行动就会失败。

幸好这个问题可以用一行简短的代码解决:

```
val states: Observable = actions.map {
        currentState = reducer(currentState, it)
        currentState
    }.publish().apply { disposable = connect() } 
```

使用 publish()我们创建了一个可连接的观察对象，它正在等待一个连接开始发布状态。
同“T1”。apply {disposable = connect ()} '我们一创建它就连接，这样，即使我们没有任何订户，所有的操作都将被执行。我们还保留了这种连接的一次性，以防必须为商店实现某种生命周期。

太纯洁了:

有了一个好的 reducer 函数，没有任何副作用并且总是返回有效状态，我们仍然有一个问题:

因为用户操作而修改状态很简单，但是当我们想要维护一个持久性、创建日志或从后端收集数据时会发生什么呢？我们的 reducer 函数不能处理这些动作，尤其是当它们是异步的时候。为此，我们必须在混合中引入一个新概念，这将有助于我们产生副作用，保持减压器功能作为一个纯功能，并能够控制执行哪些副作用。

中间件:中间件是一个软件组件，它允许我们在不破坏架构的情况下引入期望的副作用。

```
interface Middleware<S, A> {
    fun dispatch(store: Store<S, A>, next: (A) -> Unit, action: A)
} 
```

中间件由一个函数组成，该函数接收安装它的存储库作为参数，一个函数继续执行流程和要执行的动作。通过这种方式，中间件拥有了它可能需要的所有信息以及继续执行的机制。

下面的例子是一个不执行任何操作的中间件:

```
val doNothing = object : Middleware<Int, Action> {
    override fun dispatch(store: Store<Int, Action>, next: (Action) -> Unit, action: Action) {
        next(action)
    }
} 
```

这个在执行一个动作之前和之后在屏幕上打印当前状态:

```
val printLogger = object : Middleware<Int, Action> {
    override fun dispatch(store: Store<Int, Action>, next: (Action) -> Unit, action: Action) {
        println("State is ${store.currentState} before action: $action")
        next(action)
        println("State is ${store.currentState} after action: $action")
    }
} 
```

由于是中间件本身决定在代码中的哪一点执行动作，所以它们可以灵活地执行所有类型的任务，包括异步调用。能够捕获指示必须发送请求的动作，并在数据到达时发送新的动作。

```
fun Middleware<S, A>.decompose() =
        { s: Store<S, A> ->
            { next: (A) -> Unit ->
                { a: A ->
                    dispatch(s, next, a)
                }
            }
        } 
```

这个函数分解中间件，这样我们可以首先传递存储，然后传递延续函数并完成动作。

现在，我们将定义一个允许我们组成中间件的函数:

```
private fun Middleware<S, A>.combine(other: Middleware<S, A>) = object : Middleware<S, A> {
        override fun dispatch(store: Store<S, A>, next: (A) -> Unit, action: A) =
            this@combine.dispatch(store, other.decompose()(store)(next), action)
    } 
```

感谢我们已经能够分解第二个中间件的事实，我们可以只传递两个参数，并将结果函数作为下一个传递给另一个。

```
private inline fun <R, T : R> Array.reduceOrNull(operation: (acc: R, T) -> R) =
        if (this.isEmpty()) null else this.reduce(operation) 

private fun Array>.combineAll(): Middleware<S, A>? =
            this.reduceOrNull { acc, middleware -> acc.combine(middleware)} 
```

有了这两个函数，我们现在可以很容易地减少整个中间件阵列。有了这个我们就可以做最后一个来完成店铺。

```
private val actions = PublishSubject.create()
    fun dispatch(action: A) = middlewares.combineAll()?.dispatch(this, actions::onNext, action) ?: actions.onNext(action) 
```

我们没有直接公开动作，而是添加了一个函数来组合所有的中间件并使用结果，或者如果没有中间件，它会将结果直接发送给动作的主体。

最终结果:

我们已经设法编写了一个大约 40 行代码的库，它实现了 redux 库的最重要的功能，并且内置了对 Rx 的开箱即用支持:

```
interface Middleware<S, A> {
    fun dispatch(store: Store<S, A>, next: (A) -> Unit, action: A)

    fun combine(other: Middleware<S, A>) : Middleware<S,A> = object : Middleware<S, A> {
        override fun dispatch(store: Store<S, A>, next: (A) -> kotlin.Unit, action: A) =
            this@Middleware.dispatch(store, other.decompose()(store)(next), action)
    }

     fun decompose() =
        { s: Store<S, A> ->
            { next: (A) -> Unit ->
                { a: A ->
                    dispatch(s, next, a)
                }
            }
        }
}

inline fun <R, T : R> Array.reduceOrNull(operation: (acc: R, T) -> R) = if (this.isEmpty()) null else this.reduce(operation)

fun <S,A> Array>.combineAll() =
    this.reduceOrNull { acc, middleware -> acc.combine(middleware) }

class Store<S, A>(initialState: S, private val reducer: (S, A) -> S, private vararg val middlewares: Middleware<S, A>) {

    var currentState = initialState
        private set

    private val disposable: Disposable

    private val actions = PublishSubject.create()

    val states: Observable = actions.map {
        currentState = reducer(currentState, it)
        currentState
    }.publish().apply { disposable = connect() }

    fun dispatch(action: A) =
        middlewares.combineAll()?.dispatch(this, actions::onNext, action) ?: actions.onNext(action)
} 
```

如果你对 Redux Library 感兴趣，我强烈推荐你订阅我们的[月刊](http://eepurl.com/cC96MY)。

帖子[如何用 kotlin 和 Rx](https://apiumhub.com/tech-blog-barcelona/how-to-implement-a-redux-library-with-kotlin-and-rx/) 实现 Redux 库首先出现在 [Apiumhub](https://apiumhub.com) 上。
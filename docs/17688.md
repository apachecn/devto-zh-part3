# 带箭的科特林实用刽子手(下)

> 原文：<https://dev.to/s_anastasov/functional-hangman-in-kotlin-with-arrow-part-2-3knl>

将一个[功能的刽子手](https://dev.to/2018/functional-hangman-in-kotlin-with-arrow)游戏从 Scala (ZIO)转换成 Kotlin(带箭头)是一个很好的练习。我喜欢从事这项工作，我学到了很多东西。当我在 [#arrow](https://kotlinlang.slack.com/messages/C5UPMM0A0/) 频道上寻求反馈时，维护者之一 [Leandro](https://twitter.com/mLeandroBF) 提出了一个有趣的建议。与其硬编码数据类型`IO`,我应该试着让程序多态并使用`Kind`。这意味着编写专注于领域逻辑的代码，使用抽象，并推迟对具体数据类型的决定，如`IO`或`Single`(来自 RxJava)，直到主函数。

# 旅途

我不熟悉这种编程风格，所以我使用优秀 Arrow 文档中的这个[示例](https://arrow-kt.io/docs/patterns/polymorphic_programs/)作为指南。

## 写入控制台

在上一篇文章中，我使用了`IO<A>`来与控制台交互。`IO<A>`代表一个操作，它可以被延迟执行、因异常而失败(异常在`IO`中被捕获)、永远运行或者返回一个单独的`A`。让我们来看看最初的实现:

```
fun putStrLn(line: String): IO<Unit> = IO { println(line) }

// Usage in main()
putStrLn("Hello world!").unsafeRunSync() 
```

`putStrLn`是一个接受一个`String`并返回一个`IO<Unit>`函数。当我们调用`unsafeRunSync()`时，`IO`取一个 lambda，这个 lambda 在世界的尽头被延迟求值。如果我们想用`Single`达到同样的效果，我们可以使用`Single.fromCallable`包装我们的 lambda，并在调用`subscribe()`时在主函数中对其求值。

```
fun putStrLn(line: String): Single<Unit> = Single.fromCallable {
    println(line)
}

// Usage in main()
putStrLn("Hello World").subscribe() 
```

这里`IO`和`Single`都有共同点。一组**功能**，如:**懒惰评估**、**异常处理**，以及**永远运行**或**完成一个`A`类型的结果**。`IO`和`Single`做得更多，但是对于这个用例，我们想要一些尽可能简单但功能相同的东西。Arrow 中有一个类型类可以做到这一点，它叫做`MonadDefer`。经过几次迭代，以及 Arrow 团队的反馈，这是我想出的用于打印到控制台的代码。

```
fun <F> putStrLn(M: MonadDefer<F>, line: String): Kind<F, Unit> = M.invoke {
    println(line)
} 
```

`putStrLn`函数是类型`F`的泛型，但不是任何`F`。这个`F`，不管是什么，都需要做某些事情，比如**懒惰评估**和**错误处理**。这个`F`的东西需要`MonadDefer`的**能力**。返回值也需要一个类型参数，在 Arrow 中我们可以通过返回`Kind<F, SOMETHING>`来实现。在打印到命令行的情况下，那个`SOMETHING`就是`Unit`(没有返回值)。`MonadDefer`带有一个`invoke`函数，我们可以用它来构造`Kind<F, Unit>`的值。我们在里面传递一个 lambda，它将被延迟计算。

与最初的实施相比，我们有几个关键的不同之处:

*   一个类型参数`F`
*   还有一个类型为`MonadDefer<F>`的参数
*   返回类型是`Kind<F, Unit>`而不是`IO<Unit>`

现在我们可以使用这个函数将一些内容打印到控制台。为了做到这一点，我们需要一个具有`MonadDefer`的**功能**的数据类型。`IO`可以这样做，所以我们可以使用它。Arrow 还附带了`SingleK`，一个具有`MonadDefer`功能的`Single`的包装器。

```
putStrLn(IO.monadDefer(), "Hello World!").fix()
        .unsafeRunSync()

putStrLn(SingleK.monadDefer(), "Hello World!").fix()
        .single.subscribe() 
```

Arrow 还附带了代表`Observable`的`ObservableK`，代表协程的`DeferredK`等。

## 从控制台读取

从控制台读取类似于向控制台写入。我们仍然需要一个类型参数`F`，我们仍然需要返回`Kind<F, SOMETHING>`，我们需要延迟执行操作，无论成功与否。`readLine()`返回一个可空的`String?`，如果发生这种情况，我们需要发出错误信号。

```
// Original code
fun getStrLn(): IO<String> = IO { readLine() ?: throw IOException("Failed to read input!") }

// Polymorphic code
fun <F> getStrLn(M: MonadDefer<F>): Kind<F, String> = M.invoke {
    readLine() ?: throw IOException("Failed to read input!")
} 
```

## 从控制台#2 读取

我抛出一个`IOException`表示失败。会包装这个异常，这样就没那么糟糕了。但是有一个更好的方法(感谢[莱安德罗](https://twitter.com/mLeandroBF))。

我将可空的`String?`转换成一个`Option<String>`。然后我使用`fold`来检查`Option`是否有值。如果它是空的，我使用`raiseError`来创建`MonadDefer`，当它被求值时返回一个错误。如果它不为空，我创建一个`MonadDefer`，使用`just`返回一个`String`。

这里的关键区别是我没有抛出`IOException`。使用异常可能[昂贵](http://java-performance.info/throwing-an-exception-in-java-is-very-slow/)。

`M.defer`这里指的是`readLine()`懒洋洋地发生。

```
fun <F> getStrLn(M: MonadDefer<F>): Kind<F, String> = M.defer {
    readLine().toOption()
            .fold(
                    { M.raiseError<String>(IOException("Failed to read input!")) },
                    { M.just(it) }
            )
} 
```

## 刽子手类

下一步是使`Hangman`类多态。为此，我添加了一个类型参数`F`和类型属性`MonadDefer<F>`。

```
class Hangman<F>(private val M: MonadDefer<F>) {
    ...
} 
```

## 选择一个字母

为了让`getChoice()`函数以多态的方式工作，我们需要做一些改动。返回类型从`IO<Char>`变为`Kind<F, Char>`。`IO.binding`变成了`M.binding`(其中`M`是类型`MonadDefer<F>`的属性)。

`getStrLn()`和`putStrLn()`也需要`M`作为参数。

```
fun getChoice(): Kind<F, Char> = M.binding {
        putStrLn(M, "Please enter a letter").bind()
        val line = getStrLn(M).bind()
        val char = line.toLowerCase().first().toOption().fold(
                {
                        putStrLn(M, "Please enter a letter")
                                .flatMap { getChoice() }
                },
                { char ->
                        M { char }
                }
        ).bind()
        char
} 
```

## 包装完毕

更新所有其他函数遵循相同的模式。将`IO<SOMETHING>`替换为`Kind<F, SOMETHING>`，将`IO.binding`替换为`M.binding`，并将`M`作为参数传入控制台进行读写。

你可以在这里找到完整的代码。

## 主程序

用`Single`运行的主程序看起来是这样的:

```
Hangman(SingleK.monadDefer()).hangman.fix().single.subscribe() 
```

或者用`IO`

```
Hangman(IO.monadDefer()).hangman.fix().unsafeRunSync() 
```

运行哪种类型构造函数的决定是在执行时做出的。从`Single`切换到`IO`或`Observable`只需要更新主功能。

## 增量改进

我还在学习 FP，我不知道 Arrow 中的大多数类型类，也不知道它们能做什么。在我的第一次尝试中，我用了`Async`而不是`MonadDefer`。`Async`扩展`MonadDefer`并增加额外的能力。我在 [#arrow](https://kotlinlang.slack.com/messages/C5UPMM0A0/) 频道寻求反馈，他们把我引向了`MonadDefer`。和 Arrow 维护人员一起，我们做了一些改进。

为了避免每次都将`M`传递给`printStrLn`,我可以将它转换成`MonadDefer`T3 上的扩展函数

```
fun <F> MonadDefer<F>.putStrLn(line: String): Kind<F, Unit> = invoke {
    println(line)
} 
```

我可以通过委托使用[实现](https://kotlinlang.org/docs/reference/delegation.html#implementation-by-delegation)，并让`Hangman`类实现`MonadDefer<F>`。

```
class Hangman<F>(private val M: MonadDefer<F>): MonadDefer<F> by M 
```

这意味着在`Hangman`类 I 中的任何地方都可以使用`MonadDefer`的方法，如`binding`和`invoke`，以及扩展方法，如`putStrLn`。

```
val hangman: Kind<F, Unit> = binding {
        putStrLn("Welcome to purely functional hangman").bind()
        val name = getName.bind()
        putStrLn("Welcome $name. Let's begin!").bind()
        val word = chooseWord.bind()
        val state = State(name, word = word)
        renderState(state).bind()
        gameLoop(state).bind()
        Unit
    } 
```

你可以在这里找到完整的实现。

# 结论

使用像`MonadDefer`这样的抽象将业务逻辑从像`IO`或`Single`这样的实现细节中解放出来。它还可以使不同模块的组合更容易，因为具体数据类型的决定被延迟到主程序。
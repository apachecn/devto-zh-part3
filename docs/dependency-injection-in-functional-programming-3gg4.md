# 函数编程中的依赖注入

> 原文：<https://dev.to/psfeng/dependency-injection-in-functional-programming-3gg4>

# 函数式编程中的依赖注入(Kotlin)

## TL；博士；医生

*   FP 中的 DI 可以使用部分函数应用程序或阅读器 monad 来实现
*   但是 DI 在 FP 中可能没有意义
*   对于有很多副作用的应用程序来说，一个 OO-FP-OO 三明治可能是一个很好的方法，其中域核心是纯功能性的，而外层是 OO 并使用依赖注入器。
*   所有解决方案都有利有弊，最终选择取决于手头的任务和个人偏好

当我开始研究函数式编程时，我一直想知道 DI 在这种范式中是如何工作的，并惊讶地发现，关于它的文章并不多，尤其是对初学者来说，所以我决定通过分享我所学到的东西来帮助填补这一空白。因此，本文的目标是那些已经开始研究函数范式并想知道依赖注入在其中如何工作的开发人员，所以我将尝试介绍每个主题，而不需要假设太多的先验知识。我使用的语言是科特林语，尽管这些原则是通用的。这篇文章最好从上到下阅读，因为概念的解释遵循逻辑顺序。开始吧！

## 为什么要函数式编程？

像许多开发人员一样，在过去的几年里，我开始对函数式编程产生了浓厚的兴趣，因为我在实践中看到了很多关于可变状态的问题。作为一名过去 5 年的 Android 开发人员，随着 MVP 架构的广泛采用，我已经尝试了这些问题。当时，它风靡一时，因为它允许我们远离不可测试的上帝活动/片段，并将呈现逻辑委托给可测试的呈现者。随着新功能的实现和需求的改变，我们的演示者将开始看起来像旧的巨大活动/片段，并且做出任何改变将变得越来越危险，因为演示者以命令式(例如`view.showLoading()`)修改视图，并且多个视图改变功能已经就位。我们将演示者进一步分开，但是不知道一个改变是否会影响其他东西的复杂性仍然存在。一定有更好的方法！

有一天我还在办公室(邢巴塞罗那)的时候开了一个见面会，会上演讲者在向观众介绍 [Redux](https://redux.js.org/) 。我很高兴我决定留下来，因为它最终启发了我，让我学会了函数式编程。我开始挖掘，发现 Redux 部分灵感来自 Elm，然后开始查 Elm。

Elm 是一种非常棒的纯函数式语言，它非常努力地对初学者友好，虽然我认为它做得很好，但范式的转变仍然足够。这与其说是语法本身，不如说是思维方式从命令式转变为功能式(对于 Android 用户，你还记得你是什么时候开始使用 RxJava 的吗？).经过一段时间的练习，我开始对 Elm 感到舒服，但是我仍然记得和一个同事的对话，他试图向我解释“单子”(Elm 没有单子)。不管我读了多少文章(包括[这篇带图的](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html))我还是不明白。事实是，即使不是所有的文章，也是大多数文章关注的是“什么”,而不是“为什么”我们需要单子。就在那时，我决定全力以赴，自学哈斯克尔。

在阅读了 [LYAH](http://learnyouahaskell.com/) 和大块的 [Haskell 编程](http://haskellbook.com/)(令人惊奇的书顺便说一下)之后，我决定为了好玩，通过构建一个 [ArnoldC 编译器](https://github.com/ps-feng/arnoldh)来做一些练习。这种体验非常有价值，即使我最终没有专业地使用 Haskell。它永远地改变了我看代码的方式，但是虽然我现在知道单子是什么，我也确信类型类(函子、应用程序、单子...)对于函数式编程来说并不重要，正如 Elm 或 Elixir 所示。[抽象是工具，不是目标](https://package.elm-lang.org/help/design-guidelines#avoid-gratuitous-abstraction)！我知道这是固执己见，其他人可能会有不同的意见，这很好。我认为类型类对于库作者来说特别有用，当他们努力提供通用的解决方案，但是终端用户应用程序特性很少需要这种级别的抽象。

乔·阿姆斯特朗说“国家是万恶之源”，我同意。我的结论是，有了这两条原则，你可以走得很远:

*   不变
*   参考透明性(对于给定的输入，您总是得到相同的输出，没有副作用)

只要你的编程语言有一流/顶级的功能，仅仅应用这两个原则就能让你走得更远。

纯函数擅长转换数据。对于任何给定的输入，它将返回相同的输出，没有副作用。这使得函数式编程适用于类似管道的应用程序，在管道中，输入数据通过不同的阶段进行转换。这种应用的例子有解析器、编译器、数据分析等。

但是 UI 或者 CRUD 应用呢？这类应用程序基本上充满了副作用。我们如何在这里适应函数式编程范式？这难道不违背前面提到的两个原则吗？

因此，在尝试使用函数式编程来解决我在过去几年中遇到的相同问题的过程中，我试图强迫自己严格应用函数式编程，并看看它会如何发展。

我在 Kotlin 中构建了一个[示例服务器应用程序](https://github.com/ps-feng/sample-fp-di)来测试 waters，记住了上面的 2 个原则。这是一个非常简单的 Spring Boot 应用程序，它公开了一个返回一些数据的端点。在内部，应用程序将调用另一个端点，并在返回响应之前处理结果。相当标准。

为了发挥作用，我遵循了以下准则:

*   只使用函数，避免使用除粘合初始化代码之外的类。没有类而只有函数会鼓励你从数据转换的角度考虑问题，避免状态。
*   使用`val`和不可变的数据结构(`Map`，`List`...).
*   避免副作用。

我遵循干净的架构，将我的代码分成 3 个逻辑层:表示层、领域层和数据层。当服务器接收到一个请求时，逻辑流程如下

`request handler -> interactor -> external services`

一切都好。但是后来我遇到了一个问题...我如何实现 DI？我的交互器(域)不再是类，所以我不能注入它们应该调用的库。

如果你在谷歌上搜索“函数式编程依赖注入”，你会发现(也许)关于这个主题的文献少得惊人。我可能知道为什么会这样，但是请耐心听我说，继续读下去！我将介绍两种最典型的方法:

*   部分功能应用
*   读者单子

还有其他的风格可以帮助你实现类似的目标，比如无标签的 final 模式或者自由单子，但是我不打算在这篇文章中讨论它们。

## 依赖注入目标

在我们看函数式编程对 DI 的解决方案之前，让我们后退一步，思考一下我们试图解决的问题。不涉及太多细节，我们可以将 DI 目标总结为:

*   控制反转和松耦合。实现不会选择它们的依赖关系。这允许调用者提供这些依赖项的具体实现，允许从外部配置代码的行为。通常，这是通过使类依赖于接口来实现的，接口的实现是通过构造函数传入的。
*   单元测试。这源于前面的目标，因为它允许我们将模拟实现注入到被测试的类中。

让我们看看如何用函数式编程来实现这些目标。

## 部分功能应用

部分函数应用程序是函数式编程中一个非常酷的概念，它将一些参数传递给一个函数，然后得到一个接受其余参数的函数作为回报。这个概念来源于[*curry*](https://stackoverflow.com/q/218025/328616)，这是一种技术，通过这种技术可以将任意一个 *N* 自变量的函数转化为一个自变量的一系列 *N* 函数。例如:

```
fun sum(a: Int, b: Int): Int = a + b
sum(1, 2) // returns 3

// can be converted into a function that accepts 1 argument (a)
// and returns another function that accepts 1 argument (b)
fun sum(a: Int): (Int) -> Int {  
    return { b -> a + b }  
}

val sum1 = sum(1)
sum1(2) // returns 3 
```

这对我们有什么帮助？想象一下，我们希望能够将不同的记录器注入到一个函数中。我们可能想要一个简单地将输出打印到控制台的程序，或者我们可能想要另一个将日志存储在文件中的程序。我们可以用 logger 部分地应用这个函数，得到的函数不再需要 logger 参数。

```
typealias ItemFetcherFn = () -> List<Item>

fun fetchItemsLogged(log: (String) -> Unit): ItemFetcherFn  {
    return {
        log("Fetching items")
        // fetch and return items...
    }
}

// let's use partial application to "inject" some logger
val consoleLogger: (String) -> Unit = { str -> println(str) }
val fetchItems: ItemFetcherFn  = fetchItemsLogged(consoleLogger)

// we can now use fetchItems anywhere, without knowing it has a logger
fun onStart(fetchItems: ItemFetcherFn ) { ... }

onStart(fetchItems) 
```

这种技术是我在我的[样本项目](https://github.com/ps-feng/sample-fp-di)的主分支中使用的。正如我之前提到的，我将代码分为 3 个逻辑层(干净的架构风格):表示层(或控制器)、域和数据层。如果你记得[依赖规则](http://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)，内层不能访问外层。我们的内层是领域层，所以它不应该有对表示和数据层的引用(注意，存储库被认为是数据层的接口，领域可以引用接口)。记住这一点，让我们从数据层的 [Services.kt](https://github.com/ps-feng/sample-fp-di/blob/d2225432bc79533d50d3dfb3320e3a4918cda702/src/main/kotlin/com/sample/todos/data/Services.kt) :
开始分析我们的例子

```
fun getUserTodos(
    baseUrl: String,
    userId: Int
): Mono<List<Todo>> {
    return WebClient.create(baseUrl).get()
        .uri { uriBuilder ->
            uriBuilder.path("/users/$userId/todos")
                .build()
        }
        .retrieve()
        .bodyToMono(object : ParameterizedTypeReference<List<DataTodo>>() {})
        .map { dataTodos -> dataTodos.map { it.toDomain() } }
} 
```

从领域层的角度来看，这个函数有一个`baseUrl`，这显然是一个数据层细节。从领域来说，我们不应该关心我们从哪里得到数据。如果我们使用面向对象，这个函数将被封装在一个存储库对象中，`baseUrl`可以通过构造函数注入，所以域层最终使用的函数签名是`fun getUserTodos(userId: Int)`。我们如何在函数式编程风格中实现这一点？使用部分功能应用！我们可以部分应用`baseUrl`。让我们看看这在[互动者的实践中是怎样的](https://github.com/ps-feng/sample-fp-di/blob/d2225432bc79533d50d3dfb3320e3a4918cda702/src/main/kotlin/com/sample/todos/domain/Interactors.kt)

```
typealias TodoFetcherFn = (userId: Int) -> Mono<List<Todo>>

fun getCompletedTodos(fetchTodos: TodoFetcherFn, userId: Int): Mono<List<Todo>> {
    return fetchTodos(userId).map { todos -> todos.filter { it.isCompleted } }
} 
```

我们的域函数依赖于一个类型为`TodoFetcherFn`的函数，它接受一个`userId`并返回一个`Mono<List<Todo>>`。我们不依赖于任何特定的实现，因此该功能是单元可测试的，所以我们实现了我们的 DI 目标。只要我们传入一个符合这个签名的函数，我们就可以开始了！

我们来看看这个函数是如何部分应用和“注入”的。这是来自控制器层的[todos . kt](https://github.com/ps-feng/sample-fp-di/blob/d2225432bc79533d50d3dfb3320e3a4918cda702/src/main/kotlin/com/sample/todos/Todos.kt):

```
fun getTodos(request: ServerRequest, fetchTodos: TodoFetcherFn): Mono<ServerResponse> {
    val userId = request.pathVariable("id").toIntOrNull()

    return if (userId != null) {
        val todosResponseMono =
            getCompletedTodos(fetchTodos, userId)
                .map { it.toResponse(userId) }
                .onErrorResume { e -> internalServerError(e) }

        ServerResponse.ok().contentType(MediaType.APPLICATION_JSON)
            .body(todosResponseMono, GetTodosResponse::class.java)
    } else {
        ServerResponse.badRequest().syncBody("id is not an integer")
    }
} 
```

最后这是注入根([sample application . kt](https://github.com/ps-feng/sample-fp-di/blob/2a78da39b991769c84015096c56b40fd87a3d17f/src/main/kotlin/com/sample/SampleApplication.kt)):

```
@Configuration
class Router @Autowired constructor(
    @Qualifier(TODOS_BASE_URL) private val todosBaseUrl: String
) {

    @Bean
    fun routes(): RouterFunction<ServerResponse> = router {
        accept(MediaType.APPLICATION_JSON).nest {
            GET("/user/{id}/completedtodos") {
                getTodos(it, getTodosFetcherFn())
            }
        }
    }

    private fun getTodosFetcherFn(): TodoFetcherFn {
        return ::getUserTodos.partially1(todosBaseUrl)
    }
} 
```

该示例使用[箭头](https://arrow-kt.io)来部分应用`getUserTodos` (baseUrl)的第一个参数。这个函数然后被传递给路由处理程序，路由处理程序然后用它调用交互器函数。

使用部分函数应用程序，我们成功地实现了 FP 版本的依赖注入。然而，你可能会有一些问题。

##### 如何孤立地测试路由处理器(`getTodos`)？

公平的问题。如果你想测试`getTodos`，你不可避免地要测试`getCompletedTodos`以及它对它的直接调用。我的建议是不要孤立地测试它，[测试整个特性](https://blog.twitter.com/engineering/en_us/topics/insights/2017/the-testing-renaissance.html)。只需使用一个[模拟网络服务器](https://github.com/square/okhttp/tree/master/mockwebserver)，端到端测试整个特性。这种方法有很多好处。

尽管如此，这是我的观点，你可能不同意。如果你真的想单独测试它，你可以让`getTodos`依赖于你传入的函数，而不是让它直接调用`getCompletedTodos`。我在[之前的提交](https://github.com/ps-feng/sample-fp-di/blob/d2225432bc79533d50d3dfb3320e3a4918cda702/src/main/kotlin/com/sample/todos/Todos.kt)中不小心做到了这一点，所以你可以检查它是如何工作的。注意，这个实现是错误的，传入的`fetchTodos`函数应该有一个与领域层的`TodoFetcherFn`分开的签名，即使它们看起来一样，这样我们就可以保持函数的独立性。

##### 如果你有 5 个依赖项，你是否向下传递 5 个自变量？

我的例子是不现实的，因为任何真正的应用程序都可能有更多的依赖关系。为了简单起见，我只是传递我知道我将使用的函数，但是你可以构造一个复合根，在其中你部分地应用我们在各层中将要调用的函数。然后你只需要传递下一层需要调用的函数。点击查看这是如何工作的[。另一种方法是传递带有所有依赖项的单个参数，这就是你对 Reader monad 所做的。](https://fsharpforfunandprofit.com/posts/dependency-injection-1/)

##### 依赖关系作为参数出现在调用链中所有函数的函数签名中

确实如此。这是否酷是主观的，但函数式编程中有一种技术，允许我们隐式传递一个参数，这样它就从函数的参数列表中消失了。下一节和读者莫纳德见面！

##### 代码导航更加困难，因为 cmd + click 对部分函数签名不起作用

是的。仔细想想，对 DI 使用部分应用的关键思想是松耦合，所以您不应该知道哪个函数最初是部分应用的。如果我们组织代码的方式没有对部分应用的函数进行深度嵌套调用，那么找到根应该很简单。

我希望我设法以一种可理解的方式解释了这个技术，但是如果这还不够，或者如果你只是想看另一个镜头，Scott Wlaschin 有一篇很棒的文章用一个更复杂的例子解释了它。

## 读者单子

我们发现，将同一个参数传递给一系列函数可能(主观上)令人讨厌。我提到过，阅读器 monad 可以允许我们隐式地传递那个参数...这怎么可能呢？我们可以将它放入一些全局值中，并在需要的地方直接访问它。我们不再需要将它传递给任何函数！但这只是硬编码，完全违背了依赖注入的目的，以防我们忘记了最初的目标。

好吧，那么读者单子是关于什么的？为了正确理解它，我们首先需要了解[什么是](https://stackoverflow.com/q/44965/328616)单子和[为什么](https://stackoverflow.com/q/28139259/328616)我们需要它。在此之前，您需要了解函子和应用程序。我个人花了很长时间才弄明白，甚至在阅读了许多解释并看到它们被表示为[图片](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)之后。我实际上从来没有得到它，直到我在实践中真正使用它们，所以我不打算在这里解释所有的概念。

相反，让我们看看我们是否可以建立一个关于它的直觉。我们可以从清楚地陈述我们的目标开始:

*   将参数传递给调用链中的所有函数
*   不要在这些函数的参数列表中显式包含它

如果我们调用一个返回值的函数，我们可以直接得到这个值。然而，如果我们想要**推迟**这个计算，我们可以将那个值包装在一个函数中并返回它。然后我们不会得到值，直到我们调用这个返回的函数。

```
fun giveMe5(): Int = 5

fun deferGiveMe5(): () -> Int = { 5 }

giveMe5() // returns 5
deferGiveMe5()() // returns 5 
```

如果我们把一堆返回相同输入的函数连接起来会怎么样？结果会是一个函数链，在我们真正调用它之前不会被求值，然后我们可以沿着这个链传递一些输入。

```
fun delegateNum(): (Int) -> Unit {
    return { num -> furtherDelegateNum()(num) }
}

fun furtherDelegateNum(): (Int) -> Unit {
    return { num -> printNum()(num) }
}

fun printNum(): (Int) -> Unit {
    return { num -> println(num) }
}

val chain = delegateNum()
chain(5) // prints 5 
```

在这种情况下，就是我们想要注入到链中的“东西”。它可能是我们需要对其中的所有功能都可用的依赖关系，而且是隐式的！所以我们实现了之前设定的目标。

现在，请注意`delegateNum()`、`furtherDelegateNum()`和`printNum()`的结构是如何相同的。事实证明，我们可以抽象这一点，这是宽松地说，什么是读者单子！创建一个可以*读取*一个公共值的懒惰(如延迟求值)函数链，然后运行这个链传递我们想要的值。

希望在这一点上，我设法给你一个读者单子的概念，所以让我们看一个真实的例子。我没有为我们的示例创建一个版本，但是我们可以使用 Jorge Castillo 的 [one](https://github.com/JorgeCastilloPrz/ArrowAndroidSamples/tree/master/monad-stack) ,它与我之前使用的架构相似，尽管他的示例是一个 Android 应用程序。在他的例子中，他从某个端点获取超级英雄列表。我们来查一下[的数据来源](https://github.com/JorgeCastilloPrz/ArrowAndroidSamples/blob/ea4f1702eac15860a468f4cf14fcb39ef408da73/monad-stack/src/main/java/com/github/jorgecastillo/kotlinandroid/data/datasource/remote/MarvelNetworkDataSource.kt) :

```
// MarvelNetworkDataSource.kt

/*  * This is the network data source. Calls are made using Karumi's MarvelApiClient.  * @see "https://github.com/Karumi/MarvelApiClientAndroid"  *  * Both requests return a new Reader enclosing an action to resolve when you provide them with the  * required execution context.  */
fun fetchAllHeroes(): Reader<GetHeroesContext, IO<List<CharacterDto>>> =
    ReaderApi.ask<GetHeroesContext>().map({ ctx ->
      IO.monadDefer().binding {
        val result = runInAsyncContext(
            f = { queryForHeroes(ctx) },
            onError = { IO.raiseError<List<CharacterDto>>(it) },
            onSuccess = { IO.just(it) },
            AC = ctx.threading
        ).bind()
        result.bind()
      }.fix()
    }) 
```

通过他的函数链传递的值是`ctx`，它的类型是`GetHeroesContext`，这是一个数据类，具有这个链需要的所有依赖关系(参见 [SuperHeroesContext.kt](https://github.com/JorgeCastilloPrz/ArrowAndroidSamples/blob/ea4f1702eac15860a468f4cf14fcb39ef408da73/monad-stack/src/main/java/com/github/jorgecastillo/kotlinandroid/di/context/SuperHeroesContext.kt) )。

该函数从`getHeroes` (HeroesRepository)调用，从 [HeroesUseCases.kt](https://github.com/JorgeCastilloPrz/ArrowAndroidSamples/blob/ea4f1702eac15860a468f4cf14fcb39ef408da73/monad-stack/src/main/java/com/github/jorgecastillo/kotlinandroid/domain/usecase/HeroesUseCases.kt) :
调用

```
fun getHeroesUseCase() = getHeroes(NetworkOnly).map { io -> io.map { discardNonValidHeroes(it) } } 
```

然后从`getSuperHeroes`([super heroes presentation . kt](https://github.com/JorgeCastilloPrz/ArrowAndroidSamples/blob/ea4f1702eac15860a468f4cf14fcb39ef408da73/monad-stack/src/main/java/com/github/jorgecastillo/kotlinandroid/presentation/SuperHeroesPresentation.kt#L39)调用，最后从[super herois stactivity . kt](https://github.com/JorgeCastilloPrz/ArrowAndroidSamples/blob/ea4f1702eac15860a468f4cf14fcb39ef408da73/monad-stack/src/main/java/com/github/jorgecastillo/kotlinandroid/view/SuperHeroListActivity.kt#L43):
调用

```
override fun onResume() {
    super.onResume()
    getSuperHeroes().run(heroesContext)
} 
```

请注意`heroesContext`是如何通过`run`函数在函数链中传递的，以及在链中函数的参数中没有它的痕迹。

这就是你如何用阅读单子做 DI！如果你觉得需要更技术性的解释，可以查看作者本人的这篇文章。

现在，你可能又会有一些问题...

##### 如何在阅读器单子和部分应用之间做决定？

这是一个[常见问题](https://stackoverflow.com/q/42780191/328616)。人们通常使用 Reader monad 的主要原因是为了避免反复传递相同的参数。尽管如此，这可能是主观的。

我在介绍中说过，在我看来，这种抽象(类型类)并不是绝对必要的，我们有一个替代方案，但是你是否喜欢它在某种程度上是主观的。就我个人而言，目前我并不认为我需要它们来实现我稍后将建议的方法(我将在下面告诉你，继续阅读！).

##### 我们不是将阅读器链中的所有函数都耦合到我们传递的值吗？

我也这么想...链中的任何函数都可以访问我们传递的值。在上面的例子中，这些函数中的任何一个都可以访问`GetHeroesContext`。我将在下一节详细介绍。

## 持批判观点

我们已经看到了在函数式编程中实现依赖注入的两种方法，但是当我们确实实现了我们在开始时设定的目标时，这种方法比 OO 风格的依赖注入框架更好吗？

关于部分功能应用，一个叫 Onur 的用户在我之前已经提到过的 Scott Wlaschin 的文章中留下了一个发人深省的评论:

> 这是一篇非常需要的文章，不幸的是，它没有抓住要点。通常，对象是不可组合的。依赖注入是解决这个问题的方法。就像函数式编程使用函数来构建更大的程序一样，依赖注入用于构建更大的对象程序。在您的示例中，依赖关系仅在 1 个级别中表示，但可以有更深层次的关系。其次，依赖注入注册是实现控制反转的一种方式，这样组件不会自己创建，但是注册就像一个描述依赖关系和层次结构的配方，并且**在应用程序**之外。第三，依赖注入还结合了组件的**生命周期**。最后，**所有这些都暗示着杂质**。

这确实是千真万确的。在我们的例子中，组合根可以说是*而不是*在我们的应用程序之外。[我们已经在我们的路由器函数](https://github.com/ps-feng/sample-fp-di/blob/2a78da39b991769c84015096c56b40fd87a3d17f/src/main/kotlin/com/sample/SampleApplication.kt#L40)中得到它。我们可以把它放到其他文件中，但是我们仍然需要手动定义部分函数应用程序，我们希望把它们传递给我们特定的入口函数，而使用阿迪框架，我们只需在某个地方定义提供者函数，然后只需在构造函数中声明我们想要使用的函数。

一生是另一回事...这样的生存期在纯上下文中没有意义，但是像数据库连接这样的东西是有生存期的。那么 DI 暗示杂质，在函数界尝试做有意义吗？

让我们回到上一节的最后一个关注点。在我们的例子中使用 Reader monad 是不是意味着我们将链中的所有函数都耦合到了我们传递的依赖上下文中？让我们来看看[超级英雄正文](https://github.com/JorgeCastilloPrz/ArrowAndroidSamples/blob/ea4f1702eac15860a468f4cf14fcb39ef408da73/monad-stack/src/main/java/com/github/jorgecastillo/kotlinandroid/di/context/SuperHeroesContext.kt) :

```
sealed class SuperHeroesContext(ctx: Context) {

  val heroDetailsPage = HeroDetailsPage()
  val apiClient
    get() = CharacterApiClient(MarvelApiConfig.Builder(
        BuildConfig.MARVEL_PUBLIC_KEY,
        BuildConfig.MARVEL_PRIVATE_KEY).debug().build())
  val threading = IO.async()

  data class GetHeroesContext(val ctx: Context, val view: SuperHeroesListView) : SuperHeroesContext(ctx)
  data class GetHeroDetailsContext(val ctx: Context, val view: SuperHeroDetailView) : SuperHeroesContext(ctx)
} 
```

该上下文有 5 个依赖项。再来看看[marvelnetworkdata source . kt](https://github.com/JorgeCastilloPrz/ArrowAndroidSamples/blob/ea4f1702eac15860a468f4cf14fcb39ef408da73/monad-stack/src/main/java/com/github/jorgecastillo/kotlinandroid/data/datasource/remote/MarvelNetworkDataSource.kt):

```
fun fetchAllHeroes(): Reader<GetHeroesContext, IO<List<CharacterDto>>> =
    ReaderApi.ask<GetHeroesContext>().map({ ctx ->
      IO.monadDefer().binding {
        val result = runInAsyncContext(
            f = { queryForHeroes(ctx) },
            onError = { IO.raiseError<List<CharacterDto>>(it) },
            onSuccess = { IO.just(it) },
            AC = ctx.threading
        ).bind()
        result.bind()
      }.fix()
    }) 
```

我们可以看到我们正在访问`ctx.threading`并且`ctx`被传递给`queryForHeroes`，后者在内部访问`apiClient`。但是是什么阻碍了我们访问`ctx.view`或者`ctx.heroesDetailPage`？这些应该对数据层可用吗？我认为我们违反了[依赖规则](%5Bdependency%20rule%5D%28http://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html%29)。除此之外，一般来说，争取“让不可能的状态变得不可能”是一个好主意(参见理查德·费尔德曼和帕特里克·斯特普弗的演讲，或本文)。如果我们不能访问`ctx.view`，我们就什么也做不了！

我们已经表明，我们可以通过部分函数应用程序或阅读器单子来实现 DI，但问题是，我们应该这样做吗？

## OO-FP-OO，一种混合方法

让我们提醒自己关于函数式编程我们最喜欢的事情:

*   不变性。一旦数据值被创建，我们就永远不能修改它。
*   参照透明。给定一个函数和一个输入值，每次你都会得到相同的输出，没有副作用*。*

这些属性使得对程序进行推理变得更加容易，因为您可以确信行为是一致的。如果语言是静态类型的，[等式推理](http://www.haskellforall.com/2013/12/equational-reasoning.html)成为可能。这将使我们有更少的错误和更少的认知负荷，因为你的思想背景应该只是你前面的功能。

典型的 Android 应用程序或后端 CRUD 服务充满了副作用。您需要访问网络或数据库来检索一些数据，然后您需要修改 UI 来显示这些数据或返回一些 Json 响应。最重要的是，像大多数语言一样，Kotlin 没有办法强制引用透明性，所以你可以使用任何由[箭头](https://arrow-kt.io/)提供的类型类，但是没有什么可以阻止你编写副作用代码，这些代码可能是像`println`一样明显无害的东西，或者你可以只调用`Thread.sleep(...)`来停止这个世界。在实践中，这意味着我们需要遵循一些准则，并确保我们团队中的每个人都知道这些准则。

如果我们需要访问网络或数据库，而我们无论如何都不能强制执行 purity，为什么我们不能更实际一点，简单地用 OO 风格来做这些呢？我们的领域逻辑可以与外部因素(如 UI 或数据层)隔离开来，因此我们可以用 FP 风格编写它。如果您将我们的业务逻辑视为一个确定性的黑盒，那么您只需传入一些数据(从数据库加载)，取出一些数据并将其呈现给 UI。

```
data layer input -> domain logic -> output UI 
```

如您所见，域逻辑位于中间。如果剩下的是 OO，我们得到一个 OO-FP-OO 三明治(这个概念的灵感来自于 [Dax Fohl](http://disq.us/p/1fdatya) ，尽管他建议采用 FP-OO-FP 方法)。

回到本文的主题和上一节末尾留下的未解决的问题，我们可以继续利用我们的 DI 框架，只保持我们的业务逻辑核心功能。注意，这并不意味着我们应该开始在 OO 部分的任何地方都有可变状态。我的方法是“优先选择 FP 而不是 OO”，这意味着通常要有 FP 的思维模式，但是要务实，在更方便且不危险的情况下，尽量少用 OO。

我在示例的另一个分支中尝试了这种方法。域逻辑保持[与部分应用案例
中的](https://github.com/ps-feng/sample-fp-di/blob/1302326254702d9a8e46c411e539efdd26231e2e/src/main/kotlin/com/sample/todos/domain/Interactors.kt)完全相同

```
// Interactors.kt
fun getCompletedTodos(fetchTodos: TodoFetcherFn, userId: Int): Mono<List<Todo>> {
    return fetchTodos(userId).map { todos -> todos.filter { it.isCompleted } }
} 
```

然而，现在 [Repositories.kt](https://github.com/ps-feng/sample-fp-di/blob/1302326254702d9a8e46c411e539efdd26231e2e/src/main/kotlin/com/sample/todos/data/Repositories.kt) 利用 Spring Boot 的依赖注入系统来注入基本 url:

```
@Component
class UserTodosRepository @Autowired constructor(
    @Qualifier(ExternalServices.TODOS_BASE_URL) private val baseUrl: String
) {
    fun getUserTodos(userId: Int): Mono<List<Todo>> {
        return WebClient.create(baseUrl).get()
            .uri { uriBuilder ->
                uriBuilder.path("/users/$userId/todos")
                    .build()
            }
            .retrieve()
            .bodyToMono(object : ParameterizedTypeReference<List<DataTodo>>() {})
            .map { dataTodos -> dataTodos.map { it.toDomain() } }
    }
} 
```

它被注入到[路由处理器](https://github.com/ps-feng/sample-fp-di/blob/1302326254702d9a8e46c411e539efdd26231e2e/src/main/kotlin/com/sample/todos/Todos.kt)中，后者只将`UserTodosRepository::getUserTodos`函数传递给域函数。

```
@Component
class TodosHandler @Autowired constructor(
    private val repository: UserTodosRepository
) {
    fun getTodos(request: ServerRequest): Mono<ServerResponse> {
        val userId = request.pathVariable("id").toIntOrNull()

        return if (userId != null) {
            val todosResponseMono =
                getCompletedTodos(repository::getUserTodos, userId)
                    .map { it.toResponse(userId) }
                    .onErrorResume { e -> internalServerError(e) }

            ServerResponse.ok().contentType(MediaType.APPLICATION_JSON)
                .body(todosResponseMono, GetTodosResponse::class.java)
        } else {
            ServerResponse.badRequest().syncBody("id is not an integer")
        }
    }
} 
```

由于我们正在使用 DI 框架，我们不再需要在外层中有部分应用程序样板文件。此外，我们的数据层可以只请求它所需要的依赖项，而不是像 Reader monad 的情况那样，在上下文中注入一个特性所涉及的所有层所需要的依赖项。

纯粹主义者可能会说，功能核心不应该知道任何关于`getUserTodos`函数的事情，而应该直接提供给用户 todos。我们的域函数将简单地通过`isCompleted`进行过滤，不做任何其他事情。实质上，副作用被推到了外层。这种风格类似于“端口和适配器”架构，马克·西曼[在这里](http://blog.ploeh.dk/2016/03/18/functional-architecture-is-ports-and-adapters/)做了很好的描述(警告:前面有 F#和 Haskell！).很公平，尽管你也可以说我们这样做的代价是将业务逻辑推到外层，这样我们就可以有一个纯粹的功能核心。

在任何情况下，你都可以看到我们可以成功地混合 OO 和 FP，并在它们最擅长的地方使用它们。DI 框架很方便，只要我们将副作用很好地封装在我们的 OO 部分和 FP 风格的纯数据处理部分中，我们就可以两全其美！

## 结论

我们已经看到，使用部分函数应用程序或阅读器 monad，我们可以得到与 OO 风格的依赖注入相似的结果。然而，它们可能不是最好的方法，因为它们看起来更像是面向对象模式到功能世界的直接转换。

OO 和 FP 并不互相排斥，我们可以在它们最有效的地方使用它们。一个 OO-FP-OO 三明治，其中业务核心是功能性的，外层使用 OO，可能是一个实用的前进方法。

在任何情况下，每种方法都有其优点和缺点，您选择哪种方法将取决于您手头的任务和您自己的偏好。

#### 部分功能应用

+函数式方法(没有类)
+显式，没有魔法
+实现简单
-冗长(在 Kotlin + Arrow 中)
-在复合根中大量的手动设置。

#### 读者单子

+函数方法(没有类)
+相对显式
-通过所有层向下传递的单个参数(如果像我们使用的例子中那样使用)
-在组合根中手动设置
-不同的层依赖关系可以被链中的所有函数访问

#### OO-FP-OO，混合方式

+简洁的代码
+易于指定的依赖关系
+ DI 框架负责编写样板文件
- DI 很神奇，尤其是当你不熟悉框架的时候
-使用类
-类可能会鼓励有状态代码
-开发人员需要严格保持函数的纯净，确保类只用于 DI，不用于保存状态

我个人认为 Kotlin 的混合方法是一个很好的实用方法，但是要注意它的缺点，尤其是最后一个缺点。

这是一篇很长的帖子！我希望你喜欢这本书，下次再见！
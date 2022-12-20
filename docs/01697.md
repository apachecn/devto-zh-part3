# 使用 Mock 和 Atrium 在 Kotlin 中进行模拟验证

> 原文：<https://dev.to/sirech/mock-verification-in-kotlin-using-mockk-and-atrium-glk>

最近我一直在和科特林一起工作。这是一种非常棒的语言。优雅、强大、简洁，它解决了我在使用 *Java* 时遇到的大部分烦恼，但它保持了一定的熟悉度，使得从它的过渡非常容易管理。

无论如何，我发现自己最近不得不在 [SpringBoot](https://spring.io/) 中构建一个我想要测试的过滤器。为此，我需要同时使用模拟和验证行为。*科特林*进化得相当快，有大量的替代品可供选择。我将展示如何用两个优秀的图书馆来做到这一点，[莫克](https://mockk.io/)和[中庭](https://docs.atriumlib.org/)。

## 莫克和中庭，一个强大的组合

在我开发 *Kotlin* 的短暂时间里，我注意到了一个模式。每当您需要标准库中没有提供的东西时，您倾向于从使用您熟悉的现有的 *Java* 库开始。然后，在某个时候，你会发现有一个本地的 *Kotlin* 库可以更好地利用这种语言的特性。

mock 似乎正在成为 *Kotlin* 应用程序事实上的嘲讽库。它的语法很大程度上基于 lambdas，看起来就像 DSL，正如你在这个直接取自他们页面的例子中看到的:

```
val car = mockk<Car>()

every { car.drive(Direction.NORTH) } returns Outcome.OK
car.drive(Direction.NORTH) // returns OK

verify { car.drive(Direction.NORTH) } 
```

Enter fullscreen mode Exit fullscreen mode

同时，*中庭*不太成熟，但是在得到一个同事的推荐后，我尝试了一下。它使用了`expect`，所以对于像我这样习惯了 *RSpec* 的人来说，这已经是一场胜利了。无论如何，语法需要一些时间来适应，但它可以很有表现力。我特别喜欢将它与数据类结合起来，使每个测试只有一个断言，而不是很多。

## 手头的问题

我试图构建的东西并不特别复杂。我想为一个 *SpringBoot* 应用程序编写一个过滤器，根据一些逻辑将一些头注入到请求中。所有控制器将透明地使用这些值，而不必关心计算。过滤器是这样的。

```
@Component
class Filter : OncePerRequestFilter() {
    @Autowired
    lateinit var processor: Processor

    override fun doFilterInternal(request: HttpServletRequest, response: HttpServletResponse, filterChain: FilterChain) {
        val wrappedRequest = HttpServletRequestWrapper(request).apply {
            addHeader(Headers.EXTRA_HEADER, processor.process(request))
        }

        filterChain.doFilter(wrappedRequest, response)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我想测试两件事:

*   应该用我的`wrappedRequest`调用`filterChain`
*   `wrappedRequest`中应该有正确的标题

### 设置测试

我正在使用 [JUnit 5](https://junit.org/junit5/) 进行测试(说到本地库，我还没有尝试过类似 [Spock](https://dzone.com/articles/testing-kotlin-with-spock-part-1-object) 的东西)。测试的基本设置需要设置我需要的过滤器和模拟。

```
@ExtendWith(MockKExtension::class)
internal class FilterTest {
    val request = MockHttpServletRequest()

    @MockK
    lateinit var response: HttpServletResponse

    @RelaxedMockK
    lateinit var filterChain: FilterChain

    val filter = Filter().apply {
        processor = ProcessorImpl()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我使用注释来初始化模拟(这需要用`MockKExtension`来注释测试)。我的`filterChain`是一个`RelaxedMockK`，这意味着它的方法将返回一个默认值，除非另外指定。

### 一个非常简单的测试

如果我只想检查方法是否被调用，我真的不需要太多的

```
@Test
fun `calls the next step in the filter`() {
    filter.doFilter(request, response, filterChain)
    verify { filterChain.doFilter(any(), response) }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 测试被包装的请求

之前的测试还可以，但是对我的口味来说有点平淡。我想确保用我的`wrappedRequest`调用`filterChain`，并且它包含我注入的头。这个测试变得有趣多了

```
@Test
fun `injects header into the request and passes it to the filter`() {
    filter.doFilter(request, response, filterChain)

    slot<ServletRequest>().let { slot ->
        verify { filterChain.doFilter(capture(slot), response) }

        expect(slot.captured).isA<HttpServletRequestWrapper> {
            expect(subject.getHeader(Headers.EXTRA_HEADER)).toBe("value")
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不再那么简单了！我们来分解一下。

首先，我们捕获`doFilter`的第一个参数(即:包装的请求)。我们通过执行`slot<ServletRequest>`来创建一个新的插槽，并通过执行`capture(slot)`将它传递到`verify`块来捕获它。包装所有东西的`let`块就在那里，这样我们就不需要额外的局部变量了(也让我们感觉内部更加 kotlin-y)。

毕竟，`slot.captured`包含了我们在过滤器中创建的`wrappedRequest`。这里是*中庭*可以大放异彩的地方。我们首先使用`isA`来检查请求的类型是否正确。然后在块`subject`内部是转换类型，我们最终检查我们的头是否在那里。

## 总结

有了它，我们的小过滤器就可以用很少的开销进行正确的测试。我不是 Kotlin 的鉴赏家，但是一旦你理解了,*mock*和 *Atrium* 的语法，对我来说感觉相当优雅。我认为这是一个很好的起点，可以在没有大量样板代码的情况下构建越来越好的测试。
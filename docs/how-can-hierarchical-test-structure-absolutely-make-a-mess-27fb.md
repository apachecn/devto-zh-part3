# 分层考试结构怎么绝对乱？

> 原文：<https://dev.to/waterlink/how-can-hierarchical-test-structure-absolutely-make-a-mess-27fb>

你曾经使用一个简单的 xUnit 风格的测试框架编写过你的单元测试吗？

那么你可能知道，随着测试变得越来越复杂，它们收集的样板文件和重复文件越来越多，要么在测试方法中传播，要么在设置函数中传播。

现在，层次化的上下文框架非常健壮，可以减轻这种样板问题并消除这种重复。它们允许您拥有嵌套的上下文，每个上下文都有自己的设置，并“继承”父上下文的设置。

这样，您可以表达许多不同的场景，而无需在测试设置代码中重复一次。

很棒，不是吗？

现在，如果我告诉你，层次化的测试结构会导致更微妙的重复(这本来是应该防止的),很难发现和重构，会怎么样呢？

让我给你一个过于简单的例子:

## 两个胎儿，难以捉摸地相像

前几天，我正在开发两个“fetcher”类，它们与第三方 API 的 HTTP 客户端对话，包含需要完成的业务逻辑，还可以处理即时的“成功”和异步的“接受”响应。

他们的代码非常相似，处理响应和重新调度异步任务的整个逻辑都是重复的，所以我们在产品代码中的一个单独的 collaborator 对象中对其进行了重构。

然后我们想:“嗯，我们在产品代码中重构了复制。测试代码中肯定会有重复。我们也把它弄干吧！”

没那么快！

当我们开始并排阅读两个测试套件时，发现它们看起来不太像。很难发现和隔离重复。

它们是以分层的风格编写的，并且充分利用了嵌套上下文的全部功能。

下面是第一个测试的简化示例(在 Kotlin 中):

```
describe("FetcherOne") {
    lateinit var asyncQueue: AsyncQueue
    lateinit var fetcher: FetcherOne

    val client = Client()
        .withMockResponseStatus("accepted")

    // … more irrelevant variables here …

    beforeEachTest {
        asyncQueue = AsyncQueue()
        fetcher = FetcherOne(asyncQueue, client)
    }

    describe("perform") {
        beforeEachTest {
            fetcher.perform()
        }

        it("tells client to do stuff") {
            assertThat(client.actions).contains("doStuff")
        }

        it("triggers an async polling job") {
            assertThat(asyncQueue).contains(fetcher::pollStatus)
        }

        // … more irrelevant tests here …

        context("when there is no need to check for status") {
            beforeGroup {
                client.mockResponseStatus("successful")
            }

            it("does not trigger an async polling job") {
                assertThat(asyncQueue).isEmpty()
            }

            // … more irrelevant tests here …
        }

        // … more irrelevant contexts here …
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以测试套件从根上下文开始。您可以在这里为您的测试套件设置全局默认值，并在嵌套的上下文中覆盖其中的一些。

如果您有一个典型的“快乐路径”场景，然后您有更多的“特殊情况”，您将使用嵌套上下文，这是很好的。

请注意，我们模拟 HTTP 客户端以“接受”状态进行响应:

```
val client = Client()
    .withMockResponseStatus("accepted") 
```

Enter fullscreen mode Exit fullscreen mode

然后在其中一个嵌套上下文中，我们将响应状态覆盖为“成功”:

```
beforeGroup {
    client.mockResponseStatus("successful")
} 
```

Enter fullscreen mode Exit fullscreen mode

记住所有这些，看看第二个测试套件:

```
describe("FetcherTwo") {
    lateinit var asyncQueue: AsyncQueue
    lateinit var fetcher: FetcherTwo

    val client = Client()

    // … more irrelevant variables here …

    beforeGroup {
        client.mockResponseStatus("successful")
    }

    beforeEachTest {
        asyncQueue = AsyncQueue()
        fetcher = FetcherTwo(asyncQueue, client)
    }

    describe("perform") {
        beforeEachTest {
            fetcher.perform()
        }

        it("tells client to do some other stuff") {
            assertThat(client.actions).contains("doSomeOtherStuff")
        }

        it("does not trigger an async polling job") {
            assertThat(asyncQueue).isEmpty()
        }

        // … more irrelevant tests here …

        context("when there is a need to check operation status async") {
            beforeGroup {
                client.mockResponseStatus("accepted")
            }

            it("triggers an async job to check the last operation") {
                assertThat(asyncQueue).contains(fetcher::checkLastOperation)
            }

            // … more irrelevant tests here …
        }

        // … more irrelevant contexts here …
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，这个测试套件的作者选择了不同的响应状态作为默认的“快乐之路”场景——“成功”:

```
val client = Client()

beforeGroup {
    client.mockResponseStatus("successful")
} 
```

Enter fullscreen mode Exit fullscreen mode

*(并在此基础上使用了不同的嘲讽风格)*

并且嵌套上下文将响应状态覆盖为“已接受”状态:

```
beforeGroup {
    client.mockResponseStatus("accepted")
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，有了这个例子，你就可以在颠倒一个测试套件的默认和定制场景之后，找出如何足够快地重构它，对吗？

这个例子很简单。我们之间的关系更复杂。假设您的异步轮询和重试逻辑(我们试图重构的内容)依赖于 4 个因素:

*   您将有 4 层嵌套的上下文，将每个元素描述为整个场景的一部分；
*   每个上下文可以选择不同的默认情况，而不是应该在内部上下文中覆盖哪些情况。

如您所见，这很快就会变得一团糟，并且很难重构。

如果我们有一个经典的平面测试结构，我们会在测试套件中有一点重复，但是并排比较测试套件并重构它们会容易得多。

让我给你看一个例子:

```
describe("FetcherOne") {
    lateinit var asyncQueue: AsyncQueue
    lateinit var fetcher: FetcherOne

    val client = Client()

    beforeEachTest {
        asyncQueue = AsyncQueue()
        fetcher = FetcherOne(asyncQueue, client)
    }

    it("perform - calls client") {
        client.mockResponseStatus("accepted")

        fetcher.perform()

        assertThat(client.actions).contains("doStuff")
    }

    it("perform - triggers async polling job when accepted") {
        client.mockResponseStatus("accepted")

        fetcher.perform()

        assertThat(asyncQueue).contains(fetcher::pollStatus)
    }

    it("perform - does not trigger async job when successful") {
        client.mockResponseStatus("successful")

        fetcher.perform()

        assertThat(asyncQueue).isEmpty()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第二个测试套件现在看起来非常相似(不值得在这里展示，但是如果你愿意的话，你可以[看到要点](https://gist.github.com/waterlink/e26a4dc585b628d6eb6074b9383a209f))。

如您所见，重构两个测试套件之间的重复几乎是一件容易的事情。

> 你想在 Kotlin 中学习更多关于测试和 TDD 的知识吗？
> 
> 我已经写了一个 4 部分(共 350 页)“[终极教程:Kotlin](https://iwillteachyoukotlin.com) 入门”(+更多即将到来)，你可以通过成为我每月简讯的成员获得免费的奖金。
> 
> 除了 Kotlin，它还有很多好东西，比如 TDD、干净的代码、软件架构、业务影响、5 个为什么、验收标准、人物角色等等。
> 
> —在这里注册，[开始学习如何使用 TDD](https://iwillteachyoukotlin.com) 构建成熟的 Kotlin 应用程序！

## 结论

现在，我不是想让你相信这种或那种风格。我只是想告诉你，这两种风格各有优缺点。

正如我们已经注意到的，即使是风格的力量也可能会让你搬起石头砸自己的脚。所以要小心:权力带来责任。

不要让你的层次测试太复杂，嵌套太多！

## 轮到你了

如果你喜欢我的想法，考虑给这篇文章很多发展的反应😊请在下面的评论中分享你对这两种风格的体验:

你还记得哪些棘手的测试情况？

* * *

原载于 [HackerNoon](https://hackernoon.com/how-can-hierarchical-test-structure-absolutely-make-a-mess-f72f47b5bf57)
图片来源: [pexels](https://www.pexels.com/photo/abstract-close-up-cobweb-connection-276502/)
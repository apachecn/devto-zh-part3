# xUnit 入门

> 原文：<https://dev.to/kritner/getting-started-with-xunit-5dp8>

由[麦克斯·尼尔森](https://unsplash.com/@maxcodes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的封面照片

我以前曾与 MSTest 和 NUnit 合作过，但由于某种原因没有与 xUnit 合作。幸运的是，来自任何一个框架似乎都很容易翻译成 xUnit。

在最近的帖子中，我做到了:

[你最喜欢/最有用的扩展方法？](https://dev.to/kritner/your-favoritemost-useful-extension-methods-51il)

我第一次探索与 xUnit 一起工作，以帮助确保我实现的扩展方法如我所愿地工作。虽然我以前写过关于扩展方法的文章，但是我没有涉及它们的测试；我认为它应该有自己的位置。

### 什么是单元测试？

如果你以前从未使用过单元测试， [wikipedia](https://en.wikipedia.org/wiki/Unit_testing) 将它们描述为:

> 在[计算机编程](https://en.wikipedia.org/wiki/Computer_programming)中，**单元测试**是一种[软件测试](https://en.wikipedia.org/wiki/Software_testing)方法，通过该方法对[源代码](https://en.wikipedia.org/wiki/Source_code)的各个单元、一个或多个计算机程序模块的集合以及相关的控制数据、使用程序和操作程序进行测试，以确定它们是否适合使用。[【1】](https://en.wikipedia.org/wiki/Unit_testing#cite_note-kolawa-1)

我通常是这样描述的:单元测试是确保一段代码——理想情况下是一个功能——产生预期结果的过程，不依赖于外部资源。当进行单元测试时，被测方法之外需要的任何东西都应该通过抽象来提供，而不是具体化——以确保你只测试*被测方法。该方法所需的依赖关系可以通过利用[模仿、伪造和/或存根](https://en.wikipedia.org/wiki/Mock_object)来提供，这些代码被编程来展示特定的行为，以帮助覆盖代码中所有潜在的“分支”。*

### MSTest、nUnit 和 xUnit 的区别

我没有使用过 MSTest，因为在此之前它允许在一个测试中使用多个测试场景，我不知道如何描述它们的区别。幸运的是，快速的谷歌搜索让我看到了 xUnit 网站上的一个“差异”页面:

[将 xUnit.net 与其他框架进行比较【xUnit.net T2】](https://xunit.github.io/docs/comparisons)

似乎所有的框架或多或少都做同样的事情；尽管我觉得有趣的是。net core 似乎在他们自己的 MSTest 测试框架上广泛使用了 xUnit。

### 项目设置

幸运的是，有一个项目模板(至少是我使用的 VS install 选项)可以创建一个 xUnit 测试项目。如果没有，您只需要将以下 NuGet 包添加到项目中:

*   微软。测试软件开发工具包
*   xunit
*   xunit.runner.visualstudio

您的 csproj 文件应该如下所示:
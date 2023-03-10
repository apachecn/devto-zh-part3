# 最佳 Python 测试框架

> 原文：<https://dev.to/lambdatest/top-5-python-frameworks-for-test-automation-in-2019-3pe3>

在被选为 2018 年最佳编程语言之后，Python 仍然继续上升，目前根据 Tiobe 发布的指数，Python 是仅次于 Java 和 C 的第三大最佳编程语言。随着这种语言的使用越来越多，基于 Python 的测试自动化框架也越来越受欢迎。显然，在为他们的项目选择最佳框架时，开发人员和测试人员会有点困惑。在选择一个时，你应该判断很多事情，框架的脚本质量，测试用例的简单性和运行模块的技术，并找出它们的弱点。这是我试图帮助你比较 2019 年测试自动化的前 5 个 Python 框架，以及它们相对于其他框架的优势和劣势。因此，您可以根据自己的需要选择理想的 Python 框架来进行自动化测试。

## 机器人框架

Robot Framework 主要用于验收测试驱动的开发以及验收测试，是顶级 Python 测试框架之一。虽然是用 Python 开发的，但是也可以在 **IronPython** 上运行，也就是**。基于. net** 和基于 **Java 的 Jython** 。Robot 作为一个 Python 框架兼容所有平台——Windows、MacOS 或 Linux。

**注:** **[洗牌](https://www.lambdatest.com/free-online-tools/shuffle-letters?utm_source=devto&utm_medium=organic&utm_campaign=apr20_kj&utm_term=kj&utm_content=free_tools)** 在线生成随机单词。该工具通过打乱字母顺序，从给定的输入中生成完全随机的单词。现在试试洗牌吧！

## 前提条件是什么？

是的，有

*   首先，你将能够使用机器人框架(RF)，只有当你安装了 Python 2.7.14 或任何高于它的版本。虽然现在也使用 Python 3.6.4，但是 RF 官方博客中提供的代码片段将确保添加适当的注释，包括所有需要的更改。

*   您还需要安装“pip”或 python 包管理器。

*   最后，开发框架应该是必须下载的。开发人员中流行的框架是 PyCharm 社区版。但是，由于代码片段不依赖于 IDE，因此您可以使用之前使用过的任何 IDE。

## 机器人有哪些优点&缺点？

让我们来看看 Robot 作为一个测试自动化框架相对于其他 Python 框架有哪些优缺点。

**优点**

*   使用关键字驱动的测试方法，它通过帮助测试人员容易地创建可读的测试用例，使得自动化过程更加简单。

*   测试数据语法可以很容易地使用。

*   由通用工具和测试库组成，它有一个巨大的生态系统，其中的各个元素可以在单独的项目中使用。

*   该框架高度可扩展，因为它有许多 API。

*   机器人框架通过 [Selenium Grid](https://www.lambdatest.com/blog/lambdatest-now-live-with-an-online-selenium-grid-for-automated-cross-browser-testing/?utm_source=devto&utm_medium=organic&utm_campaign=apr20_kj&utm_term=kj&utm_content=blog) 帮助你运行并行测试。但是，此功能不是内置的。

**缺点**

*   Robot Framework 虽然听起来非常方便，但在创建定制的 HTML 报告时却很棘手。但是，您仍然可以通过使用 Robot framework 呈现 xUnit 格式的简短报告。

*   机器人框架的另一个缺陷是并行测试的不足。

## Robot 对你来说是顶级的 Python 测试框架吗？

如果您是自动化领域的初学者，并且开发经验较少，使用 Robot 作为顶级 Python 测试框架比 pytest 或 pyunit 更容易使用，因为它具有丰富的内置库，并且涉及使用更容易的面向测试的 DSL。但是，如果你想开发一个复杂的自动化框架，最好还是切换到 pytest 或者其他任何涉及 Python 代码的框架。

## Pytest

用于各种软件测试， **pytest** 是另一个测试自动化的顶级 Python 测试框架。该工具是开源的，易于学习，可以被 QA 团队、开发团队以及个人实践小组使用，也可以用于开源项目。由于它像“断言重写”这样有用的功能，互联网上的大多数项目，包括 Dropbox 和 Mozilla 这样的大公司，都从 unittest(Pyunit)转向了 pytest。让我们深入研究一下，看看这个 Python 框架有什么特别之处。

## 前提条件是什么？

除了 Python 中的工作知识，pytest 不需要任何复杂的东西。你所需要的是一个有命令行界面、python 包管理器和开发 IDE 的工作桌面。

## pytest 有哪些优点&缺点？

**优点**

*   在 Python 测试社区中，在 pytest 出现之前，开发人员将他们的测试包含在大型类中。然而，pytest 带来了一场革命，因为它使得以比以前更紧凑的方式编写测试套件成为可能。

*   其他测试工具要求开发人员或测试人员使用调试器或检查日志并检测某个值的来源。Pytest 帮助您编写测试用例，使您能够存储测试用例中的所有值，并最终通知您哪个值失败了，哪个值被断言了。

*   测试更容易编写和理解，因为样板代码不需要那么多。

*   Fixtures 是您可以通过向测试函数添加参数来使用的函数。他们的工作是返回值。在 pytest 中，您可以通过使用一个夹具中的另一个夹具来使它们模块化。使用多个 fixtures 有助于您覆盖所有的参数组合，而无需重写测试用例。

*   pytest 的开发人员发布了一些有用的插件，使框架具有可扩展性。例如，pytest-xdist 可以用来执行并行测试，而不需要使用不同的测试运行程序。单元测试也可以参数化，而无需复制任何代码。

*   为开发人员提供了某些特殊的例程，使测试用例编写更简单，更不容易出错。代码也变得更短，更容易理解。

**缺点**

pytest 使用特殊例程的事实意味着您必须在兼容性方面做出妥协。您将能够方便地编写测试用例，但是您将不能在任何其他测试框架中使用这些测试用例。

## Pytest 对你来说是顶级的 Python 测试框架吗？

嗯，你必须从学习一门成熟的语言开始，但是一旦你掌握了它，你将获得所有的特性，比如静态代码分析，对多 IDE 的支持，最重要的是，编写有效的测试用例。对于编写功能测试用例以及开发一个复杂的框架，它比 unittest 要好，但是如果你的目标是开发一个简单的框架，它的优势有点类似于 Robot Framework。

[使用 pytest 和 Selenium WebDriver](https://www.lambdatest.com/blog/17-lessons-i-learned-for-writing-effective-test-cases/?utm_source=devto&utm_medium=organic&utm_campaign=apr20_kj&utm_term=kj&utm_content=blog) 运行您的第一个测试自动化脚本。

## UnitTest，又称 Pyunit

Unittest 或 PyUnit 是 Python 附带的单元测试的标准测试自动化框架。它深受 JUnit 的启发。断言方法以及所有清理和设置例程都由基类 TestCase 提供。TestCase 子类中每个方法的名字都以“test”开头。这允许它们作为测试用例运行。你可以使用 load 方法和 TestSuite 类来分组和加载测试。总之，您可以使用它们来构建定制的测试运行程序。就像用 JUnit 进行 [Selenium 测试一样，unittest 也能够使用 unittest-sml-reporting 并生成 XML 报告。](https://www.lambdatest.com/blog/automated-testing-with-junit-and-selenium-for-browser-compatibility/?utm_source=devto&utm_medium=organic&utm_campaign=apr20_kj&utm_term=kj&utm_content=blog)

**注:-** [**混排文本行**](https://www.lambdatest.com/free-online-tools/shuffle-text-lines?utm_source=devto&utm_medium=organic&utm_campaign=apr20_kj&utm_term=kj&utm_content=free_tools) -文本行混排器以一组文本行作为输入，并以随机顺序输出。现在试试洗牌。

## 前提条件是什么？

没有这样的先决条件，因为默认情况下 Python 附带了 unittest。要使用它，您将需要 python 框架的标准知识，并且如果您想要安装额外的模块，您将需要安装 pip 以及用于开发的 IDE。

## PyUnit 有哪些优点&缺点？

**优点**

作为 Python 标准库的一部分，使用 Unittest 有几个优点。

*   开发人员不需要安装任何额外的模块，因为它与盒子一起提供。

*   Unittest 是 xUnit 的衍生产品，其工作原理与其他 xUnit 框架类似。没有很强的 Python 背景的人一般会觉得工作起来很舒服。

*   您可以用更简单的方式运行单独的测试用例。您需要做的就是在终端上指定名称。输出也很简洁，使得框架在执行测试用例时很灵活。

*   测试报告在几毫秒内生成。

**缺点**

*   通常，snake_case 用于命名 python 代码。但是由于这个框架受 Junit 的启发很大，传统的 camelCase 命名方法仍然存在。这可能会非常令人困惑。

*   测试代码的意图有时变得不清楚，因为它太支持抽象了。

*   需要大量的样板代码。

## PyUnit 对你来说是顶级的 Python 测试框架吗？

根据我个人的观点以及其他 Python 开发人员的观点，pytest 引入了一些习惯用法，允许测试人员以非常紧凑的方式编写出更好的自动化代码。尽管 unittest 是一个默认的测试自动化框架，但是它的工作原理和命名约定与标准的 Python 代码有些不同，并且它需要太多的样板代码，这使得它不是一个首选的 Python 测试自动化框架。

## 规规矩矩

我们都知道行为驱动开发，这是最新的基于敏捷的软件开发方法，它鼓励开发人员、业务参与者和质量分析师相互协作。Behave 是另一个顶级的 Python 测试框架，它允许团队执行 BDD 测试而没有任何复杂性。这个框架的本质非常类似于自动化测试的 SpecFlow 和[Cucumber](https://www.lambdatest.com/blog/automation-testing-with-selenium-cucumber-testng/?utm_source=devto&utm_medium=organic&utm_campaign=apr20_kj&utm_term=kj&utm_content=blog)。测试用例是用简单易读的语言编写的，然后在执行过程中粘在代码上。行为是由行为规范设计的，这些步骤可以被其他测试场景重用。

## 前提条件是什么？

任何具备 Python 基础知识的人都应该能够使用 Behave。我们来看看前提条件。

*   在安装 behave 之前，你必须安装 Python 2 . 7 . 14 以上的任何版本。

*   使用 behave 需要 Python 包管理器或 pip。

*   开发环境是你需要的最后也是最重要的东西。您可以使用大多数开发人员首选的 Pycharm，也可以使用您选择的任何其他 IDE。

## 规规矩矩有什么优点&缺点？

像所有其他行为驱动测试框架一样，关于 behavior 的优势的观点因人而异。让我们来看看使用 Behave 的常见利弊。

**优点**

*   系统行为由半正式语言和领域词汇表表达，这些词汇表使行为在组织中保持一致。

*   从事具有相似特性的不同模块的开发团队得到了适当的协调。构建模块总是准备好执行各种测试用例。

*   推理和思考以细节为特色，从而产生更好的产品规格。

*   由于规范的格式相似，利益相关者或管理者对 QA 和 dev 的输出有更好的清晰度。

**缺点**

*   唯一的缺点是它只适用于黑盒测试。

## 是为你守规矩的顶级 Python 测试框架？

嗯，正如我们所说，Behave(Python 框架)只对黑盒测试最有效。Web 测试是一个很好的例子，因为用例可以用简单的语言描述。然而，对于集成测试或单元测试，behave 并不是一个好的选择，因为冗长只会导致复杂测试场景的复杂化。开发人员和测试人员都推荐 pytest-bdd。它是 behavior 的替代方法，因为它使用了 pytest 中所有好的东西，并实现它来测试行为驱动的场景。

**注意:-** [**小写文本**](https://www.lambdatest.com/free-online-tools/text-lowercase?utm_source=devto&utm_medium=organic&utm_campaign=apr20_kj&utm_term=kj&utm_content=free_tools) -免费在线工具，可将大写文本立即转换为小写，没有任何广告或使用限制。现在尝试将大写文本转换为小写文本。

## 生菜

莴苣是另一个简单易用的基于 Cucumber 和 Python 的行为驱动自动化工具。莴苣的主要目标是关注行为驱动开发的常见任务，使过程更简单和有趣。

## 前提条件是什么？

您将需要至少安装 Python 2.7.14 和一个 IDE。您可以使用 Pycharm 或您选择的任何其他 IDE。此外，为了运行测试，您将需要安装 python 包管理器。

## 有哪些优点&有哪些缺点？

*   嗯，就像任何其他 BDD 测试框架一样，莴苣使开发人员能够创建多个场景，并用简单的自然语言描述这些特性。

*   开发和 QA 团队得到了适当的协调，因为规格是相似的格式。

*   对于黑盒测试，莴苣是非常有用的运行行为驱动的测试案例。

**缺点**

使用莴苣作为 Python 框架只有一个缺点。为了成功执行行为驱动测试，开发团队、QA 和利益相关者之间的沟通是必要的。缺席或沟通障碍会使流程变得模糊不清，任何团队都可能提出问题。

## 莴苣对你来说是顶级的 Python 测试框架吗？

根据开发人员和自动化测试人员的说法，Cucumber 在执行 BDD 测试时更有用。然而，如果我们谈论的是 Python 开发人员和 QA，没有比 pytest-bdd 更好的替代品了。pytest 的所有优秀特性，如紧凑性、易于理解的代码，都在这个框架中实现，并结合了冗长的行为驱动测试。

[https://www.youtube.com/embed/9RINZgnr3Bw](https://www.youtube.com/embed/9RINZgnr3Bw)

## 包装完毕！

在上面的文章中，我们讨论了基于不同测试过程的 2019 年测试自动化的 5 大 Python 框架。pytest、Robot framework、unittest 是用于功能和单元测试的，而莴苣和 Behave 只适合于行为驱动测试。从陈述的特性，我们可以得出结论，对于功能测试，pytest 是最好的。但是如果你是基于 python 的自动化测试的新手，Robot Framework 是一个很好的入门工具。虽然功能有限，但它能让你在赛道上轻松领先。对于基于 python 的 BDD 测试，莴苣和 behavior 都同样好，但是如果你已经有了 pytest 的经验，最好使用 pytest-bdd。

我希望我的文章能帮助你从顶级 Python 测试框架中做出正确的选择，满足你的 [Python web automation](https://www.lambdatest.com/python-web-automation?utm_source=devto&utm_medium=organic&utm_campaign=apr20_kj&utm_term=kj&utm_content=blog) 需求。测试愉快！
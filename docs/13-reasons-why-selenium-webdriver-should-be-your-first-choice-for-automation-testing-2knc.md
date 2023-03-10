# Selenium WebDriver 应该成为自动化测试首选的 13 个理由！

> 原文：<https://dev.to/lambdatest/13-reasons-why-selenium-webdriver-should-be-your-first-choice-for-automation-testing-2knc>

Selenium 由 Thoughtworks 于 2004 年开发，供内部使用，是一种广泛用于 web 应用程序自动化测试的工具。最初，Selenium IDE(集成开发环境)被全球多个组织和测试人员使用，Selenium 自动化测试的好处是节省了大量时间和精力。使用 Selenium IDE 进行自动化测试的主要缺点是它只能在 Firefox 上工作。为了解决这个问题，使用了 Selenium RC(远程控制),这使得 Selenium 能够支持自动化的[跨浏览器测试](https://goo.gl/TkFjcu)。

但目前，网站正变得更加强大，具备多种功能。此外，如今，随着大多数组织[都在制作一个手机友好型网站](https://goo.gl/tsHXtW)，测试人员必须记住，一个网站不仅要能在桌面上正常工作，还要能在 Android 和 iOS 上正常工作。Selenium RC 逐渐落后，因为它不具备跨多种设备测试网站浏览器兼容性的功能。为了克服所有这些问题，Selenium Webdriver 介入进来。

Selenium Webdriver 支持许多 Selenium 原始版本不支持的特性。此外，它具有测试动态网站的能力，即在点击按钮或其他用户功能时页面内容动态变化的网站。它的工作原理是模仿一个正在与网页互动的真实用户。此外，Webdriver 的 API(应用编程接口)是用户友好的，易于理解。它还可以与其他测试框架一起使用，比如 [JUnit，以简化 Selenium](https://goo.gl/tThsxQ) 的自动化测试。在下一节中，让我们详细了解 web 驱动程序与 Selenium RC 或 IDE 的不同之处。

## Selenium web driver 和 Selenium RC & IDE 有多大区别？

除了一些很酷的特性和支持多种语言，还有许多其他特性使 Selenium WebDriver 不同于 Selenium IDE 或 Selenium RC。让我们来看看。

*   Selenium IDE 只适用于 Mozilla，而 RC 和 WebDriver 适用于 IE、Chrome、Firefox、Opera、Safari 和许多其他浏览器。
*   Selenium WebDriver 和 RC 不支持录制和回放，而另一方面，Selenium IDE 支持。
*   Selenium RC 的一个缺点是，它要求用户在执行脚本之前启动服务器。但是，WebDriver 和 IDE 一样，不需要用户启动任何服务器。
*   谈到应用程序类型，Selenium IDE 是一个基于 GUI 的插件。Selenium RC 是一个基于 Java 的独立应用程序，它允许用户执行基于 HTML 的测试套件。但是 WebDriver 作为一个核心 API，在绑定的帮助下提供了对不同编程语言的多语言支持。
*   Selenium IDE 和 RC 的核心引擎是基于 JavaScript 的，而在 WebDriver 上，没有核心引擎，应用程序可以只使用浏览器进行本机工作。
*   与 IDE 和 RC 相比，Selenium WebDriver 可能有点复杂，因为 API 更大。
*   在 Selenium IDE 中，API 不是面向对象的。对于 RC 来说有点面向对象，而在 WebDriver 上，所有的 API 本质上都是完全面向对象的。
*   Selenium IDE 和 RC 不支持鼠标光标移动，而 WebDriver 既支持这两者，也支持鼠标光标的移动。
*   在配置 Selenium IDE 和 RC 时，需要使用语法“xpath=”附加完整的 XPath。然而，WebDriver 根本不需要这个过程。
*   Selenium WebDriver 支持其实现不受 RC 或 IDE 支持的侦听器。
*   最后，如上所述，iOS 和 Android 应用程序测试在 WebDriver 中受支持，而在 RC 或 IDE 中不受支持。

## 为什么用 Selenium WebDriver 进行自动化测试应该是你的首选？

现在我们都知道了 Selenium WebDriver 是什么，以及它与 Selenium 其他版本的区别。让我们讨论一下使用 Selenium WebDriver 进行自动化测试的好处。

1.  **开源**:首先，使用 Selenium WebDriver 进行自动化测试的主要好处是它是开源的。它提供了 QTP 的所有功能，而且相对更多，完全免费。它可以从官方网站直接下载，并且基于社区，也可以获得对该工具的支持。
2.  语言支持:多语言支持是 Selenium WebDriver 自动化测试的主要优势之一。WebDriver 支持所有测试人员应该了解的编程语言，如 Python、PHP、Java、C#、Ruby、JavaScript 等。虽然它有一个定制的脚本语言，但它也提供了对每一种主要编程语言的绑定。增加了 web 开发人员使用他们熟悉的任何编程语言的灵活性。
3.  **跨多个操作系统工作**:与之前发布的版本不同，Selenium Webdriver 的主要优势之一是支持多个操作系统，如 Linux、UNIX、Mac 以及 Windows。使用他们的解决方案套件，可以创建可在任何平台上使用的定制测试套件。WebDriver 使你有可能在 Windows 上创建一个测试用例，并在 Mac 上执行它。
4.  **跨浏览器兼容性测试**:与之前的版本不同，Selenium WebDriver 的自动化测试优势扩展了对自动化跨浏览器测试的支持。它支持所有主要的浏览器，所以你可以在 Chrome，Firefox，Safari，Opera，IE，Edge，Yandex 和更多浏览器上进行测试。当您对网站进行跨浏览器测试时，WebDriver 为您提供了一个自动化的解决方案。
5.  **支持多种框架和语言** : WebDriver 可以集成 Maven 或 ANT 等各种框架来编译源代码。对于应用程序测试和报告，它还可以与测试框架(如 [TestNG)集成，以便使用 Selenium WebDriver](https://goo.gl/XJCxF1) 简化自动化测试。它甚至可以与 Jenkins 集成，用于[持续集成或持续交付](https://goo.gl/CM6gPz)自动化构建和部署。
6.  **跨设备测试**:跨多个设备的支持是 Selenium WebDriver 自动化测试的另一个主要好处。现在可以为 iPhones、Blackberry 和 Android 上的测试编写自动化测试用例，从而帮助解决跨设备问题。
7.  社区支持:Selenium 的支持主要是基于社区的，因此可以定期升级和更新。所有的更新都是随时可用的，不需要任何特定的培训。因此，使 Selenium WebDriver 预算友好且资源丰富。
8.  **易于实现**:Selenium WebDriver 的用户友好性是 Selenium web driver 为自动化测试带来的广受好评的好处之一。该工具是开源的，它允许用户编写他们的个性化扩展，这允许他们开发定制的操作，一旦用户达到高级水平，就可以操纵这些操作。
9.  **插件和可重用性**:使用 Selenium WebDriver 编写的脚本支持[浏览器兼容性测试](https://goo.gl/PBH5zG)。因此，测试人员可以使用 WebDriver 运行多个测试场景，因为它涵盖了每个功能测试方面。可以定制的附加组件扩大了测试应用程序的范围，为使用 Selenium WebDriver 进行自动化测试提供了巨大的好处。 [![Download Whitepaper](img/509a11516833e3cdea484340c5c3922c.png)](https://www.lambdatest.com/blog/13-reasons-why-selenium-webdriver-should-be-your-first-choice-for-automation-testing/#) 
10.  **鼠标光标和键盘模拟**:web driver 的一个重要特性是可以通过处理鼠标和键盘事件来模拟真实的用户场景。API(高级用户交互)由需要执行这些事件时所需的动作类组成。您可以自动化简单的场景，如鼠标单击或按键事件，以及复杂的事件，如拖放、单击并按住、多项选择等。
11.  **利用代码**:使用 Selenium WebDriver 进行自动化测试的另一个主要好处是通过利用开发代码来加速测试周期。测试人员可以利用开发人员使用的语言。例如，如果测试人员想要验证数据库中更新的日期字段，而不是通过浏览到另一个页面来执行间接验证，测试人员可以提取应用程序使用的数据模型，而不用担心自动化数据流。
12.  **不需要启动服务器**:使用 Selenium WebDriver 进行自动化测试的一个主要好处是，您不需要在测试之前启动任何服务器。代码中编写的命令被直接解释为 web 服务，远程驱动程序通过 HTTP 请求接收这些命令，然后这些请求可以在浏览器中执行，从而发送响应。
13.  **高级浏览器交互** : WebDriver 还可以让你模拟高级交互，比如点击浏览器的后退和前进按钮。没有这样的开源工具可以为这种场景提供自动化测试。这对于测试不存储任何 cookies 或缓存的涉及在线资金转账或银行应用的金融科技应用非常有用。

## LambdaTest 如何在云上用 Selenium WebDriver 帮助无缝自动化测试？

通过在本地工作站上安装 Selenium WebDriver，您可以使用该工具执行[自动化跨浏览器测试](https://goo.gl/5FjVbC)。然而，这有一定的局限性。您的本地工作站可能没有安装所有浏览器及其版本。此外，它还受到多个操作系统的限制。

这就是 LambdaTest 来帮助你的地方。使用他们可扩展的、基于云的、跨浏览器的测试平台，您可以通过他们的 Selenium 网格彻底执行 Selenium 测试。Selenium grid 将为您提供在 2000 多种浏览器上执行[浏览器兼容性测试](https://goo.gl/idkppN)的权限，并确保您的应用在所有这些浏览器上都能完美呈现。不仅如此，您还可以跨不同的操作系统和设备测试您的应用程序。因此，不需要安装额外的虚拟机或浏览器。你所需要的是工作互联网连接和一个 LambdaTest 上的免费注册，开始为你的应用程序执行自动化测试。

没有什么是完美的，除了强调 Selenium WebDriver 的好处之外，它也有一些缺点。如果您正在使用任何其他测试套件，并且想要将您的测试环境迁移到 Selenium，这是不可能的。然而，如果您的项目是新的，并且您正在寻找一个自动化测试套件，使测试更容易，只需要很少的手工劳动，那么使用 Selenium WebDriver 进行测试应该是您的最佳选择，而不是使用像 QTP 这样的付费工具。

这就是我所能想到的 Selenium WebDriver 自动化测试的所有好处。如果你有更多的列表，请在下面的评论区写下它们。

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://goo.gl/3MNn2Z)

原文出处:[lambdatest.com](https://goo.gl/gBp8NH)

**相关文章**

1.  [使用 JUnit 和 Selenium 对浏览器兼容性进行自动化测试](https://goo.gl/h3TsCB)
2.  [react js 在 2019 年会继续称霸吗？](https://goo.gl/W6XdxL)
3.  [什么是持续集成和持续交付？](https://goo.gl/9SEHiL)
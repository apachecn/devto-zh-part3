# Selenium IDE 到 Selenium web driver——迁移到脚本自动化

> 原文：<https://dev.to/maheshkay/selenium-ide-to-selenium-webdriver---migrating-to-script-automation--3hea>

我刚入职的时候，我们公司没有测试团队。我们被要求自己测试界面。我那时经常使用 Java 和 Python 后端。在前端有很多 Jquery 模块。这会让任何测试人员工作几个小时。

过去有像 QTP/UFT 这样的工具。这个工具很贵，对于小团队预算来说，创业公司负担不起。我知道你们中的许多人可能正在与跨国公司合作，他们可以让你们获得这些工具，而没有任何预算问题。但是由于与初创公司合作，我们的预算有限。

所以硒来拯救我们了。

Selenium 有大量的语言绑定，这有助于您现有的工作与测试脚本的集成。还有专门为 Firefox 开发的 Selenium IDE。

[![enter image description here](img/8d39096b6aad57916fda48eebf5a0ece.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C0En0F2D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ftm3wGN.png)

我确信有一些类似的其他工具可以在 chrome 和其他浏览器上工作，并且有和 Selenium 一样的用例。

**为什么要用硒？**

*   易于无人值守执行
*   许多脚本可以同时执行。
*   报告和记录更加容易。
*   与平台和语言无关。
*   免费和开源

关于 Selenium 最好的部分是它是免费和开源的。这有助于扩展这个框架，并按照公司希望的方式使用它。所以你不局限于像其他公司那样使用他们自己的语言绑定。

**网络驱动和 Java**

当我们的客户项目最初是 Java 的时候。我们尝试了其他一些有助于 webdriver 的高级工具。当时，大多数在线教程都是针对使用 Java 作为绑定语言的 Selenium Webdriver 的。

现在 Selenium 3 使用了 Firefox Gecko 驱动程序。我们现在正在把现有的脚本转移到新版本的 Firefox 驱动程序中。如果您还没有切换，请使用 Selenium WebDriver 和 Java setup 检查 [Firefox Gecko 驱动程序。](https://www.youtube.com/watch?v=hbxRgpMTif8)

现在，我们的许多客户端应用程序都从 Java 中移走了。我认为至少我的公司正在远离 java webdriver。我知道许多大公司都在大量使用。net 和 Java。但是考虑到 Javascript 和 Python 在初创公司中获得了更多的份额。我想是的，在不久的将来，转向 Python 和 Javascript 对我来说是安全的。

**WebDriver 和 Python**

我发现 python 更容易编写脚本和测试。我们的客户将一些后端项目转移到 python。所以要测试接口和结果输出。

我们有必要检查 selenium 的 python 绑定。你可以通过 Selenium Webdriver for Python 了解如何设置 [Chrome 驱动程序。如果你已经学会了如何安装 pip 包并在你的系统路径中设置驱动程序，这是非常容易做到的。](https://www.youtube.com/watch?v=V69wc4Tmwjc)

Web 自动化之后，我们将何去何从？

我将学习如何自动化我的一些测试应用程序，这样我就可以将我的 selenium 经验用于应用程序自动化。目前反应和颤动占用了我大部分时间。因此，一旦我有了值得发布的东西，我可能会使用这些测试平台之一来测试应用程序。

虽然我不完全是一名测试员。但是当公司想让你自己做测试的时候。在这种情况下，学习编写测试并让你的工作接受测试就成了一种习惯。在这个过程中，我学到了很多小东西。现在我的重点是学习应用程序的自动化。

你测试自己的代码或应用的过程如何？
# 你对脚本测试和录音回放测试感到困惑吗？

> 原文：<https://dev.to/lambdatest/are-you-confused-between-scripting-testing-and-record--replay-testing-3gc7>

所以你正计划向自动化测试迈进。但是你一直在争论选择哪一个？您应该向记录和重放自动化测试迈进吗？或者您更愿意坚持使用优秀的旧脚本？在这篇文章中，我们将帮助你弄清楚这两种方法之间的区别，即记录&重放&脚本测试。

软件规划、开发和测试是您在任何项目中都会遇到的关键活动，并且这些活动中的每一个都不能单独执行，因为每个项目都有一些“内部和外部依赖”。说到测试，你应该遵循“黑盒测试”和“白盒测试”的正确组合。这两种测试方法之间的主要区别是[测试策略](https://www.lambdatest.com/blog/why-you-need-to-understand-test-management-strategy-to-become-pro/)、测试计划、测试脚本和测试执行所需的技术专业知识的深度。一个擅长“黑盒测试”的测试人员在没有任何关于源代码的内部知识的情况下，根据正常的&边界用例测试产品。这种方法与“白盒测试”完全相反，在“白盒测试”中，测试人员编写测试代码来测试产品的功能。

“记录和重放测试”是执行黑盒测试的一种流行的测试机制。它非常适用于具有静态用户界面的产品，并且很少的更改被推送到生产服务器(至少从 UI/UX 的角度来看)。

## 洞见录&回放测试

顾名思义，预定义的测试场景会被记录下来&只需按一下按钮，就可以播放这些场景(即‘Record’来记录用例，而‘Replay’来回放记录的用例)。这种方法听起来很有趣，因为该工具允许您创建一个脚本来自动执行那些记录的动作。这种方法有一个巨大的好处，因为 QA 团队可以最早开始产品测试，并且 QA 团队不需要任何编程专业知识。对于喜欢执行“左移”测试的组织来说，这是一个完美的选择。

众所周知，产品需求可以在一段时间内改变 bug 修复，这可能导致后端代码的变化，以及用户界面(前端)的变化。在这种情况下，记录和回放测试将会中断，因为这些测试是在假设用户界面很少发生变化的情况下创建的。

记录和重放可能是学习测试自动化的第一步，因为测试人员不需要任何脚本/编码专业知识。对于那些来自手工测试背景，并想在‘自动化测试’领域立足的人来说，这是一个很好的学习工具。然而，随着用例数量的增加，记录的脚本可能会很大，这会影响这些脚本的执行速度。这是一个理想的方法，因为它不会对记录的脚本产生太大的影响。总而言之，

*   记录和重放测试适用于测试时间紧张的应用程序/网站。

*   对于有手工测试背景并想冒险进入自动化测试的测试团队来说，它是理想的。

*   开发/测试团队之外的成员可以使用它。

***你知道吗？为网络开发者提供的在线 [MAC 地址生成器](https://www.lambdatest.com/free-online-tools/random-mac-generator?utm_source=devto&utm_medium=organic&utm_campaign=jul18_rd&utm_term=rd&utm_content=free_tools)，免费，易于使用，没有广告或弹出窗口。尝试这种简单而方便的方法来生成随机的 MAC 地址。***

## 记录回放测试中可能出现的主要实时问题&

每种技术都有缺点，下面是关于记录和重放测试的一些技术:

**问题 1 —录制过多**

当你在任何网站上导航时，你倾向于执行许多动作，其中一些动作可能是无意的。记录和重放工具的问题是它会记录“太多的信息”,并且大部分信息可能是“噪音”,即不需要成为测试脚本的一部分。例如，下面是录音片段

*   将鼠标移动到位置 100，200(相对于 X，Y 坐标)

*   点击“用户标识”文本框

*   键入用户名

*   点击“密码”文本框

*   键入密码

*   等待 5 秒钟

*   点击“提交”按钮

这个场景看起来非常好，但是 5 秒钟的延迟可能会产生误导。有可能是测试人员输入了错误的密码并试图删除该密码，或者有其他原因。

**问题 2 —录像太少**

记录和重放测试的问题是，有时记录会太少，这样短的记录不会转录成“有效的测试脚本”。在这种情况下，记录的测试脚本必须重新记录，这可能需要更多的时间&两次重试。

还有一种可能是你记录了错误的东西，根据用例的复杂性，记录所花费的时间将会是指数级的。例如，在记录一个用例时，您在最后一步犯了一个错误。因为你不能部分录制，你需要重新录制整个场景。

**问题 3——测试用例的修改&循环执行测试用例**

假设您有一个测试场景，其中您必须测试用户的真实性。这将涉及到在你的网络应用程序/网站的各个领域的“用户名和密码”的基础上的测试用例的排列和组合。如果您计划使用“记录和重放测试”来执行测试，您必须对每组用户名和密码执行相同的任务。您不能修改“记录的测试脚本”来在脚本中输入每个用户的详细信息。

由于记录的测试脚本可能是不可理解的(例如，它可能是只能由 Record & Replay 工具解码的 HexCode)，您将为每个场景创建一个新的脚本，即使测试场景中的变化本质上可能是极小的。这些是记录回放测试的一些主要缺点，但是对于对自动化测试感兴趣的人来说，这是一个理想的起点。此外，从您的开发团队到营销团队，您的组织中的任何人都可以执行它。

***你知道吗？一个在线的 [IP 生成器](https://www.lambdatest.com/free-online-tools/random-ip-generator?utm_source=devto&utm_medium=organic&utm_campaign=jul18_rd&utm_term=rd&utm_content=free_tools)是免费的，易于使用，没有广告或弹出窗口。尝试这种简单而方便的方法来生成随机的 IP 地址。***

## 洞察脚本

使用 Selenium(自动化 web 应用程序的开源工具)创建测试脚本被认为是最受欢迎的自动化测试方法。与记录和重放测试不同，被分配了使用 Selenium framework 编写测试脚本任务的测试人员需要具备相当数量的 Python/PHP/Perl/Ruby/其他[编程语言](https://www.lambdatest.com/blog/top-programming-languages-helpful-for-testers/)的编码专业知识。

根据需要测试的功能，测试人员还应该了解 Selenium 中用于相应编程语言的不同模块。基于脚本的测试对于任何类型的项目都是可行的选择(无论是在开发阶段还是维护阶段)。因为 Selenium 是一个广泛使用的测试自动化框架，所以开发团队中的成员很可能也有大量关于它的知识。在这样的场景中，他们可以与测试人员合作，提出[有效的测试用例](https://www.lambdatest.com/blog/17-lessons-i-learned-for-writing-effective-test-cases/?utm_source=devto&utm_medium=organic&utm_campaign=jul18_rd&utm_term=rd&utm_content=blog)和具有长期利益的测试脚本。

与记录和重放测试不同，修改脚本(使用 Selenium 编写)是可能的；尽管周转时间取决于脚本开发人员/测试人员的编码专业知识。它还适合于执行压力测试和回归测试，这使它成为自动化测试的“更具可伸缩性的方法”。

LambdaTest blog 已经有了大量的自动化测试入门资源，这些资源对自动化入门很有帮助。此外，我们会定期发布越来越多的博客，供您讨论自动化测试。

总而言之，

*   使用 Selenium 编写脚本是具有“开发思维”并拥有 Selenium 等框架知识的测试人员的首选。

*   它适合于以更健壮的方式创建测试套件/测试用例。

*   使用 Selenium 框架的测试脚本可用于实现最佳的“代码覆盖度量”，即测试代码甚至可用于测试边界场景和正常测试场景。

*   适合执行端到端测试。

***你知道吗？[随机十六进制生成器](https://www.lambdatest.com/free-online-tools/random-hex-generator?utm_source=devto&utm_medium=organic&utm_campaign=jul18_rd&utm_term=rd&utm_content=free_tools)允许用户生成随机十六进制数。***

## 结论

基于项目的时间表和测试团队的技术知识，您应该选择记录和重放测试或者使用像 Selenium 这样的框架编写脚本。尽管每种方法都有自己的优缺点，但是一个理想的测试计划是并行执行测试的。作为一名负责测试 web 应用程序/网站特性的测试人员，您应该记住您的 web 应用程序在不同浏览器、操作系统和设备上的跨浏览器兼容性。就自动化跨浏览器测试而言，并行测试可以提供最大的吞吐量。如果你是自动化的新手，那么记录和回放可以帮你扭转局面。一旦使用 Record & Replay 保存了测试，您就可以并行执行这些脚本(在不同的浏览器、操作系统和设备组合上)。但是，如果您已经精通一种编程语言，那么 Selenium 可以提供强大的、可定制的多线程功能，以最大效率并行运行自动化跨浏览器测试用例。
([https://goo.gl/iNepU8](https://goo.gl/iNepU8))

1.  [什么是持续集成和持续交付？](https://goo.gl/Ubwf9k)
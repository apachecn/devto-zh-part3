# 开发人员和设计人员的辅助工具

> 原文：<https://dev.to/vorillaz/accessibility-tools-for-developers-and-designers-369a>

在[的上一篇文章](https://dev.to/vorillaz/getting-started-with-web-accessibility-4k82)中，我已经介绍了开始使用 web accessibility 的几个要点，在这篇文章中，我将介绍如何轻松地建立一个 A11Y 开发环境，如何测量和报告您的进度，以及最后一些自动化报告的方法。

## 从小做起。

开始使用可访问性可能看起来很难，也很有挑战性，尤其是对于维护遗留代码库的团队来说。除了学习关于 HTML、JavaScript 甚至设计弹性用户界面的可访问性的基础知识，你必须从建立一个稳固的工作环境开始，为你自己和你的同事整合一些工具。
您可以从获取、测量、报告开始，最终逐步走向更易访问的代码库。

## 开发工具

[Edge](https://docs.microsoft.com/en-us/microsoft-edge/devtools-guide/elements/accessibility) 、 [Firefox](https://developer.mozilla.org/en-US/docs/Tools/Accessibility_inspector) 、 [Safari](https://developer.apple.com/library/archive/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html) 和 [Chrome](https://developers.google.com/web/tools/chrome-devtools/accessibility/reference) 都有一个检查模式，通过可访问性树提供对当前页面上暴露于辅助技术的重要信息的访问。您可以使用它们来跟踪开发过程中的任何问题、错误或瓶颈。

## 林挺

对于遗留或新的代码库，您可以使用林挺来检测和防止常见的可访问性问题。使用林挺与独立工具或 ESLint 插件，你可以马上开始。此外，您还可以建立一种通用的沟通语言，而不考虑团队内部的可访问性。

*   React A11y 是 JSX 的一个 ESlint 插件
*   Vue A11Y 是一个社区驱动的 Vue 工具集
*   eslint-plugin-react-Native-a11y 非常适合 React 本地项目

## 举报

报告您的代码库的状态和健康状况可能是至关重要的，尤其是对于分布式团队。使用上面的工具，你可以追踪你的进度，设定目标，并对你即将发布的版本进行评估。

*   Pa11y 是一个报告可访问性问题的命令行界面实用程序。
*   [Tenon](https://tenon.io/) 是一个在线爬虫，为 508 和 WCAG 2.1 问题提供即时反馈。
*   Web 可访问性评估工具是一个在开发者中广泛使用的可访问性浏览器扩展。
*   Lighthouse 是一个独立的浏览器扩展，可以生成关于可访问性问题的深度报告。
*   a11y.css 是一个关于 css 问题的小书签。

W3C 也有一个很棒的在线服务和网页可访问性评估工具的列表。

## 自动化流程

报告在某些时候会令人沮丧，特别是对于那些不定期交付生产的团队。报告和林挺可以实现自动化，但是使用 CLI 工具可以轻松地与您的 CI/CD 工作流集成。此外，像 Lighthouse 这样的工具与 git-hooks 配合得非常好。
您甚至可以在部署时在捆绑过程中添加可访问性检查和报告功能，[accessibility-web pack-plugin](https://github.com/evcohen/accessibility-webpack-plugin)就是一个很好的例子。最后但同样重要的是，有一些很棒的工具，比如 [accesslint](https://github.com/accesslint) 可以处理 GitHub 的 pull 请求。

## 测试

测试始终是开发团队的优先事项，为易访问性测试调整工作流应该被建立，并且在开发生命周期中是至关重要的。

*   AxeCore 是一个由 Deque Systems 开发的用于端到端可访问性测试的自动化模块。你可以直接使用 AxeCore 和[硒](https://marmelab.com/blog/2018/02/22/accessibililty-testing-e2e.html) [小丑](https://github.com/nickcolley/jest-axe)或[木偶师](https://marmelab.com/blog/2018/07/18/accessibility-performance-testing-puppeteer.html)。- [自动化易访问性测试工具](https://github.com/paypal/AATT)或 AATT 也是端到端自动化测试的一个选项。AATT 是由贝宝开发和使用。
*   A11y 机器或 a11ym 是一个自动化的可访问性测试工具，它抓取并测试任何 Web 应用程序的页面，以生成详细的报告。

## 手动测试

GDS 易访问性团队发现，只有 [~30%的问题是通过自动化测试](https://accessibility.blog.gov.uk/2017/02/24/what-we-found-when-we-tested-tools-on-the-worlds-least-accessible-webpage/)发现的，因此自动化总是一种策略，但是手动测试可以增强最终用户的体验。使用屏幕阅读器进行测试应该成为你的可访问性测试的一部分。
请注意浏览器/屏幕阅读器的组合很重要。建议您在最适合您选择的屏幕阅读器的浏览器中测试您的应用程序。
下面是一些常用的屏幕阅读器/浏览器组合:

*   非可视桌面访问或 NVDA 是一个开源的 Windows 屏幕阅读器，与 Firefox 一起广泛使用。
*   VoiceOver 是苹果设备上与 Safari 一起流行的集成屏幕阅读器。
*   通过语音或 JAWS 进行作业访问是 Windows 和 IE/Edge 上广泛使用的屏幕阅读器。
*   Window-Eyes 也常用于 IE 和 Edge。
*   ChromeVox 是 Chromebooks 上的集成屏幕阅读器，可作为谷歌 Chrome 的扩展。

## 最后的想法

开始开发或设计时考虑可访问性有时看起来很头疼，特别是对于那些长期一起工作并且已经建立了一些基础知识、工具或公共交流语言的团队来说。幸运的是，你可以在开始时遵循一些小步骤，并逐渐采用一种更“容易”的开发、设计或测试方式。我们比以往任何时候都可以使用更多的服务，为每个人创造一个更好更开放的网络。

*这个教程最初是发布在我的[博客](https://www.vorillaz.com)上的。*
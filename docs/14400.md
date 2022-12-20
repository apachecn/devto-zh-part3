# 为什么您应该使用木偶师进行测试

> 原文：<https://dev.to/lambdatest/why-you-should-use-puppeteer-for-testing-4a6o>

在过去的十年里，世界上出现了强大的基于 Javascripts 的 web 应用程序，同时新的框架也在发展。这些框架挑战了长期以来与削弱网站性能相关的问题。交互式 UI 元素、无缝速度和令人印象深刻的样式组件已经开始在一个网站中共存，并且不会严重影响速度。CSS 和 HTML 现在被注入到 JS 中，而不是相反，因为 JS 更有效。虽然这些 [JavaScript 框架](https://www.lambdatest.com/blog/choosing-the-right-javascript-framework-in-2018/?utm_source=devto&utm_medium=organic&utm_campaign=jul20_sd&utm_term=sd&utm_content=blog)的使用提升了性能，但也让测试人员付出了代价。

测试和调试变得越来越复杂。作为一个节点库，Puppeteer 已经被引入，以支持 Chrome 浏览器测试。

从木偶测试开始你的旅程？看看你如何在 [LambdaTest 的在线云](https://www.lambdatest.com/puppeteer-testing?utm_source=devto&utm_medium=organic&utm_campaign=jul20_sd&utm_term=sd&utm_content=webpage)上测试你的木偶测试脚本。

## 无头浏览器

顾名思义，无头浏览器是作为一个应用程序进程运行的浏览器，没有任何用户界面元素。无头浏览器，比如 Chrome，速度更快，占用内存更少，更灵活，在自动化压力下更稳定。

Google 一直致力于确保为 DevTools 生态系统提供适当的支持。使用 Puppeteer，web 爬行、抓取和启动自动化脚本现在可以比以前更轻松、更稳定地完成。

## 自动化变得容易

在自动化测试方面，Selenium 领先业界。这个奇妙的开源工具已经被测试社区欣然接受。但是这是一个大问题，selenium 测试需要安装浏览器，不管 selenium 是安装在云上还是本地机器上。再加上与各种组件集成的复杂性，流程很难平稳运行。

当谈到在浏览器中使用自动化脚本时，Headless chrome 已经成为人们的首选。Karma、Mocha 和 Puppeteer 的使用让使用自动化脚本的测试突然变得更加容易。

***嘿！你知道[这个大写文字工具](https://www.lambdatest.com/free-online-tools/text-uppercase?utm_source=devto&utm_medium=organic&utm_campaign=jul20_sd&utm_term=sd&utm_content=free_tools)，可以让你把所有的小写字母变成大写字母。只需将文本复制并粘贴到下面的文本区域，然后单击按钮。您将得到所有的文本，除了它将是大写的。***

## 可视化 UI 回归测试

Puppeteer 可以用于各种 UI 元素的回归测试。为了测试网站的 UI，建议使用最新的 chromium 版本来确保更好的 api 控制。通常 Mocha 与 Puppeteer 一起使用，以便对各种 UI 特性进行回归测试。此外，摩卡为您提供了可以在测试前后使用的挂钩。

Puppeteer 之所以成功，主要是因为它简化了与在浏览器上执行的自动化任务相关的功能。

## 截图测试

Jest、Headless Chrome 和 Puppeteer 的终极三重奏可以用于基于截图的测试。事实上，LambdaTest 的自动截图功能充分利用了这三个工具，可以在多种 chrome 浏览器上截图。您可以轻松地捕获 React 树和其他可序列化的值，并在测试时使用它们来分析状态变化。Jest 带有强大的 React 生态系统，它的集成为 JavaScript 元素的测试铺平了道路。

puppet er-screen down-tester 是 puppet er 本身的一个库，允许在测试时生成屏幕截图。

类似地，ava-puppeteer 是 GitHub 上的另一个库，不久将在 npm 上提供。

[![](img/8963695be9773c941c344b18c9462f87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pO9PGlKs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AwCpOIKLH2kT_OJZL.jpg)

***了解一下:[testμ](https://www.lambdatest.com/testuconf-2022/?utm_source=devto&utm_medium=organic&utm_campaign=jul20_sd&utm_term=sd&utm_content=testu_conf)(TestMu，读作“TestU”)会议将软件测试人员、开发人员、有影响力的人和社区构建者聚集在一起，讨论测试的未来和它背后的人们。***

## 性能测试

使用木偶师，可以测量和分析性能指标，以便针对不同的设备和网络速度优化网站。Chrome 提供了能够记录性能时间线的开发工具，当自动化时，可以揭示与性能相关的问题。木偶师是 Chrome 开发者工具协议的高级 API 控件。木偶师轻松处理异步元素及其与 DOM 状态变化相关的行为的熟练程度。您甚至可以将性能数据保存为 JSON 文件。

这么说吧，当你使用木偶师的时候，你就是木偶师。您可以对服务人员进行控制，并在有或没有缓存时间的情况下测试缓存以及网站的加载时间。使用 responseEnd 可以跟踪网站响应时间，以及网络带宽和延迟相关问题的影响。

## 使用木偶师进行负载测试

负载测试仅仅是计算您可能在您的站点上运行的最大实例数。多个 Puppeteer 实例可以轻松地并行启动，通过它您可以对网站进行负载测试。

[![*Load Testing*](img/52a40f331562c89b905cafd135791724.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZjBC-BJn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2AVD-C8G0bKIVOjkiK.jpg)

Puppeteer 名副其实，因为你对你的无头 chrome 有一个神圣的命令，你有能力在单个或多个浏览器会话上启动大量的测试实例。

```
$ puppeteer-loadtest — s=120 — c=40 — file=XYZ.js 
```

Enter fullscreen mode Exit fullscreen mode

这里,–s 代表样本数,–c 代表每个样本的并发会话数。

## [端到端测试](https://www.lambdatest.com/learning-hub/end-to-end-testing?utm_source=devto&utm_medium=organic&utm_campaign=sep13_sd&utm_term=sd&utm_content=learning_hub)

端到端或验收测试通常被认为是最不受欢迎的测试之一。Selenium 早期用于验收测试，它很容易出错，有很多 bug。然后是木偶戏，更加关注 API 控制，帮助解决以 bug 形式出现的复杂问题。木偶师的超能力之一是它不需要知道你的堆栈，可以与任何框架一起工作，无论是 React，Angular 还是任何其他库。

***你知道吗？[文本重复器](https://www.lambdatest.com/free-online-tools/text-repeater?utm_source=devto&utm_medium=organic&utm_campaign=jul20_sd&utm_term=sd&utm_content=free_tools)按照指定的次数打印输入字符串的重复。对于每次重复，它都在新的一行中打印字符串。***

## 结论

木偶师和 chrome 的成功不能仅仅归结于它们的效率，它得到谷歌支持的事实也是它被全球接受和认可的原因。然而，最大的问题是，它与无头浏览器的完整性是辉煌的，但与其他浏览器可能会动摇。

话虽如此，我坚信这个工具将随着不断扩大的谷歌生态系统而繁荣发展。木偶师的未来一片光明。
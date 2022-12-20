# Vue cli 3:夜间监视+浏览器堆栈

> 原文：<https://dev.to/digitaledawn/vue-cli-3-nightwatch-browserstack-jek>

继[我去年写的文章](https://dev.to/digitaledawn/nightwatch-browserstack-2381-temp-slug-6227363)关于我们使用 Vue cli 2 的 Nightwatch 和 Browserstack 进行端到端测试的配置之后，我想分享一下我们现在使用 Vue cli 3 的新设置。

这是我们想要实现的设置的概要。我们希望能够在以下方面运行端到端测试:

*   使用本地 selenium 和 Chromedriver 的本地主机。
*   使用本地 selenium 和 Chromedriver 测试/生产 URL
*   浏览器堆栈期间测试/生产 URL

更多的细节在之前的文章中，所以如果你需要的话可以查看一下。在这里，我将主要分享最终的配置。

快速提醒一下:

*   您需要使用 Browserstack local，这样它就可以访问您网络中任何不能公开访问的链接
*   默认情况下，Browserstack 不会在失败时更新会话状态，而是使用文件名进行命名测试。您必须使用他们的 API 来更新测试状态和名称，并且需要访问 selenium 会话 id 来这样做。

如果您不熟悉使用 Vue cli 3 进行端到端测试，最好在阅读之前先看一下[。基本上，它允许您使用自己选择的框架(Nightwatch 或 Cypress)运行端到端测试，而无需您进行任何配置。runner 部分是抽象的，您可以提供自己的配置，它将与他们提供的默认配置合并。](https://cli.vuejs.org/config/#nightwatch)

### 夜视配置

除了 Cli 3 正在处理的内容(它有一个与我们的配置合并的默认配置)之外，这与之前的几乎相同。

现在，我们从 dotenv 文件(Vue Cli 也使用)中获取测试配置和生产配置(在我们的例子中用于每个环境的不同 URL 和 id)。
# 用于 PWA 开发的高效堆栈

> 原文：<https://dev.to/kefranabg/a-productive-stack-for-pwa-development-27o>

“一次编写，随处使用”。我喜欢这种理念，我认为这是每个 web 开发人员的梦想:构建一个所有平台(移动、浏览器、桌面)都完全支持的应用程序，无需安装步骤。这是一个复杂的想法，渐进式网络应用程序正在试图解决。

在本文中，我将讨论一个完整的全栈环境，它可以帮助我快速启动 PWA 项目。

在开始之前，我想分享一下这个 github 库。这个开源项目是一个入门模板，它完美地阐释了我将在后面提出的观点。因此，如果您需要一个具体的集成示例，请随意查看。这个堆栈非常固执己见，但这也是它强大的原因。

以下是我的需求:

“我想要一个完整的全栈环境，它能帮助我专注于我的开发技能，在不损害最佳实践的情况下快速构建渐进式 Web 应用。”

让我们一步一步来！

## 数据库+ REST API

我将非常简短。我们不想要任何数据库或 REST API。让我说得更具体一些:我们不想独自管理它们。为什么？因为很难托管它们，而且需要维护更多的代码。我们也不想处理缩放等问题。请记住，我们想要的是在开发过程中快速！

firebase 的 Firestore 似乎是一个不错的选择！多亏了客户端 API，我们将能够从云 NoSQL 数据库中读取和写入数据，而 [firestore 安全规则](https://firebase.google.com/docs/firestore/security/get-started)将帮助我们提供访问控制和数据验证。此外，Firestore 为数据持久性提供了一个[离线支持](https://firebase.google.com/docs/firestore/manage-data/enable-offline)，这对于 PWAs 来说非常有趣。Firestore 也是一个实时数据库。这意味着我可以监听我的数据结构的特定部分，并实时更新我的应用程序。

如果你需要在一个安全的环境中运行代码(不是在客户端)，该怎么办？例如，当客户端执行 CRUD 操作时触发业务逻辑？

为了处理这些具体情况，我们可以使用 [firebase 云函数](https://firebase.google.com/docs/functions/)。

酷！我们已经完成了堆栈的大部分工作，确保我们已经删除了大量代码。这就是我们想要的！

## 用户认证

你真的应该使用第三方(电子邮件、电话、社交)来验证你的用户。开发人员编写的代码更少，我们的用户验证也更简单。

同样，我们可以使用 firebase 来节省一些开发时间。 [Firebase 认证](https://firebase.google.com/docs/auth/)提供了很多处理认证的方法:

*   社交网络登录(谷歌、脸书、推特、Github……)
*   电话认证
*   带有认证链接的电子邮件
*   经典电子邮件/密码

## 前端

### 💚Javascript 框架:

为什么选择 [Vue.js](https://vuejs.org/) ？因为我真的很喜欢这个框架的理念，而且我使用它的效率很高。它很简单，提供了很好的开发者体验，并且可能是当今最容易学习的 javascript 框架。

在我看来，没有“最好的 javascript 框架”。最重要的是使用你喜欢的工作。

### 💅UI 组件框架/ CSS:

在 PWA 环境中，我建议编写自己的 CSS 和 UI 组件。我认为很难找到一个好的轻量级 UI 组件框架，我们的 PWA 需要快速加载。所以如果你决定使用一个框架，要小心它会如何影响你的应用程序的大小。

### 🔁前端状态管理:

Vuex 是 Vue.js 的默认状态管理器，使用起来非常简单。它配有 [vue-devtools](https://github.com/vuejs/vue-devtools) ，这是一个令人惊叹的浏览器扩展，可以调试你的应用程序，跟踪事件，并对你的应用程序中的数据结构有一个全局的了解。

### ✅测试框架:

我个人喜欢使用 Cypress(仅 Chrome)进行 e2e 测试，Jest 进行单元测试，但最重要的是使用你喜欢的测试框架。

### 👨‍✈️前端质量工具:

我们的先决条件之一是产生高质量的代码，因为 PWA 必须是快速和轻量级的。以下是我们可以使用的工具:

*   [BundleSize](https://github.com/siddharthkp/bundlesize) :代码拆分是你应该关心的事情。这是提高网站性能最简单的方法。您的 PWA 需要在初始加载时很快，即使网络连接很差。这就是为什么你的应用程序应该分成几个 javascript 包。BundleSize 有助于保持对组成应用程序的不同包大小的永久控制。

*   灯塔(browser extension/chrome devtools):一个伟大的审计工具，可以为你的 web 应用页面生成报告。lighthouse 报告将对你的网页的几个主题进行评级(性能、可访问性、最佳实践和渐进式网络应用)。这些报告也会给你一些建议来改善你可能得到的负面评价。

*   代码覆盖率(chrome devtools):这个工具将显示网页上执行了多少代码和加载了多少代码。这将有助于您了解您可以 lazyload 哪部分代码，并且只发布用户需要的代码。

*   在我看来，代码格式非常重要。我不介意用不用分号。对我来说，唯一重要的是代码统一，这样任何人都能以同样的方式阅读。不管你喜不喜欢，pretty 都会按照自己的方式格式化代码，这就是 pretty 的伟大之处。

*   尽可能多的使用 linter 规则(在合理的范围内)，这将组织你的代码并帮助你回顾最佳实践。如果不想自己写一套规则，我建议使用现有的 eslint 配置。我喜欢 airbnb 的，因为它非常严格和完整。

## PWA 支持

如果你不熟悉 PWA，建议你看一下[这个](https://developers.google.com/web/progressive-web-apps/)。

在这一部分，我不打算谈论服务人员如何工作，或者如何创建 web 应用程序清单。已经有很多关于它的文章和文档了，所以我将把重点放在你可以用来开个好头的工具上，并根据我的经验给你一些建议。

### [【pwacompath】](https://github.com/GoogleChromeLabs/pwacompat):

该库将 Web 应用清单提供给不兼容的浏览器，以获得更好的渐进式 Web 应用。它还带有一些其他很酷的功能，比如为 IOS 创建动态闪屏图像(IOS 上默认还不支持)。

### [【vista-CLI 插件 pwa】](https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-pwa):

这个插件将帮助你用 Workbox 配置你的 PWA，这是今天处理服务人员的最好方法。默认情况下，为了更加简单，您的服务工作者文件是从一个基本的 JSON 配置(您可以访问)生成的。但是如果你想要对你的服务工作者配置有更多的控制，你将不得不自己写一个服务工作者文件(参考[官方文档了解更多细节](https://developers.google.com/web/tools/workbox/modules/workbox-cli#injectmanifest))。
Vue-cli-plugin-pwa 附带了库 [register-service-worker](https://github.com/yyx990803/register-service-worker) ，该库简化了服务工作者注册，并公开了常见服务工作者事件的挂钩，如“updateFound”(当新内容可用时)、“registered”(当服务工作者已注册时)等。

### 离线:

如果没有网络连接就不能正常工作，那么 PWA 就不是好的 PWA。一个干净的离线管理需要两件事:

*   静态文件缓存:如果您希望应用程序在没有网络连接的情况下启动，这是一个必需的步骤。如果你使用的是 vue-cli-plugin-pwa，希望你不需要配置任何东西。否则，您将需要进行一些工具箱配置。

*   动态缓存:最简单的方法是显示一个离线页面，告诉用户如果没有网络连接就不能访问数据，但是我们可以做得更好。我们想要的是用户可以访问他之前已经获取的数据。如果您的数据来自 firestore 数据库，您只需启用[离线持久化](https://firebase.google.com/docs/firestore/manage-data/enable-offline)，firestore 会处理剩下的事情。否则，您必须用 workbox 缓存请求响应。
    现在我们完成了基本配置！

但是请记住，您仍然可以改善您的 PWA 用户体验。以下是您可以添加的一些改进示例:

*   鼓励你的 IOS 用户安装你的 PWA:
    IOS 不会像 Chrome 在 Android 设备上那样自动提示用户安装 web 应用。但是你可以很容易地克服这个缺点，通过显示一个模态提示，用清晰简单的步骤来安装应用程序。[这里有一篇关于这个](https://dockyard.com/blog/2017/09/27/encouraging-pwa-installation-on-ios)的文章。

*   为您的 PWA 显示“新版本可用”:
    您是否曾在浏览网站时注意到弹出通知，告知您该网站有新版本可用？这正是我要说的。由于这个弹出窗口，用户将明白他不是你应用程序的最后一个版本，所以他需要重新加载以获得最后一个版本。

## 预渲染

在许多情况下，服务器端渲染是多余的。我喜欢预渲染的一点是，它不会影响我编写前端应用程序代码的方式或项目的架构，同时获得 SSR 的几乎所有优势。如果你不知道什么是预渲染，也不知道什么时候应该(不应该)使用预渲染，建议你看一下[这个](https://github.com/chrisvfritz/prerender-spa-plugin#what-is-prerendering)。

为了预渲染我们的应用页面，我们可以使用 [prerender-spa-plugin](https://github.com/chrisvfritz/prerender-spa-plugin#what-is-prerendering) 。简而言之，这个插件启动一个无头浏览器，加载你的应用程序的路线，并将结果保存到静态 HTML 文件中。这意味着我的所有应用程序将保持静态，因此我们可以轻松地托管它。

## 托管

现在我们需要托管我们的静态网站，我想你开始明白我喜欢 firebase😉。

[Firebase hosting](https://firebase.google.com/docs/hosting/) 将帮助我们快速将 PWA 部署到 CDN，通过安全连接提供我们的内容(HTTPS 需要在生产中使用服务人员)。

## 持续集成/交付

酷！我们快完成了。但是我们很懒，有些部分我们可以自动化。

CircleCI 是一个很棒的平台，它将帮助我们运行测试，使用我们的质量工具检查代码，并在我们将代码推送到远程存储库时部署到 firebase 主机。这是我们想要的工作流程:

1.  安装我们的项目依赖项。
2.  安装依赖项步骤完成后，我们将并行执行以下操作
3.  乔布斯:运行 e2e 测试，运行单元测试，运行 linter 和检查我们的代码格式与漂亮。
4.  如果所有以前的工作都成功结束，我们将建立我们的应用程序(预渲染)。
5.  一旦构建步骤结束，我们将使用 BundleSize 检查 javascript 包的大小。
6.  最后，如果目标分支是发布版本，我们将部署到 firebase 主机。

[这里的](https://github.com/kefranabg/bento-starter/blob/master/.circleci/config.yml)是对应上述工作流程的 CircleCI 配置文件。

## 结论

有了这种全栈解决方案，我们唯一需要关心的是编写 PWA 代码，这正是我们所寻求的。总结一下:

*   Firebase 平台节省了我们大量的时间，并照顾 PWA 托管。
*   前端质量工具帮助我们保持代码干净、快速和高性能。请记住，这在 PWA 环境中非常重要。
*   一个好的 CI/CD 配置允许对代码质量和应用程序部署的空闲时间保持永久的控制。
*   考虑在 SSR 上使用预渲染，因为这样更容易实现。

如前所述，如果您对构建 PWA 感兴趣并且不知道从哪里开始，或者如果您只是想要一个我在本文中介绍的堆栈的具体示例，请查看这个 github 库。

如果您有任何问题，请随时通过 [twitter](https://twitter.com/FranckAbgrall) 联系我！
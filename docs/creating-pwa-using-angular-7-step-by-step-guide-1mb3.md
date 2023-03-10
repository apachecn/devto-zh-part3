# 使用有角度的 7 步指南创建 PWA

> 原文：<https://dev.to/ronakpatel70/creating-pwa-using-angular-7-step-by-step-guide-1mb3>

很长一段时间以来，渐进式 Web 应用程序(PWA)一直是技术领域讨论最多的话题，因为它见证了前端世界的巨大兴趣。人们正在探索它在商业上的潜力，并且大多数人正在使用 PWA 概念开发应用程序。

[![PWA With Angular](img/754fd611176161285dbff999b99f8f39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hmiTWc_U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/PWA-With-Angular.png)

投资者对 PWA 申请比本土申请更感兴趣的原因有很多。受欢迎程度越来越高，微软正在推广这一概念，甚至允许开发者为 Windows Store 创建 PWA 支持的应用程序。

在本文中，我们将关注两个方面——用户方面和技术方面，并帮助您发现使用 Angular 创建 PWA 是多么有效和容易。由于 Angular v7 是最新版本，所以我们将其命名为 v7，尽管相同的概念(不能排除一点点差异)也可以应用于 v5 和 v6。

## PWA 是如何产生的？

科技巨头谷歌的设计师兼工程师弗朗西斯·贝里曼和亚历克斯·罗素在 2015 年提出了这项创新。用户可以利用从现代网络浏览器、服务人员和网络应用清单获得支持的新功能。最初，它可以得到应有的关注，但几年后，它成为争论的主要话题之一。在接下来的段落中，我们将讨论为什么 pwa 对您的业务如此重要。

## 为什么是渐进式网络应用？

在我们讨论 PWA 的原因之前，我们需要了解一些关于智能手机使用以及人们如何与原生应用交互的令人兴奋的情况。

随着智能手机用户的飞速增长，他们对服务提供商的期望也在增加。有时，你会不知所措，不知道你的用户有什么期望。

***<u>这就是服务提供商面临的挑战...</u>***

根据调查，用户一般不会下载他们不经常使用的应用程序。如果你更深入地观察一下，就会发现除了预装的应用程序(由服务提供商提供)，用户下载第三方应用程序的空间非常有限(10 个应用程序)。更令人惊讶的事实是，space 被包括 WhatsApp、Tinder、脸书、Skype、YouTube、谷歌 Chrome、Gmail、Instagram、Spotify、Snapchat 等在内的前 10 大最受欢迎的应用占据。

在如此复杂的情况下，成千上万的应用程序(其中许多实际上很有吸引力)存在于不同的平台上，在用户的智能手机屏幕上为你的应用程序找到一个位置是一项艰巨的任务。

## 原生应用的问题

所有使用智能手机的人都利用它们来完成多项任务，但当下载应用程序时，他们会考虑特定的问题。比如说；

*   如果应用程序值得下载
*   如果设备中有足够的空间
*   如果有比下载应用程序更好的选择

此外，你可能会意识到，用户甚至不会使用他们设备上安装的大多数应用程序。这些是投资者探索 PWA 的潜力并在他们的网站中实施它的一些原因。

## 那么，PWA 到底是什么？

PWAs 是 web 应用程序，具有一组提供类似应用程序的用户体验的功能。它们非常类似于 web 应用程序，尽管它们是通过 URL 通过 web 服务器部署和访问的，从而节省了大量的存储数据和驱动器空间。在下一段中，我们将讨论 PWA 的特性以及使用 PWA 会带来哪些好处。

## PWA 的特点

PWA 的概念围绕以下内容展开:

### 1.进步分子

PWA 应该与所有浏览器兼容，如 Chrome、Safari、Firefox 和所有设备。

### 2.应答的

它应该具有响应性，以便它可以适合所有屏幕，与它正在使用的设备(如移动设备、标签、fab)无关。

### 3.连通性独立

即使在低质量网络(如 2G、3G、4G 等)上，它也应该提供丰富的用户体验。)并离线工作，以防没有稳定的连接。

### 4.类似 App 的体验

虽然它根本不是一个本地应用，但它应该提供类似应用的体验，用户应该从浏览器添加 PWA。

### 5.新鲜的

它应该利用服务人员自动更新，因此用户不需要更频繁地更新它。

### 6.安全的

它应该通过 HTTP 提供，这样用户就可以像在本地应用程序中一样安全地使用它。

### 7.发现的

利用 W3C 清单，PWA 应该可以被搜索引擎发现，并被识别为“应用程序”

### 8.推送通知

它应该吸引用户向他们发送推送通知，包括更新和提醒。

### 9.可安装

应该允许用户将网站作为应用程序安装，如果可能的话，应该弹出提示框提示用户**“添加到主屏幕”**

### 10.可链接

它应该是共享的，用户或接收者不应该要求安装它。

## PWA 的好处

看到渐进式 Web 应用程序越来越受欢迎，显然它将在很大程度上影响原生应用程序。然而，我不能就此打赌，尽管一段时间后事情会完全不同，这将被视为商业方面的重要因素。它有很多好处，从长远来看，将有助于公司获得好处。

### 成本效率

与原生应用相比，PWA 非常具有成本效益，对于应用发行商来说，最大的优势是在应用开发和维护方面节省了成本。因为假设制作一个网站比制作一个 Android 应用要快很多。

### 跨平台

PWA 是通用的，利用任何技术、框架或库制作的应用程序都可以跨所有平台运行。它让开发人员可以自由地创建一个跨平台的应用程序，而不是为不同的平台开发不同版本的应用程序。

要了解更多信息，请访问文章"[PWA–渐进式网络应用:你需要知道的一切](https://aglowiditsolutions.com/blog/progressive-web-apps-development/)"

## 角度 PWA 的设置过程

要了解如何使用 Angular 开发 PWA 应用程序，您需要创建一个使用 JSON API 的前端 web 应用程序。现在，您需要利用 Angular HttpClient 模块向 JS GitHub 存储库中生成的 JSON API 发送 HTTP 请求。在这里，API 对于从 PWA 应用程序的网站获取数据是必不可少的。现在，使用 Angular Material Package 的材料设计，您需要构建 PWA 友好的 UI。

## 使用 Angular v7 设置项目的分步指南

**拟采用的技术；**

*   Angular CLI v7
*   Node.js 8.9 以上版本
*   npm v5.4.2

**添加 Angular CLI 需要 Node.js(最好是最新推荐版本)**

`$ node -v`

[![Checking Node Version](img/7a1f045235c950a07a211ab7dd6ea206.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rV6TYvAl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/Checking-Node-Version.png)

使用以下代码添加/安装 Angular CLI

`$ npm install -g @angular/cli`

[![Add / Install Angular cli](img/6d386f55eff59fb60639421389279103.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IZnCiSJU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/angular-cli.png)

**IMP 注意:**根据 npm 的配置，你可能需要使用 *sudo*

现在使用以下命令生成项目

`$ ng new pwa-example`

一旦你完成了这些过程，基础文件将会自动创建，它看起来像这样；

[![Create Project](img/9ed88b61aabaf3f9c70a9adbc5775b41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e8Y8kE1_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/create-project.png)

使用下面的命令移动到刚刚创建的新投影中

`cd pwa-example`

并使用以下代码添加角度 PWA

`$ ng add @angular/pwa`

[![Add PWA](img/e4cd264ec125e4fe502370028d2667a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7i0_Nn38--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/add-pwa.png)

使用此代码将导入并注册`"@angular/service-worker"`包。此外，它将执行一些其他任务，如

`"index.html"`文件将被更新并包含带有主题颜色属性的`"manifest.json,"` & meta 标签。”

包括一些符合 PWA 的“图标资产”

### **发射角 PWA 应用**

要启动 PWA 应用程序，您需要`"– ng serve"`命令，但由于“服务人员”的原因，这是不可能的您需要有一台服务器来启动应用程序。按照命令安装服务器。

`npm install -g http-server`

现在，您需要注册服务人员，这将在您拥有类似生产的环境时进行。因此，您需要使用特定的命令(如下所示)将应用程序构建为生产版本。

`ng build --prod`

[![build app](img/55e08fe87e3568ad2d39a1adb1c13bdc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kvAP-ydl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/build-app.png)

接下来，您需要使用，

`http-server -c-1 dist\pwa-example</code>`

[![start-http-server](img/469a3f9a5acd82a0790e7b591f3a9424.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H2FfbsU7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/start-http-server.png)

然后你可以输入 url 地址(比如 [http://localhost:8080](http://localhost:8080) )来检查你的项目是否注册正确。

[![Angular PWA Example](img/d7553c5e87ab4671f503fa9a7817bc1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IKw5O545--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/Angular-PWA-Example.png)

**注意:**你也可以检查它是否离线工作。

### **集成“添加到主屏幕”**

一旦你完成了检查过程，并确保应用程序正常工作，你需要集成功能，如添加“添加到主屏幕”

**要添加该功能，需要按照下面的两步作为给n；**

为此，您需要检查文件夹中的两个文件，一个是`"ngsw-config.json"`，`"manifest.json"`

有一个`"ngsw-config.json"`文件，指定 Angular service worker 应该缓存哪些文件和数据 URL，以及应该如何更新缓存的文件和数据。该文件将包括下图中提到的配置。

[![ngsw config](img/45318fa02156e10c6afe328eecd0240e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--opGDmnD4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/ngsw-config.png)

您需要将`"manifest.json"`文件添加到项目中

[![manifest json](img/90d3e07a347a6dee5c6847936d80de25.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kJB216r3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/manifest-json.png)

使用 Web 应用程序清单，您可以添加的基本和必要信息，对于配置，您可以使用上面给出的图像示例。如图所示，manifest.json 文件将包括类似于" name "、" short_name "、" theme_color "、" background_color "、" display "、" scope "、" start_url "和不同大小的" ico ns "的属性。

**现在添加`manifest.json`文件到`angular.json`和`index.html`**

这里是需要添加到`angular.json`中的`manifest.json`，您可以在资产部分找到。

看一下下图；

[![angular-json](img/39dcb9bf7950f4d73c9085a4e4325d6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4Obwj0Ma--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/angular-json.png)

现在在 head 部分添加一个到已创建的`index.html`文件的链接。

[![Index-html](img/82f1595217d00495638423fa18f234c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F_fhnB78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/Index-html.png)

**注意:**该功能将对 HTTPS 提供的应用程序有用。

你得到的结果如下

[![Final Output](img/e1d6e285e79ce6c5a69843329c6e768d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ie9D6YPF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/Final-Output.png)

做得好！你开发了 PWA！

现在，你已经创建了你的第一个 PWA 网站，你需要使用谷歌工具中的“灯塔”来测试它。

[![Angular PWA Lighthouse](img/43de43345bf0bbd1264239461f97cfc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JkGYEIX6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aglowiditsolutions.com/wp-content/uploads/2019/05/Angular-PWA-Lighthouse.png)

现在让人们得到 PWA 的好处，帮助你的事业获得新的高度。

**结论**
在这篇文章中，我们试图简化 PWA 开发，我们希望这篇教程对你有价值。此外，如果你想用 Angular 开发任何应用程序，你可以用非常划算的价格雇佣 Angular 的开发者 T4。您可以通过评论分享您的想法，并让我们知道我们在下一篇文章中如何做得更好。快乐阅读！
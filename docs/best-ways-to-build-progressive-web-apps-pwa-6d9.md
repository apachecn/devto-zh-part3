# 构建渐进式 Web 应用程序(PWA)的最佳方式

> 原文：<https://dev.to/realabbas/best-ways-to-build-progressive-web-apps-pwa-6d9>

**[渐进式网络应用](https://xtriba.com)** 又名 **PWA** 是网络开发行业的最新趋势。
[![PWA](img/ee695ad8a676fca7dc1c9da44d2dd5e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dCvS0dsb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qph.fs.quoracdn.net/main-qimg-e9e0f8bfcdecc04f83c5d3aa7abfa612) 
它们变得日益流行，因为它们使得用 JAVASCRIPT、HTML 和 CSS 构建 web 应用程序变得容易，并且给基于 web 的应用程序以原生的用户体验。
我将讨论在 PWAs 开发过程中应该记住的所有基本要点和高级要点。
首先，进步网站和传统网站之间有一些关键的区别。渐进式 web 应用程序必须能够做到以下几点:

*   使用渐进式代码库与大多数浏览器和设备(移动和桌面)一起工作。
*   使用响应式设计适合所有屏幕和外形。
*   利用服务人员实现离线连接([https://techbeacon . com/offline-first-web-mobile-apps-top-frameworks-components](https://techbeacon.com/offline-first-web-mobile-apps-top-frameworks-components))(需要 https)
*   提供类似应用程序的体验，利用重新参与工具，如推送通知
*   利用 web 应用清单来描述所使用的资源。目前，JAVASCRIPT 及其 UI 框架是 PWAs 流行背后的主要参与者。**使用 React 构建渐进式网络应用**
*   它由脸书管理和支持，该公司在其 [mrroom 网站](https://www.mrroom.in)上使用该框架，从而证明该框架经过了每日 11.8 亿用户的严格测试([https://s21 . q4c dn . com/399680738/files/doc _ presentations/f b-Q316-Earnings-slides . pdf](https://s21.q4cdn.com/399680738/files/doc_presentations/FB-Q316-Earnings-Slides.pdf))。
*   React 是 React Native([https://facebook.github.io/react-native/](https://facebook.github.io/react-native/))的基础，可以让你轻松地将 React 构建的应用移植到原生应用。用户界面由组件组成。这些组件可以使用 Node.js 在浏览器和服务器上进行渲染，也可以使用 React Native 在应用程序内部进行渲染。这样，管理需要交付给许多不同操作系统、浏览器和设备的应用程序的挑战就迎刃而解了。**入门:使用聚合物模板**
*   聚合物模板([https://github . com/start Polymer/progressive-we b-app-template](https://github.com/StartPolymer/progressive-web-app-template))使用 PRPL 模式来优化应用向设备的交付。使用它来:
*   推动初始路线的关键资源
*   渲染初始路线
*   预缓存剩余的路线
*   延迟加载并按需创建剩余路线**使用 Webpack 管理依赖关系**最终测试:**使用 Lighthouse 检查您的代码**第一部分“渐进式 Web 应用程序”返回以下分析:
*   应用程序可以加载离线/不稳定的连接
*   页面加载性能很快
*   站点逐渐增强
*   网络连接安全
*   可以提示用户添加到主屏幕
*   已安装的 web 应用程序将启动自定义闪屏
*   地址栏匹配品牌颜色
*   设计是移动友好的每一个子部分，然后打破了具体的技术，你可以添加或修改，以提高你的渐进式网络应用程序的性能。例如，“应用程序可以加载离线/易断连接”的报告提供了以下详细信息:
*   拥有注册的服务人员(并描述什么是服务人员)
*   离线时，URL 响应 200。第二部分“最佳实践”提供了以下详细信息:
*   使用现代离线功能
*   使用现代协议
*   使用现代 CSS
*   使用现代 JavaScript 特性
*   避免损害用户体验的 API
*   可访问性第三部分“性能”列出了从一个响应式 web 应用程序加载一个页面需要多长时间，以及加载页面中的每个项目需要多长时间。它还包括深入研究导致速度变慢的任何问题的工具。最后一部分，“更好的东西”，检查最新的 HTML5/JS 特性([http://caniuse.com/](http://caniuse.com/))是否在使用中。Lighthouse 的目标是测试你的站点，并给你解决问题的方向。**额外工具**以上工具是我们团队审查的众多不同渐进式网络应用工具中的精选。除了上述工具之外(或作为其替代),您还可以考虑一些额外的工具，包括:
*   [AMP](https://www.ampproject.org/) (加速移动页面)——引导图像/JS 压缩以加速您的网站的工具。谷歌大力支持 AMP(甚至在搜索结果中突出 AMP 支持的移动网站)。谷歌在 AMP 中的元素上支持 AMP 缓存(【https://developers.google.com/amp/cache/】)，开发者可以免费使用。
*   [AngularJS](https://angularjs.org/) —Google 的前端 JavaScript 框架比 React 更重量级，但也是 enterprise Java 和中的高度首选([https://techbeacon . com/node/1563)。NET 开发人员](https://techbeacon.com/node/1563)among)
*   [Ionic 2](http://ionicframework.com/)—基于 AngularJS 2 的框架于 2017 年初发布，乍一看，它似乎是一个有吸引力的解决方案，可以在 2015 年发布的原始 Ionic 框架的成功基础上进行构建。对于从 Cordova/PhoneGap 转向现代响应式 web 应用解决方案的开发人员来说，Ionic 是一个很好的平台。底线是渐进式网络应用的工具仍在快速成熟和变化。许多领先的浏览器供应商，特别是苹果(Safari 和移动 Safari)、谷歌(Chrome)和微软(Edge)每 1-3 个月提供一次定期更新。我们总是密切关注新的解决方案，并评估我们应该如何或是否应该在我们的项目中使用它们。最终目标是使用网络技术增加类似应用的体验。采用渐进式网络应用程序的流行网站。随便看看。

[![PWA](img/e3350ae3717ac7ecd9c17583b0f4a79a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DMTBpK2r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qph.fs.quoracdn.net/main-qimg-6f9b00ca6fc1ab4fa761934c16a0faaa)

***奖金:***Udemy 上的 PWA 最佳课程[渐进式网络应用(PWA)——完全指南](https://www.udemy.com/progressive-web-app-pwa-the-complete-guide/)
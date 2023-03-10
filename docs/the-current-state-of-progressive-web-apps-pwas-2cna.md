# 渐进式网络应用(PWAs)的现状

> 原文：<https://dev.to/browserlondon/the-current-state-of-progressive-web-apps-pwas-2cna>

长期以来，网络应用一直处于本地应用的次要地位。虽然移动应用受益于对设备 API 的完全访问、敏捷的性能以及对离线内容和推送通知等功能的支持，但网络的无状态和浏览器沙盒性质经常将基于网络的应用降级为第二选择。

然而，最近几年我们已经看到潮流开始转向，特别是随着[客户端渲染](https://www.browserlondon.com/blog/2015/08/26/performance-enhancing-the-web/)的流行以及对诸如后台抓取之类的本地特性的操作系统级支持的增加。这种新型网站经常被称为“PWA”(渐进式网络应用)，但这实际上意味着什么呢？

## 定义渐进式网络应用

最初由夫妻二人组弗朗西丝·贝里曼和亚历克斯·罗素创造(现在由谷歌倡导)，PWAs 最初被定义为:

*   **渐进式**–适用于所有用户，无论他们选择何种浏览器，因为他们以[渐进式增强](https://www.browserlondon.com/blog/2017/03/14/performance-benefits-progressive-enhancement/)为核心宗旨。
*   **响应迅速**–适合任何外形规格:台式机、手机、平板电脑或未来的任何产品。
*   **独立连接**–增强了服务人员离线或在低质量网络上工作的能力。
*   **类似应用程序的**——对用户来说就像一个应用程序，具有[式的交互](https://www.browserlondon.com/blog/2016/10/03/how-we-improve-user-experience-with-transitions/)和导航，因为它是建立在应用程序外壳模型上的。
*   **新鲜**–由于服务人员的更新流程，始终保持最新。
*   **安全**–通过 HTTPS 提供，以防止窥探并确保内容未被篡改。
*   **可发现的**–由于 W3C 清单和服务人员注册范围允许搜索引擎找到它们，因此可被识别为“应用程序”。
*   **可重新参与**–通过推送通知等功能轻松重新参与。
*   **可安装的**–允许用户在主屏幕上“保留”他们认为最有用的应用程序，而没有应用程序商店的麻烦。
*   **可链接**–通过 URL 轻松分享，无需复杂安装。

[![A screenshot of the original Google progressive web app page](img/b19f4acee648573d782e5d17a3c3883b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nOZjX0iX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/03/pwa-google-definition-1024x576.jpg)

然而，为了让你的应用程序“可安装”(因此是 PWA)，当前的基准技术要求仅仅是:

*   在 HTTPS 服役。
*   包括一个填充的 JSON 清单，其中包含一些关于站点的元数据。
*   包括一个服务工作器(位于客户机和服务器之间的后台脚本，能够拦截请求)

正是这最后的规定，即服务人员，实现了类似应用程序的推送通知、离线支持和后台更新。然而，至关重要的是，满足 PWA 技术要求所需要的只是服务人员在场——实际上不需要*发挥作用或实施任何这些功能。*

除此之外，大多数现代网站都已经提供了安全的服务，我相信你可以看到，用最少的努力就能满足不断进步的 web 应用程序的要求。不过，请注意，这并不一定意味着一个给定的网站符合 PWA 名称(和原始定义)所暗示的“类似应用”的品质。

## 定义类 app

当人们想到“类似应用程序”的网站时，他们通常指的是 SPA(单页应用程序)。不是在服务器上为每个请求动态构建站点，而是在包含应用程序的捆绑包旁边为所有访问者提供相同的(空)页面。

在许多方面，这与传统网站相比，与原生应用程序的行为更相似——应用程序的外壳被下载到客户端(并直接在客户端上运行),然后从 API 动态获取内容更新。由于导航和交互发生在设备上(而不是需要往返服务器)，用户交互感觉“爽快”和即时。这就是贝里曼和拉塞尔提到的“ [app-shell](https://developers.google.com/web/fundamentals/architecture/app-shell) ”模型。

[![Screenshot of the app-shell model in use on Flipkart](img/8430c4384e3a2d28b21afd6714577b4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IyYz6dHE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/03/app-shell-model-1024x576.jpg)

然而，如果你的应用程序或网站不是从一开始就以这种方式构建的，那么采用这种方式就需要重新考虑产品的架构，这通常是不可行的。传统的服务器提供的网站仍然主导着网络，并且可能在未来很长一段时间内都是如此，这可能就是为什么(正如[杰森·格里斯比指出的](https://www.slideshare.net/grigs/progressive-web-app-challenges))谷歌对 PWA 的定义已经变得更加[模糊不清，三连胜](https://web.archive.org/web/20151218101024/https://developers.google.com/web/progressive-web-apps/)“可靠”、“快速”和“吸引人”。即使是传统网站，也有可能在谷歌自己的[灯塔](https://developers.google.com/web/tools/lighthouse/)等工具上的渐进式网络应用类别中获得高分。

虽然所有站点都可以(并且可能应该)实现基线 PWA 要求——没有站点*不会*受益于基本的离线支持、可发现性和 HTTPS——但该术语的定义不再区分客户端和服务器呈现的站点。因此，对于不同的群体来说，它意味着不同的东西，围绕它的期望(以及营销或销售)变得有些模糊，这也许并不令人惊讶。

## 不良 PWA 行为

不幸的是，想要创造一种更本土风格的体验的愿望可能会导致比浏览器内移动网站提供的更糟糕的 PWA 体验。

首先，app-manifest 允许(尽管不要求)开发者隐藏浏览器“chrome ”,包含共享表和地址栏。通过这样做，我们立即消除了最初 PWA 愿景的两个核心租户:共享能力和链接能力。“刷新”按钮也消失了，这意味着在出现错误时无法重新加载页面(除非重启应用程序)。

[![Screenshot demonstrating the difference in window chrome between an installed and uninstalled PWA](img/252e96620a8e16375da5695fd03c53b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n-kDCf_Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/03/pwa-installed-vs-uninstalled-1024x576.jpg)

更糟糕的是，在 iOS 上，应用程序状态目前是不持久的。这意味着每次 PWA 失去焦点时(例如，您切换到不同的应用程序)，它都会重新启动。可以手动保存应用程序状态/当前页面，并在加载时恢复它，但这必须作为自定义功能来编写，并且每次切换焦点时都会产生启动时间。

[https://player.vimeo.com/video/330463094](https://player.vimeo.com/video/330463094)

虽然在 iOS 上还处于早期阶段(对服务人员的支持只是最近才在 11.3 版本中引入)，但在撰写本文时，出于这个原因，很难推荐在平台上“安装”一个站点作为 PWA。

## 向前看

贝里曼和拉塞尔定义的核心租户有很多值得钦佩的地方，然而，鉴于其现在相当脆弱的定义，我们在采用渐进式网络应用术语时必须小心谨慎。贝里曼自己说:

> 这个名字是给你的老板、你的投资者起的……这是营销，就像 HTML5 和实际的 HTML 没有什么关系一样。“PWAs 只是一堆有着崭新品牌名称的技术，让开放网络持续更长时间，帮助它与专有的、封闭的网络竞争，直到下一件事(希望是下一件事)出现，让它保持敏锐和相关。”

当然，随着 iOS 对基于渐进式网络应用的网站支持的改善，我们将继续受益于 PWA 唯一真正的*功能性*要求:服务人员。后台更新、推送通知和完全离线支持都是很好的补充，将大大消除对传统跨平台“原生”应用的需求。

与此同时，我们应该像以往一样，根据什么对用户或客户最好来决定在特定情况下使用什么技术，而不是根据什么能获得最多的博客帖子。

* * *

帖子[进步网络应用的现状](https://www.browserlondon.com/blog/2019/04/15/current-state-progressive-web-app-pwa/)首先出现在[浏览器伦敦](https://www.browserlondon.com)上。
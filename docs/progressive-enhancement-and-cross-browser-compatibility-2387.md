# 渐进增强和跨浏览器兼容性

> 原文：<https://dev.to/akshaypai/progressive-enhancement-and-cross-browser-compatibility-2387>

随着越来越多的数据和统计数据向开发者和设计者公开，我们必须专注于利用这些数据和统计数据来构建一个功能性网站。渐进增强基于这一前提，它使开发人员能够优先考虑对最终用户最重要的概念和特性。

我们可以考虑类似于产品实现的渐进式增强策略。它从构建一个最小可行的产品开始，这个产品是健壮的，并且具有产品的基本核心特征。MVP 完成后，我们将逐步在它的基础上添加更多的特性，并对其功能进行彻底的分析。

### 但是为什么这真的有必要呢？

这个问题的答案源于网络浏览器缺乏标准。大多数较新的网站都被鼓励使用现代浏览器的功能。然而，假设你所有的客户和未来潜在的潜在客户都使用现代的网络浏览器将是一个公平的失算！访问者也可以从传统的浏览器登陆你的网站，这就是为什么跨浏览器兼容性问题需要解决的原因。

渐进式增强策略在此介入，以提供[跨浏览器兼容性](https://www.lambdatest.com/blog/what-is-cross-browser-compatibility-and-why-we-need-it/?utm_source=dev&utm_medium=Blog&utm_campaign=ak-13-230419eu&utm_term=OrganicPosting)。它确保每个人都可以访问网页的核心元素，不管他们使用什么浏览器。我们可以建立网站，牢记可能出现的问题，并以尽量减少这些问题的方式发展。

在本文中，我们将探讨渐进式改进是如何工作的，以及如何用它来解决网站目前面临的跨浏览器兼容性的主要问题。

## 什么是渐进式增强？

渐进式增强是一种创建跨浏览器兼容的 web 设计的技术，其中开发时的最高优先级是保持核心网页内容，而其他复杂的附加组件和功能则保持次要地位。

考虑到你正在建造装饰墙。在所有情况下，最基本的要求是首先建墙并粉刷。

现在你可以添加基础漆给它一些颜色。一旦颜色完成，你最好与你的客户讨论想法，并在墙上画出设计。最后，你会添加壁挂，以提高其优雅。

从这个类比中，你可以很容易地推断出使用 HTML 来构建墙或基础。CSS 就像墙上的画。与客户讨论，选择壁挂和其他装饰项目作为点睛之笔，将是 Javascript 的工作。

在使用渐进增强技术进行跨浏览器兼容的网页设计时，主要的重点是使用最少的网页设计技术来构建网站，以提供功能强大的结果。

一旦确保网站足够强大，能够以高功能准确性工作，那么，其他附加功能将被考虑在内。

渐进式增强技术更强调网站的健壮性和功能性。因此，开发过程从一个简单的设计开始，逐渐过渡到一个添加了特性和增强的更复杂的设计。

这确保了网站的核心功能可以跨不同的网络浏览器访问，最大限度地减少了跨浏览器兼容性问题，主要是对功能的影响。因此，更好的跨浏览器兼容性非常重要。

## 渐进增强和优雅退化有什么不同？

另一种 web 开发技术是优雅的退化。在这种网页设计技术中，网站是按照最新版本的网页浏览器的高级用户界面(用户界面)、功能和增强功能全面设计的。

然后每走一步就慢慢往下走，到最后只保留核心功能。这样，对于渐进增强和适度退化，层的方向保持相反。

虽然这两种方法背后的主要思想是建立跨浏览器兼容性，即网站的功能在不同的浏览器上保持不变。渐进式增强方法的实施考虑到了浏览器的基本版本，另一方面，优雅的降级更侧重于 web 浏览器的高级版本，并慢慢降低增强以兼容较低版本。

此外，由于渐进式增强方法保留了所有版本中的核心内容，因此它为网站的 SEO(搜索引擎优化)提供了良好的结果，其中可以在所有版本中检测到关键字。这使得渐进式增强成为 web 开发中的首选技术。

## 渐进增强有哪些不同的层次？

正如我们在前面的章节中所看到的，渐进式改进是一种多层的 web 设计方法，其中，当我们从 web 浏览器的基本版本升级到高级版本时，每一步都会添加一些附加组件和功能，以避免跨浏览器的兼容性问题。

现在让我们讨论这些不同层次的渐进增强，以便更深入地理解这种方法如何有效地处理跨浏览器兼容性问题。

[![LAMBDATEST](img/4cdf65432aaa2a4b9389ee18bf158ffd.png)](https://www.lambdatest.com/?utm_source=dev&utm_medium=Blog&utm_campaign=ak-13-230419eu&utm_term=OrganicPosting)

### 1。内容层:丰富的语义 HTML 标记

[![HTML](img/e930e3247f06594d6cb85139ee99569b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8S42WGQb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/11/HTML.jpg)

这一层通过使用丰富的语义 HTML 标记来关注网页的核心内容。丰富的语义 HTML 标记确保了向最终用户传达所需含义和思想的适当结构化的内容。

HTML 标签如`nav`、`footer`、`article`、`aside`等。用于确保内容的良好定义的结构。这些标签被用来取代普通的通用块`div`和内嵌标签`span`，以实现一个干净的网页，符合不断发展的网页设计 HTML5 标准。

甚至以前使用的`object`标签也被新引入的和标签所取代，以获得丰富的语义 HTML 标记。

富语义 HTML 标记的主要目的是允许基于文本的、基于语音的、过时的和机器人式的用户代理能够勤奋地浏览网站内容。

### 2。表示层:用 CSS 设计网站样式

[![CSS](img/df850d5741f8f98cbbf0711356520a64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e4dYcF96--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/11/CSS.jpg)

随着带有语义 HTML 标记的 HTML5 的发展，面对 CSS3，网站样式也有了发展。HTML5 和 CSS3 很好地融合在一起，让网页变得更有吸引力，颜色、图像、背景、渐变、阴影、文本效果和许多元素增强了整体用户体验。

而有时 web 浏览器可能有它们自己的不同的元素集和媒体类型，它们以特定的方式定义以供它们在不同的情况下使用。

第二层(即表示层)背后的想法是使基于视觉的用户代理能够显示或改变网站内容的视觉表示。此外，这一层在通过多种方式增强用户体验(UX)方面起着重要作用。

### 3。脚本行为层:JavaScript

[![Javascript](img/87bdb9b32c33b674b83858768b84d660.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lyJjMWZi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2018/11/Javascript.jpg)

这一层将以上各层累积在一起，构建出一个具有战略意义的智能网页。它更关注 web 设计的性能，使用不引人注目的 JavaScript 或 jQuery。

JavaScript 通过将这些特性作为独立的模块来维护，从而实现了这些特性的健壮性。这允许这些特性独立可用，尽管由于浏览器兼容性问题，一些脚本无法成功运行。

这一层并不限制内容的可访问性，相反，它为更广泛的用户丰富了内容的可用性。

JavaScript 使已经能够使用它的用户代理能够为最终用户提供最大的可用性和最佳的用户体验。

上面解释的使用渐进增强技术的网站开发的 3 个层次肯定有助于实现跨浏览器兼容性。因为渐进式增强侧重于向最终用户提供最低限度的高质量功能，并为使用较高版本 web 浏览器的用户提供附加增强。

使用渐进式改进作为 web 开发的一种方法，不仅有助于通过迎合最终用户的更高功能来服务于网站的目的，而且有助于通过强调核心内容来提高网站的 SEO。

## 跨浏览器测试一个渐进增强的共同点&优雅退化

不管你从优雅的退化和渐进的增强中选择哪一种，适应这两种 web 开发方法都有一个共同的目标，即跨浏览器兼容性。

为了实现共同的目标，详细的跨浏览器测试是至关重要的，以确保您的网站或 web 应用程序在网站上线之前可以无缝地跨现代和传统浏览器运行。

LambaTest 免费提供正确的[跨浏览器测试解决方案](https://www.lambdatest.com/feature?utm_source=dev&utm_medium=Blog&utm_campaign=ak-13-230419eu&utm_term=OrganicPosting)，你可以在[2000 多个桌面和移动浏览器中检查你的网站或网络应用的跨浏览器兼容性](https://www.lambdatest.com/list-of-browsers?utm_source=dev&utm_medium=Blog&utm_campaign=ak-13-230419eu&utm_term=OrganicPosting)。LambdaTest 提供了多种特性来加速跨浏览器测试过程。

1.  **实时测试–**您可以在云服务器上托管的虚拟机内，跨所有主流浏览器及其各种版本执行实时交互测试。您可以记录您的测试会话，捕捉运行中的虚拟机的屏幕截图，甚至可以使用 LambdaTest 集成将它推送到各种错误跟踪工具。
2.  **自动化屏幕截图测试—**一次性在各种浏览器上捕捉大量屏幕截图。
3.  **响应式测试—**在各种设备和屏幕尺寸上测试你的 RWD(响应式网页设计)。
4.  **Lambda Tunnel—**借助 SSH 连接安全地测试您本地托管的网站。

还有更多的[特性](https://www.lambdatest.com/feature?utm_source=dev&utm_medium=Blog&utm_campaign=ak-13-230419eu&utm_term=OrganicPosting)和[集成](https://www.lambdatest.com/integrations?utm_source=dev&utm_medium=Blog&utm_campaign=ak-13-230419eu&utm_term=OrganicPosting)被不断添加到他们的平台中。他们的免费增值计划为您提供终身访问他们所有高级功能的机会，每月使用量有限。你所需要做的就是注册并开始跨浏览器测试！

[![LAMBDATEST](img/4cdf65432aaa2a4b9389ee18bf158ffd.png)](https://www.lambdatest.com/?utm_source=dev&utm_medium=Blog&utm_campaign=ak-13-230419eu&utm_term=OrganicPosting)

**相关帖子:**

1.  [创建浏览器兼容 HTML 和 CSS 的完整指南](https://www.lambdatest.com/blog/complete-guide-on-creating-browser-compatible-html-and-css/?utm_source=dev&utm_medium=Blog&utm_campaign=ak-13-230419eu&utm_term=OrganicPosting)

2.  [自动化跨浏览器测试](https://www.lambdatest.com/blog/automated-cross-browser-testing/?utm_source=dev&utm_medium=Blog&utm_campaign=ak-13-230419eu&utm_term=OrganicPosting)

3.  [使用 LambdaTest](https://www.lambdatest.com/blog/performing-cross-browser-testing-with-lambdatest/?utm_source=dev&utm_medium=Blog&utm_campaign=ak-13-230419eu&utm_term=OrganicPosting) 执行跨浏览器测试
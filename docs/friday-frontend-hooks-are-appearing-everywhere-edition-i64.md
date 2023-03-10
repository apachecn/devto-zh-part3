# 星期五前端:钩子到处出现版

> 原文：<https://dev.to/kball/friday-frontend-hooks-are-appearing-everywhere-edition-i64>

四月快乐！本周的时事通讯包括几个非常有趣的例子，说明 React 钩子是如何开始变得突出的。该 API 已经在生产版本中发布了 2 个月，但是我们开始看到大量非常有趣的开源包提供了定制的钩子。

尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [打破 CSS 自定义属性出:root 也许是个好主意](https://css-tricks.com/breaking-css-custom-properties-out-of-root-might-be-a-good-idea/)

随着 CSS 属性变得越来越普遍，我们看到了一个又一个教程在`:root`级别定义它们。这很好，并且与 CSS 典型的全局性质(以及大多数人使用 SCSS 变量的方式)一致，但是看到一篇文章深入探讨我们可以实际确定 CSS 变量的范围并使用它来创建封装而没有任何花哨的 CSS-in-JS 甚至 BEM 样式的命名，这是很好的。

##### [在基于组件的 Web 应用程序中管理 Z 索引](https://www.smashingmagazine.com/2019/04/z-index-component-based-web-application/)

对 z 索引和堆栈上下文的深入解释，以及在基于组件的应用程序中防止 z 索引问题的可靠推荐方法。非常值得一读！

##### [如何在 CSS 中对齐事物](https://www.smashingmagazine.com/2019/03/css-alignment/)

这是一个很好的例子，展示了我们用 CSS 在页面上对齐的各种方法。不会对任何一种方法进行深入研究，但会强调不同的方法，何时使用它们，以及在为任何想要深入研究某一特定方法的人指出其他资源之前，一些常见的症结是什么。

##### [CSS 计数器指南](https://medium.com/@samanthaming/a-guide-to-css-counter-8e80039ac547)

CSS 计数器是一个非常奇怪的东西…它们是基于选择器的匹配而隐式递增的…这篇文章将会给你关于它们如何工作的所有信息，尽管我仍然不确定我是否理解你什么时候想要使用它们。

#### JavaScript

##### [你在大多数教程中找不到的 12 个 JavaScript 技巧](https://medium.com/@bretcameron/12-javascript-tricks-you-wont-find-in-most-tutorials-a9c9331f169d)

一套伟大的短食谱和技巧，用于编写更简洁和高性能的代码。其中一些我每天都在使用，但还有一些对我来说还是新的(按位“或”作为舍入的快捷方式是我从未见过的)。蒂尔。)

##### [什么是`this`？JavaScript 对象的内部工作原理](https://medium.com/javascript-scene/what-is-this-the-inner-workings-of-javascript-objects-d397bfa0708a)

一篇引人入胜的文章，让你探索自己真正理解了多少。当你有一点时间的时候，看看这个，因为如果你尝试这些练习，你会得到比通读更多的收获。

##### [你工具箱里应该有的 10 个 React 钩子](https://blog.bitsrc.io/10-react-custom-hooks-you-should-have-in-your-toolbox-aa27d3f5564d)

钩子真正“投入生产”只有几个月的时间，但是我们开始看到一个丰富的开源定制钩子生态系统的出现。本帖经过一组好看的。

##### [现代而干净的带钩路由](https://parastudios.de/modern-and-clean-routing-with-hooks/)

人们开始用钩子做的另一个非常有趣的例子。这一次，它们被用来为 React 应用程序实现一个超级简单的路由系统。

##### [免费 Nuxt.js 教程— Vue 精通课程](https://medium.com/vue-mastery/free-nuxt-js-tutorial-creating-an-app-1a531bc6045)

我构建的最后两个主要客户端项目，前端是用 Nuxt.js 构建的。这是一个构建在 Vue.js 之上的更高级别的框架，它使设置“通用 JavaScript”(具有客户端水合作用的 SSR)变得非常容易，还具有许多额外的出色功能。我强烈推荐探索 Nuxt，如果您还没有，这篇文章是一个不错的起点。

#### 其他牛逼

##### [晨杯编码](https://www.humanreadablemag.com/morningcupofcoding)

对于那些不能获得足够的学习和不把自己限制在前端的人，我强烈推荐这份时事通讯。Morning Cup of Coding 是一份时事通讯，结构与此类似，但涵盖了所有编程领域。由 Pek 策划，每个工作日发送，它被设计成你的晨读清单。

##### [14 个最好的 JavaScript API](https://www.creativebloq.com/features/best-javascript-apis)

这个标题有点误导，从广义上来说，这些实际上比 JavaScript 更多的浏览器 API(大多数在 Node 中没有任何意义)，但这仍然是一个超级酷的外观，展示了一组新的、在某些情况下仍在开发中的浏览器功能，我们可以开始在我们的网站和应用程序中使用它们。

##### [别用那么多 div 了！语义 HTML 简介](https://dev.to/kenbellows/stop-using-so-many-divs-an-intro-to-semantic-html-3i9i)

语义标签和 HTML 的优秀介绍。如果您的网站(或组件)看起来仍然像 div 汤，您一定要通读一遍。

##### [责任 JavaScript:第一部分](https://alistapart.com/article/responsible-javascript-part-1)

从另一个角度看框架优先开发实践的成本，以及开发人员以人机工程学为中心的 web 开发方法的缺点。有趣的阅读。

##### [如何使用 JSDelivr](https://zellwk.com/blog/jsdelivr/)

现代 web 开发经常抱怨的一个问题是它的构建和 CLI 密集程度。你需要理解 npm，建立系统，以及这么多的东西，只是为了建立一个简单的网络应用程序？事实上，对于很多事情，你不需要……而且你可以直接从免费的 cdn(比如 JSDelivr)加载大多数开源包。这篇文章解释了这一点以及如何去做。

##### [一个没有 Webpack 的未来](https://www.pikapkg.com/blog/pika-web-a-future-without-webpack)

一个挑衅性的标题，和一个有趣的想法。看看我们如何能够利用 JavaScript 现在具有本机模块的事实来摆脱捆绑器的要求，并且能够在没有捆绑器的情况下发布现代 web 应用程序(具有依赖性)。感兴趣的读者也可以看看作者就同一主题的采访。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
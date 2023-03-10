# 星期五前端:故障模式版

> 原文：<https://dev.to/kball/friday-frontend-failure-modes-edition-2p4n>

我希望你有一个伟大的假期周末和惊人的一周！除了感恩节庆祝活动，上周末是我的生日，所以周围有很多美食、朋友和家人。我喜欢度假归来——一周中我会更有活力。

本周的时事通讯强调了几种不同类型的失败——最大的失败是我们如何管理开源中的依赖性，这在事件流攻击中得到了体现。第二，一篇很棒的“假设”文章，着眼于一些真正疯狂的失败模式，开发人员已经忘记了 web 请求是不可靠的。

也有你的探索，教育和其他伟大的前端材料的常规组合。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [黑客攻击 CSS 编写模式](http://www.tipue.com/blog/css-writing-mode/)

超级有趣的探索，你可以通过修补`writing-mode` CSS 属性来创建什么，特别是当结合一些简单的 CSS 转换时。爱死了！

##### [分隔符内<选择>](https://codepen.io/tigt/post/separators-inside-the-select-element)

有趣的是，您可以使用这些选项在 select 标记中创建可视分隔符。有些奇怪，有些还行，但总的来说相当有限，说实话。任何时候最后的建议是“不要真正改变设计，我们不能建造这个”，你知道你已经遇到了一个边缘案例。我想知道这是否值得尝试让 WHATWG 注意到 HTML 的改进

##### [如何一劳永逸地理解 CSS 的立场绝对](https://medium.freecodecamp.org/how-to-understand-css-position-absolute-once-and-for-all-b71ca10cd3fd)

Position: absolute 是 CSS 的关键特性之一，只是有时候有用，但是当你需要它的时候，你真的需要它，理解它是有用的。如果你不小心，不理解它可能会让你损失 2000 行 javascript 代码！🤣🤣

##### [萨斯完全入门](https://blog.bitsrc.io/the-complete-beginners-guide-to-sass-ee8d5278f4c)

这是一个很好的介绍，涵盖了 Sass 的所有主要特性。如果出于某种原因，您仍然在没有 Sass 或其他预处理器的情况下编写 CSS 或者您刚刚开始使用 Sass/SCSS，还不太适应，那么这篇文章是一个很好的地方，可以让您了解 Sass 提供了什么。

##### [苹果手表上反应灵敏的图像](https://ericportis.com/posts/2018/respimg-apple-watch/)

在手表表面呈现网页似乎是一种荒谬的极端，但现在有了 Apple Watch 浏览器，这已经成为可能。这篇文章介绍了将响应能力延伸到极限所需的一些技术和注意事项。

#### JavaScript

##### [网络工作者 vs 服务工作者 vs 小工](https://bitsofco.de/web-workers-vs-service-workers-vs-worklets/)

非常好的分解了三种不同类型的“JavaScript 工作者”。您的通用 web 工作人员、浏览器内代理/缓存服务工作人员以及插入渲染引擎的 Houdini 是令人惊叹的工作人员。:)

##### [理解 JavaScript 中的记忆化以提高性能](https://blog.bitsrc.io/understanding-memoization-in-javascript-to-improve-performance-2763ab107092)

记忆化是一种超级强大的技术，这是一个关于它的很好的教程，正好使用 JavaScript。有趣的事实——作为一个没有计算机科学学位的人，很长一段时间以来，我都觉得自己在算法等理论方面是在追赶……但是一位同事指出，这个问题通常只是命名:恰当的例子是，[动态编程](https://en.wikipedia.org/wiki/Dynamic_programming)听起来非常奇特，但实际上是一次又一次的记忆化应用。

##### [JSX 是句法糖](https://fettblog.eu/jsx-syntactic-sugar/)

JSX 一开始是极端两极化的，至今仍有支持者和反对者。有一点非常有帮助，那就是意识到 JSX 不是模板，而是一种特定领域的语言，是一系列函数调用之上的语法糖。这篇文章深入探讨了这个问题，并向您展示了一个假想的 JSX 编译器是什么样子的。

##### [用 Jest 和酶深度检测反应成分](https://blog.bitsrc.io/how-to-test-react-components-with-jest-and-enzyme-in-depth-145fcd06b90)

在过去的几年中，测试前端组件已经变得非常容易，以至于没有理由不进行前端测试。我喜欢这篇文章的原因是，它不仅展示了编写测试的例子，而且讨论了如何决定测试什么以及以什么顺序测试的过程。

##### [来自离子的声音](https://blog.ionicframework.com/a-vue-from-ionic/)

这真的很有趣，有几个原因。首先，对于那些不知道的人来说，Ionic 是一个移动应用程序 UI 工具包&一组内置的服务和实用程序，旨在使使用 web 技术制作移动应用程序变得非常容易。它建立在 Cordova/phonegap 之上，具有一组易于使用的组件和可插拔服务。从历史上看，它只是有棱角的。此次发布是他们支持多框架的第一步(有趣的是，他们选择了 Vue 而不是 React 作为第二步)。这也是在 [nativescript-vue](https://nativescript-vue.org/) 之上的另一个选项，用于使用 vue 构建原生移动应用。非常非常有趣。

#### 其他牛气

##### [关于事件流事件的详细信息](https://blog.npmjs.org/post/180565383195/details-about-the-event-stream-incident)

一个非常聪明的社会工程攻击在本周被发现，这与今年早些时候臭名昭著的黑客博客“收获信用卡”的帖子相呼应。该攻击似乎特别针对 Copay crypto-wallet 应用程序，尽管在此之前，所有类型的库(包括所有主要的前端框架 CLI)都建议重新安装以避免受影响的依赖关系。即使你没有受到影响，也值得一读，因为它提出了许多关于我们如何管理开源依赖的非常重要的问题。

##### [如果呢？](https://csswizardry.com/2018/11/what-if/)

网络是一个疯狂的环境，在这里，事情随时都可能出错。有时请求会失败，或者异常缓慢，这意味着图像、javascript、CSS 或 ajax 调用可能会突然挂起。当这种情况发生时，你的网站或应用程序会发生什么变化？如果你没有想过，你可以把自己设计成一些非常病态的情况，正如这篇文章所强调的。

##### [前端开发者要管理加载体验](https://css-tricks.com/front-end-developers-have-to-manage-the-loading-experience/)

权力越大，责任越大。有了现代前端框架和工具，我们有能力从一个基本空白的页面启动一个网站，从各处加载各种类型的数据，并根据我们的需要进行渲染。但是伴随这种能力而来的是对这些中间状态的责任——当一切都是异步的时候，我们如何管理加载体验呢？

##### [渐进式网络应用的广泛指南](https://www.smashingmagazine.com/2018/11/guide-pwa-progressive-web-applications/)

我只是在想，我已经有一段时间没有看到一篇关于渐进式网络应用的深入文章了，而这个超高质量的深入指南出现在了 Smashing 杂志上。看看吧！

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
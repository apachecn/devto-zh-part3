# 星期五前端:综述版的开始

> 原文：<https://dev.to/kball/friday-frontend-beginning-of-the-roundups-edition-40ma>

我们肯定是在 12 月——你可以说出来，因为每个人都开始拿出他们的年终总结。

我们在本周的新闻简报中已经看到了一些，以及一些关于新的 Angular 和 React 功能的非常有趣的内容，关于 big Edge 新闻的综述，等等。

尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [编程 CSS](https://adactio.com/journal/14574)

看看你如何看待 CSS 在更传统的编程语言和概念的类似物。坦率地说，我认为最终我们需要承认 CSS 攻击的领域与那些语言不同，因此有不同的选择和思维模式，但是如果这有助于来自编程背景的人开始了解它，那就太好了！

##### [【CSS 对象模型(CSSOM)介绍与指南】](https://css-tricks.com/an-introduction-and-guide-to-the-css-object-model-cssom/)

我将把它放在时事通讯的 CSS 部分，但是其中的大部分代码实际上是 JavaScript。这是一个用 JavaScript 读取和操作样式的浏览器 API 的指南。这是一个有趣的视角，它揭示了什么是暴露的，如何暴露的，以及你如何能把它弄糟。:P

##### [一个 CSS 维恩图](http://adrianroselli.com/2018/12/a-css-venn-diagram.html)

很好的演示了如何只用 HTML 和 CSS 创建维恩图。利用 CSS 形状、自定义属性和 CSS 网格。

##### [CSS 文字阴影来自 CodePen](https://freebiesupply.com/blog/css-text-shadows/)

在灵感和例子阵营中，这是一系列利用 CSS 文本阴影创造不同效果的例子。每一个都在一个 codepen 中，所以你可以随心所欲地修改。

#### JavaScript

##### [2018 年回顾及 npm 对 2019 年的预测](https://blog.npmjs.org/post/180868064080/this-year-in-javascript-2018-in-review-and-npms)

年终回顾开始了，这是一个很好的看看！来自 npm 的一些有趣的数据点；我喜欢他们对跨框架“注册表份额”下载的看法；当我在尝试判断相对受欢迎程度之前挖掘这样的数据时，如何解释整体注册表增长一直是个问题。另外，如果你喜欢这些东西，你可能会对我和劳里·沃斯就网络的未来做的采访感兴趣。

##### [大 ol '球 o' javascript](http://bradfrost.com/blog/post/big-ol-ball-o-javascript/)

对一篇关于“全栈”开发趋势的挑战以及前端开发经常被贬低或至少不被重视的方式的帖子的深思熟虑的反应。值得一读，即使这对你或你的组织来说不是一个挑战。(也值得一读引发它的[原帖](http://www.heydonworks.com/article/reluctant-gatekeeping-the-problem-with-full-stack)

##### [什么是影子 DOM？](https://bitsofco.de/what-is-the-shadow-dom/)

这完全让我大吃一惊。我不知道有一个特定的 API 来操作影子 DOM，尽管稍微看了一下就发现这是 web 组件的 4 个相关规范之一。目前的世界状态还很早，但我可以很容易地想象类似声明式 react/vue 的框架，它们被设计用于显式创建 shadow-dom 组件，然后为我们提供真正的可重用组件，可以嵌入到任何网站或应用程序中。超级爽！

##### [为什么 React 钩子依赖调用顺序？](https://overreacted.io/why-do-hooks-rely-on-call-order/)

深入探究 React 的新 Hooks API 背后的思想。由总是深思熟虑的丹·阿布拉莫夫撰写，这本书介绍了许多其他提议的备选方案，以及那些导致团队选择 Hooks 方法的缺点。

##### [元编程，高阶组件和有角藤的混合](https://blog.nrwl.io/metaprogramming-higher-order-components-and-mixins-with-angular-ivy-75748fcbc310)

我不会像 React 和 Vue 那样密切地关注角度的变化，但偶尔会有一些有趣的东西越过我的雷达，让我无法忽略。这一篇着眼于新的 Ivy 渲染器将如何实现更大的动态性，并实现更高阶的组件和混音等功能，这些功能在 Angular 中是很难实现的。

#### 其他牛逼

##### [2018 年前端开发回顾](https://dev.to/treyhuffine/a-recap-of-frontend-development-in-2018-p78)

今年前端世界发生的所有事情的另一个综述。事情发展得如此之快，让人感觉有些事情发生在几十年前，但事实上时间并不长。WebAssembly 1.0、TypeScript 的大规模增长和 babel 7.0 都在我的首选列表中，但这里还有更多。

##### [2019 年的 UX](https://trends.uxdesign.cc/)

另一篇综述/反思文章，这篇文章特别关注 UX 的设计。它很长，但是即使你不从事设计，我也建议你检查并思考其中的几点(你可以使用右边的点来导航——我花了一些时间才注意到)。首先，关于影响的要点(“设计不是拯救世界”)强调了科技公司是如何变得越来越强大，以及这如何影响我们在这个行业中的道德责任。以下关于“行为”的要点审视了科技行业对敬业度的痴迷，以及它可能带来的一些负面后果。花点时间读读这些，并思考一下——我们有幸在这个能产生如此影响的行业工作，但伴随着巨大的权力而来的是巨大的责任。

##### [原型和生产](https://adactio.com/journal/14562)

透视创建产品代码和原型代码之间的差异。我认为对话比这更微妙——产品质量的构成可能会因你的产品和市场阶段而有很大不同——但这是对其中一些问题的一个很好的审视。

##### [浏览器多样性评论，关于边缘新闻](https://css-tricks.com/browser-diversity-commentary-regarding-the-edge-news/)

最近有消息称，微软放弃了开发独立浏览器引擎的努力，将在 Chromium 的基础上开发下一个版本的 Edge。对我来说，这真的是生态系统的短期收益和长期损失。

##### [DOM 中的“D”](https://www.24a11y.com/2018/the-d-in-the-dom/)

看看 DOM 作为“文档”的概念，以及它对我们如何编写 HTML 的影响。不管我们的 HTML 是手动编写的，在服务器上生成的，还是通过 javascript 在客户端动态更新的，它仍然被浏览器解析为文档，这对它的行为有影响，特别是对于像屏幕阅读器这样的更程序化的阅读器。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
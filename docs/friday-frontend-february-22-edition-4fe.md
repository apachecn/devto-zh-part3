# 星期五前端:2 月 22 日版

> 原文：<https://dev.to/kball/friday-frontend-february-22-edition-4fe>

本周的版本中有许多好东西。这是我读过的最好的 BEM 文章之一，一篇令人难以置信的 CSS 艺术作品，一些可靠的 JavaScript 教程，以及一堆其他令人敬畏的 webby 东西。

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [使用 CSS 网格的正确方式](https://vgpena.github.io/using-css-grid-the-right-way/)

使用 CSS Grid 的一组有用的最佳实践。作者没有试图完全涵盖整个规范，而是强调了可用属性和用例的子集，这些属性和用例工作得很好。

##### [Mice 和 BEM:克服 CSS 组织的常见问题](https://seesparkbox.com/foundry/bem_css_organization)

这是我见过的关于 BEM 的最好的文章之一，假设你已经对 BEM 有点熟悉了。它没有介绍概念或给你介绍，而是解决一些常见的挑战，并为你提供思考如何构建和编写 CSS 的工具。干得好。

##### [如何@支持作品](https://css-tricks.com/how-supports-works/)

与 CSS 内置的其他形式的回退相比，看看@supports 标志的用例(或缺少用例)很有意思。

##### [CSS 变量+ calc() + rgb() =强制高对比度颜色](https://css-tricks.com/css-variables-calc-rgb-enforcing-high-contrast-colors/)

当我在 ZURB Foundation 工作时，我们经常遇到的一个挑战是，我们如何从单一颜色中自动生成匹配的颜色集(例如，带有边框和文本颜色的按钮)，同时又不违反视觉可访问性准则。这是个很难的问题！这是对使用现代 CSS 的现有工具的一种有趣的理解。

##### [纯 CSS 静物——水和柠檬](https://codepen.io/ivorjetski/pen/xMJoYO)

这真是令人难以置信。它看起来不仅仅像一件艺术品，更像一张怪异的照片。完全用 HTML 和 CSS。坦白地说，我已经盯着这个看了 10 分钟，用 DOM inspector 检查了它，但我仍然觉得我不知道他们是如何做到的。哇哦。

#### JavaScript

##### [创建自定义 React 钩子所需的一切](https://dev.to/progresstelerik/everything-you-need-to-create-a-custom-react-hook-47kl)

现在 Hooks 已经是公开的了，我希望越来越多的教程能够问世。这仍然是非常基础的，但是是一个很好的、简单的、相对独立的教程，可以帮助您入门。

##### [基于 React 的多步招式的魔力](https://css-tricks.com/the-magic-of-react-based-multi-step-forms/)

这是一个坚实的教程，展示了使用 React 实现多步表单(又名向导)是多么简单。虽然本教程使用 React，但这种通用方法将 will 转换为其他现代框架，如 Vue 或 Angular。

##### [使用 Vue.js](https://code.tutsplus.com/tutorials/turn-your-static-svg-drawings-into-interactive-widgets-and-infographics-with-vuejs--cms-32728) 将您的静态 SVG 绘图变成交互式小部件和信息图

现在这只是有趣！你们都知道我喜欢 SVG，使用像 Vue 这样的一流 JavaScript 框架来操作 SVG 将它带到了一个全新的水平。

##### [用 JavaScript 学习纯函数](https://medium.com/@cristisalcescu/learn-pure-functions-with-javascript-abf9b1c59246)

回到基础，看看纯函数的概念以及它们在 JavaScript 中是如何工作的。没有什么神奇的，但一个坚实的教程，然后一大堆链接更多的信息，如果你想更深入。

#### 提升链接

(注意:我把这个放在推广下，因为它不是我们的标准内容，它是一个附属链接，所以如果你以后从他那里买东西，我会受益，但这个特定的内容是 100%免费的，如果你对在线业务或营销感兴趣，我强烈推荐它)

##### [最后的机会:杰夫·沃克的产品发布公式](https://productlaunchformula.com/go?p=kball&w=alum_allaccess)

在过去的几周里，我一直在宣传这个产品，今天是它上市的最后一天。如果你一直没有看我在这里分享的视频…现在是时候了！杰夫·沃克刚刚拆除了隐藏这些视频的“防火墙”……而且观看这些视频不会花你一分钱。甚至没有你的电子邮件地址。直到今天午夜，星期五。我相信 Jeff 会喜欢你购买他的全套课程(我在 2016 年购买了，我很喜欢，如果你想告诉你更多，请给我发电子邮件)，但这些视频现在是免费的，它们真正向你展示了如何构建你的产品或业务发布。

#### 其他牛逼

##### [让你的网站页面在 1 分钟内瞬间完成，提高你 1%的转化率](https://instant.page/)

非常有趣的库(和概念)——当有人悬停在链接上时，自动预加载链接，让你的站点看起来反应更快。相当酷！

##### [网络蓝牙简介](https://www.smashingmagazine.com/2019/02/introduction-to-webbluetooth/)

在我主持的 JSConf Hawai'i 小组讨论中，我们讨论了很多关于网络平台可以给网络之外的各种环境带来的好处。比如沙箱、安全性和难以置信的开发简易性。问题是我们如何继续扩展网络平台，让它接触到所有现在需要本地连接的东西，这里有一篇关于 API 的文章，正是这样做的。了解如何直接从 PWA 中的 web 应用程序访问蓝牙。非常非常整洁！

##### [深入 GraphQL 查询](https://scotch.io/tutorials/deep-dive-into-graphql-queries)

GraphQL 在我今年要学的前 5 件事的列表中，本教程是一个不错的地方。查看 GraphQL 中构成查询的所有不同部分。

##### [第三方网站](https://github.com/patrickhulce/third-party-web)

对不同第三方 JavaScript 工具的相对成本的有趣观察。以多种方式分解事物，包括查看分析提供商、广告网络和社交解决方案。例如，我没有意识到 disqus 有多贵！我可能需要看看其他的评论解决方案。

##### [诡异的 A11y](http://adrianroselli.com/2019/02/uncanny-a11y.html)

聪明的双关语，看看我们如何经常试图过度解决可及性，结果却搬起石头砸自己的脚。它给出了一些常见失误的很好的例子，然后是一些具体的改进建议。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
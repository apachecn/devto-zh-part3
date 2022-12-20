# 星期五前端:转向设计版

> 原文：<https://dev.to/kball/friday-frontend-diversion-into-design-edition-29nj>

欢迎来到十二月！今年过得真快。希望这对你来说是个好机会！

本周的时事通讯有我们通常的 CSS、JavaScript 和 web 相关主题的组合，但这次我加入了一些设计观点，因为它们引起了我的兴趣。

生活在前端，即使是我们中最专注于工程的人也会发现自己陷入了一些设计工作，至少在交互方面，我们应该不断思考什么是好的设计。

祝你一周愉快！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [CSS 动画 101](https://github.com/cssanimation/css-animation-101)

一本关于 CSS 动画的优秀、全面的电子书，在 github 上免费发布。网页版也有很多 codepens 的链接，所以你可以点击试试。太好了！

##### [用 CSS 变量干切换:一次声明的区别](https://css-tricks.com/dry-switching-with-css-variables-the-difference-of-one-declaration/)

这是一个展示 CSS 变量威力的杰作。作者提出了一个对我来说非常突出的观点，这也是我想进一步思考的问题:CSS 变量允许样式变得更加逻辑化和数学化。然后，他们展示了广泛使用案例中引人入胜的例子。非常非常酷。

##### [CSS 形状简介](https://tympanus.net/codrops/2018/11/29/an-introduction-to-css-shapes/)

虽然我心中的工程师喜欢 CSS 网格和 CSS 变量等功能的强大，但我心中的创造性修补者绝对喜欢 CSS 形状。这些将会为大量更艺术化的布局和网络上的随机部分打开大门。

##### [2018 年 CSS 重置一览](https://bitsofco.de/a-look-at-css-resets-in-2018/)

CSS 重置在历史上是一种剥离特定于浏览器的样式表，并创建一个公共基础来开发设计的方法。这篇文章着眼于目前正在使用的重置，以及一个有趣的趋势，即使用“重置”来创建一个不那么纯粹的“重置”，但也是一个完整的“基础”，其中包含一组实用程序。

##### [创建 CSS 网格图库(带模糊效果和交互媒体查询)](https://webdesign.tutsplus.com/tutorials/create-a-css-grid-image-gallery-with-blur-effect-and-interaction-media-queries--cms-32287)

我对交互媒体查询知之甚少，但它们对本文探索的用例完全有意义:在悬停无意义的触摸屏设备上以不同的方式显示“悬停”内容。他们也有很好的浏览器支持。

#### JavaScript

##### [类型脚本:类型谓词](https://fettblog.eu/typescript-type-predicates/)

好了，从技术上来说，这是 TypeScript，不仅仅是 JavaScript，但这是一个非常酷的特性！对我来说，一个常见的挑战是如何处理接受多种类型并分解成不同情况的函数，而不放弃完全键入和调用未知类型的好处。类型谓词为您提供了一种方式，告诉编译器您动态地了解了哪些类型。

##### [React 16.x 路线图](https://reactjs.org/blog/2018/11/27/react-16-roadmap.html)

世界上最流行的前端框架正在开发一些非常酷的东西。到现在为止，你们可能都听说过钩子，但是我认为即将到来的并发模式至少同样有趣。能够在主线程之外呈现复杂的组件将是用户体验的一大进步，尤其是在 cpu 速度较慢的移动设备上。

##### [对比 2019 年 JavaScript HTTP 请求库](https://blog.bitsrc.io/comparing-http-request-libraries-for-2019-7bedb1089c83)

很好的浏览了 5 个库的利弊列表，可以用来做 HTTP 请求。我自己也一直在使用 axios……他们的缺点是“使用起来相当棘手”。我不确定我同意…当然，做复杂的事情是有一些复杂性的，但是基本的要求只是最基本的。

##### [Javascript 函数式编程的概念](https://medium.com/the-renaissance-developer/concepts-of-functional-programming-in-javascript-6bc84220d2aa)

用 JavaScript 进行函数式编程的扎实概述。我以前说过，现在还要再说一遍——学习函数式编程是我从事编码工作以来最有益的投资之一。即使我没有编写纯粹的函数式代码，我的代码也从函数式概念中受益匪浅。

##### [角状路由完全指南](https://www.smashingmagazine.com/2018/11/a-complete-guide-to-routing-in-angular/)

框架路由器是最佳实践和概念在前端生态系统中交叉传播的一个很好的例子。Angular 路由器看起来很像 vue-router，它看起来很像 react-router，而 react-router 又很像 Ember 路由器，我认为这是整个事情的开端。也就是说，如果你掌握了一门，学习其他的将会轻而易举。本文碰巧用 Angular 做了一些例子，但是即使您不使用 Angular，它所涵盖的概念在任何其他有路由器的 SPA 框架中也会对您有用。

#### 其他牛逼

##### [只需使用:焦点招式，该死](https://gomakethings.com/just-use-focus-styles-damnit/)

这是一篇关于交互/设计的评论，但是它强调了一些我认为非常值得思考的东西。作为前端开发人员和设计人员，我们经常根据我们如何使用 web 来做出决策。但不是每个人都以同样的方式做事。用户不仅仅是键盘或鼠标用户，而且经常在它们之间流畅地切换，假设用户都是 1 或另一个会将我们引入歧途。

##### [难度不是 UX 脏字](https://usabilitygeek.com/difficulty-not-ux-dirty-word/)

这也是一个关注用户体验/设计的作品，这是对试图简化一切的趋势的一个非常有趣的反驳。这篇文章认为，摩擦，或者说复杂性，实际上本身就有价值。我不确定我是否同意，但是我确实认为盲目追求简单是不好的——你的界面的复杂性与你的用户必须解决的问题的复杂性是不可分割的。你想要抽象或去除任何和所有不必要的复杂性，但是如果你开始去除必要的复杂性，你也在去除实用性。

##### [微软正在开发 Chromium 浏览器，将取代 Windows 10 上的 Edge](https://www.windowscentral.com/microsoft-building-chromium-powered-web-browser-windows-10)

好吧，我认为这实际上很不给力，因为这意味着我们基本上将只剩下 2 种不同的浏览器变体(参见[这篇文章](https://css-tricks.com/the-ecological-impact-of-browser-diversity/)解释为什么这很重要)，但这对 web 开发者来说是一件大事。

##### [Console.rules(💪)](https://itnext.io/console-rules-b30560fc2367)

有没有在像脸书这样的网站上打开你的开发者控制台，注意到他们在你的控制台中预装了一个红色的停止点和一些其他有趣的信息？想知道他们是如何设计他们的“stop”的，所以它又大又红？本文深入探讨了您可以用来修补和操作控制台的工具。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
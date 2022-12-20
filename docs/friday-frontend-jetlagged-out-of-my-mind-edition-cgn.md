# 星期五前端:时差出我的脑海版

> 原文：<https://dev.to/kball/friday-frontend-jetlagged-out-of-my-mind-edition-cgn>

提前为这一集里任何糟糕的语法、错别字、或离奇的语句道歉；我在阿姆斯特丹参加 React Amsterdam，时差都没倒过来！但这也可能让事情变得更有趣，谁知道呢。；)

无论如何，本周我们已经为您准备了一系列精彩的文章——我最喜欢的是对显示属性如何在幕后工作的深入探讨。

尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [使用 Mixin 从响应字体大小中提取数学信息](https://css-tricks.com/using-a-mixin-to-take-the-math-out-of-responsive-font-sizes/)

这篇有趣的文章分解了如何使用 mixins 来创建响应性字体大小——字体可以根据屏幕大小在一定范围内上下浮动，而不仅仅是基于媒体查询的跳转。

##### [挖掘到显示属性:显示的两个值](https://www.smashingmagazine.com/2019/04/display-two-value/)

精彩幕后看看改变`display`属性是怎么回事。不管你对 CSS 有多了解，你都应该阅读这篇文章，它会帮助你更好地理解到底发生了什么。

##### [如何在 CSS 中创建剪裁模糊的背景图片](https://codyhouse.co/blog/post/how-to-clipped-blurred-background-images-in-css)

简单明了，这向你展示了如何创建一个漂亮的“磨砂”模糊效果，对卡片页脚、英雄图片上的页眉以及其他类似的设计模式非常有用。

##### [创建一个全出血 CSS 实用程序](https://andy-bell.design/wrote/creating-a-full-bleed-css-utility/)

另一个 quick-hitter 教程，这一次是为了创建一个实用程序类，让图像或其他内容从容器中“溢出”,并在用户碰巧使用的任何设备上进行全出血(屏幕边缘到屏幕边缘)。

##### [动画表演 101:引擎盖下的浏览器](https://www.viget.com/articles/animation-performance-101-br)

我喜欢动画(当有品味地应用时)，CSS 让它变得如此简单。但是为什么有些动画工作起来像黄油一样流畅，而另一些却占用了大量的 CPU 资源，最后却结结巴巴地出现问题呢？这篇文章分析了正在发生的事情，以及为什么一些属性动画制作的很快，而另一些却很慢。

#### JavaScript

##### [你需要知道的关于无极的一切](https://medium.freecodecamp.org/promise-all-in-javascript-with-example-6c8c5aea3e32)

我听说对于新的 JavaScript 开发人员来说，承诺是最具挑战性的事情之一。Promise.all 是使它们比回调更好使用的关键特性之一，这篇文章很好地解释了这一点。

##### [高阶函数:它们是什么，以及一个 React 例子](https://medium.freecodecamp.org/higher-order-functions-what-they-are-and-a-react-example-1d2579faf101)

随着函数式编程变得越来越普遍，高阶组件成为 React 中极其强大和常用的模式，理解高阶函数对于现代前端开发人员来说非常有价值。如果术语“高阶函数”让你说“啊，那是什么？”，或者“是的，我想我得到了他们，但我不确定”，这是一个很好的帖子。

##### [如何用 Angular 和 NgRx 开始飞行](https://blog.angularindepth.com/how-to-start-flying-with-angular-and-ngrx-b18e84d444aa)

我根本不是角度专家，但如果我要在那里做一个项目，我肯定想找到一个 flux 架构解决方案，让我把我的 Redux & Vuex 知识带过来，好好利用它。这篇文章解释了 NgRx，一个开源库。

##### [现代 JavaScript 开发中的设计模式](https://dev.to/treyhuffine/design-patterns-in-modern-javascript-development-2jfl-temp-slug-2180980)

我倾向于对谈论设计模式的人有一点警惕——这通常是过于复杂的结构的代码，在灵活的语言如 JavaScript 中是不必要的，并且可能过于吓人或暗示比实际需要更多的知识。然而，识别重复的模式仍然是有价值的，这样您就可以适当地应用它们，本文中强调的 3 种模式确实是我在现代 JavaScript 开发中反复看到的模式。

##### [与 NativeScript-Vue 共享代码](https://www.nativescript.org/blog/code-sharing-with-nativescript-vue)

React Native 或 NativeScript 等基于 javascript 的原生应用程序开发的梦想是，也许我们可以为我们的 web 应用程序和原生移动和桌面应用程序使用相同的代码库。可能吗？嗯，可以这么说……这篇文章介绍了当前的技术水平，特别是使用 Vue 和 NativeScript。

#### 其他牛逼

##### [了解子资源完整性](https://www.smashingmagazine.com/2019/04/understanding-subresource-integrity/)

很好，也很中肯，这解释了加载外部脚本的安全特性，我必须承认我从来没有完全理解。如果你从别人的服务器上加载东西，你如何 100%保证脚本不会改变并开始做一些意想不到的或恶意的事情？输入子资源完整性…

##### [原生图像为网页懒人加载！](https://addyosmani.com/blog/lazy-loading/)

这超级刺激！虽然我们用来创建延迟加载图像的工具在

##### [使用自动生成的 id 实施可访问性最佳实践](http://bradfrost.com/blog/post/enforcing-accessibility-best-practices-with-automatically-generated-ids/)

我喜欢在组件库的层次上烘焙可访问性的想法(无论是开源的还是特定于公司的)。这种方法强调了这是多么简单——虽然示例使用了 React，但它应该可以在任何框架中工作(事实上，在带有计算属性的 Vue 中会感觉更加自然)。

##### [Web 组件将取代你的前端框架](https://www.dannymoerkerke.com/blog/web-components-will-replace-your-frontend-framework)

我有点不同意作者的观点——特别是我认为大多数现代前端框架采用的声明式模型比 web 组件仍然需要的命令式方法更符合人体工程学，也更有效率。也就是说，我对 web 组件创建独立于框架的可重用组件的潜力感到非常兴奋。

##### [为什么取消按钮永远不应该有颜色](https://uxmovement.com/buttons/why-cancel-buttons-should-never-have-a-color/)

更多的是设计而不是开发，但是前端开发人员经常默认地做出这些决定，我们应该意识到后果。我发现这是无色取消按钮的一个令人信服的例子。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
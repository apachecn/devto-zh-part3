# 星期五前端:1 月 25 日版

> 原文：<https://dev.to/kball/friday-frontend-january-25-edition-1eh3>

希望你度过了愉快的一周！今天我为大家准备了一份范围广泛的主题清单，本周没有明确的主题出现，但有很多很棒的内容。

首先，在 CSS 类别中，我们从一些长篇学习开始，进入一些特定属性的快速点击，然后以 2019 年的愿望清单结束。在 JavaScript 中，我们有一些纯粹的“基础”学习机会，看看 TypeScript 与 Flow，一个 Vue 快速点击，以及一个非常古怪的最终帖子。最后，在 Other Awesome 中，我们报道了我们社区中正在进行的一些辩论，看看标准，并分享一些交叉的文章。

希望你喜欢！

最佳，

来自 ZenDev 的 KBall

附注:我正在尝试在简讯中加入推广链接。请随意忽略它，但是如果你对推广的链接感兴趣，那么关注它们是支持我继续发布这份时事通讯的好方法。我会尽量只包括与前端开发人员相关的内容，如果有任何不满意的地方，请告诉我。

#### CSS&SCS

##### [【SCSS/萨斯全攻略】](https://medium.com/@js_tut/the-complete-guide-to-scss-sass-49ac053fcde5)

Sass 语言的全方位演练。从对 CSS 基本理解的假设开始，向您展示 Sass 在此基础上提供的所有内容。如果你现在还不习惯使用 Sass，绝对值得一试。如果你已经在萨斯的世界里，你可能不会从中得到太多；与其说它是最佳实践，不如说它是一个特性列表。

##### [你对 CSS 布局了解多少？](https://css-tricks.com/how-well-do-you-know-css-layout/)

一系列的 CSS 布局概念和实现，每一个都有一个例子，一个描述，和一些文章的链接。太好了！

##### [在语言特定样式的 lang 属性选择器上使用:lang 伪类](https://bitsofco.de/use-the-lang-pseudo-class-over-the-lang-attribute-for-language-specific-styles/)

这有点小众，但是很酷。我甚至没有意识到`:lang`伪类的存在(它也很老了；并且具有通用浏览器支持)但是这篇文章给出了一个很好的解释，说明了为什么如果您正在按照语言来设计样式，那么这无疑是一条正确的道路。

##### [制作 CSS 网格动画](https://css-irl.info/animating-css-grid)

哇…这太酷了。CSS 网格属性`grid-template-columns`和`grid-template-rows`现在可以在 Firefox nightly 中动画化。当然，如果你不使用 nightly，你就不能玩这个演示…但是有一个很好的视频展示了这个效果。

##### [2019 CSS 心愿单](https://css-tricks.com/2019-css-wishlist/)

有趣的是，看看人们希望在不久的将来 CSS 会有什么。对我来说，更好的表单样式绝对是最重要的，但这是非常实用的。回顾一下人们在 2013 年所期待的事情也很有意思(这些事情很少被实施……尽管我们所得到的是惊人的)。

#### JavaScript

##### [如何删除 ES6 中的数组重复](https://medium.com/dailyjs/how-to-remove-array-duplicates-in-es6-5daa8789641c)

对于那些希望提高“普通”JavaScript 技能的人来说，这是一个很好的简洁的例子，通过三种不同的方式实现了删除数组重复的功能。没有什么特别深刻的，但一个伟大的学习练习。

##### [为什么我已经停止从我的 JavaScript 模块中导出默认值](https://humanwhocodes.com/blog/2019/01/stop-using-default-exports-javascript-module/)

了解使用 ES6 模块的不同模块设计的利弊。您使用的是 ES6 模块，对吗？:)但是说真的，我真的很高兴看到这样的文章——对于 JavaScript 领域的许多人来说，模块仍然是一个困惑的来源，很难知道什么是正确的架构。我们越能让人们公开探索各种选择及其后果，我们就越好。

##### [将 30K 行代码从 Flow 移植到 TypeScript](https://davidgom.es/porting-30k-lines-of-code-from-flow-to-typescript/)

非常有趣地研究了使用 Flow 和使用 TypeScript 之间的一些区别。如果您对这些工具中的一个或另一个有深入的了解，您会非常喜欢它…如果您正在考虑从哪一个开始，我从这篇文章中得到的最大收获是，这两个工具非常接近等价，在某些方面稍好一些，在其他方面稍差一些，但是 TypeScript 社区(以及开源库的相关类型)要大得多，也更全面。

##### [vue . next Tick 是什么 Tick？](https://vuejsdevelopers.com/2019/01/22/vue-what-is-next-tick/)

如果你和我一样是老派，你可能会熟悉使用超时为 0 或 1 的`setTimeout`来“插入队列”,并把一些事情推迟到 UI 线程完成下一批工作之后。Vue.js 实际上附带了专门用于此目的的功能，这种方式比只使用 setTimeout 更具语义和框架意识。本文解释并展示了您可能想要使用它的一些原因。

##### [用 Javascript 和表情符号制作 URL 动画](http://matthewrayfield.com/articles/animating-urls-with-javascript-and-emojis/)

异想天开，而不是实际的，但这篇文章只是让我开心地傻笑，所以我想分享它。作者注意到了两个事实，即 URL 可以包含表情符号，并且可以基于 javascript 计时器非常快速地更新 URL 的哈希，并使用它来创建一组 URL 动画。好玩！据我所知没有实际意义，但是超级酷。

#### 提升链接

##### [Udemy:开启一年强势销售！](http://click.linksynergy.com/fs-bin/click?id=hIdOL5Z4eK4&offerid=507388.9785&type=3&subid=0)

到 1 月 26 日(明天！)Udemy 正在进行一场特价活动，顶级课程的折扣降至 11.99 美元。我在[最近关于 2019 年要学的前端主题的帖子](https://dev.to/kball/top-5-frontend-development-topics-to-learn-in-2019-4m0m)中推荐的所有课程都在“待售”列表中，还有成千上万的其他课程。如果你有一些你一直想学的东西，现在就抓住机会，用最便宜的价格买到它！

#### 其他牛逼

##### [大分裂](https://css-tricks.com/the-great-divide/)

一个清晰的总结，描述了前端使用 HTML 和 CSS 的人与所有时间都使用 JavaScript 的人之间日益扩大的鸿沟。我的感觉是这种分歧是真实的，尽管我不知道它有多新——在更关注应用程序逻辑的人和关注接口层的人之间一直存在分歧，只是我们现在把他们都称为前端。:P 也就是说，这是我们这个领域目前面临的重要挑战之一，所以关注这一点很重要。

##### [HTML5 输入类型:现在在哪里？](https://www.smashingmagazine.com/2019/01/html5-input-types/)

HTML5 引入了一组新的输入类型，如“email”和“tel ”,让浏览器做一些智能的事情，如验证和预填充。但是对浏览器的支持却是喜忧参半。本文介绍了当前的支持状态，并给出了何时使用这些新输入类型以及何时不使用的建议。

##### [Web 标准:什么，为什么，以及如何](https://www.smashingmagazine.com/2019/01/web-standards-guide/)

标准的发展和改进是关键的“公共产品”之一，随着开源软件的兴起，已经为我们现在的 web 开发提供了令人难以置信的高效和富有成效的环境。但与开源不同，标准通常在“后台”运行，看不到它们是什么或它们是如何开发的。就个人而言，我认为理解它们和它们产生的过程是提升你的技能的关键，也有助于推动我们的行业向前发展。这是一篇很棒的 explainer 文章……如果你对这个话题感兴趣(尤其是 TC39 JavaScript 标准委员会),你可能会喜欢最近的 JSParty 播客。

##### [为大家介绍爱奥尼亚 4:爱奥尼亚](https://blog.ionicframework.com/introducing-ionic-4-ionic-for-everyone/)

这很有趣。Ionic 是最早的“来自 web 框架的移动应用”工具之一，考虑到可用组件的广度和易于集成，它可能是更好的工具之一，但有一个问题:它们总是关注角度。如果你在棱角分明的世界里，那很好…但如果不是，那就有点不走运了。不再是了。他们发布了一个新版本，完全使用本地 web 组件构建，然后使用一组特定于框架的绑定，让这些 web 组件感觉“内置”在您可能使用的任何框架中。还想要棱角分明？没问题。使用 Vue 的 React？插入他们的 React 或 Vue 适配器，您就可以开始工作了。

##### [新到 JAMstack？入门所需的一切](https://snipcart.com/blog/jamstack)

对“jam stack”(JavaScript、API 和标记)概念的扎实介绍。我认为这种将越来越多的东西提前静态生成并预先计算以接近用户的概念是我们这个时代的趋势之一，并将在越来越多的项目中出现。如果你还没有看，一定要读一下这篇文章，了解一下这里发生了什么。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
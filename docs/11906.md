# 星期五前端:CSS 网格正在成长版

> 原文：<https://dev.to/kball/friday-frontend-css-grid-is-growing-up-edition-4cd7>

像任何好的软件一样，CSS Grid 已经到了人们要求更多的地步。我们不仅看到了一些很好的用例以及最佳实践的出现，还看到了一整套扩展标准的想法。在下面的文章中可以找到其中的一些。

当然，这里也有一些很棒的 JavaScript 文章和其他资源。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [我们想从网格中得到什么](https://css-tricks.com/what-we-want-from-grid/)

软件作为一种媒介的独特之处在于它总是在进化。这就是为什么对我来说，真正好的软件最重要的标志之一是它激发用户想要更多。如果你的用户说“嘿，这太棒了…如果我们能做到这一点会怎么样?”,你就知道你做对了。OMG 那就太神奇了！”我们现在看到 CSS Grid 就是这样，这篇文章很好地总结了人们所要求的一些“这个”。

##### [看马，没有媒体质疑！使用 CSS 网格的响应式布局](https://css-tricks.com/look-ma-no-media-queries-responsive-layouts-using-css-grid/)

这是一次精彩的演示，演示了如何使用 CSS Grid 创建响应式布局，而无需一次媒体查询。这种“无媒体查询”响应的概念(曾被称为“液体”设计)变得越来越普遍，也越来越容易实现。

##### [网格:CSS 网格布局的简单可视备忘单](http://grid.malven.co/)

标题很好地描述了它。这很好，因为它在网格中给出了许多与它们的属性相关的缩略图大小的例子——这使得扫描你试图实现的事情类型并获得如何实现的信息变得容易。

##### [纯 CSS 绘图要领](http://diana-adrianne.com/how/)

我定期分享用 CSS 和 HTML 完成的艺术作品，我总是被它们震撼，通常并不真正了解作者是如何做他们正在做的事情的。这篇文章非常酷，因为它开始指向那个方向…它采用了几个月前我们作为一篇文章分享的'[纯 CSS Francine](http://diana-adrianne.com/purecss-francine/) '艺术作品，但随后指出了 5 个关键的 CSS 属性，展示了不使用这些属性的作品会是什么样子。这不是一个教程本身，但肯定有助于照亮一些通往 CSS 艺术的道路。

#### JavaScript

##### [滑入滑出 Vue.js](https://www.smashingmagazine.com/2019/02/vue-framework-third-party-javascript/)

(偏见提醒:这篇文章是我写的)。Vue.js 的一个关键优势是它可以很好地与其他代码配合:它很容易逐步嵌入到其他应用程序中，但也很容易将非 Vue 代码包装到 Vue 中。本文探讨了第二个优势，涵盖了三种不同类型的第三方 JavaScript 以及将它们嵌入 Vue 的方法。

##### [递进反应](https://houssein.me/progressive-react)

在过去的几个月里，我们看到了很多文章强调 JavaScript 性能、客户端性能的重要性，以及衡量其对用户的影响。这篇文章做得很好的是采用一个单一的框架，并分解一组要考虑的事情和改进的方法，以便对您的客户端性能、首次交互时间等做一些事情。

##### [用钩子反应门户](https://www.jayfreestone.com/writing/react-portals-with-hooks/)

React 门户和钩子是 React 中两个相对较新的特性。门户允许您在组件的 DOM 上下文之外呈现“子组件”(例如，如果您有一个模型，它在概念上是组件的子组件，但不应该是 DOM 中的子组件)，而钩子是除类之外管理有状态组件的另一种方法。将两者结合起来有一些不明显的细微差别，本文很好地探索了这些差别并展示了如何做到这一点。

##### [重构一个组件使用 React 钩子](https://javascriptplayground.com/refactoring-to-react-hooks/)

尽管 React 团队已经明确声明，使用钩子不需要重构现有的基于类的组件(基于类的组件不会消失)，但是不可避免的是，如果你开始使用钩子，你可能会为了一致性而重构旧的组件。本文很好地向您展示了如何使用生命周期挂钩将现有的基于类的组件改造成新的功能性“挂钩”组件。

##### [提升你的 JavaScript 技能的 12 个概念](https://hackernoon.com/12-javascript-concepts-that-will-level-up-your-development-skills-b37d16ad7104)

这是一篇提高 JavaScript 语言基础的优秀文章，无论您使用什么样的框架和其他工具，都会有所帮助。

#### 提升链接

##### [一个月训练营](https://mbsy.co/sGmNV)

一个月认为，你成功完成训练营的最大因素之一是你是否与其他人一起工作。他们分组授课，注重与教师和其他学生的互动。专注于初学者，他们有一系列的课程，包括 [JavaScript](https://mbsy.co/sGmNT) 、 [HTML & CSS](https://mbsy.co/sGmNR) 等等。点击这些链接中的任何一个都可以享受 10%的折扣。

#### 其他牛逼

##### [试 GraphQL](https://trygql.com/)

GraphQL 是我今年最想学的话题之一，并且看起来非常简洁。这是一个免费的在线教程，有一套互动练习来逐步探索这门语言。从超级基础开始，所以你不需要任何先验知识。去看看！

##### [跳跃:网页动画中的新步骤()](https://danielcwilson.com/blog/2019/02/step-and-jump/)

这真的很酷！web 动画中的一些新属性，允许您进行逐帧动画或在关键帧动画中的不同点之间“跳跃”。可能的用例是类似“动画书”风格的动画或带有跳跃秒针的时钟。这篇文章最好在 Firefox 中阅读，因为到目前为止，新的动画属性只在 Firefox 中受支持。

##### [Chrome 中 JavaScript 加载优先级](https://addyosmani.com/blog/script-priorities/)

一个有趣的故障，我从它身上学到了很多。介绍了在页面中包含 JavaScript 的各种方法(在页面头、在页面体的末尾、使用 defer、使用 async ),并根据它们的加载和执行方式展示了它们对性能的不同影响。还包括何时使用哪个的建议。非常非常整洁！

##### [资源优先化——让浏览器帮助你](https://developers.google.com/web/fundamentals/performance/resource-prioritization)

如果上面的文章很有趣，但是感觉有点高，或者让你问“但是我怎么做呢？”这是一个简单的教程，向你展示了 3 个工具，你可以用它们来影响加载的优先级，以及如何使用每一个。

##### [图标 SVG](https://iconsvg.xyz/)

一组可定制和下载的 SVG 图标，都有麻省理工学院的许可。如果你没有使用像 fontawesome(顺便说一句，我强烈推荐)这样的全套图标集，但想找一些一次性的，这看起来是一个很好的资源。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
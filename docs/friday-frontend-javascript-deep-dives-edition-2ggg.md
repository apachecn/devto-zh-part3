# 星期五前端:JavaScript 深度潜水版

> 原文：<https://dev.to/kball/friday-frontend-javascript-deep-dives-edition-2ggg>

希望你度过了愉快的一周！这一集包含了很多有趣的东西，但对我来说最突出的是两篇关于不同 JavaScript 片段的深度文章。特别是将函数组件与类进行比较的那篇文章非常引人注目，我也很喜欢阅读关于承诺的文章。

当然，我们在 CSS 中有很多其他好的东西，还有其他很棒的东西。对 CSS 心智模型的探索，一些 CSS 属性和选择器的好目录，向无密码的未来迈进了一大步。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [CSS 自定义属性打得过 Sass 循环吗？](https://css-tricks.com/do-css-custom-properties-beat-sass-loops/)

仔细看看 CSS 自定义属性相对于以前用来创建一组主题组件(如警报)的机制的可读性优势。

##### [CSS 心智模型](https://www.quirksmode.org/blog/archives/2019/02/css_for_javascr_1.html)

作者试图展示 CSS 和 JavaScript 的一些不同之处。这是一个具有挑战性的话题——我已经读了很多，也写了一些——我不确定是否有可能做一个完整的评估。本文不尝试；它仍然是非常基本的，但我认为它是一个有价值的对话的补充。

##### [gif 中鲜为人知的 CSS 属性](https://www.designyourway.net/blog/css-html/lesser-known-css-properties-in-gifs/)

一系列 CSS 属性的动画可视化演示。这对于获得可能性的感觉是很棒的，然后当你发现你感兴趣的东西时，会有资源链接来了解更多。

##### [CSS 选择器备忘单&详情](https://medium.com/design-code-repository/css-selectors-cheatsheet-details-9593bc204e3f)

选择器是 CSS 的基本构件之一。当然，你可以在那些很酷的属性上疯狂，但你真的需要知道如何瞄准元素。这篇文章不仅包括一个可下载的小抄，还包括一个有趣的游戏，在这里你可以测试你的知识。

##### [CSS 调查状态](https://stateofcss.com/)

我已经说过一次了，但是我想再把它放出来，鼓励你们去接受它。尤其是当你关注 CSS 的时候！该调查的作者之前在 JavaScript 世界做过一些调查，尽管他们真诚地努力获得广泛的回应，但他们的受众在 React 世界中往往非常多。就个人而言，尽管我很喜欢 React(我确实喜欢，但它是一个很棒的工具！)，该社区往往处于许多趋势的边缘，所以我真的想鼓励那些处于更传统前端角色的人参与调查。

#### JavaScript

##### [React 组件的“黄金法则”如何帮助你写出更好的代码](https://medium.freecodecamp.org/how-the-golden-rule-of-react-components-can-help-you-write-better-code-127046b478eb)

我发现这是一个非常好的分解/思考分离 React fosters 的“一切都是组件”心理模型中的关注点的方式。它很好地解释了高阶组件的概念，并展示了采用一种将逻辑组件与表示组件分开的结构是如何有帮助的。

##### [在 Vue.js 中创建自定义指令](https://medium.com/js-dojo/creating-custom-directives-in-vue-js-286142392fd8)

简单明了地介绍了在 Vue 中创建自定义指令的内容。如果你碰巧在上周阅读了我的文章,我强调了自定义指令是你集成第三方 JavaScript 工具箱中的关键工具之一——如果你喜欢它，但仍然不确定如何进行，那么这篇文章将从最基础的开始，逐步构建。

##### [所有数字来自 Vue.js 状态报告 2019](https://www.monterail.com/blog/state-of-vuejs-2019)

众所周知，我是 Vue.js 的超级粉丝，所以我喜欢看这种挖掘 Vue 周围社区以及人们如何使用它的东西。有趣的是看到 Vue 用户最常用的“其他 JS 库”仍然是 jQuery，而 node & PHP 是最受欢迎的两个后端(后者很可能与 Laravel 拥抱 Vue 有关)。无论如何，那里有很多，如果你喜欢 Vue，你应该去看看。

##### [函数组件和类有什么不同？](https://overreacted.io/how-are-function-components-different-from-classes/)

这完全让我大吃一惊。我花了一段时间去领会作者在说什么，但是一旦我这样做了，它就变得如此明显，我不能相信我以前没有注意到它。在 React 中，只要不需要本地状态或生命周期组件，通常会将基于类的组件和功能组件视为相对等同的组件。现在有了钩子，你也可以处理这些情况，所以它们是等价的，对吗？…然而它们并不相同。它们在处理可变性的方式上有微妙而重要的不同。看这个帖子。

##### [深入到承诺决议与对象包括一个然后属性](https://dev.to/stefanjudis/til-promise-resolution-with-objects-including-a-then-property-2nhj)

最近，我和一个在不同的代码训练营教过书的人进行了一次对话，我问她，对于初学者来说，今天最困难的事情是什么。答案是承诺。现在，其中一些可能只是一般意义上的难以实现的异步，但一些是特定于承诺的，所以现在我正在寻找关于承诺的好文章。这是一个，深入挖掘承诺决议如何工作，特别是与不同类型的对象。

#### 提升链接

##### [一个月训练营](https://mbsy.co/sGmNV)

一个月是一个基于群组的在线训练营，提供许多不同的课程。由一位在联大和哥伦比亚大学等地授课的讲师主持，他们开设为期一个月的项目课程。新课程下周一开始，除了预先录制的材料，你还可以参加每周的办公时间课程，并与教师和其他学生共享一个空闲频道。他们有一系列的课程，包括 [JavaScript](https://mbsy.co/sGmNT) 、 [HTML & CSS](https://mbsy.co/sGmNR) 等等。点击这些链接中的任何一个都可以享受 10%的折扣。

#### 其他牛逼

##### [我应该在制作中使用源地图吗？](https://css-tricks.com/should-i-use-source-maps-in-production/)

受 Rails 领域默认设置变化的影响，Chris Coyier 深入探讨了这个问题以及将源代码地图发布到产品中的利与弊。大部分对话都集中在 CSS 上，但我认为同样的想法也适用于 JS。

##### [什么时候按钮不是按钮？](https://www.smashingmagazine.com/2019/02/buttons-interfaces/)

当你选择使用一个`button`元素而不是尝试用一个 div 创建你自己的元素时，舌头和脸颊走过了你从浏览器得到的许多功能。寓教于乐。

##### [如何在虚拟现实中构建一个无止境的跑步者游戏(上)](https://www.smashingmagazine.com/2019/03/virtual-reality-endless-runner-game-vr-part-1/)

虚拟现实有点超出我的能力范围，但你可以在 WebVR 中用 [A-frame](https://aframe.io/) 做的事情是非常惊人的。本文将带您了解如何使用 A-frame 创建一个 WebVR runner 游戏。

##### [W3C 批准 WebAuthn 作为无密码登录的网络标准](https://venturebeat.com/2019/03/04/w3c-approves-webauthn-as-the-web-standard-for-password-free-logins/)

自从我把所有东西都换成一个密码，买了新一代 Macbook pro，我对我们无密码的未来有了一点超前的认识。对于绝大多数应用程序，我只需在 mac 上输入我的指纹，它就能让我登录……不需要忘记密码，不存在密码被黑客攻击的跨站点漏洞。太神奇了。现在，我们正在考虑为这种方法建立一个标准，而不需要中间的密码管理器。我等不及了。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
# 星期五前端:现代 CSS 的力量

> 原文：<https://dev.to/kball/friday-frontend-the-power-of-modern-css-5fph>

希望你有一个伟大的一周，并期待一个美好的周末！本周的文章分为几个主题，我想向大家介绍一下。

首先，有一组文章强调了现代 CSS 的力量(以及一些超级令人兴奋的即将到来的特性)。

其次，在“其他精彩”部分有一大堆有趣的与性能相关的文章，既有关于为什么应该关注 web 性能的案例，也有给你带来影响的工具。

当然还有一些其他 CSS、JavaScript 和 web 相关的主题，包括我在 React Hooks 上看到的最有趣的文章之一。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

P.S .如果你没有看到它并且感兴趣，我本周发表了一篇关于我在 2019 年需要学习的[五大前端主题的帖子](https://dev.to/kball/top-5-frontend-development-topics-to-learn-in-2019-4m0m)。

#### CSS&SCS

##### [在防御效用至上的 CSS](https://frontstuff.io/in-defense-of-utility-first-css)

这是我所见过的对“实用优先”CSS(与原子 CSS 相关)背后的思想的最透彻、最出色的解释之一。讨论了许多反对的观点，并逐一解决了它们。绝对让我开了一点眼界——我现在可能会在一个项目中尝试一下，看看效果如何。

##### [为什么我们需要 CSS 子网格](https://dev.to/kenbellows/why-we-need-css-subgrid-53mh)

CSS 子网格是 CSS 网格的下一部分，它将彻底改变我们的布局方式。目前[正在规范](https://www.w3.org/TR/css-grid-2/)的过程中，希望我们能在明年左右在浏览器中看到它。这篇文章解释了它是什么，它所解决的问题是什么，以及它将如何被使用。坦率地说，它让我对 CSS 网格更加兴奋。前端开发现在是一个很好的地方。

##### [何时以及如何使用 CSS 多栏布局](https://www.smashingmagazine.com/2019/01/css-multiple-column-layout-multicol/)

在当今令人惊叹的网格和 flexbox 布局机制中，CSS 也有一个多列布局结构，几乎每个浏览器都支持(至少大部分支持)。它相对受限——只有很少的场景可以很好地工作——但它是工具箱中另一个强大的工具。

##### [远离萨斯](https://cathydutton.co.uk/posts/why-i-stopped-using-sass/)

关于 Sass 的一些关键价值主张如何“超越”到可以直接在 CSS 中使用。就我个人而言，我使用 mixins 已经足够了，所以我仍然对 Sass 感到非常兴奋，但是我认为作者有一些非常好的观点，这再次提醒了我们 CSS 已经变得多么强大。

##### [CSS 中的新视野:胡迪尼与绘画 API](https://dev.to/bnevilleoneill/new-horizons-in-css-houdini-and-the-paint-api-5116-temp-slug-2173531)

关于 CSS 的力量的话题，这是对即将到来的 CSS 胡迪尼的一个很好的审视，这是最令人兴奋的正在进行的发展之一，它将影响 CSS 将有多强大。

#### JavaScript

##### [使用 React 门户呈现 DOM 层次结构外的子节点](https://css-tricks.com/using-react-portals-to-render-children-outside-the-dom-hierarchy/)

我以前没怎么见过或想过门户，但这篇文章让我清楚地了解了它们是什么以及它们是如何工作的。允许您创建概念上的子组件，这些子组件不一定是组件的 DOM 子组件，保留子组件的所有良好的层次结构优势，同时允许您突破布局限制。情态动词是典型的例子，但是我可以想到很多其他的场景。

##### [React 中的网页可访问性入门](https://blog.usejournal.com/getting-started-with-web-accessibility-in-react-9e591fdb0d52)

很好地展示了如何使用 React 实现一个可访问的 web 应用程序。关于 React 工具的上下文信息为您提供了解决一系列可访问性挑战的方法。爱死了！

##### [我如何用 React 钩子用大约 100 行代码构建了一个异步表单验证库](https://medium.freecodecamp.org/how-i-built-an-async-form-validation-library-in-100-lines-of-code-with-react-hooks-81dbff6c4a04)

React 的新钩子 API 的价值主张之一是钩子是可组合的。这是我看到的第一篇真正开始探索这种组合的威力的文章，为表单验证构建了一个非常简单而强大的 API。非常酷！

##### [类型脚本 2.8:条件类型](https://blog.mariusschulz.com/2019/01/09/typescript-2-8-conditional-types)

最新版本的 TypeScript 提供了一个非常强大的功能，允许您实现处理多种类型的函数，并向编译器提供线索，告诉您何时可以推断一种类型而不是另一种类型。这是对 TypeScript 工具箱的一个非常强大的补充。

##### [真正的 JavaScript，不要太多，阶段三及以上](https://changelog.com/jsparty/58)

(偏见警告-我做了这次采访)幕后看看 ECMA 和 TC39 委员会如何努力创建 JavaScript 语言的标准。如果你曾经想知道所有这些东西是如何工作的(或者如何参与)，这是一个很好的对话，或者如果你喜欢阅读，上面的链接有一份抄本。

#### 其他牛逼

##### [网络的伦理表现](https://timkadlec.com/remembers/2019-01-09-the-ethics-of-performance/)

讨论 web 性能的道德含义，证明我们有道德义务来调整我们网站的性能。着眼于排斥，获取，以及能源和浪费。如果我们关心全球变暖，热门网站的能源成本实际上开始变得重要起来，调整性能可以产生真正的影响。

##### [网页可用性事宜](https://dev.to/addyosmani/web-page-usability-matters-3aok)

从另一个角度看 web 性能的含义，这一次是从它如何影响用户行为的角度。着眼于性能的不同维度，这些维度如何与我们可能希望避免(疯狂点击)或鼓励(转化)的用户行为相关联，并着眼于您可以用来优化各种性能维度的一些方法。

##### [前端性能检查表 2019](https://www.smashingmagazine.com/2019/01/front-end-performance-checklist-2019-pdf-pages/)

无论上面的文章是否让您相信密切关注性能是您想要做的事情，或者前端性能已经在您的日程上，拥有一个需要考虑和解决的要点清单可能会有所帮助。正是如此，可以下载 PDF/Mobi/Etc 文件，也可以通过 HTML 在线下载。对于清单中的每一点，都有上下文链接和如何做的指导。非常好。

##### [设计 Flexbox 检查员](https://hacks.mozilla.org/2019/01/designing-the-flexbox-inspector/)

Firefox 中新 Flexbox inspector 背后的思考过程非常有趣。看看他们如何思考 Flexbox 的独特属性，以及如何在开发工具中向开发人员展示这些属性和浏览器围绕它们做出的决策，这很有趣。

##### [为什么大家都爱 GraphQL？](https://blog.bitsrc.io/why-does-everyone-love-graphql-17de7f99f05a)

在我最近关于 2019 年要学习的主题的帖子中，我将 GraphQL 放在了那里，这主要是基于快速的采用增长和我听到的越来越多的项目和公司采用它。这篇文章探讨了这种快速增长背后的一些“为什么”。导致 GraphQL 如此迅速流行的优势和特性是什么。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
# 星期五前端:网格和组件版

> 原文：<https://dev.to/kball/friday-frontend-grids-and-components-edition-g7p>

五月底快乐！我们现在肯定是进入夏天了，我对温暖天气的回归感到超级兴奋！

在本周的版本中有许多好东西，但是有两件大事引起了我的注意。首先是一些关于 CSS 网格和一个很酷的新网格生成器的优秀文章。然后在 JS 领域，我们有一些很棒的文章，总结了关键的面向组件的开发原则。他们碰巧使用了 React 示例，但是不管您使用什么框架，都应该是有用的。

哦，对了，请不要错过其他精彩的 WebAssembly 案例研究。非常非常有趣地一瞥使用 WASM 的挑战和好处。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [折叠大教堂](https://www.joshwcomeau.com/posts/folding-the-dom/)

这真让我大吃一惊。这是一个关于如何在 DOM 中实现 3d 效果(比如折叠和展开一幅图像)的很好的分步指南。

##### [用 CSS 或 HTML 包装长词](https://www.cjcid.com/articles/wrapping-long-words-css-html/)

我最近正在努力解决这个问题——如何让单词根据分配给它们的空间在正确的位置换行。有许多基于 CSS 和标记的选项，每一个都有自己的优缺点。这篇文章非常详细地介绍了它们。

##### [深入显示属性:一路网格向下](https://www.smashingmagazine.com/2019/05/display-grid-subgrid/)

深入探讨了`display: grid`属性实际上是如何工作的，它是如何与其他显示属性(如浮动)交互的，以及嵌套是什么样子的。同样作为一个子注释，我看到有人提到新的子网格特性已经在 Firefox nightly 中登陆。激动人心！！！

##### [用 CSS 网格和 Flexbox 创建一个分割的仿容器布局](https://andy-bell.design/wrote/create-a-split-faux-container-layout-with-css-grid-and-flexbox/)

一个 CSS Grid 和 Flexbox 如何互补使用的好例子。作者使用 Grid 来实现外部布局，然后使用 Flexbox 来布局每个网格项目内部的内容。

##### [CSS 网格生成器](https://cssgrid-generator.netlify.com/)

奇妙的新的基于 web 的图形用户界面，用于生成带有 CSS 网格的布局，并导出代码用于您的项目。

#### JavaScript

##### [函数式 JavaScript:如何使用 Array reduce 而不仅仅是数字](https://jrsinclair.com/articles/2019/functional-js-do-more-with-reduce/)

与上周关于优雅的 JavaScript 错误处理的流行文章出自同一作者，这是一篇非常清晰的文章，解释了 reduce 方法在数组上的强大功能。我已经使用了 reduce，并且感觉非常舒服，这仍然扩展了我对这种方法的可能性和能力的看法。我强烈推荐阅读这篇文章。

##### [为什么你应该在 Redux](https://medium.com/javascript-in-plain-english/https-medium-com-javascript-in-plain-english-why-you-should-use-an-object-not-an-array-for-lists-bee4a1fbc8bd) 中使用一个对象，而不是一个数组

前端开发需要越来越多的数据管理，考虑不同方法的利弊很重要。数据规范化是一个巨大的问题，这是使用对象而不是数组作为基本结构的一个令人信服的例子。(在本文中，我见过的一种方法是将核心数据存储在这样一个键控对象中，然后按照您关心的顺序保存一个键数组)。

##### [React 中有哪些可复用组件？](https://www.robinwieruch.de/react-reusable-components/)

面向组件的开发是前端的一个明显的大趋势，所以不管你的框架如何，理解更好的组件技术都是有帮助的。本文介绍了重构 React 组件以提高其可重用性的过程，但是所涉及的概念适用于任何框架。

##### [一反应过来举起状态举起&放下例子](https://www.robinwieruch.de/react-lift-state/)

与上面的文章出自同一作者，但是太好了，我不得不把它放进去。处理状态管理和状态应该在哪里是面向组件开发的一个基本部分，并且通常随着开发的进行,“正确答案”会发生变化。本文介绍了将哪个组件拥有状态向上移动到父组件或向下移动到子组件的过程。干得好，虽然这个例子是在 React 中，但是这个概念适用于任何面向组件的框架。

##### [你不知道存在的 8 个 DOM 特性](https://dev.to/bnevilleoneill/8-dom-features-you-didn-t-know-existed-4klf-temp-slug-2968224)

是的，它比 JavaScript 更特定于 DOM，但它们是使用 JavaScript 与 DOM 交互的方式，在前端环境中，这是我们正在做的很大一部分。另外，里面有很好的东西！

#### 提升链接

##### 

最近有几个人找我找工作，我强烈推荐 TripleByte。它们免去了求职中许多应用程序的麻烦，而且是由工程师为工程师设计的。过程很简单:参加一个在线测试，然后和 TripleByte 的人进行电话面试。如果你都通过了，他们会帮你跑腿，让你直接去很多公司参加最后的现场面试。目前只针对在旧金山湾区、纽约市、西雅图和洛杉矶寻找工作的人，但是如果你在这些地方之一，并且 100%地考虑找一份新工作，那么试试 TripleByte。

#### 其他牛气

##### [字体-显示与图标字体不兼容](https://www.zachleat.com/web/font-display-icon-fonts/)

我已经是图标内联 SVG 的大力提倡者，但这是反对图标字体实践的绝技。它链接到一组反对图标字体的现有论据，然后一步一步地介绍新的`font-display`属性如何在图标字体中没有正确的用法。

##### [无障碍图标按钮](https://www.sarasoueidan.com/blog/accessible-icon-buttons/)

深入探究让基于图标的按钮具有可访问性的所有方法。我喜欢的是，它不仅展示了方法，还展示了如何在 chrome devtools 中检查可访问性的工具，并通过思考进行了讨论。这是一个提高无障碍技能的绝佳资源。

##### [易贝的网络大会:一个真实世界的用例](https://www.ebayinc.com/stories/blogs/tech/webassembly-at-ebay-a-real-world-use-case/)

这是一个在实际问题上使用尖端技术的很好的例子，深入研究边缘案例和行为不符合预期的地方，然后跟踪客户成果中可测量的(和大的)改进。

##### [非设计师的基本设计原则](https://www.freecodecamp.org/news/fundamental-design-principles-for-non-designers-ad34c30caa7/)

作为前端开发人员，我们经常处于试图在我们拥有的(静态)设计和产品的(动态)需求之间插入的位置。出现了设计没有完全涵盖的问题，我们该怎么办？在一个理想的世界中，我们可以与设计师进行对话，但世界很少是理想的，所以至少有一种良好实践的意识是有用的。

##### [2019 年你应该知道的 13 个 GraphQL 工具和库](https://blog.bitsrc.io/13-graphql-tools-and-libraries-you-should-know-in-2019-e4b9005f6fc2)

没有什么大的惊喜，但是有一个可靠的列表，所以如果你正在使用 GraphQL(或者考虑开始使用它)，这是值得一读的。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
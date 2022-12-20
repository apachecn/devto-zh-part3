# 星期五前端:今年的最后一版

> 原文：<https://dev.to/kball/friday-frontend-last-edition-of-the-year-4nk3>

欢迎来到 2018 年最后一个星期五前端！

我将在假期休息 2 周，然后在 1 月 11 日星期五回到我们定期安排的时事通讯。我希望你也有机会在这个假期休息一下，或者至少花一天时间来反思过去的一年和未来的一年。

下面我们有一组有趣的折衷的文章，从关于 CSS 最佳实践的哲学争论到一个很酷的背景去除图像工具，停下来谈谈 XSS。还有一些更简单的教程可以帮助你不断提高游戏水平。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [将数学保存在 CSS 中](https://css-tricks.com/keep-math-in-the-css/)

一个论点是，我们应该在我们的 CSS 中保留甚至是预先计算的数学，而不是运行计算，只包括最终的工作。我完全同意这一点——这让我想起了这样一句话:代码写一次，读几十次。这是给你未来的自己(或者你代码库中的任何人)留下注释。

##### [什么时候应用:hover，:focus，和:active 伪类？](https://bitsofco.de/when-do-the-hover-focus-and-active-pseudo-classes-apply/)

这些不同的伪类的生命周期的非常好的细分，以及当我们考虑移动设备时，这是如何变化的。

##### [可见性属性不仅仅是关于可见性](https://bitsofco.de/the-visibility-property-isnt-just-about-visibility/)

深入了解 visibility 属性的所有细微差别，以及它如何影响可访问性。

##### [研究 CSS 规范中的一个属性](https://24ways.org/2018/researching-a-property-in-the-css-specifications/)

在“授人以鱼”这一类别中，这篇文章分解了 CSS 规范是如何组织的，以及如何使用它们来学习 CSS。如果你试图用 CSS 做一些事情，却找不到关于它的好的教程或文章，那么规范就是权威的来源。知道如何研究你需要什么将大有帮助。

##### [新增 CSS 逻辑属性！](https://medium.com/@elad/new-css-logical-properties-bc6945311ce7)

这真的很有趣……最近的 CSS 规范，比如 flexbox，已经脱离了传统的使用物理方向的趋势，比如“左”、“右”、“上”和“下”，而倾向于使用逻辑方向，比如“开始”和“结束”。这使得管理具有不同自然方向的多种语言的一组 CSS 变得容易得多，使 web 不再以英语为中心。现在有提案(和实现！)将这个概念扩展回更老的属性，如边距、填充、浮动等等。挑战还是有的，但这是一个很有意思的方向。

#### JavaScript

##### [高级 JavaScript 注入](https://brutelogic.com.br/blog/advanced-javascript-injections/)

哇哦。我通常不把安全的东西放在这里，但是这引起了我的注意，并在我浏览的时候让我大吃一惊。探究 XSS 背后的思想，以及如何将 URL 组件直接翻译成 JavaScript 代码，从而为聪明的黑客打开大门。

##### [科技趋势对决🏆:反应 vs 角度 vs Vue](https://medium.com/zerotomastery/tech-trends-showdown-react-vs-angular-vs-vue-61ffaf1d8706)

在过去的几周里，我花了很多时间思考、研究和讨论不同调查的优缺点。我的第一个结论？他们都有偏见，在得出你的结论时，查看一系列来源是很重要的。这显示了对最近争论的 JavaScript 调查状态的截然不同的答案——特别是 Angular 显示得更高，而 Vue 显示得相当低——但是如果您正在考虑投资和学习哪个框架，这绝对值得添加到您的数据集。

##### 

看看 JavaScript 开发人员在涉及到公民权利等问题时需要考虑的因素。一个经常讨论的领域是可访问性——使残疾用户仍然能够使用我们的网站和应用程序。这里涉及的其他领域包括隐私权和保护用户免受第三方代码攻击的重要性。在我们工作的时候，这绝对是一系列值得考虑的事情。

##### [为什么我不再使用 D3.js](https://medium.com/@PepsRyuu/why-i-no-longer-use-d3-js-b8288f306c9a)

D3 曾经是创建定制数据可视化的标准。在 jQuery 和 Backbone 时代，当创建“反应式”前端是一个极端的挑战时，它实现了电力、数据驱动和反应式动画。但是正如这篇文章所强调的——工具在不断变化，而且现在已经有能力使用 React 和 Vue 等强大的反应式框架来控制 SVG，因此很有必要重新考虑我们应该为 dataviz 使用什么工具。

#### 其他牛逼

##### [Remove.bg](https://www.remove.bg/)

一种托管工具，可自动识别照片中的背景并将其移除，留给您透明背景的裸露图像。似乎只对人的照片有效，但仍然非常非常酷！

##### [号召所有的网络开发者:这就是为什么你应该使用 Firefox](https://stories.jotform.com/calling-all-web-developers-heres-why-you-should-be-using-firefox-983f012d4aec)

随着上周关于 Edge 将使用 Chromium 的消息，对于我们为什么需要使用 Firefox 来帮助支持浏览器引擎的多样性并防止垄断，有各种各样的“负面”观点，但这是一个关于改用 Firefox 的好处的积极消息。

##### [静态对比服务器渲染](https://frontarm.com/articles/static-vs-server-rendering/)

JavaScript 开发人员，尤其是前端开发人员，过去在执行选项方面很容易。你的代码将在浏览器中运行，没有如果或者但是。这个故事变得更加复杂——随着使用 React、Vue 和 Angular 等框架的服务器端呈现(SSR)的最新趋势，以及使用 Gatsby 等工具的完全静态站点生成，有必要后退一步，了解它们之间的差异以及您的选择。

##### [2019 UI 及 UX 设计趋势](https://uxplanet.org/2019-ui-and-ux-design-trends-92dfa8323225)

非常侧重于设计，但值得通读，特别是如果你与设计师密切合作，以了解前端实施中的趋势。

##### [CSS &用于创建信息图的 JavaScript 片段](https://speckyboy.com/css-javascript-snippets-infographics/)

非常具体，但是有一种趋势是使用 HTML、CSS 和 JavaScript 使信息图交互，而不是固定在图像中。如果您必须这样做，您可能会发现这些有用的片段——每个片段都有自己的代码笔，以便于访问和修改。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
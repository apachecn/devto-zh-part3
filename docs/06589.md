# Web 组件之旅:错误的方式、缺少的部分和有希望的道路

> 原文：<https://dev.to/webpadawan/the-journey-of-web-components-wrong-ways-lacking-parts-and-promising-paths-1d5a>

在我之前关于 Web 组件的博客文章中，我描述了几个至今仍未解决的挑战性问题。我收到的反馈出乎意料的热烈，我很高兴被亚历克斯·罗素提到了 T2，他是最初提案 T5 的作者之一。有些人说我的笔记让人大开眼界，而其他人则提到了更多的问题。

摘下粉红色的眼镜并不是我想要实现的唯一目标。我的批评旨在找出痛点，并提醒达成共识的重要性。Jan Miksovsky 最近发表的关于 HTML `<slot>`元素历史的[博客文章是一个很好的例子，说明了我们可能会像 5 年前一样，通过再次推动提案而走进死胡同。](https://component.kitchen/blog/posts/a-history-of-the-html-slot-element)

今天，我想进一步扩展我的视野，从一个稍微不同的角度探讨同一个主题，探索 Web 组件迄今为止以及在可预见的未来被更广泛采用的途径。和以前一样，我依靠我的个人经验和观察，所以请随意添加您自己的笔记，如果您认为我错过了一些重要的东西，请纠正我。

上一篇文章中我想提醒你的另一件事是即将到来的关于 Web 组件的[“面对面”(F2F)会议](https://github.com/w3c/webcomponents/issues/802)，将有规范作者、浏览器供应商和用户参加。是的，在议程中有一个位置专门用于真正的用例。这是个好消息，我期待着会议记录一旦发表就能看到。

## 走错了路

2016 年初，当我开始使用 Web 组件时，并没有太多的例子。对我来说，当时唯一的灵感来源就是[高分子入门套件](https://github.com/Polymer/polymer-starter-kit)。从那时起，它发生了很大的变化，并被基于 LitElement 的继任者 [PWA Starter Kit](https://github.com/Polymer/pwa-starter-kit) 所取代。但简而言之，它们做的是一样的:将`<my-app>`组件放在`index.html`中。

将整个 SPA 封装到 web 组件中是一些人开始梦想抛弃框架的地方。在这个非常时刻，在我看来，他们开始陷入使用不当模式的陷阱。在这里，我分享 Mattia Astorino 提出的担忧。从我之前的帖子中，你已经知道过度使用 Shadow DOM 会如何反击。

进一步讲，每个 SPA 都需要一个客户端路由。是的，人们也一直在使用 Web 组件进行路由。在我以前的团队中，我们使用 [`<app-router>`](https://github.com/erikringsmuth/app-router) ，这是 2014 年的一个普通 v0 定制元素，也是我参与的第一批开源项目之一。当时真的很喜欢它的声明式路由方式。

但是今天这听起来对我来说很荒谬:首先我们在注册一个定制元素的时候运行 JS，然后每次用户导航的时候我们从 DOM 中搜索匹配的路线[并再次运行 JS。是我错了还是链条上多了一环？声明性本身是好的，但是试图在任何需要的地方使用 HTML，或者以其他人使用 JSX 的方式使用 HTML，又是一个严重的错误。](https://github.com/erikringsmuth/app-router/blob/6ad9c9ac2d877d1e4a64b6f883baa3991a6200ed/src/app-router.js#L216-L223)

我们必须重新发明的下一个特定于框架的轮子是一套用于加载数据和管理应用程序状态的工具。在聚合物领域缺乏最佳实践。简而言之，我们应该使用 [`<iron-ajax>`](https://github.com/PolymerElements/iron-ajax) 来触发来自 DOM 的 API 请求，然后将数据再次存储在 DOM 中——这完全是在捣乱。

有人会说，一旦你被锁定在一个 web 组件集合中，倾向于尽你所能地推进它。这可以通过以下事实来解释:Polymer 严重受限于 HTML 导入(感谢 [Mozilla](https://hacks.mozilla.org/2014/12/mozilla-and-web-components/) 放弃了它们)，并且基于“模型驱动视图”概念的一些假设，灵感来自关于[“声明式复兴”](https://addyosmani.com/blog/the-webs-declarative-composable-future/)的梦想。

说到 HTML，HTML 模块提案仍有机会改善我们的开发者体验，并允许更多地使用`<template>`。这是 Edge 团队对 Chromium 做出贡献的领域之一。虽然这可以帮助*以更好的方式创建* Web 组件，但我们不应该再采取那些错误的方式*使用*它们。

## 缺少的部分

回到 2019 年，今天 Web 组件有哪些比较好的用例？您首先想到的可能是 UI 组件，尤其是复杂的表单控件。令人惊讶的是，定制元素目前并没有被设计成在提交标准的`<form>` HTML 元素时被拾取。Chrome 团队提议的[表单参与 API](https://github.com/w3c/webcomponents/issues/187) 有望解决这个问题。

当查看定制元素 API 的建议更新的[列表时，浏览器供应商似乎需要做大量的工作来达成一致并实施。在此之前，如果没有类似](https://github.com/w3c/webcomponents/issues/187#issuecomment-467314213) [`<iron-form>`](https://github.com/PolymerElements/iron-form) 元素的变通办法，我们将无法使用 HTML 表单验证和提交。在我看来，一旦这个问题最终得到解决，Web 组件将会拥有更多的受众。

与在表单中使用 Web 组件相关的其他问题很少。其中之一是 Safari 中阴影树内表单元素的[破损表单自动填充](https://bugs.webkit.org/show_bug.cgi?id=172567)。另一个问题是，LastPass 或 1Password [等密码管理器不支持影子 DOM 中的](https://twitter.com/chadhikes/status/1079865767192084480) `<input>`元素，因为它们可能依赖于全局 API，如`document.querySelector()`。

我们需要记住的另一个影子 DOM 问题是它如何影响可访问性。这是罗布·多德森描述的[，但是让我用两个词重复一下:影子树引入了文档中 id 的边界，这影响了 ARIA 属性，比如`aria-labelledby`。这有时会使组件变得更难访问和粒度化。](https://robdodson.me/the-future-of-accessibility-for-custom-elements/#butwaittheresmore)

当在影子 DOM 中使用 IDs 时，IDs 的类似问题破坏了 SVG 中的内部[引用。例如，`<use>`元素不工作是因为`href`引用是如何被解析的。好消息是，至少](https://github.com/w3c/webcomponents/issues/179#issuecomment-438838649) [Safari 有一个解决方案](https://github.com/w3c/webcomponents/issues/772#issuecomment-467191544)被认为可以修复这个特定的错误——这个方案仍然需要被所有其他浏览器厂商采用，然后反映在标准中。

正如你所看到的，至少有两个看似完美的案例需要用 Web 组件来解决——即表单元素和图标——已经或多或少地被阻塞了一段时间。在我看来，这些缺乏的部分多年没有被覆盖(包括我在上一篇文章中已经提到的问题)对新标准的扩展伤害最大。

## 前途光明的道路

那么 Web 组件最终是如何被采用的呢？浏览器支持(至少对于基础来说)现在很好。我们今天真正缺乏的是在生产中使用原生组件模型的真实例子——最好是在大型项目中——以及作为其逻辑结果的最佳实践。这反过来导致*对 Web 组件*缺乏信任，如此循环往复。

当然，YouTube 是一个例子，它也是一个用聚合物建造的完整的温泉浴场。但到目前为止，我们观察到的大多数抱怨是，它在 Firefox 和 Edge 中运行缓慢，导致 T2 决定将 V0 规格的移除推迟到 Chrome 75。最后，YouTube 仍然没有使用原生的 Shadow DOM，而是使用了 polyfill(并且很可能在未来继续这样做)。

我想强调的另一个例子是 GitHub，它使用定制元素切换到了[,作为从 jQuery 转移的努力的一部分。特别棒的是定制元素非常适合将内容保持在 HTML 和渐进增强中的想法。请看](https://github.blog/2018-09-06-removing-jquery-from-github-frontend/#custom-elements)[细节-对话框](https://github.com/github/details-dialog-element)和[细节-菜单](https://github.com/github/details-menu-element)元素，它们是这种哲学的很好的例子。

继续寻找理论上 Web 组件可以加入的宣传列车，今天的座右铭似乎是[设计系统](https://twitter.com/css/status/1113550993961172993)。可重用性和与框架无关是我们不应该低估的优势；这就是为什么[特斯拉](https://twitter.com/alisonailea/status/1114203815211753472)选择了网络组件。随着时间的推移，我们会看到有多少人考虑下同样的赌注。

企业公司可能是 Web 组件的最强大受众，这是我们从 JS 2018 调查的状态[中看到的——检查聚合物的数字。让我提一下](https://2018.stateofjs.com/front-end-frameworks/overview/tools-company-size) [ING](https://dev.to/thepassle/ing--web-components-aef) 及其开源爱好者团队，他们创建了 [open-wc](https://open-wc.org) ，红帽赞助了[pattern fly](https://github.com/patternfly/patternfly-elements)elements library，这些都是大公司的例子。

一个有希望的途径是使用 Web 组件来构建一个架构，并嵌入部分应用程序。这个概念被称为[微前端](https://micro-frontends.org/#the-dom-is-the-api)。像[【麻辣 iframes】](https://twitter.com/davatron5000/status/1116795270400864257)这样的自定义元素，我们应该在多大程度上使用是值得商榷的。但是至少要记住 DOM 和 CSS 不再是全球性的了。

## 接下来是什么？

回到我们开始的地方，让我再次强调，Web 组件不是一颗银弹。它们不会取代 JS 框架，尽管有些人[声称相反](https://www.dannymoerkerke.com/blog/web-components-will-replace-your-frontend-framework)，或者甚至开始[说再见来回应](https://twitter.com/heydonworks/status/1115510306522959873)。事实上，这种有偏见的说法通常弊大于利。这就是为什么我想把我的 5 美分加入到正在进行的讨论中，并把它推向正确的方向。

Web 组件现在可以安全地使用了，尽管所有未解决的问题都需要进一步的标准化，我已经尝试将其系统化。预先警告是预先准备好的，我希望你足够勇敢，甚至不要害怕 Chrome [崩溃](https://github.com/vaadin/vaadin-grid/issues/1530)或[渲染问题](https://github.com/vaadin/flow/issues/5025#issuecomment-462668991)，这些问题已经影响了几个稳定版本。有些事情需要稳定下来，但毕竟没那么可怕。

在最近关于 MDN web docs 页面标题在 React 中被重写的圣战中，David Flanagan 承认开发人员缺乏专业知识，这对我来说也不是一个真正有效的借口。只要大多数前端团队坚持众所周知的堆栈，我们将如何采用例如 CSS 网格布局，或 ES 模块也支持本地？

因此，让我们接受挑战，并把它作为一个先锋的机会，用 web 平台提供的本地组件模型构建全新的事物。现在 2019 年了，是时候尝试一下自定义元素和阴影 DOM 了。它们本身可能不是“下一件大事”，而是下一件大事赖以建立的坚实基础。

对于任何对长期解决方案感兴趣的人，我建议在开始一个新项目或重构现有项目时，根据实际需要考虑定制元素、影子 DOM 或两者都考虑。此外，我很想听听你从 Web 组件中学到了什么——包括你自己的痛点，因为我意识到这并不都是好事。

最后，我想鼓励任何人对名为[Web Components:the right way](https://github.com/mateusortiz/webcomponents-the-right-way)的“牛逼”列表做出贡献。正如我之前[宣布](https://dev.to/webpadawan/web-components-the-right-way-project-relaunch-20d7)的那样，我是那个项目的维护者，并尽力使它成为一个或多或少完整和最新的知识库。和往常一样，我非常感谢听到你的反馈和任何关于这篇博文的评论。
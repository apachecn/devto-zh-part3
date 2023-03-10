# React 适合那些认为 React 之类的东西很奇怪很难的人

> 原文：<https://dev.to/magnificode/react-for-people-who-think-things-like-react-are-weird-and-hard-14nl>

*标题无耻地源自[克里斯·科伊尔关于咕噜的精彩帖子。](https://24ways.org/2013/grunt-is-not-weird-and-hard/)T3】*

在工程的世界里，我们倾向于陷入事物的技术层面。我想写一篇关于 react 的介绍文章，给我们的同事、项目经理、客户经理、营销人员等一些启发。他们可能对 React 的技术方面不太了解，但仍然很想知道它是什么，它解决了什么问题，或者没有解决什么问题。

## 先讲一点历史

[![Falling asleep](img/815790bbc952d02024bad37e765e7af9.png)](https://i.giphy.com/media/HlqvH9JrahLZ6/giphy.gif)

是的，是的，我知道，历史是超级无聊的，但我认为我们了解一点 React 的来源是很重要的。这将为我们提供有用的见解，让我们了解 React 是为了解决什么问题而设计的。让我们首先使用 Reacts 本身的定义来提供一些上下文:

从 React 文档中:

> React 是一个声明式的、高效的、灵活的 JavaScript 库，用于构建用户界面。它允许你用称为“组件”的小而孤立的代码片段组成复杂的 ui。

在继续之前，让我用 Tyler McGinnis 博客中的一句非常简洁的话来解释一下声明式编程的含义。

> 声明式编程是“用符合开发者心智模型而不是机器操作模型的语言进行编程的行为”。

这个图书馆是由脸书的团队建造的。特别是在脸书，他们处理用户定向广告的应用程序变得如此笨拙，以至于那里的开发人员建立了一个系统来使他们的代码更易于维护。这一点，加上脸书对 Instagram 的收购，促使开发者将这项技术与脸书分离，以便它可以开源。

React 最初是作为对新兴的 MVC(模型视图控制器)方法的回应而提出的，许多 web 应用程序都朝着这个方向发展。Angular 非常受欢迎，Backbone.js 你们中的一些人可能还记得，它的触角伸到了 WordPress 的各个地方。React 决定采用这些方法，并专注于成为工程师构建用户界面的声明性方式。

## 爽，那么是什么？

既然我们知道了构建的开发人员对它的意图是什么，我们就可以探索它在实践中是什么。

集合的反应不同于角度，或主干，或任何其他数百万个框架，是什么将任何框架与任何其他框架区分开来。领养。React 得到了非常积极的维护，不断有新的特性出现，继续帮助人们制作声明性的、动态的用户界面。

不必深入讨论，React 本身并不能为给定的网站提供任何性能或安全优势。但是结合许多其他工程实践，当用户界面需要复杂的交互和数据存储时，React *可以*帮助创建一个更易维护的应用程序。

在我们发现过程的早期，我们总是与客户坐下来，试图理解他们的业务目标、关键绩效指标和其他我们可以用来衡量成功的指标。让我们来看两种不同的场景，一种是 React 可能不是最好的，另一种是最好的。

### 场景 1:“哦闪亮”的场景

[![Man distracted by something shiny](img/d4ba5ebde8fc282f3fa271059e86439c.png)](https://i.giphy.com/media/81MHl1DY9kxMI/giphy.gif)

你遇到了 X 公司。X 公司有大量的内容。X 公司希望利用我们出色的 UX 和设计团队找到创造性的方式来展示和分享这些内容。他们有复杂的帖子布局，一些不同的帖子类型，视频，社论，也许是画廊。他们走近你说“嘿！我们有所有这些内容，我们想要一个美丽的用户体验，一个简单的方式来编辑，发布和分享我们的内容。哦，我们也听说过 React，我们喜欢使用 React 的网站的快捷性，让我们确保您在 React 中为我们构建这个！”

在这里，我们发现自己处于这样一种情况，X 公司对开发生态系统的了解刚好足以推荐 React。从社区的角度来看，这很棒，非常酷，JavaScript 框架拥有如此广泛的知名度，以至于我们领域之外的人都对它感兴趣。值得注意的是，客户推荐的框架或技术永远不应该被嘲笑。我们的工作就是帮助他们理解为什么他们是对的，或者为什么有更好的解决方案。

在我们与 X 公司的案例中，React 不是最好的解决方案，原因有几个。

*   自行反应并不能让网站变得爽快。还有几十种其他更微妙的技术正在被用来让脸书、Instagram、Pinterest 或 Grubhub 等应用程序看起来如此时髦。
*   这个客户端提出的所有需求都不需要一个框架来构建一个声明性的动态用户界面。Client X 描述的所有内容都可以在 WordPress 内部完成，很可能需要借助 AJAX 或 Web History API 之类的先进技术。网站速度或页面转换并不意味着网站应该依赖 React。

### 场景 2:“我没有一个聪明的名字”场景

Y 公司接近你。他们的业务与 x 公司的需求相似。大量的内容，多种帖子布局和类型。让 Y 公司与众不同的是，他们还允许用户注册他们的网站，这让这些用户能够喜欢、反应、组织甚至发布他们自己的内容。

Y 公司需要一个网站，用户可以在这个网站上与他们产品的各个方面进行基本的交互，这改变了网站中存在的数据的形式。帖子#1 有 100 个赞，用户 A 将帖子#1 保存到他们的“牛逼帖子”文件夹中，该文件夹对于该用户是唯一的。这些数据是高度动态的，是由网站的用户塑造的。

我们现在进入 React 的领域，React 是一个声明性的 JavaScript 库，它可以获取这种动态的、不断变化的数据，并通过设计良好的界面组件在前端显示出来。Y 公司有一个*可能*的 React 用例。这里需要注意的是，我们绝对可以**在 WordPress 的环境中构建这些功能。问题变成了；React 会创造一个比 WordPress 更易维护、更易扩展、反应更灵敏的应用程序吗？**

*   React 将为开发人员创建一种易于管理的方式，在站点的不同部分之间传递数据，而无需多次调用数据库来检索数据。
*   React 将使工程师更容易构建在前端的多个视图之间共享的组件。例如，每个帖子的卡片，显示标题、作者、点赞数以及对该帖子的反应，该帖子显示在主页、用户特定的“精彩帖子”文件夹以及搜索结果中。
*   React 将帮助开发人员对各种视图使用相同的模板，同时只在必要时更改数据。

## 什么反应**不做什么**。

React 本身只是一个 JavaScript 库，使开发人员更容易构建声明性的动态用户界面。它不会执行以下操作:

*   固有地提高站点性能
*   React 没有提供在页面之间轻松转换的方法。考虑 WordPress 如何处理页面间的导航。当构建一个 React 应用程序，或者前端带有 React 的无头 WordPress 时，开发者*必须*构建出在页面、帖子、自定义帖子类型等之间导航的功能。**分开**。
*   React 没有附带一种方式，默认为消费数据。必须建立连接来从一个源提供 React 应用程序数据，不管这个源是 WP API 还是另一个 REST API 端点。必须建立连接，并且必须对数据进行整形并将其传递给应用程序的各个部分。
*   React 本质上并不比 alternative X 更安全。不管我们选择使用什么库或框架，安全性总是可以提高的。

以上几点解释了为什么在大多数情况下，对 React 应用程序的评估往往比 WordPress 版本高。WP 本身包含了很多方面。而对于 React 应用程序，这些我们认为理所当然的东西必须从(接近)零开始构建。

## 为什么要用 React 呢？

从工程的角度来看，React 允许我们更容易地在更大的项目整体中设想一个组件，抽象出功能和风格，并以一种清晰的逻辑方式构建该组件。实际上，React 更像是一个通向一堆其他技术的大门，这些技术可以为请求、消费和创建*海量*数据的应用程序提供性能优势。

React 是一个工具，它帮助我们开发人员开发具有复杂逻辑的应用程序，并且更容易与用户进行细微的交互。

## TLDR；我如何知道 React 适合我？

我认为重要的是，在我们出售一个项目之前，我们*考虑**可能*帮助任何给定客户解决问题的技术。我们需要理解项目的业务目标和完整的功能需求，而不是在我们能够进行完整的发现之前就进行规定。到那时，也只有到那时，我们才能规定技术，或我们可以利用的一套技术，使工程更有效，并使网站尽可能好。无论如何，这里有几条非常宽泛的指导方针，解释了在什么情况下*考虑*做出反应是安全的..

*   客户业务模式是否规定许多用户将直接与他们的数据交互(反应、喜欢、用户管理自己内容的帐户特定部分)？-考虑做出反应。
*   用户界面是否足够复杂、可靠并直接受数据影响(用户创造的喜欢或反应、图形、图表、内容的数量)？-考虑做出反应。

用于此帖子的资源:

*   [https://blog . rising stack . com/the-history-of-react-js-on-a-timeline/](https://blog.risingstack.com/the-history-of-react-js-on-a-timeline/)
*   [https://reactjs.org/docs/getting-started](https://reactjs.org/docs/getting-started)
*   [https://overreacted.io/react-as-a-ui-runtime/](https://overreacted.io/react-as-a-ui-runtime/)
*   [https://tylermcginnis . com/impressive-vs-declarative-programming/](https://tylermcginnis.com/imperative-vs-declarative-programming/)
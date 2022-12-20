# 如何对状态和效果使用基本的 React 钩子

> 原文：<https://dev.to/progresstelerik/how-to-use-basic-react-hooks-for-state-and-effects-4bld>

随着 React Conf 2018 的推出，我们了解到 React 16.7 版本将提供一个重要的新功能:React Hooks。请和我一起浏览工作示例，并“迷上”React。

我们正看着 React 慢慢变得更加固执己见，随着最新的功能，我敢说它变得比以往任何时候都更像一个框架。React 通常被认为是一个功能不全的框架，而更像是一个库，可以很好地呈现 DOM 并管理其状态。这个生态系统有一些固执己见的工具，仍然被广泛用作许多 React 应用程序的标准，例如 Redux。现在钩子还不是 Redux，但是对于那些最近发现 Redux 的人来说，它们是另一种做事方式，Redux 建立在 Flux 模式之上，是每个应用程序管理状态的一个要求。

我相信钩子是通向 Redux 的桥梁，也是一种在不引入 Redux 的情况下管理简单状态的方法。丹·阿布拉莫夫在 React Conf 2018 上向我们展示了我们的第一个工作示例，这完全有道理，因为他也是 Redux 的创造者和[主要贡献者。我认为这在某种程度上有助于钩子的使用，并表明他们正在考虑如何将更好的状态管理引入 React core，以及如何确保钩子将补充并优先使用 Redux。](https://github.com/reduxjs/redux/graphs/contributors)

随着代码拆分、悬念、懒惰、备忘录和 now hooks 等东西的引入，我认为我们在 2018 年看到的变化是在考虑开发者和性能的情况下完成的。与其他框架的竞争也正在成为现实。我认为这种竞争环境迫使 React 变得更加轻量级。如果我们将会看到一个更加固执己见的 React，具有类似框架的特性，我真的很高兴 React 团队正在对 API 进行这些最新的添加。我问了几个人，他们是否认为 React 正在成为一个框架，几个人说是的，一个人只是叹了口气，另一个人说，直到他们使路由器成为 React 的一部分，我不认为它是一个框架。

## 钩为胜

随着 [React Conf 2018](https://conf.reactjs.org/) 的到来，我们了解到，随着 16.7 版本的发布，一个新的功能将会出现: [React Hooks](https://reactjs.org/docs/hooks-intro.html) 。

目前你可以阅读关于[钩子](https://reactjs.org/docs/hooks-intro.html)，查看 [RFC(请求评论)](https://github.com/reactjs/rfcs/pull/68)，并使用`npm install react@next react-dom@next`来体验新特性。钩子代表了 [React](https://reactjs.org/) 的一个严格的附加特性，它引入了[无中断变化](https://reactjs.org/docs/hooks-intro.html#no-breaking-changes)，这意味着它是完全选择加入的。它为编写基于类的组件提供了另一种选择，即简单地使用状态和访问生命周期方法。

因为钩子将通过[逐步采用策略](https://reactjs.org/docs/hooks-intro.html#gradual-adoption-strategy)来实现(与现有代码并存)，这给了团队时间来重构他们的代码，或者在他们使用钩子引入新的功能组件时简单地留下遗留代码。钩子已经被冠以“React 的未来”的称号，但是也注意到在可预见的将来，类将会得到支持。钩子给功能组件带来了我们曾经只能用类做的事情。比如能够和[状态](https://reactjs.org/docs/hooks-state.html)、[效果](https://reactjs.org/docs/hooks-effect.html)以及具体的上下文一起工作。

过去，一些 React 开发人员经历过何时使用、何时不使用类的困惑。 [@SophieBits](https://twitter.com/SophieBits) 在[“今天和明天做出反应”](https://www.youtube.com/watch?v=V-QO-KO90iQ)的谈话中评论说，上课对于人类和机器来说都可能是“[艰难的](https://reactjs.org/docs/hooks-intro.html#classes-confuse-both-people-and-machines)我认为只要有可能，我们将会看到更多这样的事情从课堂上消失。在某些情况下，这实际上是正确的做法——关于这一点的立场可以追溯到几年前，比如:[“如何使用 React 类在晚上睡觉”，](https://medium.com/@dan_abramov/how-to-use-classes-and-sleep-at-night-9af8de78ccb4)尽管我们有时必须在当前的 React 中使用它们。但是这个问题现在正在处理，我们已经看到开发者有强烈的意见，并且在他们可以的时间和地点使用大多数功能组件。

钩子让功能组件在 React 中占上风。React API (16.7.0-alpha) 中已经有的一些附加钩子[包括:useReducer、useCallback、useMemo、useRef、useImperativeMethods、useMutationEffect、useLayoutEffect、uhh 我把它们都拿到了吗？把重点讲清楚，这是有用的东西！](https://reactjs.org/docs/hooks-reference.html)

## 那么什么是钩子，我们如何使用呢？

描述钩子最简单的方法是简单地给你看两个例子，一个是需要访问状态和生命周期方法的类组件，另一个例子是我们用一个功能组件实现同样的事情。我已经通读了钩子的提议，因此我将使用 Dan Abramov 在文档中使用的类似代码示例。

你可以直接阅读文件，我建议你这么做。然而，我想给你提供一个工作实例，你可以触摸，感受和摆弄。我通过弄脏我的手学得最好。出于这个原因，我想提供文档中说明的相同示例，但每个阶段都有一个 [StackBlitz](https://stackblitz.com/) 演示，以便您可以自己测试。

下面的 GIF 展示了我们将在下一节看到的类和函数组件示例之间的区别:

[![Functional vs Class](img/1b7471a03bdf8887b1005e98e3f45b14.png "State and Effect (Hooks)")](https://res.cloudinary.com/practicaldev/image/fetch/s--G6SKtkKQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/reactstatehook-04n054a108f6e739248898fad28d2ed3da9c6.gif%3Fsfvrsn%3D137e3710_1)

### 并列`useState`的例子

下面我们遵循 React 文档中提供的规范计数器示例。它在一个`Counter`组件中包含一个按钮。单击后，它将状态提升一级，并更新`state.count`以进行渲染。

首先，我们看看如何用一个基于类的组件来做这件事，只需要使用`setState`:

关于基于类的组件，首先要注意的是，类语法使用了一个引用`this`关键字的构造函数。在构造函数中，我们有一个状态属性。我们还使用`setState()`更新计数。

现在，让我们看看如何用钩子做同样的事情，使用一个功能组件:

在功能组件示例中，我们有一个额外的导入`useState`。没有更多的类语法或构造函数，只有一个`const`。它的赋值设置了默认值，不仅提供了`count`属性，还提供了一个用于修改状态的函数`setCount`。这个`setCount`指的是一个函数，你可以随意命名。

功能组件`incrementCount`方法更容易阅读，直接引用我们的状态值而不是引用`this.state`。

### 并列`useEffect`的例子

当更新状态时，我们有时会有副作用，这些副作用需要随着每次更改而发生。在我们的计数器示例中，我们可能需要更新数据库，对本地存储进行更改，或者只是更新文档标题。在文档中，React 团队选择了后一个例子，以使事情尽可能简单易懂。所以让我们这样做，并更新我们的例子，使用新的钩子`useEffect`产生副作用。

让我们将这个副作用添加到我们现有的反例中，并再次看看用类和钩子来做这件事的老方法。我们将首先看看如何用一个基本的基于类的组件来实现这一点:

接下来如何用钩子做同样的事情:

既然我们引入了额外的行为，我们开始看到越来越多的证据表明切换到钩子提供了一种更干净的方式来处理状态和副作用。我们只需调用一次`useEffect`就可以实现类组件中两个独立的生命周期方法。在功能组件的例子中，我们确实多了一个导入，但是如果我们能够去掉类语法，一个额外的生命周期方法，并使我们的代码更干净、可读性更好，这是完全值得的。

## 想怎么叫`useState``useEffect`就怎么叫！

就像使用`setState`一样，你可以任意多次调用`useState`。让我们切换到一个示例，展示一个稍微复杂一些的情况，我们有一个显示在页面上的名字，一些允许名字被更改的输入，我们希望控制名字和姓氏。我们可以创建两个独立的属性，每个属性都有自己的更新或设置功能，只需在每个属性上调用`useState`来设置默认值。

在下面的 GIF 中，你可以看到它的样子——以及基于类的版本的样子，我们将在下面深入探讨。

[![Functional vs Class](img/d75588d33b104334e889e89e2e9682ae.png "State and Effects (Hooks)")](https://res.cloudinary.com/practicaldev/image/fetch/s--xNKjjeDM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/reactstatehook-06n07.gif%3Fsfvrsn%3D18e0cae0_1)

如你所料，我们还为每个名字提供了一个更新功能，这样你就可以独立地处理对它们的修改。

让我们看看基于类的组件的代码:

现在钩子也是这样:

我不会再重复所有的区别，但是我想让你看到一个稍微复杂一点的例子。希望你开始看到使用钩子的好处。

让我们对这个例子再做一次修改，使用`useEffect`将我们的名字保存到本地存储器，这样我们在刷新页面时就不会丢失我们的状态。

让我们看看基于类的组件的代码:

现在钩子也是这样:

## 总结起来

希望这些互动的例子能让你了解`setState`和`useEffect`的基础。我计划发布更多关于 React 钩子以及如何将它们与我们的 [KendoReact 本地 React 组件](https://www.telerik.com/kendo-react-ui/)一起使用的文章。如果你是 React 新手，我们在 Telerik 博客上有更多的内容，特别是关于[的内容，其中包含了大量关于 React 及其生态系统的信息。请浏览我们的文章和产品，如果您对 React 相关主题的文章有任何问题或想法，请告诉我。](https://www.telerik.com/blogs/all-things-react)

感谢您花时间阅读 React Hooks——以下是您可以在网上找到的关于这个主题的附加信息！

## 早期文献和挂钩文章:

[React Docs on Hooks](https://reactjs.org/docs/hooks-intro.html)

[Making Sense React Hooks](https://dev.to/ben/making-sense-of-react-hooks-2in4-temp-slug-3153604)

[理解 Hooks in React A Deep Dive](https://blog.bitsrc.io/understanding-hooks-in-react-a-deep-dive-d5d5dc88ecd9)

[一个简单的 Intor into Hooks](https://dev.to/dceddia/a-simple-intro-to-react-hooks-1okm-temp-slug-5362079)

[React 16.7 Hooks 教程](https://dev.to/techiediaries/react-167-hooks--with-axios-and-django-api-part-1-217f-temp-slug-436890)

[Hooked(formid table)](https://formidable.com/blog/2018/hooked-facebook-react/)

[Flux 无需大惊小怪:从容器到 Hooks](https://medium.com/iqoqo-engineering/flux-without-the-fuss-from-containers-to-hooks-bda35c622e3f)

## 视频资源上勾

[React Conf 2018 Day One Talks](https://www.youtube.com/watch?v=kz3nVya45uQ)

[使用自定义钩子跨 React 组件共享复杂逻辑](https://egghead.io/lessons/react-share-complex-logic-across-react-components-with-custom-hooks)

[使用 React useRef 和 useEffect 钩子访问和修改 DOM 节点](https://egghead.io/lessons/react-access-and-modify-a-dom-node-with-the-react-useref-and-useeffect-hooks)

[使用自定义钩子跨多个 React 组件共享逻辑](https://egghead.io/lessons/react-share-logic-across-multiple-react-components-with-custom-hooks)

[使用 useState React 钩子](https://egghead.io/lessons/react-use-the-usestate-react-hook) [测试使用 React 钩子的 React 组件](https://egghead.io/lessons/react-test-react-components-that-use-react-hooks)

[React 钩子一个完整](https://www.youtube.com/watch?v=jd8R0a2Ur8Q)
# 反应钩子，我的游戏攻略

> 原文：<https://dev.to/swiip/react-hooks-my-introduction-563e>

<figure>[![](img/3fafddc7c0f55de98131dbfa75454345.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fjBdRBTr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/637/1%2AGF8wxCup_DMmn5qgsuN2zg.jpeg) 

<figcaption>不好意思，这有点容易😂</figcaption>

</figure>

这么多已经发表了，怎么写一篇关于 React Hooks 的文章？这是我在开始这个之前问自己的问题。我几乎放弃了这个想法，然后终于找到了一堆继续下去的好理由:

*   首先，如果你不写别人已经写过的东西，你就什么也写不出来。
*   我打算在一个现有的项目上使用 hooks 开始我自己的旅程，所以我需要一个切入点。
*   由于受到大自然的推动，长时间使用 Recompose(见下文),查看了 React Fiber，并有机会在早期测试钩子，我可能对这个新特性有一些有趣的见解。

所以我将简单地开始这篇文章，考虑到你知道 React 的基础知识，但对钩子一无所知。

### 反应出钩子之前的技术状态

创建 React 组件有两种主要方式:创建类或函数。

function 组件更时尚，因为它更短，非常适合热爱函数式编程的社区。更进一步说，这是 React 概念的直接应用，即组件是输入属性到标记的直接转换。

[![](img/31ee11a8263a5a62ad0b7ab8cbc721a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rWEPr3tC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXXGm5h7lYHVSSIxD4WvhIA.png)

但是在 hooks 之前，功能组件真的很有限。没有状态，没有生命周期，你只能在非常简单的用例中使用这些组件。

为了拥有完全的控制权，你必须将你的组件转换成类版本。

[![](img/b552d4fe3fe428e195fc56588fff54e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9jlA9EUf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxN8hrRwXW-K8vYqOcKmVLA.png)

我不想进入永无止境的“函数与类”的争论，我们只能说，每次需要状态变量时从一个切换到另一个是痛苦的(除非你在你的编辑器中有一些像 Webstorm 一样的花哨功能)。

### 高阶组件(HoC)

React 社区出现了一种模式。它现在非常普遍，令人惊讶的是在其他框架中没有对等物:HoC。

基本上，它存在于一个函数中，该函数接受一个参数中的组件并返回一个新的组件，该组件包装了您的组件并传递允许您使用特定功能的属性。

这种模式迅速传播开来，有多个开源库主要依靠 HoC 来访问 Redux 存储、注入 GraphQL 数据、获得 i18n 翻译等等。

这种模式有很多好处:

*   您自己的组件可以保持简单，大多数情况下，它们可以是功能组件。
*   您的组件使用的每个特性都可以很容易地识别出来。它甚至可以在几个组件之间共享。

那些最喜欢这种模式的人甚至想象用 HoC 做任何事情。有一个名为 [Recompose](https://github.com/acdlite/recompose) 的事实上的库专门用于这个概念。Recompose 提供了一整套基本的 HoC 来使用 React 的各个方面，您只需要用您需要的每个特性来组合您的 HoC。

[![](img/51a0b70dd3406c96ae742d352075398d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C4PS0vl3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AiImzLc-7Av0fTLzSNgGM4w.png)

我是 Recompose 的忠实粉丝，我用它构建了整个应用程序，完全没有使用任何类组件。这对于代码结构和逻辑重用来说非常好。

但是 HoC 也有真正的缺点，你必须明白:

*   组件封装的概念不容易理解。当我从头开始写一个的时候，我仍然需要片刻的思考。
*   在你写的代码周围封装 2、4、有时 6 或 8 个组件会有性能开销，这是事实。它可能不重，但它是真实的。
*   HoC 真的让你的调试面板看起来一团糟。让我想起了 JavaScript 回调地狱的黑暗日子！

[![](img/dc0f5b0b5b008112c180b4d7aa776913.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--01LrLYeE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2AycADQGTO94h2mfOP4RO6_g.png)

### 钩子！

哇，终于主话题了！我想解释一下 React 社区中这些用法的背景，以便理解钩子试图解决什么问题。现在我们准备好了！

[![](img/a7e375f52c4ec50332201d25149eaf4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZJI9qaaT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/610/1%2ARQo0Fw3qp1bKSOIDGpisBA.png)

> 钩子让你不用写类就可以使用状态和其他 React 特性。您还可以**构建自己的钩子**来在组件之间共享可重用的有状态逻辑。

那是直接引自包含钩子的 React 16.8.0 发布的[官方公告](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html)(没错，他们已经发布了！)

如果我尝试自己的解释，我会说有一组新的简单函数，您可以在功能组件中使用这些函数来访问所有 React 特性，这是您以前无法做到的。

[![](img/cbb1349df2f2de8ff1f1a9edcc16424f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PXUJtm19--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aab2RKZKShDthbVlRhDRYTw.png)

这些函数触发基本的 React 功能，您可以直接使用它们。你也可以[并被鼓励去做](https://reactjs.org/docs/hooks-custom.html)，把它们组合成更高级的。或者是您最喜欢的框架，它会使用它们来让您访问它们的特性。

有 3 个简单易记的基本要素:

*   *使用状态*为组件声明一个新的状态变量
*   *使用效果*与组件生命周期交互
*   *使用上下文*获得一个[上下文](https://reactjs.org/docs/context.html)值

完整的 API 可以在 React 官方文档中找到。

正如我已经深入介绍过的，你可以把钩子看作是 HoC 的好的部分的结合:组织和共享组件特性，而没有它们不好的部分:扩展的组件封装。

对我来说，作为一个 Recompose 的用户，hooks 让我最喜欢的一个库变得毫无用处，但是除了在它试图填补空白的框架中直接概括这个概念，一个库还能有什么更好的结局呢？

对于那些关心重新组合本身的人来说，你必须知道创作者 [Andrew Clark](https://medium.com/u/6025bd347b9a) 现在在 React 核心团队中，他亲自编写钩子代码，[官方声明它注定会被放弃](https://github.com/acdlite/recompose#a-note-from-the-author-acdlite-oct-25-2018)。大到他了！

在这一点上没有说的是挂钩是完全复古兼容的。类组件仍然像以前一样工作，HoC 也仍然一样工作。React 核心团队甚至不建议开发人员激进地转换他们的代码库(这正是我不会做的😇)而是建议在考虑重构旧组件之前，先在一些新组件上习惯它。

### 为什么这么刺激？

如果你像我一样是一名 JavaScript Web 开发人员，也许你已经想到了你可以从这个新特性中获得的所有好处，以及为什么它是一个非常重要的特性，而不仅仅是一个新的小工具。

然而我认为总结它们是一个很好的练习。让我们想象一个充满钩子的 React 世界会是什么样子(我非常肯定社区将会广泛采用它们):

*   功能组件和类组件之间分割的结束，至少在同一个项目中。功能和类组件变成了个人风格的选择，就像空格对制表符缩进或尾随逗号…
*   放弃 HoC 以节省复杂性、性能开销和调试混淆。
*   如果没有阶级，就没有这个🎉。
*   由于组件逻辑现在只是函数调用，它可以在其他文件中提取，共享，重用和测试的方式比以前更好。

最后一点是最重要的。这是我通过大量使用重组已经尝到的味道(是的，有一个“a”)。我无法用语言来告诉你这有多神奇，我很高兴看到我现在可以使用 React 本身来实现同样的东西！

### 工作原理(大致)

从用户的角度来看，你只要从你的功能组件中调用 use** functions 就可以了，仅此而已。但是，当您知道这个组件函数将在每次变更检测中被调用时，它是如何可能的，甚至是如何执行的呢？

在这一点上，你发现使用钩子是有规则的。

> 钩子不是标准函数。

简而言之:你只能从功能组件中调用钩子，而且每次执行时你都必须使用相同数量的钩子，并以相同的顺序执行(所以你不能把钩子放在一个条件或循环中)。

看看伪代码中的钩子实现是有意义的。

<figure>[![](img/82d379b5efd1b7331f2df7a13b3821ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JZNDa4VT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALnhFQjPigIdfVwW_unXoKg.png) 

<figcaption>原文由[杰米凯尔](https://medium.com/u/cc2eaf4f2cd2)</figcaption>

</figure>

撰写

所以每个钩子函数在一个共享数组中返回 React 的运行实例。React 控制着对组件的调用，所以它完全知道从哪个组件使用了钩子。

hooks 数组允许 React 将前一个调用匹配到同一个钩子的下一个调用，这解释了为什么有一个关于数量和顺序的规则。这种实现可以(并且已经)讨论过，但是 React 核心团队认为这是考虑几种解决方案的最佳选择，正如 [Dan Abramov 很好地解释的那样](https://overreacted.io/why-do-hooks-rely-on-call-order/)。

更加全球化，因为钩子不能在任何地方、任何顺序、任何时间等被调用。：

> 钩子在设计上并不是一种安全的模式。

这是 React 团队做出的牺牲，允许一个非常简单的 API 用于绝大多数人。

关于性能，我们在多次调用的 render 函数中添加代码。这是有代价的，作为 React 开发者，我们有责任不在这些函数中进行密集的计算。

然而，我们应该只从这些函数中调用一些钩子，我们不应该做更多的事情。相反，我们获得了不定义和实例化整个类的开销，这可能更重要。

最后，当性能真的很重要时，一些钩子有记忆选项，允许开发人员精确定义何时运行代码。

### 我们来写点钩子吧！

这是一篇相当长的文章，但我们几乎没有触及主题。在整个应用程序中使用钩子(并且只使用钩子)会带来更多的问题。

这就是为什么这只是第一篇关于这个主题的理论性文章。我计划在我转向 hooks 的过程中再写一篇。

作为一个适度的悬念，以下是我打算在接下来的文章中解决的问题:

*   第三方框架中的挂钩(2019 年初)
*   组件间逻辑共享的示例
*   重新组合和挂钩之间的比较
*   钩子和 SSR 与 Next
*   测试定制钩子和带钩子的组件

* * *
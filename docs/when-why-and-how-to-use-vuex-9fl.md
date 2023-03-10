# 何时为什么以及如何使用 Vuex

> 原文：<https://dev.to/napoleon039/when-why-and-how-to-use-vuex-9fl>

## 你会从这篇文章中得到什么？

本文试图教你高级、大型、复杂的 Vue.js 网站和 web 应用(web apps)中使用的热门商店 **Vuex** 。这绝不是一个完整而绝对的指南。它只是通知您*何时*应该考虑将 Vuex 添加到您的项目中，*为什么*应该在这种情况下使用 Vuex，最后*如何*第一次开始使用 Vuex。

本文中会有一些简单的例子，在这些例子中，Vuex 可能是不必要的。但是这些例子只是为了帮助你掌握一些概念，所以要记住这一点。

## vuex 是什么？

大多数 JavaScript 框架都有这些所谓的*存储*。你可能会多次遇到这个术语。这个词到底是什么意思？是一种存储吗？和电子商务有关的东西？

嗯，不完全是🤷‍♂️

商店可以说是在您选择的 JavaScript 框架中创建高级应用程序和网站的关键。不管那是 Vue.js，React，Ember 还是 Angular。现在，这并不意味着你绝对必须使用商店来创建一个高级的网络应用程序，不。你可以创建一个非常高级的网站，即使有这个特定框架提供的简单的内置功能。然而，在一个先进和庞大(更不用说复杂)的网站/网络应用程序的混乱中，商店给了你更多的控制权。利用商店可以让你组织某些东西，也就是状态。

网站或 web 应用程序的状态对于增加与用户的交互性是必要的。如果您曾经使用 props 将一些数据发送到子组件，并使用自定义事件将一些数据发送回父组件(使用`this.$emit`)，那么您所做的就是管理状态。例如，传递 props 来告诉子组件列表中的所有项目，并发出一个事件来告诉父组件某个项目已被删除，这就是管理该列表的状态。

因此，跟踪和管理我们网站的状态非常重要。Vuex 为我们做这个。

## 为什么要用 Vuex？

Vue 不限于使用特定的商店。许多商店可供我们使用。还有`Redux`，它作为一个状态管理库非常受欢迎，因为几乎每个使用 React 的人都使用它。但是即使可以用 Vue 用 Redux 等店，还是用 Vuex 比较好。

原因是 Vuex 和 Redux 一样，也受到 Flux 的启发，并且是为了利用 Vue 提供的开箱即用功能而构建的。首先，虽然 Redux 中的状态是不可变的，并且在改变它时会被完全替换，但是 Vuex 提供了一种非常具体的方式来使*的状态发生变化。*

使 Vuex 更强大的是，组件从 Vuex 存储中获取它们的状态，并可以在存储状态改变时反应性地高效更新。

Vuex 提供了这么多，在为你的 Vue 项目选择状态管理工具时，它应该是你的首选。

## 什么时候使用 Vuex 比较合适？

Vuex 功能强大、高效，可以为我们处理很多事情。所以我们应该多利用它，对吗？不对！

> 仅仅因为一个特性/工具/库提供了很多优势，并不意味着它应该一直被使用。

VueRouter 就是一个很好的例子。尽管它包含了一些非常有用的功能，但你可能不会在所有网站上使用。有些网站不需要路由器。也许有一个像 Express 这样的后端，它应该处理页面的路由，或者也许你有一个使用动态组件的单页面应用程序(SPA)。同样，并不是每个网站或网络应用程序都有理由使用 Vuex。

简而言之，这就像你买了一部智能手机，而你只是想要一个打电话和接电话的东西。这款手机有一个很好的摄像头，256GB 的存储空间，RAM 可以让你玩高端游戏。但是如果你只想打电话，你真的需要所有这些吗？

我们的 Vue 项目和 Vuex 也是这样。Vuex 提供的东西必须是必要的，否则非但没有帮助，反而会成为网站的负担。在某些情况下，您可以使用基本的 Vue 功能，有时您确实需要调用 Vuex 的强大功能。那么*什么时候*应该考虑使用 Vuex？

Vuex 的核心是一个存储，它将我们应用程序的所有状态保存在一个中心位置。通常，我们利用道具和自定义事件来跟踪状态。这很好，只要我们的网站有一些改变状态的组件。如果你的项目变得更大更复杂，会发生什么？

会有更多的组件。道具被传递给更多的组件，大量的自定义事件被触发。谁改变了这种状态，它应该是别的东西，但一些组件改变了它。等待....该组件如何访问它，是哪个组件？！！

你看，混乱！当一个网站有更多的组件和增加的复杂性，你需要组织如何改变状态。这是一个理想的情况，集中的 Vuex 商店会有所帮助。用于小型或简单应用程序/网站的 Vuex 就像我们的智能手机例子一样，有点矫枉过正。只有当你觉得你的项目很复杂，需要一些帮助来管理它的时候，才使用它。

## 如何开始使用 Vuex

我不会深入讨论 Vuex 的使用。这些是关于使用 Vuex 的一些基本概念。本节将有一些简单的例子来改变 Vuex 存储中的状态和检索状态。

第一步是安装 Vuex。

```
npm install vuex 
```

Enter fullscreen mode Exit fullscreen mode

最好将所有 Vuex 逻辑保存在一个单独的文件中。我们将把我们的逻辑保存在一个名为`store.js`的文件中。当然，你可以给它起任何你想要的名字。我们将把我们的存储对象放在这个文件中，然后把它导入到`main.js`中，在这里我们将把它注册到全局 Vue 实例中。

[![Creating our store](img/bf6997613c7cc2051e389d365089f9bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--elOIr9eC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6uim19875n67yrnzmkr5.png)
[![Registering our store in main.js](img/91ef18353d78de9113654be1a54bd69e.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--QoOiQ-sn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pibvz9xu4u6c9f2rau6k.png)

在我们的`store.js`文件中，我们有一个*存储*对象，这是我们的 Vuex 存储。我们需要在存储中存储一些数据，然后这些数据会被其他组件改变或检索。我们可以使用预定义的关键字`state`向我们的存储添加数据。我们可以像在常规 Vue 实例中使用`data()`一样使用它。在我们的存储中，我们已经有了一个计数器数据属性。现在我们需要从一个组件中检索它。让我们从我们的`App.vue`组件中检索*计数器*。我们可以使用命令`this.$store.state.counter`来完成这项工作。在这里，`$store`关键字对我们是可用的，因为我们在`main.js`文件中注册了我们的 Vuex 商店。

[![initial App.vue component](img/4a54b42892262564d7cc34926ab05b90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jcVBk58u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j95ywnjld5ji76kappjl.png)

现在有另一个概念，你会在检索数据时经常用到。有些情况下，您需要在使用数据之前对其进行处理或执行一些数学运算。如果这项工作对于不同的组件是不同的，那么除了为每个组件分别实现它之外别无选择。但是如果所有组件的操作都相同，那么可以使用`getters`。使用 getters，我们可以对数据执行操作，然后检索最终结果。一个 *getter* 感觉类似于一个函数。但这是不同的。这是因为 getter 需要状态对象。这由 Vuex 自动传递给 getter。我们使用了我们的`counter`数据属性来跟踪按钮被点击的次数，然后我们检索它。所以我们在显示之前把点击次数翻倍怎么样。我们可以使用吸气剂来达到这个结果。

[![Declaring getters in our store](img/49d0498971fecec73c4828cf7621a70b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wzI-T1_3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fgzwu4m8rsdwll2kdzfo.png)

我们创造了我们的吸气剂。让我们在我们的`App.vue`组件中使用它。请记住，我们必须使用我们的 getter，所以我们不能使用`this.$store.state`。对于*吸气剂*，我们使用`this.$store.getters`。

[![Using getters in our App.vue component](img/79f8cd2f74fa4cf9eda2e23f32352dbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EDoIzz9k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wkpb8s252f7ijdzcwwd4.png)

记住只使用 getter 的名称。在我们的例子中，这意味着使用`doubleClicks`和**而不是**和`doubleClicks()`。这是因为没有必要运行它，即使它看起来像一个常规函数。可以把它想象成给事件监听器附加一个回调函数。就像回调是如何被自动调用的一样，getters 是由 Vuex 自动调用和执行的。

最后我们来看`mutations`。当改变一个数据属性的状态时，你只需要*提交*这个改变。要对数据属性进行的更改在变异中指定。然后，可以从任何组件更改状态，并且仍然以同样的方式更新。

我们通过手动增加来改变我们的`counter`数据属性。让我们为它创造一个突变。`mutations`还需要访问*状态的*对象，以便对其进行变异。

[![Creating a mutation in our store](img/57648814120a6433ea41fae0e8877660.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_9oYWmiN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bstgbtgn7i97oerr7xkw.png)

当涉及到使用突变时，就有点不同了。与*获取器*和*状态*不同，突变不像`this.$store.mutations`那样被访问。相反，如前所述，一个突变被*提交*，我们在括号中指定突变的名称。

[![Using mutation in App.vue](img/eb0596a9a886f71bf1f117536b2e74f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---G2gGZma--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tb8913xdjiij44vemppc.png)

这里，我们告诉 Vuex 让*提交*名为`increment`的变异，或者换句话说，执行名为`increment`的变异。

* * *

这些都是非常简单的例子，你不应该在这种情况下使用 Vuex。但是我希望你能够理解使用 Vuex 的一些基本概念。

至此，您已经学习了开始探索这种状态管理工具的高级用途的必要概念。现在你应该能够决定一个项目是否需要 Vuex，以及如何使用它。
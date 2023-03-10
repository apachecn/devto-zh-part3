# React 应用程序设置 Redux 的简单指南

> 原文：<https://dev.to/rleija_/simple-guide-to-setup-redux-to-a-react-apphttps-dev-to-new-4fj>

*原帖@ [Linguine 博客](https://blog.linguinecode.com/post/setup-redux-react)T3】*

在之前的一篇文章中，我写了如何通过构建一个简单的 cat 应用程序来使用 React state。

当应用程序很小时，维护反应状态相对容易。

但是随着应用程序的增长，React 状态树变得更加混乱、难以管理和复杂。

当你的应用程序状态开始包含服务器响应、缓存和 UI 状态数据时，情况就更是如此。

UI 状态数据可以包括路线信息、是否显示加载微调器、分页、标签等。

在某个时候，你的应用程序会有太多的事情发生，以至于你已经无法控制你的应用程序的状态，以及它是如何工作的。

## Redux 就是为了解决这些问题

Redux 是一个微型的状态管理库。

这意味着使您的状态管理更加可预测，并集中您的反应状态数据和状态逻辑。

Redux 通过实现 3 个核心原则来解决这些问题。

### 校长 1:真理的单一来源

您的整个应用程序状态数据都在一个对象树中。

这棵树也可能被称为商店。

通过维护一个单独的存储，你可以更容易地调试或检查你的应用程序。

### 主体 2:状态为只读

你的商店数据作为反馈道具传递下去。哪个 React 不允许你直接修改道具对象。

这将有助于保持整个应用程序的一致性。

Redux 只允许你通过一个叫做 dispatch 的函数来更新你的商店数据，你必须定义**动作**来触发。

这些**行动**描述了商店将会发生的变化。

### 原理三:用纯函数做变化

这些功能也被称为**减速器**，它们附属于**动作**。

reducer 的工作是获取当前状态和一个动作，并返回下一个状态。

所以当你调用一个动作，比如， *ADD_CAT* 。

Redux 将接受这个动作请求，检查它是否存在，以及它是否有一个附属的**缩减器**。

然后，它将执行**缩减器**函数来更新存储数据。

P.S. Redux 不仅可以在 React 上运行，还可以在任何视图 JavaScript 库上使用，甚至可以在普通 JS 上使用！

## 加入 Redux 进行反应

为了简单起见，我将修改之前构建的 cat list 应用程序，以展示如何在 React 中使用 Redux。

我知道又是一个列表 app，但是很简单，很容易上手。

另外，如果您想了解实际代码，请滚动到底部的 Github 源代码链接。

我需要做的第一步是创建 **package.json** 文件。

[![React Redux package json file](img/e9fa85dd899e30a9ebfb4d3191b8a46f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PJyqPzoZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/19041952/redux-package-json-file-701x1024.png)

该项目将需要以下 React 库

**React**–UI 库。

React DOM-让我们将 React 应用程序附加到 DOM 的工具。

**Redux**–状态管理库。

**React Redux**–Redux React 库，让我们将 Redux 存储附加到 React 应用程序。

这个库对于这个例子来说有点矫枉过正，但是它很受欢迎，并且想要展示它的一些优点。

Redux Thunk 让我们在应用程序变得庞大时将我们的**reducer**分割成更小的部分，并且让我们在我们的动作中运行 **dispatch** 。

一旦你的 **package.json** 文件准备好了，在你的终端中运行 *npm install* 。

## React app 结构

下面是应用程序的结构。

[![React redux app structure](img/57d77af85418716bf19191772c07f710.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0kxZvyWZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/19114936/with-redux-app-structure.jpg)

正如你可能看到的，我有我的**公共**目录，它保存了最初的**index.html**文件。

我还有一个 **src** 目录，其中保存了这个应用程序工作所需的一些重要文件。

**index . js**–它负责使 Redux 在 React 应用程序中可用，并抓取 React 应用程序并将其转储到 HTML 中。

**app . js**——主源应用文件。它允许您添加猫名，并以列表格式显示它们。

**store . js**——是一种胶水，它抓住减速器，用它创建一个 Redux store。

**reducers/cats . js**–负责描述卡特彼勒减速器的外观，命名动作，并将动作附加到修改卡特彼勒减速器数据的函数中。

现在你已经知道了应用程序的结构，让我们开始浏览代码。

### 创建 Redux 减速器

首先，我将建立我的卡特彼勒减速器文件。

[![Redux store reducer example](img/a4bfe5037a2bd0ac83877e158006eb9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iyiZUe3---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/19041539/redux-store-example.png)

我要创建的第一件事是一个名为 **initialState** 的变量。

**initialState** 将保存一个名为 **list** 的属性，这是一个猫名数组。

**initialState** 还定义了猫状态的初始状态。

下一个要创建的变量叫做**动作**。

**动作**是键值对对象。

键是动作的名称，值是要执行的 reducer。

就在 **actions** 变量的下面，我定义了一个简单的函数，叫做 **addCat** 。

这个名字不言自明。该函数将猫名添加到状态下的**列表**属性中。

## 创建 Redux 存储文件

这个文件可能看起来很吓人，但也没那么糟糕。我将一步一步地检查它。

[![Redux create store file](img/63012a86861d8365a142c37fc7167c4d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ubFAOiMu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/20043703/redux-store-file-1024x724.png)

首先，我导入 Redux 库，以及上面创建的 cat reducer 文件。

其次，我正在创建一个名为 **createReducer** 的函数，它将初始状态和动作粘合在一起，从而创建一个 Reducer。

我用它创建了我的 cat reducer，然后注入到一个名为* *rootReducer* *的变量中。

然后，我通过使用 create store 函数导出一个新的存储，并向它的根 reducer 提供一些中间件。

在这个应用程序示例中，使用 **combineReducers** 可能是另一个大材小用，但是它向您展示了如何将 Redux 存储拆分和添加 Redux。

## 创建猫名列表 app

下一个要处理的文件是 **App.js** 文件。这个文件将负责显示 UI，允许用户输入新的猫名，并将其添加到 Redux 存储中。

[![Using Redux connect in React component](img/3cacab9cfc43e2c89bb8f88a74f7f0bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uWUb-OES--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/20044552/redux-app-file-655x1024.png)

如果你不熟悉 React 钩子，我强烈推荐你阅读这篇文章，这篇文章教你它们是如何工作和如何使用的: [React useState](https://blog.linguinecode.com/post/getting-started-with-react-hooks) 。

继续，这个文件很大。又是一步一步的时间。

[![Using React redux connect to get state data](img/d6b1a65e08999fd5515d0711a0c83eb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rG3Q6x63--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/20045830/react-redux-breakdown-1.png)

这里的第一步是从 React Redux 库中导入 React **useState** 和 **connect** 函数。

然后我将创建名为 **App** 的 React 组件。

然后我将**连接**函数中的**应用**反应组件导出为一个特设组件(高阶组件)。

你可能会问，“连接是做什么的？”

问得好，**连接**函数让 React 组件将自己锁定到 Redux 存储上。

**connect** 函数不修改组件，但是它创建了一个新组件来传递来自 Redux 存储的任何状态数据，并且它提供了一个名为 **dispatch** 的函数。

这里有一个儿童插图，可以直观地看到它是如何工作的。

[![How Redux connect wraps React component](img/18c3c544c4e6d95da267758fafed7212.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LQUxy_nU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/20142206/kid-illustration-redux-connect-1024x576.jpg)

Redux **connect** 接受一些参数，但我将讨论 2 个最重要的参数。

在上面的例子中，我只传入 Redux 调用的第一个参数， **mapStateToProps** 。

**mapStateToProps** 是一个允许你挑选你想要的 Redux 存储数据的功能。

[![mapStateToprops variable inside Redux connect](img/9666510d27cf6b31738f3dd29ae37e88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fa8M7dpB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/20143605/redux-map-state-to-props-example-1024x390.png)

在 **App.js** 文件中，我决定全部获取，但你不必。

如果提供了第一个参数，那么包装器组件将订阅 Redux 存储。

它就像一个监听器，总是向您创建的组件提供最新的数据。

如果你想让你的应用程序不订阅商店，只需将 **null** 或 **undefined** 作为第一个参数。

Redux connect 中的第二个参数是 **mapDispatchToProps** 。

**mapDispatchToProps** 允许您创建定制的分派函数，并将它们传递给 React 组件。

让我们看看 React 组件的输入和按钮部分。

[![Using setState onChange and Redux dispatch onClick](img/6a246e9876614357f34150f45cb82c4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B9ECVugP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/20144328/redux-dispatch-onclick-798x1024.png)

在反应组件中，在**返回**语句之前，我为猫的名字创建了一个新的 **useState** 钩子。

我还在 **onChange** 事件的输入 HTML 元素中附加了 **setCatName** 。

所以每当用户键入新的猫名时， **setCatName** 就会触发，并更新 **catName** 的值。

我还添加了一个按钮，用于在 **onClick** 事件中提交新的猫名。

在 **onClick** 事件中，我说的是检查猫名是否为空。如果是空的，返回一个**警告**说“猫名不能为空！”

如果有名字，我想通过使用 **dispatch** 来触发 *ADD_CAT* Redux 动作，并在一个名为 **payload** 的属性中提供新的猫名值。

**有效负载**是通过调度传递数据时的常见做法。

不一定要叫有效载荷，想怎么叫都行。但是属性**类型**，必须存在。

就在 **dispatch** 函数之后，我正在将 cat name 值重置为一个空字符串。

**调度**又是干什么的？？

是的， **dispatch** 是一个你只能从 Redux **connect** 得到的功能。

Dispatch 允许您触发 reducer 文件中定义的动作，这是修改 Redux 存储的唯一方法。

把 dispatch 想象成 Redux 的 **setState** 。

**App.js** 文件的最后一部分是显示我从 Redux 存储中获取的猫名。

[![React loop inside render](img/eb8a1ae01fb89bd590799a9a46a5a4ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z9ewAO0d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/20145641/looping-through-list-react.png)

## 添加 Redux 商店提供者组件

最后，这一杰作的最后部分。

在我们的 **index.js** 文件中，我将把提供者组件添加到 React 应用程序中，并从 **store.js** 文件中提供创建的商店。

[![Adding redux store provider component to React](img/e97bdbaf9f8b23b2d43b4492b6834a7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZTXNeADe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/20150005/redux-provider-store.png)

提供者组件使 Redux 存储对任何已经包装在 **connect** 函数中的嵌套组件可用。

让您的提供者位于顶层是一个很好的实践，这样您的整个 React 应用程序就可以访问 Redux store 数据。

## 结论

Redux 有很多样板和移动部分，但是一旦你开始理解它；这对于这个状态管理工具如何帮助管理大型项目更有意义。

如果你有任何问题，请随时在推特上问我。

[Github 源码链接](https://github.com/rleija703/react-examples/tree/master/examples/with-redux)
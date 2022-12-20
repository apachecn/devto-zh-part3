# (Redux)传奇继续——实现你自己的 Redux-传奇式的中间件

> 原文：<https://dev.to/baso53/the-redux-saga-continues-implementing-your-own-redux-saga-like-middleware-5fed>

因此，您已经用 Redux 编写了第一个 React 应用程序。您使用 *redux-thunk* 、 *redux-promise* 或 *redux-saga* 作为您的中间件，使您能够通过简单的 redux 动作执行 API 调用。生活是美好的，但是你开始怀疑，这个中间件到底对我的行为做了什么？当我写这些想法的时候，到底发生了什么样的魔法？

在本文中，我们将尝试解释那里发生了什么，以及如何基于一个流行的选项 *redux-saga* 为 Redux 实现您自己的中间件，我强烈推荐您查看这个选项。

## 有点背景

如果你还不熟悉 Redux，我将尝试提供一个** *非常***简化的解释，没有任何实际的语法。

Redux 是一个应用程序状态容器，它将状态存储在一个名为 **store 的对象中。**

**存储器**只能被称为**还原器的特殊函数返回的数据占用。**

Reducers 是纯函数，这意味着对于给定的输入，它们总是返回相同的结果。这就是 Redux 被称为可预测状态容器的原因，根据 reducers 接收到的输入，您总是可以知道存储中会有什么。减速器接收到的这些输入被称为**动作。**动作总是有一个类型，并且可选地携带额外的数据，基于此，reducers 将数据放入存储中。然后是* *中间件，* *位于动作和还原器之间。它是一个中介器，可以读取发送的数据(调用动作的一个好听的名字)，然后用它做一些事情。通常，中间件用于日志记录、发送错误报告或异步获取数据，然后将动作和获取的数据一起传递给 reducer。

工作流看起来像这样。

[![Redux workflow](img/568275237df1589b090a48dc691acaee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mFIaCeTo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2048/1%2AorO_UtqBUha_hMvddkXTVw.png)

如果您以前使用过 Redux，那么您可能已经使用过一些中间件。通常，您会使用中间件，该中间件使您能够基于某些副作用(例如 API 调用)的结果，有条件地调度(或不调度)动作。但是，在数据到达 reducer 之前，中间件实际上可以用于您想对数据做的任何事情，比如日志记录，或者在应用程序崩溃时向管理员发送错误报告。

## 创建商店

为了保持简短，我将使用 *create-react-app* 来生成我们的应用程序，react 已经设置好了，然后安装 *redux* 和 *react-redux* 来轻松连接这两个应用程序。我们在这里不会对 React 做太多的介绍，所以如果您不熟悉也不用担心。

我们的简单演示应用程序的目标是从一些 web API 获取数据，使用我们的中间件将其保存到 Redux，并显示给用户。

首先，我们将编写一个简单的**缩减器**，它将保存从 API 接收的数据。我将使用的 API 返回随机的个人信息，包括姓名和国家。这是我们想要保存到商店的数据。我们的缩减器将处理三种动作类型:***FETCH _ DATA _ REQUEST***，***FETCH _ DATA _ SUCCESS***和***FETCH _ DATA _ FAILED***。

我们的减速器看起来像这样。我们将把这段代码放在一个名为 ***reducer.js*** 的新文件中。
# 取消装箱冗余

> 原文：<https://dev.to/codingsam/unboxing-redux-4g6o>

[![](img/6024d1f5580411c000e8f4f9d792619a.png)](https://medium.com/@codingsam/unboxing-redux-5510bce4178d?source=rss-58cf94a36c97------2)

Redux 只是一个商店的流量

Redux 是一个 Javascript 库，帮助开发人员管理应用程序状态，例如，它可以是登录用户或社交网络应用程序中给定用户的关注者。

我和 Redux 一起工作了几个月，感觉棒极了！当我开始使用它时，我突然想到了一些东西。然后，我想起很久以前我看过关于通量设计模式。如果你对 redux 或者 flux 一无所知也不用担心，我会尽量说清楚。我刚刚意识到这两件事。我告诉自己:“所以，这只是一个 flux 实现，但是你只有一个 store”。

当我对一些习惯使用 Redux 的人这样说的时候，他们的反应是这样的:
“什么？什么是通量？”。这有点奇怪，因为对我来说，redux 显然与通量有关。但后来我意识到一件事。谈论 Redux 真的很容易，甚至不考虑 flux。大多数人把 Redux 仅仅描述为前端的应用状态管理库，这是事实。但我认为这不是最完整的答案。如果你一头扎进去，开始使用它，你就错过了全局。别担心，我不是在评判！如果你刚刚加入了一个使用这个库的项目，你必须学习它是如何工作的，并把事情做好，对吗？这可能需要一些时间，直到一切都有意义。

如果你不知道 redux 是什么，那你来对地方了。我会试着用简单的语言向你解释。即使你对 Redux 很熟悉，你确定你真的知道它是什么，它所有的概念从何而来？

我认为，定义 redux 意味着你首先要解释什么是通量。一旦你理解了它，redux 就自然而然地出来了，你就会明白我为什么把它定义为“只有一个商店的通量”。

这不是一个通量或 redux 教程。在这篇文章中，我将对 Redux 进行“*拆箱*”，这意味着，我将尝试解释它是什么以及它的核心概念来自哪里。
如果你已经迷路了，不要担心。我将分解它，这样你最终会明白什么是通量和还原。然后，你可以跳进 [Redux 入门](https://redux.js.org/introduction/getting-started)指南，开始构建令人敬畏的项目！让我来为你解封。

## 通量

当我打开 Redux 盒子时，我首先发现的是[通量](https://facebook.github.io/flux/)。它是一种设计模式，由脸书创建，用于管理应用程序状态。我不打算专注于任何特定的实现。我认为一旦你理解了模式，你就可以找到任何实现并开始使用它。
它有*动作*，单个*调度员*，一个或多个*商店*，当然还有*视图*。让我们来分解一下:

*   *Action* :这只是一个至少需要一个类型键的对象，它将定义动作的类型。稍后将使用这种类型来决定状态更新

*   *调度员*:其职责是将动作发送到所有商店

*   *存储*:存储接收动作并相应地更新应用程序状态

*   *视图*:这只是用户正在交互的 UI 组件

下图显示了该模式的主要组件以及它们之间的交互方式:

[![Diagram that shows how the Flux pattern works](img/53dd2404825a2db7343c8e89e7c8d41f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LIJyMoO6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AxsAiyYD66qYqIxkJrsBl0g.png)

它是这样工作的:

*   用户与应用程序*视图*交互，这些交互将创建*动作*

*   *行动*通过*调度员*发送到所有门店

*   每个*存储器*，其被编程为监听给定的*动作*可以更新其状态

*   如果需要的话，新的状态将可供*视图*使用

每个存储负责处理给定域的状态和逻辑。例如，如果您正在创建一个社交网络，您可能有一个用于管理用户的存储和另一个用于提要的存储。有些商店会监听分派的动作，有些商店会忽略它，由开发人员决定哪个商店关心哪个动作。在该示例中，动作可以是“获取用户的订阅源”或“用户请求关注另一用户”。这是一个真正简化的通量视图。我建议你深入研究一下官方文件。

现在我给你看了 Redux 盒子里面的东西，通量模式，让我们继续看盒子本身… Redux！

## Redux

看了下 Flux，改成只有一个店，加上*减速器*的概念。就这样，现在你有了 Redux。
下图显示了 Redux 的不同组件如何相互作用:

[![Diagram that shows how Redux works](img/6901a2fba9fb3a7e816f865ecf7337f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YIkvn2jw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Aj69swQLVOLX7gwj3vUFeVw.png)

它是这样工作的:

*   用户与*视图*交互，可以创建一些*动作*

*   那些*动作*被(通过*调度器*调度到*减速器*

*   *减速器*接收当前状态和动作，并返回新状态

*   视图现在只需要从*存储*中获取新的状态

看起来很像通量，对吧？但你只有一家店。改变状态的唯一方法是分派一个动作。减速器会得到它并返回新的状态。关于 reducer 的一个非常重要的事情是，它必须是一个*纯函数*，这个函数对于相同的参数产生相同的结果，并且没有任何副作用。这意味着 reducer 必须创建一个新的状态对象，而不是修改现有的状态对象。这是 Redux 检测状态被更新的唯一方法。
例如，如果你正在做一个社交网络项目，你的状态可能是这样的:

```
{  "followers":  [],  "following":  [],  "feed":  []  } 
```

还有减速器…

```
function reducer (state, action) {
  switch (action.type) {
    case "FETCH_FEED_SUCCESS": {
      const { feed } = action;
      return { ...state, feed };
    }
    case "FETCH_FOLLOWERS_SUCCESS": {
      const { followers } = action;
      return { ...state, followers};
    }
    case "FOLLOW_SOMEONE": {
      const { userToFollow } = action;
      const { following = []: currentFollowing } = state;
      const following = [...currentFollowing, userToFollow];
      return { ...state, following };
    }
    default:
      return state;
  }
} 
```

默认情况下不需要创建新对象，因为它不修改状态。只有一个缩减器会把我们带到一个又大又乱的缩减器函数，对吗？幸运的是，您可以将 reducer 分成多个函数，每个函数只负责处理部分状态。

```
// Feed reducer
function feed (state, action) {
  switch (action.type) {
    case "FETCH_FEED_SUCCESS": {
      const { feed } = action;
      return feed;
    }
    default:
      return state;
  }
}

// Followers reducer
function followers (state, action) {
  switch (action.type) {
    case "FETCH_FOLLOWERS_SUCCESS": {
      const { followers } = action;
      return followers;
    }
    default:
      return state;
  }
}

// Following reducer
function following (state, action) {
  switch (action.type) {
    case "FOLLOW_SOMEONE": {
      const { userToFollow } = action;
      return { ...state, userToFollow };
    }
    default:
      return state;
  }
} 
```

借助 [combineReducers](https://redux.js.org/api/combinereducers) 功能，您可以做到这一点。您的所有组合减速器将接收您分派的任何操作。你必须这样做，因为这里只有一家商店，也就是说，只有一个减速器。

像 Flux 一样，你仍然有动作和存储，但是以一种更简单的方式。您只需要分派动作并处理 reducer 中的状态变化。

您可以将 Redux 与您选择的任何 UI 框架一起使用。有一些包为您提供了一些功能，使得将您的框架连接到 redux 存储更加容易。例如，如果你想将 redux 与 [React](https://reactjs.org/) 一起使用，你可以使用 [react-redux](https://github.com/reduxjs/react-redux) 包。

## 结论

很容易直接进入 redux，而不理解是什么激发了所有这些概念，比如动作和商店。我认为重要的是要有一个大的图景并了解通量，即使它只是一个简化的概述，就像你刚刚读到的那样。乍一看，通量可能有点棘手。我认为 Redux 以一种更简单、更容易的方式给出了这种模式。您只有一个存储，状态更新由 reducers 处理，它需要是纯函数。

下次有人问你什么是 redux，你可以直接做这个拆箱。您首先显示 Redux 框。然后，你打开它，从盒子里拿出焊剂。现在你解释通量，最后，你回到 Redux 框，一切都有意义了。这就是为什么我用这个简单的句子来定义 Redux:“*Redux 只是只有一个存储*的 flux”。

我希望你喜欢并学到了一些东西。编码快乐！:)

让我知道你的想法，并关注我以获得更多关于开发人员的精彩内容:)

[codingsam01 @ Twitter](https://twitter.com/codingsam01)

[codingsam01 @ Instagram](https://www.instagram.com/codingsam01)
# redux——一个挥之不去的问题

> 原文：<https://dev.to/geojow/redux---a-persistent-question-27ek>

*本文原帖[此处](https://medium.com/@geojow/redux-a-persistent-question-df5bb15373e8)* 。

> Redux 是 JavaScript 应用程序的可预测状态容器。——redux.js.org

作为一个 web 开发的新手，我在开始使用 React 几秒钟后就遇到了 Redux。

在我要说的是一个相当陡峭的学习曲线之后，我刚刚被介绍到 state 和 props，我慢慢地但肯定地开始理解如何使用 Redux，以及为什么它是开发 web 应用程序时如此有用的武器。

这就是我的麻烦开始的地方，我陷入了某种形式的 Redux 流沙。我创建的每个新组件都有自己的 Redux 存储。每个 API 调用都至少有一个 Redux 动作，我总是调用“SetCurrentUser”或“ClearCurrentUser”动作来跟踪事情！

> 仅仅因为你能，并不意味着你应该

我构建了多个 redux 存储，所有的功能都是相同的，只是组件略有不同，我的 redux 文件夹变得臃肿，当然，我的组件文件很好，很简洁，但是代价是什么呢？

所以现在当我创建一个新的组件时，我考虑是否要简化它，我把自己想象成如下:

> 当我离开时，我需要页面上的数据吗？或者我可以在返回页面时再打一次电话吗？

更多时候，问题第一部分的答案！)是没有！我可以使用组件状态来处理数据。

我现在使用 Redux 来处理像 Auth 和 Permissions 这样的事情，这些数据我需要放在不止一个地方。

这是对一个复杂主题的过度简化，有许多关于它的优秀文章，但这是我的热门话题，也是一个很好的起点。
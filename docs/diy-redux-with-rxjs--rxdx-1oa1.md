# 用 RxJS = RxDx

> 原文：<https://dev.to/onerzafer/diy-redux-with-rxjs--rxdx-1oa1>

<figure>[![](img/fea319145a8396cad0211868906ac309.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q_fDQX38--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Advz733Mj3DVx2ytegmBW-g.png) 

<figcaption>照片由[史蒂夫·哈拉马](https://unsplash.com/photos/iVGevPcaJzk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

如果你正在开发一个巨大的应用程序，状态管理是必须的，使用适当的状态管理可以是一个真正的救命稻草。在 React 上，Redux 是我们的救生员，在 Angular 上，我们有 NgRx 来担当这个角色。

我认为，在架构层面上，两者都有相似的样板，如果你已经掌握了其中一个，那么你对另一个的学习曲线将会更加平滑。我在 NgRx 上真正喜欢的不是库本身，而是 RxJS，它确实是处理流事件的最令人惊叹的库。

当我在研究 Redux 和 NgRx 时，我意识到几乎没有人在没有大量助手库的情况下单独使用 Redux，但另一方面，NgRx 的情况并不相似，因为它在大多数情况下提供了一个完整的解决方案。所以我决定做一个实验，将 RxJS 和所有 Redux helper 库结合起来，创建一个超级 duper 状态管理库，用于像 Redux 这样具有相似指纹的 React。

在这一点上，诀窍是显而易见的:首先，我用 RxJS 重新实现了 Redux，并在其上添加了一些中间件 salt，它已经准备好了！所以在这篇文章中，我将试着解释我是如何做到的。

提示:我已经发布了最终库的 alpha 版本，你可以在这里查看* **** *。**

 **### 步骤 0:解构 Redux 和相关库

Redux 主要提供了一个 **createStore** 函数，它显然创建了一个商店。这个 createStore 需要一个**缩减器、** **初始状态、**和一个**增强器**。

**Reducer** 是取一个状态返回一个状态的函数。很简单，对吧？但是等一下，我将需要不止一个减压器，这意味着我将需要一个助手函数来将所有减压器组合成一个大减肥器。

出于类似的目的，Redux 有 **combineReducers** 功能，非常方便。一个**初始状态**是一个对象，它可能是一个空的或者根本没有定义的状态。甚至更简单！**增强器**有点奇怪，有如下签名:

```
(createStore) => (reducer, initialState) => store 
```

我将再次需要不止一个增强器，我将再次需要一个助手函数来将所有增强器组合成一个名为 **applyMiddleware 的增强器。**

当我们运行 createStore 函数时，它返回一个类似于下面简化对象的对象:

```
{
   getState: () => state,
   subscribe: () => unsubscribe,
   dispatch: (action) => void
} 
```

**getState** 返回当前状态。**订阅**返回一个**取消订阅**对象，但是如果我打算使用 RxJS，我可以使用它自己的订阅和取消订阅。因此，我将有一个更简单的实现。**分派**功能实际上将动作传递给减速器。RxJS 在这里也很方便，它有自己的调度机制。

因此，我创建了一个待办事项列表来实现我的目标:

*   实施`createStore(reducer, initialState, enhancer) => store`
*   实施`combineReducer(structuredReducersObject) => reducer`
*   实施`applyMiddleware(createStore) => (reducer, initialState) => store`

开始吧！

### 第一步:在 RxJS 行为主体和操作者的帮助下创建 Store

在任何时候，**商店**的消费者调用**的 getState，**商店应该返回当前状态。如果我使用**行为主体**作为状态源，内部存储状态的实现将更容易，因为通过调用**行为主体.值**，来自 RxJS 观察对象的类似行为属于**行为主体**。

事情是这样的:**
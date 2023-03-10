# 模块化冗余架构

> 原文：<https://dev.to/jovidecroock/modular-redux-57nl>

## 简介

去年我一直在做一个相当大的应用程序，在这篇文章中我将主要谈论前端。要知道的要点是状态管理堆栈由 redux 和 redux-saga 组成。

这个应用程序一直在增长，我们尝试了延迟加载一些页面，这似乎很好，但我们知道它可以做得更好。

在这篇文章中，我将详细阐述用来实现我们正在构建的架构的概念，并以一个展示这一点的最小代码沙箱来结束。

## 内聚 vs 耦合

某一天，我们有一个想法，看看我们的文件夹结构，并评估它与这些条款。

内聚性表示某一部分与模块的关系。示例:将所有操作分组在/actions 下是一种低内聚的形式，而将所有 projectActions 分组在一个名为 project 的模块下是一种高内聚的形式。

耦合可以被看作是当你改变模块 x 中的某个东西时，它会影响模块 y 吗？

好的软件在模块内部具有高内聚，模块之间耦合度低。这就是我们开始为我们的应用考虑模块化架构的方式，剧透一下我们也发现了这一点。

## 问题

当惰性加载时，我们发现我们引入了额外的加载时间，块加载，只有在块加载后，我们才能开始获取数据。这带来了我们不想要的开销，这是我们在实施新架构之前必须克服的新障碍。

我们不希望我们的新架构给 UX 带来新的限制。

## 建筑

现在是最后一部分，经过一些研究和工作，我们得到了架构。

文件夹结构如下:

```
/common
/reducers --> static reducers
/sagas --> static sagas
/modules
  /module1
    /submodule1
    actions.js
    sagas.js
    reducers.js
    index.js
... 
```

所以每个模块都包含它自己的子模块和冗余逻辑，这些将在模块加载时被注入。

当我们有子模块时，例如，当我们查看某个用户时，我们可以控制该用户的约会，然后我们还可以使它成为一个仅按需加载的懒惰模块。

当我们在我们的团队中评估这种方法时，这似乎是一种优雅的扩展。

### 减速器

在阅读 redux 文档后，我们发现了一种动态注入 reducers 的方法。
请注意，我们对始终相关的状态进行了区分，例如应用程序元数据或登录用户。

我们的店铺变更如下:

```
function createReducer(asyncReducers) {
  return combineReducers({
    ...staticReducers,
    ...asyncReducers
  });
}

export const sagaMiddleware = createSagaMiddleware();

export default function configureStore() {
  const store = createStore(createReducer(), applyMiddleware(sagaMiddleware));

  store.asyncReducers = {};

  store.injectReducer = (key, asyncReducer) => {
    store.asyncReducers[key] = asyncReducer;
    store.replaceReducer(createReducer(store.asyncReducers));
  };

  store.removeReducer = key => {
    delete store.asyncReducers[key];
    delete store.getState()[key];
  };

  return store;
} 
```

asyncReducers 的引入使我们能够始终跟踪在任何给定时间加载了哪些动态减速器。

helper 方法使得动态添加和删除相关的 reducers 变得非常容易。

### 传奇故事

这有点棘手，但经过仔细研究后，我们发现当你导出 sagaMiddleware 时，你实际上可以动态运行 saga 的。

这看起来很容易，但经过一些测试后，你真的必须在卸载时删除传奇，因为你可以继续添加一个偶然的传奇。这造成了一些有趣的情况，五个网络请求同时被发送，甚至没有人知道如何发送。

这就是为什么我们做了一个小助手，可以在需要的时候使用分叉和取消。

```
function runSaga(key, saga) {
  const runnableSaga = function* main() {
    const sagaTask = yield fork(saga); // Forks the saga returning a task
    const { payload } = yield take(CANCEL_SAGA); // We listen for this action.

    if (payload === key) { // If the action is dispatched with our key cancel it.
      yield cancel(sagaTask);
    }
  };

  sagaMiddleware.run(runnableSaga); // sagaMiddleware exported from above.
}

function cancelSaga(key) {
  store.dispatch({
    type: CANCEL_SAGA,
    payload: key,
  });
} 
```

对于不熟悉 redux-saga fork 的人来说，take 和 cancel 都是库暴露的帮助器。

### UX-问题

这个问题的解决方案实际上很简单，我们通常加载`module/index`，所以这是一个静态导入，当它被调用时，它将注入 saga 和 reducer。发生这种情况后，它会调度一个初始动作来开始获取数据，同时你的 UI 块被加载。这使得两个加载部分同时发生，一个可能比另一个花费更长的时间，但这并没有太大的区别。

酷的部分是，这向我们介绍了一种新的加载方式，就像 facebook 在加载数据时呈现一个看似空的时间线一样，我们也可以在加载数据和 UI 时这样做。

当模块被卸载时，它会取消 saga 并移除缩减器，我们可以继续处理新打开的模块。

### 做出反应

为了实现这一点，我们实际上利用了钩子的力量，我们在模块索引中有一个`useEffect`,它只在初始化时运行，并在卸载时清理工作。
我们也可以用 componentDidMount 和 componentWillUnmount 来做这件事，但是用一个函数来做这件事感觉很干净。

我们唯一需要通过的是一组 saga，一组 reducers 和一个 initialAction。

对于惰性加载，我们使用`React.Suspense`和`React.lazy`，这感觉非常直观，只需在加载时提供一个后备。

## 总结性的

这种方法对我来说感觉很好，因为当我们卸载一个模块时，我们也删除了 saga、reducer 和 state。这使得我们不能添加一个传奇替身(是的，这是可能的)，我们不能有任何陈旧的状态。这反过来也减少了你的网页在任何给定点的内存消耗。

这种方法绝不仅仅局限于 redux-saga，它可以线性地应用于 redux-thunk 以及更多。

请注意，我们的方法必须是增量采用的，因为我们仍然必须能够在过渡到新架构的同时开发功能。

请随意给出你对此的看法，我很想听听！

[演示](https://codesandbox.io/s/w07qqwlkq5)

## 来源

[减速器拆分](https://redux.js.org/recipes/code-splitting)
[代码拆分](https://webpack.js.org/guides/code-splitting/)
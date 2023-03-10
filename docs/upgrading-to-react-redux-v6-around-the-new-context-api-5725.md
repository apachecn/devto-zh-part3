# 升级到 React-Redux v6:围绕新的上下文 API

> 原文：<https://dev.to/wgao19/upgrading-to-react-redux-v6-around-the-new-context-api-5725>

于是 [React-Redux](http://react-redux.js.org) 升级为 [6.0.0](https://github.com/reduxjs/react-redux/releases/tag/v6.0.0) 。我花了一些时间来迁移我们的代码库。这是我学到的一点。

在这篇文章中，我将介绍 React-Redux v6 的以下主题:

*   使用自定义上下文
*   进入商店
*   支持多个商店

这篇文章没有涵盖以下主题，尽管它们也是对 React-Redux 在 v6 之后的 API 的修改:

*   用`forwardRef`替换`withRef`
*   已弃用`createProvider()`

# 重大变化

React-Redux v6 的主要实现变化是，它从使用 [React 的遗留上下文 API](https://reactjs.org/docs/legacy-context.html) 迁移到了 [React 的新上下文 API](https://reactjs.org/docs/context.html) 。它主要影响它如何在内部访问商店，以及它如何允许其用户应用程序访问商店。

这意味着如果你的应用程序只使用 React-Redux 的两个主要 API`<Provider />`和`connect`，它很可能会正常工作。

其他变化包括直接将商店作为道具传递给连接的组件，通过`storeKey`取消多个商店，取消`createProvider`等。

以下是最初被 React-Redux v6 破坏的库的简短列表，并且已经发布了(或处于测试阶段)它们的最新支持:

*   [还原形式](http://redux-form.com) : ^ [8.0.0](https://github.com/erikras/redux-form/releases/tag/v8.0.0)
*   [连接-反应-路由器](https://github.com/supasate/connected-react-router) : ^ [6.0.0](https://github.com/supasate/connected-react-router/releases)
*   [反应-还原-燃烧基](http://react-redux-firebase.com):^[3 . 0 . 0-阿尔法](https://github.com/prescottprue/react-redux-firebase/releases/tag/v3.0.0-alpha)

*如果你使用的是 [React-Router-Redux](https://github.com/reactjs/react-router-redux) ，这个库已经被弃用，不再维护，取而代之的是 Connected-React-Router。关于迁移，您可以参考 Connected-React-Router 的文档。*

# 提供自定义上下文

您可以提供自己的上下文对象，而不是使用 React-Redux 中的默认上下文实例。

```
<Provider context={MyContext} store={store}>
  <App />
</Provider> 
```

Enter fullscreen mode Exit fullscreen mode

如果您提供一个自定义上下文，React-Redux 将使用该上下文实例，而不是它的默认实例。

注意，使用 React 的新上下文 API，虽然可以嵌套`<Context.Provider />`，但是将使用提供给最近祖先提供者的值。早期祖先中提供的值将不会被查询或合并。~~这意味着您不应该在 React-Redux 的`<Provider />`下嵌套您的定制上下文的提供者。它会破坏 React-Redux 的用法。~~更多关于上下文 API 的解释可以在[这里](https://ag04.com/deep-dive-into-new-react-context-api/)找到。

*注意:我后来了解到[这个问题](https://github.com/reduxjs/react-redux/issues/1126)，其中**用嵌套上下文的提供者隐藏**是一个合法的用例，在这种情况下是一个出色的解决方案。我想我不应该说“你不应该…”*

向`<Provider />`提供定制上下文后，您还需要向所有连接的组件提供这个上下文实例:

```
export default connect(mapState, mapDispatch, null, {
  context: MyContext
})(MyComponent);

// or
const ConnectedComponent = connect(mapState, mapDispatch)(MyComponent);
<ConnectedComponent context={MyContext} /> 
```

Enter fullscreen mode Exit fullscreen mode

不向连接的组件提供上下文将导致运行时错误:

> 不变违反
> 
> 在“连接(MyComponent)”的上下文中找不到“存储”。将根组件包装在中，或者将自定义 React 上下文提供程序传递给，并将相应的 React 上下文使用者传递给连接选项中的连接(Todo)。

下面是我们在 CodeSandbox 中的异步注入缩减器:[使用 React-Redux v6 和自定义上下文](https://codesandbox.io/s/n0wqjov2vp)异步注入缩减器。

# 访问商店

从上下文或从导入其他文件中获取存储似乎从来没有被库的维护者推荐过。尽管如此，这种情况还是很常见的。

[React-Redux 官方文档](https://react-redux.js.org/using-react-redux/connect-mapdispatch#can-i-call-storedispatch)

> 在 React 组件中直接与存储交互是一种反模式，无论是存储的显式导入还是通过上下文访问存储。

在 v6 中，React-Redux 不再使用 React 的遗留上下文 API。相反，它使用 React 的新上下文 API。这意味着通过定义`contextTypes`来访问存储的老方法将不起作用。

React-Redux 导出它用于`<Provider />`的默认上下文实例，这样您就可以通过这样做来访问存储:

```
import { ReactReduxContext } from 'react-redux'

// in your connected component
render() {
  return (
    <ReactReduxContext.Consumer>
      {({ store }) => <div>{store}</div>}
    </ReactReduxContext.Consumer>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我用一个更干净的实现分叉了最后一个 CodeSandbox 示例:[使用默认上下文](https://codesandbox.io/s/xzvvl9yyz)用 React-Redux v6 异步注入 reducer。

# 支持多个店铺

同样，也不建议使用多个商店。整个 Redux v.s. Flux 讨论似乎已经划了一条清晰的线:

[我可以或应该创建多个商店吗？我可以直接导入我的存储，并自己在组件中使用它吗？](https://redux.js.org/faq/store-setup#can-or-should-i-create-multiple-stores-can-i-import-my-store-directly-and-use-it-in-components-myself)

> 最初的流量模式描述了在一个应用程序中有多个“商店”，每个商店保存不同领域的数据。这可能会引入一些问题，例如需要一个存储“等待”另一个存储进行更新。这在 Redux 中是不必要的，因为数据域之间的分离已经通过将单个 reducer 拆分成更小的 reducer 来实现。

在 v6 中，不赞成指定多个存储并用`storeKey`访问它们。然而，可以通过提供(多个)定制上下文来实现它，并让不同的存储存在于不同的上下文中。

```
// a naive example

// there is no need to supply a default value when creating the context
// the value will be supplied when React-Redux mounts with your Context.Provider
const ContextA = React.createContext();
const ContextB = React.createContext();

// assuming reducerA and reducerB are proper reducer functions
const storeA = createStore(reducerA);
const storeB = createStore(reducerB);

// rendering
return (
  <Provider store={storeA} context={ContextA}>
    <Provider store={storeB} context={ContextB}>
      <App />
    </Provider>
  </Provider> ); 
```

Enter fullscreen mode Exit fullscreen mode

可以连锁`connect()`

```
import { compose } from 'redux';
import { connect } from 'react-redux';

compose(
  connect(mapStateA, null, null, { context: ContextA }),
  connect(mapStateB, null, null, { context: ContextB })
)(MyComponent); 
```

Enter fullscreen mode Exit fullscreen mode

CodeSandbox 示例:[一个使用独立 store](https://codesandbox.io/s/92pm9n2kl4) 的主题阅读列表 app，通过提供(多个)自定义上下文实现。

[https://codesandbox.io/embed/92pm9n2kl4](https://codesandbox.io/embed/92pm9n2kl4)

从开发体验的角度来看，我觉得新的上下文 API 为多个商店提供了更清晰的隔离。也许在这个时候可以不那么不明智？

# 链接和引用

*   [React-Redux v6.0.0 发布标签](https://github.com/reduxjs/react-redux/releases/tag/v6.0.0)
*   惯用 Redux:React-Redux 的历史和实现

和一些发布线程

*   [React-Redux v6 反馈线程，#1083](https://github.com/reduxjs/react-redux/issues/1083)
*   [从上下文访问商店？#1123](https://github.com/reduxjs/react-redux/issues/1123)
*   [值在 React-Redux v6 的 SSR 中未定义，#1107](https://github.com/reduxjs/react-redux/issues/1107)
*   [升级到 v6.0.0 后，通过 connectAdvanced 连接的组件被重新渲染为不相关的动作分派，#1118](https://github.com/reduxjs/react-redux/issues/1118)
*   [`<Provider>`错过了在它的构造函数运行时和它挂载#1126](https://github.com/reduxjs/react-redux/issues/1126) 时之间发生的状态变化
*   [使用不同的商店密钥更新文档，#1132](https://github.com/reduxjs/react-redux/issues/1132)

有很多地方可以获得帮助

*   [React-Redux 的 GitHub 问题](https://github.com/reduxjs/react-redux/issues/)
*   [react flux Redux 通道](https://www.reactiflux.com)
*   [StackOverflow React-Redux 标签](https://stackoverflow.com/questions/tagged/react-redux)
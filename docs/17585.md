# 反应:反应 Redux 6.0がリリース

> 原文：<https://dev.to/gumi/react-react-redux-60-3e8l>

[React Redux](https://react-redux.js.org/) 的 v6.0.0 于 2018 年 12 月 5 日发布。 本文根据[发行信息](https://github.com/reduxjs/react-redux/releases/tag/v6.0.0)介绍其内容。

React Redux 6.0 是第一个支持[Context](https://reactjs.org/docs/context.html) API 的发行版，该 API 是 React 16.4 中的新增功能。 因此，需要 React 16.4 或更高版本。 有关 v6 的详细变更以及 React Redux 到目前为止的实现变迁，请参考“[idiomatic redux:the history and implementation of react-redux](https://blog.isquaredsoftware.com/2018/11/react-redux-history-implementation/)”

# 打破互换性的变更

对 React Redux 6.0 进行的向后兼容性更改有以下两种:

*   [`connect`](https://react-redux.js.org/api#connect)的[`withRef`](https://react-redux.js.org/api#arguments-1)选项已被`forwardRef`替换。 将`{forwardRef : true}`传递给`connect`后，只要对连接的包装组件进行引用，就会返回该组件的实例。

*   无法再将`store`作为属性传递给连接的组件。 相反，自定义属性`context={MyContext}`既可以接收`<Provider>`，也可以接收`<ConnectedComponent>`。 另外，作为`connect`的选择项可以传递的是`{context : MyContext}`。

# 变更后的动作

在 React Redux 6.0 中，与以往的动作有以下两个不同。

*   尝试从传统上下文引用`Store`实例的库将不再工作。 因为`Store`的状态已经被纳入`<Context.Provider>`了。 由此动作发生变化的例如是`connected-react-router`和`react-redux-subspace`。 (在当前实现中，`Store`也会添加到其上下文中。 但是，没有参考上下文`Store`的公开的 API。 使其可由其他库引用。 但是，有可能被废除。 ）

*   在`componentWillMount`中，与从构造函数发送的`Action`相关的动作发生变化。 以前，通过父组件的构造函数发送，只要子组件已装载，就可以立即使用更新状态。 这是因为组件分别从`Store`开始单独读取。 在 React Redux 6.0 中，所有组件都从上下文中获得相同的当前`Store`状态值。 因此，树保持一致，不会分裂。 整体来说这是改善。 但是，有时会影响依赖于到目前为止的行为的 APP 应用。

# 其他变更

*   `React.createContext()` の使用([使用 React.createContext()](https://github.com/reduxjs/react-redux/pull/1000) )
*   Prettierの使用([用更漂亮的](https://github.com/reduxjs/react-redux/pull/1071))
*   `isValidElementType` を開発ビルドでのみ実行([从生产版本中删除 react-is/isValidElementType](https://github.com/reduxjs/react-redux/pull/1069)
*   在`isPlainObject()`中将`null`作为平面对象的原型来处理(“[Treat `null`as a valid plain object prototype in`isPlainObject()`](https://github.com/reduxjs/react-redux/pull/1075)”)
*   `connectAdvanced`仅在属性发生变更时重新绘制(“[Add connectAdvanced ( ) tests](https://github.com/reduxjs/react-redux/pull/1079) ”)
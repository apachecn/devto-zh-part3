# 快速注释修复 0.89 之后的“连接”流类型注释

> 原文：<https://dev.to/wgao19/quick-note-fixing-connect-flowtype-annotation-after-089-joi>

这将是一个关于如何修复大多数`connect`注释的快速帖子。

将流量提升到 0.85 以上是很困难的。[在 0.85](https://medium.com/flow-type/asking-for-required-annotations-64d4f9c1edf8) 创建了数百个需要注释的错误后，流程要求更多的注释。最拖累我的主要是`connect`和(尤其是一起)一些其他高阶组件。

## 诠释了`connect`最常见的用例

也就是说，`connect`的用例带有一个`mapStateToProps`，而[是`mapDispatchToProps`的对象简写](https://react-redux.js.org/using-react-redux/connect-mapdispatch#defining-mapdispatchtoprops-as-an-object):

### 流向`react-redux_v5.x.x/flow_v0.89.x-`

首先，将`react-redux`的`flow-typed`定义更新到 [v0.89.x](https://github.com/flow-typed/flow-typed/blob/master/definitions/npm/react-redux_v5.x.x/flow_v0.89.x-/react-redux_v5.x.x.js) 版本。这包含了对 Flow 0.85 中 bug 修复的最新支持，它要求我们显式地将类型传递到导出的泛型类型中。要了解更多信息，您可以阅读拉动请求 [#3012](https://github.com/flow-typed/flow-typed/pull/3012) 。

### `connect`宣言解密

`connect`将有六个类型参数

```
connect<-P, -OP, -SP, -DP, S, D> 
```

解密到:

```
connect<Props, OwnProps, StateProps, DispatchProps, State, Dispatch> 
```

并非所有这些类型参数都是必需的。您可以用占位符`_`替换其中的一部分。但是你不能省略占位符。

### 如何标注

*   注释`mapStateToProps`的返回
*   为`connect`标注`Props`和`OwnProps`类型参数
*   `Props`通常是组件的道具类型
*   `OwnProps`通常是`Props`减去`connect`通过`mapStateToProps`和`mapDispatchToProps`输入的

```
// react-redux_v5.x.x/flow_v0.89.x-/test_connect.js
type OwnProps = {|
  passthrough: number,
  forMapStateToProps: string,
  forMapDispatchToProps: string,
|}

type Props = {
  ...OwnProps,
  fromMapDispatchToProps: string,
  fromMapStateToProps: string,
}
type State = { a: number }
type MapStateToPropsProps = { forMapStateToProps: string }

const mapState = (state: State, props: MapStateToPropsProps) => {
  return {
    fromMapStateToProps: 'str' + state.a,
  }
}

const mapDispatch = {
  dispatch1: (num: number) => {},
  dispatch2: () => {},
}

connect<Props, OwnProps, _, _, _, _>(mapState, mapDispatch)(MyComponent) 
```

## 从哪里了解更多？

我在 Flow 的 GitHub repo 中看到这个问题，如果不是在很多其他地方的话。我自己也很难想明白。

> 有没有注解`connect`的「官方方式」？

简而言之:没有。

如 [#7493](https://github.com/facebook/flow/issues/7493) 第一次回复中所建议的，回答得长一点:

> 看看流类型 libdef 旁边的测试。这是查看最新推荐实践的最佳方式。

## 链接

*   [询问所需注释](https://medium.com/flow-type/asking-for-required-annotations-64d4f9c1edf8)
*   [react-redux_v5.x.x 增加支持流量 0.89+ #3012](https://github.com/flow-typed/flow-typed/pull/3012)
*   【0.85 之后的 type connect(来自 flow-typed/react-redux)官方的方式是什么？#7493
*   [0.89+](https://github.com/flow-typed/flow-typed/blob/master/definitions/npm/react-redux_v5.x.x/flow_v0.89.x-/test_connect.js)流量连接的流量型测试
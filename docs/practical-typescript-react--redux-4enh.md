# 实用打字稿。反应+还原

> 原文：<https://dev.to/voronar/practical-typescript-react--redux-4enh>

### 前言

今天，任何比`hello world`应用程序更复杂的现代前端开发过程，其中不同的团队在一个项目下工作，都对代码质量提出了很高的要求。为了在我们的`#gostgroup`前端团队中保持高质量的代码，我们与时俱进，并不怕使用现代技术，这些技术在[不同规模的公司](https://www.typescriptlang.org/community/friends.html)的一些项目中显示出它们的实际利益。

有很多文章以 TypeScript 为例讨论静态类型的好处，但是今天我们关注我们最喜欢的(在`#gostgroup`中，我想你也是)前端堆栈(React + Redux)中更实际的问题。

### “我根本不知道没有强静打字你是怎么活的。你是做什么的？整天调试你的代码？”未知的人。

### “不，我们整天写打字。”我的同事。

许多人抱怨这样一个事实:用 TypeScript(这里和接下来我指的是 subject stack)编写代码迫使你花费大量时间手工编写类型。一个很好的例子是`react-redux`库中的`connect`函数:

```
type Props = {
  a: number,
  b: string;
  action1: (a: number) => void;
  action2: (b: string) => void;
}

class Component extends React.PureComponent<Props> { }

connect(
  (state: RootStore) => ({
    a: state.a,
    b: state.b,
  }), {
    action1,
    action2,
  },
)(Component); 
```

这里的问题是什么？正如您所看到的，对于通过连接器传递的每个新注入的属性，我们必须在通用 React 组件属性类型中声明属性类型。非常无聊的东西。如果我们有可能将所有连接器注入的属性类型自动合并到一个通用类型中，并将该类型与通用的 React 组件属性类型结合起来，这将是一件很酷的事情。我有好消息告诉你。现在我们可以用 TypeScript 做这种很棒的打字工作。准备好了吗？走吧。

### 超强的打字力

TypeScript 不长期停滞不前，快速进步(我很喜欢)。从 2.8 版本开始，我们有了非常有趣的特性(条件类型)，允许我们“表达非统一类型映射”。我不会在这里停下来给你深入解释这个特性，只是给[留下一个到文档](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-8.html)的链接，其中有一个例子:

```
type TypeName<T> =
  T extends string ? "string" :
  T extends number ? "number" :
  T extends boolean ? "boolean" :
  T extends undefined ? "undefined" :
  T extends Function ? "function" :
  "object";

type T0 = TypeName<string>;  // "string"
type T1 = TypeName<"a">;  // "string"
type T2 = TypeName<true>;  // "boolean"
type T3 = TypeName<() => void>;  // "function"
type T4 = TypeName<string[]>;  // "object" 
```

这个功能如何帮助我们解决问题？在 [`react-redux`库类型](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/react-redux/index.d.ts)中有一个`InferableComponentEnhancerWithProps`类型，它隐藏了公共组件接口中注入的属性类型(当我们通过`JSX`实例化一个组件时，必须显式传递的属性)。`InferableComponentEnhancerWithProps` type 有两个泛型类型参数:`TInjectedProps`和`TNeedsProps`。我们对第一个感兴趣。让我们试着从真正的连接器中“拉出”这种类型！

```
type TypeOfConnect<T> = T extends InferableComponentEnhancerWithProps<infer Props, infer _>
  ? Props
  : never
; 
```

让我展示一个来自[库](https://github.com/Voronar/ts-react-redux/blob/master/src/App.tsx) :
的真实例子

```
import React from 'react';
import { connect } from 'react-redux';

import { RootStore, init, TypeOfConnect, thunkAction, unboxThunk } from 'src/redux';

const storeEnhancer = connect(
  (state: RootStore) => ({
    ...state,
  }), {
    init,
    thunkAction: unboxThunk(thunkAction),
  }
);

type AppProps = {}
  & TypeOfConnect<typeof storeEnhancer>
;

class App extends React.PureComponent<AppProps> {
  componentDidMount() {
    this.props.init();
    this.props.thunkAction(3000);
  }
  render() {
    return (
      <>
        <div>{this.props.a}</div>
        <div>{this.props.b}</div>
        <div>{String(this.props.c)}</div>
      </>
    );
  }
}

export default storeEnhancer(App); 
```

在上面的例子中，我们将连接分为两个阶段进行存储。在第一个示例中，我们将 redux store enhancer 分配给`storeEnhancer`变量(它有`InferableComponentEnhancerWithProps`类型),以便用我们的`TypeOfConnect`类型助手提取注入的属性类型，并通过交集运算符`&`将推断的类型与自己的组件属性类型连接起来。在第二阶段，我们修饰我们的源组件。现在，无论您向 connector 添加什么属性，它都将始终在我们的组件属性类型中。厉害！这就是我们想要实现的一切！

Mind-coder 注意到 thunk 动作包装了特殊的`unboxThunk`函数。我们为什么要这么做？让我们把这件事搞清楚。首先让我们看看来自 repo 的教程应用程序的 thunk 动作签名:

```
const thunkAction = (delay: number): ThunkAction<void, RootStore, void, AnyAction> => (dispatch) => {
  console.log('waiting for', delay);
  setTimeout(() => {
    console.log('reset');
    dispatch(reset());
  }, delay);
}; 
```

正如我们在函数签名中看到的，thunk action 并不立即返回主动作体，而是返回 redux 中间件 dispatcher 的特殊函数。在重复动作中产生副作用是常见的方式。然而，当我们在组件中使用该动作的绑定版本时，它具有没有中间函数的“剪切”形式。如何声明这个可变的函数签名？我们需要一个特殊的变压器。打字稿再次向我们展示了他的超能力。首先，让我们声明一个从任何函数签名中截取中间函数类型:

```
CutMiddleFunction<T> = T extends (...arg: infer Args) => (...args: any[]) => infer R
  ? (...arg: Args) => R
  : never
; 
```

这里我们使用了 TypeScript 3.0 中另一个很酷的新人，它允许我们推断函数 rest 参数类型(更多细节请参见文档)。接下来我们可以定义我们的“函数切割器”:

```
const unboxThunk = <Args extends any[], R, S, E, A extends Action<any>>(
  thunkFn: (...args: Args) => ThunkAction<R, S, E, A>,
) => (
  thunkFn as any as CutMiddleFunction<typeof thunkFn>
); 
```

现在，我们只需用这个转换器包装我们的源 thunk 操作，并在连接器中使用它。

通过这种简单的方式，我们减少了打字的手工工作。如果你想更深入，你可以试一下 [redux-modus](https://www.npmjs.com/package/@gostgroup/redux-modus) 库，它以类型安全的方式简化了动作和减速器的创建。

当你试图使用像`redux.bindActionCreators`这样的动作绑定工具时，你将不得不更加注意正确的类型推断，而不是开箱即用。

**更新 0**
如果有人喜欢这个解决方案，你可以[竖起大拇指](https://github.com/DefinitelyTyped/DefinitelyTyped/issues/31227#issuecomment-446681805)查看`@types/react-redux`包中的这个实用程序类型。

**更新 1**

一些有用的实用程序类型。不再需要为 hoc 手动声明注入的属性类型。只需给你 hoc 并自动提取其注入的属性类型:

```
export type BasicHoc<T> = (Component: React.ComponentType<T>) => React.ComponentType<any>;
export type ConfiguredHoc<T> = (...args: any[]) => (Component: React.ComponentType<T>) => React.ComponentType<any>;

export type BasicHocProps<T> = T extends BasicHoc<infer Props> ? Props : never;
export type ConfiguredHocProps<T> = T extends ConfiguredHoc<infer Props> ? Props : never;

export type HocProps<T> = T extends BasicHoc<any>
  ? BasicHocProps<T> : T extends ConfiguredHoc<any>
  ? ConfiguredHocProps<T> : never
;

const basicHoc = (Component: React.ComponentType<{a: number}>) => class extends React.Component {};
const configuredHoc = (opts: any) => (Component: React.ComponentType<{a: number}>) => class extends React.Component {};

type props1 = HocProps<typeof basicHoc>; // {a: number}
type props2 = HocProps<typeof configuredHoc>; // {a: number} 
```

**update 2**T3【以 [ConnectedProps](https://react-redux.js.org/using-react-redux/static-typing#recommendations) 类型的形式合并到`react-redux`上游代码库。
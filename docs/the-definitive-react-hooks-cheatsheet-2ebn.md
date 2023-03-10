# 最终反应钩 Cheatsheet

> 原文：<https://dev.to/antjanus/the-definitive-react-hooks-cheatsheet-2ebn>

React Hooks 是 React 世界的新热点。我正在稳定地写越来越多的内容，我认为有一个备忘单可以参考，它包含了基本的钩子和错综复杂的`useEffect`。查看官方的[钩子 API 参考](https://reactjs.org/docs/hooks-reference.html)获得更多深入的信息。

**目录**

*   [使用生命周期方法的效果](#useeffect-for-lifecycle-methods)
    *   [componentDidUpdate+componentDidMount](#substitute-for-componentdidupdate-componentdidmount)
    *   [componentDidMount + componentWillUnmount](#substitute-for-componentdidmount-componentwillunmount)
*   [使用一般副作用的效果](#useeffect-for-side-effects)
*   [使用状态](#usestate)
*   [useReducer](#usereducer)
*   [打造自己的钩子](#building-your-own-hooks)

## 使用效果(对于生命周期方法)

除了其他功能之外，它还允许你编写自己的副作用，并在需要时触发重新渲染。

但为了更简单，useEffect 也替代了生命周期方法。来说说他们吧。

### 替换 componentDidUpdate+componentDidMount

它什么时候运行？在每次渲染时

**有什么蹊跷？**它不仅仅是一个`componentDidUpdate`的替代品，它还可以在挂载上运行。所以不是 1 比 1

**重要特性？** useEffect 可以接受第二个参数，你*可以*跳过那个参数。你也可以返回一个函数，我们将在下一节讨论。

**代码沙盒游乐场:** [去玩吧](https://codesandbox.io/s/1r8nrzpl9j)

**语法:**

```
import { useEffect } from 'react';

useEffect(() => {
  // whatever runs here will run on each re-render
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 替换 componentidmount+componentWillUnmount

它什么时候运行？在组件安装和卸载时

有什么好处？语法非常接近前面的用例。这让我困惑了好几次，但是一旦你看了文件就明白了。如果效果运行不止一次，请确保您传递了第二个参数

**重要特性？**这是一个只在*运行一次*的效果。装载逻辑放在效果函数的主体中，卸载/清理逻辑放在从效果返回的函数中。

**代码沙盒游乐场:** [去玩吧](https://codesandbox.io/s/xjrk36x244)

**语法:**

```
import { useEffect } from 'react';

useEffect(() => {
  // run mount logic here such as fetching some data

  return () => {
    // unmount logic goes here
  };
}, []); // note the empty array 
```

Enter fullscreen mode Exit fullscreen mode

您可以将`mount`或`unmount`逻辑保留为空，只处理其中一个生命周期替代。这意味着:

1.  将`mount`逻辑留空，以便仅运行`unmount`逻辑(仅替代`componentWillUnmount`)
2.  不返回任何内容，因此仅运行`mount`逻辑(仅替代`componentDidMount`)

## 为副作用使用 Effect

的主要目标是囊括你可能想要使用的任何副作用。副作用本质上是你在你的组件内做的影响整个世界的*事情*。无论是网络请求、设置文档标题还是其他什么。

### 必要时运行

**什么时候运行？**当组件重新渲染时，`useEffect`会检查依赖关系。如果依赖值改变了，useEffect 将运行效果

有什么好处？ React 做一个粗浅的比较。如果你使用一个你变异了的对象或者数组，React 会认为什么都没变。

**重要特性** useEffect 在事物不变时跳过运行效果。您实际上不必在效果中使用依赖值。您可以将一个属性值作为依赖项传入。

**代码沙盒游乐场:** [去玩吧](https://codesandbox.io/s/j14zoq7095)

**语法:**

```
import { useEffect } from 'react';

function SomeComponent(props) { 
    useEffect(() => {
      // logic runs only when dependency variables changed
    }, [arrOfDependency, values, props.id]); // array of values to check if they've changed
} 
```

Enter fullscreen mode Exit fullscreen mode

**潜在用例**

由于钩子更难解释，我想提供一个用例列表

1.  当一个属性改变以获取新数据时，运行一个副作用(比如获取)
2.  仅当计算值发生变化时，才运行资源密集型计算
3.  当值更新时更新页面(如文档标题)

## 使用状态

状态可能是人们从无状态(功能)组件转向类组件的*和*原因。允许我们拥有没有类的有状态组件。

**它返回什么？**当前状态和让您设置状态的功能

有什么好处？状态设置功能将用新的状态替换以前的状态，而不是像类状态那样合并它们。在设置状态之前，您需要自己合并对象。

**重要特性**你可以在你的组件中使用任意数量的`useState`钩子。向`useState`传递任何值都会创建初始状态。不调用变量`state`和`setState`，而是使用上下文名称(例如`user`和`setUser`)也是一个惯例。`useState`接受任何状态值，不必是对象。

**代码沙盒游乐场:** [查看使用状态示例](https://codesandbox.io/s/53ovy28krn)

**语法:**

```
import { useState } from 'react';

// setup
const defaultValue = { name: "Antonin" };
const [state, setState] = useState(defaultValue);

// scenario 1 usage
// resulting state only contains key `user` with value 'antjanus'
setState({ user: 'antjanus' }); 

// scenario 2 usage
// resulting state contains key `name` with value 'A. Januska'
setState({ name: 'A. Januska' }); 

// scenario 3 usage
// resulting state is a merger between `{ name: 'A. Januska' }` and `{ user: 'antjanus'}`
setState({ ...state, user: 'antjanus'}); 
```

Enter fullscreen mode Exit fullscreen mode

## 用户

`useReducer`是`useState`的替代物，如果你过去用过 Redux，这看起来会很熟悉。

**有哪些论点？它返回什么？** `useReducer`接受一个`reducer`函数和`initialState`。它返回当前的`state`和一个`dispatcher`(听起来熟悉吗？)

**它是如何运行的？**在状态改变时，`dispatch`一个具有类型和数据有效载荷的对象(阅读[通量标准动作](https://github.com/redux-utilities/flux-standard-action)了解更多信息)。我们传递给 useReducer 的`reducer`将接收当前状态和被分派的对象。它返回新的状态。

有什么好处？这是一个更复杂的工作流程，但如果你使用过 Redux，它的工作方式就像你预期的一样。

**重要特性**减速器每次调度都会运行。它可以访问以前的状态。`useReducer`还包括第三个参数，可以用来创建初始状态

**代码沙盒游乐场:** [查看 useReducer 示例](https://codesandbox.io/s/zqj3j03jpl)

**语法**

```
import { useReducer } from 'react';

function reducer(currentState, action) {
  switch(action.type) {
     // handle each action type and how it affects the current state here
  }
}

function SomeComponent() {
  const [state, dispatch] = useReducer(reducer, initialState);

  dispatch({ type: 'ADD', payload: data }); // { type: 'ADD', payload: data } gets passed into the `reducer` as the `action` argument while `state` gets passed in as the `currentState` argument
} 
```

Enter fullscreen mode Exit fullscreen mode

## 打造自己的钩子

关于建立自己的钩子的快速说明。这就像使用现有的钩子并在一个以`use`开头的函数内将它们组合在一起一样简单。这里有一个`useUser`钩子的简单例子。

**有什么要求？**该函数以关键字`use`开始。例如`useUser`或`useSomethingElse`。

**重要特性:**你可以调用你定制的钩子中的任何钩子，它会像预期的那样工作。

**代码沙盒游乐场:** [查看自定义钩子示例](https://codesandbox.io/s/2wzponv3oy)

**语法:**

```
import { useEffect } from 'react';

function useUser(userId) {
  let [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/user/${userId}`)
        .then(data => data.toJSON())
        .then(data => setUser(data));
  }, [userId]);

  return user;
}

function SomeComponent(props) {
  const user = useUser(props.id);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 剩下的呢？

您还可以使用其他挂钩，如`useMemo`、`useCallback`等。我会说那些是更高级的钩子，如果你理解了基本的钩子，就去看看[的官方文件](https://reactjs.org/docs/hooks-reference.html)。

我也知道其中有一些高级用法的例子(比如将 useReducer 的`dispatch`向下传递几级)。

如果您发现不正确的地方或一些有用的额外信息没有包括在内，请告诉我！我会把它包括在内！

> 你觉得备忘单有用吗？请给我买一杯咖啡，这样我就可以继续做下去，产生更多的内容！:)你也可以[在 Twitter 上关注我](https://twitter.com/AntJanus)
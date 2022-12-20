# 跳过 React 中的渲染和记忆

> 原文：<https://dev.to/nickymeuleman/skipping-renders-and-memoization-in-react-5ke>

在许多情况下，React 组件会在不需要的时候重新渲染。
如果渲染的结果与组件上一次渲染的结果完全相同，那么最好完全跳过渲染([协调](https://reactjs.org/docs/reconciliation.html))步骤。

## 基于组件的类

### `shouldComponentUpdate`

在类组件中，方法`shouldComponentUpdate`允许这样做。
是 [`render()`](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/) 之前调用的[生命周期方法](https://reactjs.org/docs/react-component.html#commonly-used-lifecycle-methods)。该方法返回一个布尔值。布尔告诉 React 是否可以跳过`render()`。

当`true`时，`render()`会像平常一样执行。

当`false`时，告知**可以**跳过执行`render()`。

`shouldComponentUpdate()`用下一个道具和下一个状态调用。这允许复杂的逻辑，其中当前的属性/状态与先前的属性/状态进行比较，以便确定输出是否会不同，因此，*组件应该更新*。

默认情况下，`shouldComponentUpdate()`返回`true`。
完全不指定该方法等同于

```
shouldComponentUpdate(nextProps, nextState) {
 return true
} 
```

不要依靠这个来完全阻止渲染。它可能现在就能工作，但是它可能会导致错误，并且可能会在未来发生变化。相反，把它当作一个反应的提示，告诉它*“你可以安全地跳过渲染这一步，无论如何结果将与之前的结果相同”*。

`shouldComponentUpdate`中的逻辑可能很快变得非常复杂，并且容易出错。
不知不觉中，这个方法看起来就像这样

```
shouldComponentUpdate(nextProps, nextState) {
  const propsComparison = this.props.a !== nextProps.a && this.props.b !== nextProps.b && this.props.c !== nextProps.c && this.props.d !== nextProps.d
  const stateComparison = this.state.one !== nextState.one && this.state.two !== nextState.two && this.state.three !== nextState.three
 return propsComparison && stateComparison
} 
```

*😢我只是想检查一下**是否有任何**道具或状态改变了，为什么这么难？*

### `React.PureComponent`

`React.PureComponent`确实如此！😎

`PureComponent`进行道具和状态的浅层比较(通过使用 [Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) )。
这减少了你跳过必要更新的机会(例如，当你添加新道具时)。

除非你确信自己需要一个定制的`shouldComponentUpdate`，否则还是选择`PureComponent`。

这意味着这两个片段是等价的

```
class Driver extends React.Component {
  shouldComponentUpdate() {
    // a shallow comparison of all the props and state
  }
  render() {
    <p>{this.props.name}</p>;
  }
} 
```

```
class Driver extends React.PureComponent {
  render() {
    <p>{this.props.name}</p>;
  }
} 
```

## 功能组件

当试图将同样的优化应用于函数组件而不是基于类的组件时，一个问题出现了。功能组件不能真正跳过渲染步骤。函数组件(实际上只是一个函数)要么被执行，要么不被执行。

这就是[记忆](https://en.wikipedia.org/wiki/Memoization)帮助的地方。

记忆化基本上是*为了以后的*记住某事的技术术语。

React 不能只记住数据片段，它可以记住整个组件。

### `React.memo`

`React.memo`确实如此！

前两个例子是基于类的组件，`React.memo`是功能组件。

不像在基于类的组件中那样跳过渲染步骤，`React.memo`将重用上一次渲染的结果，而不是计算新的结果。

```
// the function component
const Driver = function(props) {
  return <p>{props.name}</p>;
};
// exporting the memoized function component
export default React.memo(Driver); 
```

*   带道具的记忆驱动组件的初始渲染`{ name: "Charles Leclerc" }`
    *   函数组件渲染`<p>Charles Leclerc</p>`。
*   道具变为`{ name: "Daniel Ricciardo" }`
    *   组件渲染`<p>Daniel Ricciardo</p>`
*   其他一些变化触发了我们的驱动组件的更新
    *   看到道具没变。
    *   React 不计算渲染结果，而是使用之前的结果:`<p>Daniel Ricciardo</p>`

默认情况下，`React.memo`与`React.PureComponent`相当，因为它对所有道具进行了浅层比较(再次使用 [Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) )。
如果你想要更多的控制权并负责比较，`React.memo`接受第二个参数，一个比较函数。这使得它可以与基于类的组件中的`shouldComponentUpdate`相媲美。

比较函数也返回一个布尔值。
布尔值告诉 React 是否应该使用组件以前的结果，而不是计算新的结果。

当`false`时，功能组件将正常执行。

当`true`时，将不执行该功能组件，而是使用之前的结果。

> 小心！这和`shouldComponentUpdate`正好相反！

用上一个道具和下一个道具调用比较函数。这允许复杂的逻辑，其中当前属性与先前属性进行比较，以确定输出是否不同，因此，应该使用组件的*记忆结果/备忘录*。

```
// the function component
const Driver = function(props) {
  return <p>{props.name}</p>;
};
// the custom comparison function
const comparisonFn = function(prevProps, nextProps) {
  return prevProps.name === nextProps.name;
};
// exporting the memoized function component
export default React.memo(Driver, comparisonFn); 
```

用基于类的组件扩展 parallels:
除非你确信你需要一个定制的比较函数，否则最好使用默认行为。

## 例子

在这个演示中，有一个包含两种状态的顶层组件，一个`count`和一个`unusedCount`。顾名思义，它将一直处于闲置状态🙃。
可以通过按钮增加`count`和`unusedCount`。

顶部组件有 4 个子组件，它们都将显示`count`以及子组件渲染了多少次。

具有上述优化之一的组件将仅在`count`更新时呈现。其他的也会在`unusedCount`更新的时候渲染。

[https://codesandbox.io/embed/lr7wmjlv1l](https://codesandbox.io/embed/lr7wmjlv1l)

[直接代码沙盒链接](https://codesandbox.io/s/lr7wmjlv1l)

## `React.memo` vs `React.useMemo`

而 **`React.memo`** 是[的高阶分量](https://reactjs.org/docs/higher-order-components.html)，因为它接受一个分量并返回新的/记忆的**分量**。

**`React.useMemo`** 是一个[钩子](https://reactjs.org/docs/hooks-intro.html)(这是一个函数)。它接受一个函数，并返回您传递的函数的记忆化返回值**。**

### `React.useMemo`

```
const memoizedValue = React.useMemo(() => computeExpensiveValue(a, b), [a, b]); 
```

`React.useMemo`接受函数作为第一个参数。这个函数返回的值就是`React.useMemo`将要返回的值。只有在必要时才会重新计算。如果没有，T2 将返回记忆值。

你告诉`React.useMemo`它是否应该通过第二个参数，一个数组来重新计算结果。如果*依赖项*数组中的某些内容发生变化，传递的函数返回值将会被重新计算。不传递任何内容会导致每次组件呈现时都计算该值(并导致函数运行)。

您传递的函数中使用的每个值都应该包含在 dependencies 数组中。这将防止许多意想不到的行为。

React 团队已经创建了一个 ESLint 包， [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks) ，它的设计是为了在你违反[钩子](https://reactjs.org/docs/hooks-rules.html)的规则时发出警告。dependencies 数组的完整性由包中一个名为`exhaustive-deps`的规则来检查。

### 举例

```
import React from 'react';

function calculatePodiums(name) {
  // very expensive calculation
  return numResult;
}

const Driver = function(props) {
  const numOfPodiums = React.useMemo(() => calculatePodiums(props.name), [
    props.name
  ]);
  return (
    <div>
      <p>My name is: {props.name}</p>
      <p>I drive for: {props.team}</p>
      <p>I have been on the podium {numOfPodiums} times</p>
    </div>
  );
}; 
```

*   使用道具`{ name: "Kimi Räikkönen", team: "Ferrari" }`对我们的驱动组件进行初始渲染
    *   函数组件计算`numOfPodiums`并使用该计算的结果进行渲染。
*   道具变为`{ name: "Kimi Räikkönen", team: "Alfa Romeo Racing" }`
    *   `React.useMemo`在依赖关系数组中没有发现任何变化，并且**没有**重新计算`numOfPodiums`
    *   使用`numOfPodiums`的备忘录/记忆值。
*   道具再次变为`{ name: "Antonio Giovinazzi", team: "Alfa Romeo Racing" }`
    *   `React.useMemo`发现依赖关系数组中有变化，并计算`numOfPodiums`
    *   使用新计算的值。

### 加成:react . use 回调

这是特定`React.useMemo`用法的快捷方式。

`React.useMemo`返回一个被记忆的**值**

`React.useCallback`返回一个被记忆的**函数**

*🤔但是一个值完全可以是一个函数！*

正确！
这意味着这两个片段是等价的

```
const memoizedFunction = React.useMemo(function() {
  return function doTheThing(a, b) {
    // do the thing
  }
}
}, [a, b]) 
```

⬆这个函数记住了第一个参数(一个函数)返回的值，这个函数叫做`doTheThing`。

```
const memoizedFunction = React.useCallback(function doTheThing(a, b) {
    // do the thing
  }
}, [a, b]) 
```

⬆这记住了第一个参数，它是一个叫做`doTheThing`的函数。

像`React.useMemo`一样，第二个参数是一个依赖数组。
函数`React.useCallback`返回的值只有在数组中的内容发生变化时才会改变。
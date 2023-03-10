# 用钩子反应组件生命周期

> 原文：<https://dev.to/elanandkumar/react-component-lifecycle-with-hook-6lo>

在前一篇文章中，我解释了如何将类组件转换成功能组件，这样我们就可以利用钩子。
以下是文章链接:

[![elanandkumar image](img/7e9dada9c16e0237b2b8a7563fef0e44.png)](/elanandkumar) [## React 钩子:将类组件迁移到函数中并使用钩子

### Anand Kumar 5 月 12 日 1915 分钟阅读

#react #reacthooks #javascript](/elanandkumar/react-hooks-migrate-class-component-to-hooks-3879)

在这篇文章中，我将帮助你理解如何获得与使用 react 钩子的组件生命周期相同的输出。

我将介绍以下生命周期方法:

*   `componentWillMount`
*   `componentDidMount`
*   `componentDidUpdate`
*   `shouldComponentUpdate`
*   `componentWillUnmount`

在我们开始之前，让我们先了解一下下面的钩子:

*   `useState`
*   `useEffect` ### useState `useState`返回一个有状态值和一个更新它的函数。####语法:![useState syntax](img/d611d9f428b533fc23c6a1092bb493e5.png) ### useEffect 对于功能组件，我们不允许**在组件主体内部产生副作用，比如突变、计时器、突变或任何其他副作用。**

 **所以，这里我们有`useEffect`，它在渲染发生后运行**。默认情况下，效果会在每次渲染后运行，但是有一些方法可以控制它，我们将在文章的后面讨论。**

#### 语法:

[![useEffect syntax](img/162745dbfc697827e2cfd446a68490eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kO8YMRFt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.elanandkumar.com/static/a0e61e77cdc8d734938c1f31ba58d450/9ab67/useEffect-syntax.jpg)

有关钩子或 react 提供的任何其他钩子的更多细节，请查看官方文档。

所以，让我们从组件生命周期开始，试着理解钩子对组件生命周期的替代。

> **Pro 提示:**不要在任何代码块中使用钩子。它必须位于功能组件的顶级层次。

让我们看看一个小应用程序的运行示例。这种用法反应了基于类的组件和生命周期。在文章的最后，您可以检查使用功能组件创建的相同应用程序，并具有相似的生命周期执行。
[https://codepen.io/anand-kr/embed/yWzqZd?height=600&default-tab=js,result&embed-version=2](https://codepen.io/anand-kr/embed/yWzqZd?height=600&default-tab=js,result&embed-version=2)
我们开始吧。

### componentWillMount

当我们将任何类组件转换为功能组件时，我们可以在使用`useState`定义我们的状态(如果有的话)之后或者在代码到达 render (jsx)部分以实现`componentWillMount`行为之前编写代码。正如我们所知，代码执行是自上而下进行的。

### 组件装载

正如之前关于`useEffect`的解释，我们可以传递第二个 optionalParameter 来实现`componentDidMount`行为。让我们看看下面的代码片段。

```
React.useEffect(()=>{
  console.log('Robot: componentDidMount');
  // ...code goes here...
  fetchData();
}, []); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们将空白数组`[]`作为第五行的第二个参数。为什么？🤔

因为当提供第二个参数时，只有当它检测到所提供的任何条目中的变化时，它才帮助调用效果。这个数组依赖于我们在`useEffect`中作为第一个参数传递的函数。

在这种情况下，它是空白的，这意味着它永远不会检测到任何变化，因此它将只调用一次。这不就是`componentDidMount`做的吗？只调用一次，对吗？

> 请注意，如果第二个参数没有在`useEffect`中传递，它将总是在渲染后执行。

### componentDidUpdate

比方说，如果组件的任何属性发生变化，我们需要再次调用 api 调用，一旦组件更新，我们就在这里这样做。我们可以使用`useEffect`来实现。不过，这一次，我们需要向第二个参数传递一个条目。

```
React.useEffect(() => {
  console.log('Robot: componentWillUpdate');
  // code goes here
  fetchData();
}, [props.selectedRobotId]); 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们在数组中传递了一个道具`selectedRobotId`。因此，每当这个道具要改变时，效果就会运行，我们就会得到更新的组件。

### shouldcomponentdupdate

这个有点复杂，但是让我来帮你理解。众所周知，这种生命周期方法帮助我们自己控制组件的渲染。下面是一个例子:

```
shouldComponentUpdate(nextProps, nextState) {
  console.log("Robot: shouldComponentUpdate");
  return (
    nextProps.selectedRobotId !== this.props.selectedRobotId ||
    nextState.loadedRobot.id !== this.state.loadedRobot.id ||
    nextState.isLoading !== this.state.isLoading
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，对于功能组件，我们不能以这种方式使用反应组件生命周期方法。那么，如何达到同样的结果呢？

嗯，对于 react，我们有一个叫做`React.memo`的东西。我们可以这样包装组件，如下所示:

```
export default React.memo(Robot); 
```

Enter fullscreen mode Exit fullscreen mode

这看起来像是`shouldComponentUpdate`的替代品吗？不.....

好吧，事情是这样的。`React.memo`接受第二个参数(一个函数),它提供了对`nextProps`和`prevProps`的访问。这可以帮助我们实现和`shouldComponentUpdate`一样的东西。

```
export default React.memo(Robot, (prevProps, nextProps) => {
  console.log('Robot: shouldComponentUpdate');
  return nextProps.selectedRobotId === prevProps.selectedRobotId;
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 这里有一点需要注意。这与`shouldComponentUpdate`相反。如果不想重新渲染，则返回 **true** 。如果你想让它渲染，你可以返回 **false** 。

React 很好地处理了这个问题，所以第二个函数只在你不想重新渲染任何道具变化时才需要。否则，让 React 为您处理它。:)

### componentWillUnmount

请记住，在对`useEffect`的解释中，我提到过在`useEffect`内部有一个可选的返回函数。这个返回函数用于清理的目的。

```
React.useEffect(() => {
  console.log('Robot: componentDidUpdate');
  //...code goes here
  fetchData();
  return () => {
    console.log('cleaning up...');
  }
}, [props.selectedRobotId]) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段类似于`componentDidUpdate`代码块，除了它现在有一个返回函数。因此，每当`props.selectedRobotId`改变时，这个效果运行，返回函数在效果完成时调用，我们可以在那里进行清理。但是，这并不是发生在 T2 身上的事情。对吗？
好了，为了实现`componentWillUnmount`，我们可以在代码中再添加一个`useEffect`，并将第二个参数作为空白数组传递(`[]`)。与`componentDidMount`类似，但`useEffect`这次内部将有一个返回功能，它将作为`componentWillUnmount`工作。

```
React.useEffect(() => {
  //...code goes here
  fetchData();
  return () => {
    console.log('cleaning up...');
  }
}, [props.selectedRobotId]);

React.useEffect(() => {
  //...code goes here
  fetchData();
  return () => {
    console.log('Robot: componentWillUnmount');
  }
}, []); 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们在两个层面上进行清理的方法。一次是在组件更新时，另一次是在组件准备卸载时。

## 总结

在本文中，我们讨论了`useState`和`useEffect`。然后我们使用`useEffect`和`memo`来实现基于类的组件的组件生命周期方法。

起初，这可能看起来令人困惑，但不要担心。这是同一个应用程序的 codepen(如上所示),但是使用了 React 钩子。

[https://codepen.io/anand-kr/embed/KLXGJQ?height=600&default-tab=js,result&embed-version=2](https://codepen.io/anand-kr/embed/KLXGJQ?height=600&default-tab=js,result&embed-version=2)

请检查代码和实践。此外，如果您有任何问题/顾虑，请随时分享或联系我。

快乐学习！**
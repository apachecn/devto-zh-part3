# 我的 React 组件状态中的奇怪延迟似乎累积了旧的状态。

> 原文：<https://dev.to/hocoh/strange-lag-in-my-react-component---state-seems-to-accumulate-old-state--sandbox-provided-1ip2>

我在 render()函数中放了一个 console.log 来帮助可视化状态。

**这里我的沙盒:**【https://codesandbox.io/s/77ymmnpr1 T2】

**我在做什么:**

在我的代码中，我创建了三个可扩展的标签，然后我放了一些 labelComponent 来覆盖它们。覆盖层有一个按钮返回菜单。问题是，当我展开另一个 labelComponent 时，旧状态与新状态之间会有一点延迟。因此，我向您提供了 console.log。

在控制台中输入如下内容:

```
PATHNAME: newState

PATHNAME: oldState

PATHNAME: newState

PATHNAME: oldState 
```

编辑:其他可能的怪异行为是我的组件以增量滞后方式显示旧状态。似乎状态积累了旧的状态，然后以一种混乱的顺序显示出来，或者类似的东西。

在这种情况下，我的控制台在第二次渲染时返回:

```
stateOne

current state 
```

然后，第三次渲染时:

```
stateOne

stateTwo

currentState 
```

第四次渲染时:

```
stateOne

stateTwo

stateThree

currentState 
```

等等。第一个状态是旧状态，然后是当前状态

那是什么意思？我调用 this.setState()只是为了把新的状态放入其中，那么为什么它会回到旧的状态呢？

任何提示都很好，

谢谢
# 用 React 钩子控制组件可见性

> 原文：<https://dev.to/craicoverflow/controlling-component-visibility-with-react-hooks-32km>

[![](img/0526d47611004c36cd1514d610c9dd45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QyINTFjm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/f83hzUs.jpg)

随着 React 16.8 的发布，*钩子*是世界上最流行的前端框架的最新成员。钩子让我们能够从功能组件中访问状态和其他生命周期事件。在此之前，功能组件总是无状态的。为了使用状态，我们必须编写类组件，这些组件比它们的对应函数更加冗长和复杂。

在本指南中，我们将制作一个简单的组件，它将保持隐藏状态，直到单击一个按钮。我们还将设置一个超时事件，在设定的持续时间后隐藏组件。

为此，我们将使用两个钩子- `useState`和`useEffect`。当我们实现它们时，我将解释它们做什么，但是现在让我们定义组件和它的道具。

```
export const Alert = ({ visible, duration, onDurationEnd, children }) => {
    return children;
}; 
```

我定义了一个有四个支柱的功能组件。

*   `visible`(必选)-这将是一个布尔值，可以是`true`或`false`。它控制组件的当前可见状态。
*   `duration`(可选)-这是组件在再次隐藏之前应该显示的持续时间(毫秒)。如果未设置，组件将保持可见。
*   `onDurationEnd`(可选)-持续时间结束后执行的回调函数。通常用于将组件的可见性从父组件重置为`false`。
*   (必需)-这可以是任何东西，从单个单词到多个组件。`children`作为子元素添加到组件中。

### 状态

允许我们使用和设置组件状态，这种状态在渲染中是持久的。`useState`返回一对，当前状态值和一个修改它的函数。最后，`useState`使用一个参数来设置初始值。

```
import React, { useState } from "react";

export const Alert = ({ visible, duration, onDurationEnd, children }) => {
    const [isVisible, setVisibility] = useState(null);

    return children;
}; 
```

这里我们设置了一个钩子来控制组件的可见性。初始状态值是`null`,因为它几乎会在渲染后立即被覆盖。

我真正喜欢`useState`的是你可以在一个组件中多次声明它。

```
const [isVisible, setVisibility] = useState(null);
// defining a second state variable
const [message, setMessage] = useState(null); 
```

这样做的好处是，我们可以分离不同状态值的控制。在类组件中，所有状态值都在一个对象中。所以如果你想更新一个，你也必须更新其余的。

```
function setVisibility(visible) {
    this.setState({
        ...this.state,
        isVisible: visible
    });
} 
```

### 效果

`useEffect`让我们从功能组件执行副作用功能。副作用是指影响正在执行的功能之外的东西，比如状态或网络请求。把`useEffect`想成`componentDidMount`、`componentDidUpdate`、`componentWillUpdate`的总和。通过使用这个钩子，您可以告诉 React 在每次渲染后执行钩子内部的逻辑。

```
export const Alert = ({ visible, duration, onDurationEnd, children }) => {
    const [isVisible, setVisibility] = useState(null);

    useEffect(() => {
        setVisibility(visible); // update the state
    }, [visible]); // hook is only triggered when value changes

    return children;
}; 
```

这是做什么的？设置初始状态值后，`useEffect`钩子是下一个要运行的事件。钩子覆盖了`isVisible`的初始值，以匹配从父组件的道具中获取的值。

`useEffect`中的最后一个参数是可选的优化。只有当`visible`的值改变时，效果才会重新运行，防止不必要的重新渲染。

一旦`useEffect`钩子完成，我们要检查`isVisible`的值。如果是`false`，我们不想渲染组件，所以返回`null`。

```
if (!isVisible) return null; 
```

如果`duration`包含一个值，我们需要设置一个超时，一旦持续时间过去，它将重置组件的可见性。如果定义了`onDurationEnd`,那么一旦超时完成，父组件期望控制该组件可见性的值也被重置为`false`。

```
if (duration) {
    setTimeout(() => {
        setVisibility(false);

        // pass `false` back to the parent to update its state
        if (onDurationEnd) {
            onDurationEnd(false);
        }
    }, duration);
} 
```

看看下面完成的组件。React 钩子的引入使得开发组件更快，需要担心的生命周期事件更少。与做同样事情的类组件相比，文件本身减少了许多行。

```
import React, { useState, useEffect } from "react";
import PropTypes from "prop-types";

/**
 * Customisable alert component that remains hidden until called.
 *
 * @param {boolean} props.visible - The current visibility of the component.
 * @param {number} [props.duration] - The time in milliseconds to display the component for. If not set, the component will stay visible.
 * @param {func} onDurationEnd - Set visible state of component from parent.
 * @param {*} props.children - Child components.
 */
export const Alert = ({ visible, duration, onDurationEnd, children }) => {
    const [isVisible, setVisibility] = useState(null);

    useEffect(() => {
        setVisibility(visible);
    }, [visible]);

    if (!isVisible) return null;

    if (duration) {
        setTimeout(() => {
            setVisibility(false);

            if (onDurationEnd) {
                onDurationEnd(false);
            }
        }, duration);
    }

    return children;
};

Alert.propTypes = {
    visible: PropTypes.bool.isRequired,
    timeout: PropTypes.number,
    children: PropTypes.node.isRequired
}; 
```

在 [GitHub](https://github.com/craicoverflow/react-hooks-timeout-example) 上查看[现场演示](https://endaphelan.me/react-hooks-timeout-example/)或本教程的代码。

如果你喜欢这个帖子，请查看[原文](http://bit.ly/2PnJfy4)并订阅更多！
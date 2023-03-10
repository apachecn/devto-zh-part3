# 轻轻介绍反应钩。第一部分

> 原文：<https://dev.to/przemwo/gently-introduction-to-react-hooks-part-1-1a47>

在这一系列文章中，我将回答以下问题:

*   什么是 React 钩子？
*   为什么会有反应钩？
*   React 钩子怎么用？

从现在开始我假设:

1.  你不了解反应钩。
2.  你至少有 React 的基本知识(任何超过 5 分钟的教程就足够了)。

## 我的故事

我已经和 React 合作两年多了。我必须承认这两年过得非常愉快。所以当我第一次听说 React hooks 的时候，我是非常怀疑的。“为什么要改变一些好的和有效的东西？”当我看到第一个钩子的例子时，我“这不是一个好方向”的感觉更加强烈了。但是 hooks 一直从各个方向攻击我，越来越多的人似乎对 React 的新功能感到高兴。我决定给他们一个尝试...我加入了一个快乐的圈子。但首先要做的是。

## 什么是 React 钩子？

钩子的引入是为了取代组件的类创建。用什么代替？替换为函数创建。

哇哦！一个人可以大喊大叫。我们本可以一直创造具有功能的组件。钩子到底是怎么回事？在我回答这个问题之前，让我们后退两步。

## 我们如何在 React 中创建组件？

一般来说，在 React 中创建组件有两种方法。

1.  使用类(类组件)。
2.  使用函数(函数组件)。

功能组件似乎容易得多:

*   人们不必与`this`关键字“搏斗”,记住绑定方法。
*   它们可读性更强，书写速度更快。
*   它们更容易测试和推理。

所以让我们问一个简单的问题...

## 为什么在 React 中创建组件有两种方式？

如果功能组件如此“酷”，为什么不仅仅使用它们呢？为什么首先要使用类呢？

类组件有两个功能组件没有的重要特性:

1.  它们可以有**状态**。
2.  它们提供对组件的**生命周期方法**的访问。

**什么是状态？**它是组件“记住”任何关于自身信息的能力。

例如，按钮组件可以记住用户是否点击了它。并且根据这一点将其本身呈现为绿色或红色。

什么是组件的生命周期方法？组件的生命周期是从浏览器中第一次绘制组件(甚至是之前的某个时刻)开始，直到将其从浏览器中移除。生命周期方法让我们在组件存在的关键时刻执行任何代码。

例如，假设我们想知道按钮的高度。该信息在按钮实际呈现在浏览器中后才可用。多亏了`componentDidMount`,我们可以访问按钮，并在渲染时获得它的高度。

在过去，我们不可能在使用功能组件时使用这些功能。但是从 React 16.8 开始——多亏了 React 钩子的引入——状态和生命周期方法都可以用于函数组件！

## 给我看看一些代码！

让我们从编写类组件的 React 钩子开始我们的冒险。

我们有一个简单的组件来呈现输入字段。用户可以输入他们的名字，它将保存在组件状态中，并显示在输入字段的上方。

```
import React from 'react';

class MyComponent extends React.Component {
    state = {
        userName: "Bob",
    }

    handleUserNameChanged = (e) => {
        this.setState({ userName: e.target.value });
    }

    render() {
        return(
            <>
                <h2>User name: {this.state.userName}</h2>
                <input
                    type="text"
                    value={this.state.userName}
                    onChange={this.handleUserNameChanged}
                />
            </>
        );
    }
} 
```

现在让我们写一个函数组件。目标是编写一个与类组件功能完全相同的组件。让我们从一个空箭头函数开始:

```
import React from 'react';

const MyComponent = () => {
    // code goes here
}; 
```

然后执行以下操作:

1.  复制`render`方法返回的代码。它将由我们的函数组件直接返回。
2.  复制`handleUserNameChanged`方法，并在其前面添加`const`关键字。
3.  我们在函数组件中没有`this`关键字。删除它的所有出现。
4.  我们感兴趣的是`userName`而不是`state.userName`。从代码中删除所有的`state.`。
5.  我们不把`state`定义为一个对象。我们改为定义变量`userName`，并给它一个字符串`"Bob"`作为初始值。
6.  将`setState`改为更具描述性的函数:`setUserName`。我们把从输入字段得到的值传递给它。这个函数将负责改变我们保存在`userName`变量中的值。

我们的函数组件应该如下所示:

```
import React from 'react';

const MyComponent = () => {
    const userName = "Bob";

    const handleUserNameChanged = (e) => {
        setUserName(e.target.value);
    }

    return(
        <>
            <h2>User name: {userName}</h2>
            <input
                type="text"
                value={userName}
                onChange={handleUserNameChanged}
            />
        </>
    );
} 
```

在这个阶段，我们的组件不工作。我们得到关于一个错误的信息:`setUserName`没有被定义。让我们提醒自己`setUserName`应该是什么？应该是一个**函数**改变了`userName`的值。

我们将编写该函数的一个简单实现。这个函数将接受一个新的`userName`值，并且(目前)它将返回当前的`userName`值。

```
const setUserName = (newUserName) => userName; 
```

现在将它添加到我们的函数组件中(第 4 行):

```
import React from 'react';

const MyComponent = () => {
    const userName = "Bob", setUserName = (value) => userName;

    const handleUserNameChanged = (e) => {
        setUserName(e.target.value);
    }

    return(
        <>
            <h2>User name: {userName}</h2>
            <input
                type="text"
                value={userName}
                onChange={handleUserNameChanged}
            />
        </>
    );
} 
```

我们的代码几乎可以工作。几乎是因为它显示输入字段和用户名为“Bob”。但是我们不能更改用户名。为什么？我们缺少组件的**状态**，在其中我们可以保存我们的新用户名。我们想在这里使用状态**。幸运的是，React 给了我们一个`useState`钩子。**

## 使用状态钩子

是一个钩子，让我们在函数组件中使用状态。

`useState` hook 是一个返回包含两个元素的数组的函数:

1.  第一个元素是存储状态值的变量。
2.  第二个元素是一个函数，我们可以用它来改变一个新值的状态。

我们可以传递一个带有初始状态值的参数。可以是任何一个`string`、`number`、`boolean`、`array`或`object`。在我们的例子中，我们传递了`string`“Bob”。

我们可以写:

```
const state = useState("Bob"); // state is an array
const userName = state[0]; // first element is a state's value
const setUserName = state[1]; // second element is a function 
```

感谢**数组析构**我们可以写得更优雅:

```
const [userName, setUserName] = useState("Bob"); 
```

我们可以这样理解:

1.  我们希望使用 state 并将其值保存在名为`userName`的**变量**中。
2.  我们可以通过用新值调用`setUserName` **函数**来改变状态。
3.  我们将初始`userName`值设置为`"Bob"`。

有了这些知识，让我们回到我们的例子。从 React 导入`useState`并在组件中使用。

```
import React, { useState } from 'react'; // import useState hook

const MyComponent = () => {
    const [userName, setUserName] = useState("Bob");

    const handleUserNameChanged = (e) => {
        setUserName(e.target.value);
    }

    return(
        <>
            <h2>User name: {userName}</h2>
            <input
                type="text"
                value={userName}
                onChange={handleUserNameChanged}
            />
        </>
    );
} 
```

现在我们的函数组件应该和类组件完全一样了。感谢 React 的`useState`钩子，我们创建了一个可以有状态的函数组件！

## 太好了，它正在工作，但是那些奇迹在哪里呢？

你可能会想，增加**钩子**来反应并不会给牌桌带来任何惊人的好处。事实上你是对的。如果你比较初始类组件和它的对应函数，就不会有太多的不同。真的很难理解为什么这么多人对 hooks 这么兴奋。

我向你保证一件事。如果你和我一起看完这个系列，你将会大吃一惊！这太棒了。瞬间。至少我有一个。

在[下期](https://dev.to/przemwo/gently-introduction-to-react-hooks-part-2-4lkc)部分见温柔介绍 React Hooks！

*   [轻轻介绍 React Hooks。第一部分](https://dev.to/przemwo/gently-introduction-to-react-hooks-part-1-1a47)
*   [轻轻介绍 React Hooks。第二部分](https://dev.to/przemwo/gently-introduction-to-react-hooks-part-2-4lkc)

* * *

感谢阅读！如果你喜欢这个，让我知道！发表评论，给一个❤️或分享它！

随意查看我的 [Twitter](https://twitter.com/PrzemekWolnik) 账号，有更多这样的内容。
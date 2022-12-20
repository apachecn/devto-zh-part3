# 轻轻介绍反应钩。第二部分

> 原文：<https://dev.to/przemwo/gently-introduction-to-react-hooks-part-2-4lkc>

这是第二部分系列**轻轻介绍反应钩**。如果您还没有阅读第一部分，我鼓励您阅读:

*   [轻轻介绍 React Hooks。第一部分](https://dev.to/przemwo/gently-introduction-to-react-hooks-part-1-1a47)

让我们从我们离开的地方开始。我们的组件工作正常。用户可以在输入字段中输入自己的姓名。让我们给它添加新的功能吧！

我们希望在组件呈现后，输入字段(用户名“Bob”)中的文本被自动选中。得益于此，用户将能够轻松方便地更改该字段的值，而无需使用鼠标。

考虑如何编写这样的功能？
首先，我们的组件(包含输入字段)必须是实际渲染的(在浏览器中绘制)。然后，我们需要从代码中直接访问输入元素。最后，我们选择那里的文本(`userName`的初始值)。

## 让我们写一些代码

我们将像以前一样从类组件开始。稍后，我们将把它转换成一个使用 **React 钩子**的功能组件。

要访问呈现的组件(包括我们的输入字段)，我们必须使用`componentDidMount`生命周期方法。
此外，为了真正“捕获”这个输入字段，我们必须创建一个对它的引用，并将其存储在一个变量中。这将由`createRef`方法提供。
最后，参考已呈现的输入字段，我们将使用`select`方法来选择文本。

我在代码中标记了新元素:

```
import React from 'react';

class MyComponent extends React.Component {
    state = {
        userName: "Bob",
    }

    handleChangeUserName = (e) => {
        this.setState({ userName: e.target.value });
    };

    //create new reference
    inputRef = React.createRef();

    //use componentDidMount
    componentDidMount() {
        //using the reference to the input field we select the text in it
        this.inputRef.current.select();
    }

    render() {
        return(
            <div className="card">
                <h2>User name: <span>{this.state.userName}</span></h2>
                <input
                    //we set a reference to the input field
                    ref={this.inputRef}
                    type="text"
                    name="userName"
                    id="userName"
                    value={this.state.userName}
                    onChange={this.handleChangeUserName}
                />
            </div>
        );
    }
} 
```

类组件按预期工作。

是时候把它改写成一个功能组件了。让我们从[上一部分](https://dev.to/przemwo/gently-introduction-to-react-hooks-part-1-1a47)结束的地方开始。

抓住功能组件的最后一个例子。接下来，从类组件中复制新元素。不要忘记删除对`this`的引用，并添加缺失的`const`。

我们在这个阶段的功能组件应该是这样的:

```
import React, { useState } from 'react';

const MyComponent = () => {
    const [userName, setUserName] = useState("Bob");

    const handleUserNameChanged = (e) => {
        setUserName(e.target.value);
    }

    //const added
    const inputRef = React.createRef();

    componentDidMount() {
        //this. removed
        inputRef.current.select();
    }

    return (
        <>
            <h2>User name: {userName}</h2>
            <input
                //this. removed
                ref={inputRef}
                type="text"
                value={userName}
                onChange={handleUserNameChanged}
            />
        </>
    );
} 
```

我们的代码不起作用。`componentDidMount`有问题。但是在我们解决这个问题之前，让我们先来看看`createRef`。

在函数组件中，像在类一中一样，如果你想使用对被渲染元素的引用，你可以使用`createRef`。然而，当我们想要使用引用时，React 为我们提供了一个特殊的钩子。叫做`useRef`。让我们在例子中使用它。

```
//import useRef hook
import React, { useState, useRef } from 'react';

const MyComponent = () => {
    const [userName, setUserName] = useState("Bob");

    const handleUserNameChanged = (e) => {
        setUserName(e.target.value);
    }

    //switched to useRef
    const inputRef = useRef();

    componentDidMount() {
        inputRef.current.select();
    }

    return (
        <>
            <h2>User name: {userName}</h2>
            <input
                ref={inputRef}
                type="text"
                value={userName}
                onChange={handleUserNameChanged}
            />
        </>
    );
} 
```

在我们的例子中，使用`useRef`会产生与使用`createRef`相同的结果。然而，这两种方法互不相同。如果你对其中的区别感兴趣，我推荐你阅读[这本](https://stackoverflow.com/a/54620836)。

好吧，但是我们的例子还是坏了。

## 那`componentDidMount`呢？

函数组件中没有`componentDidMount`方法。那么，我们如何才能到达已经呈现的组件呢？我们需要使用另一个钩子:`useEffect`。

## 使用效果挂钩

`useEffect` hook 长这样:

```
useEffect(() => {
    //effect's code goes here
}); 
```

`useEffect`是一个以回调函数为参数的函数。这个回调函数内部的代码(姑且称之为 effect)将在我们组件的**次**渲染之后执行。在每一个之后意味着也在第一个之后。在我们的情况下，这就是我们所需要的。

让我们导入`useEffect`钩子并替换`componentDidMount` :

```
//import useEffect
import React, { useState, useRef, useEffect } from 'react';

const MyComponent = () => {
    const [userName, setUserName] = useState("Bob");

    const handleChangeUserName = (e) => {
        setUserName(e.target.value);
    };

    const inputRef = useRef();

    //useEffect instead of componentDidMount
    useEffect(() => {
        inputRef.current.select();
    });

    return (
        <div className="card">
            <h2>User name: <span>{userName}</span></h2>
            <input
                ref={inputRef}
                type="text"
                name="userName"
                id="userName"
                value={userName}
                onChange={handleChangeUserName}
            />
        </div>
    );
}; 
```

我们的组件工作正常。嗯，差不多了。“几乎”是因为它会在第一次呈现组件后立即选择输入字段中的文本，但每次输入新字符时都会这样做。这不是我们想要的！

正如我之前写的，传递给`useEffect`的函数在每次渲染后执行。当用户在输入字段中输入一个新字符时，`userName`的新值被保存到状态中，这又导致组件的下一次呈现。然后传递给`useEffect`的功能被再次执行，输入栏中的文本被选中。我们必须修好它！

`useEffect`可以接受一个数组作为第二个参数。这个数组的元素可以是任意数量的变量。**每次渲染**后，React 会检查这些变量中是否有一个发生了变化。如果是这样的话，效果就会显现。

```
useEffect(() => {
    //effect's code goes here
}, [/* list of dependencies */]); 
```

如果我们想让我们的效果依赖于`userName`的变化，我们可以这样写:

```
useEffect(() => {
    console.log('User name has changed!');
}, [userName]); 
```

我们可以这样理解:

1.  我们想利用效果。
2.  这种效果是将文本写入控制台。
3.  我们希望这个效果在第一次渲染后运行。
4.  在每一次渲染后，如果`userName`改变了，我们想再次运行效果。

在我们的例子中，我们希望效果(在输入字段中选择文本)运行一次(就在第一次呈现之后)，然后对组件中的进一步更改保持不敏感。无论我们的组件发生什么变化(比如用户输入一个新名字)，效果都不会再运行了。所以我们希望依赖数组为空。我们来写吧！

```
useEffect(() => {
    //effect's code goes here
}, []); //empty array of dependencies 
```

这段代码意味着效果将只运行一次(在第一次渲染之后)。

这相当于 **`componentDidMount`** 的生命周期法。

这正是我们错过的。因此，让我们修复我们的功能组件:

```
import React, { useState, useRef, useEffect } from 'react';

const MyComponent = () => {
    const [userName, setUserName] = useState("Bob");

    const handleChangeUserName = (e) => {
        setUserName(e.target.value);
    };

    const inputRef = useRef();

    useEffect(() => {
        inputRef.current.select();
    }, []); // effect has no dependencies

    return (
        <div className="card">
            <h2>User name: <span>{userName}</span></h2>
            <input
                ref={inputRef}
                type="text"
                name="userName"
                id="userName"
                value={userName}
                onChange={handleChangeUserName}
            />
        </div>
    );
}; 
```

我们拿到了。我们的组件如我们所愿地工作。我们已经将一个类组件重写为一个功能组件，其工作方式完全相同。它采用**状态**和**生命周期法**。

## 但是我仍然不明白这些快乐

我完全理解你。我自己也有一样的。反应钩子有什么大惊小怪的？既然结果和以前完全一样，为什么还要学习一种新的语法呢？

然而，如果你在**的下一部分继续和我在一起，温柔地介绍反作用钩子**，你应该会有第一个“好的，这很酷”的时刻。

*   [轻轻介绍 React Hooks。第一部分](https://dev.to/przemwo/gently-introduction-to-react-hooks-part-1-1a47)
*   [轻轻介绍 React Hooks。第二部分](https://dev.to/przemwo/gently-introduction-to-react-hooks-part-2-4lkc)

* * *

感谢阅读！如果你喜欢这个，让我知道！发表评论，给一个❤️或分享它！

随意查看我的 [Twitter](https://twitter.com/PrzemekWolnik) 账号，有更多这样的内容。
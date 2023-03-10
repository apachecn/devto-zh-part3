# constate，React 中的轻量级状态管理

> 原文：<https://dev.to/chriswcs/constate-light-weight-state-management-in-react-258c>

### 简介

现在钩子已经发布了，我相信我们将会看到模式的出现，社区将会发现钩子可以解决问题并简化代码。本文是对 [constate](https://github.com/diegohaz/constate) 的基本介绍，constate 是一个利用钩子的轻量级状态管理解决方案。请在下面随意评论你对这个话题和图书馆的看法。请保持文明讨论，谢谢！

### 简单的 API +钩子

该库的 API 非常简单。我们将使用一个名为 createContainer 的函数，然后我们将使用内置的钩子来完成其余的工作。它在很多方面都不是很固执己见，这使得它可以灵活地适应用户想要的风格和组织。下面这个简单(而且非常简单)的例子展示了它是如何工作的。

```
import React from "react"
import createContainer from "constate"

// Our custom hook that returns what will be available in our context
function useText() {
    const [text, setText] = React.useState('hello')

    const changeLang = () => {
        setText('hola')
    }

    return {
        text,
        changeLang,
    }
}

// Our container
const TextContainer = createContainer(useText)

// we now use the useContext hook to pull out the Context
function TextButton() {
    const { changeLang } = React.useContext(TextContainer.Context)
    return <button onClick={changeLang}>Change</button> }

function TextBox() {
    const { text } = React.useContext(TextContainer.Context)
    return <h1>{text}</h1> }

// the Container has a provider on it that we need to wrap our components in
function App() {
    return (
        <TextContainer.Provider>
            <TextButton />
            <TextBox />
        </TextContainer.Provider>
    )
} 
```

### 待办事项示例

是的，我列了一张待办事项清单。如果这让你难过，我道歉😩。以后我会做更多原创的例子😎。与此同时，让我们检查一下。

```
// This is the useTodos custom hook that will be passed to createContainer
import React from 'react';

const useTodos = () => {
    const [ todos, setTodos ] = React.useState([]);
    const [ val, setVal ] = React.useState('')

    const addTodo = () => {
        setTodos([ val, ...todos ]);
        setVal('');
    }

    const updateForm = (e) => {
        setVal(e.target.value);
    }

    return {
        todos, 
        val, 
        addTodo, 
        updateForm
    }
}

export default useTodos; 
```

注意上面我是如何用不同的方式来处理它的。我可以有一个包含所有状态的 useState 调用，或者如果我想要类似 Redux 的东西，我可以使用 useReducer。这显示了库提供的灵活性。

下面是一个组件，展示了如何访问状态和功能。使用析构，我可以获得这个组件需要的所有值和函数。其他组件以同样的方式访问状态。

```
import React from 'react';
import TodoContainer from './TodoContainer';

const TodoInput = () => {
    const {val, updateForm, addTodo} = React.useContext(TodoContainer.Context);
    return (
        <div>
            <input value={val} onChange={updateForm}/>
            <button onClick={addTodo}>ADD</button>
        </div>
    );
}

export default TodoInput; 
```

在这里找到剩余的代码。考虑在您的下一个项目中检查 constate 并探索 hooks。感谢您花时间阅读这篇文章！

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ ChrisWcs ](https://github.com/ChrisWcs) / [例 _of_constate](https://github.com/ChrisWcs/examples_of_constate)

### constate 和 react 挂钩的使用示例

<article class="markdown-body entry-content container-lg" itemprop="text">

## 使用 constate 进行状态管理的示例

这个项目是用 [Create React App](https://github.com/facebook/create-react-app) 引导的。

</article>

[View on GitHub](https://github.com/ChrisWcs/examples_of_constate)
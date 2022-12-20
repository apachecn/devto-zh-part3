# React 挂钩:使用 useContext 和 useReducer 提升/传递状态

> 原文：<https://dev.to/email2vimalraj/react-hooks-lift-up--pass-down-state-using-usecontext-and-usereducer-5ai0>

这篇文章发表在我的博客上。

我遇到过这样一种情况，我有许多子组件和兄弟组件试图在它们之间共享状态。之前，我用`prop`发送了一个方法，在组件之间共享更新后的状态。有一段时间，道具的数量不断增加，我讨厌这样。

然后出现了一种基于`context`的方法，将状态存储在一个全局存储中，并在全局范围内共享。但是即使有了`context` API，您也必须有一个`render props`来消费来自全局`context`的存储状态。你很快就会意识到，你的组件变成了嵌套的、不可维护的、令人难以忘怀的。

现在这篇文章谈论我们如何利用最新的 React 的`hooks`概念，用更干净的代码达到同样的目的。

让我们首先用一些子组件和兄弟组件构建示例 UI。

## 咱们 UI

前往[代码沙箱](https://codesandbox.io)进行快速实验。确保你创建了一个`React`代码沙箱。

用以下内容替换`index.js`:

```
import React from "react";
import ReactDOM from "react-dom";

function App() {
  return (
    <div className="App">
      <h1>Lift up / Pass down state</h1>

      <UserList />
      <AddGenderToUser />
      <AddAgeToUser />
    </div>
  );
}

function UserList() {
  return (
    <ul>
      <li>
        <span>Vimalraj Selvam</span>
        <button type="button">Edit</button>
      </li>

      <li>
        <span>Bhuvaneswari Vimalraj</span>
        <button type="button">Edit</button>
      </li>
    </ul>
  );
}

function AddGenderToUser({ username }) {
  return (
    <div>
      <h2>Add gender to {username}</h2>
      <button type="button">Add Age</button>
    </div>
  );
}

function AddAgeToUser({ username }) {
  return (
    <div>
      <h2>Add Age to {username}</h2>
      <button type="button">Submit</button>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

这里我有 3 个子组件作为`App`组件的父组件:`UserList`、`AddGenderToUser`和`AddAgeToUser`。

这是一个非常简单的例子。所以不要过多考虑这个应用程序的用例。

我想只在特定用户的`Edit`按钮被点击时显示`AddGenderToUser`组件，并用所选用户名更新组件的标题。

从`AddGenderToUser`组件点击`Add Age`按钮，同样的事情也发生在`AddAgeToUser`组件上。

首先，让我们在没有用户被选中时创建应用程序的初始状态。

```
const initialState = {
  username: null,
  gender: null,
  age: null
}; 
```

然后创建我们的 reducer 方法来执行不同的操作。我能想到的行动有:

*   更新用户
*   设置当前用户的性别
*   设置当前用户的年龄

让我们把它放在一个`reducer`函数中:

```
const UPDATE_USER = "UPDATE_USER";
const SET_GENDER = "SET_GENDER";
const SET_AGE = "SET_AGE";

function reducer(state, action) {
  switch (action.type) {
    case UPDATE_USER:
      return {
        username: action.username,
        gender: null,
        age: null
      };
    case SET_GENDER:
      return {
        username: state.username,
        gender: action.gender,
        age: null
      };
    case SET_AGE:
      return {
        username: state.username,
        gender: state.gender,
        age: action.age
      };
    default:
      return initialState;
  }
} 
```

我们的还原方法很简单。它从`action`参数中取值并将其设置为当前状态。

现在让我们使用 react 中的`useReducer`钩子在父`App`组件中使用这个 reducer 函数。这样我们就可以通过`context`消耗减速器的属性。

让我们在`App`组件的`return`语句之前添加下面一行。

```
const [user, dispatch] = React.useReducer(reducer, initialState); 
```

这里的`user`是当前状态，`dispatch`是我们触发减速器上定义的各种动作的方法。为此，我们必须向下传递`dispatch`方法，并且如果在`state`对象上发生任何更新，父对象/父对象的其他子对象也应该知道。

为了实现上述目标，我们必须利用 react 的`context` API 来存储我们的状态和调度。

让我们用下面一行初始化`context`。这一行应该在你的`App`函数之前(真的无所谓)。

```
const MyContext = React.createContext(null); 
```

我已经用 null 初始化了上下文。我们必须将我们的状态和调度放入上下文中。为此，让我们编辑我们的`App`组件，用`context's`提供者包装所有的子组件。更新后的`App`组件应该如下所示:

```
<MyContext.Provider value={{ user, dispatch }}>
  <UserList />
  {user.username && <AddGenderToUser />}
  {user.gender && <AddAgeToUser />}
</MyContext.Provider> 
```

很好，现在我们可以访问`user`状态和相应的`dispatch`方法了。此外，我还添加了一些基于`user`州属性`username` & `gender`的子元素的条件渲染。

让我们更新我们的`UserList`组件，在特定用户点击`Edit`按钮时触发`UPDATE_USER`动作。为此，我们必须使用 React 的`useContext`钩子从`context`中获取`dispatch`方法。

重写的`UserList`组件:

```
function UserList() {
  const { dispatch } = useContext(MyContext);
  return (
    <ul>
      <li>
        <span>Vimalraj Selvam</span>
        <button
          type="button"
          onClick={() => dispatch({ type: UPDATE_USER, username: "Vimalraj" })}
        >
          Edit
        </button>
      </li>

      {/* Removed for brevity */}
    </ul>
  );
} 
```

我们正在调度`UPDATE_USER`动作，并发送`username`来更新状态的属性。现在，当您为特定用户单击`Edit`按钮时，您可以看到`AddGenderToUser`组件出现。但是我们仍然没有在出现的组件中看到用户名。让我们解决这个问题！

```
function AddGenderToUser() {
  const { user, dispatch } = useContext(MyContext);

  return (
    <div>
      <h2>Add gender to {user.username}</h2>
      <button
        type="button"
        onClick={() => dispatch({ type: SET_GENDER, gender: "??" })}
      >
        Add Age
      </button>
    </div>
  );
} 
```

我们正在获取当前的`user`状态和`dispatch`方法。我们提取`username`属性显示在标题中，并在点击`Add Age`按钮时触发`SET_GENDER`动作。

您也可以对`AddAgeToUser`功能重复同样的操作。

代码沙盒中有完整的版本，请随意查看。

[![Edit Lift up / pass down state](img/0b3f0135583496627e3621355d8e9248.png)](https://codesandbox.io/s/pw5zlq8zj0?fontsize=14)

在代码沙箱中，我稍微更新了`App`组件，以便在年龄更新后显示详细信息。

如果这篇文章对你有帮助，请点击喜欢并分享。
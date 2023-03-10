# useContext:放弃 Redux 的好理由

> 原文：<https://dev.to/emeka/usecontext-a-good-reason-to-drop-redux-216l>

自从 react 宣布在 react 的稳定版本中支持钩子以来，我没有创建任何新的基于类的组件。我们很多人仍然使用 redux 来管理我们的状态，但事实是我们并不总是需要这种额外的复杂性。使用 react 的上下文 API，您还可以跨各种组件共享数据。

我假设你至少已经对 [react](https://reactjs.org) 、 [react-hooks](https://reactjs.org/docs/hooks-reference.html) 和 [redux](https://redux.js.org) 有一点了解。如果你对 redux 一无所知，没问题，你可以使用 context 直接跳到[。](#using-context)

假设我们有一个包含我们的登录状态和用户详细信息的身份验证数据，以及包含我们希望在应用程序的各个部分显示的文章列表的数据。如果我们正在使用 redux，我们将能够通过连接我们的组件和 redux store，使用通常称为“mapStateToProps”的概念在我们的应用程序中的各个组件之间共享这些数据。

```
import react from 'react';
import connect from 'react-redux';

function User(props) {
  const getUserArticles = (articles) => articles
    .filter(article => article.userId === props.user.id);
  return (
    <div>
      <h1>{`${props.user.name}'s Article`}</h1>
      <ul>
        {getUserArticles(props.articles)
          .map(article => <li key={article.id}>{article.title}</li>)}
      </ul>
    </div>
  );
}
const mapStateToProps = ({auth, article}) => ({
  user: auth.user,
  articles: article.articles
});

export default connect(mapStateToProps)(User); 
```

这是一个典型的例子，说明如果我们通过 redux 管理 auth 和文章状态，我们的组件会是什么样子。我们的文章和认证数据由我们的服务在后台获取，并发送到我们的 redux 状态。这种状态现在可以被任何组件访问，就像我们上面所做的一样。

## 利用上下文

我们可以通过上下文跨组件实现相同的数据共享。在 react 中创建上下文类似于:

```
const MyContext = React.createContext(defaultValue); 
```

`createContext`将默认值作为参数，仅当在消费组件的树上方没有找到匹配的提供者时使用。

让我们创建文章和身份验证上下文。

```
import React from "react";

export const authContext = React.createContext({
  loggedIn: false,
  user: {},
  updateAuth: () => {}
});

export const articleContext = React.createContext({
  articles: [],
  updateArticles: () => {}
}); 
```

每个上下文对象都带有一个提供者 React 组件，该组件允许消费组件订阅上下文更改。

```
<MyContext.Provider value={/* some value */}> 
```

任何功能组件都可以通过使用钩子
`useContext`读取上下文并订阅其变化

```
const value = useContext(MyContext) 
```

根据 react 文档，`useContext`接受一个上下文对象(从 React.createContext 返回的值)并返回该上下文的当前上下文值。
如果订阅了某个提供者，那么每当该提供者的值属性发生变化时，作为该提供者后代的所有组件都将重新呈现。

让我们看看如何利用前面定义的 authContext 和 articleContext。

```
import React, { useState } from "react";
import { authContext, articleContext } from "./contexts";
import UserComponent from "./user";

function App() {
  const [auth, setAuth] = useState({
    loggedIn: false,
    user: {},
    updateAuth: update => setAuth(auth => ({ ...auth, ...update }))
  });
  const [articles, setArticles] = useState({
    articles: [],
    updateArticles: articles => setArticles(articles)
  });

  return (
    <authContext.Provider value={auth}>
      <articleContext.Provider value={articles}>
        <UserComponent />
      </articleContext.Provider>
    </authContext.Provider>
  );
}

export default App; 
```

此时你可能有两个问题:

1.  为什么不直接将价值传递给提供商的价值主张呢？
2.  当状态挂钩已经返回一个更新方法时，为什么我们还要定义另一个更新方法？

对于第一个问题，react 文档中有一个警告。它说:因为上下文使用引用标识来确定何时重新呈现，所以当提供者的父级重新呈现时，有一些问题可能会在消费者中触发无意的呈现。例如，下面的代码将在每次提供者重新呈现时重新呈现所有使用者，因为总是为 value 创建一个新对象:

```
function App() {
  return (
    <Provider value={{something: 'something'}}>
      <Toolbar />
    </Provider>
  );
} 
```

要解决这个问题，请将值提升到父状态中。

对于第二个问题，我们需要使 update 函数真正易于使用，以便用户只需要担心他们试图更新的当前值属性，而不用覆盖或删除未更改的属性。我们的更新函数使用扩展操作符将新值与旧值合并。

当使用 redux 时，我们分派动作来更新我们的 redux 状态。典型的动作分派是这样完成的:

```
store.dispatch({type: 'update_articles', value: articles }) 
```

我们继续在我们的 reducer 中使用分派的动作，比如

```
export const articlesreducer = (state = {}, action) => {
  switch(action.type) {
    case('update_articles'):
      return { ...state, articles: action.value };
    default:
      return state;
  }
} 
```

这是我们如何更新 redux 状态的典型例子。有了上下文，我们就可以摆脱所有这些。如果你从来没有使用过 redux，你可能不需要看到这个，我道歉

现在我们要重构我们的用户组件，模拟一个基于 promise 的服务，它包含获取 auth 和 articles 数据的函数。

我们的模拟服务可能是这样的:

```
export const getAuth = () => {
  return new Promise(resolve => {
    resolve({
      loggedIn: true,
      user: {
        name: "Jon Doe",
        id: "1"
      }
    });
  });
};

export const getArticles = () => {
  return new Promise(resolve => {
    resolve([
      { id: "1", userId: "1", title: "A few good men" },
      { id: "2", userId: "1", title: "Two and a half guns" },
      { id: "3", userId: "1", title: "Hey brother" }
    ]);
  });
}; 
```

现在，我们可以将连接到 redux store 的用户组件重构为订阅上下文提供者的组件:

```
import React, { useContext, useEffect } from "react";
import { authContext, articleContext } from "./contexts";
import { getAuth, getArticles } from "./services";

function User() {
  const { articles, updateArticles } = useContext(articleContext);
  const auth = useContext(authContext);

  useEffect(() => {
    getAuth().then(data => auth.updateAuth(data));
    getArticles().then(articles => updateArticles({ articles }));
  }, [auth.updateAuth, updateArticles]);

  const getUserArticles = articles =>
    articles.filter(article => article.userId === auth.user.id);
  return (
    <div>
      <h1>{`${auth.user.name}'s Article`}</h1>
      <ul>
        {getUserArticles(articles).map(article => (
          <li key={article.id}>{article.title}</li>
        ))}
      </ul>
    </div>
  );
}

export default User; 
```

用户组件现在从文章和授权上下文中读取值，如果其中任何一个的值发生变化，则重新呈现。我们还可以从用户组件中更新上下文。

对于我们已经创建的这个小项目来说，这种形式的数据管理可能看起来有点矫枉过正，但这样做的目的只是为了看看我们如何使用 react 的上下文 API 来代替 redux。这在较大的应用程序中非常理想，在这些应用程序中，具有不同嵌套级别的各种组件需要访问 auth 和 articles 数据。

我建议你去官方的 [react 文档网站](https://reactjs.org/docs/context.html)了解更多关于上下文 API 的知识。为了便于参考，本文中的代码位于这个[代码沙箱](https://codesandbox.io/embed/245rjjjqxp)
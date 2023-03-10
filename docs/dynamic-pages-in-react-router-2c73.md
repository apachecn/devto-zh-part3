# react 路由器中的动态页面

> 原文：<https://dev.to/paramharrison/dynamic-pages-in-react-router-2c73>

> 在 [Twitter](https://twitter.com/learnwithparam) 上关注我，很高兴接受您对主题或改进的建议

在[第 1 部分](https://dev.to/paramharrison/basic-routing-in-react-using-react-router-406e)中，我们学习了如何在 react router 中创建基本的静态页面。现在我们将学习如何在 react 路由器中创建动态 URL。

我们将创建两条路线，

*   用户路由(显示个人用户所有链接的静态路由)
*   用户路由——每个用户将由他们唯一的 ID 标识，URL 将传递这个 ID，组件将显示正确的用户内容

让我们创建一个虚拟的`users`数据

```
const users = [
  {
    name: `Param`,
  },
  {
    name: `Vennila`,
  },
  {
    name: `Afrin`,
  },
]; 
```

Enter fullscreen mode Exit fullscreen mode

让我们在 App.js 文件中为所有用户和单个用户创建新的路线。

```
// App.js
...

const UsersPage = () => {
  return (
    <h3>Users Page</h3>
  );
};

const App = () => {
  return (
    <section className="App">
      <Router>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/users">Users</Link>
        <Route exact path="/" component={IndexPage} />
        <Route exact path="/users" component={UsersPage} />
        <Route exact path="/about" component={AboutPage} />
      </Router>
      <a href="/about">about with browser reload</a>
    </section>
  );
};

... 
```

Enter fullscreen mode Exit fullscreen mode

我们创建了一个到用户页面的链接，以及用户页面的路由定义及其相关组件(`UsersPage`)。

让我们在用户页面中添加每个用户的链接(`/user/1`、`/user/2`、`/user/3` )

```
// userId will be Array index + 1
const UsersPage = () => {
  return (
    <>
      <h3>Users Page</h3>
      {users.map((user, index) => (
        <h5 key={index}>
          <Link to={`/user/${index + 1}`}>{user.name}'s Page</Link>
        </h5>
      ))}
    </>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

> `<></>`这是`<React.Fragment></React.Fragment>`的简称。你可以在这里阅读更多关于[的片段](https://reactjs.org/docs/fragments.html)

现在我们有了带链接的用户页面。如果您单击该链接，将导致没有页面，因为我们没有为每个用户创建路由定义。

我们可以像这样创建每个单独的路由定义

```
<Route exact path="/user/1" component={UserPage1} />
<Route exact path="/user/2" component={UserPage2} /> 
```

Enter fullscreen mode Exit fullscreen mode

不要！😱。我只是在撒谎，我们都知道，这不会扩展到包含更多动态数据的动态页面。让我们看看如何在 react 路由器中创建动态路由定义。这很容易。

```
<Route path="/user/:userId" component={UserPage} /> 
```

Enter fullscreen mode Exit fullscreen mode

这里`:userId`是路线定义中的动态路线参数。它被传递给组件。你可以在`UserPage`组件中获得名为`userId`的道具。

让我们在示例中添加这段代码。

```
// App.js
...

const UserPage = () => {
  return (
    <h3>User Page</h3>
  );
};

const App = () => {
  return (
    <section className="App">
      <Router>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/users">Users</Link>
        <Route exact path="/" component={IndexPage} />
        <Route exact path="/users" component={UsersPage} />
        <Route exact path="/user/:userId" component={UserPage} />
        <Route exact path="/about" component={AboutPage} />
      </Router>
      <a href="/about">about with browser reload</a>
    </section>
  );
};

... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的用户页面 URL 开始工作了。但是该页面不显示用户的任何信息。让我们显示信息。

### 如何访问组件中的路线参数

React 路由器向所有组件传递两个道具

*   `match`道具
*   `location`道具

让我们通过在组件
中安慰它来检查那些道具有什么信息

```
// App.js

const UserPage = ({ match, location }) => {
  return (
    <>
      <p>
        <strong>Match Props: </strong>
        <code>{JSON.stringify(match, null, 2)}</code>
      </p>
      <p>
        <strong>Location Props: </strong>
        <code>{JSON.stringify(location, null, 2)}</code>
      </p>
    </>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看那些道具都有什么，

```
/*
  URL: /user/1
  userId: 1
*/

// Match Props
{ "path": "/user/:userId", "url": "/user/1", "isExact": true, "params": { "userId": "1" } }

// Location Props
{ "pathname": "/user/1", "search": "", "hash": "", "key": "7e6lx5" } 
```

Enter fullscreen mode Exit fullscreen mode

如果我们仔细观察内容，我们感兴趣的`userId`参数在`match.params.userId`中。

让我们使用`UserPage`组件中的参数并显示关于用户的信息。

```
...

// Getting the userId from match props and display the user from the users array
const UserPage = ({ match, location }) => {
  const { params: { userId } } = match;

  return (
    <>
      <p>
        <strong>User ID: </strong>
        {userId}
      </p>
      <p>
        <strong>User Name: </strong>
        {users[userId - 1].name}
      </p>
    </>
  );
};

... 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/p3ozwy018j](https://codesandbox.io/embed/p3ozwy018j)

```
// Object destructuring in JavaScript
const {
  params: { userId },
} = match; 
```

Enter fullscreen mode Exit fullscreen mode

> 我们在代码中使用了对象析构。你可以在这里了解更多信息

就这样了，伙计们。我们已经成功地完成了动态路由的开发，并且知道了如何访问动态路由组件中的路由参数。希望你喜欢这个系列，请继续关注更多的高级特性🤗

你可以查看这个系列的代码库[这里](https://github.com/learnwithparam/react-router-series)和这个部分的代码[这里](https://github.com/learnwithparam/react-router-series/commit/f6935df3e43e250ecdc6aa036e09f384b7548231)
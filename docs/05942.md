# 如何在 React 路由器中处理查询参数

> 原文：<https://dev.to/paramharrison/how-to-handle-query-params-in-react-router-1fmp>

> 在 [Twitter](https://twitter.com/learnwithparam) 上关注我，很高兴接受您对主题或改进的建议

在[第 3 部分](https://dev.to/paramharrison/dynamic-pages-in-react-router-2c73)中，我们学习了如何在 react 路由器中创建动态 URL。在这一部分，我们将学习如何处理 URL 中的查询参数。

让我们用查询参数
为搜索页面创建一个新路径

```
// App.js
...
const SearchPage = () => {
  return (
    <h3>Search Page</h3>
  );
}

const App = () => {
  return (
    <section className="App">
      <Router>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/users">Users</Link>
        <Link to="/search?q=react">Search</Link>
        <Route exact path="/" component={IndexPage} />
        <Route exact path="/users" component={UsersPage} />
        <Route exact path="/user/:userId" component={UserPage} />
        <Route exact path="/about" component={AboutPage} />
        <Route exact path="/search" component={SearchPage} />
      </Router>
      <a href="/about">about with browser reload</a>
    </section>
  );
};

... 
```

Enter fullscreen mode Exit fullscreen mode

这是我们创建链接、路由定义和组件的常用方式。如果仔细观察，静态路由和带查询参数的路由没有区别。

我们不需要在路由定义中定义查询参数，因为默认情况下，它将由 react 路由器处理，并将查询参数发送到组件的 props 中。

如果我们像这样定义路径，它就不会匹配路径。你可以在 codesandbox 里试试。

现在，我们需要访问传递给组件中 URL 的所有查询参数。在我们之前的[部分](https://dev.to/paramharrison/dynamic-pages-in-react-router-2c73)中，我们讨论了 react 路由器如何传递两个参数`match`和`location`。

*   对于动态路由，react 路由器将路由参数传递给`match` props
*   对于有查询参数的路线，有关查询参数的信息将通过`location` props 发送

让我们看看，它在`location`道具
中发送了什么信息

```
...

const SearchPage = ({ match, location }) => {
  return (
    <p>
      <strong>Location Props: </strong>
      {JSON.stringify(location, null, 2)}
    </p>
  );
}

... 
```

Enter fullscreen mode Exit fullscreen mode

你可以在搜索页面上看到，

```
Location  Props:  {  "pathname":  "/search",  "search":  "?q=react",  "hash":  "",  "key":  "allc40"  } 
```

Enter fullscreen mode Exit fullscreen mode

你可以清楚地看到，react 路由器在`location.search`中传递查询参数信息。如果您添加更多的查询参数，您将获得相同字符串值中的所有内容。例如，`?q=react&limit=3`

为了获得每个值，您可以使用自己的 helper 库或查询 params npm 包，这样您就可以获得作为良好的键值对对象的值。

> 一个这样的包是`yarn add qs`。但是有很多，你可以选择哪个适合你的网址

让我们显示组件中的`location.search`值。

```
const SearchPage = ({ match, location }) => {
  return (
    <p>
      <strong>Query Params: </strong>
      {
        location.search
      }
    </p>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/n7q6kw9n8m](https://codesandbox.io/embed/n7q6kw9n8m)

就这样了，朋友们，下次博文再见。请继续支持并帮助我教得更多，学得更多🤗

你可以查看这个系列的代码库[这里](https://github.com/learnwithparam/react-router-series)和这个部分的代码[这里](https://github.com/learnwithparam/react-router-series/commit/9c3e6a38528f34269158da6b24fade7a862299ec)
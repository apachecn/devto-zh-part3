# 使用 react 路由器在 React 中创建 404 页面

> 原文：<https://dev.to/paramharrison/creating-404-page-in-react-using-react-router-2em9>

在我们之前的文章中，我们已经学习了如何在 react router 中创建[动态 URL，还学习了如何在 react router](https://learnwithparam.com/blog/dynamic-pages-in-react-router/) 中处理[查询参数，并将这些值传递给页面组件。](https://learnwithparam.com/blog/how-to-handle-query-params-in-react-router/)

在这一部分，我们将学习如果没有匹配 URL 的路由，如何显示 404 页面。

你可以在这里找到代码沙盒[的最后部分和最新代码库](https://learnwithparam.com/blog/how-to-handle-query-params-in-react-router/)

运行应用程序并访问任何不存在的路线，例如`/404-not-found`

目前，您看不到任何呈现的页面组件。让我们来处理它，如果没有与路径匹配的路由，就显示一个默认页面。

这在 react 路由器中很容易实现。在 react 路由器组件中，创建一个未定义路径的路由。并确保将代码放在所有路由的底部，这样路由器将检查所有路由，如果没有找到匹配，则回退。

```
// App.js

...

const NoMatchPage = () => {
  return (
    <h3>404 - Not found</h3>
  );
};

const App = () => {
  return (
    <section className="App">
      <Router>
        ...
        <Link to="/users">Users</Link>
        <Link to="/search?q=react">Search</Link>
        ...
        <Route exact path="/about" component={AboutPage} />
        <Route exact path="/search" component={SearchPage} />
        <Route component={NoMatchPage} />
      </Router>
    </section>
  );
};

... 
```

访问任何一个随机的 URL，你都会看到一个简单的 404 页面。但是等等，我们先检查一下其他页面是否正常工作没有断。不会的！

点击`about`链接，你会看到 about 和 404 页面都被渲染了。因为第一个路由器匹配了`/about`路径的确切路由，然后它遍历到底部并加载 404 路由，因为该路由没有定义路径。

React 路由器提供了一个名为`Switch`组件，一旦一条路由被解析，该组件就会中断，且不会加载其下的任何其他路由组件。

这很容易实现。将所有的路线包含在`Switch`组件中。让我们这样做，使 404 页的工作没有错误。

```
// App.js

...
import { Link, BrowserRouter as Router, Route, Switch } from 'react-router-dom';
...

const App = () => {
  return (
    <section className="App">
      <Router>
        ...
        <Switch>
          <Route exact path="/" component={IndexPage} />
          ...
          <Route exact path="/search" component={SearchPage} />
          <Route component={NoMatchPage} />
        </Switch>
      </Router>
    </section>
  );
};

... 
```

这个`Switch`组件将检查匹配的路线，如果一条路线已经匹配，它中断检查下一条路线。通过这种方式，我们将避免为所有页面渲染 404 route。

[https://codesandbox.io/embed/m4n21l3m38](https://codesandbox.io/embed/m4n21l3m38)

就是这样，伙计们，希望你学会了使用 react 路由器为你的 react 应用程序创建 404 页面的技巧🤗

你可以查看这个系列的代码库[这里](https://github.com/learnwithparam/react-router-series)和这个部分的代码[这里](https://github.com/learnwithparam/react-router-series/commit/b3d80a2e92e1bccdcfba219d086e782b5f762a4c)

### 保持联系！

如果你喜欢这篇文章，你可以在 [Twitter](https://twitter.com/learnwithparam) 上找到我，了解更新、公告和新闻。🐤
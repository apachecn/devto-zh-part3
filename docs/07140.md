# React 中使用 React 路由器的基本路由

> 原文：<https://dev.to/paramharrison/basic-routing-in-react-using-react-router-406e>

> 在 [Twitter](https://twitter.com/learnwithparam) 上关注我，很高兴接受您对主题或改进的建议

React 是广泛用于客户端 web 应用程序的库。在任何 web 应用程序中，都会有多个页面。正确路由 URL 并根据路由参数加载不同的页面是一个普遍要求。

有一个很棒的 npm 包，它将所有的复杂性用于 React 中的路由目的。`react-router-dom`是广泛使用的 react 库之一。

## 基本路由

让我们创建两个简单的页面

*   首页(`/`)
*   关于页面(`/about`)

使用`create-react-app` CLI 创建一个简单的 react 应用。用 npx - `npx create-react-app my-react-app`
很容易

```
// App.js
import React from 'react';

const App = () => {
  return (
    <section className="App">
      <h1>React routing Example</h1>
    </section>
  );
};

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建两个页面。简单地说，两个功能反应组分。

```
// App.js
...

const IndexPage = () => {
  return (
    <h3>Home Page</h3>
  );
};

const AboutPage = () => {
  return (
    <h3>About Page</h3>
  );
};

... 
```

Enter fullscreen mode Exit fullscreen mode

在深入研究 react 路由器代码之前，首先让我们了解一下，在 react 应用程序中路由一个页面需要什么。

*   将有页面之间的导航链接。
*   定义到页面的路径。它定义了 URL 路径和为 URL 加载的组件。
*   定义一个路由器，它将检查所请求的 URL 是否存在于定义的路由中。

让我们使用 react 路由器的`Link`和`Route`组件创建链接和路由。首先安装包`yarn add react-router-dom`。

```
// App.js
...
import { Link, Router as BrowserRouter, Route } from 'react-router-dom';
...

const App = () => {
  return (
    <section className="App">
      <Router>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Route path="/" component={IndexPage} />
        <Route path="/about" component={AboutPage} />
      </Router>
    </section>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们分别浏览每一行

```
import { Link, Router as BrowserRouter, Route } from 'react-router-dom'; 
```

Enter fullscreen mode Exit fullscreen mode

这里我们导入三个组件，

*   `Link`组件将创建页面的 HTML 链接。
*   `Route`组件将定义路线。
*   组件将处理路由的逻辑。当用户点击链接时，它检查该链接是否存在于路线定义中。如果存在，路由器将在浏览器中更改 URL，route 将呈现正确的组件。

> 浏览器路由器是路由器的一种，也是应用最广泛的路由器。它使用组件下的 HTML5 推送状态来路由您的页面。在本系列的后面，我们将更详细地讨论不同类型的路由器。

```
// Link with URL
<Router>
  <Link to="/">Home</Link>
  <Link to="/about">About</Link>
</Router> 
```

Enter fullscreen mode Exit fullscreen mode

`Router`应该是包围`Link`和`Route`的父组件。以便它能够处理路由。如果我们将链接或路线放在外面，它将不起作用。它将抛出一个错误。

`Link`接受`to` props，它定义了要链接的 URL。

#### 为什么我们需要链接组件，为什么不需要一个带有 href 的 HTML 锚标签？

*   HTML `a`标签将创建一个服务器端链接。因此，每次用户点击路由时，它不会检查路由器或路由。相反，它只是将浏览器中的页面重定向到该路由。
*   而 Link 检查路由器，路由器检查路由并加载组件，无需在浏览器中重新加载页面。这就是为什么它被称为客户端路由。当点击链接组件时，它不会从服务器加载页面。

```
// Route with definition
<Route path="/" component={IndexPage} /> 
```

Enter fullscreen mode Exit fullscreen mode

这里`Route`有路径和组件道具。`component`当用户来到这条路线时，props 帮助渲染组件。`path` props 定义了用户访问页面时要匹配的 url 路径。

如果你继续下去，检查我们的路线是否工作，它会工作。但是它有一个小故障。

如果您单击 about 链接，它将在页面中呈现`IndexPage`和`AboutPage`组件。为什么🤔

因为为 about 定义的路径是`/about`。在这里，路由器从上到下遍历路由定义。首先检查路径为`/`的路由，about URL 有`/`，所以它首先呈现 IndexPage 组件。然后它检查下一条路线`/about`，也是匹配的，所以它呈现 AboutPage 组件。

#### 如何匹配精确路线？

很简单，问题本身就有答案😎。在路线中使用`exact`道具。

```
...

const App = () => {
  return (
    <section className="App">
      <Router>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Route exact path="/" component={IndexPage} />
        <Route exact path="/about" component={AboutPage} />
      </Router>
    </section>
  );
};

... 
```

Enter fullscreen mode Exit fullscreen mode

只有当整条路径匹配时，prop 才会帮助匹配路径，否则它不会渲染组件。

现在，组件将呈现良好，链接将正常工作。

[https://codesandbox.io/embed/9j8l8325qy](https://codesandbox.io/embed/9j8l8325qy)

各位，这就是我们的全部内容，您已经完成了 React 路由器系列的第 1 部分。希望你喜欢下一个大的 react 应用程序，并从中学习到一些东西🤗

你可以查看这个系列的代码库[这里](https://github.com/learnwithparam/react-router-series)和这个部分的代码[这里](https://github.com/learnwithparam/react-router-series/commit/3db2531748a6f314f108c4b1024118c2d89e41a3)

> 注:本文最初是为[我的博客](https://learnwithparam.com/blog/basic-routing-in-react-router/)写的。我在这里为神奇的开发者社区重新发布它。
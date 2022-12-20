# react 路由器中不同类型的路由器

> 原文：<https://dev.to/paramharrison/different-types-of-routers-in-react-router-1j6k>

> 在 [Twitter](https://twitter.com/learnwithparam) 上关注我，很高兴接受您对主题或改进的建议

在本系列的第 1 部分中，我们讨论了基本路由。我们使用`BrowserRouter`作为我们的路由器。

react 路由器中几乎没有更多不同类型的路由器。在这里，我们将看到何时使用这些路由器。

## 记忆路由器

```
// https://example.com (same url for all routes)
import { MemoryRouter as Router } from 'react-router-dom'; 
```

Enter fullscreen mode Exit fullscreen mode

*   路由器不会改变浏览器中的 URL，而是将 URL 的改变保存在内存中
*   这是非常有用的测试和非浏览器环境✅
*   但是在浏览器中，它没有历史。因此，您不能使用浏览器历史❌后退或前进

[https://codesandbox.io/embed/wnq10opj68](https://codesandbox.io/embed/wnq10opj68)

## 哈希表

```
/*
  Hashed routes, you can go in history.
  https://example.com/#/
  https://example.com/#/about
*/
import { HashRouter as Router } from 'react-router-dom'; 
```

Enter fullscreen mode Exit fullscreen mode

*   使用客户端哈希路由的路由器。
*   每当有新的路由呈现时，它就用散列路由更新浏览器 URL。(例如，`/#/about`)
*   服务器不会处理 URL 的散列部分，服务器将总是为每个请求发送`index.html`,并忽略散列值。哈希值将由 react 路由器处理。
*   它用于支持通常不支持 HTML 推送状态 API ✅的传统浏览器
*   它不需要在服务器中进行任何配置来处理✅路由
*   创建 react 路由器包的团队不建议使用此路线。仅当您需要支持传统浏览器或没有服务器逻辑来处理客户端路由❌时，才使用它

用他们自己的话说，

> 由于这种技术只是为了支持传统的浏览器，我们鼓励你配置你的服务器来使用`BrowserHistory`。

[https://codesandbox.io/embed/yq6onnlj4v](https://codesandbox.io/embed/yq6onnlj4v)

## 浏览器路由器

```
/*
  You can go in history
  https://example.com/
  https://example.com/about
*/
import { BrowserRouter as Router } from 'react-router-dom'; 
```

Enter fullscreen mode Exit fullscreen mode

*   广泛流行的路由器和使用 HTML5 pushState API 的现代浏览器的路由器。(即`pushState`、`replaceState`和`popState` API)。
*   它在浏览器中路由为普通的 URL，你不能通过 URL 区分它是服务器呈现的页面还是客户端呈现的页面。
*   它假设，你的服务器处理所有的请求 URL(例如，`/`，`/about`)并指向根`index.html`。从那里，BrowserRouter 负责路由相关页面。
*   它接受`forceRefresh` props 来支持不支持 HTML5 推状态 API ✅的传统浏览器

[https://codesandbox.io/embed/9j8l8325qy](https://codesandbox.io/embed/9j8l8325qy)

希望这些例子和帖子有助于理解 react 路由器中不同类型路由的基础知识。接下来，我们将继续学习 react 路由器的更多高级功能🤗

你可以在这里查看这个系列的代码库

> 注:本文最初是为[我的博客](https://learnwithparam.com/blog/different-types-of-router-in-react-router/)写的。我在这里为神奇的开发者社区重新发布它。
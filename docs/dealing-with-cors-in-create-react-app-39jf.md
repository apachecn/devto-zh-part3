# 在 Create React 应用程序中处理 CORS

> 原文：<https://dev.to/progresstelerik/dealing-with-cors-in-create-react-app-39jf>

如果你曾经构建过一个必须从不同域请求数据的 web 应用程序，你可能不得不考虑浏览器的[同源策略](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)和 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 。

在本文中，我们将学习如何使用 [Create React App](https://facebook.github.io/create-react-app/) 的代理功能来解决 CORS 问题。

## 问题

如果我们的应用程序托管在某个域(例如`domain1.com`)下，并且它试图向位于不同域(例如`domain2.com`)下的 API 发出请求，那么浏览器的同源策略就会启动并阻止该请求。

CORS 是一个允许`domain2.com`告诉浏览器`domain1.com`可以通过发送特定的 HTTP 头向它发出请求的特性。

然而，CORS 可能很难得到正确的结果，所以有时人们通过在生产中的同一个域中服务他们的前端和后端来完全避免它。

Create React App 允许我们在开发中复制这种设置，这样我们也不必在那里与 CORS 打交道。为此，它提供了两种选择:一种非常简单，但不太灵活，另一种需要更多的工作，但非常灵活。

## 自动代理

我们可以告诉 Create React App 拦截对未知路由的请求，并使用`package.json`中的`proxy`选项将它们发送到不同的域。它看起来像这样:

```
{
  "name": "flickr-client",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "react": "^16.8.6",
    "react-dom": "^16.8.6",
    "react-scripts": "^2.1.8"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "proxy": "http://localhost:4000"
} 
```

当我们启动我们的应用程序时，它将在`http://localhost:3000`下提供。如果我们请求根路径`/`，那么 Create React App 将为我们的应用程序响应相应的 HTML。但是如果我们像`/api`一样请求不同的路径，Create React App 会透明地将其转发给`http://localhost:4000/api`。

如果我们在您的浏览器开发工具中查看网络请求，我们会看到请求是向`http://localhost:3000/api`发出的，但实际上是由`http://localhost:4000/api`在浏览器不知情的情况下提供的。

没有比这更简单的了！

### 手动代理

如果我们需要更多地控制这些跨域请求是如何发出的，我们还有另一个选择，那就是创建一个类似于
的文件`src/setupProxy.js`

```
module.exports = function(app) {
  // ...
}; 
```

该函数接收`app`，一个 [Express](https://expressjs.com/) 应用程序的实例，所以我们可以对它做任何我们想做的事情。

例如，我们可以使用像 [`http-proxy-middleware`](https://github.com/chimurai/http-proxy-middleware) 这样的中间件来代理请求，就像我们使用`proxy`选项:
一样

```
const proxy = require("http-proxy-middleware");

module.exports = app => {
  app.use(
    "/api",
    proxy({
      target: "http://localhost:4000",
      changeOrigin: true
    })
  );
}; 
```

但是我们可以更进一步，使用`http-proxy-middleware`的选项如`pathRewrite`来改变请求的路径:

```
const proxy = require("http-proxy-middleware");

module.exports = app => {
  app.use(
    "/api",
    proxy({
      target: "http://localhost:4000",
      changeOrigin: true,
      pathRewrite: {
        "^/api": "/api/v1"
      }
    })
  );
}; 
```

利用这种配置，对`http://localhost:3000/api/foo`的请求将被转发到`http://localhost:4000/api/v1/foo`。

我们还可以添加一个类似 [`morgan`](https://github.com/expressjs/morgan) 的日志记录器，同时我们还可以添加:

```
const proxy = require("http-proxy-middleware");
const morgan = require("morgan");

module.exports = app => {
  app.use(
    "/api",
    proxy({
      target: "http://localhost:4000",
      changeOrigin: true,
      pathRewrite: {
        "^/api": "/api/v1"
      }
    })
  );

  app.use(morgan('combined'));
}; 
```

因此，现在每次向我们的代理发出请求时，它都会被记录到控制台。

可能性确实是无穷无尽的。

## 结论

如果您的 web 应用程序需要从不同的域请求数据，并且您希望您的开发环境模拟一个生产配置，其中前端和后端由同一个域提供服务，请确保查看 Create React App 的`proxy`和`src/setupProxy.js`选项。它们将使你的应用程序开发更加容易！

### 进一步阅读

想了解有关使用 Create React App 开发 React 应用程序的更多信息吗？查看下面的帖子，不要忘记访问我们的 [All Things React 帖子](https://www.telerik.com/blogs/all-things-react),获取关于所有 React 的广泛信息和指示。

*   [您好，创建 React App 2.0！](https://dev.to/progresstelerik/hello-create-react-app-20-316-temp-slug-3014493)
*   [关于 Create React App 我不知道的 5 件事](https://dev.to/progresstelerik/5-things-i-didnt-know-about-create-react-app-49nj)
*   [关于 Create React App 你不知道的 10 件事](https://dev.to/progresstelerik/10-more-things-you-didnt-know-about-create-react-app-3dj8)
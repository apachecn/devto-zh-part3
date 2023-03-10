# 建立一个没有 Redux 的 React SPA。

> 原文：<https://dev.to/kelp404/build-a-react-spa-without-redux-4b6g>

## 我们需要两个元素在客户端渲染一个页面。

*   **模板**。反应组件。
*   **数据**。我们从后端获取数据。

客户端应用程序从后端获取数据，然后将其呈现在浏览器 DOM 上。这确实是一项简单的工作。但是我们在项目中所做的。我们用[通量](https://github.com/facebook/flux)， [redux](https://github.com/reduxjs/redux) ， [redux-thunk](https://github.com/reduxjs/redux-thunk) ， [redux-saga](https://github.com/redux-saga/redux-saga) ...

我用过 redux，但是我觉得 redux 构建动作和 reducers 的时间太长了。我们只想要一个客户端路由器。我们用组件定义了路由页面以及如何获取数据。

我造[水豚路由器](https://github.com/kelp404/capybara-router)。它是一个没有流量和冗余的反应式路由器。
它提供了一种简单的方法来构建 SPA 的前端应用。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ kelp404 ](https://github.com/kelp404) / [水豚路由器](https://github.com/kelp404/capybara-router)

### 无流量和冗余的异步反应路由器。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 水豚路由器

[![npm version](img/3581004e1fbba0b8d789305c474e030c.png)](https://www.npmjs.com/package/capybara-router)[![Coverage Status](img/73ea007dc7c05bc4a50e6538d443da65.png)](https://coveralls.io/github/kelp404/capybara-router)[![Actions Status](img/8a2a7995e8c5c7e71140745af9d5cfd0.png)](https://github.com/kelp404/capybara-router/actions)

这是一个没有流量和冗余的反应式路由器。我们只是想要一个简单的方法来构建一个单页面应用程序。
定义包含组件的规则，以及如何获取路由器中每个页面的数据。

[![](img/be921fa504ae8710f08b16ba03cb757e.png)](https://raw.githubusercontent.com/kelp404/capybara-router/master/_capybara.jpg)

## 装置

```
npm install capybara-router --save
```

## 现场演示

[https://kelp404.github.io/capybara-router/](https://kelp404.github.io/capybara-router/)

## 例子

[/例](https://raw.githubusercontent.com/kelp404/capybara-router/master//example)

```
const React = require('react');
const ReactDOM = require('react-dom');
const {Router, RouterView} = require('capybara-router');
const history = require('history');
const axios = require('axios');
const ErrorPage = props => {
  return <h2 className="text-center">{`${props.error}`}</h2>;
};
const Home = props => {
  console.log(props.data);
  return <h2>Home</h2>;
};
```

…</article>

[View on GitHub](https://github.com/kelp404/capybara-router)

### 水豚路由器 SPA 现场演示。

*   **https://kelp404.github.io/capybara-router/**

### 用水豚路由器构建 SPA 的简单示例代码。

```
const React = require('react');
const ReactDOM = require('react-dom');
const {Router, RouterView} = require('capybara-router');
const history = require('history');
const axios = require('axios');

const ErrorPage = props => {
  return <h2 className="text-center">{`${this.props.error}`}</h2>; };
const Home = props => {
  return <h2>Home</h2>; };

const router = new Router({
  history: history.createBrowserHistory(),
  routes: [
    {
      name: 'web',
      uri: '/',
      onEnter: props => {
        document.title = 'Home';
      },
      resolve: {
        data: params => {
          return axios({
            method: 'get',
            url: `/data/${params.id}.json`
          }).then((response) => {
            return response.data;
          });
        }
      },
      component: Home
    }
  ],
  errorComponent: ErrorPage
});
router.start();

const element = (
  <RouterView>
    <p className="text-center text-muted h3" style={padding: '20px 0'}>
      <i className="fa fa-spinner fa-pulse fa-fw"></i> Loading...
    </p>
  </RouterView> );
ReactDOM.render(
  element,
  document.getElementById('root')
); 
```

[更多细节示例](https://github.com/kelp404/capybara-router/tree/master/example)
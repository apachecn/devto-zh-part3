# 代码智能，不难

> 原文：<https://dev.to/solkimicreb/code-smart-not-hard-bb1>

*为开发大型生产应用的开发人员提供的一系列想法。*

### 平均 App 解剖

为了获得尽可能多的观众，我将在演示中使用一个相当常见的设置。我们的普通应用...

*   有一个静态的登录页面与一些营销摊位。
*   有一些公共页面，至少有一个登录和注册。
*   有一些私人页面。
*   使用 JWT 令牌进行身份验证。
*   是用 React 用 redux，react-router 和 axios 写的。
*   是用 create-react-app 引导的。

我在一家咨询公司工作，这是最常见的情况。希望你也能把下面的想法应用到你的首选堆栈中。

### 提示 1:拥有坚实的 API 层

api 应该处理所有与网络相关的事情。

*   避免重复的 URL 和标题，使用一个基础 API 实例来代替。

*   在这里处理认证。确保将 auth 令牌添加到`localStorage`和基本 API 实例中。

*   将 API 拦截器用于一般的回退行为——比如全局加载指示器和错误通知。

```
import axios from 'axios'
import store from '../store'
import { startLoading, stopLoading, notify } from '../actions'

const JWT_TOKEN = 'JWT_TOKEN'

// have a base api instance to avoid repeating common config - like the base URL
// https://github.com/axios/axios#custom-instance-defaults
const api = axios.create({
  baseURL: process.env.REACT_APP_API_URL,
  timeout: process.env.REACT_APP_API_TIMEOUT
})

// add the Auth header to the base API instance once here to avoid repeated code
if (localStorage.getItem(JWT_TOKEN)) {
  const token = localStorage.getItem(JWT_TOKEN)
  api.defaults.headers.Authorization = `Bearer ${token}`
}

// keep networking logic - like handling headers and tokens - in the network layer
export function login (token) {
  api.defaults.headers.Authorization = `Bearer ${token}`
  localStorage.setItem(JWT_TOKEN, token)
}

export function logout () {
  delete api.defaults.headers.Authorization
  localStorage.removeItem(JWT_TOKEN)
}

// handle generic events - like loading and 500 type errors - in API interceptors
api.interceptors.request.use(config => {
  // display a single subtle loader on the top of the page when there is networking in progress
  // avoid multiple loaders, use placeholders or consistent updates instead
  store.dispatch(startLoading())
  return config
})

api.interceptors.response.use(
  resp => {
    store.dispatch(stopLoading())
    return resp
  },
  err => {
    store.dispatch(stopLoading())
    // if you have no specific plan B for errors, let them be handled here with a notification
    const { data, status } = err.response
    if (500 < status) {
      const message = data.message || 'Ooops, something bad happened.'
      store.dispatch(notify({ message, color: 'danger' }))
    }
    throw err
  }
)

export default api 
```

### 技巧 2:保持状态简单

因为加载和一般错误处理已经包含在 API 中，所以您不需要使用[成熟的异步动作](https://redux.js.org/advanced/async-actions)。在大多数情况下，它足以覆盖成功事件。

*action.js*

```
import articlesApi from '../api/articles'

const LIST_ARTICLES = 'LIST_ARTICLES'

export function listArticles () {
  return async dispatch => {
    // no need to handle LIST_ARTICLES_INIT and LIST_ARTICLES_ERROR here
    const articles = await articlesApi.list()
    dispatch({ type: LIST_ARTICLES, articles })
  }
} 
```

*reducer.js*

```
import { LIST_ARTICLES } from '../actions/articles'

export function articles (state = [], { type, articles }) {
  switch (type) {
    case LIST_ARTICLES:
      return articles
    default:
      return state
  }
} 
```

当你有一个具体的 b 计划时，你应该只处理初始化和错误事件。

### 提示 3:保持路线简单

实现一个正确的`ProtectedRoute`组件是很棘手的。请为公共页面和受保护页面保留两个独立的路由器树。登录和注销事件将自动在树之间切换，并在必要时重定向到正确的页面。

```
import React from 'react'
import { Switch, Route, Redirect } from 'react-router-dom'

// isLoggedIn is coming from the redux store
export default App ({ isLoggedIn }) {
  // render the private routes when the user is logged in
  if (isLoggedIn) {
    return (
      <Switch>
        <Route exact path="/home" component={HomePage} />
        <Route exact path="/article/:id" component={ArticlePage} />
        <Route exact path="/error" component={ErrorPage} />
        <Redirect exact from="/" to="/home" />
        <Route component={NotFoundPage} />
      </Switch>
    )
  }

  // render the public router when the user is not logged in
  return (
    <Switch>
      <Route exact path="/login" component={LoginPage} />
      <Route exact path="/register" component={RegisterPage} />
      <Redirect to="/login" />
    </Switch>
  )
} 
```

上面的图案有一个乖巧的 UX。它没有在登录和注销时添加历史条目，而这正是用户所期望的。

### 提示 4:正确初始化应用程序

在知道用户是登录还是注销之前，不要呈现任何内容。大胆猜测可能会导致公共/私人页面在重定向到正确页面之前短暂闪烁。

```
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'
import store from './store'

// do not render the app until we know if there is a logged in user or not
store.dispatch(getMe()).then(renderApp)

function renderApp () {
  ReactDOM.render(<App />, document.getElementById('root'))
} 
```

`getMe()`应该调用`/me`端点，该端点返回登录用户或 401(未授权)错误代码。在 localStorage 中检查 JWT 令牌是不够的，令牌可能已过期，这可能导致用户的无限重定向循环。

```
export function getMe (data) {
  return async dispatch => {
    try {
      const user = await userApi.getMe(data)
      dispatch({ type: LOGIN, user })
    } catch (err) {
      userApi.logout()
    }
  }
} 
```

### 提示 5:使用登陆页面

回头客会对你的产品感兴趣，并且在他们的浏览器中有一个缓存的应用。新人不会，判断很快。

服务器端渲染你的整个应用程序可以给人一个很好的第一印象，但这是最困难的技术之一。先不要跳上火车。大多数时候，你可以依靠一个简单的启发式方法:新来者很可能会从你的登陆页面开始。

保持你的登陆页面简单、静态，并与你的应用程序分开。然后使用[预加载](https://developer.mozilla.org/en-US/docs/Web/HTML/Preloading_content)或 [HTTP/2 推送](https://www.smashingmagazine.com/2017/04/guide-http2-server-push/)在用户阅读登陆页面时加载你的主应用。两者之间的选择取决于具体的用例:如果您有一个大的包，那么选择预取；如果有多个动态命名的小块，那么选择 HTTP/2 推送。

* * *

我希望我能教一些新把戏！如果你到目前为止，请分享这篇文章。如果这一个得到足够的爱，我可能会创建第二个关于创建可重用组件。

*谢谢！*
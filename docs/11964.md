# 使用 Next.js 进行用户身份验证

> 原文：<https://dev.to/jolvera/user-authentication-with-nextjs-4023>

**注:**我在 [API 路线](https://nextjs.org/docs/api-routes/dynamic-api-routes)发布之前写了这篇文章。我需要更新帖子以使用最新的 Next.js 功能。同时，你应该阅读[使用 Auth0](https://auth0.com/blog/ultimate-guide-nextjs-authentication-auth0/) 进行 Next.js 认证的终极指南，这是一个很好的指南，描述了你可以在 Next.js 中使用的所有认证模式。这篇文章只关注一种方法，并向你解释如何构建它。我认为保留这两个指南是有价值的，所以我会努力保持更新。

> [之前发表在我的博客上](https://jolvera.dev/posts/user-authentication-with-nextjs)

使用 Next.js 进行用户身份验证是社区请求最多的例子之一。GitHub 的这一期有超过 300 个赞和数百条带有建议和提议的评论。

该问题要求机构群体提供一个具有特定要求的示例:

*   跨页面可重用的身份验证助手
*   选项卡之间的会话同步
*   托管在`now.sh`上的简单无密码电子邮件后端

这个例子的主要目的是为新来者提供一个起点。

随着 [Next.js 8](https://nextjs.org/blog/next-8/) 的发布，一个实例最终被接受并合并到[实例库中](https://github.com/zeit/next.js/tree/canary/examples/with-cookie-auth)。在这篇文章中，我们将从头开始创建这个例子。

*你可以在 [Next.js 范例库](https://github.com/zeit/next.js/tree/canary/examples/with-cookie-auth)中找到代码，或者玩玩现在 2 中部署的[工作演示。](https://nextjs-with-cookie-auth.now.sh)*

*   [项目设置](#project-setup)
*   [后端](#backend)
*   [前端](#frontend)
    *   [登录页面和认证](#login-page-and-authentication)
    *   [个人资料页面和授权](#profile-page-and-authorization)
    *   [授权助手功能](#authorization-helper-function)
    *   [授权高阶组件](#authorization-high-order-component)
    *   [具有授权请求的页面组件](#page-component-with-authorized-requests)
    *   [注销和会话同步](#logout-and-session-synchronization)
*   [部署到现在 2](#deploy-to-now-2)
*   [地方发展](#local-development)
*   [结论](#conclusion)

## [项目设置](#project-setup)

我们将用推荐的文件夹结构和一个`now.json`文件将项目设置为一个 [monorepo](https://zeit.co/examples/monorepo/) ，这样我们现在就可以部署它了。

```
$ mkdir project
$ cd project
$ mkdir www api
$ touch now.json 
```

## [后端](#backend)

我们将使用`micro`来处理传入请求，使用`isomoprhic-unfetch`来发出 API 请求。

```
$ cd api
$ npm install isomorphic-unfetch micro --save 
```

为了简化我们的例子，我们将使用 GitHub API 作为无密码后端。我们的后端将调用`/users/:username`端点并检索用户的`id`，然后从现在开始，这个`id`将成为我们的令牌。

在我们的应用程序中，我们将创建两个函数作为端点:`login.js`返回令牌，而`profile.js`从给定的令牌返回用户信息。

```
// api/login.js

const { json, send, createError, run } = require('micro')
const fetch = require('isomorphic-unfetch')

const login = async (req, res) => {
  const { username } = await json(req)
  const url = `https://api.github.com/users/${username}`

  try {
    const response = await fetch(url)
    if (response.ok) {
      const { id } = await response.json()
      send(res, 200, { token: id })
    } else {
      send(res, response.status, response.statusText)
    }
  } catch (error) {
    throw createError(error.statusCode, error.statusText)
  }
}

module.exports = (req, res) => run(req, res, login); 
```

```
// api/profile.js

const { send, createError, run } = require('micro')
const fetch = require('isomorphic-unfetch')

const profile = async (req, res) => {
  if (!('authorization' in req.headers)) {
    throw createError(401, 'Authorization header missing')
  }

  const auth = await req.headers.authorization
  const { token } = JSON.parse(auth)
  const url = `https://api.github.com/user/${token}`

  try {
    const response = await fetch(url)

    if (response.ok) {
      const js = await response.json()
      // Need camelcase in the frontend
      const data = Object.assign({}, { avatarUrl: js.avatar_url }, js)
      send(res, 200, { data })
    } else {
      send(res, response.status, response.statusText)
    }
  } catch (error) {
    throw createError(error.statusCode, error.statusText)
  }
}

module.exports = (req, res) => run(req, res, profile) 
```

这样，我们就拥有了在后端处理简化的身份验证/授权策略所需的一切。

## [前端](#frontend)

现在，在我们的`www/`文件夹中，我们需要安装我们的 Next.js 应用程序和依赖项，

```
$ cd www/
$ npm create-next-app .
$ npm install
$ npm install isomorphic-unfetch next-cookies js-cookie --save 
```

创建我们的页面，

```
$ touch pages/index.js
$ touch pages/profile.js 
```

将包含我们的身份验证助手的文件，

```
$ mkdir utils
$ touch utils/auth.js 
```

以及包含本地开发定制服务器的文件。我们稍后需要在本地复制 monorepo 设置。

```
$ touch server.js 
```

此时，我们的`www/`文件夹结构应该是这样的。

```
.
├── components
│   ├── header.js
│   └── layout.js
├── package-lock.json
├── package.json
├── pages
│   ├── index.js
│   ├── login.js
│   └── profile.js
├── server.js
└── utils
    └── auth.js 
```

我们的前端结构已经准备好了。

### [登录页面和认证](#login-page-and-authentication)

登录页面将包含的形式，将验证我们的用户。该表单将向`/api/login.js`端点发送一个带有用户名的 POST 请求，如果用户名存在，后端将返回一个令牌。

对于这个例子，只要我们在前端保存这个令牌，我们就可以说用户有一个活动的会话。

```
// www/pages/login.js

import { Component } from 'react'
import fetch from 'isomorphic-unfetch'
import Layout from '../components/layout'
import { login } from '../utils/auth'

class Login extends Component {
  static getInitialProps ({ req }) {
    const protocol = process.env.NODE_ENV === 'production' ? 'https' : 'http'

    const apiUrl = process.browser
      ? `${protocol}://${window.location.host}/api/login.js`
      : `${protocol}://${req.headers.host}/api/login.js`

    return { apiUrl }
  }

  constructor (props) {
    super(props)

    this.state = { username: '', error: '' }
    this.handleChange = this.handleChange.bind(this)
    this.handleSubmit = this.handleSubmit.bind(this)
  }

  handleChange (event) {
    this.setState({ username: event.target.value })
  }

  async handleSubmit (event) {
    event.preventDefault()
    const username = this.state.username
    const url = this.props.apiUrl

    try {
      const response = await fetch(url, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ username })
      })
      if (response.ok) {
        const { token } = await response.json()
        login({ token })
      } else {
        console.log('Login failed.')
        // https://github.com/developit/unfetch#caveats
        let error = new Error(response.statusText)
        error.response = response
        return Promise.reject(error)
      }
    } catch (error) {
      console.error(
        'You have an error in your code or there are Network issues.',
        error
      )
      throw new Error(error)
    }
  }

  render () {
    return (
      <Layout>
        <div className='login'>
          <form onSubmit={this.handleSubmit}>
            <label htmlFor='username'>GitHub username</label>

            <input
              type='text'
              id='username'
              name='username'
              value={this.state.username}
              onChange={this.handleChange}
            />

            <button type='submit'>Login</button>

            <p className={`error ${this.state.error && 'show'}`}>
              {this.state.error && `Error: ${this.state.error}`}
            </p>
          </form>
        </div>
        <style jsx>{`
          .login {
            max-width: 340px;
            margin: 0 auto;
            padding: 1rem;
            border: 1px solid #ccc;
            border-radius: 4px;
          }
          form {
            display: flex;
            flex-flow: column;
          }
          label {
            font-weight: 600;
          }
          input {
            padding: 8px;
            margin: 0.3rem 0 1rem;
            border: 1px solid #ccc;
            border-radius: 4px;
          }
          .error {
            margin: 0.5rem 0 0;
            display: none;
            color: brown;
          }
          .error.show {
            display: block;
          }
        `}</style>
      </Layout>
    )
  }
}

export default Login 
```

我们的`getInitialProps()`将根据我们所处的环境并通过检查我们是在浏览器还是服务器中来生成一个 URL。

第一行将根据环境将协议设置为`https`或`https`。

```
...
const protocol = process.env.NODE_ENV === 'production' ? 'https' : 'http'
... 
```

接下来，根据我们是在浏览器中还是在服务器中，我们得到我们的`host`。这样，即使我们现在使用动态生成的 URL，或者使用`http://localhost:3000`进行本地开发，我们也能获得正确的 URL。

```
...
const apiUrl = process.browser
  ? `${protocol}://${window.location.host}/${endpoint}`
  : `${protocol}://${req.headers.host}/${endpoint}`;
... 
```

其他一切都很标准，表单在提交时发出 POST 请求。我们还使用本地状态来处理简单的验证错误消息。

如果我们的请求成功，我们将通过保存带有从 API 获得的令牌的 cookie 来登录我们的用户，并将用户重定向到我们的配置文件页面。

```
...
cookie.set("token", token, { expires: 1 });
Router.push("/profile")
... 
```

### [简介页面和授权](#profile-page-and-authorization)

对于客户端专用的 spa，要对用户进行身份验证或授权，我们必须让他们请求页面，加载 JavaScript，然后向服务器发送请求来验证用户的会话。幸运的是，Next.js 给了我们 SSR，我们可以使用`getInitialProps();`检查用户在服务器上的会话。

#### [授权助手功能](#authorization-helper-function)

在创建我们的个人资料页面之前，我们将在`www/utils/auth.js`中创建一个助手函数，该函数将限制授权用户的访问。

```
// www/utils/auth.js

import Router from 'next/router'
import nextCookie from 'next-cookies'

export const auth = ctx => {
  const { token } = nextCookie(ctx)

  if (ctx.req && !token) {
    ctx.res.writeHead(302, { Location: '/login' })
    ctx.res.end()
    return
  }

  if (!token) {
    Router.push('/login')
  }

  return token
} 
```

当用户加载页面时，该函数将尝试使用`nextCookie`从 cookie 中获取令牌，然后如果会话无效，它会将浏览器重定向到登录页面，否则 Next.js 将正常呈现页面。

```
// Implementation example
...
Profile.getInitialProps = async ctx => {
  // Check user's session
  const token = auth(ctx);

  return { token }
}
... 
```

对于我们的例子来说，这个助手足够简单，可以在服务器和客户机上工作。最理想的情况是，我们希望限制对服务器的访问，这样我们就不会加载不必要的资源。

#### [授权高阶组件](#authorization-high-order-component)

另一种抽象的方式是使用一个特设，我们可以使用在我们的限制页面，如个人资料。我们可以这样使用它:

```
import { withAuthSync } from '../utils/auth'

const Profile = props =>
  <div>If you can see this, you are logged in.</div>

export default withAuthSync(Profile) 
```

此外，它对我们以后的日志功能也很有用。就像这样，我们以标准的方式编写我们的 HOC，并包含我们的`auth` helper 函数来处理授权。

我们也在我们的`auth.js`文件中创建我们的 HOC。

```
// Gets the display name of a JSX component for dev tools
const getDisplayName = Component =>
  Component.displayName || Component.name || 'Component'

export const withAuthSync = WrappedComponent =>
  class extends Component {
    static displayName = `withAuthSync(${getDisplayName(WrappedComponent)})`

    static async getInitialProps (ctx) {
      const token = auth(ctx)

      const componentProps =
        WrappedComponent.getInitialProps &&
        (await WrappedComponent.getInitialProps(ctx))

      return { ...componentProps, token }
    }

    render () {
      return <WrappedComponent {...this.props} />
    }
} 
```

#### [页面组件带有授权请求](#page-component-with-authorized-requests)

我们的个人资料页面将显示我们的 GitHub 头像、姓名和简历。为了从我们的 API 中提取这些数据，我们需要发送一个授权请求。如果会话无效，我们的 API 将抛出一个错误，如果是这样，我们将把用户重定向到登录页面。

这样，我们就创建了带有授权 API 调用的受限配置文件页面。

```
// www/pages/profile.js

import Router from 'next/router'
import fetch from 'isomorphic-unfetch'
import nextCookie from 'next-cookies'
import Layout from '../components/layout'
import { withAuthSync } from '../utils/auth'

const Profile = props => {
  const { name, login, bio, avatarUrl } = props.data

  return (
    <Layout>
      <img src={avatarUrl} alt='Avatar' />
      <h1>{name}</h1>
      <p className='lead'>{login}</p>
      <p>{bio}</p>

      <style jsx>{`
        img {
          max-width: 200px;
          border-radius: 0.5rem;
        }
        h1 {
          margin-bottom: 0;
        }
        .lead {
          margin-top: 0;
          font-size: 1.5rem;
          font-weight: 300;
          color: #666;
        }
        p {
          color: #6a737d;
        }
      `}</style>
    </Layout>
  )
}

Profile.getInitialProps = async ctx => {
  // We use `nextCookie` to get the cookie and pass the token to the
  // frontend in the `props`.
  const { token } = nextCookie(ctx)
  const protocol = process.env.NODE_ENV === 'production' ? 'https' : 'http'

  const apiUrl = process.browser
    ? `${protocol}://${window.location.host}/api/profile.js`
    : `${protocol}://${ctx.req.headers.host}/api/profile.js`

  const redirectOnError = () =>
    process.browser
      ? Router.push('/login')
      : ctx.res.writeHead(301, { Location: '/login' })

  try {
    const response = await fetch(apiUrl, {
      credentials: 'include',
      headers: {
        'Content-Type': 'application/json',
        Authorization: JSON.stringify({ token })
      }
    })

    if (response.ok) {
      return await response.json()
    } else {
      // https://github.com/developit/unfetch#caveats
      return redirectOnError()
    }
  } catch (error) {
    // Implementation or Network error
    return redirectOnError()
  }
}

export default withAuthSync(Profile) 
```

我们将带有`credentials: "include"`选项的`GET`请求发送给我们的 API，以确保我们发送的头`Authorization`中包含我们的令牌。这样，我们就可以确保我们的 API 获得授权我们的请求和返回数据所需的信息。

### [注销并会话同步](#logout-and-session-synchronization)

在我们的前端，要注销用户，我们需要清除 cookie 并将用户重定向到登录页面。我们在我们的`auth.js`文件中添加了一个函数来完成这个任务。

```
// www/auth.js

import cookie from "js-cookie";
import Router from "next/router";

export const logout = () => {
  cookie.remove("token");
  Router.push("/login");
}; 
```

每次我们需要注销我们的用户时，我们调用这个函数，它应该会处理它。然而，其中一个要求是会话同步，这意味着如果我们注销用户，它应该从所有浏览器选项卡/窗口中完成。为此，我们需要监听一个全局事件监听器，但是我们将使用[存储事件](https://developer.mozilla.org/en-US/docs/Web/Events/storage)，而不是设置自定义事件。

为了让它工作，我们必须将事件监听器添加到所有受限页面的`componentDidMount`方法中，因此我们将把它包含在我们的 [withAuthSync HOC](https://reactjs.org/docs/higher-order-components.html) 中，而不是手动完成。

```
// www/utils/auth.js

// Gets the display name of a JSX component for dev tools
const getDisplayName = Component =>
  Component.displayName || Component.name || 'Component'

export const withAuthSync = WrappedComponent =>
  class extends Component {
    static displayName = `withAuthSync(${getDisplayName(WrappedComponent)})`

    static async getInitialProps (ctx) {
      const token = auth(ctx)

      const componentProps =
        WrappedComponent.getInitialProps &&
        (await WrappedComponent.getInitialProps(ctx))

      return { ...componentProps, token }
    }

    // New: We bind our methods
    constructor (props) {
      super(props)

      this.syncLogout = this.syncLogout.bind(this)
    }

    // New: Add event listener when a restricted Page Component mounts
    componentDidMount () {
      window.addEventListener('storage', this.syncLogout)
    }

    // New: Remove event listener when the Component unmount and
    // delete all data
    componentWillUnmount () {
      window.removeEventListener('storage', this.syncLogout)
      window.localStorage.removeItem('logout')
    }

    // New: Method to redirect the user when the event is called
    syncLogout (event) {
      if (event.key === 'logout') {
        console.log('logged out from storage!')
        Router.push('/login')
      }
    }

    render () {
      return <WrappedComponent {...this.props} />
    }
} 
```

然后，我们将触发所有窗口注销的事件添加到我们的`logout`函数中。

```
// www/utils/auth.js

import cookie from "js-cookie";
import Router from "next/router";

export const logout = () => {
  cookie.remove("token");
  // To trigger the event listener we save some random data into the `logout` key
  window.localStorage.setItem("logout", Date.now()); // new
  Router.push("/login");
}; 
```

最后，因为我们将此功能添加到了我们的身份验证/授权特设中，所以我们不需要在我们的个人资料页面中做任何更改。

现在，每当我们的用户注销时，会话将在所有窗口/选项卡间同步。

## T3】部署到现在 2

剩下的唯一事情就是在我们的`now.json`文件中写入我们的配置。

```
// now.json

{
  "version": 2,
  "name": "cookie-auth-nextjs", //
  "builds": [
    { "src": "www/package.json", "use": "@now/next" },
    { "src": "api/*.js", "use": "@now/node" }
  ],
  "routes": [
    { "src": "/api/(.*)", "dest": "/api/$1" },
    { "src": "/(.*)", "dest": "/www/$1" }
  ]
} 
```

配置文件现在告诉我们如何路由请求以及使用什么构建器。您可以在[部署配置(now.json)](https://zeit.co/docs/v2/deployments/configuration) 页面中了解更多信息。

## [地方发展](#local-development)

在我们的 API 中，当函数`profile.js`和`login.js`部署在 Now 2 中时，它们作为 [lambdas](https://zeit.co/docs/v2/deployments/concepts/lambdas/) 正常工作，但是我们不能像现在这样在本地使用它们。

我们可以通过使用基本路由将这些函数导入到一个小型服务器中来本地使用它们。为了实现这一点，我们创建了第三个名为`dev.js`的文件，它将仅用于本地开发，并将`micro-dev`作为开发依赖项进行安装。

```
$ cd api
$ touch dev.js
$ npm install micro-dev --save-dev 
```

```
// api/dev.js

const { run, send } = require("micro");
const login = require("./login");
const profile = require("./profile");

const dev = async (req, res) => {
  switch (req.url) {
    case "/api/profile.js":
      await profile(req, res);
      break;
    case "/api/login.js":
      await login(req, res);
      break;

    default:
      send(res, 404, "404\. Not found.");
      break;
  }
};

exports.default = (req, res) => run(req, res, dev); 
```

当一个特定的 URL 被请求时，服务器将返回函数，这对于路由来说有点不合常规，但是对于我们的例子来说是可行的。

然后，在我们的前端，我们将为我们的 Next.js 应用程序使用一个定制的服务器，它将某些请求代理到我们的 API 服务器。为此，我们将使用`http-proxy`作为开发依赖项，

```
$ cd www
$ npm install http-proxy --save-dev 
```

```
// www/server.js

const { createServer } = require("http");
const httpProxy = require("http-proxy");
const { parse } = require("url");
const next = require("next");

const dev = process.env.NODE_ENV !== "production";
const app = next({ dev });
const handle = app.getRequestHandler();

const proxy = httpProxy.createProxyServer();
const target = "http://localhost:3001";

app.prepare().then(() => {
  createServer((req, res) => {
    const parsedUrl = parse(req.url, true);
    const { pathname, query } = parsedUrl;

    switch (pathname) {
      case "/":
        app.render(req, res, "/", query);
        break;

      case "/login":
        app.render(req, res, "/login", query);
        break;

      case "/api/login.js":
        proxy.web(req, res, { target }, error => {
          console.log("Error!", error);
        });
        break;

      case "/profile":
        app.render(req, res, "/profile", query);
        break;

      case "/api/profile.js":
        proxy.web(req, res, { target }, error => console.log("Error!", error));
        break;

      default:
        handle(req, res, parsedUrl);
        break;
    }
  }).listen(3000, err => {
    if (err) throw err;
    console.log("> Ready on http://localhost:3000");
  });
}); 
```

最后一步是修改我们的`package.json`，用`npm run dev`运行我们的定制服务器。

```
// www/package.json

...
 "scripts": {
    "dev": "node server.js",
    "build": "next build",
    "start": "next start"
},
... 
```

有了这个设置，我们可以将它部署到根文件夹中的 Now 2 运行`now`，或者使用它在本地运行`api/`文件夹中的`micro-dev dev.js -p 3001`和`www/`文件夹中的`npm run dev`。

## [结论](#conclusion)

这个例子是仔细研究问题评论、建议、代码示例、博客帖子和现有实现，并从中提取精华的结果。

该示例最终成为使用 Next.js 在前端进行身份验证的最简单的表示，省略了现实实现中可能需要的特性和强烈推荐的第三方库，如 Redux 和 Apollo(使用 GraphQL)。此外，该示例是后端不可知的，这使得它很容易在服务器中与任何语言一起使用。

最后，许多讨论之一是是否使用`localStorage`或 cookies。该示例使用 cookies，因此我们可以在服务器和客户端之间共享令牌。
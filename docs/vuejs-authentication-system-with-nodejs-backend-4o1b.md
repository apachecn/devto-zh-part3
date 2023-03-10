# 带有 Node.js 后端的 Vue.js 认证系统

> 原文：<https://dev.to/jscrambler/vuejs-authentication-system-with-nodejs-backend-4o1b>

**这篇文章最初由 Lamin Sanneh** 发表在 [Jscrambler 博客](https://blog.jscrambler.com/vue-js-authentication-system-with-node-js-backend/?utm_source=dev.to&utm_medium=referral)上。

当构建一个 *Vue.js* 认证系统时，涉及到两个主要场景。场景一:一方同时控制前端和后端；场景二:第三方控制后端。如果是后一种情况，我们必须调整前端。它必须处理后端提供的任何身份验证类型。

本教程的最终代码可从以下 GitHub 库获得:

*   [前端客户端](https://github.com/JscramblerBlog/jscrambler-vueauthclient)
*   [后端客户端](https://github.com/JscramblerBlog/jscrambler-vueauthclient-backend)

## 前端 JavaScript 认证类型

在我们的应用程序中，我们可以灵活地选择各种身份验证类型。这是因为我们将同时负责前端和后端。让我们来区分这两种类型。第一种是*本地*或*同域*身份验证—这是前端和后端都在同一个域上运行的情况。第二种是*跨域*认证——前端和后端运行在不同的域上。

这是两个主要类别，但它们下面还有许多子类别。鉴于上述情况，我们将使用*本地*认证，因为我们负责整个系统。我们将使用许多 *Node.js* 库。但是最主要的两个是 [Passport.js](http://www.passportjs.org/) 和 [Express.js](https://expressjs.com/) 。 *Passport.js* 是一个认证库。它提供了几个特性，如*本地*认证、 *OAuth 认证*和*单点登录认证*。 *Express.js* 是用于构建 web 应用的 *Node.js* 的服务器框架。

## 应用范例

我们的应用程序前端将有两个主要页面:登录页面和仪表板页面。经过身份验证的用户和匿名用户都可以访问登录页面。只有经过身份验证的用户才能访问仪表板页面。登录页面将有一个表单，该表单将通过 Ajax 向我们的后端 API 提交数据。然后，后端将检查凭证是否正确，并用 cookie 回复前端。前端将使用这个 cookie 来访问任何锁定的页面。

每次请求锁定页面时，都会重新验证 cookie。如果 cookie 无效或用户未登录，他们将无法访问仪表板。后端将发送一个错误响应，前端将知道如何将用户重定向回登录页面。

我们不会建立一个真正的数据库—我们将在后端使用一组用户来模拟某种形式的数据库。最后，我们将有一个注销链接。这将向我们的服务器发送一个请求，以使我们的当前会话无效，从而注销当前用户。

因此，让我们使用 *Node.js* 作为后端，开始构建我们的 *Vue.js* 认证系统。

## vista . js 前端设置

首先，我们首先需要有最新版本的 *Node.js* 和 *vue-cli* 设置。在撰写本文时， *vue-cli* 的最新版本是版本 3。如果安装的版本是 2，我们想要升级—我们首先需要通过运行:
删除旧版本

```
npm uninstall vue-cli -g 
```

然后运行
安装最新版本

```
npm install -g @vue/cli 
```

接着是

```
npm install -g @vue/cli-init 
```

完成上述设置后，进入终端中的任意文件夹并运行:

```
vue init webpack vueauthclient 
```

这将使用 [webpack](https://webpack.js.org/) 文件夹组织在`vueauthclient`中创建一个新应用程序。

我们应该在命令行上得到一些提示。选择所有缺省值是安全的——但是对于本文，我们可以为测试选择“否”。接下来，使用`cd vueauthclient`导航到这个文件夹，并使用
运行应用程序

```
npm run dev 
```

这将启动一个开发服务器，可通过 URL `localhost:8080`访问。访问这个 URL 后，页面上应该可以看到带有一些文本的 Vue.js 徽标。负责显示该页面的`Vue.js`组件位于文件:

```
vueauthclient/src/components/HelloWorld.vue 
```

## 主登录屏幕

让我们设置我们的登录页面。然后，我们将主页更改为默认的登录页面屏幕，我们还没有创建。从现在开始，当提到文件时，我们将省去主应用程序文件夹`vueauthclient`。

让我们使用
安装名为 [Axios](https://github.com/axios/axios) 的 Ajax 库

```
npm install axios --save 
```

这是一个使 HTTP Ajax 调用任何后端服务器变得更容易的库。它可用于前端和后端应用程序，但在这里，我们将只在前端使用它。

接下来，在`src/components/Login.vue`中创建一个登录组件文件。在该文件中，粘贴以下内容:

```
<template>
    <div>    
        <h2>Login</h2>    
        <form v-on:submit="login">    
            <input type="text" name="email" /><br>    
            <input type="password" name="password" /><br>    
            <input type="submit" value="Login" />    
        </form>    
    </div>
</template>

<script>
    import router from "../router"    
    import axios from "axios"    
    export default {    
        name: "Login",    
        methods: {    
            login: (e) => {    
                e.preventDefault()    
                let email = "user@email.com"   
                let password = "password"    
                let login = () => {    
                    let data = {    
                        email: email,    
                        password: password    
                    }    
                    axios.post("/api/login", data)    
                        .then((response) => {    
                            console.log("Logged in")    
                            router.push("/dashboard")    
                        })    
                        .catch((errors) => {    
                            console.log("Cannot log in")    
                        })    
                }    
                login()    
            }    
        }    
    }
</script> 
```

让我们分解这段代码，看看发生了什么。

下面的模板部件是一个带有两个输入字段的表单:`email`和`password`。该表单附带了一个`submit`事件处理程序。使用 *Vue.js* 语法`v-on:submit="login"`，这将把字段数据提交给`login`组件方法。

```
<template>
    <div>
        <h2>Login</h2>
        <form v-on:submit="login">
            <input type="text" name="email" /><br>
            <input type="password" name="password" /><br>    
            <input type="submit" value="Login" />    
        </form>    
    </div>
</template> 
```

在代码的脚本部分，如下所示，我们正在导入路由器文件。这个住在`src/router/index.js`。我们还为前端导入了 *Axios* ajax 库。然后，我们存储用户凭证，并向我们的后端服务器发出登录请求:

```
<script>
    import router from "../router"        
    import axios from "axios"    
    export default {    
        name: "Login",    
        methods: {    
            login: (e) => {    
                e.preventDefault()   
                let email = "user@email.com"
                let password = "password"
                let login = () => {
                    let data = {
                        email: email,
                        password: password
                    }
                    axios.post("/api/login", data)
                        .then((response) => {
                            console.log("Logged in")
                            router.push("/dashboard")
                        })
                        .catch((errors) => {
                            console.log("Cannot login")
                        })
                }
                login()
            }
        }
    }
</script> 
```

在下面的脚本区，

```
e.preventDefault()
let email = "[user@email.com](mailto:user@email.com)"
let password = "password" 
```

我们现在将硬编码的用户名和密码存储在变量中。这有助于通过防止我们重复输入相同的东西来加速开发。稍后，我们将把它们切换出来，从表单提交中获取真正的数据。

在下面代码的最后部分，我们使用上面的凭证进行 ajax 调用。在来自服务器的`ok`响应的情况下，我们将用户重定向到*仪表板*。如果响应不是`ok`，我们将停留在相同的页面上，并在控制台中记录一个错误。

```
let login = () => {
  let data = {
    email: email,
    password: password
  }
  axios.post("/api/login", data)
    .then(response => {
      console.log("Logged in")
      router.push("/dashboard")
    })
    .catch(errors => {
      console.log("Cannot login")
    })
}
login() 
```

现在我们已经设置了登录组件，让我们更改路由器以确保它能够识别新页面。在文件`src/router/index.js`中，将现有路由器更改为:

```
import Vue from "vue"
import Router from "vue-router"
import Login from "@/components/Login"
import HelloWorld from "@/components/HelloWorld"
Vue.use(Router)
export default new Router({
  routes: [
    {
      path: "/",
      name: "HelloWorld",
      component: HelloWorld
    },
    {
      path: "/login",
      name: "Login",
      component: Login
    }
  ]
}) 
```

我们所做的是导入我们的新组件，然后向 routes 数组添加一个对象。删除 HelloWorld 路由注册，因为我们不再需要它。

最后，对于登录页面，让我们确保它是应用程序的默认页面。从
改变登录路径注册的当前路径

```
path: "/login", 
```

至

```
path: "/", 
```

不要忘记删除 HelloWorld 路由的路由注册，否则可能会发生错误。在浏览器中再次导航到`localhost:8080`，我们应该会看到新的登录表单。在这个阶段提交除了抱怨后端 URL `localhost:8080/api/login`不存在之外，不会做任何事情。

## 设置第一个安全页面-仪表板

现在进入仪表板页面。通过在`src/components/Dashboard.vue`创建一个文件来为它创建一个组件。在那里，粘贴以下内容:

```
<template>
    <div>    
        <h2>Dashboard</h2>    
        <p>Name: {{ user.name }}</p>    
    </div>
</template>
<script>
    import axios from "axios"    
    import router from "../router"    
    export default {    
        name: "Login",    
        data() {    
            return {    
                user: {    
                    name: “Jesse”    
                }    
            }    
        },    
        methods: {    
            getUserData: function() {    
                let self = this    
                axios.get("/api/user")    
                    .then((response) => {    
                        console.log(response)    
                        self.$set(this, "user", response.data.user)    
                    })    
                    .catch((errors) => {    
                        console.log(errors)    
                        router.push("/")    
                    })    
            }    
        },    
        mounted() {    
            this.getUserData()    
        }    
    }
</script> 
```

在模板部分，我们显示当前用户名。在设置后端之前，我们将在前端对用户进行硬编码。这是为了让我们能够处理这个页面，否则我们会得到一个错误。

在脚本部分，我们正在导入 *Axios* 库和我们的路由器。然后，我们为组件提供了一个数据函数，返回一个带有用户属性的对象。正如我们所看到的，我们目前有一些硬编码的用户数据。

我们还有两个方法叫做`getUserData`和`mounted`。组件打开后, *Vue.js* 引擎调用`mounted`方法。我们只要申报就行了。第二种方法，`getUserData`被称为`mounted`法。在这里，我们调用后端服务器来获取当前登录用户的数据。

在调用后端的过程中，我们从服务器得到一个响应。我们将根据响应类型处理两种可能的情况。
首先，如果调用成功，我们使用:
用后端返回的数据设置用户属性

```
self.$set(this, "user", response.data.user) 
```

其次，如果存在登录问题，服务器会以错误响应。然后，前端将用户重定向回登录页面，代码如下:

```
router.push("/") 
```

我们使用上面的`push`方法进行重定向，它在名为`vue-router`的包中可用， *Vue.js* 的默认路由器。让我们通过将它添加到路由文件中来添加该页面的路由配置，就像我们对登录页面所做的那样。导入组件:

```
import Dashboard from "@/components/Dashboard" 
```

并添加路线定义:

```
{
    path: "/dashboard",
    name: "Dashboard",
    component: Dashboard
} 
```

## 用 Axios 设置前端数据层

现在我们已经有了我们的前端页面，让我们配置 *Axios* 和 *Vue.js* 。我们将让他们准备好与我们的后端通信。因为我们处于开发阶段，前端运行在端口`8080`上。一旦我们开始开发我们的后端服务器，它将运行在一个不同的端口号 3000 上。在我们为生产做好准备之前，情况将一直如此。

没有什么可以阻止我们在同一个端口上运行它们。事实上，最终会是这样。如果我们回忆一下，我们会选择*同域*方法。现在，我们将在不同的端口上运行后端和前端。这是因为我们想要利用 *Vue.js* 开发服务器的许多有用特性。我们将在后面的章节中讨论如何合并两者(前端和后端)。

在继续之前，让我们在这里强调一个问题。在不同的端口上开发我们的应用程序有一个缺点。它被称为[跨源请求共享](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)，简称`CORS`。默认情况下，它不允许我们向后端发出*跨域* Ajax 请求。有一个 *Node.js* 库可以解决这个问题，但是我们将把它留给另一个教程。

[![Protect your Vue App with Jscrambler](img/487999d84e82d029624bc043b6e94a7c.png)](https://blog.jscrambler.com/how-to-protect-your-vue-js-application-with-jscrambler/?utm_source=dev.to&utm_medium=referral)

*Vue.js* 开发服务器有个叫*代理*的东西。它允许我们的后端服务器认为前端运行在与自己相同的端口上。要启用该特性，打开`config/index.js`中的配置文件。在`dev`属性下，添加一个对象，如下:

```
proxyTable: {

"/api": "http://localhost:3000"

}, 
```

在上面的代码中，我们将以 */api* 开头的 Ajax 请求重新路由到 URL `http://localhost:3000`。请注意，这不同于我们的前端应用程序运行的 URL。如果我们没有这段代码，Ajax 请求默认会被发送到`http://localhost:8080`，这不是我们想要的。准备生产时，我们可以将其移除:

最后，使用
安装前端 cookie 库

```
npm install vue-cookies --save 
```

## 保护我们的后端 API

现在让我们继续设置一个 *Node.js* 后端。首先，我们需要在你的系统上安装 [Node.js](https://nodejs.org/en/) 来完成这个部分。前往终端窗口。创建一个名为`vueauthclient-backend`的空文件夹。使用
导航至文件夹

```
cd vueauthclient-backend 
```

然后使用命令
初始化一个新的 *Node.js* 应用程序

```
npm init 
```

会有几个提示。让我们接受默认值，并在需要的地方指定值。我们应该以一个名为`package.json`的文件结束。在项目的根目录下创建一个名为`index.js`的文件。这是我们的主要代码所在的地方。使用命令
安装几个库

```
npm install --save body-parser cookie-session express passport passport-local 
```

*   *body-parser* 库允许我们从前端发送的 Ajax 请求中访问值。
*   cookie-session 允许我们在服务器上存储 cookie，并且能够在客户端登录时发送一个给客户端。
*   *express* 是我们的`Node.js`框架，帮助我们构建 Ajax APIs。它还允许我们从我们的`Node.js`应用程序中提供静态文件。
*   *passport.js* 是一个帮助我们认证用户的库。它通过为每个用户创建和管理会话来实现这一点。
*   *passport-local* 是 *Passport.js* 的库组件。它专门通过使用*本地*认证类型进行简单认证。例如，如果我们想要使用`SSO`登录类型，我们将需要安装具有该特性的 *Passport.js* 组件。现在我们已经安装了库，让我们导入并设置它们。

在`index.js`文件的顶部，使用代码:
导入库

```
const express = require('express')

// creating an express instance
const app = express()
const cookieSession = require('cookie-session')
const bodyParser = require('body-parser')
const passport = require('passport')

// getting the local authentication type
const LocalStrategy = require('passport-local').Strategy 
```

首先，让我们使用
初始化 *cookie 会话*和*主体解析器*库

```
app.use(bodyParser.json())

app.use(cookieSession({
    name: 'mysession',
    keys: ['vueauthrandomkey'],
    maxAge: 24 * 60 * 60 * 1000 // 24 hours
})) 
```

我们将 cookie 设置为 24 小时后过期。接下来，让我们指示我们的 *Node.js* app，我们要使用 *Passport.js* 。添加下面一行代码:

```
app.use(passport.initialize()); 
```

接下来，告诉 *Passport.js* 启动它的会话管理系统:

```
app.use(passport.session()); 
```

因为我们不会使用真正的数据库来管理用户，为了简洁起见，我们将使用一个数组。添加以下几行:

```
let users = [
  {
    id: 1,
    name: "Jude",
    email: "user@email.com",
    password: "password"
  },
  {
    id: 2,
    name: "Emma",
    email: "emma@email.com",
    password: "password2"
  }
] 
```

接下来，让我们设置登录、注销和获取用户数据的 URL。这些将分别出现在`POST /api/login`、`GET /api/logout`和`GET /api/user`。对于登录部分，粘贴以下内容:

```
app.post("/api/login", (req, res, next) => {
  passport.authenticate("local", (err, user, info) => {
    if (err) {
      return next(err);
    }

    if (!user) {
      return res.status(400).send([user, "Cannot log in", info]);
    }

    req.login(user, err => {
      res.send("Logged in");
    });
  })(req, res, next);
}); 
```

在这里，我们指示 *Express.js* 使用提供的凭证对用户进行身份验证。如果出现错误或失败，我们将向前端返回一条错误消息。如果用户登录了，我们会用一条成功消息来响应。 *Passport.js* 处理证件检查。我们将很快对此进行设置。注意，方法`passport.authenticate`驻留在 *Passport.js* 库中。

我们将设置的下一个 URL 是`logout`。这使得我们的 *cookie* 失效，如果有的话。添加这个实现功能:

```
app.get("/api/logout", function(req, res) {
  req.logout();

  console.log("logged out")

  return res.send();
}); 
```

最后，获取当前登录用户数据的 URL。登录后，`Passport.js`使用来自前端的 cookie 作为标识符向请求添加一个用户对象。我们必须使用该对象的 id 从后端的数据数组中获取所需的用户数据。粘贴以下内容:

```
app.get("/api/user", authMiddleware, (req, res) => {
  let user = users.find(user => {
    return user.id === req.session.passport.user
  })

  console.log([user, req.session])

  res.send({ user: user })
}) 
```

注意，这一次，我们在回调之前传递了第二个变量。这是因为我们想要保护这个 URL，所以我们传递一个中间件过滤器。该过滤器将检查当前会话是否有效，然后才允许用户继续其余的操作。让我们使用:
来创建中间件

```
const authMiddleware = (req, res, next) => {
  if (!req.isAuthenticated()) {
    res.status(401).send('You are not authenticated')
  } else {
    return next()
  }
} 
```

我们必须确保在为`/api/user`创建 API 路由之前声明它。

接下来，让我们配置 *Passport.js* 以便它知道如何让我们登录。登录后，它将用户对象数据存储在一个 *cookie 会话*中，并在以后的请求中检索数据。要使用*本地策略*配置 *Passport.js* ，添加以下内容:

```
passport.use(
  new LocalStrategy(
    {
      usernameField: "email",
      passwordField: "password"
    },

    (username, password, done) => {
      let user = users.find((user) => {
        return user.email === username && user.password === password
      })

      if (user) {
        done(null, user)
      } else {
        done(null, false, { message: 'Incorrect username or password'})
      }
    }
  )
) 
```

在这里，我们指示 *Passport.js* 使用我们上面创建的`LocalStrategy`。我们还指定哪些领域期待从前端，因为它需要一个用户名和密码。然后，我们使用这些值来查询用户。如果这些有效，我们调用`done`回调，它将在会话中存储用户对象。如果无效，我们将用一个`false`值调用 done 回调，并返回一个错误。需要注意的一点是，上面的代码与`login` URL 协同工作。URL 回调中对`passport.authenticate`的调用触发了上面的代码。

接下来，让我们告诉 *Passport.js* 如何处理给定的用户对象。如果我们想在会话中存储它之前做一些工作，这是必要的。在这种情况下，我们只想存储 id，因为当我们从 cookie 中提取它时，它足以识别用户。添加以下内容以实现这一点:

```
passport.serializeUser((user, done) => {
  done(null, user.id)
}) 
```

接下来，我们来设置反向。当用户请求安全的 URL 时。我们告诉 passport 如何从我们的用户数组中检索用户对象。它将使用我们使用`serializeUser`方法存储的 id 来实现这一点。补充一下:

```
passport.deserializeUser((id, done) => {
  let user = users.find((user) => {
    return user.id === id
  })

  done(null, user)
}) 
```

现在，让我们使用下面的代码添加启动 *Node.js* 服务器的代码:

```
app.listen(3000, () => {
  console.log("Example app listening on port 3000")
}) 
```

运行命令:

```
node index.js 
```

这实际上启动了服务器。控制台中会出现一条消息，文本为*示例应用程序监听端口 3000* 。

## 准备生产

现在，当我们访问页面`localhost:8080`时，我们应该会看到一个登录表单。当我们提交表单时，我们被重定向到仪表板页面。我们使用前面设置的代理来实现这一点。

这对于开发来说是可以接受的——但是它违背了拥有一个*同域*应用程序的目的。为了有一个*同域*场景，我们需要为生产编译我们的应用程序。

在此之前，让我们测试一下代理是否在工作。注释掉`config/index.js`中的代理 URL 代码。我们可能需要重启开发服务器，因为我们更改了一个配置文件。

现在，让我们重新访问登录页面并提交表单。我们将得到一个错误，说不允许我们访问后端服务器。为了解决这个问题，我们需要配置我们的 *Node.js* 后端服务器。后端现在将服务于我们的前端应用程序，而不是开发服务器。

在前端控制台中，运行命令:

```
npm run build 
```

这将生成生产所需的所有必要文件。我们可以在`dist`文件夹中找到该命令创建的所有文件。从这一点开始，我们有两个选择:我们可以复制这个文件夹，使它成为我们的 *Node.js* 应用程序的一部分，或者我们可以告诉 *Node.js* 服务器在我们的文件系统中直接引用它。如果我们仍然希望它们作为单独的存储库，那么后者是有用的。我们将使用后一种方法。

导航到文件夹`dist`。运行命令`pwd`获取 dist 文件夹的绝对路径，假设我们在基于 Linux 的系统或 Mac 上。如果我们在 Windows 上，我们可以使用等效的命令获得文件夹的绝对路径。

复制绝对路径，但不要忘记在任何修改后重启 *Node.js* 服务器。因为我们不想一直重启服务器，所以让我们安装 [nodemon](https://github.com/remy/nodemon) 。当我们的代码改变时，它可以为我们处理。

接下来，在导入语句后粘贴以下内容:

```
const publicRoot = '/absolute/path/to/dist'

app.use(express.static(publicRoot)) 
```

这是告诉服务器在哪里寻找文件。

最后一步是向我们的 *Node.js* 应用程序的根添加一个路由。这就是我们已经编译好的*生产就绪*代码。加入
就可以了

```
app.get("/", (req, res, next) => {
  res.sendFile("index.html", { root: publicRoot })
}) 
```

现在，即使代理被禁用，让我们访问服务器根`localhost:3000`。我们将看到登录表单。提交这个，我们应该看到显示用户名的仪表板页面。

## 注销功能和登录数据

请注意，我们的应用程序仍然使用硬编码的数据，我们希望从提交的表单中获取这些数据。将登录组件中的这些行从:

```
let email = "user@email.com"

let password = "password" 
```

致:

```
let email = e.target.elements.email.value

let password = e.target.elements.password.value 
```

现在，我们使用表单中的数据。接下来，让我们设置一个链接来注销我们。在组件`src/App.vue`中，将模板更改为:

```
<template>
    <div id="app">    
        <img src="./assets/logo.png">    
        <div>    
            <router-link :to="{ name: 'Dashboard'}">Dashboard</router-link>    
            <router-link :to="{ name: 'Login'}">Login</router-link>    
            <a href="#" v-on:click="logout">Logout</a>    
        </div>    
        <router-view/>    
    </div>
</template> 
```

在这里，我们创建了登录页面、仪表板页面和注销链接的链接。注销链接目前没有相应的方法，所以让我们创建它。在`src/App.vue`中，在`scripts`部分添加注销方法:

```
 logout: function (e) {
    axios
      .get("/api/logout")
      .then(() => {
        router.push("/")
      })
} 
```

这里，我们向 *Node.js* 后端发出一个 Ajax 请求。然后，当响应返回时，我们将用户重定向到登录页面。注销将无法与我们部署的应用程序一起工作，因为我们需要使用
再次为生产部署它

```
npm run build 
```

现在，我们可以重新访问 URL `localhost:3000`。我们可以登录、注销和访问仪表板页面。

## 结论

学完本教程后，我们应该能够添加尽可能多的认证页面。

如果这是第一次使用 *Vue.js* ，请在这里查看我们的介绍性[博文](https://blog.jscrambler.com/build-app-vue-js/?utm_source=dev.to&utm_medium=referral)。这将有助于建立和构建一个基本的 *Vue.js* 应用程序。

此外，不要忘记保护你的 Vue.js 应用程序免受代码窃取和逆向工程。参见[我们关于使用 Jscrambler 保护 Vue.js 应用的便捷指南](https://blog.jscrambler.com/how-to-protect-your-vue-js-application-with-jscrambler/?utm_source=dev.to&utm_medium=referral)。
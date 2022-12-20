# 像老板一样说话

> 原文：<https://dev.to/simov/oauth-like-a-boss-2m3b>

在我的[上一篇文章](https://dev.to/simov/oauth-simplified-2pbd)中我谈到了 **[格兰特](https://github.com/simov/grant)** :

> 一个用于 NodeJS 的 OAuth 中间件，同时也是一个完全透明的 OAuth 代理。

这一次，我们将探索几个真实世界的例子:

1.  从服务器应用程序登录，用 JavaScript 编写。
2.  从 GitHub 页面上托管的浏览器应用程序登录。
3.  从浏览器扩展登录。
4.  从用另一种编程语言编写的服务器应用程序登录。

# 平常的东西

想象一下，我们有一个托管在`awesome.com`上的 web 应用程序，也想象一下，我们的应用程序利用 [GitHub API](https://developer.github.com/v3/) 来管理我们用户的存储库。我们的网站上还有一个网页表单，允许我们的用户只选择他们愿意授予我们的应用程序
的[权限](https://developer.github.com/apps/building-oauth-apps/understanding-scopes-for-oauth-apps/#available-scopes)

```
<form action="/connect/github" method="POST">
  <p>Grant read/write access to:</p>
  <label>
    <input type="radio" group="scope" name="scope" value="repo" />
    public and private repositories</label>
  <label>
    <input type="radio" group="scope" name="scope" value="public_repo" />
    public repositories only</label>
  <button>Login</button>
</form> 
```

这张表格将被送到`POST`所选择的 OAuth `scope`到`/connect/github`的路线，即 [Grant](https://github.com/simov/grant) 操作的路线。

接下来，我们需要一个[授权](https://github.com/simov/grant)服务器来为我们处理 OAuth 流:

```
var express = require('express')
var session = require('express-session')
var parser = require('body-parser')
var grant = require('grant-express')

express()
  .use(session({secret: 'dev.to'}))
  .use(parser.urlencoded()) // only needed for POST requests
  .use(grant(require('./config.json')))
  .use('/login', (req, res) => res.end(`the above HTML form`))
  .use('/hello', (req, res) => {
    var {access_token} = req.session.grant.response
    console.log(access_token)
    res.end('nice!')
  })
  .listen(3000) 
```

具有以下配置:

```
{  "defaults":  {  "origin":  "https://awesome.com",  "state":  true,  "transport":  "session"  },  "github":  {  "key":  "...",  "secret":  "...",  "dynamic":  ["scope"],  "callback":  "/hello"  }  } 
```

我们允许 OAuth `scope`为 GitHub 设置`dynamic` ally。我们还将使用这个会话作为`transport`，在我们的最终`callback`路由中交付 OAuth 流的结果。

最后，我们必须在 GitHub 上创建一个实际的 [OAuth 应用程序](https://github.com/settings/applications/new)，并将其`key`和`secret`复制/粘贴到上面的配置中。我们还必须将它的*授权回调 URL* 设置为`https://awesome.com/connect/github/callback`，这是 [Grant](https://github.com/simov/grant) 保留的第二条路由。

这将允许我们选择一个范围，并通过导航到`https://awesome.com/login`登录 GitHub

# 从另一台主机登录

现在想象一下，我们在 [GitHub 页面](https://pages.github.com/)的`https://simov.github.io/stars/`上托管了另一个应用，它允许用户探索 GitHub 上托管的给定存储库收到的明星的统计数据和历史。

我们的应用程序将只访问公共数据。不幸的是，GitHub 对 API 的默认速率限制是每小时 60 个 HTTP 请求。但是，如果请求与访问令牌一起发送，速率限制将提高到每小时 5000 个 HTTP 请求。

所以，我们需要再次登录，**但是**我们已经有一个[授权的](https://github.com/simov/grant)服务器在`awesome.com`上运行，那么为什么不重用它:

```
{  "defaults":  {  "origin":  "https://awesome.com",  "state":  true,  "transport":  "session"  },  "github":  {  "key":  "...",  "secret":  "...",  "dynamic":  ["scope"],  "callback":  "/hello",  "overrides":  {  "stars":  {  "key":  "...",  "secret":  "...",  "dynamic":  ["callback"],  "transport":  "querystring"  }  }  }  } 
```

我们希望 GitHub 有一个名为`stars`的子配置。这将是一个不同的 [OAuth 应用](https://github.com/settings/applications/new)，注意`key`和`secret`。

我们还想设置最终的`callback` URL `dynamic` ally，但不是它上面允许的`scope`。我们将在没有任何显式作用域设置的情况下登录，这在 GitHub 的情况下[意味着只获得对公共数据的读取权限。](https://developer.github.com/apps/building-oauth-apps/understanding-scopes-for-oauth-apps/#available-scopes)

最后，我们覆盖了从`defaults`继承的`transport`。我们需要将响应数据编码为完全限定的绝对`callback` URL 中的 querystring，指向我们托管在 [GitHub 页面](https://pages.github.com/)上的浏览器应用。

然后，我们必须导航到连接路由进行登录:

```
// store the current URL
localStorage.setItem('redirect', location.href)
// set callback URL dynamically - https://simov.github.io/stars/
var callback = encodeURIComponent(location.origin + location.pathname)
// navigate to the connect route
location.href = `https://awesome.com/connect/github/stars?callback=${callback}` 
```

动态设置我们的最终`callback` URL 很方便，以防我们将来想要在不同的域上托管我们的应用。

登录后，用户将被重定向回我们的浏览器应用，托管在 GitHub:

```
https://simov.github.io/stars/?access_token=... 
```

现在是时候从查询字符串中提取`access_token`并存储它以备将来使用:

```
var qs = new URLSearchParams(location.search)
localStorage.setItem('token', qs.get('access_token')) 
```

这是一个很好的最后接触，将我们的用户重定向到他们开始使用我们的 OAuth 应用程序登录之前的位置:

```
location.href = localStorage.getItem('redirect') // go back to the last URL
localStorage.removeItem('redirect') 
```

这是我之前提到的应用程序。

# 从浏览器扩展登录

接下来我们有一个[浏览器扩展](https://developer.chrome.com/extensions)，它通过添加一个很酷的小按钮来增强 GitHub 的 UI。当点击时，它会聚集一些关于我们当前浏览的库的有用信息:

```
{  "manifest_version":  2,  "name":  "Cool Little Button",  "version":  "1.0.0",  "background"  :  {"scripts":  ["background.js"]},  "content_scripts":  [{"matches":  ["https://github.com/*"],  "js":  ["content.js"]}],  "permissions":  ["storage"]  } 
```

然而，这个扩展依赖于从 [GitHub API](https://developer.github.com/v3/) 获取的数据，默认情况下，这将再次限制我们每小时 60 个 HTTP 请求。因此，这将是很好的，让我们的用户登录快速，方便，直接从我们的扩展，因此取消速率限制到每小时 5000 HTTP 请求:

```
{  "defaults":  {  "origin":  "https://awesome.com",  "state":  true,  "transport":  "session"  },  "github":  {  "key":  "...",  "secret":  "...",  "dynamic":  ["scope"],  "callback":  "/hello",  "overrides":  {  "stars":  {  "key":  "...",  "secret":  "...",  "dynamic":  ["callback"],  "transport":  "querystring"  },  "extension":  {  "dynamic":  false,  "transport":  "querystring",  "callback":  "https://github.com/extension/callback"  }  }  }  } 
```

这一次我们将重用从 GitHub 配置的根级别继承的 OAuth 应用程序(`key`和`secret`)。我们也不希望用户设置任何配置选项`dynamic`盟友。

然后我们可以从`background.js`脚本中打开一个新的选项卡，让我们的用户登录:

```
chrome.tabs.create({url: 'https://awesome.com/connect/github/extension')}) 
```

我们会将用户重定向到 GitHub 上一个不存在的页面。在这种情况下，GitHub 将使用 404 Not Found 状态代码的通用 HTML 页面进行响应，**但是**我们的访问令牌将在 querystring:
中进行编码

```
https://github.com/extension/callback?access_token=... 
```

同样，我们需要做的就是提取并存储它，这次是通过将下面的代码放入`content.js`脚本:

```
var qs = new URLSearchParams(location.search)
chrome.storage.sync.set({token: qs.get('access_token')}) 
```

# 从另一种语言登录

[Grant](https://github.com/simov/grant) 不是把我们限制在服务器上的 JavaScript 和 NodeJS。能够通过 HTTP 进行动态配置，并将结果发送回我们想要的任何地方，Grant 使我们能够访问来自**任何其他编程语言的 **180+** 登录提供者。**

这一次我们将把我们的子配置命名为`proxy`，允许`dynamic`配置 Grant:
中的每个可用选项

```
{  "defaults":  {  "origin":  "https://awesome.com",  "state":  true,  "transport":  "session"  },  "github":  {  "key":  "...",  "secret":  "...",  "dynamic":  ["scope"],  "callback":  "/hello",  "overrides":  {  "stars":  {  "key":  "...",  "secret":  "...",  "dynamic":  ["callback"],  "transport":  "querystring"  },  "extension":  {  "dynamic":  false,  "transport":  "querystring",  "callback":  "https://github.com/extension/callback"  },  "proxy":  {  "dynamic":  true  }  }  }  } 
```

出于这个例子的目的，我将使用 [Go](https://golang.org/) ，我只需选择一个，但以下适用于任何其他语言:

```
package main
import (
  "fmt"
  "net/url"
  "net/http"
)
func main() {
  http.HandleFunc("/login", func (w http.ResponseWriter, r *http.Request) {
    qs := url.Values{}
    qs.Add("key", "...") // yes
    qs.Add("secret", "...") // we're passing an OAuth app dynamically!
    qs.Add("scope", "repo user gist")
    qs.Add("transport", "querystring")
    qs.Add("callback", "http://localhost:3000/hello")
    http.Redirect(w, r, "https://awesome.com/connect/github/proxy?" + qs.Encode(), 301)
  })
  http.HandleFunc("/hello", func (w http.ResponseWriter, r *http.Request) {
    qs, _ := url.ParseQuery(r.URL.RawQuery)
    fmt.Println(qs["access_token"][0])
    w.Write([]byte("nice!"))
  })
  http.ListenAndServe(":3000", nil)
} 
```

现在我们需要做的就是导航到`http://localhost:3000/login`

# 结论

我可以用 5 行 JSON 配置来总结整篇文章。

然而，它的目的是演示服务器端登录如何与各种类型的应用程序连接，以及 [Grant](https://github.com/simov/grant) 如何适应更广泛的情况。

希望这将为希望利用服务器端登录的前端开发人员以及希望快速访问许多提供商的后端开发人员提供方便的指导。

这里是[所有例子的源代码](https://github.com/simov/oauth-like-a-boss)。

编码快乐！
# Golang web 应用中如何控制路由器访问权限

> 原文：<https://dev.to/ale_ukr/how-to-control-router-access-permissions-in-golang-web-application-1ofo>

在这篇文章中，我将介绍如何限制用户访问 golang web 应用程序中的特定 url。我将使用一个轻量级的、惯用的、可组合的路由器来构建 Go HTTP 服务。

让我们创建我们的主包。

```
package main

import (
    "net/http"
    "github.com/go-chi/chi"
)

func main() {
  r := chi.NewRouter()

  r.Get("/", homePageHandler)
  r.Get("/admin", adminPageHandler)
  http.ListenAndServe(":3000", r)
}

func homePageHandler(w http.ResponseWriter, r *http.Request) {
  w.Write([]byte("This is home page")) 
}

func adminPageHandler(w http.ResponseWriter, r *http.Request) {
  w.Write([]byte("This is admin page")) 
} 
```

在此之后，如果我们转到`/admin`页面，我们会看到“这是管理页面”。

现在，让我们将此路径设置为仅允许管理员访问。

我们必须更换

`r.Get("/admin", adminPageHandler)`
同
`r.Mount("/admin", adminRouter())`

Mount 附加另一个 http。处理器或 chi 路由器作为路由路径上的子路由器。

然后，我们必须在 adminRouter()函数中附加中间件。

```
func adminRouter() http.Handler {
    r := chi.NewRouter()
    // Middleware with access rules for router.
    r.Use(AdminOnly)
    r.Get("/", adminPageHandler)

    return r
} 
```

在这个中间件中，我们有一个简单的检查，用户是否有权访问这个页面。

```
func AdminOnly(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        // If user is admin, allows access.
        if IsLoggedInAdmin(r) {
            next.ServeHTTP(w, r)
        } else {
            // Otherwise, 403.
            http.Error(w, http.StatusText(403), 403)
            return
        }

        return
    })
} 
```

为了便于演示，我将只使用一个随机布尔函数来决定是否使用 admin。您可以根据您的用户验证模型修改此功能。

```
func IsLoggedInAdmin(r *http.Request) bool {
    return rand.Float32() < 0.5
} 
```

仅此而已。看起来真的很简单，不是吗？

让我们再转到第`/admin`页。

正如你所看到的，现在，有时(取决于我们的随机决定)，用户没有访问这个页面了。

你可以在这里找到源代码
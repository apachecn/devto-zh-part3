# 在 Go 中版本化您的 API

> 原文：<https://dev.to/geosoft1/versioning-your-api-in-go-1g4h>

在某些时候，你的 API 需要有类似于`/v1`或`/v2`的版本(比如 [github](https://developer.github.com/v3/) API)。
为了在 Go 中实现这一点，我将使用 [gorilla/mux](https://github.com/gorilla/mux) 路由器，并且我将假设您拥有一个功能正常的 [Go](https://golang.org/doc/install) 环境。

我们将使用以下`main.go`文件创建一个新项目:

```
package main

import (
    "flag"
    "net/http"

    "github.com/gorilla/mux"
)

var (
    port = flag.String("port", "8080", "port")
)

func main() {
    flag.Parse()
    var router = mux.NewRouter()
    var api = router.PathPrefix("/api").Subrouter()
    api.NotFoundHandler = http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusNotFound)
    })
    http.ListenAndServe(":"+*port, router)
} 
```

简而言之，我们已经创建了一个新的路由器，它有一个很好的 soubrouter 来处理代表我们版本化路由基础的`/api`。
路线会显示为`/api/v1/endpoint`、`/api/v2/endpoint`等。

此外，我们还定义了一个 not found 处理程序，它附加到子路由器上，只返回一个状态代码。

**注意**我们将在以下各种返回代码中使用，以了解在每个时刻执行的是什么例程。

在这一步中，我们可以将一个[中间件](https://github.com/gorilla/mux#middleware)连接到我们的子路由器，以打印当前请求的路由。

```
api.Use(func(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        log.Println(r.RequestURI)
        next.ServeHTTP(w, r)
    })
}) 
```

现在我们准备添加我们的第一个 API 版本

```
var api1 = api.PathPrefix("/v1").Subrouter()
api1.HandleFunc("/status", func(w http.ResponseWriter, r *http.Request) {
    w.WriteHeader(http.StatusOK)
})
api1.NotFoundHandler = http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    w.WriteHeader(http.StatusForbidden)
}) 
```

以与`/api`创建相同的方式，添加`/v1`子路由器和相应的未找到处理程序。注意，这个副路由器的底座是`api`副路由器，而不是主`router`。我们还为名为`/status`的端点定义了处理函数。同样，我们可以创建`/v2`。只要粘贴这段代码，用 2 替换 1，我们的代码就变成了

```
func main() {
    flag.Parse()
    var router = mux.NewRouter()
    var api = router.PathPrefix("/api").Subrouter()
    api.NotFoundHandler = http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusNotFound)
    })
    api.Use(func(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            log.Println(r.RequestURI)
            next.ServeHTTP(w, r)
        })
    })
    var api1 = api.PathPrefix("/v1").Subrouter()
    api1.HandleFunc("/status", func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusOK)
    })
    api1.NotFoundHandler = http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusForbidden)
    })
    var api2 = api.PathPrefix("/v2").Subrouter()
    api2.HandleFunc("/status", func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusAccepted)
    })
    api2.NotFoundHandler = http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusNoContent)
    })
    http.ListenAndServe(":"+*port, router)
} 
```

我们已经准备好测试我们的 API。为此，运行项目并使用`curl`。

```
curl -I 'localhost:8080/api/'
HTTP/1.1 403 Not Found

curl -I 'localhost:8080/api/v1/'
HTTP/1.1 404 Forbidden

curl -I 'localhost:8080/api/v1/status'
HTTP/1.1 200 OK

curl -I 'localhost:8080/api/v2/'
HTTP/1.1 204 No Content

curl -I 'localhost:8080/api/v2/status'
HTTP/1.1 200 Accepted 
```

测试看起来没问题，但是我们错过了 API 的认证。第一个想法是简单地使用一个带有令牌的 [MatcherFunc](https://github.com/gorilla/mux#matching-routes) 和下面的代码行

```
var api = router.PathPrefix("/api").Subrouter() 
```

成为

```
var api = router.MatcherFunc(func(r *http.Request, rm *mux.RouteMatch) bool {
    return r.Header.Get("x-auth-token") == "admin"
}).PathPrefix("/api").Subrouter() 
```

再次测试

```
curl -I 'localhost:8080/api/v1/status' -H "x-auth-token: admin"
HTTP/1.1 202 OK

curl -I 'localhost:8080/api/v1/status' -H "x-auth-token: notadmin"
HTTP/1.1 404 Not Found 
```

不好，如果我输入错误的密码，我会收到`not found`错误代码。这基本上没什么问题，但我想看看我是否被授权。因此，我们将移动中间件上的身份验证代码，最终代码如下所示:

```
package main

import (
    "flag"
    "net/http"
    "log"

    "github.com/gorilla/mux"
)

var (
    port = flag.String("port", "8080", "port")
)

func main() {
    flag.Parse()
    var router = mux.NewRouter()
    var api = router.PathPrefix("/api").Subrouter()
    api.NotFoundHandler = http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusNotFound)
    })
    api.Use(func(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            if r.Header.Get("x-auth-token") != "admin" {
                w.WriteHeader(http.StatusUnauthorized)
                return
            }
            log.Println(r.RequestURI)
            next.ServeHTTP(w, r)
        })
    })
    var api1 = api.PathPrefix("/v1").Subrouter()
    api1.HandleFunc("/status", func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusOK)
    })
    api1.NotFoundHandler = http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusForbidden)
    })
    var api2 = api.PathPrefix("/v2").Subrouter()
    api2.HandleFunc("/status", func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusAccepted)
    })
    api2.NotFoundHandler = http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(http.StatusNoContent)
    })
    http.ListenAndServe(":"+*port, router)
} 
```

最后试验

```
curl -I 'localhost:8080/api/v1/status' -H "x-auth-token: admin"
HTTP/1.1 200 OK

curl -I 'localhost:8080/api/v1/status' -H "x-auth-token: notadmin"
HTTP/1.1 401 Unauthorized 
```

耶，我们做到了。我们有带一些认证的版本化 API。参见 github 上的[项目](https://github.com/geosoft1/api1)。

享受吧。
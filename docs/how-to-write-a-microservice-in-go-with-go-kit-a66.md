# 如何在 Go with Go kit 中编写一个微服务

> 原文：<https://dev.to/napolux/how-to-write-a-microservice-in-go-with-go-kit-a66>

我在网上搜索了很多(我认为我的 google-fu 相当不错)关于用 Go with Go kit 编写简单的“RESTful”微服务的教程。

我找不到任何东西...

来自 Go 套件库的例子很好，但是文档很枯燥。

然后我决定买这本书，名为 [Go 编程蓝图，第二版](https://www.amazon.com/Programming-Blueprints-real-world-production-ready-cutting-edge/dp/1786468948)很好，**但是只有两章专门介绍 Go 套件**(一章用于微服务的实际开发，一章用于实际部署)，我现在真的不关心 [gRPC](https://grpc.io/) ，这本书第 10 章的例子也在实现中。太多的脚手架代码，如果你问我:P

Sooo，我决定回馈社区，写一篇教程，为了“边做边学”。本教程将受到上述书籍的极大启发，并可能在许多方面得到改进。

### 随时给个反馈！

**你会在我的博客**上找到微服务完整源代码的链接，这是本文的原始来源。coding.napolux.com

### 什么是 Go 套件？

从 [Go 套件](https://github.com/go-kit/kit) README.md:

> Go kit 是一个编程工具包，用于在 Go 中构建微服务(或优雅的单片)。我们解决分布式系统和应用程序架构中的常见问题，因此您可以专注于交付业务价值
> 
> [...]
> 
> Go 是一种很棒的通用语言，但是微服务需要一定量的专门支持。RPC 安全性、系统可观测性、基础设施集成，甚至程序设计——Go kit 填补了标准库留下的空白，让 Go 成为任何组织编写微服务的一流语言。

关于这个我不想讨论太多:**我太新了，去不了有强烈意见**。这个社区当然分为喜欢它的[和不喜欢它的](https://gist.github.com/posener/330c2b08aaefdea6f900ff0543773b2e)。[你也可以在这里找到一篇关于 Go 微服务框架差异的好文章](https://medium.com/seek-blog/microservices-in-go-2fc1570f6800)。

### 我们要做什么？

我们将创建一个非常基本的微服务，它将返回并验证一个日期...目标是了解 Go kit 如何工作，仅此而已。没有 Go kit 也可以轻松复制所有的逻辑，但我是来学习的，所以...

我希望你能一瞥，并为你的下一个项目有一个好的起点！

我们的微服务会有一些端点。

*   在`/status`的一个`GET`端点将返回一个简单的答案，确认微服务已经启动并正在运行
*   位于`/get`的一个`GET`端点将返回今天的日期
*   在`/validate`的一个`POST`端点将接收一个`dd/mm/yyyy`中的日期字符串(如果你问我，这是唯一存在的日期格式，以美国为例！)根据简单的正则表达式格式化并验证它...

**同样，你会在我的博客**上找到微服务完整源代码的链接，这是本文的原始来源。coding.napolux.com

开始吧！！！

### 先决条件

你应该已经在你的机器上安装了 [Golang](https://golang.org/doc/install) 和&。我发现[的官方下载包](https://golang.org/doc/install)运行得更好(我在 env 上有一些问题。var)比我的 Macbook 上的[自制软件安装](https://brew.sh/)要多。

另外，你应该知道 Go 语言，例如，我不打算解释什么是`struct`。

### 纳波塔微服务

好，让我们从在我们的`$GOPATH`文件夹中创建一个名为`napodate`的新文件夹开始。这也将是我们的包的名称。

在那里放一个`service.go`文件。让我们在文件的顶部添加我们的服务接口。

```
package napodate

import "context"

// Service provides some "date capabilities" to your application
type Service interface {
    Status(ctx context.Context) (string, error)
    Get(ctx context.Context) (string, error)
    Validate(ctx context.Context, date string) (bool, error)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们定义了服务的“蓝图”:**在 Go kit 中，您必须将服务建模为接口**。如上所述，我们将需要三个端点来映射到这个接口。

我们为什么要使用`context`包？阅读[https://blog.golang.org/context](https://blog.golang.org/context):

> 在 Google，我们开发了一个上下文包，使得跨越 API 边界将请求范围的值、取消信号和截止时间传递给处理请求所涉及的所有 goroutines 变得很容易

基本上，这是必要的，因为我们的微服务应该从一开始就处理并发请求，并且每个请求的上下文都是强制性的。

你不会想把东西弄混的。在教程的后面会有更多的介绍。我们现在用的不多，但是要习惯！:P 我们现在有了微服务接口。

### 实施我们的服务

您可能知道，没有实现，接口什么都不是，所以让我们实现我们的服务。让我们再给`service.go`添加一些代码。

```
type dateService struct{}

// NewService makes a new Service.
func NewService() Service {
    return dateService{}
}

// Status only tell us that our service is ok!
func (dateService) Status(ctx context.Context) (string, error) {
    return "ok", nil
}

// Get will return today's date
func (dateService) Get(ctx context.Context) (string, error) {
    now := time.Now()
    return now.Format("02/01/2006"), nil
}

// Validate will check if the date today's date
func (dateService) Validate(ctx context.Context, date string) (bool, error) {
    _, err := time.Parse("02/01/2006", date)
    if err != nil {
        return false, err
    }
    return true, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

新定义的类型`dateService`(一个空结构)是我们如何将服务的方法组合在一起，同时以某种方式将实现“隐藏”到世界的其他地方。

见`NewService()`作为我们“对象”的构造器。这就是我们将调用来获取服务实例的方法，同时像优秀程序员应该做的那样屏蔽内部逻辑。

### 让我们写一个测试

如何使用`NewService()`的一个很好的例子可以在我们服务的测试中看到。继续创建一个`service_test.go`文件。

```
package napodate

import (
    "context"
    "testing"
    "time"
)

func TestStatus(t *testing.T) {
    srv, ctx := setup()

    s, err := srv.Status(ctx)
    if err != nil {
        t.Errorf("Error: %s", err)
    }

    // testing status
    ok := s == "ok"
    if !ok {
        t.Errorf("expected service to be ok")
    }
}

func TestGet(t *testing.T) {
    srv, ctx := setup()
    d, err := srv.Get(ctx)
    if err != nil {
        t.Errorf("Error: %s", err)
    }

    time := time.Now()
    today := time.Format("02/01/2006")

    // testing today's date
    ok := today == d
    if !ok {
        t.Errorf("expected dates to be equal")
    }
}

func TestValidate(t *testing.T) {
    srv, ctx := setup()
    b, err := srv.Validate(ctx, "31/12/2019")
    if err != nil {
        t.Errorf("Error: %s", err)
    }

    // testing that the date is valid
    if !b {
        t.Errorf("date should be valid")
    }

    // testing an invalid date
    b, err = srv.Validate(ctx, "31/31/2019")
    if b {
        t.Errorf("date should be invalid")
    }

    // testing a USA date date
    b, err = srv.Validate(ctx, "12/31/2019")
    if b {
        t.Errorf("USA date should be invalid")
    }
}

func setup() (srv Service, ctx context.Context) {
    return NewService(), context.Background()
} 
```

Enter fullscreen mode Exit fullscreen mode

我让测试更具可读性，但是你真的应该用[子测试来编写它们，以获得一个更新的语法](https://blog.golang.org/subtests)。

测试是绿色的。)但是先关注一下`setup()`方法。对于每个测试，我们使用`NewService()`和上下文返回一个服务实例。

### 运输工具

我们的服务将使用 HTTP 公开。我们现在将对接受的 HTTP 请求和响应进行建模。在`service.go`的同一个文件夹中创建一个名为`transport.go`的文件。

```
package napodate

import (
    "context"
    "encoding/json"
    "net/http"
)

// In the first part of the file we are mapping requests and responses to their JSON payload.
type getRequest struct{}

type getResponse struct {
    Date string `json:"date"`
    Err  string `json:"err,omitempty"`
}

type validateRequest struct {
    Date string `json:"date"`
}

type validateResponse struct {
    Valid bool   `json:"valid"`
    Err   string `json:"err,omitempty"`
}

type statusRequest struct{}

type statusResponse struct {
    Status string `json:"status"`
}

// In the second part we will write "decoders" for our incoming requests
func decodeGetRequest(ctx context.Context, r *http.Request) (interface{}, error) {
    var req getRequest
    return req, nil
}

func decodeValidateRequest(ctx context.Context, r *http.Request) (interface{}, error) {
    var req validateRequest
    err := json.NewDecoder(r.Body).Decode(&req)
    if err != nil {
        return nil, err
    }
    return req, nil
}

func decodeStatusRequest(ctx context.Context, r *http.Request) (interface{}, error) {
    var req statusRequest
    return req, nil
}

// Last but not least, we have the encoder for the response output
func encodeResponse(ctx context.Context, w http.ResponseWriter, response interface{}) error {
    return json.NewEncoder(w).Encode(response)
} 
```

Enter fullscreen mode Exit fullscreen mode

**你会在我的博客**上找到微服务完整源代码的链接，这是本文的原始来源。coding.napolux.com

如果你问我，我会说这是一点代码，但是你会在回购文件的`transport.go`中找到注释，帮助你浏览它。

**在文件的第一部分**，我们将请求和响应映射到它们的 JSON 负载。对于`statusRequest`和`getRequest`,我们不需要太多，因为没有有效载荷被发送到服务器。对于`validateRequest`,我们将传递一个要验证的日期，所以这里是`date`字段。

回答也很简单。

**在第二部分**我们将为传入的请求编写“解码器”,告诉服务应该如何翻译请求并将它们映射到正确的请求结构。`get`和`status`是空的，我知道，但是它们在那里是为了完整。记住，**我正在边做边学**...

**最后但并非最不重要的**，我们有响应输出的编码器，这是一个简单的 JSON 编码器:给定一个对象，我们将从中返回一个 JSON 对象。

这就是传输，让我们创建我们的端点！

### 端点

让我们创建一个新文件`endpoint.go`。这个文件将包含我们的端点，这些端点将来自客户端的请求映射到我们的内部服务

```
package napodate

import (
    "context"
    "errors"

    "github.com/go-kit/kit/endpoint"
)

// Endpoints are exposed
type Endpoints struct {
    GetEndpoint      endpoint.Endpoint
    StatusEndpoint   endpoint.Endpoint
    ValidateEndpoint endpoint.Endpoint
}

// MakeGetEndpoint returns the response from our service "get"
func MakeGetEndpoint(srv Service) endpoint.Endpoint {
    return func(ctx context.Context, request interface{}) (interface{}, error) {
        _ = request.(getRequest) // we really just need the request, we don't use any value from it
        d, err := srv.Get(ctx)
        if err != nil {
            return getResponse{d, err.Error()}, nil
        }
        return getResponse{d, ""}, nil
    }
}

// MakeStatusEndpoint returns the response from our service "status"
func MakeStatusEndpoint(srv Service) endpoint.Endpoint {
    return func(ctx context.Context, request interface{}) (interface{}, error) {
        _ = request.(statusRequest) // we really just need the request, we don't use any value from it
        s, err := srv.Status(ctx)
        if err != nil {
            return statusResponse{s}, err
        }
        return statusResponse{s}, nil
    }
}

// MakeValidateEndpoint returns the response from our service "validate"
func MakeValidateEndpoint(srv Service) endpoint.Endpoint {
    return func(ctx context.Context, request interface{}) (interface{}, error) {
        req := request.(validateRequest)
        b, err := srv.Validate(ctx, req.Date)
        if err != nil {
            return validateResponse{b, err.Error()}, nil
        }
        return validateResponse{b, ""}, nil
    }
}

// Get endpoint mapping
func (e Endpoints) Get(ctx context.Context) (string, error) {
    req := getRequest{}
    resp, err := e.GetEndpoint(ctx, req)
    if err != nil {
        return "", err
    }
    getResp := resp.(getResponse)
    if getResp.Err != "" {
        return "", errors.New(getResp.Err)
    }
    return getResp.Date, nil
}

// Status endpoint mapping
func (e Endpoints) Status(ctx context.Context) (string, error) {
    req := statusRequest{}
    resp, err := e.StatusEndpoint(ctx, req)
    if err != nil {
        return "", err
    }
    statusResp := resp.(statusResponse)
    return statusResp.Status, nil
}

// Validate endpoint mapping
func (e Endpoints) Validate(ctx context.Context, date string) (bool, error) {
    req := validateRequest{Date: date}
    resp, err := e.ValidateEndpoint(ctx, req)
    if err != nil {
        return false, err
    }
    validateResp := resp.(validateResponse)
    if validateResp.Err != "" {
        return false, errors.New(validateResp.Err)
    }
    return validateResp.Valid, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们深入研究一下这个问题...为了将我们所有的服务方法`Get()`、`Status()`和`Validate()`作为端点公开，我们将编写处理传入请求的函数，调用相应的服务方法，并根据响应构建和返回适当的对象。

这些方法就是`Make...`方法。它们将接收 serviceas 参数，然后使用类型断言将请求类型“强制”为特定类型，并使用它来调用服务方法。

在这些将在`main.go`文件中使用的`Make...`方法之后，我们将编写符合服务接口
的端点

```
type Endpoints struct {
    GetEndpoint      endpoint.Endpoint
    StatusEndpoint   endpoint.Endpoint
    ValidateEndpoint endpoint.Endpoint
} 
```

Enter fullscreen mode Exit fullscreen mode

我们来举个例子:

```
// Status endpoint mapping
func (e Endpoints) Status(ctx context.Context) (string, error) {
    req := statusRequest{}
    resp, err := e.StatusEndpoint(ctx, req)
    if err != nil {
        return "", err
    }
    statusResp := resp.(statusResponse)
    return statusResp.Status, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法将允许我们使用端点作为 Go 方法。

### HTTP 服务器

对于我们的微服务，我们需要一个 HTTP 服务器。Go 在这方面很有帮助，但是我选择了[https://github.com/gorilla/mux](https://github.com/gorilla/mux)作为我们的路由，因为它的语法看起来非常干净简洁，所以让我们创建一个小可爱的 HTTP 服务器，映射到我们的端点。

在项目中创建一个名为`server.go`的新文件。

```
package napodate

import (
    "context"
    "net/http"

    httptransport "github.com/go-kit/kit/transport/http"
    "github.com/gorilla/mux"
)

// NewHTTPServer is a good little server
func NewHTTPServer(ctx context.Context, endpoints Endpoints) http.Handler {
    r := mux.NewRouter()
    r.Use(commonMiddleware) // @see https://stackoverflow.com/a/51456342

    r.Methods("GET").Path("/status").Handler(httptransport.NewServer(
        endpoints.StatusEndpoint,
        decodeStatusRequest,
        encodeResponse,
    ))

    r.Methods("GET").Path("/get").Handler(httptransport.NewServer(
        endpoints.GetEndpoint,
        decodeGetRequest,
        encodeResponse,
    ))

    r.Methods("POST").Path("/validate").Handler(httptransport.NewServer(
        endpoints.ValidateEndpoint,
        decodeValidateRequest,
        encodeResponse,
    ))

    return r
}

func commonMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.Header().Add("Content-Type", "application/json")
        next.ServeHTTP(w, r)
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

端点将从`main.go`文件传递到服务器，而`commonMiddleware()`，not exposed，将负责为每个响应添加特定的头。

### 最后，我们的 main.go 文件

让我们结束吧！我们有一个带端点的服务。我们有一个 HTTP 服务器，我们只需要一个可以打包所有东西的地方，当然这是我们的`main.go`文件。放入一个新文件夹，姑且称之为`cmd`。

```
package main

import (
    "context"
    "flag"
    "fmt"
    "log"
    "net/http"
    "os"
    "os/signal"
    "syscall"

    "napodate"
)

func main() {
    var (
        httpAddr = flag.String("http", ":8080", "http listen address")
    )
    flag.Parse()
    ctx := context.Background()
    // our napodate service
    srv := napodate.NewService()
    errChan := make(chan error)

    go func() {
        c := make(chan os.Signal, 1)
        signal.Notify(c, syscall.SIGINT, syscall.SIGTERM)
        errChan <- fmt.Errorf("%s", <-c)
    }()

    // mapping endpoints
    endpoints := napodate.Endpoints{
        GetEndpoint:      napodate.MakeGetEndpoint(srv),
        StatusEndpoint:   napodate.MakeStatusEndpoint(srv),
        ValidateEndpoint: napodate.MakeValidateEndpoint(srv),
    }

    // HTTP transport
    go func() {
        log.Println("napodate is listening on port:", *httpAddr)
        handler := napodate.NewHTTPServer(ctx, endpoints)
        errChan <- http.ListenAndServe(*httpAddr, handler)
    }()

    log.Fatalln(<-errChan)
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们一起来分析这个文件。我们声明`main`包并导入我们需要的东西。

[我们使用一个标志来使监听端口可配置](https://gobyexample.com/command-line-flags)，我们服务的默认端口将是经典的`8080`，但是由于这个标志，我们可以使用任何我们不使用的端口。

接下来是我们的服务器的设置:我们创建一个上下文(参见上文对上下文的解释)并获得我们的服务。[还设置了一个错误通道](https://gobyexample.com/channels)。

> 通道是连接并发 goroutines 的管道。您可以从一个 goroutine 向通道发送值，并在另一个 goroutine 中接收这些值。

**然后我们创建两个 go routine**。一个用于在我们按下`CTRL+C`时停止服务器，另一个将实际监听传入的请求。

看看`handler := napodate.NewHTTPServer(ctx, endpoints)`这个处理程序将映射我们的服务端点(还记得上面的`Make...`方法吗？)并返回正确答案。

你以前在哪里看过`NewHTTPServer()`？

一旦通道收到错误消息，服务器将停止并死亡。

### 咱们该服务了！

如果你做了所有正确的事情，运行

```
go run cmd/main.go 
```

Enter fullscreen mode Exit fullscreen mode

从你的项目文件夹，你应该可以卷曲你的微服务！

```
curl http://localhost:8080/get
{"date":"14/04/2019"}

curl http://localhost:8080/status
{"status":"ok"}

curl -XPOST -d '{"date":"32/12/2020"}' http://localhost:8080/validate
{"valid":false,"err":"parsing time \"32/12/2020\": day out of range"}

curl -XPOST -d '{"date":"12/12/2021"}' http://localhost:8080/validate
{"valid":true} 
```

Enter fullscreen mode Exit fullscreen mode

### 包装完毕

我们从零开始创建了一个新的微服务，即使它超级简单，这也是一个很好的开始使用 Go 编程语言 Go kit 的方法。

希望你和我一样喜欢这个教程！

**你会在我的博客**上找到微服务完整源代码的链接，这是本文的原始来源。coding.napolux.com
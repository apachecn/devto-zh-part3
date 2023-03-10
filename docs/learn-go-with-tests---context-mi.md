# 通过测试学习-上下文

> 原文：<https://dev.to/quii/learn-go-with-tests---context-mi>

这是一篇来自 WIP 项目的文章，名为 [Learn Go with tests](https://github.com/quii/learn-go-with-tests) ，其目的是熟悉 Go 并学习 TDD 的相关技术

软件通常会启动长时间运行的资源密集型进程(通常在 goroutines 中)。如果导致这种情况的操作由于某种原因被取消或失败，您需要通过应用程序以一致的方式停止这些进程。

如果你不能做到这一点，你引以为豪的敏捷的 Go 应用程序可能会出现难以调试的性能问题。

在本章中，我们将使用包`context`来帮助我们管理长时间运行的流程。

我们将从一个 web 服务器的经典示例开始，当 hit 启动一个潜在的长时间运行的进程，以获取一些数据并在响应中返回。

我们将练习一个场景，在这个场景中，用户在数据被检索之前取消了请求，我们将确保该进程被告知放弃。

我在快乐之路上设置了一些代码让我们开始。这是我们的服务器代码。

```
func NewHandler(store Store) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprint(w, store.Fetch())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

函数`NewHandler`接受一个`Store`并返回给我们一个`http.HandlerFunc`。门店定义为:

```
type Store interface {
    Fetch() string
} 
```

Enter fullscreen mode Exit fullscreen mode

返回的函数调用`store`的`Fetch`方法获取数据并将其写入响应。

我们在测试中使用了一个对应于`Store`的存根。

```
type StubStore struct {
    response string
}

func (s *StubStore) Fetch() string {
    return s.response
}

func TestHandler(t *testing.T) {
    data := "hello, world"
    svr := NewHandler(&StubStore{data})

    request := httptest.NewRequest(http.MethodGet, "/", nil)
    response := httptest.NewRecorder()

    svr.ServeHTTP(response, request)

    if response.Body.String() != data {
        t.Errorf(`got "%s", want "%s"`, response.Body.String(), data)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个愉快的路径，我们想要制作一个更真实的场景，在用户取消请求之前,`Store`不能完成一个`Fetch`。

## 先写测试

我们的处理程序将需要一种方式来告诉`Store`取消工作，所以更新接口。

```
type Store interface {
    Fetch() string
    Cancel()
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将需要调整我们的间谍，所以它需要一些时间来返回`data`，并知道它已经被告知取消。我们也将它重命名为`SpyStore`，因为我们现在正在观察它的调用方式。它必须添加`Cancel`作为实现`Store`接口的方法。

```
type SpyStore struct {
    response string
    cancelled bool
}

func (s *SpyStore) Fetch() string {
    time.Sleep(100 * time.Millisecond)
    return s.response
}

func (s *SpyStore) Cancel() {
    s.cancelled = true
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们添加一个新的测试，在 100 毫秒之前取消请求，并检查存储以查看它是否被取消。

```
t.Run("tells store to cancel work if request is cancelled", func(t *testing.T) {
      store := &SpyStore{response: data}
      svr := Server(store)

      request := httptest.NewRequest(http.MethodGet, "/", nil)

      cancellingCtx, cancel := context.WithCancel(request.Context())
      time.AfterFunc(5 * time.Millisecond, cancel)
      request = request.WithContext(cancellingCtx)

      response := httptest.NewRecorder()

      svr.ServeHTTP(response, request)

      if !store.cancelled {
          t.Errorf("store was not told to cancel")
      }
  }) 
```

Enter fullscreen mode Exit fullscreen mode

又是谷歌博客上的

> 上下文包提供了从现有的上下文值导出新的上下文值的函数。这些值形成一棵树:当一个上下文被取消时，从它派生的所有上下文也被取消。

重要的是，要派生出上下文，以便对于给定的请求，取消可以在整个调用栈中传播。

我们所做的是从返回给我们一个`cancel`函数的`request`中派生出一个新的`cancellingCtx`。然后，我们通过使用`time.AfterFunc`调度该函数在 5 毫秒内被调用。最后，我们通过调用`request.WithContext`在请求中使用这个新的上下文。

## 试运行测试

正如我们所料，测试失败了。

```
-------- FAIL: TestServer (0.00s)
    --- FAIL: TestServer/tells_store_to_cancel_work_if_request_is_cancelled (0.00s)
        context_test.go:62: store was not told to cancel 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

记住要遵守 TDD。编写*最少*的代码来通过我们的测试。

```
func Server(store Store) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        store.Cancel()
        fmt.Fprint(w, store.Fetch())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得这个测试通过，但感觉不好，不是吗！我们当然不应该在接受的每个请求之前取消`Store`。

通过训练，它突出了我们测试中的一个缺陷，这是一件好事！

我们需要更新我们的快乐路径测试，以保证它不会被取消。

```
t.Run("returns data from store", func(t *testing.T) {
    store := SpyStore{response: data}
    svr := Server(&store)

    request := httptest.NewRequest(http.MethodGet, "/", nil)
    response := httptest.NewRecorder()

    svr.ServeHTTP(response, request)

    if response.Body.String() != data {
        t.Errorf(`got "%s", want "%s"`, response.Body.String(), data)
    }

    if store.cancelled {
        t.Error("it should not have cancelled the store")
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

运行两个测试，快乐路径测试现在应该失败了，现在我们被迫做一个更明智的实现。

```
func Server(store Store) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        ctx := r.Context()

        data := make(chan string, 1)

        go func() {
            data <- store.Fetch()
        }()

        select {
        case d := <-data:
            fmt.Fprint(w, d)
        case <-ctx.Done():
            store.Cancel()
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做了什么？

`context`有一个方法`Done()`，当上下文为“完成”或“取消”时，该方法返回一个发送信号的通道。我们想听那个信号，如果我们收到它就呼叫`store.Cancel`，但是如果我们的`Store`设法在它之前呼叫`Fetch`，我们就想忽略它。

为了管理它，我们在一个 goroutine 中运行`Fetch`，它会将结果写入一个新的通道`data`。然后，我们使用`select`有效地争用两个异步流程，然后我们或者写一个响应或者写`Cancel`。

## 重构

我们可以通过在我们的 spy
上创建断言方法来稍微重构我们的测试代码

```
func (s *SpyStore) assertWasCancelled() {
    s.t.Helper()
    if !s.cancelled {
        s.t.Errorf("store was not told to cancel")
    }
}

func (s *SpyStore) assertWasNotCancelled() {
    s.t.Helper()
    if s.cancelled {
        s.t.Errorf("store was told to cancel")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

创建间谍时记得传入`*testing.T`。

```
func TestServer(t *testing.T) {
    data := "hello, world"

    t.Run("returns data from store", func(t *testing.T) {
        store := &SpyStore{response: data, t: t}
        svr := Server(store)

        request := httptest.NewRequest(http.MethodGet, "/", nil)
        response := httptest.NewRecorder()

        svr.ServeHTTP(response, request)

        if response.Body.String() != data {
            t.Errorf(`got "%s", want "%s"`, response.Body.String(), data)
        }

        store.assertWasNotCancelled()
    })

    t.Run("tells store to cancel work if request is cancelled", func(t *testing.T) {
        store := &SpyStore{response: data, t: t}
        svr := Server(store)

        request := httptest.NewRequest(http.MethodGet, "/", nil)

        cancellingCtx, cancel := context.WithCancel(request.Context())
        time.AfterFunc(5*time.Millisecond, cancel)
        request = request.WithContext(cancellingCtx)

        response := httptest.NewRecorder()

        svr.ServeHTTP(response, request)

        store.assertWasCancelled()
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法可以，但是它是惯用的吗？

我们的 web 服务器关心手动取消`Store`有意义吗？如果`Store`恰好也依赖于其他运行缓慢的进程呢？我们必须确保`Store.Cancel`正确地将取消消息传播给它的所有依赖者。

`context`的要点之一是它是一种提供取消的一致方式。

[从 go 文档](https://golang.org/pkg/context/)

> 对服务器的传入请求应该创建上下文，对服务器的传出调用应该接受上下文。它们之间的函数调用链必须传播上下文，可以选择用使用 WithCancel、WithDeadline、WithTimeout 或 WithValue 创建的派生上下文替换它。当一个上下文被取消时，从它派生的所有上下文也被取消。

又是来自谷歌博客:

> 在 Google，我们要求 Go 程序员将上下文参数作为第一个参数传递给传入和传出请求之间的调用路径上的每个函数。这使得许多不同团队开发的 Go 代码能够很好地互操作。它提供了对超时和取消的简单控制，并确保像安全凭证这样的关键值正确地传输 Go 程序。

(暂停一会儿，想想每一个必须在上下文中发送的功能的结果，以及它的人类工程学。)

感觉有点不安？很好。让我们试着遵循这种方法，而不是通过`context`传递给我们的`Store`，让它负责。这样，它也可以将`context`传递给它的家属，他们也可以负责阻止自己。

## 先写测试

我们将不得不改变我们现有的测试，因为它们的职责正在改变。我们的处理程序现在唯一负责的事情是确保它发送一个上下文到下游的`Store`，并且当它被取消时，它处理来自`Store`的错误。

让我们更新我们的`Store`界面来显示新的职责。

```
type Store interface {
    Fetch(ctx context.Context) (string, error)
} 
```

Enter fullscreen mode Exit fullscreen mode

暂时删除我们的处理程序中的代码

```
func Server(store Store) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

更新我们的`SpyStore`

```
type SpyStore struct {
    response string
    t        *testing.T
}

func (s *SpyStore) Fetch(ctx context.Context) (string, error) {
    data := make(chan string, 1)

    go func() {
        var result string
        for _, c := range s.response {
            select {
            case <-ctx.Done():
                s.t.Log("spy store got cancelled")
                return
            default:
                time.Sleep(10 * time.Millisecond)
                result += string(c)
            }
        }
        data <- result
    }()

    select {
    case <-ctx.Done():
        return "", ctx.Err()
    case res := <-data:
        return res, nil
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们必须让我们的间谍像一个真正的方法一样工作。

我们模拟了一个缓慢的过程，在这个过程中，我们通过在 goroutine 中一个字符一个字符地追加字符串来缓慢地构建结果。当 goroutine 完成其工作时，它将字符串写入`data`通道。goroutine 监听`ctx.Done`，如果在该通道中发送信号，它将停止工作。

最后，代码使用另一个`select`来等待 goroutine 完成它的工作或者等待取消的发生。

这与我们之前的方法类似，我们使用 Go 的并发原语让两个异步进程相互竞争，以确定我们返回的内容。

在编写自己的接受`context`的函数和方法时，您将采用类似的方法，因此请确保您理解正在发生的事情。

我们从`SpyStore`的字段中删除了对`ctx`的引用，因为我们对它不再感兴趣。我们现在严格测试行为，这比测试实现细节更好，比如“你通过一个特定的值来运行`foo`”。

最后，我们可以更新我们的测试。注释掉我们的取消测试，这样我们可以先修复快乐路径测试。

```
t.Run("returns data from store", func(t *testing.T) {
    store := &SpyStore{response: data, t: t}
    svr := Server(store)

    request := httptest.NewRequest(http.MethodGet, "/", nil)
    response := httptest.NewRecorder()

    svr.ServeHTTP(response, request)

    if response.Body.String() != data {
        t.Errorf(`got "%s", want "%s"`, response.Body.String(), data)
    }

    if store.ctx != request.Context() {
        t.Errorf("store was not passed through a context %v", store.ctx)
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestServer/returns_data_from_store
-------- FAIL: TestServer (0.00s)
    --- FAIL: TestServer/returns_data_from_store (0.00s)
        context_test.go:22: got "", want "hello, world"
        context_test.go:26: store was not passed through a context <nil> 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

```
func Server(store Store) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        data, _ := store.Fetch(r.Context())
        fmt.Fprint(w, data)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的幸福之路应该是...开心。现在我们可以修复另一个测试。

## 先写测试

我们需要测试我们没有在错误案例上写任何类型的响应。遗憾的是，我们没有办法解决这个问题，所以我们必须扮演我们自己的间谍来测试这个问题。

```
type SpyResponseWriter struct {
    written bool
}

func (s *SpyResponseWriter) Header() http.Header {
    s.written = true
    return nil
}

func (s *SpyResponseWriter) Write([]byte) (int, error) {
    s.written = true
    return 0, errors.New("not implemented")
}

func (s *SpyResponseWriter) WriteHeader(statusCode int) {
    s.written = true
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`SpyResponseWriter`实现了`http.ResponseWriter`，所以我们可以在测试中使用它。

```
t.Run("tells store to cancel work if request is cancelled", func(t *testing.T) {
    store := &SpyStore{response: data, t: t}
    svr := Server(store)

    request := httptest.NewRequest(http.MethodGet, "/", nil)

    cancellingCtx, cancel := context.WithCancel(request.Context())
    time.AfterFunc(5*time.Millisecond, cancel)
    request = request.WithContext(cancellingCtx)

    response := &SpyResponseWriter{}

    svr.ServeHTTP(response, request)

    if response.written {
        t.Error("a response should not have been written")
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行测试

```
=== RUN   TestServer
=== RUN   TestServer/tells_store_to_cancel_work_if_request_is_cancelled
-------- FAIL: TestServer (0.01s)
    --- FAIL: TestServer/tells_store_to_cancel_work_if_request_is_cancelled (0.01s)
        context_test.go:47: a response should not have been written 
```

Enter fullscreen mode Exit fullscreen mode

## 写足够的代码让它通过

```
func Server(store Store) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        data, err := store.Fetch(r.Context())

        if err != nil {
            return // todo: log error however you like
        }

        fmt.Fprint(w, data)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在此之后，我们可以看到服务器代码已经简化，因为它不再明确负责取消，它只是通过`context`并依赖下游函数来处理可能发生的任何取消。

## 包装完毕

### 我们已经讲过了什么

*   如何测试请求被客户端取消的 HTTP 处理程序。
*   如何使用上下文来管理取消？
*   如何通过使用 goroutines、`select`和 channels 来编写一个接受`context`并用它来取消自身的函数。
*   遵循 Google 关于如何通过调用栈传播请求范围的上下文来管理取消的指导方针。
*   如何在需要的情况下为`http.ResponseWriter`滚动自己的间谍？

### 语境呢。价值？

米哈尔·施特尔巴和我有相似的观点。

> 如果你用 ctx。价值在我的(不存在的)公司，你被解雇了

一些工程师提倡通过`context`传递值，因为*感觉方便*。

便利常常是糟糕代码的原因。

`context.Values`的问题是它只是一个非类型化的映射，所以你没有类型安全，你必须处理它不包含你的值。你必须创建一个从一个模块到另一个模块的映射键的耦合，如果有人改变了什么，事情就开始变坏。

简而言之，**如果一个函数需要一些值，将它们作为类型化参数，而不是试图从`context.Value`** 中获取它们。这使得被静态检查和记录的每个人都可以看到。

#### 但是...

另一方面，在上下文中包含与请求正交的信息，如跟踪 id，会很有帮助。潜在地，调用栈中的每个函数都不需要这些信息，这会使函数签名非常混乱。

[Jack Lindamood 说**上下文。值应该通知，而不是控制**](https://medium.com/@cep21/how-to-correctly-use-context-context-in-go-1-7-8f2c0fafdf39)

> 语境的内容。价值是给维护者的，不是给用户的。它不应成为记录的或预期的结果的必需输入。

### 附加材料

*   我真的很喜欢读 Michal strba 的[Context should Go 2。他的观点是，必须到处传递`context`是一种气味，这表明语言在取消方面存在缺陷。他说，如果这个问题能在语言层面而不是在图书馆层面得到解决，那会更好。在此之前，如果您想管理长时间运行的流程，您将需要`context`。](https://faiface.github.io/post/context-should-go-away-go2/)
*   [Go 博客进一步描述了与`context`合作的动机，并提供了一些例子](https://blog.golang.org/context)
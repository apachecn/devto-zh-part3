# 深入探究长生不老药的功效

> 原文：<https://dev.to/fteem/a-deeper-dive-in-elixirs-plug-2dk8>

*本帖原载于[我的博客](https://ieftimov.com)2018 年[12 月 31 日](https://ieftimov.com/a-deeper-dive-in-elixir-plug)。*

作为 Elixir 和 Phoenix 的新手，我花了相当多的时间在项目文档上。最近让我印象深刻的一件事是 [Phoenix 的插件文档](https://hexdocs.pm/phoenix/plug.html#content)的第一句话:

> Plug 位于 Phoenix 的 HTTP 层的核心，Phoenix 将 Plug 放在前端和中心。

因此，很自然地，我觉得有必要更深入地研究 Plug 并更好地理解它。我希望下面的文章能帮助你了解 Plug。

## 什么是插头？

[![](img/67e32c8eb4c136244d7b70fc02e1575c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gjaHzbBb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wc1em7dxm4kmw7zq7efb.jpg)

正如[自述](https://github.com/elixir-plug/plug#plug)所言，塞里蒙是:

1.  web 应用程序间可组合模块的规范
2.  Erlang VM 中不同 web 服务器的连接适配器

但是，这意味着什么呢？嗯，它基本上说明了 1)Plug*定义了在 Elixir 中构建 web 应用程序的方式*以及 2)它为您提供了编写 web 服务器能够理解的应用程序的工具。

让我们来看看这意味着什么。

## Web 服务器，yeehaw！

Erlang 最流行的 HTTP 服务器之一是 [Cowboy](https://github.com/ninenines/cowboy) 。这是一个小型、快速、现代的 Erlang/OTP HTTP 服务器。如果你用 Elixir 编写任何 web 应用程序，它都可以在 Cowboy 上运行，因为 Elixir 核心团队已经为 Cowboy 构建了一个插件适配器，方便地命名为 [plug_cowboy](https://github.com/elixir-plug/plug_cowboy) 。

这意味着如果您将这个包包含在您的包中，您将获得与 Cowboy web 服务器对话的 Elixir 接口(反之亦然)。这意味着你可以发送和接收请求以及其他 web 服务器可以做的事情。

那么为什么这很重要呢？

为了理解 Plug，我们需要了解它是如何工作的。基本上，使用适配器(`plug_cowboy`)，Plug 可以接受 Cowboy 中的连接请求，并将其转换为有意义的结构，也称为`Plug.Conn`。

这意味着 Plug 使用`plug_cowboy`来理解 Cowboy 的本质细节。通过这样做，Plug 允许我们轻松地构建能够接收、处理和响应请求的处理函数和模块。

当然，Plug 背后的想法并不是只与 Cowboy 合作。如果你看一下 José Valim (Elixir 的 BDFL)的回答,他清楚地指出“Plug 是不同网络服务器的通用适配器。目前我们只支持牛仔，但也有支持其他人的工作。”

## 输入插头

好了，现在我们已经了解了 Cowboy 和它的插头适配器，让我们看看插头本身。

如果你看一下 [Plug 的自述文件](https://github.com/elixir-plug/plug/blob/master/README.md)，你会注意到有两种风格的 Plug，一个函数或者一个模块。

最简单的插件可以是一个函数，它只需要一个`Plug.Conn`结构(我们将在后面详细介绍)和一些选项。该函数将操作该结构并在最后返回它。这是来自`README` :
的例子

```
def hello_world_plug(conn, _opts) do
  conn
  |> put_resp_content_type("text/plain")
  |> send_resp(200, "Hello world")
end 
```

Enter fullscreen mode Exit fullscreen mode

<small>代码公然抄袭 [Plug 的文档](https://github.com/elixir-plug/plug/blob/master/README.md)。</small>

如果看功能的话，还挺简单的。它接收连接结构，将其内容类型放入`text/plain`中，并返回一个带有 HTTP 200 状态和主体为`"Hello world"`的响应。

第二种味道是模块插头。这意味着，您可以定义一个模块，它接受一个连接和初始化选项，并返回连接:

```
defmodule MyPlug do
  def init([]), do: false
  def call(conn, _opts), do: conn
end 
```

Enter fullscreen mode Exit fullscreen mode

<small>代码公然抄袭 [Plug 的文档](https://github.com/elixir-plug/plug/blob/master/README.md)。</small>

考虑到这一点，让我们更进一步，看看如何在一个微小的应用程序中使用 Plug。

## 插一个插头作为端点

到目前为止，我们讨论的最重要的事情是什么是插头，以及它在高层次上的用途。我们还看了两种不同类型的插头。

现在，让我们看看如何在 Cowboy 服务器上安装一个插件，并将它作为一个端点:

```
defmodule PlugTest do
  import Plug.Conn

  def init(options) do
    # initialize options

    options
  end

  def call(conn, _opts) do
    conn
    |> put_resp_content_type("text/plain")
    |> send_resp(200, "Hello world")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个模块将做的是，当安装在一个牛仔服务器上时，将把`Content-Type`头设置为`text/plain`，并将返回一个带有主体`Hello world`的 HTTP 200。

让我们启动 IEx，自己测试一下:

```
› iex -S mix
Erlang/OTP 21 [erts-10.2] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1] [hipe] [dtrace]

Interactive Elixir (1.7.4) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> {:ok, _ } = Plug.Cowboy.http PlugTest, [], port: 3000
{:ok, #PID<0.202.0>} 
```

Enter fullscreen mode Exit fullscreen mode

这将启动牛仔服务器作为一个波束进程，监听端口 3000。如果我们`cURL`它，我们将看到响应体和它的头:

```
› curl -v 127.0.0.1:3000
> GET / HTTP/1.1
> Host: 127.0.0.1:3000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< cache-control: max-age=0, private, must-revalidate
< content-length: 11
< content-type: text/plain; charset=utf-8
< date: Tue, 25 Dec 2018 22:54:54 GMT
< server: Cowboy
<
* Connection #0 to host 127.0.0.1 left intact
Hello world 
```

Enter fullscreen mode Exit fullscreen mode

你看，响应的`Content-Type`被设置为`text/plain`，主体为`Hello world`。在这个例子中，插件本身本质上是一个端点，为我们的`cURL`命令(或浏览器)提供纯文本。在这一点上，您可以想象，您可以在牛仔服务器上插入更复杂的插件，它会很好地为它们服务。

要关闭端点，您只需:

```
iex(2)> Plug.Cowboy.shutdown PlugTest.HTTP
:ok 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里看到的可能是用 Elixir 编写的最小的 web 应用程序。这是一个应用程序，它接受请求并通过 HTTP 返回一个有效的响应，包括状态和主体。

那么，这到底是怎么回事呢？我们如何接受请求并在这里构建响应？

## 潜入`Plug.Conn`

为了理解这一点，我们需要放大我们的模块`PlugTest`的`call/2`函数。我还会在函数的末尾加入一个`IO.inspect`，这样我们就可以检查这个结构是什么:

```
def call(conn, _opts) do
  conn
  |> put_resp_content_type("text/plain")
  |> send_resp(200, "Hello world")
  |> IO.inspect
end 
```

Enter fullscreen mode Exit fullscreen mode

如果您通过 IEx 会话再次启动 Cowboy 实例，并通过`cURL`(或浏览器)点击`127.0.0.1:3000`，您应该在 IEx 会话中看到类似这样的内容:

```
%Plug.Conn{
  adapter: {Plug.Cowboy.Conn, :...},
  assigns: %{},
  before_send: [],
  body_params: %Plug.Conn.Unfetched{aspect: :body_params},
  cookies: %Plug.Conn.Unfetched{aspect: :cookies},
  halted: false,
  host: "127.0.0.1",
  method: "GET",
  owner: #PID<0.316.0>,
  params: %Plug.Conn.Unfetched{aspect: :params},
  path_info: [],
  path_params: %{},
  port: 3000,
  private: %{},
  query_params: %Plug.Conn.Unfetched{aspect: :query_params},
  query_string: "",
  remote_ip: {127, 0, 0, 1},
  req_cookies: %Plug.Conn.Unfetched{aspect: :cookies},
  req_headers: [
    {"accept",
     "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8"},
    {"accept-encoding", "gzip, deflate, br"},
    {"accept-language", "en-US,en;q=0.9"},
    {"connection", "keep-alive"},
    {"host", "127.0.0.1:3000"},
    {"upgrade-insecure-requests", "1"},
    {"user-agent",
     "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.98 Safari/537.36"}
  ],
  request_path: "/",
  resp_body: nil,
  resp_cookies: %{},
  resp_headers: [
    {"cache-control", "max-age=0, private, must-revalidate"},
    {"content-type", "text/plain; charset=utf-8"}
  ],
  scheme: :http,
  script_name: [],
  secret_key_base: nil,
  state: :sent,
  status: 200
} 
```

Enter fullscreen mode Exit fullscreen mode

我们到底在看什么？它实际上是一个连接的插头表示。这是底层 web 服务器和牛仔服务器收到的请求的直接接口。

这个结构的一些属性是不言自明的，比如`scheme`、`method`、`host`、`request_path`等等。如果你想详细了解这些字段是什么，我建议你看一下`Plug.Conn`的[文档](https://hexdocs.pm/plug/Plug.Conn.html#module-request-fields)。

但是，为了更好地理解`Plug.Conn`结构，我们需要理解每个连接结构的连接生命周期。

### 连接生命周期

就像仙丹`Plug.Conn`中的任何地图一样，我们可以在上面进行模式匹配。让我们修改之前创建的小端点，并尝试添加一些额外的`IO.inspect`函数调用:

```
defmodule PlugTest do
  import Plug.Conn

  def init(options) do
    # initialize options

    options
  end

  def call(conn, _opts) do
    conn
    |> inspect_state
    |> put_resp_content_type("text/plain")
    |> inspect_state
    |> put_private(:foo, :bar)
    |> inspect_state
    |> resp(200, "Hello world")
    |> inspect_state
    |> send_resp()
    |> inspect_state
  end

  defp inspect_state(conn = %{state: state}) do
    IO.inspect state
    conn
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

因为`Plug.Conn`允许模式匹配，所以我们可以获得连接的`state`，将其打印出来并返回连接本身，这样`call/2`函数中的管道就会像预期的那样继续工作。

让我们将这个插件安装到一个 Cowboy 实例上，并用一个简单的`cURL`请求点击它:

```
iex(6)> Plug.Cowboy.http PlugTest, [], port: 3000
{:ok, #PID<0.453.0>}

# curl 127.0.0.1:3000

iex(21)> :unset
:unset
:unset
:set
:sent 
```

Enter fullscreen mode Exit fullscreen mode

你看，当连接进入插头时，它的状态从`:unset`变为`:set`，最后变为`:sent`。这意味着一旦调用了插头，连接的状态就是`:unset`。然后我们做多个动作，或者换句话说，我们调用`Plug.Conn`上的多个函数，这些函数向连接添加更多信息。显然，由于 Elixir 中的所有变量都是不可变的，所以每个函数都返回一个新的`Plug.Conn`实例，而不是改变现有的实例。

一旦设置了主体和连接的状态，状态就会变为`:set`。直到那一刻，状态被固定为`:unset`。一旦我们将响应发送回客户端，状态就会变为`:sent`。

这里我们需要理解的是，无论我们在一个管道中有一个或多个插头，它们都将接收一个`Plug.Conn`，对其调用函数，无论是提取数据还是向其添加数据，然后连接将被传递给下一个插头。最终，在管道中，将有一个插件(以端点或 Phoenix 控制器的形式)来设置主体和响应状态，并将响应发送回客户端。

这里有更多的细节，但这足以概括我们对`Plug`和`Plug.Conn`的理解。

## 下一级`Plug`使用`Plug.Router`生成

现在我们已经了解了`Plug.Conn`是如何工作的，以及插头如何通过调用`Plug.Conn`模块中定义的函数来改变连接，让我们来看看插头的一个更高级的特性——将插头变成路由器。

在我们的第一个例子中，我们看到了最简单的 Elixir web 应用程序——一个简单的插件，它接受请求并返回一个简单的响应，其中包含一个文本体和一个 HTTP 200。但是，如果我们想要处理不同的路由或 HTTP 方法呢？如果我们想用 HTTP 404 优雅地处理对未知路由的任何请求呢？

`Plug`附带的一个好处是一个叫做`Plug.Router`的模块，你可以在这里看到它的文档[。路由器模块包含一个 DSL，它允许我们为传入的请求定义路由算法，并为路由编写处理程序(由 Plug 提供支持)。如果你来自 Ruby land，而`Plug`基本上是 Rack，这个 DSL 就是 Sinatra.rb](https://hexdocs.pm/plug/Plug.Router.html#content)

让我们使用`Plug.Router`创建一个微型路由器，给它的管道和一些端点添加一些插头。

**快速旁白:什么是管道？**

尽管它与管道操作符(`|>`)同名，但是在 Plug 的上下文中，管道是一个接一个执行的 Plug 的列表。真的是这样。管道中最后一个插件通常是一个端点，它将设置响应的主体和状态，并将响应返回给客户端。

现在，回到我们的路由器:

```
defmodule MyRouter do
  use Plug.Router

  plug :match
  plug :dispatch

  get "/hello" do
    send_resp(conn, 200, "world")
  end

  match _ do
    send_resp(conn, 404, "oops")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

<small>代码公然抄袭 [`Plug.Router`的文档](https://hexdocs.pm/plug/Plug.Router.html)。</small>

您将注意到的第一件事是，所有路由器也是模块。通过`use`调用`Plug.Router`模块，我们包含了一些让我们的生活更简单的功能，比如`get`或`match`。

如果你注意到在模块的顶部有两行:

```
plug :match
plug :dispatch 
```

Enter fullscreen mode Exit fullscreen mode

这是路由器的*流水线*。所有到达路由器的请求都将通过这两个插头:`match`和`dispatch`。第一个用于匹配我们定义的路线(如`/hello`)，而另一个将调用为特定路线定义的功能。这意味着，如果我们想要添加其他插件，大多数情况下它们将在两个强制插件(`match`和`dispatch`)之间被调用。

让我们将路由器安装在牛仔服务器上，看看它的行为:

```
iex(29)> Plug.Cowboy.http MyRouter, [], port: 3000
{:ok, #PID<0.1500.0>} 
```

Enter fullscreen mode Exit fullscreen mode

当我们点击`127.0.0.1:3000/hello`时，我们将得到以下内容:

```
› curl -v 127.0.0.1:3000/hello
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 3000 (#0)
> GET /hello HTTP/1.1
> Host: 127.0.0.1:3000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< cache-control: max-age=0, private, must-revalidate
< content-length: 5
< date: Thu, 27 Dec 2018 22:50:47 GMT
< server: Cowboy
<
* Connection #0 to host 127.0.0.1 left intact
world 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们收到了作为响应体的`world`和一个 HTTP 200。但是如果我们点击任何其他 URL，路由器将匹配其他路由:

```
› curl -v 127.0.0.1:3000/foo
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 3000 (#0)
> GET /foo HTTP/1.1
> Host: 127.0.0.1:3000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 404 Not Found
< cache-control: max-age=0, private, must-revalidate
< content-length: 4
< date: Thu, 27 Dec 2018 22:51:56 GMT
< server: Cowboy
<
* Connection #0 to host 127.0.0.1 left intact
oops 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，因为`/hello`路由不匹配，我们默认使用另一个路由，也称为“catch all”路由，它返回`oops`作为响应体和一个 HTTP 404 状态。

如果你想了解更多关于`Plug.Router`和它的路线匹配宏，你可以在[的文档](https://hexdocs.pm/plug/Plug.Router.html#module-routes)中读到更多。我们还需要用插头覆盖更多的距离。

## 内置插头

在上一节中，我们提到了插头`match`和`dispatch`，以及插头管道。我们还提到，我们可以在管道中插入其他插头，这样我们就可以检查或更改每个请求的`Plug.Conn`。

令人兴奋的是`Plug`还带有内置插头。这意味着您可以在任何基于插件的应用程序中插入一系列插件:

*   `Plug.CSRFProtection`
*   `Plug.Head`
*   `Plug.Logger`
*   `Plug.MethodOverride`
*   `Plug.Parsers`
*   `Plug.RequestId`
*   `Plug.SSL`
*   `Plug.Session`
*   `Plug.Static`

让我们试着理解其中一些是如何工作的，以及如何将它们插入到我们的`MyRouter`路由器模块中。

### `Plug.Head`

这是一个相当简单的插头。非常简单，我将把[的所有代码](https://github.com/elixir-plug/plug/blob/v1.7.1/lib/plug/head.ex#L1)添加到这里:

```
defmodule Plug.Head do
  @behaviour Plug

  alias Plug.Conn

  def init([]), do: []

  def call(%Conn{method: "HEAD"} = conn, []), do: %{conn | method: "GET"}
  def call(conn, []), do: conn
end 
```

Enter fullscreen mode Exit fullscreen mode

这个插件的作用是将任何 HTTP `HEAD`请求转换成一个`GET`请求。仅此而已。它的`call`函数接收一个`Plug.Conn`，只匹配那些有`method: "HEAD"`的，并返回一个新的`Plug.Conn`，其中`method`变为`"GET"`。

如果你想知道`HEAD`方法是干什么的，这是来自 [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) :

> HEAD 方法与 GET 方法相同，只是服务器不能在响应中返回
> 消息体。响应 HEAD 请求的 HTTP 头
> 中包含的元信息应该与响应 GET 请求的
> 中发送的信息相同。这个方法可以用来获得请求所隐含的实体的元信息，而不需要传送实体本身。这种方法通常用于测试超文本链接
> 的有效性、可访问性和最近的修改。

让我们把这个插头插在我们的`Plug.Router`(完全是双关语):

```
defmodule MyRouter do
  use Plug.Router

  plug Plug.Head
  plug :match
  plug :dispatch

  get "/hello" do
    send_resp(conn, 200, "world")
  end

  match _ do
    send_resp(conn, 404, "oops")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们`cURL`了路由，我们将得到以下行为:

```
› curl -I 127.0.0.1:3000/hello
HTTP/1.1 200 OK
cache-control: max-age=0, private, must-revalidate
content-length: 5
date: Thu, 27 Dec 2018 23:25:13 GMT
server: Cowboy

› curl -I 127.0.0.1:3000/foo
HTTP/1.1 404 Not Found
cache-control: max-age=0, private, must-revalidate
content-length: 4
date: Thu, 27 Dec 2018 23:25:17 GMT
server: Cowboy 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，虽然我们没有使用`head`宏显式匹配`HEAD`路由，但是`Plug.Head`插件将`HEAD`请求重新映射到`GET`，我们的处理程序仍然按照预期继续工作(第一个返回 HTTP 200，第二个返回 HTTP 404)。

### `Plug.Logger`

这个有点复杂，所以我们不能在本文中内嵌所有的代码。基本上，如果我们在路由器上插上这个插头，它会记录所有传入的请求和响应状态，就像这样:

```
 GET /index.html
  Sent 200 in 572ms 
```

Enter fullscreen mode Exit fullscreen mode

这个插件使用了 Elixir 的`Logger` ( [docs](https://hexdocs.pm/logger/Logger.html) ，它支持四种不同的日志级别:

*   `:debug` -用于调试相关消息
*   `:info` -任何类型的信息(默认级别)
*   `:warn` -用于警告
*   `:error` -针对错误

如果我们查看它的`call/2`函数的源代码，我们会注意到两个逻辑单元。第一个是:

```
def call(conn, level) do
  Logger.log(level, fn ->
    [conn.method, ?\s, conn.request_path]
  end)

  # Snipped...
end 
```

Enter fullscreen mode Exit fullscreen mode

这一个将采用 Elixir 的`Logger`并使用日志记录`level`将信息记录到后端(默认情况下是`console`)。记录的信息是请求的方法(如`GET`、`POST`等)和请求路径(如`/foo/bar`)。这导致日志的第一行:

```
GET /index.html 
```

Enter fullscreen mode Exit fullscreen mode

第二个逻辑单元稍微复杂一点:

```
def call(conn, level) do
  # Snipped...

  start = System.monotonic_time()

  Conn.register_before_send(conn, fn conn ->
    Logger.log(level, fn ->
      stop = System.monotonic_time()
      diff = System.convert_time_unit(stop - start, :native, :microsecond)
      status = Integer.to_string(conn.status)

      [connection_type(conn), ?\s, status, " in ", formatted_diff(diff)]
    end)

    conn
  end)
end 
```

Enter fullscreen mode Exit fullscreen mode

简而言之:这个部分记录请求的`start`和`stop`(结束)之间的时间，并打印出两者之间的`diff`差(或者换句话说——响应所用的时间)。此外，它还打印出响应的 HTTP 状态。

为了做到这一点，它使用了`Plug.Conn.register_before_send/2` ( [文档](https://hexdocs.pm/plug/Plug.Conn.html#register_before_send/2))，这是一个在响应发送之前注册要调用的回调的实用函数。这意味着计算`diff`并把响应状态记录到`Logger`的函数将在响应发送到客户端之前被`Plug.Conn`调用。

## 用塞子裹住

你真的做到了这一步-我为你鼓掌。我希望这是您在 Plug 及其相关模块/功能方面的一次美好旅程，并且您学到了一些新东西。

我们看了很多关于`Plug`的细节。对于我们谈到的一些模块，我们仅仅触及了皮毛。例如，`Plug.Conn`有相当多的有用函数。或者`Plug.Router`在它的 DSL 中有更多的功能，你可以在那里编写更精细和更周到的 API 或 web 应用。与此相一致，`Plug`还提供了更多内置插头。它甚至有一个插件，可以轻松地提供静态文件，将它插入基于插件的应用程序是轻而易举的事情。

但是，除了我们在本文中跳过的所有内容之外，我希望您理解插件模型是多么强大，它为我们提供了如此简单和不引人注目的功能。

在以后的帖子中，我们将会看到更多关于`Plug`中其他插件的细节，但在此之前，如果你觉得这篇文章有帮助(或没有帮助)，请给我发评论或消息。
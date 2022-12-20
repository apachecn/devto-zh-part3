# 灵丹妙药:用 Plug、Cowboy 和 Poison 构建一个小型 JSON 端点

> 原文：<https://dev.to/jonlunsford/elixir-building-a-small-json-endpoint-with-plug-cowboy-and-poison-1826>

很多时候，我只想在应用程序中添加一个简单的 JSON 端点，公开一个服务，或者处理 webhook 事件，而不需要一个完整框架的开销。让我们看看使用 Erlang 的 [Cowboy](https://github.com/ninenines/cowboy) HTTP 服务器，用 [Plug](https://hexdocs.pm/plug/readme.html) 构建一个生产就绪端点是多么容易。

* * *

#### 塞的是:

> 1.  Specification of composable modules between web applications
> 2.  Erlang VM
> 
> 中不同 web 服务器的连接适配器

如果你来自 **Ruby/Rails** ，think **Rack** ，来自 **Node** ，think **Express** 等等，当然这些库的概念在表面上是相似的，它们有自己独特的权利。

#### 牛仔是:

> 用于 Erlang/OTP 的小型、快速和现代化的 HTTP 服务器

此外，它还是一个支持 HTTP/2 的容错“现代 web 服务器”，为 Websockets 提供了一套处理程序，并为长期连接提供了接口。在不涉及太多细节的情况下，可以肯定地说，对于生产来说，这是一个可以接受的选择。[查阅文档](https://ninenines.eu/docs/en/cowboy/2.5/guide/)了解更多信息。

#### 毒药是:

> 一个 JSON 仙丹库，专注于极快的速度，而不牺牲 T2 的简单性，T4 的完整性和正确性。

换句话说，它是一个超级快速、可靠的 JSON 解析库。

* * *

#### 建筑端点

简短定义结束后，让我们构建一个端点来处理传入的 webhook 事件。现在，我们希望这是“生产就绪”，这对我们的用例意味着什么？

1.  **容错:**始终可用。永远下不去(至少不容易:)
2.  **易于配置:**可以部署到任何环境
3.  **久经考验:**让我们对我们运送的产品充满信心

我们有一个非常简单的使用案例，在选择工具和投入时间解决方案之前，了解自己的需求是一个好主意。

* * *

#### 1。创建一个新的、受监督的 Elixir 应用程序:

```
$ mix new webhook_processor --sup
$ cd webhook_processor 
```

Enter fullscreen mode Exit fullscreen mode

`--sup`将创建一个适合用作 OTP 应用程序的应用程序。OTP 和监理会从上面给我们 **#1** 的要求。我们的服务器会被监控，并在崩溃时自动重启，而服务器可能会崩溃 Erlang VM 应该不会(至少不容易:)。

#### 2。将 Plug、Cowboy 和 Poison 添加为依赖项

```
# ./mix.exs
defmodule WebhookProcessor.MixProject do
  use Mix.Project

  def project do
    [
      app: :webhook_processor,
      version: "0.1.0",
      elixir: "~> 1.7",
      start_permanent: Mix.env() == :prod,
      deps: deps()
    ]
  end

  # Run "mix help compile.app" to learn about applications.
  def application do
    [
      # Add :plug_cowboy to extra_applications
      extra_applications: [:logger, :plug_cowboy],
      mod: {WebhookProcessor.Application, []}
    ]
  end

  # Run "mix help deps" to learn about dependencies.
  defp deps do
    [
      {:plug_cowboy, "~> 2.0"}, # This will pull in Plug AND Cowboy
      {:poison, "~> 3.1"} # Latest version as of this writing
    ]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们添加了`plug_cowboy`(在`deps`中)作为 Plug 和 Cowboy 的单一依赖项。我们还需要将`:plug_cowboy`添加到`extra_applications`列表中(在`application`)。

#### 3。混合 deps.get

```
$ mix deps.get 
```

Enter fullscreen mode Exit fullscreen mode

#### 4。实现 application.ex

```
# ./lib/webhook_processor/application.ex
defmodule WebhookProcessor.Application do
  @moduledoc "OTP Application specification for WebhookProcessor"

  use Application

  def start(_type, _args) do
    # List all child processes to be supervised
    children = [
      # Use Plug.Cowboy.child_spec/3 to register our endpoint as a plug
      Plug.Cowboy.child_spec(
        scheme: :http,
        plug: WebhookProcessor.Endpoint,
        options: [port: 4001]
      )
    ]

    # See https://hexdocs.pm/elixir/Supervisor.html
    # for other strategies and supported options
    opts = [strategy: :one_for_one, name: WebhookProcessor.Supervisor]
    Supervisor.start_link(children, opts)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 5。实现 WebhookProcessor。端点

```
# ./lib/webhook_processor/endpoint.ex
defmodule WebhookProcessor.Endpoint do
  @moduledoc """
  A Plug responsible for logging request info, parsing request body's as JSON,
  matching routes, and dispatching responses.
  """

  use Plug.Router

  # This module is a Plug, that also implements it's own plug pipeline, below:

  # Using Plug.Logger for logging request information
  plug(Plug.Logger)
  # responsible for matching routes
  plug(:match)
  # Using Poison for JSON decoding
  # Note, order of plugs is important, by placing this _after_ the 'match' plug,
  # we will only parse the request AFTER there is a route match.
  plug(Plug.Parsers, parsers: [:json], json_decoder: Poison)
  # responsible for dispatching responses
  plug(:dispatch)

  # A simple route to test that the server is up
  # Note, all routes must return a connection as per the Plug spec.
  get "/ping" do
    send_resp(conn, 200, "pong!")
  end

  # Handle incoming events, if the payload is the right shape, process the
  # events, otherwise return an error.
  post "/events" do
    {status, body} =
      case conn.body_params do
        %{"events" => events} -> {200, process_events(events)}
        _ -> {422, missing_events()}
      end

    send_resp(conn, status, body)
  end

  defp process_events(events) when is_list(events) do
    # Do some processing on a list of events
    Poison.encode!(%{response: "Received Events!"})
  end

  defp process_events(_) do
    # If we can't process anything, let them know :)
    Poison.encode!(%{response: "Please Send Some Events!"})
  end

  defp missing_events do
    Poison.encode!(%{error: "Expected Payload: { 'events': [...] }"})
  end

  # A catchall route, 'match' will match no matter the request method,
  # so a response is always returned, even if there is no route to match.
  match _ do
    send_resp(conn, 404, "oops... Nothing here :(")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很多，但是这个文件的大部分只是一些有用的注释。要点是，我们*使用*宏、 *get* 和 *post* ，从`Plug.Router`生成我们的路线。这个模块本身就是一个插件，它定义了自己的插件管道。注意，`match`和`dispatch`是我们处理请求和发送响应所必需的。*管道*在这里是一个关键概念，因为插头的顺序决定了操作的顺序。注意，match 是在我们定义解析器之前的，这意味着我们不会解析任何东西，除非有一个路由匹配。如果顺序颠倒了，我们将试图解析请求，而不管路由是否匹配。更多信息请参考`Plug.Router`上的[文档](https://hexdocs.pm/plug/Plug.Router.html#content)。

#### 6。使端点可配置

```
# ./lib/webhook_processor/application.ex
defmodule WebhookProcessor.Application do
  @moduledoc "OTP Application specification for WebhookProcessor"

  use Application

  def start(_type, _args) do
    # List all child processes to be supervised
    children = [
      # Use Plug.Cowboy.child_spec/3 to register our endpoint as a plug
      Plug.Cowboy.child_spec(
        scheme: :http,
        plug: WebhookProcessor.Endpoint,
        # Set the port per environment, see ./config/MIX_ENV.exs
        options: [port: Application.get_env(:webhook_processor, :port)]
      )
    ]

    # See https://hexdocs.pm/elixir/Supervisor.html
    # for other strategies and supported options
    opts = [strategy: :one_for_one, name: WebhookProcessor.Supervisor]
    Supervisor.start_link(children, opts)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们已经用环境变量替换了牛仔选项的硬编码端口值，这将允许我们在任何需要的环境中运行 webhook 处理器。最后，为您需要的每个`MIX_ENV`创建一个配置文件:

```
#./config/config.exs

# This file is responsible for configuring your application
# and its dependencies with the aid of the Mix.Config module.
use Mix.Config

import_config "#{Mix.env()}.exs"

-------------------

# ./config/dev.exs

use Mix.Config

config :webhook_processor, port: 4001

-------------------

# ./config/test.exs

use Mix.Config

config :webhook_processor, port: 4002

-------------------

# ./config/prod.exs

use Mix.Config

config :webhook_processor, port: 80 
```

Enter fullscreen mode Exit fullscreen mode

#### 7。试验

```
# ./test/webhook_processor/endpoint_test.exs
defmodule WebhookProcessor.EndpointTest do
  use ExUnit.Case, async: true
  use Plug.Test

  @opts WebhookProcessor.Endpoint.init([])

  test "it returns pong" do
    # Create a test connection
    conn = conn(:get, "/ping")

    # Invoke the plug
    conn = WebhookProcessor.Endpoint.call(conn, @opts)

    # Assert the response and status
    assert conn.state == :sent
    assert conn.status == 200
    assert conn.resp_body == "pong!"
  end

  test "it returns 200 with a valid payload" do
    # Create a test connection
    conn = conn(:post, "/events", %{events: [%{}]})

    # Invoke the plug
    conn = WebhookProcessor.Endpoint.call(conn, @opts)

    # Assert the response
    assert conn.status == 200
  end

  test "it returns 422 with an invalid payload" do
    # Create a test connection
    conn = conn(:post, "/events", %{})

    # Invoke the plug
    conn = WebhookProcessor.Endpoint.call(conn, @opts)

    # Assert the response
    assert conn.status == 422
  end

  test "it returns 404 when no route matches" do
    # Create a test connection
    conn = conn(:get, "/fail")

    # Invoke the plug
    conn = WebhookProcessor.Endpoint.call(conn, @opts)

    # Assert the response
    assert conn.status == 404
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这些测试非常简单，但是它们证实了我们的服务器工作正常。有人可能会说，在这些测试中，我们唯一应该关心的是响应代码，而不是处理事件时发生的副作用。总是测试到你的模块的边界，永远不要超出，除非你正在写一个集成风格的测试。

* * *

#### 结论

我们不费吹灰之力就构建了一个小而强大的端点。多亏了 Cowboy，您应该能够从一台服务器上提供比您可能需要的更多的连接，所以让我们将低成本也添加到好处列表中。

部署呢？让我们看一下构建版本和部署到 AWS:

1.  [用 Docker & Mix](https://dev.to/jonlunsford/deploying-elixir-1-of-3-building-releases-with-mix-1o4a) 构建发布
2.  [地形化 AWS EC2 实例](https://dev.to/jonlunsford/deploying-elixir-2-of-3-terraforming-an-aws-ec2-instance-1nh5)
3.  [使用 Ansible 部署版本](https://dev.to/jonlunsford/deploying-elixir-3-of-3-provisioning-ec2-with-ansible-10o4)

和往常一样，这段代码可以在 GitHub 上找到:[https://github.com/jonlunsford/webhook_processor](https://github.com/jonlunsford/webhook_processor)
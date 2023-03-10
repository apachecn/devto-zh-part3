# Mix 和 OTP 08 :任务和 gen_tcp

> 原文：<https://dev.to/gumi/mix-otp-08-gentcp-5120>

本文在 Elixir 官网的许可下，基于“[Task and gen_tcp](https://elixir-lang.org/getting-started/mix-otp/task-and-gen-tcp.html) ”的解说，加以修改，通过 Erlang 的[`:gen_tcp`模块](http://www.erlang.org/doc/man/gen_tcp.html)如何处理请求 此外，还将介绍 Task 模块的使用方法。

# 回声服务器

首先安装回声服务器，启动 TCP 服务器吧。 用响应发送请求中收到的文本。 目标是一点一点地改善，在受到监视的同时可以处理多个连接的服务器。

TCP 服务器大致执行以下步骤。

1.  等待端口可用，然后保持插座。
2.  等待客户端连接到该端口，然后接受。
3.  读取客户端请求，并写入要返回的响应。

实施这些步骤吧。 `apps/kv_server`转移到 APP 应用程序，打开`lib/kv_server.ex`。 请将模块`KVServer`改写如下。

```
require Logger

defmodule KVServer do
  def accept(port) do
    # オプションの機能はつぎのとおり:
    #
    # 1\. `:binary` - データをバイナリとして受け取る(リストでなく)
    # 2\. `packet: :line` - データを1 行ずつ受け取る
    # 3\. `active: false` - データが受け取れるようになるまで`:gen_tcp.recv/2`を待たせる
    # 4\. `reuseaddr: true` - リスナーが落ちたときアドレスを再利用できるようにする
    #
    {:ok, socket} =
      :gen_tcp.listen(port, [:binary, packet: :line, active: false, reuseaddr: true])
    Logger.info("Accepting connections on port #{port}")
    loop_acceptor(socket)
  end

  defp loop_acceptor(socket) do
    {:ok, client} = :gen_tcp.accept(socket)
    serve(client)
    loop_acceptor(socket)
  end

  defp serve(socket) do
    socket
    |> read_line()
    |> write_line(socket)

    serve(socket)
  end

  defp read_line(socket) do
    {:ok, data} = :gen_tcp.recv(socket, 0)
    data
  end

  defp write_line(line, socket) do
    :gen_tcp.send(socket, line)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`KVServer`模块通过调用`accept/1`启动服务器。 参数是端口号，传递的值为 4040。 `accept/1`首先监视端口，等待插座可用。 接下来要调用的是`loop_acceptor/1`。 在循环的同时，等待客户端连接。 而且，每次有连接时调用的是`serve/1`。

`serve/1`也是循环处理。 一行一行地从插座读取，然后写回。 请注意，`serve/1`函数用[管道运算符`|>`](https://qiita.com/fumio_nonaka/private/19bfff8309adba5fbe7c#%E3%83%91%E3%82%A4%E3%83%97%E6%BC%94%E7%AE%97%E5%AD%90)组成了该处理流程。 右边的函数接受管道运算符左边的值作为第一参数，其返回值由下一个管道运算符进一步传递给右边的函数。

```
socket |> read_line() |> write_line(socket) 
```

Enter fullscreen mode Exit fullscreen mode

用通常的自变量的书写方法进行函数嵌套的处理，使用管道运算符可以很容易地书写。

```
write_line(read_line(socket), socket) 
```

Enter fullscreen mode Exit fullscreen mode

`read_line/1`通过`:gen_tcp`模块的函数安装了插座的读写。 读取为[`:gen_tcp.recv/2`](http://erlang.org/doc/man/gen_tcp.html#recv-2)，写入为[`:gen_tcp.send/2`](http://erlang.org/doc/man/gen_tcp.html#send-2)。

`serve/1`一直循环运行。 于是，调用该函数的`loop_acceptor/1`的主体无法到达末尾调用，所以可以不进行循环吧。 但是，之后为了将`serve/1`分为其他流程，需要末尾调用。

这样就有了回声服务器。 `kv_server`请在 APP 传输中用`iex -S mix`启动 IEx 的会话。 然后，按如下方式调用`KVServer.accept/1`。

```
iex> KVServer.accept(4040)

00:00:00.000 [info]  Accepting connections on port 4040 
```

Enter fullscreen mode Exit fullscreen mode

服务器启动后将进入等待连接的状态。 在此，使用[Telnet](https://ja.wikipedia.org/wiki/Telnet) 客户端访问服务器吧。 请使用命令行工具输入以下内容: 在其他客户端上，输入的命令行也大致相同。

```
$  telnet 127.0.0.1 4040
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'. 
```

Enter fullscreen mode Exit fullscreen mode

回声服务器等待输入。 如果键入键并按[enter]键，则该文本应显示在下一行中。

```
hello
hello
world
world 
```

Enter fullscreen mode Exit fullscreen mode

要退出 Telnet，请使用 control/ctrl +切换到命令模式，然后在 quit 后输入[enter] (退出方式因客户端而异)。 Telnet 关闭后，IEx 会话将显示以下错误: 这是因为`:gen_tcp.recv/2`试图接收数据时，客户端的连接关闭了。 稍后修改服务器时将处理此问题。

```
** (MatchError) no match of right hand side value: {:error, :closed}
    (kv_server) lib/kv_server.ex:33: KVServer.read_line/1
    (kv_server) lib/kv_server.ex:26: KVServer.serve/1
    (kv_server) lib/kv_server.ex:20: KVServer.loop_acceptor/1 
```

Enter fullscreen mode Exit fullscreen mode

目前，必须先纠正的是 TCP 受体崩溃时的问题。 服务器还没有监视进程。 然后，如果服务器掉落，它将不会重新启动，然后就无法再处理请求了。 所以，服务器必须移动到监视树。

# 任务

`Agent`、`GenServer`以及`Supervisor`都可以处理多个信息，管理状态。 但是，想做的事情是简单的任务时，可以使用[`Task`模块](https://hexdocs.pm/elixir/Task.html)。

例如，在新的进程中执行传递给自变量的无名函数，并添加到监视树中的就是[`start_link/1`函数](https://hexdocs.pm/elixir/Task.html#start_link/1)。 打开`lib/kv_server/application.ex`，对`start/2`函数做如下补充。 总是将两个元素的元组传递给子列表。 而是将[`Task.start_link/1`](https://hexdocs.pm/elixir/Task.html#start_link/1)作为任务调用。

```
def start(_type, _args) do
  children = [
    {Task, fn -> KVServer.accept(4040) end}  # 追加
  ]

  opts = [strategy: :one_for_one, name: KVServer.Supervisor]
  Supervisor.start_link(children, opts)
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，服务器将包含在监视树中，并随 APP 应用程序一起启动。 请使用[`mix run`指令](https://hexdocs.pm/mix/Mix.Tasks.Run.html)如下启动 APP。

```
$  mix run --no-halt
 00:00:00.000 [info]  Accepting connections on port 4040 
```

Enter fullscreen mode Exit fullscreen mode

```
$  telnet 127.0.0.1 4040
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
say you
say you
say me
say me 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，端口号直接打了。 例如，通过改写以下内容，在 APP 应用程序启动时，从系统环境中读取端口号。

```
port = String.to_integer(System.get_env("PORT") || "4040")
children = [
  # {Task, fn -> KVServer.accept(4040) end}
  {Task, fn -> KVServer.accept(port) end}
] 
```

Enter fullscreen mode Exit fullscreen mode

如下所示，在启动 APP 应用程序的`mix run`命令中用变量添加端口号。

```
$  PORT=4321 mix run --no-halt 
```

Enter fullscreen mode Exit fullscreen mode

上述端口的设定赋予了默认值( 4040 )。 因此，如果省略端口号，就会用该号码等待连接。

```
$  mix run --no-halt
 00:00:00.000 [info]  Accepting connections on port 4040 
```

Enter fullscreen mode Exit fullscreen mode

增加 Telnet 客户端会怎么样？ 请在连接第一个客户端的情况下，尝试再打开一个 Telnet 客户端。 第二个客户端没有显示回声。

```
$  telnet 127.0.0.1 4040
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
hello
hello?
HELLOOOOOO? 
```

Enter fullscreen mode Exit fullscreen mode

这是因为我们在同一个进程中处理请求。 如果连接了一个客户端，则在连接期间将无法接受另一个客户端。

# 任务管理员

为了使服务器能够处理并发连接，必须以一个进程为受主，让另一个创建的进程处理请求。 想到的一个方法是用`KVServer. loop_acceptor/1`改写为使用`Task.start_link/1`。 现在，从受主进程链接的任务开始。

```
defp loop_acceptor(socket) do
  {:ok, client} = :gen_tcp.accept(socket)
  # serve(client)
  Task.start_link(fn -> serve(client) end)
  loop_acceptor(socket)
end 
```

Enter fullscreen mode Exit fullscreen mode

但是，不能将`serve(client)`的任务链接到受主上。 这是因为，如果对请求的处理下降，则会导致受主卷入其中，从而导致所有连接都断开。

避免这种情况的方法是为每个流程设置一个管理员(请参阅“[Mix 和 OTP 05 :动态管理员](https://qiita.com/fumio_nonaka/private/fb190a0912cd5aae14d3)”)。 让管理员开始临时任务，并将其添加到监视树中。 将`KVServer.Application`的`start/2`函数改写如下。

```
def start(_type, _args) do
  port = String.to_integer(System.get_env("PORT") || "4040")

  children = [
    {Task.Supervisor, name: KVServer.TaskSupervisor},  # 追加
    {Task, fn -> KVServer.accept(port) end}
  ]

  opts = [strategy: :one_for_one, name: KVServer.Supervisor]
  Supervisor.start_link(children, opts)
end 
```

Enter fullscreen mode Exit fullscreen mode

至此，`Task.Supervisor`将以`KVServer.TaskSupervisor`的名称启动。 请注意，受主依赖于此管理程序。 也就是说，管理者必须先开始。 因此，`KVServer`的`loop_acceptor/1`改写为`Task.Supervisor`处理请求。

```
defp loop_acceptor(socket) do
  {:ok, client} = :gen_tcp.accept(socket)
  # serve(client)
  {:ok, pid} = Task.Supervisor.start_child(KVServer.TaskSupervisor, fn -> serve(client) end)  # 追加
  :ok = :gen_tcp.controlling_process(client, pid)  # 追加
  loop_acceptor(socket)
end 
```

Enter fullscreen mode Exit fullscreen mode

[你注意到`：gen_tcp.controlling_process/2`](http://erlang.org/doc/man/gen_tcp.html#controlling_process-2)的呼叫了吗？ 这使子进程成为客户端套接字的“控制进程”。 否则，受主崩溃时，所有客户端都将丢失。 因为套接字与接受的进程相关联(这是缺省行为)。

重新启动服务器时，Telnet 客户端会同时打开多个。 即使关闭一个客户端，受主也不会掉落，所以其他客户端的连接应该是有效的吧。

```
$  mix run --no-halt
 00:00:00.000 [info]  Accepting connections on port 4040 
```

Enter fullscreen mode Exit fullscreen mode

加上管理人员，正确制定了监视战略。 即使受主崩溃，也不会丢失已存在的连接。 另外，任务管理员崩溃时，也没有必要降低受主吧。

另一个必须考虑的是重启战略。 任务的默认值`:restart`被设定为`:temporary`。 这意味着不会重新启动。 关于`Task.Supervisor`开始的连接可以说是合适的。 因为恢复失败的连接没有意义。 但是，如果受体掉了，应该让其重新启动吧。

一个方法是在新定义的模块中使用`use Task`，作为`restart: :permanent`调用`start_link`函数，重启任务。 但是，与其他开发人员的库集成时，代理、任务和服务器的定义方式不会改变。 因此，让我们动态定制子进程的规范。 为此，在`KVServer.Application`的`start/2`中改写为调出[`Supervisor.child_spec/2`](https://hexdocs.pm/elixir/Supervisor.html#child_spec/2)。

```
def start(_type, _args) do
  port = String.to_integer(System.get_env("PORT") || "4040")

  children = [
    {Task.Supervisor, name: KVServer.TaskSupervisor},
    # {Task, fn -> KVServer.accept(port) end}
    Supervisor.child_spec({Task, fn -> KVServer.accept(port) end}, restart: :permanent)
  ]

  opts = [strategy: :one_for_one, name: KVServer.Supervisor]
  Supervisor.start_link(children, opts)
end 
```

Enter fullscreen mode Exit fullscreen mode

`Supervisor.child_spec/2`可以通过模块和元组制作孩子的规格。 此外，还可以复盖子代的规格。 这样，受主始终工作。 然后，可以在一直运行的管理员的指导下开始临时任务进程。 以下列举`KVServer.Application`和`KVServer`的定义吧。

```
defmodule KVServer.Application do
  @moduledoc false

  use Application

  def start(_type, _args) do
    port = String.to_integer(System.get_env("PORT") || "4040")

    children = [
      {Task.Supervisor, name: KVServer.TaskSupervisor},
      Supervisor.child_spec({Task, fn -> KVServer.accept(port) end}, restart: :permanent)
    ]

    opts = [strategy: :one_for_one, name: KVServer.Supervisor]
    Supervisor.start_link(children, opts)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
require Logger

defmodule KVServer do
  @doc """
  定められた`port`で接続の受け入れを始める。
  """
  def accept(port) do
    {:ok, socket} =
      :gen_tcp.listen(port, [:binary, packet: :line, active: false, reuseaddr: true])
    Logger.info("Accepting connections on port #{port}")
    loop_acceptor(socket)
  end

  defp loop_acceptor(socket) do
    {:ok, client} = :gen_tcp.accept(socket)
    {:ok, pid} = Task.Supervisor.start_child(KVServer.TaskSupervisor, fn -> serve(client) end)
    :ok = :gen_tcp.controlling_process(client, pid)
    loop_acceptor(socket)
  end

  defp serve(socket) do
    socket
    |> read_line()
    |> write_line(socket)

    serve(socket)
  end

  defp read_line(socket) do
    {:ok, data} = :gen_tcp.recv(socket, 0)
    data
  end

  defp write_line(line, socket) do
    :gen_tcp.send(socket, line)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

#### Mix 和 OTP 也抽签

*   [尝试使用 Mix 和 OTP 01:mix](https://dev.to/gumi/mixotp-01-mix-dg1)
*   MixとOTP 02:经纪人
*   MixとOTP 03:基因服务器
*   [Mix 和 OTP 04 :管理员和 APP 沟通](https://dev.to/gumi/mix-otp-04-bj0)
*   [Mix 和 OTP 05 :动态管理员](https://dev.to/gumi/mix-otp-05-l3p)
*   [MixとOTP 06: ETS](https://dev.to/gumi/mix-otp-06-ets-ol3)
*   [Mix 和 OTP 07 :依存关系和模糊项目](https://dev.to/gumi/mix-otp-07-1p5k)
*   MixとOTP 08: Taskとgen_tcp
*   [Mix 和 OTP 09: DocTest 和 with 的模式匹配](https://dev.to/gumi/mix-otp-09-doctest-with-533g)
*   [Mix 和 OTP 10 :分散处理的 Task 和设定](https://dev.to/gumi/mix-otp-10-3i31)
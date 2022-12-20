# Mix 和 OTP 09: DocTest 和 with 的模式匹配

> 原文：<https://dev.to/gumi/mix-otp-09-doctest-with-533g>

本文在 Elixir 官方网站的许可下，基于“[Doctests，patterns and with](https://elixir-lang.org/getting-started/mix-otp/docs-tests-and-with.html) ”的解说，加以补充修正，对基于`DocTest`的测试和使用`with`的图案匹配进行说明

# DocTest

在 Elixir 网站上，他说文档对语言有着重要的作用(第一类)。 例如，在命令行工具中输入`mix help`可以列出 Mix 命令，在 IEx 控制台中可以通过`h Enum`看到模块的说明。

本文实现命令的分析功能。 然后，加上文档，通过`DocTest`确认是最新状态吧。 通过这个测试，可以正确地保持文档的代码示例。

在`lib/kv_server/command.ex`中如下定义指令分析的函数`parse/1`，试着执行`DocTest`。 为了`DocTest`而添加到文档文本中的代码示例，请在空格 4 处缩进后添加提示`iex>`。 代码分为多行时，与 IEx 一样，可以使用`...>`。 这是期待添加到下一行的结果。 在空行或新的`iex>`提示符处，分隔代码示例。

```
defmodule KVServer.Command do
  @doc ~S"""
  与えられた`line`をコマンドとして解析する。

  ## 例

    iex> KVServer.Command.parse("CREATE shopping\r\n")
    {:ok, {:create, "shopping"}}

  """
  def parse(_line) do
    :not_implemented  # 未実装
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

以`@doc ~S"""`开始了文档的文本。 使用大写字母`~S`是为了避免在测试时`\r\n`转换为换行和复位。

执行`DocTest`的文件在`test/kv_server/command_test.exs`中制作。 然后，从`KVServer.CommandTest`模块中调用`doctest KVServer.Command`。

```
defmodule KVServer.CommandTest do
  use ExUnit.Case, async: true
  doctest KVServer.Command
end 
```

Enter fullscreen mode Exit fullscreen mode

进行`mix test`后，`DocTest`会显示如下错误。 `parse/1`因为函数没有像文档那样实现。

```
1) doctest KVServer.Command.parse/1 (1) (KVServer.CommandTest)
    test/kv_server/command_test.exs:3
    Doctest failed
    code:  KVServer.Command.parse("CREATE shopping\r\n") === {:ok, {:create, "shopping"}}
    left:  :not_implemented
    right: {:ok, {:create, "shopping"}}
    stacktrace:
      lib/kv_server/command.ex:7: KVServer.Command (module) 
```

Enter fullscreen mode Exit fullscreen mode

要通过`DocTest`，只需如下实现`parse/1`函数即可。 正在将一行文本转换为以空格分隔的列表，以与命令匹配。 [`String.split/1`](https://hexdocs.pm/elixir/String.html#split/1)将连续的白色空格作为一个分隔符字符串处理。

```
# def parse(_line) do
def parse(line) do
  # :not_implemented
  case String.split(line) do
    ["CREATE", bucket] -> {:ok, {:create, bucket}}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

再增加`DocTest`的代码示例吧。 按如下方式添加新命令和错误说明:

```
@doc ~S"""
与えられた`line`をコマンドとして解析する。

## 例

  iex> KVServer.Command.parse("CREATE shopping\r\n")
  {:ok, {:create, "shopping"}}

  iex> KVServer.Command.parse("CREATE  shopping  \r\n")
  {:ok, {:create, "shopping"}}

  iex> KVServer.Command.parse("PUT shopping milk 1\r\n")
  {:ok, {:put, "shopping", "milk", "1"}}

  iex> KVServer.Command.parse("GET shopping milk\r\n")
  {:ok, {:get, "shopping", "milk"}}

  iex> KVServer.Command.parse("DELETE shopping eggs\r\n")
  {:ok, {:delete, "shopping", "eggs"}}

定められていないコマンドや数の合わない引数はエラーになる。

  iex> KVServer.Command.parse("UNKNOWN shopping eggs\r\n")
  {:error, :unknown_command}

  iex> KVServer.Command.parse("GET shopping\r\n")
  {:error, :unknown_command}

""" 
```

Enter fullscreen mode Exit fullscreen mode

以下代码根据这些文档的示例实现了`parse/1`函数。 因为匹配了命令名称和参数的数量，所以没有几个条件判定的语法就能清晰地解析命令。

```
def parse(line) do
  case String.split(line) do
    ["CREATE", bucket] -> {:ok, {:create, bucket}}
    ["GET", bucket, key] -> {:ok, {:get, bucket, key}}
    ["PUT", bucket, key, value] -> {:ok, {:put, bucket, key, value}}
    ["DELETE", bucket, key] -> {:ok, {:delete, bucket, key}}
    _ -> {:error, :unknown_command}
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

`DocTest`数到 7。 这是因为对每个代码示例都进行了测试。

```
==>  kv_server
.......  Finished in 0.06 seconds
7 doctests, 0 failures 
```

Enter fullscreen mode Exit fullscreen mode

如果在代码示例之间不放空行，ExUnit 就会将其视为一个测试进行编译。

```
iex> KVServer.Command.parse("UNKNOWN shopping eggs\r\n")
{:error, :unknown_command}
iex> KVServer.Command.parse("GET shopping\r\n")
{:error, :unknown_command} 
```

Enter fullscreen mode Exit fullscreen mode

`DocTest`正如其名字所示，以文档为主，测试是传统的定位。 其目的是使文档保持最新，而不是代替测试。 关于`DocTest`，详情请参阅“[`ExUnit.DocTest`](https://hexdocs.pm/ex_unit/ExUnit.DocTest.html)”。

# 同

命令分析好了。 在`KVServer.Command`模块中，暂时放置用于执行的`run/1`函数。

```
@doc """
与えられたコマンドを実行する。
"""
def run(_command) do
  {:ok, "OK\r\n"}
end 
```

Enter fullscreen mode Exit fullscreen mode

然后是要执行的逻辑的实现。 改写`lib/kv_server.ex`的`KVServer`模块吧。 首先是`serve/1`函数。 在`case/2`中根据指令的分析和执行的结果进行处理。

```
defp serve(socket) do
  # socket
  # |> read_line()
  # |> write_line(socket)
  msg =
    case read_line(socket) do
      {:ok, data} ->
        case KVServer.Command.parse(data) do
          {:ok, command} ->
            KVServer.Command.run(command)
          {:error, _} = err ->
            err
        end
      {:error, _} = err ->
        err
    end

  write_line(socket, msg)
  serve(socket)
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来是`read_line/1`函数。

```
defp read_line(socket) do
  # {:ok, data} = 
  :gen_tcp.recv(socket, 0)
  # data
end 
```

Enter fullscreen mode Exit fullscreen mode

然后，`write_line`函数根据命令应该做的事情会发生变化。

```
# defp write_line(line, socket) do
defp write_line(socket, {:ok, text}) do
  # :gen_tcp.send(socket, line)
  :gen_tcp.send(socket, text)
end

defp write_line(socket, {:error, :unknown_command}) do
  # 予め定めたエラーはクライアントに書き込む。
  :gen_tcp.send(socket, "UNKNOWN COMMAND\r\n")
end

defp write_line(_socket, {:error, :closed}) do
  # 接続が閉じたらきちんと終了する。
  exit(:shutdown)
end

defp write_line(socket, {:error, error}) do
  # 不明なエラーはクライアントに書き込んで終了する。
  :gen_tcp.send(socket, "ERROR\r\n")
  exit(error)
end 
```

Enter fullscreen mode Exit fullscreen mode

写的代码还只是大框架。 但是，可以启动服务器，确认动作。

```
$  mix run --no-halt
 00:00:00.000 [info]  Accepting connections on port 4040 
```

Enter fullscreen mode Exit fullscreen mode

目前，如果命令和参数数量正确，将显示“OK”，否则显示“UNKNOWN COMMAND”。

```
$  telnet 127.0.0.1 4040
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
CREATE shopping
OK
HELLO
UNKNOWN COMMAND 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止的动作都是正确的。 但是，`serve/1`函数嵌套在`case/2`中，不容易看。 在这种情况下可以使用的是 Elixir v1.2 所具备的`with`句法。 不是条件，而是通过模式匹配进行处理。

`with`将`<-`右边返回的表达式值与左边的模式匹配。 然后，一致后进入下一个公式。 不一致时，其值会直接返回。 以下代码用`with`的公式重新组装了`case/2`的条件。 如果存在与`{:ok, x}`不一致的表达式，则立即返回缺少`with`语法的值。 详情请阅读文档的[`with/1`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#with/1)一项。

```
defp serve(socket) do
  msg =
    # case read_line(socket) do
    #   {:ok, data} ->
    #     case KVServer.Command.parse(data) do
    #       {:ok, command} ->
    #         KVServer.Command.run(command)
    #       {:error, _} = err ->
    #         err
    #     end
    #   {:error, _} = err ->
    #     err
    # end
    with {:ok, data} <- read_line(socket),
         {:ok, command} <- KVServer.Command.parse(data),
         do: KVServer.Command.run(command)

  write_line(socket, msg)
  serve(socket)
end 
```

Enter fullscreen mode Exit fullscreen mode

# [执行](#%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%82%92%E5%AE%9F%E8%A1%8C%E3%81%99%E3%82%8B)命令

剩下的是安装`KVServer.Command`模块的`run/1`。 根据参数的命令，用多节函数规定不同的服务器操作。 各个函数节在`KV.Registry`的服务器上进行适当的处理。 `:kv`APP 应用程序启动时注册服务器。 并且，`:kv_server`依赖于`:kv`APP。 因此，依赖 APP 应用程序的服务是没有问题的。

```
# def run(_command) do
#   {:ok, "OK\r\n"}
# end
def run(command)

def run({:create, bucket}) do
  KV.Registry.create(KV.Registry, bucket)
  {:ok, "OK\r\n"}
end

def run({:get, bucket, key}) do
  lookup(bucket, fn pid ->
    value = KV.Bucket.get(pid, key)
    {:ok, "#{value}\r\nOK\r\n"}
  end)
end

def run({:put, bucket, key, value}) do
  lookup(bucket, fn pid ->
    KV.Bucket.put(pid, key, value)
    {:ok, "OK\r\n"}
  end)
end

def run({:delete, bucket, key}) do
  lookup(bucket, fn pid ->
    KV.Bucket.delete(pid, key)
    {:ok, "OK\r\n"}
  end)
end

defp lookup(bucket, callback) do
  case KV.Registry.lookup(KV.Registry, bucket) do
    {:ok, pid} -> callback.(pid)
    :error -> {:error, :not_found}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`run/1`函数只有`def run(command)`，没有主体。 可以对没有主体的只有标头的函数声明默认参数(参照“[Elixir 入门 08 :模块和函数](https://dev.to/gumi/elixir-08--1c4c)”“默认参数”)。 但是，这里没有提供默认值。 用来弄清楚自变量。

在上述`KVServer.Command`模块中，加入了通用的私人`lookup/2`。 该函数寻找目标进程，如果找到则返回其`pid`，如果没有则返回`{:error, :not_found}`。 于是，在`KVServer`模块的`write_line/2`中，也必须在`write_line(socket, {:error, error})`的前面加上对应的函数节。

```
defp write_line(socket, {:error, :not_found}) do
  :gen_tcp.send(socket, "NOT FOUND\r\n")
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，命令可以分别执行。

```
$  telnet 127.0.0.1 4040
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
CREATE shopping
OK
PUT shopping milk 1
OK
PUT shopping eggs 3
OK
GET shopping milk
1
OK
DELETE shopping eggs
OK 
```

Enter fullscreen mode Exit fullscreen mode

服务器的功能差不多做好了。 还没着手的是测试。 如何进行测试不得不考虑。

`KVServer.Command.run/1`实现了将命令直接发送到名为`KV.Registry`的服务器。 服务器注册`:kv`APP 应用。 这意味着服务器将成为全球化的。 于是，如果两个测试同时发送了信息，测试就会相互竞争(可能会失败)。 因此，您必须决定是进行单元测试，并将其隔离以便同时运行，还是编写在全局状态下运行的集成测试。 现在，让我们运行一个完整的 APP 应用程序堆栈，以测试生产系统。

> 为了能够单独测试`KVServer.Command.run/1`，服务器必须实现为作为参数传递，而不是直接将命令发送到`KV.Registry`的进程。 例如，假设函数的标题为`def run(command, pid)`。 然后，后续的函数节将相应地进行改写。
> 
> ```
> def run({:create, bucket}, pid) do
>   KV.Registry.create(pid, bucket)
>   {:ok, "OK\r\n"}
> end
> # 他の関数節も同様。 
> ```
> 
> 这样改写的情况下，会制作`KV.Registry`的实例，作为`run/2`的自变量传递。 因为不依赖于全局的`KV.Registry`，所以可以不共享状态而使测试异步。

集成测试将执行从 TCP 服务器到子进程的整个堆栈，具体取决于全局服务器名称。 测试取决于全局状态，因此必须进行同步。 集成测试会影响测试的性能，但同时会显示 APP 应用程序的组件是如何协作的。 通常使用的场景是对 APP 的主要流程的测试。 例如，这次的命令分析不适合确认根据详细条件的实现。

综合测试的安装如下`test/kv_server_test.exs`。 使用 TCP 客户端向服务器发送命令，以查看是否得到了期望的响应。 集成测试将检查与服务器的所有交互，包括未知命令和缺少的错误。 然后，请注意，不需要关闭套接字，就像 ETS 表或链接的进程一样。 因为测试过程结束后，套接字会自动关闭。

```
defmodule KVServerTest do
  use ExUnit.Case

  setup do
    Application.stop(:kv)
    :ok = Application.start(:kv)
  end

  setup do
    opts = [:binary, packet: :line, active: false]
    {:ok, socket} = :gen_tcp.connect('localhost', 4040, opts)
    %{socket: socket}
  end

  test "server interaction", %{socket: socket} do
    assert send_and_recv(socket, "UNKNOWN shopping\r\n") ==
           "UNKNOWN COMMAND\r\n"

    assert send_and_recv(socket, "GET shopping eggs\r\n") ==
           "NOT FOUND\r\n"

    assert send_and_recv(socket, "CREATE shopping\r\n") ==
           "OK\r\n"

    assert send_and_recv(socket, "PUT shopping eggs 3\r\n") ==
           "OK\r\n"

    # GET returns two lines
    assert send_and_recv(socket, "GET shopping eggs\r\n") == "3\r\n"
    assert send_and_recv(socket, "") == "OK\r\n"

    assert send_and_recv(socket, "DELETE shopping eggs\r\n") ==
           "OK\r\n"

    # GET returns two lines
    assert send_and_recv(socket, "GET shopping eggs\r\n") == "\r\n"
    assert send_and_recv(socket, "") == "OK\r\n"
  end

  defp send_and_recv(socket, command) do
    :ok = :gen_tcp.send(socket, command)
    {:ok, data} = :gen_tcp.recv(socket, 0, 1000)
    data
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这次，由于测试依赖于全局数据，所以不对`use ExUnit.Case`赋予`async: true`。 此外，每次测试结束`:kv`APP 并重新启动，以便测试总是以新的状态进行。 实际测试后，`:kv`APP 程序的结束会作为警告加入到终端中吧。

```
00:00:00.000 [info]  Application kv exited: :stopped 
```

Enter fullscreen mode Exit fullscreen mode

为了在测试时不显示日志信息，ExUnit 具备的是`:capture_log`的功能。 在每次测试之前添加`@tag :capture_log`，或者为整个测试模块确定`@moduletag :capture_log`后，ExUnit 将自动捕获测试运行期间的日志。 然后，如果测试失败，捕获的日志将除 ExUnit 报告外还会输出。

请在`use ExUnit.Case`之后加上`@moduletag :capture_log`。

```
use ExUnit.Case
@moduletag :capture_log 
```

Enter fullscreen mode Exit fullscreen mode

然后，如果测试失败，将显示报告，例如:

```
1) test server interaction (KVServerTest)
    test/kv_server_test.exs:17
    ** (RuntimeError) oops
    stacktrace:
      test/kv_server_test.exs:29

    The following output was logged:

    00:00:00.000 [info]  Application kv exited: :stopped 
```

Enter fullscreen mode Exit fullscreen mode

这个集成测试很简单，但并不快。 因为测试是同步运行的，而且每个测试都需要关闭并重新启动 APP 应用程序，这是一项繁重的设置。 因此，可能会因超时而出错，如下所示。

```
1) test server interaction (KVServerTest)
    test/kv_server_test.exs:16
    ** (MatchError) no match of right hand side value: {:error, :timeout}
    code: assert send_and_recv(socket, "DELETE shopping eggs\r\n") ==
    stacktrace:
      test/kv_server_test.exs:43: KVServerTest.send_and_recv/2
      test/kv_server_test.exs:33: (test) 
```

Enter fullscreen mode Exit fullscreen mode

为了避免超时，请调整传递给[`:gen_tcp.recv/3`](http://erlang.org/doc/man/gen_tcp.html#recv-3)的第 3 参数的超时毫秒。

```
defp send_and_recv(socket, command) do
  :ok = :gen_tcp.send(socket, command)
  {:ok, data} = :gen_tcp.recv(socket, 0, 3000)  # 1000)
  data
end 
```

Enter fullscreen mode Exit fullscreen mode

最佳的测试策略必须根据 APP 情况来确定。 必须平衡代码质量和可靠性，以及整个测试的运行时间等。 例如，一开始可以只对服务器进行综合测试。 如果服务器在随后的发行版中继续扩展，或者某些 APP 应用程序经常出现错误，则必须考虑拆分测试。 可以编写不像集成测试那样沉重的单元测试，集中进行测试。

#### Mix 和 OTP 也抽签

*   [尝试使用 Mix 和 OTP 01:mix](https://dev.to/gumi/mixotp-01-mix-dg1)
*   MixとOTP 02:经纪人
*   MixとOTP 03:基因服务器
*   [Mix 和 OTP 04 :管理员和 APP 沟通](https://dev.to/gumi/mix-otp-04-bj0)
*   [Mix 和 OTP 05 :动态管理员](https://dev.to/gumi/mix-otp-05-l3p)
*   [MixとOTP 06: ETS](https://dev.to/gumi/mix-otp-06-ets-ol3)
*   [Mix 和 OTP 07 :依存关系和模糊项目](https://dev.to/gumi/mix-otp-07-1p5k)
*   MixとOTP 08: Taskとgen_tcp
*   Mix 和 OTP 09: DocTest 和 with 的模式匹配
*   [Mix 和 OTP 10 :分散处理的 Task 和设定](https://dev.to/gumi/mix-otp-10-3i31)
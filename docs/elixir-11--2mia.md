# Elixir 入门 11 :流程

> 原文：<https://dev.to/gumi/elixir-11--2mia>

本文在 Elixir 官方网站的许可下，根据“[Processes](https://elixir-lang.org/getting-started/processes.html) ”的解说，加以修改，对 Elixir 的流程制作方法和流程之间的信息交换方法进行说明。

在 Elixir 中，所有代码都在进程中移动。 过程相互分离，并行工作，传递消息进行通信。 该过程不仅是 Elixir 并行处理的基础，而且有助于构建分散性和容错性([、容错性](http://e-words.jp/w/%E3%83%95%E3%82%A9%E3%83%BC%E3%83%AB%E3%83%88%E3%83%88%E3%83%AC%E3%83%A9%E3%83%B3%E3%82%B9.html))优异的程序。

不要将 Elixir 流程与操作系统流程混淆。 Elixir 的进程相对于内存和 CPU 非常轻量。 与许多其他编程语言线程不同。 同时运行几万到几十万个流程也并不少见。

# 产卵

要制作新的流程，以[`spawn/1`函数](https://hexdocs.pm/elixir/Kernel.html#spawn/1)的调用为基本。 自变量是在另一个进程中执行的函数，返回值是 APP 中唯一的进程标识符 PID (标识号因环境而异)。 执行给定函数后，以下代码生成的进程将终止。

```
iex> spawn fn -> 1 + 2 end
#PID<0.87.0> 
```

Enter fullscreen mode Exit fullscreen mode

调查当前进程的 PID 的是[`self/0`](https://hexdocs.pm/elixir/Kernel.html#self/0)函数。 将该 PID 传递给[`Process.alive?/1`](https://elixir-lang.org/getting-started/processes.html)并调用，即可确认进程是否在运行。

```
iex> pid = self()
#PID<0.84.0>
iex> Process.alive?(pid)
true 
```

Enter fullscreen mode Exit fullscreen mode

[如果使用`spawn/3`函数](https://hexdocs.pm/elixir/Kernel.html#spawn/3)，则可以从模块的函数创建过程。 参数是模块和函数以及要传递的参数的列表。 另外，第 2 参数的函数请用原子给出。

```
defmodule Example do
  def add(a, b) do
    IO.puts(a + b)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> spawn(Example, :add, [2, 3])
5
#PID<0.90.0> 
```

Enter fullscreen mode Exit fullscreen mode

# 发送和接收

[用`send/2`](https://hexdocs.pm/elixir/Kernel.html#send/2)发送信息的话，可以用[`receive/1`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#receive/1)接收。

发送的消息存储在流程邮箱中。 于是，[`receive/1`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#receive/1)块寻找当前的流程邮箱，接收与模式匹配的信息。 `receive/1`和`case/2`一样，包含监控和多个子句。

```
iex> send self(), {:hello, "world"}
{:hello, "world"}
iex> receive do
...>   {:hello, msg} -> msg
...>   {:world, msg} -> "won't match"
...> end
"world" 
```

Enter fullscreen mode Exit fullscreen mode

如果邮箱中没有与模式匹配的消息，则当前进程将等待，直到收到匹配的消息。 在这种情况下，还规定了超时。 如果假设已经有消息，则值可以为 0。 另外，为了分隔位数，可以在数值上加上下划线`_`。

```
iex> receive do
...>   {:hello, msg}  -> msg
...> after
...>   1_000 -> "nothing after 1s"
...> end
"nothing after 1s" 
```

Enter fullscreen mode Exit fullscreen mode

试着在两个过程之间发送信息吧。 下一个发送过程在`receive/1`块接收并返回值后，将结束，因为没有其他处理。 另外，[`inspect/2`函数](https://hexdocs.pm/elixir/Kernel.html#inspect/2)(第 2 参数的默认值`[]`)将参数的数据转换为内部的字符串表现，主要用于输出。

```
iex> parent = self()
#PID<0.84.0>
iex> spawn fn -> send(parent, {:hello, self()}) end
#PID<0.93.0>
iex> receive do
...>   {:hello, pid} -> "Got hello from #{inspect pid}"
...> end
"Got hello from #PID<0.93.0>" 
```

Enter fullscreen mode Exit fullscreen mode

要在 shell 中输出并清空邮箱中的所有消息，请使用帮助函数[`flush/0`](https://hexdocs.pm/iex/IEx.Helpers.html#flush/0)。

```
iex> send self(), :hello
:hello
iex> send self(), :world
:world
iex> flush()
:hello:world
:ok
iex> flush()
:ok 
```

Enter fullscreen mode Exit fullscreen mode

也可以向由模块函数创建的过程发送信息。 但是，由于函数在收到并处理消息后会结束，因此不会收到下一条发送的消息。

```
defmodule Example do
  def listen do
    receive do
      {:hello, msg} -> IO.puts(msg)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> pid = spawn(Example, :listen, [])
#PID<0.90.0>
iex> send pid, {:hello, "world"}
world
{:hello, "world"}
iex> send pid, {:hello, "tokyo"}
{:hello, "tokyo"} 
```

Enter fullscreen mode Exit fullscreen mode

递归调用函数不会结束处理，可以多次发送消息。

```
defmodule Example do
  def listen do
    receive do
      {:hello, msg} -> IO.puts(msg)
    end
    listen  #再帰呼び出し
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> send pid, {:hello, "world"}
world
{:hello, "world"}
iex> send pid, {:hello, "tokyo"}
tokyo
{:hello, "tokyo"} 
```

Enter fullscreen mode Exit fullscreen mode

# 链接

用 Elixir 制作流程时，经常会链接。 在尝试链接的进程之前，先看看`spawn/1`的进程失败时会发生什么情况。 [即使在`raise/1`](https://hexdocs.pm/elixir/Kernel.html#raise/1)中引发`RuntimeError`的异常，也只会记录错误，父进程在运行。 那是因为过程是相互分离的。

```
iex> spawn fn -> raise "oops" end#PID
<0.86.0>
iex>
[error] Process #PID<0.86.0> raised an exception
** (RuntimeError) oops
    (stdlib) erl_eval.erl:668: :erl_eval.do_apply/6 
```

Enter fullscreen mode Exit fullscreen mode

为了向其他人传达某个过程的失败，必须将它们链接起来。 为此，使用[`spawn_link/1`](https://hexdocs.pm/elixir/Kernel.html#spawn_link/1)链接到当前的进程。

在以下代码中，用`spawn_link/1`链接了父进程的 shell。 因此，由于子进程异常，父 shell 收到了`EXIT`的通知。 IEx 检测到 shell 的结束，开始新的会话。

```
iex> self()
#PID<0.84.0>
iex> spawn_link fn -> raise "oops" end
** (EXIT from #PID<0.84.0>) shell process exited with reason: an exception was raised:
    ** (RuntimeError) oops
        (stdlib) erl_eval.erl:668: :erl_eval.do_apply/6

[error] Process #PID<0.90.0> raised an exception
** (RuntimeError) oops
    (stdlib) erl_eval.erl:668: :erl_eval.do_apply/6 
```

Enter fullscreen mode Exit fullscreen mode

[使用`spawn_link/3`函数](https://hexdocs.pm/elixir/Kernel.html#spawn_link/3)，可以通过模块的函数链接流程。 参数是模块和函数以及要传递的参数的列表。 [`exit/1`](https://hexdocs.pm/elixir/Kernel.html#exit/1)结束调用的进程。 届时，自变量将显示为理由。

```
defmodule Example do
  def explode, do: exit(:boom)
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> spawn(Example, :explode, [])
#PID<0.89.0>
iex> spawn_link(Example, :explode, [])
** (EXIT from #PID<0.87.0>) shell process exited with reason: :boom 
```

Enter fullscreen mode Exit fullscreen mode

不想降低链接的当前进程时，使用[`Process.flag/2`](https://hexdocs.pm/elixir/Process.html#flag/2)。 如果对第 1 自变量赋予标志`:trap_exit`，对第 2 自变量的值赋予`true`，则停止结束。 此函数由 erlang 的[`process_flag/2`](http://erlang.org/doc/man/erlang.html#process_flag-2)确定标志(参照“[Receiving Exit Signals](http://erlang.org/doc/reference_manual/processes.html#receiving-exit-signals) ”)。 停止结束后，会用`{:EXIT, from_pid, reason}`这个元组接收信息。

```
defmodule Example do
  def explode, do: exit(:boom)
  def run do
    Process.flag(:trap_exit, true)
    spawn_link(Example, :explode, [])
    receive do
      {:EXIT, from_pid, reason} -> IO.puts("Exit reason: #{reason}")
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.run
Exit reason: boom
:ok 
```

Enter fullscreen mode Exit fullscreen mode

有时你想得到信息，而不是把两个过程链接起来。 这种情况下使用的是[`spawn_monitor/3`](https://hexdocs.pm/elixir/Kernel.html#spawn_monitor/3)，返回值是 PID 和监视过程的参照。 进程崩溃时，您将收到消息。 现在的进程不会落下，也不需要停止结束。

```
defmodule Example do
  def explode, do: exit(:boom)
  def run do
    {_pid, _ref} = spawn_monitor(Example, :explode, [])
    receive do
      {:DOWN, _ref, :process, _from_pid, reason} -> IO.puts("Exit reason: #{reason}")
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.run
Exit reason: boom
:ok 
```

Enter fullscreen mode Exit fullscreen mode

也可以通过调用[`Process.link/1`](https://hexdocs.pm/elixir/Process.html#link/1)来确定链接。 [`Process`](https://hexdocs.pm/elixir/Process.html)模块还具备其他各种功能。

流程和链接对于构建容错系统也起着重要的作用。 Elixir 进程相互隔离，缺省情况下不共享任何内容。 因此，一个过程的失败不会导致其他过程崩溃，也不会产生负面影响。

但是，如果将流程链接起来，就会建立发生故障时的关系。 流程经常链接的是管理员。 管理员可以检测到进程已下降，并开始新的替代进程。

其他语言必须补充处理例外。 Elixir 可以继续失败，因为管理员可以正确地重新启动系统。 “让它早点失败”是用 Elixir 开发软件时的常见想法。

# 任务

`spawn`基于函数构建的`Task`提供了更好的错误报告和内检功能。 如果用[`Task.start/1`](https://hexdocs.pm/elixir/Task.html#start/1)和[`Task.start_link/1`](https://hexdocs.pm/elixir/Task.html#start_link/1)代替`spawn/1`和`spawn_link/1`，则返回值不仅仅是 PID，而是元组`{：ok、pid}`。 这样就可以在监视树中处理任务了。 错误报告也变得详细。

```
iex> Task.start fn -> raise "oops" end
{:ok, #PID<0.91.0>}
iex>
[error] Task #PID<0.91.0> started from #PID<0.87.0> terminating
** (RuntimeError) oops
    (stdlib) erl_eval.erl:668: :erl_eval.do_apply/6
    (elixir) lib/task/supervised.ex:88: Task.Supervised.do_apply/2
    (stdlib) proc_lib.erl:247: :proc_lib.init_p_do_apply/3
Function: #Function<20.99386804/0 in :erl_eval.expr/5>
    Args: [] 
```

Enter fullscreen mode Exit fullscreen mode

[`Task.async/3`](https://hexdocs.pm/elixir/Task.html#async/3)在后台执行函数。 如果将其返回值传递给[`Task.await/2`](https://hexdocs.pm/elixir/Task.html#await/2)，则得到结果的值(第 2 参数的超时为默认值 5000 )。 在不停止 APP 应用程序的执行的情况下，对于高负荷的处理非常有用。 另外，[`:timer.sleep/1`](http://erlang.org/doc/man/timer.html#sleep-1)是自变量在毫秒期间暂停处理的 erlang 的函数。

```
defmodule Math do
  def hypot(x, y) do
    :timer.sleep(3000)
    :math.sqrt(x * x + y * y)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> task = Task.async(Math, :hypot, [3, 4])
%Task{
  owner: #PID<0.87.0>,
  pid: #PID<0.94.0>,
  ref: #Reference<0.4236645349.3512991745.24823>
}
iex> Math.hypot(5, 12)
13.0  #<- 3 秒後
iex> Task.await(task)
5.0 
```

Enter fullscreen mode Exit fullscreen mode

# status

您可能希望要构建的 APP 应用程序具有状态。 例如，您可能想要保留 APP 应用程序设置，或分析文件并将其读取到内存中。

状态一般由过程来决定。 形成流程并循环，保持状态，交换信息。 现在，我们来制定一个新的流程，可以容纳键值对。

初始化函数(`start_link`)用`Task.start_link/1`创建过程，并向专用函数(`loop`)传递空地图。 `receive/1`方框在信息的键为`:get`时，用[`send/2`](https://hexdocs.pm/elixir/Kernel.html#send/2)发送信息。 然后，返回在`Map.get/3`中得到的密钥值。 如果键是`:put`，则用`Map.put/3`在地图上加上键和值。 请注意，它们都是通过递归调用函数来继续处理的。

```
defmodule KeyValue do
  def start_link do
    Task.start_link(fn -> loop(%{}) end)
  end
  defp loop(map) do
    receive do
      {:get, key, caller} ->
        send caller, Map.get(map, key)
        loop(map)
      {:put, key, value} ->
        loop(Map.put(map, key, value))
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

调用模块初始化的函数(`KeyValue.start_link`)时，映射仍为空。 因此，即使发送`:get`信息也得不到值。 当前进程的收件箱中什么都没有。

```
iex> {:ok, pid} = KeyValue.start_link
{:ok, #PID<0.91.0>}
iex> send(pid, {:get, :hello, self()})
{:get, :hello, #PID<0.87.0>}
iex> flush
nil
:ok 
```

Enter fullscreen mode Exit fullscreen mode

向进程发送`:put`消息时，状态会更新，并添加键和值。 之后，通过`:get`消息也能得到密钥的值吧。 如果知道 PID 的话，即使是其他的进程，也可以发送消息来操作状态。

```
iex> send pid, {:put, :hello, :world}
{:put, :hello, :world}
iex> send pid, {:get, :hello, self()}
{:get, :hello, #PID<0.87.0>}
iex> flush
:world
:ok 
```

Enter fullscreen mode Exit fullscreen mode

[使用`Process.register/2`](https://hexdocs.pm/elixir/Process.html#register/2)可以给 PID 命名并登录。 无论从哪个流程，都可以用那个名字发送信息。

```
iex> Process.register(pid, :kv)
true
iex> send :kv, {:get, :hello, self()}
{:get, :hello, #PID<0.87.0>}
iex> flush
:world
:ok 
```

Enter fullscreen mode Exit fullscreen mode

在进程中注册和保留状态和名称是 elixir APP 应用程序中经常使用的技术。 大多数情况下，我不会写我介绍的手动处理的代码。 Elixir 预先具备了很多抽象的做法。 例如，将状态抽象化的就是`Agent`。

# 代理

`Agent`是对保持状态的后台进程的抽象。 可以通过 APP 应用程序和节点的其他进程进行访问。 [`Agent.start_link/2`](https://hexdocs.pm/elixir/Agent.html#start_link/2)以链接到当前进程的`Agent`为首(第 2 参数的选项为默认值`[]`)。 返回值元组`{:ok, pid}`的第 2 要素是对`Agent`的状态的参照。

[根据`Agent.update/3`](https://hexdocs.pm/elixir/Agent.html#update/3)，可以用第 2 参数的函数更新第 1 参数的`Agent`的状态(第 3 参数的超时为默认值 5000 毫秒)。 而`Agentから値を取り出すのが`[`Agent.get/3`](https://hexdocs.pm/elixir/Agent.html#get/3)。 通过第 2 参数的函数处理并返回从第 1 参数的`Agent`得到的值(第 3 参数的超时为默认值 5000 毫秒)。

```
iex> {:ok, agent} = Agent.start_link(fn -> %{} end)
{:ok, #PID<0.112.0>}
iex> Agent.update(agent, fn map -> Map.put(map, :hello, :world) end)
:ok
iex> Agent.get(agent, fn map -> Map.get(map, :hello) end)
:world 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> {:ok, agent} = Agent.start_link(fn -> [1, 2, 3] end)
{:ok, #PID<0.105.0>}
iex> Agent.update(agent, fn state -> state ++ [4, 5] end)
:ok
iex> Agent.get(agent, &(&1 -- [2, 4]))
[1, 3, 5] 
```

Enter fullscreen mode Exit fullscreen mode

#### Elixir 入门彩票

*   [Elixir 入门 01 :编写代码并尝试](https://dev.to/gumi/elixir-01--2585)
*   [Elixir 入门 02 :型的基本](https://dev.to/gumi/elixir-02--30n1)
*   [Elixir 入门 03 :运算符的基本](https://dev.to/gumi/elixir-03--33im)
*   [Elixir 入门 04 :模式匹配](https://dev.to/gumi/elixir-04--1346)
*   [仙丹入門 05: 条件-情况/条件/条件](https://dev.to/gumi/elixir-05----casecondif-60o)
*   [Elixir 入门 06 :二进制和字符串以及字符列表](https://dev.to/gumi/elixir-06--35na)
*   [Elixir 入门 07 :关键词列表和地图](https://dev.to/gumi/elixir-07--39hi)
*   [Elixir 入门 08 :模块和函数](https://dev.to/gumi/elixir-08--1c4c)
*   [Introduction T0】 Elixir 09: Rethink](https://dev.to/gumi/elixir-09--1a0p)
*   [仙丹入門 10: EnumとStream](https://dev.to/gumi/elixir-10-enumstream-4fpb)
*   Elixir 入门 11 :流程
*   [Elixir 入门 12 :输入输出和文件系统](https://dev.to/gumi/elixir-12--4og6)
*   [仙丹入門 13: aliasとrequireおよびimport](https://dev.to/gumi/elixir-13-aliasrequireimport-55c1)
*   [Elixir 入门 14 :模块的属性](https://dev.to/gumi/elixir-14--3511)
*   [T0】 Elixir Beginner 15: Structure
*   [Elixir 入门 16 :协议](https://dev.to/gumi/elixir-16--lif)
*   [T0】 Elixir Beginner's Level 17: Record of Internal Package
*   [Elixir 入门 18 :锡吉尔](https://dev.to/gumi/elixir-18--5791)
*   [仙丹入門 19: tryとcatchおよびrescue](https://dev.to/gumi/elixir-19-trycatchrescue-50i8)
*   [Elixir 入门 20 :类型规格和行为](https://dev.to/gumi/elixir-20--j50)
*   [Elixir 入门 21 :调试](https://dev.to/gumi/elixir-21--21a1)
*   [Elixir 入门 22: Erlang 库](https://dev.to/gumi/elixir-22-erlang-2492)
*   [Elixir 入门 23 :下一步](https://dev.to/gumi/elixir-23--50ik)

##### Fanwai

*   [Elixir 入门:使用 Plug 为](https://dev.to/gumi/elixir-plug-40lb)
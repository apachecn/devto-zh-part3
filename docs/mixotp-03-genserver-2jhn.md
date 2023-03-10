# MixとOTP 03:根服务器

> 原文：<https://dev.to/gumi/mixotp-03-genserver-2jhn>

本文在 Elixir 官方网站的许可下，根据“[GenServer](https://elixir-lang.org/getting-started/mix-otp/genserver.html) ”的解说，加以修改，对在 Elixir 中使用`GenServer`的进程的登录和监视方法进行说明。

`Agent`是一个过程。 进程具有 PID，但没有名称。 不过，也可以用阿童木登录名字(参照“[Elixir 入门 11 :流程](https://dev.to/gumi/elixir-11--2mia)”“状态”)。 另外，[`Agent.start_link/2`](https://hexdocs.pm/elixir/Agent.html#start_link/2)在第 2 自变量中用`:name`选项命名原子。

```
iex> Agent.start_link(fn -> %{} end, name: :shopping)
{:ok, #PID<0.139.0>}
iex> KV.Bucket.put(:shopping, "milk", 1)
:ok
iex> KV.Bucket.get(:shopping, "milk")
1 
```

Enter fullscreen mode Exit fullscreen mode

但是，应该停止用原子来命名动态过程。 那就是将经常从外部客户端接收到的进程名称改为原子。 用户输入不应该是原子。 因为阿童木不会被垃圾回收。 一旦制作好的阿童木，就会保持不可再利用的状态。 如果将用户输入全部设为阿童木的话，系统内存很快就会用尽吧。

实际上，在内存不足之前，可能会达到 Erlang VM 拥有的原子的最大数量([默认值为 1，048，576](http://erlang.org/doc/efficiency_guide/advanced.html#system-limits))。 那样的话，系统应该会掉下来。

不要轻易使用内置的命名功能，创建流程与名称相关联的独特注册功能吧。 注册必须始终保持最新状态。 例如，当某个进程在错误中崩溃时，注册功能应该识别这种变化，并且不处理旧的输入。 Elixir 认为应该监视每个过程的注册。

[使用`GenServer`](https://hexdocs.pm/elixir/GenServer.html)，可以创建注册并监视进程的进程。 `GenServer`具备用于构建 Elixir 和 OTP 服务器的业务用的强大功能。 [使用`Registry`](https://hexdocs.pm/elixir/Registry.html)这个模块，也可以动态地生成本地拥有的名字。

# 第一个 GenServer

`GenServer`由两个要素组成。 这是[1]客户端 API 和[2]服务器回调。 可以将两个模块合并为一个模块，也可以分为客户端和服务器两个模块。 客户端和服务器分别在不同的进程中运行。 客户端在调用函数时与服务器交换消息。

在此，客户端 API 和服务器回调使用一个模块进行。 请在新的文件`lib/kv/registry.ex`中写入以下代码。

```
defmodule KV.Registry do
  use GenServer

  ## クライアントAPI
  @doc """
  登録を始める。
  """
  def start_link(opts) do
    GenServer.start_link(__MODULE__, :ok, opts)
  end

  @doc """
  `server`に納められた`name`のプロセスのpidを探す。

  プロセスがあれば`{：ok、pid}`を返し、見つからないときは`：error`を返す。
  """
  def lookup(server, name) do
    GenServer.call(server, {:lookup, name})
  end

  @doc """
  `server`に`name`の与えられたプロセスがあることを請け合う。
  """
  def create(server, name) do
    GenServer.cast(server, {:create, name})
  end

  ## サーバーコールバック
  def init(:ok) do
    {:ok, %{}}
  end

  def handle_call({:lookup, name}, _from, names) do
    {:reply, Map.fetch(names, name), names}
  end

  def handle_cast({:create, name}, names) do
    if Map.has_key?(names, name) do
      {:noreply, names}
    else
      {:ok, bucket} = KV.Bucket.start_link([])
      {:noreply, Map.put(names, name, bucket)}
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 客户端 API

**函数`start_link/1`**
[`GenServer.start_link/3`](https://hexdocs.pm/elixir/GenServer.html#start_link/3)传递并调用以下 3 个参数，开始链接到当前进程的新的`GenServer`进程。

*   第一个参数:实现服务器回调的模块。
    *   [`__MODULE__/0`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#__MODULE__/0):现行模块。
*   第二参数:初始化的值。
*   第三参数:定义服务器名称等的选项列表。 在这里，传递在`start_link/1`中接收到的列表。 默认值为空列表`[]`。

**发送到函数`lookup/2`和`create/2`**
`GenServer`的请求有`call`和`cast`两个。

*   `call`:是同步的，服务器必须对那些请求做出响应。
*   `cast`:异步，服务器不返回响应。

两个函数`lookup/2`及`create/2`分别通过[`GenServer.call/3`](https://hexdocs.pm/elixir/GenServer.html#call/3)和[`GenServer.cast/2`](https://hexdocs.pm/elixir/GenServer.html#cast/2)向服务器发送请求。 被传递的是第 2 参数的元组，`lookup/2`为`{:lookup, name}`，`create/2`为`{:create, name}`。 请求中经常使用可以发送多个值的元组。 在这种情况下，通常在第一个元素中定义请求的操作，然后将剩下的元素作为操作的参数。 另外，请求的元组必须分别与服务器回调`handle_call/3`和`handle_cast/2`的第 1 参数匹配。

#### 服务器回调

实现服务器端的各种回调，以确认服务器的初始化和退出，或者处理请求。 也可以省略回调。 在上述模块中，首先只添加了想尝试的处理。

**回调[`init/1`](https://hexdocs.pm/elixir/GenServer.html#c:init/1)**
`GenServer.start_link/3`的第 2 参数被传递，返回`{:ok, state}`。 `state`是新的天空地图`%{}`。 很清楚`GenServer`的 API 是如何划分客户端/服务器的吧。 `GenServer.start_link/3`在客户端被称为。 与此相对，在服务器上执行的回调为`init/1`。

**回叫[`handle_call/3`](https://hexdocs.pm/elixir/GenServer.html#c:handle_call/3)**
`call/2`的请求而安装的回叫。

*   第一参数:发送的请求。
*   第二个参数:接收请求的进程。
*   第三参数:当前服务器的状态。

返回值是`{:reply, reply, new_state}`形式的元组。 元组的第一元素`:reply`表示必须回复客户端。 第 2 要素`reply`是发送到客户端的内容，第 3 要素`new_state`是服务器的新状态。

**回叫[`handle_cast/2`](https://hexdocs.pm/elixir/GenServer.html#c:handle_cast/2)**
`cast/2`的请求而安装的回叫。

*   第一参数:发送的请求。
*   第二个参数:当前服务器的状态。

返回值是`{:noreply, new_state}`形式的元组。 在实际的 APP 应用中，恐怕不是用异步的`cast`，而是用同步的`call`来实现回调`:create`。 为了说明`cast`的回调是如何安装的，使用了`cast`。

回调`handle_call/3`和`handle_cast/2`返回的元组的形式也可以是其他形式。 此外，回调还包括[GenServer.terminate/2](https://hexdocs.pm/elixir/GenServer.html#c:terminate/2) 和[GenServer.code_change/3](https://hexdocs.pm/elixir/GenServer.html#c:code_change/3) 等。 详情请参阅“[GenServer behaviour](https://hexdocs.pm/elixir/GenServer.html) ”。

# 测试 GenServer

那么，写个测试确认一下`GenServer`是否按照预想的那样动作吧。 `GenServer`的测试与`Agent`的情况几乎没有变化。 通过`setup/1`的回调启动服务器，在该服务器上进行测试。 请在测试的文件`test/kv/registry_test.exs`中写入以下代码。

```
defmodule KV.RegistryTest do
  use ExUnit.Case, async: true

  setup do
    registry = start_supervised!(KV.Registry)
    %{registry: registry}
  end

  test "spawns buckets", %{registry: registry} do
    assert KV.Registry.lookup(registry, "shopping") == :error

    KV.Registry.create(registry, "shopping")
    assert {:ok, bucket} = KV.Registry.lookup(registry, "shopping")

    KV.Bucket.put(bucket, "milk", 1)
    assert KV.Bucket.get(bucket, "milk") == 1
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
$  mix test test/kv/registry_test.exs
.  Finished in 0.03 seconds
1 test, 0 failures

Randomized with seed 710912 
```

Enter fullscreen mode Exit fullscreen mode

与使用`Agent`的模块测试(参照“[Mix 和 OTP 02: Agent](https://qiita.com/fumio_nonaka/private/f1816b3ea9774e599bdf) ”“通过 ExUnit 的回调设置测试”)在`setup`块中有一个重要的不同。 在`start_link/1`中不注册进程，而是调用函数[`start_supervised！/2`](https://hexdocs.pm/ex_unit/ExUnit.Callbacks.html#start_supervised/2)开始。 将模块传递给参数。

`start_supervised！/2`函数通过调用`start_link/1`开始模块的进程。 最好使用此函数，因为在`ExUnit`开始下一个测试之前，注册过程一定会关闭。 这样，即使测试的状态取决于与下一个测试共享的资源，也可以避免影响下一个状态。

在测试中开始流程时，最好使用`start_supervised！`函数。 之前尝试过的`Agent`的测试，为了使用`start_supervised!/2`，最好改写`setup`块。

想要在 APP 逻辑中停止`GenServer`时，可以使用函数[`GenServer.stop/3`](https://hexdocs.pm/elixir/GenServer.html#stop/3)。 :

```
## クライアントAPI

@doc """
プロセスを止める。
"""
def stop(server) do
  GenServer.stop(server)
end 
```

Enter fullscreen mode Exit fullscreen mode

# 监控

制作的模块有一个问题。 如果在服务器上创建的进程停止或崩溃，就不能使用了。 但是，我不知道那个。 为了确认，在`test/kv/registry_test.exs`上加上测试吧。

```
test "removes buckets on exit", %{registry: registry} do
  KV.Registry.create(registry, "shopping")
  {:ok, bucket} = KV.Registry.lookup(registry, "shopping")
  Agent.stop(bucket)
  assert KV.Registry.lookup(registry, "shopping") == :error
end 
```

Enter fullscreen mode Exit fullscreen mode

[即使用`Agent.stop/3`](https://hexdocs.pm/elixir/Agent.html#stop/3)停止进程，名字也会保留下来，所以最后的`assert/1`会失败。

```
$ mix test test/kv/registry_test.exs
.

  1) test removes buckets on exit (KV.RegistryTest)
     test/kv/registry_test.exs:19
     Assertion with == failed
     code:  assert KV.Registry.lookup(registry, "shopping") == :error
     left:  {:ok, #PID<0.145.0>}
     right: :error
     stacktrace:
       test/kv/registry_test.exs:23: (test)

Finished in 0.04 seconds
2 tests, 1 failure

Randomized with seed 348772 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，需要的是监视每个制定的过程的登记。 确定监视器后，您将在每次进程结束时收到通知。 这样的话，也可以删除那个注册。 用`iex -S mix`打开控制台进行尝试。

[`Process.monitor/1`](https://hexdocs.pm/elixir/Process.html#monitor/1)返回的是将发送的消息与监视的参照相匹配的唯一参照。 停止`Agent`后，所有的信息都可以通过[`flush/0`](https://hexdocs.pm/elixir/StringIO.html#flush/1)清除。 返回的是`:DOWN`消息，显示监视的参照和停止的进程的 PID，以及结束的原因`:normal`。

```
iex> {:ok, pid} = KV.Bucket.start_link([])
{:ok, #PID<0.131.0>}
iex> Process.monitor(pid)
#Reference<0.5749347.3730309123.136703>
iex> Agent.stop(pid)
:ok
iex> flush()
{:DOWN, #Reference<0.5749347.3730309123.136703>, :process, #PID<0.131.0>,
 :normal}
:ok 
```

Enter fullscreen mode Exit fullscreen mode

正如上述测试所示，重新安装`lib/kv/registry.ex`的服务器回调吧。 首先，`GenServer`的状态需要两本词典。 一个是`name -> pid`，另一个是`ref -> name`。 接着，在`handle_cast/2`中通过`Process.monitor/1`的调用开始监视。 `handle_info/2`也必须改写以处理信息的新状态。 然后，又增加了回调[`handle_info/2`](https://hexdocs.pm/elixir/GenServer.html#c:handle_info/2)。 `:DOWN`这是因为收到消息后，会删除进程。 其他消息将被丢弃。

```
## サーバーコールバック
def init(:ok) do
  names = %{}
  refs = %{}
  # {:ok, %{}}
  {:ok, {names, refs}}
end

# def handle_call({:lookup, name}, _from, names) do
def handle_call({:lookup, name}, _from, {names, _} = state) do
  # {:reply, Map.fetch(names, name), names}
  {:reply, Map.fetch(names, name), state}
end  

# def handle_cast({:create, name}, names) do
def handle_cast({:create, name}, {names, refs}) do
  if Map.has_key?(names, name) do
    # {:noreply, names}
    {:noreply, {names, refs}}
  else
    # {:ok, bucket} = KV.Bucket.start_link([])
    {:ok, pid} = KV.Bucket.start_link([])
    ref = Process.monitor(pid)
    refs = Map.put(refs, ref, name)
    names = Map.put(names, name, pid)
    # {:noreply, Map.put(names, name, bucket)}
    {:noreply, {names, refs}}
  end
end

# 追加
def handle_info({:DOWN, ref, :process, _pid, _reason}, {names, refs}) do
  {name, refs} = Map.pop(refs, ref)
  names = Map.delete(names, name)
  {:noreply, {names, refs}}
end

# 追加
def handle_info(_msg, state) do
  {:noreply, state}
end 
```

Enter fullscreen mode Exit fullscreen mode

```
$  mix test test/kv/registry_test.exs
Compiling 3 files (.ex)
Generated kv app ..  Finished in 0.03 seconds
2 tests, 0 failures

Randomized with seed 405864 
```

Enter fullscreen mode Exit fullscreen mode

完全不接触客户端 API，只对服务器的实现进行了大幅修改就可以了。 这是明确划分服务器和客户端的优点之一。 下面总结列出包括客户端 API 在内的`KV.Registry`的代码(`lib/kv/registry.ex`) (基本上省略了文档和注释)。

```
defmodule KV.Registry do
  use GenServer

  ## クライアントAPI
  def start_link(opts) do
    GenServer.start_link(__MODULE__, :ok, opts)
  end

  def lookup(server, name) do
    GenServer.call(server, {:lookup, name})
  end

  def create(server, name) do
    GenServer.cast(server, {:create, name})
  end

  ## サーバーコールバック
  def init(:ok) do
    names = %{}
    refs = %{}
    {:ok, {names, refs}}
  end

  def handle_call({:lookup, name}, _from, {names, _} = state) do
    {:reply, Map.fetch(names, name), state}
  end  

  def handle_cast({:create, name}, {names, refs}) do
    if Map.has_key?(names, name) do
      {:noreply, {names, refs}}
    else
      {:ok, pid} = KV.Bucket.start_link([])
      ref = Process.monitor(pid)
      refs = Map.put(refs, ref, name)
      names = Map.put(names, name, pid)
      {:noreply, {names, refs}}
    end
  end

  def handle_info({:DOWN, ref, :process, _pid, _reason}, {names, refs}) do
    {name, refs} = Map.pop(refs, ref)
    names = Map.delete(names, name)
    {:noreply, {names, refs}}
  end

  def handle_info(_msg, state) do
    {:noreply, state}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

# `call`和`cast`以及`info`

到目前为止，作为回调，使用了`handle_call/3`和`handle_cast/2`以及`handle_info/2`这 3 个。 为了决定什么时候使用每个，应该考虑的事情如下。

*   `handle_call/3`:必须用于同步请求。 等待服务器响应时默认选择。 [有助于背压](https://www.reactivemanifesto.org/ja/glossary#Back-Pressure)的结构。
*   `handle_cast/2`:必须用于不用等待响应的异步请求。 `cast`甚至不确认服务器是否收到了消息。 所以，不要太用比较好吧。 例如，在上述模块的`create/2`函数的规定中，使用`cast/2`是不好的例子。 使用`call/2`比较合适。
*   `handle_info/2`:用于服务器接收的信息中，不是用`GenServer.call/2`或`GenServer.cast/2`发送的信息。 [也包括由`send/3`](https://hexdocs.pm/elixir/Process.html#send/3)发送的普通信息。 上述模块监控到的`:DOWN`消息也是其中之一。

包括`call/2`发送的内容在内，所有的信息都会传递给`handle_info/2`。 其中可能会收到意想不到的消息。 因此，必须像上述模块那样，设置接收所有情况的`handle_info/2`。 当收到不匹配的消息时，可以防止进程崩溃。 关于`handle_call/3`和`handle_cast/2`，没有那样的担心。 因为消息是用`GenServer`的 API 发送的。 不明白的消息大概是开发者的错误吧。

可以看出`call`与`cast`及`info`的区别、返回值等，可以看出，[Benjamin Tan Wei Hao](http://benjamintan.io/) 表示“[THE GenServer CHEATSHEET](https://raw.githubusercontent.com/benjamintanweihao/elixir-cheatsheets/master/GenServer_CheatSheet.pdf) ””

# 监视器和链接

如何区分使用监视器和链接呢？

首先，链路是双向的。 如果链接两个进程，其中一个崩溃，另一个也会崩溃(除非陷阱结束)。 与此相对，监视器是单向的。 正在监视的进程只是从被监视的对象接收通知。 也就是说，如果想联合崩溃的话，只要想知道链接、崩溃和结束等，就使用监视器。

上述模块的`handle_cast/2`中，同时安装了进程的链接和监视器。

```
{:ok, pid} = KV.Bucket.start_link([])
ref = Process.monitor(pid) 
```

Enter fullscreen mode Exit fullscreen mode

这是不合适的，因为虽然正在监视进程，但崩溃会协同工作。 通常，我们不会直接制定流程，而是委托给管理人员。

#### Mix 和 OTP 也抽签

*   [尝试使用 Mix 和 OTP 01:mix](https://dev.to/gumi/mixotp-01-mix-dg1)
*   MixとOTP 02:经纪人
*   MixとOTP 03:根服务器
*   [Mix 和 OTP 04 :管理员和 APP 沟通](https://dev.to/gumi/mix-otp-04-bj0)
*   [Mix 和 OTP 05 :动态管理员](https://dev.to/gumi/mix-otp-05-l3p)
*   [MixとOTP 06: ETS](https://dev.to/gumi/mix-otp-06-ets-ol3)
*   [Mix 和 OTP 07 :依存关系和模糊项目](https://dev.to/gumi/mix-otp-07-1p5k)
*   MixとOTP 08: Taskとgen_tcp
*   [Mix 和 OTP 09: DocTest 和 with 的模式匹配](https://dev.to/gumi/mix-otp-09-doctest-with-533g)
*   [Mix 和 OTP 10 :分散处理的 Task 和设定](https://dev.to/gumi/mix-otp-10-3i31)
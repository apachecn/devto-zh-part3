# MixとOTP 02:特工

> 原文：<https://dev.to/gumi/mixotp-02-agent-3n9g>

本文在 Elixir 官方网站的许可下，根据“[Agent](https://elixir-lang.org/getting-started/mix-otp/agent.html) ”的解说，加以修改，对 Elixir 的`Agent`和`ExUnit`的测试进行说明。

练习用的代码以模块`KV`加上子模块的形式进行。

```
$  mix new kv --module KV 
```

Enter fullscreen mode Exit fullscreen mode

> 通过“Mix 和 OTP”系列制作的 APP 产品是[`josevalim/kv_umbrella`](https://github.com/josevalim/kv_umbrella)。

处理流程，请根据需要参考“[Elixir 入门 11 :流程](https://dev.to/gumi/elixir-11--2mia)”。

# 状态的操作

Elixir 和 OTP 提供了以下抽象功能:

*   [`Agent`](https://hexdocs.pm/elixir/Agent.html):包含状态的单纯的包装器。
*   [`GenServer`](https://hexdocs.pm/elixir/GenServer.html):封装状态的“通用服务器”([进程](https://dev.to/gumi/elixir-11--2mia))。 支持同步和异步调用以及代码的重新装入等。
*   [`Task`](https://hexdocs.pm/elixir/Task.html):非同步的处理单位。 有时会生成进程，有时之后会得到结果。

这些功能是在流程上实现的。 而且，使用了 VM 所具备的`send`、`receive`、`spawn`、`link`等基本操作。

# 代理

`Agent`是包含状态的单纯的包装器。 如果你想从过程中得到的只是状态，那就最合适了。 在项目内开始`iex`的会话吧。

```
$  iex -S mix 
```

Enter fullscreen mode Exit fullscreen mode

首先，开始新的`Agent`，以空列表`[]`的状态进行初始化。 然后，更新`Agent`，将新项目添加到列表的开头。 在此基础上，得到了更新后的列表作为值。 最后`Agent`停止，结束过程。

```
iex> {:ok, agent} = Agent.start_link(fn -> [] end)
{:ok, #PID<0.138.0>}
iex> Agent.update(agent, fn list -> ["eggs" | list] end)
:ok
iex> Agent.get(agent, fn list -> list end)
["eggs"]
iex> Agent.stop(agent)
:ok 
```

Enter fullscreen mode Exit fullscreen mode

*   [`Agent.start_link/2`](https://hexdocs.pm/elixir/Agent.html#start_link/2):用自变量的函数制作链接到当前流程的`Agent`。
*   [`Agent.update/3`](https://hexdocs.pm/elixir/Agent.html#update/3):用第 2 参数的函数更新第 1 参数的`Agent`的状态。 函数在参数中接收的是`Agent`的当前状态。
*   [`Agent.get/3`](https://hexdocs.pm/elixir/Agent.html#get/3):用第 2 参数的函数从第 1 参数的`Agent`接收值。
*   [`Agent.stop/3`](https://hexdocs.pm/elixir/Agent.html#stop/3):同步停止`Agent`。

在模块中安装`Agent`吧。 新制作的模块是`KV.Bucket`。 但是，我会在实现之前写测试。 在项目中制作并记述测试文件`test/kv/bucket_test.exs`的代码如下(测试文件的扩展名为`.exs`)。

测试([`test/3`](https://hexdocs.pm/ex_unit/ExUnit.Case.html#test/3))首先，将空列表传递给模块`KV.Bucket`的`start_link/1`并调用。 接着，依次调用`get/2`和`put/3`，分别用[`assert/1`](https://hexdocs.pm/ex_unit/ExUnit.Assertions.html#assert/1)确认结果。

请注意，已经将选项`async: true`传递给了`ExUnit.Case`。 此选项允许测试异步进行。 这意味着它将在多核计算机上与其他异步(`async: true`)测试用例并行运行。 对加快测试套件的速度非常有效。 但是，请仅在不依赖或更改全局值时使用异步设置。 例如，在测试时写入文件系统或浏览数据库时，如果不进行同步处理(不使用`:async`)，则会导致测试之间的冲突状态。

```
defmodule KV.BucketTest do
  use ExUnit.Case, async: true

  test "stores values by key" do
    {:ok, bucket} = KV.Bucket.start_link([])
    assert KV.Bucket.get(bucket, "milk") == nil

    KV.Bucket.put(bucket, "milk", 3)
    assert KV.Bucket.get(bucket, "milk") == 3
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个测试现在当然要失败了。 因为还没有写模块`KV.Bucket`本身( module KV.Bucket is not available )。

```
$  mix test test/kv/bucket_test.exs
 1) test stores values by key (KV.BucketTest)
     test/kv/bucket_test.exs:4
     ** (UndefinedFunctionError) function KV.Bucket.start_link/1 is undefined (module KV.Bucket is not
 available)
     code: {:ok, bucket} = KV.Bucket.start_link([])
     stacktrace:
       KV.Bucket.start_link([])
       test/kv/bucket_test.exs:5: (test)

Finished in 0.02 seconds
1 test, 1 failure

Randomized with seed 980520 
```

Enter fullscreen mode Exit fullscreen mode

`KV.Bucket`模块确定为新文件`lib/kv/bucket.ex`。 代码如下所示。 首先，在[`use/2`](https://hexdocs.pm/elixir/Kernel.html#use/2)中，在上下文中使用模块`Agent`。

接下来，`start_link/1`函数开始`Agent`。 在`start_link/1`中通常会作为选项接收列表。 目前，选项只需要确定就可以不用了。 从函数主体调用`Agent.start_link/2`，将无名函数传递给参数，返回`Agent`的初始状态。

在`Agent`中用地图存储键和值。 `get/2`和`put/3`在`Agent`的 API 中操作映射的值，参数的函数中使用了捕获运算符`&`(《[Elixir 入门 08 :模块和函数](https://dev.to/gumi/elixir-08--1c4c))的“函数捕获”

```
defmodule KV.Bucket do
  use Agent
  @doc """
  新たな`Bbucket`をつくる。
  """
  def start_link(_opts) do
    Agent.start_link(fn -> %{} end)
  end
  @doc """
  `bucket`から`key`で値を得る。
  """
  def get(bucket, key) do
    Agent.get(bucket, &Map.get(&1, key))
  end
  @doc """
  `bucket`の`key`に`value`を与える。
  """
  def put(bucket, key, value) do
    Agent.update(bucket, &Map.put(&1, key, value))
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`KV.Bucket`确定了模块，也安装了使用的函数，所以测试成功。

```
$  mix test test/kv/bucket_test.exs
Compiling 1 file (.ex) .  Finished in 0.02 seconds
1 test, 0 failures

Randomized with seed 313891 
```

Enter fullscreen mode Exit fullscreen mode

# 通过 ExUnit 的回调进行测试的设定

在使用`Agent`的模块测试中，必须预先启动`Agent`。 为了避免每次测试都启动，`ExUnit`有回调。 决定回调的是[`setup/1`宏](https://hexdocs.pm/ex_unit/ExUnit.Callbacks.html#setup/1)。 `setup/1`的回调在每个测试之前执行与测试相同的过程。

传递给测试的是回调返回的映射的 PID。 此时将使用测试的上下文。 当回调返回`%{bucket: bucket}`时，`ExUnit`会将地图引入测试上下文。 因为测试上下文也是地图，所以通过模式匹配可以参照测试中的`%{bucket: bucket}`。

```
defmodule KV.BucketTest do
  use ExUnit.Case, async: true

  setup do  # setupを追加
    {:ok, bucket} = KV.Bucket.start_link([])
    %{bucket: bucket}
  end

  # test "stores values by key" do
  test "stores values by key", %{bucket: bucket} do
    # {:ok, bucket} = KV.Bucket.start_link([])
    assert KV.Bucket.get(bucket, "milk") == nil

    KV.Bucket.put(bucket, "milk", 3)
    assert KV.Bucket.get(bucket, "milk") == 3
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

详情请参阅“[`ExUnit.Case`](https://hexdocs.pm/ex_unit/ExUnit.Case.html)”。 另外，关于回调在“[`ExUnit.Callbacks`](https://hexdocs.pm/ex_unit/ExUnit.Callbacks.html)”中进行了解说。

# 代理的其他动作

用`Agent.get/3`得到值，用`Agent.update/3`更新状态之外，也可以用一个函数的调用进行两个处理。 那就是[`Agent.get_and_update/3`](https://hexdocs.pm/elixir/Agent.html#get_and_update/3)。 在返回当前值的同时，状态会被更新。 根据这个函数从`Agent`中除去键的是下面添加的`KV.Bucket.delete/2`函数。

```
@doc """
`bucket`から`key`を除きます。`key`が存在していたら、その値を返します。
"""
def delete(bucket, key) do
  Agent.get_and_update(bucket, &Map.pop(&1, key))
end 
```

Enter fullscreen mode Exit fullscreen mode

作为练习，试着增加新函数`KV.Bucket.delete/2`的测试吧。 关于`Agent`，请阅读模块“[`Agent`](https://hexdocs.pm/elixir/Agent.html)”的解说。

# 代理的客户端/服务器

先谈一下`Agent`中的客户端和服务器的区别。 因为将上述`KV.Bucket.delete/2`作为例子使用，所以为了便于修改，不捕捉函数，而是调用`Map.pop/3`。

```
def delete(bucket, key) do
  Agent.get_and_update(bucket, fn dict ->
    Map.pop(dict, key)
  end)
end 
```

Enter fullscreen mode Exit fullscreen mode

在传递给`Agent`的函数中进行的处理都是通过该`Agent`的过程来处理的。 `Agent`的进程传递消息，因此该进程称为服务器。 在参数的函数之外处理的是客户端。 两个的区别很重要。 当有负荷大的处理时，必须考虑将执行作为客户端还是服务器。

如果在服务器上进行了耗时的处理，则对该服务器的其他请求将等待处理完成。 然后，根据客户端的不同，可能会发生超时。

```
def delete(bucket, key) do
  Process.sleep(1000) # クライアントを待たせる
  Agent.get_and_update(bucket, fn dict ->
    Process.sleep(1000) # サーバーを待たせる
    Map.pop(dict, key)
  end)
end 
```

Enter fullscreen mode Exit fullscreen mode

#### Mix 和 OTP 也抽签

*   [尝试使用 Mix 和 OTP 01:mix](https://dev.to/gumi/mixotp-01-mix-dg1)
*   MixとOTP 02:特工
*   MixとOTP 03:基因服务器
*   [Mix 和 OTP 04 :管理员和 APP 沟通](https://dev.to/gumi/mix-otp-04-bj0)
*   [Mix 和 OTP 05 :动态管理员](https://dev.to/gumi/mix-otp-05-l3p)
*   [MixとOTP 06: ETS](https://dev.to/gumi/mix-otp-06-ets-ol3)
*   [Mix 和 OTP 07 :依存关系和模糊项目](https://dev.to/gumi/mix-otp-07-1p5k)
*   MixとOTP 08: Taskとgen_tcp
*   [Mix 和 OTP 09: DocTest 和 with 的模式匹配](https://dev.to/gumi/mix-otp-09-doctest-with-533g)
*   [Mix 和 OTP 10 :分散处理的 Task 和设定](https://dev.to/gumi/mix-otp-10-3i31)
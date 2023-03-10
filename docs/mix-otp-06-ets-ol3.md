# MixとOTP 06:外星人

> 原文：<https://dev.to/gumi/mix-otp-06-ets-ol3>

本文在 Elixir 官网的许可下，根据“[ETS](https://elixir-lang.org/getting-started/mix-otp/ets.html) ”的解说，加以修改，对 Elixir 中 ETS 的使用方法进行说明。

要寻找进程，必须向那些注册方进程发送消息。 如果同时由多个进程访问，注册方进程将成为瓶颈。 现在，我们来学习一下电子表格存储( ets )，并尝试将其用作缓存机制。

> 请不要突然将 ETS 用于缓存。 首先，我们要记录和分析 APP 应用的性能，并弄清楚哪些部分是瓶颈。 在此基础上，您必须决定应该缓存什么，以及应该缓存什么。 下面介绍的是在需要缓存时如何使用 ETS。

# 用 ETS 缓存

使用 ETS，Elixir 中的任何项都可以存储在内存中的表中。 ETS 工作台在 Erlang 的[`:ets`模块](http://erlang.org/doc/man/ets.html)中操作。

在制作 ETS 表的[`ets.new/2`](http://erlang.org/doc/man/ets.html#new-2)中，给与两个自变量。 这是表的名称和选项的列表。 以下代码是可选的，定义了表的类型和权限。 类型`:set`的表不能复制密钥。 只有创建表的进程才能写入访问权限为`:protected`的表。 可以从其他进程读取表。 不过，这两个是默认值。 因此，接下来省略这些。

```
iex> table = :ets.new(:buckets_registry, [:set, :protected])
#Reference<0.2154069976.707395585.80154>
iex> self()
#PID<0.133.0>
iex> :ets.insert(table, {"foo", self()})
true
iex> :ets.lookup(table, "foo")
[{"foo", #PID<0.133.0>}] 
```

Enter fullscreen mode Exit fullscreen mode

如果给制作的 ETS 表赋予`:named_table`的选项，就可以按名称参照表了。

```
iex> :ets.new(:buckets_registry, [:named_table])
:buckets_registry
iex> :ets.insert(:buckets_registry, {"foo", self()})
true
iex> :ets.lookup(:buckets_registry, "foo")
[{"foo", #PID<0.133.0>}] 
```

Enter fullscreen mode Exit fullscreen mode

`KV.Registry`改写模块，使其可以使用 ETS 表。 首先，在开始注册过程的`start_link/1`中，作为必须的参数传递名字。 这就是 ETS 表和进程自身的名称。 两个名字分别存放在不同的地方，所以不会产生重复的问题。 请如下修改`lib/kv/registry.ex`的客户端 API 的实现。

```
## クライアントAPI
@doc """
登録を始める。

引数のオプションには`:name`が必須。
"""
def start_link(opts) do
  # [1]GenServerのintに名前を渡す。
  server = Keyword.fetch!(opts, :name)
  # GenServer.start_link(__MODULE__, :ok, opts)
  GenServer.start_link(__MODULE__, server, opts)
end

@doc """
`server`に納められた`name`のプロセスのpidを探す。

プロセスがあれば`{：ok、pid}`を返し、見つからないときは`：error`を返す。
"""
def lookup(server, name) do
  # [2]lookupはサーバーでなく、ETSで直接行う。
  # GenServer.call(server, {:lookup, name})
  case :ets.lookup(server, name) do
    [{^name, pid}] -> {:ok, pid}
    [] -> :error
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`KV.Registry.lookup/2`现在不将请求发送到服务器，而是直接从 ETS 表读取。 ETS 表由所有进程共享。 这是即将实现的缓存机制的基础。

为了使高速缓存正常工作，ETS 表以服务器回调的`init/1`为默认访问权限`:protected`开始。 这意味着它在客户端进程中是只读的。 `KV.Registry`只有进程可以写入。 `read_concurrency: true`选项是为了优化同时读取的操作。

```
## サーバーコールバック
# def init(:ok) do
def init(table) do
  # [3]マップnamesをETSテーブルで置き替える。
  # names = %{}
  names = :ets.new(table, [:named_table, read_concurrency: true])
  refs = %{}
  {:ok, {names, refs}}
end

# [4]lookupのコールバックhandle_callは削除。
# def handle_call({:lookup, name}, _from, {names, _} = state) do
#   {:reply, Map.fetch(names, name), state}
# end

def handle_cast({:create, name}, {names, refs}) do
  # [5]マップでなくETSテーブルに読み書きする。
  # if Map.has_key?(names, name) do
  case lookup(names, name) do
    {:ok, _pid} ->
      {:noreply, {names, refs}}
  # else
    :error ->
      {:ok, pid} = DynamicSupervisor.start_child(KV.BucketSupervisor, KV.Bucket)
      ref = Process.monitor(pid)
      refs = Map.put(refs, ref, name)
      # names = Map.put(names, name, pid)
      :ets.insert(names, {name, pid})
      {:noreply, {names, refs}}
  end
end

def handle_info({:DOWN, ref, :process, _pid, _reason}, {names, refs}) do
  # [6]マップでなくETSテーブルから削除する。
  {name, refs} = Map.pop(refs, ref)
  # names = Map.delete(names, name)
  :ets.delete(names, name)
  {:noreply, {names, refs}}
end 
```

Enter fullscreen mode Exit fullscreen mode

现在需要`:name`选项才能启动注册过程。 此外，对于`lookup/2`等操作，必须将名称作为参数传递，而不是 PID。 这是为了用该名称引用 ETS 表。 因此，`test/kv/registry_test.exs`的测试也请将`setup`函数改写如下。

```
# setup do
setup context do
  # registry = start_supervised!(KV.Registry)
  _ = start_supervised!({KV.Registry, name: context.test})
  # %{registry: registry}
  %{registry: context.test}
end 
```

Enter fullscreen mode Exit fullscreen mode

只有这个修改是不通过测试的。 例如，“spawns buckets”测试可能会显示以下错误消息: `KV.Registry.lookup/2`没有返回期待的值。 这是因为以下两者之间存在分歧。

1.  我把 ETS 表作为缓存进行了提前的优化。
2.  `lookup/2`调用了`cast/2`(使用`call/2`比较合适)。

```
 2) test spawns buckets (KV.RegistryTest)
     test/kv/registry_test.exs:
     match (=) failed
     code:  assert {:ok, bucket} = KV.Registry.lookup(registry, "shopping")
     right: :error
     stacktrace:
       test/kv/registry_test.exs: (test) 
```

Enter fullscreen mode Exit fullscreen mode

# Competing status

Elixir 的开发也会发生竞争状态。 但是，因为被抽象化为默认情况下不共享，所以容易找出竞争的主要原因。

在测试中，在显示对 ETS 表所做的操作及其变化之前会出现时间差。 在测试中被期待的是，处理将如下进行。

1.  调用`KV.Registry.create(registry, "shopping")`。
2.  注册进程创建子进程，并更新缓存表。
3.  在`KV.Registry.lookup(registry, "shopping")`中从表中参照信息。
4.  返回`{:ok, bucket}`。

但是，`KV.Registry.create/2`是`cast/2`的操作。 然后，在未实际写入表中之前，命令会返回。 也就是说，处理进行得如下。

1.  调用`KV.Registry.create(registry, "shopping")`。
2.  在`KV.Registry.lookup(registry, "shopping")`中从表中参照信息。
3.  因为还没有信息，所以回答的是`:error`。
4.  注册进程创建子进程，并更新缓存表。

修正该失败的方法是，`KV.Registry.create/2`使用`call/2`而不是`cast/2`进行同步处理。 现在，客户端必须在表完成更改后才能继续处理。 `KV.Registry`请如下改写模块的回调。

将回调`handle_cast/2`改为`handle_call/3`。 返回值中必须包含所创建进程的 PID。 一般来说，在 Elixir 的开发中，`call/2`比`cast/2`更受欢迎。 因为可以切实得到响应，进行处理。 没有充分的必要性就使用`cast/2`，这是提前的优化吧。

```
def create(server, name) do
  # GenServer.cast(server, {:create, name})
  GenServer.call(server, {:create, name})
end

# def handle_cast({:create, name}, {names, refs}) do
def handle_call({:create, name}, _from, {names, refs}) do
  # [5]マップでなくETSテーブルに読み書きする。
  case lookup(names, name) do
    # {:ok, _pid} ->
    {:ok, pid} ->
      # {:noreply, {names, refs}}
      {:reply, pid, {names, refs}}
    :error ->
      {:ok, pid} = DynamicSupervisor.start_child(KV.BucketSupervisor, KV.Bucket)
      ref = Process.monitor(pid)
      refs = Map.put(refs, ref, name)
      :ets.insert(names, {name, pid})
      # {:noreply, {names, refs}}
      {:reply, pid, {names, refs}}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

重新测试一下吧。 在`mix test`命令中添加`--trace`选项进行尝试。 当测试发生死锁或冲突时，此选项有效。 同步进行所有测试(`async: true`无效)，提供测试的详细信息。 这样，可能会出现以下障碍。

```
$ mix test test/kv/registry_test.exs --trace

  1) test removes buckets on exit (KV.RegistryTest)
     test/kv/registry_test.exs:19
     Assertion with == failed
     code: KV.Registry.lookup(registry, "shopping") == :error
     lhs:  {:ok, #PID<0.109.0>}
     rhs:  :error
     stacktrace:
       test/kv/registry_test.exs:23 
```

Enter fullscreen mode Exit fullscreen mode

根据错误消息，表中的进程应该已经没有了，但似乎仍然存在。 这个问题与之前解决的竞争状态相反。 刚才的问题是，从创建进程的命令到表格更新之间的延迟。 这次，在停止进程后，从表中删除之前会出现延迟。 也就是说，这也是竞争状态。 结果，竞争可能不会导致障碍。 但是，确实有延迟。

很遗憾，没有与`handle_info/2`对应的同步处理的函数。 因此，无法用同步操作替换 ETS 表的更新。 必须考虑在流程停止时，注册流程一定会发送`:DOWN`的通知的方法。

简单的做法是向注册过程发送同步请求。 然后，将按顺序处理消息。 如果注册进程响应在调用`Agent.stop/3`之后发送的请求，则说明已经处理了`:DOWN`消息。 `KV.Registry.create/2`是同步的要求。 在接下来的两个测试中，在`Agent.stop/3`被调用之前制作虚拟的流程吧。 这样就不会发生竞争状态引起的故障。

```
test "removes buckets on exit", %{registry: registry} do
  KV.Registry.create(registry, "shopping")
  {:ok, bucket} = KV.Registry.lookup(registry, "shopping")
  Agent.stop(bucket)
  # 登録プロセスに:DOWNメッセージを遅らせるための呼び出し
  _ = KV.Registry.create(registry, "bogus")
  assert KV.Registry.lookup(registry, "shopping") == :error
end

test "removes bucket on crash", %{registry: registry} do
  KV.Registry.create(registry, "shopping")
  {:ok, bucket} = KV.Registry.lookup(registry, "shopping")
  # プロセスを正常でない理由で止める
  Agent.stop(bucket, :shutdown)
  # 登録プロセスに:DOWNメッセージを遅らせるための呼び出し
  _ = KV.Registry.create(registry, "bogus")
  assert KV.Registry.lookup(registry, "shopping") == :error
end 
```

Enter fullscreen mode Exit fullscreen mode

以上是作为缓存机制的 ETS 的使用方法。 表中的数据可以从任何进程读取，写入是由一个进程按顺序进行的。 重要的是，如果异步读取数据，则必须小心可能会发生冲突。

实际上，需要注册动态过程时，应该使用 Elixir 的模块之一[`Registry`](https://hexdocs.pm/elixir/Registry.html)。 该模块具有与在此使用`GenServer`和`:ets`制作的 APP 应用相似的功能。 而且，读写也可以同时进行。 40 个核心的计算机也进行了性能指标评测，可以扩展到所有核心(“Elixir v1.4 released”的“[Registry](https://elixir-lang.org/blog/2017/01/05/elixir-v1-4-0-released/#registry) ”链接的“[comparing registry”)](https://docs.google.com/spreadsheets/d/1MByRZJMCnZ1wPiLhBEnSRRSuy1QXp8kr27PIOXO3qqg/edit#gid=0)

#### Mix 和 OTP 也抽签

*   [尝试使用 Mix 和 OTP 01:mix](https://dev.to/gumi/mixotp-01-mix-dg1)
*   MixとOTP 02:经纪人
*   MixとOTP 03:基因服务器
*   [Mix 和 OTP 04 :管理员和 APP 沟通](https://dev.to/gumi/mix-otp-04-bj0)
*   [Mix 和 OTP 05 :动态管理员](https://dev.to/gumi/mix-otp-05-l3p)
*   MixとOTP 06:外星人
*   [Mix 和 OTP 07 :依存关系和模糊项目](https://dev.to/gumi/mix-otp-07-1p5k)
*   MixとOTP 08: Taskとgen_tcp
*   [Mix 和 OTP 09: DocTest 和 with 的模式匹配](https://dev.to/gumi/mix-otp-09-doctest-with-533g)
*   [Mix 和 OTP 10 :分散处理的 Task 和设定](https://dev.to/gumi/mix-otp-10-3i31)
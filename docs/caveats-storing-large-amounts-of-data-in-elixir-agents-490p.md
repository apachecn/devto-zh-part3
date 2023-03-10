# 在酏剂中存储大量数据的注意事项

> 原文：<https://dev.to/kabisasoftware/caveats-storing-large-amounts-of-data-in-elixir-agents-490p>

最近在做一个 Elixir 项目时，我遇到了一个有趣的问题，代理导致了大量的资源使用。请继续阅读，了解发生了什么。

## 仙丹中有哪些药剂？

> 代理是对状态的简单抽象。
> 
> 在 Elixir 中，通常需要共享或存储状态，这些状态必须在不同的时间点从不同的进程或由同一进程访问。
> 
> [`Agent`](https://hexdocs.pm/elixir/Agent.html#content) 模块提供了一个基本的服务器实现，允许通过一个简单的 API 检索和更新状态。

Elixir 是一种不可变的语言，默认情况下不共享任何内容。这有很多好处，但也意味着当你想在进程间共享数据时，你需要做一些额外的工作。幸运的是，Elixir 提供了许多伟大的构建模块来实现这一点，如[代理](https://hexdocs.pm/elixir/Agent.html)、 [ETS](https://elixir-lang.org/getting-started/mix-otp/ets.html) 和[记忆](https://elixirschool.com/en/lessons/specifics/mnesia/)。

## 那么有什么问题呢？

有两种方法可以使用存储在代理中的状态:

1.  通过在代理程序中操作数据表单:

```
# Compute in the agent/server
def get_something(agent) do
  Agent.get(agent, fn state -> do_something_expensive(state) end)
end 
```

1.  通过将数据拉入客户端进程并在那里对其进行操作:

```
# Compute in the client
def get_something(agent) do
  Agent.get(agent, & &1) |> do_something_expensive()
end 
```

如果你看代码，差别是非常微妙的。然而，行为上的差异是**而不是**微妙的。

在方法#1 中，数据将保留在代理进程中。但是，如果您在那里执行开销很大的操作，代理将在整个操作期间被阻塞，这意味着在操作完成之前，任何其他进程都不能访问数据。使用这种模型来响应 HTTP 请求会降低性能。

在方法#2 中，代理不会被阻塞，但是数据将被复制到正在访问数据的进程中。当数据量很小时，这不是一个真正的问题，但如果你开始存储大量的数据，这将变得非常昂贵。

## 现实生活中的例子

这种影响可能是巨大的，我将在下面的案例中演示。

在我正在做的项目中，我们在一个代理中存储了一组规则。规则是一个有 27 个字段的结构，我们在代理中存储了大约 5000 条规则。对于每个请求，有一个 HTTP 端点使用这些规则来确定响应。

在一段时间内，这很好，但是当负载开始增加时，我们注意到服务器内存不足。为了调试这个问题，我开始使用 [wrk](https://github.com/wg/wrk) 在端点上添加负载。结果如下:

```
Running 1m test @ http://localhost:4001
  4 threads and 1000 connections
  Thread Stats Avg Stdev Max +/- Stdev
    Latency 2.23s 553.43ms 3.00s 57.78%
    Req/Sec 59.72 121.98 680.00 88.64%
  Latency Distribution
     50% 2.24s
     75% 2.67s
     90% 2.88s
     99% 3.00s
  5551 requests in 1.00m, 11.80MB read
  Socket errors: connect 0, read 5971, write 3, timeout 5506
  Non-2xx or 3xx responses: 4575
Requests/sec: 92.38
Transfer/sec: 201.05KB 
```

如您所见，每秒处理 92 个请求，许多请求超时(超过 3 秒)。在测试过程中，Elixir 进程消耗了大约 **10GB 的内存**。

## 方案

正如我们在上一节中看到的，在代理中存储这么多数据需要大量内存，坦率地说，性能并不好。

通过查看代码和代理文档，我很快意识到这个问题的根本原因是所有规则都被复制到处理 HTTP 请求的进程中，对于每个请求。那么如何才能防止这种情况呢？

“不共享任何东西”是 Elixir/Erlang 的一个非常核心的原则，所以简单的回答是，如果您想在进程之间共享数据，您不能阻止数据被复制。这影响了在内存中存储数据的所有方式，所以不仅仅是代理。

有变通办法，比如 [fast_global](https://github.com/discordapp/fastglobal) 。Fastglobal 通过在运行时动态编译一个模块来工作，但是它也不是没有 T2 的缺点。

因此，解决方案是确保数据不会在进程间共享。有多种方法可以做到这一点。我采用的方法是创建一个工人进程池(用 [Poolboy](https://elixirschool.com/en/lessons/libraries/poolboy/) )来处理规则的执行。当一个 HTTP 请求进来时，规则匹配由一个工作进程处理。

在代码中大致是这样的(简化的):

```
defmodule Worker do
  use GenServer

  def start_link(_) do
    GenServer.start_link( __MODULE__ , nil, [])
  end

  def init(_) do
    rules = State.get()
    {:ok, rules}
  end

  def handle_call({:match_rules, input}, _from, rules) do
    matches = match_rules(rules, input)
    {:reply, matches, rules}
  end
end 
```

当一个 worker 启动时，它将规则从代理(`State`是一个包装代理的模块)加载(复制)到 worker 进程中。每个工作进程都包含一个规则副本，因此内存使用是可预测的。

如果规则在运行时改变了，进程就会被终止并重新启动，这样新的规则就会被自动使用。Poolboy 负责启动 N 个工人，并从池中选择一个工人。

## 最终结果

有了这些之后，`wrk`的结果看起来如下:

```
Running 1m test @ http://localhost:4001
  4 threads and 1000 connections
  Thread Stats Avg Stdev Max +/- Stdev
    Latency 1.04s 270.71ms 1.66s 69.89%
    Req/Sec 221.14 65.34 405.00 66.08%
  Latency Distribution
     50% 1.07s
     75% 1.25s
     90% 1.36s
     99% 1.47s
  52823 requests in 1.00m, 14.41MB read
  Socket errors: connect 0, read 1014, write 0, timeout 0
Requests/sec: 879.16
Transfer/sec: 245.55KB 
```

如您所见，吞吐量从 92 请求/秒增加到了 879 请求/秒。平均延迟从 2.23 秒降至 1.04 秒。使用的内存从 10GB 降至 400MB。

还不错！
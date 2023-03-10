# 使用地理服务器处理异步和并发任务

> 原文：<https://dev.to/jackmarchant/using-a-genserver-to-handle-asynchronous-and-concurrent-tasks--3g96>

在大多数情况下，我发现进程间通信对于我正在做的工作来说是一种不必要的开销。尽管 Elixir 在这一点上是众所周知的(和 Erlang 一样)，但它实际上取决于您想要实现的目标，并且进程不应该仅仅为了好玩而产生。我最近遇到了一个场景，我认为让一个单独的进程负责执行并发和异步作业是解决这个问题的最好方法。在这篇文章中，我将解释这个问题和解决方案。

### 要求

这项工作的目标是异步处理将静态资产从一个提供者移动到另一个提供者的请求。这意味着将原始文件下载到服务器上的临时文件中，然后将其上传到新的提供商，并将结果保存在数据库中。

*   GraphQL 突变需要触发这个异步作业，并且不阻塞响应。
*   当作业完成时，无论成功还是失败，我们都应该报告它或以某种方式处理它。
*   多个请求将同时通过，这意味着进程不应该因为一个请求仍在运行而被阻止处理另一个请求。
*   一个请求可以触发一个或多个作业

### 寻找解决方案的过程

从监督树的角度来看，构建 Elixir 应用程序有许多不同的选择——何时何地生成流程以及哪种类型的流程适合您的用例通常是一种猜测，直到您广泛使用它们。

我首先想到的是使用一个[动态监控器](https://hexdocs.pm/elixir/DynamicSupervisor.html)(即[任务。Supervisor](https://hexdocs.pm/elixir/Task.Supervisor.html) )并在需要完成工作时根据需求专门创建新的监督流程。

这并不像我想象的那样工作，因为主进程仍然会阻塞，直到所有的任务都完成后才响应最初的请求。

我尝试的下一个解决方案是向一个 [GenServer](https://hexdocs.pm/elixir/GenServer.html) 发送消息，并让它完成工作，这样主进程几乎可以立即返回响应。虽然这在很大程度上解决了问题，但使用 GenServers 的一个常见问题是，它们一次只能处理一条消息，因此虽然这种解决方案提供了异步行为，但它失去了并发性的好处。

我最终采用的解决方案(目前看来还行)与 Genserver 的解决方案相差不远。唯一的区别是，当我们安排一项工作要完成时，它只会产生一个带有 Task.async/1 的任务，这个任务的好处是，即使你不使用 Task.await/2 的任务，它也会在任务完成时给调用者发送一条信息。

因为它是一个生成这些任务的 GenServer，所以它可以通过 [handle_info/2](https://hexdocs.pm/elixir/GenServer.html#c:handle_info/2) 回调很容易地处理发送给它的一般消息。这是 GenServer 处理每个任务的成功或失败状态的地方，在这种情况下同步处理每个结果不是问题。

下面是生成这些任务流程的 GenServer 的一个片段。

```
defmodule TaskRunner do
  use GenServer

  @me __MODULE__

  def start_link(opts) do
    GenServer.start_link(@me, opts, name: @me)
  end

  def init(opts), do: {:ok, opts}

  def run(fun) do
    GenServer.cast(@me, {:run, fun})
  end

  def handle_cast({:run, fun}, state) do
    Task.async(fun) # sends a message back to the TaskRunner when completed
    {:noreply, state}
  end

  # handle_info/2 receives generic messages from the Task processes
  def handle_info({_task, {:ok, result}}, state) do
    Logger.info("#{inspect(result)} Job Done.")
    {:noreply, state}
  end

  def handle_info({_task, {:error, reason}}, state) do
    Logger.error("Failed to completed job: #{reason}")
    {:noreply, state}
  end

  def handle_info(_, state), do: {:noreply, state}
end 
```

Enter fullscreen mode Exit fullscreen mode

这段代码有趣的地方在于，它实际上可能是重新实现了 Elixir 中已经存在的东西，这一点我还没有完全弄明白——不管怎样，只要它能工作，我对这样做没有任何问题！在 GenServer 中封装任务的生成只是提供了“调度”任务的能力(因为每个消息都是按顺序处理的)，同时独立地响应每个任务的响应。

理论上，如果我们要发送一堆在 GenServer 的邮箱中“排队”等待处理的消息，可能会出现一个问题，如果应用程序终止，GenServer 将丢失所有消息，并且那些任务也将丢失。然而，在这一点上，我更希望看到这最终会成为一个多大的问题，因为有各种各样的因素需要考虑。

我仍然不确定这是否是构建这种异步并发行为的最佳方式，但是在我认为 OTP 方法有意义的少数情况下，我经常发现许多不同的方法来解决这种问题——这是 Elixir 的一个好的方面，也是一个坏的方面。
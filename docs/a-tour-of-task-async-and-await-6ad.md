# 任务异步和等待之旅

> 原文：<https://dev.to/bakenator/a-tour-of-task-async-and-await-6ad>

关于 Elixir 的一个优点是所有语言模块的源代码都很容易检查。对于一个个人项目，我最近在考虑使用任务模块而不是种子模块，但后来我问自己...

# 任务是如何工作的？

如果你在十六进制文档中查看任务模块的文档，它说`the most common use case for tasks is to convert sequential code into concurrent code by computing a value asynchronously`，那么它提供了下面的例子。

```
task = Task.async(fn -> do_some_work() end)
res = do_some_other_work()
res + Task.await(task) 
```

为了理解任务是如何工作的，我们可以深入研究上面使用的两个函数的任务源代码。

### [T1】task . async](#taskasync)

首先是对 Task.async 的调用。下面是该函数两种定义的源代码。

```
 def async(fun) when is_function(fun, 0) do
    async(:erlang, :apply, [fun, []])
  end

  def async(module, function_name, args)
    when is_atom(module) and is_atom(function_name) and is_list(args) do
    mfa = {module, function_name, args}
    owner = self()
    {:ok, pid} = Task.Supervised.start_link(get_owner(owner), get_callers(owner), :nomonitor, mfa)
    ref = Process.monitor(pid)
    send(pid, {owner, ref})
    %Task{pid: pid, ref: ref, owner: owner}
  end 
```

Task.async/1 是一个捕捉所有函数，它用 erlang.apply 将参数函数传递给 Task.async/3

`{:ok, pid} = Task.Supervised.start_link(get_owner(owner), get_callers(owner), :nomonitor, mfa)`

在 Task.async/3 的这一行中，当前 pid ( `owner`)和原始参数函数(`mfa`)被传递给任务。作为`Task.Supervised.start_link`函数的参数进行监督。`start_link`然后产生一个新的进程并返回它的 pid。

`send(pid, {owner, ref})`

接下来，owner_pid ( `owner`)和 monitor 引用(`ref`)的副本被发送到新任务。监督过程。这些就是任务。被监督的进程将使用向 Task.await 标识自己，当它完成运行该函数时。

`%Task{pid: pid, ref: ref, owner: owner}`

最后，Task.async/3 返回一个任务结构，用于在 Task.await/2 进行识别。

接下来让我们看看任务内部发生了什么。被监督的，被繁殖的

### 任务。监督

```
def start_link(owner, callers, monitor, fun) do
  {:ok, :proc_lib.spawn_link(__MODULE__, :reply, [owner, callers, monitor, fun])}
end

defp reply(owner, owner_pid, mref, timeout, mfa) do
    receive do
      {^owner_pid, ref} ->
        _ = if mref, do: Process.demonitor(mref, [:flush])
        send(owner_pid, {ref, invoke_mfa(owner, mfa)})
    ... 
```

`{:ok, :proc_lib.spawn_link(__MODULE__, :reply, [owner, callers, monitor, fun])}`

任务。Supervised.start_link 启动一个新进程，调用自己的回复函数。

`receive do`
T1】

在上面复制的行中，我们可以看到 reply 做的第一件事是等待 Task.async 函数中发送的{owner，ref}元组。

`send(owner_pid, {ref, invoke_mfa(owner, mfa)})`

它接到任务后。受监督运行用户在`invoke_mfa(owner, mfa)`中提供的原始功能。`invoke_mfa`基本上在内部使用`apply(module, fun, args)`,并进行额外的错误处理。

然后，原始函数的结果被发送回所有者进程(无论我们在哪里运行 Task.async)。Task.await 将获取这个返回给所有者进程的消息

### 等待

```
 def await(%Task{ref: ref, owner: owner} = task, timeout \\ 5000) when is_timeout(timeout) do
    ...
    receive do
      {^ref, reply} ->
        Process.demonitor(ref, [:flush])
        reply
    ... 
```

`def await(%Task{ref: ref, owner: owner} = task, timeout \\ 5000) when is_timeout(timeout) do`

Task.await 接受一个任务结构并提取 ref 属性。

`receive do`
T1】

该函数然后调用`receive`并等待任何传入的消息。来自任务的每条消息。Supervised 有一个 ref，它用它作为 id 向 Task.await 标识自己。

`reply`

如果 ref 匹配，则返回结果。

### Task.async/Task.await 生命周期总结

功能`fn -> do_some_work() end`是:

1.  作为参数提供给 Task.async
2.  在 Task.async 中，作为参数提供给新的 Task.supervised 进程
3.  在 Task.supervised 流程中运行
4.  结果以消息的形式从 Task.supervised 进程发送回原始进程
5.  来自任务的消息。受监督的在任务中被捕获。等待并返回

# 为什么要用 Ref 来标识？

任务。被监控的进程用一个`ref`来标识自己，这个值在`ref = Process.monitor(pid)`行中被初始化。当我们已经有了一个变量来标识一个进程，pid，为什么还要这样做呢？

老实说，我研究了一会儿源代码，没有找到使用 ref 作为标识符而不是 pid 的具体原因。自从酏剂源代码开始，就已经这样设置了。如果有人对此有什么见解，我很想知道。

# 乱序任务？

从这次任务检查中，我学到了一些关于进程间消息的非常有价值的东西。

消息可能不会按照接收的顺序进行处理！

在 Task.await 函数中，没有`receive`模式匹配的基本情况。如果消息 ref 不匹配，则没有匹配。这种情况下会发生什么？一个例子:

```
task1 = Task.async(fn ->
  :timer.sleep(10_000)
  1 
end)

task2 = Task.async(fn -> 2 end)

IO.inspect Task.await(task1)
IO.inspect Task.await(task2) 
```

在这里，我们可以看到 Task 1 在返回之前将暂停 10 秒钟。因此，任务 2 将在任务 1 之前完成并发送其响应。但是任务 1 首先阻塞等待它的响应。产量是多少？

```
1
2 
```

尽管带有函数结果的消息是按照[task2，task1]的顺序接收的，但一切仍然正常。

这是因为如果`receive`没有找到与最新传入消息匹配的消息，它会简单地将该消息放回队列并继续等待。然后，每次收到新消息时，它都会再次检查，并一直阻塞，直到找到匹配的为止。

这也意味着如果队列中已经有匹配的消息，`receive`根本不需要等待。它将检查队列中的所有现有消息，如果发现一个匹配，就继续前进。这种情况发生在`Task.await(task2)`内

希望您对这个任务异步/等待功能的探究感兴趣。

用 Elixir 编码很有趣！

任务文件的源代码可以在这里找到:
[任务模块](https://github.com/elixir-lang/elixir/blob/master/lib/elixir/lib/task.ex)
[任务。监控模块](https://github.com/elixir-lang/elixir/blob/master/lib/elixir/lib/task/supervised.ex)
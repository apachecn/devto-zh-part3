# 了解 getservers

> 原文：<https://dev.to/manzanit0/understanding-genservers-4n6i>

如果你正在读这篇文章，很可能你已经使用了长生不老药，但是你想知道它是如何起作用的。对我来说，我第一次尝试使用它时，我完全弄错了——我没有将它用作数据存储，而是为我想要保存的每个新记录生成了一个全新的 Genserver。当我发现的时候，我意识到这仅仅是因为我没有真正理解引擎盖下发生了什么。

## 什么是 Genserver？

根据 [hexdocs](https://hexdocs.pm/elixir/GenServer.html) 显示，一台 Genserver 是:

> 用于实现客户-服务器关系的服务器的行为模块。
> 
> GenServer 是一个类似于任何其他 Elixir 进程的进程，它可以用于保持状态、异步执行代码等等。使用通过此模块实现的通用服务器进程(GenServer)的优势在于，它将拥有一组标准的接口函数，并包括跟踪和错误报告功能。它也将适合一个监督树。

归根结底，它只是对客户机-服务器行为的一种抽象:它允许我们用一组回调函数定义自己的模块，我们可以从代码库中的任何地方调用它。它主要服务于两个不同的目的:首先，它允许我们异步执行代码片段，所以我们不必阻塞我们的主线程，其次，它帮助我们保存状态。

为了帮助我们理解这个概念，我们将在 Elixir 中构建自己的 Genserver 实现。非常简单的一个！

## 流程 101

在开始之前，回顾一下 Elixir 中的进程如何工作和通信是很重要的。在 Elixir 中，所有代码都在独立的进程中运行。产生这些进程的基本机制是`spawn/1`函数。

```
iex> spawn fn -> "Hello world!" end
#PID<0.117.0> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`spawn/1`返回了一个进程标识符(PID)。衍生进程通常执行我们提供给它们的函数，然后退出，所以它很可能已经死了。考虑到 wee 也可以从已经存在的模块中用`spawn/4`的函数生成进程，但是我们将在后面的例子中检验它。

此外，为了让各种过程进行交流，仙丹为我们提供了 [`send/3`](https://hexdocs.pm/elixir/Process.html#send/3) 和 [`receive/1`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#receive/1) 。`send/3`允许我们向任何进程发送一条消息，假设我们知道它是 PID，并接收拦截当前进程中的所有消息。看看这个例子:

```
iex> send self(), {:msg, "Hey!"}
{:hello, "world"}
iex> receive do
...>   {:msg, msg} -> msg
...>   _ -> "not a message!"
...> end
"Hey!" 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们正在向`self()`发送一个元组，它是对当前流程的引用，一旦发送，我们将通过运行一个`receive/1`子句拦截它。

现在，考虑到所有这些，我们可以编写两个简单的模块，它们以如下方式交换消息:

```
defmodule Person do
    def create, do: spawn(Person, :listen, [])

    def say(to, message), do: send(to, {:msg, message})

    def listen do
        receive do
            {:msg, msg} -> IO.puts("Said '#{msg}' to #{inspect self()}")
        end
        listen()
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

并且在`iex>` :

```
iex> marta = Person.create()
#PID<0.134.0>
iex> Person.say(marta, "Hey peep!")
Said 'Hey peep!' to #PID<0.134.0>
{:msg, "Hey peep!"}" 
```

Enter fullscreen mode Exit fullscreen mode

## 在一个无状态的世界里储存状态

到目前为止，在我们了解发电服务器的旅程中，我们已经了解了`spawn/1`、`send/3`和`receive/1`，但是，这如何让我们更接近了解发电服务器如何工作呢？。在跳到 Genserver 行为之前，我们还有一块拼图要揭开: **state** 。

如你所知，Elixir 是一种函数式语言，它现在知道状态是什么——我们创建具有函数的模块，我们给它们数据，它们处理这些数据。但是我们没有 C#或 Java 中的*实例*，这些实例为我们存储状态。此外，在研究过程时，当我们产生它们时，它们就死去了。那么我们如何存储状态呢？答案是递归。

为了能够在 Elixir 中维护某种状态，常见的模式是让一个进程用它所拥有的状态递归地调用自己。看看下面的例子:

```
defmodule Counter do
    def init, do: loop(0)

    def loop(counter) do
        IO.puts counter
        loop(counter + 1)
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果你试图执行它...

```
iex> Console.init()
... (many numbers which I will not paste)
199943
199944
199945
199946
199947
199948
199949

BREAK: (a)bort (c)ontinue (p)roc info (i)nfo (l)oaded
       (v)ersion (k)ill (D)b-tables (d)istribution
^C 
```

Enter fullscreen mode Exit fullscreen mode

是的，它开始打印所有的数字，直到你停止这个过程才会停止，你可以通过按两次`Ctrl + C`来完成。不管怎样，正如你所看到的，我们已经能够用递归循环来存储和改变状态。这是发电服务器如何保持状态的关键。现在，让我们开始真正的交易吧！

## 自制发电服务器

有了我们收集的所有工具，我们现在可以开始了。因为我完全是关于 TDD 的，所以让我们从一个测试开始。坚持使用 Elixir 提供的 API 类型，我想要一个为我们生成服务器的`init/0`函数和一个将消息保存到服务器的`save/2`函数。这是我的测试:

```
defmodule StoreTest do
  use ExUnit.Case

  test "saves message" do
    pid = Store.init()
    response = Store.save(pid, "Hey!")

    assert {:ok, "message received!"} == response
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

为了通过测试，我们确实需要写一点代码。首先，我们需要`init/0`函数，但是我们还需要我们讨论过的循环，该循环将为我们存储状态并发送回响应。如果你对 Genservers 有所了解，你会知道它们允许消费者通过`call/2`和`cast/2`发送同步和异步消息。在这种情况下，我们试图开发一个类似于`call/2`的函数——一个等待服务器创建响应并返回它的函数。

首先，`init/0`函数将使用我们在文章开始时提到的`spawn/4`，它将允许我们向它传递一个在我们的一个模块中定义的函数:

```
 def init() do
    spawn(__MODULE__, :loop, [[]])
  end 
```

Enter fullscreen mode Exit fullscreen mode

第三个参数是函数的参数，在这种情况下，我们希望它是一个空列表，这样就解释了`[[]]`。

继续向前，我们希望我们的`save/2`函数向服务器发送一条消息并等待响应，因此对于`send/3`和`receive/1` :
来说，这应该相当简单

```
def save(pid, message) do
    send(pid, {:save, self(), message})

    receive do
        {:ok, response} -> {:ok, response}
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

最后，循环。当我们生成流程时，我们调用一个`loop/1`函数，该函数接收状态，并应该保持轮子继续运转。由于我们的`save/2`函数向服务器发送一条消息并等待响应，我们可以假设循环将等待一条消息，然后发回一个响应。

```
def loop(state) do
    state =
      receive do
        {:save, from, msg} ->
          send(from, {:ok, "message received!"})
          [msg | state]
      end

    loop(state)
end 
```

Enter fullscreen mode Exit fullscreen mode

如果我们把这些放在一起...

```
defmodule Store do
  def init() do
    spawn(__MODULE__, :loop, [[]])
  end

  def save(pid, message) do
    send(pid, {:save, self(), message})

    receive do
      {:ok, response} -> {:ok, response}
    end
  end

  def loop(state) do
    state =
      receive do
        {:save, from, msg} ->
          send(from, {:ok, "message received!"})
          [msg | state]
      end

    loop(state)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在运行`mix test`,我们应该有一个漂亮的绿色输出。接下来，我们需要一个函数来检索我们存储的所有消息。我一如既往地从测试开始:

```
test "retrieves all message" do
    pid = Store.init()
    Store.save(pid, "...world!")
    Store.save(pid, "Hello")

    response = Store.fetch(pid)

    assert {:ok, ["Hello", "...world!"]} == response
end 
```

Enter fullscreen mode Exit fullscreen mode

好的一面是，在这种情况下，我们只需要开发`fetch/1`函数。在这种情况下，`fetch/1`将与`save/2`非常相似，因为它将发送消息并期待响应。逻辑的核心将被编码在循环中——我们需要让服务器用所有的数据做出响应。一旦我们完成，我们的代码看起来像这样:

```
defmodule Store do
  def init() do
    spawn(__MODULE__, :loop, [[]])
  end

  def save(pid, message) do
    send(pid, {:save, self(), message})

    receive do
      {:ok, response} -> {:ok, response}
    end
  end

  def fetch(pid) do
    send(pid, {:fetch, self()})

    receive do
      {:ok, response} -> {:ok, response}
    end
  end

  def loop(state) do
    state =
      receive do
        {:save, from, msg} ->
          send(from, {:ok, "message received!"})
          [msg | state]
        {:fetch, from} ->
          send(from, {:ok, state})
          state
      end

    loop(state)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 异步函数呢？

我之前评论过，Genservers 也有异步处理程序:`cast/2`。我决定只实现同步处理程序的原因是因为异步处理程序更简单。我们的同步函数是这样的:

```
def fetch(pid) do
    send(pid, {:fetch, self()})

    receive do
      {:ok, response} -> {:ok, response}
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

然而，如果我们想让它异步，我们只需删除`receive/1`子句。

```
def fetch(pid) do
    send(pid, {:fetch, self()})
end 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在通读了整件事之后，基因服务器看起来不再那么危险了，是吗？最终，它们就像我们的`Store`模块一样，是一个简单的进程包装器，这些进程之间相互通信，并为我们提供一个客户端-服务器架构。在结束之前，我要提一下代理。

代理是 Elixir core 团队提供给我们的另一个抽象概念，它使处理状态变得更加容易。说到底，它们只是 Genservers 本身的一个[包装器](https://github.com/elixir-lang/elixir/blob/master/lib/elixir/lib/agent.ex#L270)，但是它们为我们提供了一个更干净、更容易使用的 API 我们不必担心实现 cast/call 回调样板文件。下次你需要在你的应用程序中存储状态时，考虑一下——你需要一个 Genserver 吗？可以用任务或者代理来代替吗？它总是关于简单的代码！

*最初发布于[https://manzanit 0 . github . io](https://manzanit0.github.io)T3】*
# 理解 Elixir 并发模型的基础

> 原文：<https://dev.to/fteem/understanding-the-basics-of-elixirs-concurrency-model-35e5>

*这篇文章最初发表在 ieftimov.com 的[我的博客上。](https://ieftimov.com/understanding-basics-elixir-concurrency-model)*

如果你来自一个面向对象的背景，你可能曾经在你最喜欢的 OO 语言中尝试过并发。您的收获会有所不同，但总的来说，当涉及到并发性时，OO 语言更难处理。这是由于它们的本性——它们被设计成将状态保存在记忆中，需要更多的专业知识和经验才能成功。

在并发性方面，Elixir 如何经得起其他语言的考验？嗯，对于初学者来说，仅仅是功能性和不可变就是一个巨大的胜利——不需要管理状态。但同时，Elixir 在引擎盖下和它的标准库中有更多的好东西。

基于 Erlang 的虚拟机(又名 BEAM)，Elixir 使用 processesto 来运行所有代码。请注意，从这里开始，除非另有说明，我们每次提到进程时都是指 BEAM 进程，而不是 OS 进程。

Elixir 的进程是相互隔离的，它们不共享任何内存，并发运行。它们非常轻便，光束能够同时运行数千个。这就是为什么 Elixir 展示了创建流程、流程之间的通信以及流程管理的各种模块的原动力。

让我们看看如何创建流程并在它们之间发送消息。

## 创建流程

Elixir 工具箱中最简单(但功能强大)的工具之一是`IEx`——Elixir 的缩写，Interactive Elixir。如果我们跳转到`IEx`并运行`h self`，我们将得到以下输出:

```
iex(1)> h self

                      def self()

  @spec self() :: pid()

Returns the PID (process identifier) of the calling process.

Allowed in guard clauses. Inlined by the compiler. 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`self()`是 Elixir 中的内置函数，它返回调用进程的 PID(进程标识符)。如果你在`IEx`中运行它，它将返回`IEx` :
的 PID

```
iex(2)> self
#PID<0.102.0> 
```

Enter fullscreen mode Exit fullscreen mode

现在，就像`IEx`是一个 BEAM 进程，有了自己的 PID，它也可以创建新的进程。事实上，*任何光束处理*都能`spawn`光束处理。这是使用`Kernel.spawn/1`功能:
完成的

```
iex(3)> spawn(fn -> 1 + 1 end)
#PID<0.108.0> 
```

Enter fullscreen mode Exit fullscreen mode

`spawn/1`创建一个新进程，该进程调用作为参数提供的函数`fn -> 1 + 1 end`。您可能会注意到，我们看不到匿名函数的返回值，因为该函数运行在不同的进程中。相反，我们得到的是衍生进程的 PID。

另一件值得注意的事情是，一旦我们`spawn`一个进程，它将立即运行，这意味着函数将立即执行。我们可以使用`Process.alive?/1`函数来检查:

```
iex(4)> pid = spawn(fn -> 1 + 1 end)
#PID<0.110.0>

iex(5)> Process.alive?(pid)
false 
```

Enter fullscreen mode Exit fullscreen mode

虽然我们现在不需要深入研究`Process`模块，但是它有更多的函数可用于处理流程。你可以在这里浏览这些文档。

现在，让我们看看接收消息。

## 在流程中接收消息

为了我们的例子，让我们想象我们打开一个新的`IEx`会话，我们告诉`IEx`进程(又名`self`)它可能会收到一个它应该做出反应的消息。如果这让您有点摸不着头脑，请记住，因为`IEx`是一个波束进程，它可以像任何其他波束进程一样接收消息。

我们希望我们的`IEx`流程接收的消息是:

*   包含`:hello`原子和带有名称的字符串(例如`{:hello, "Jane"}`)的元组
*   包含`:bye`原子和带有名称的字符串(例如`{:bye, "John"}`)的元组

当流程在第一种情况下收到消息时，它应该回复“Hello Jane”，而在第二种情况下，它应该回复“Bye John”。如果我们可以在 Elixir 中使用`cond`宏，它看起来会像:

```
cond message do
  {:hello, name} -> "Hello #{name}"
  {:bye, name} -> "Bye #{name}"
end 
```

Enter fullscreen mode Exit fullscreen mode

为了在进程中接收消息，我们不能使用`cond`，但是 Elixir 为我们提供了一个函数`receive/1`，它将一个块作为参数。虽然它不是`cond`，但它看起来与上面的例子非常相似，因为它允许我们使用模式匹配:

```
receive do
  {:hello, name} -> "Hello #{name}"
  {:bye, name} -> "Bye #{name}"
end 
```

Enter fullscreen mode Exit fullscreen mode

你在这里看到的是 BEAM 的 actor 模型在 Elixir 中的并发使用。如果你不熟悉这个模型，不要担心——你可以在 Dev.to 上阅读一篇关于它的有趣的文章，或者你可以继续阅读，到本文结束时，你应该对它有一个很好的了解。

`receive`函数获取接收到的消息，并尝试将其与块中的一个语句进行模式匹配。显然，它不仅接受返回值，还接受函数调用。可以想象，`receive/1`是当**进程的邮箱**收到新消息时被调用的。

既然我们已经修复了流程邮箱上的操作，那么我们如何向我们的`IEx`流程发送消息呢？

这是通过`send/2`功能完成的。该函数将 PID 和消息本身作为参数。对于我们的示例，让我们向我们的`IEx`流程发送以下消息:

```
iex(1)> send self(), {:hello, "John"}
{:hello, "John"} 
```

Enter fullscreen mode Exit fullscreen mode

您在这里看到的是被放入`IEx`邮箱的消息。这意味着我们需要在我们的`IEx`会话中调用`receive`函数，这样我们就可以处理邮箱中的消息:

```
iex(2)> receive do
...(2)> {:hello, name} -> "Hello #{name}"
...(2)> {:bye, name} -> "Bye #{name}"
...(2)> end
"Hello John" 
```

Enter fullscreen mode Exit fullscreen mode

在执行完`receive`块后，消息将被立即处理，我们将看到`"Hello John"`被返回。

### 如果我们永远收不到消息怎么办？

这里需要注意的一点是，如果我们只在我们的`IEx`会话中写`receive`块，它将一直阻塞，直到流程收到消息。这是意料之中的——如果邮箱中没有与任何模式匹配的邮件，当前进程将等待匹配的邮件到达。这是`receive/1`的默认行为。

显然，如果我们陷入这样的困境，我们总是可以通过使用`Ctrl+C`来停止`IEx`会话。但是，如果我们的应用程序中的某个进程卡住了呢？如果它没有收到消息，我们如何告诉它在一段时间后应该停止等待？

Elixir 为我们提供的一个好处是使用`after` :
设置超时

```
iex(6)> receive do
...(6)> {:hello, name} -> "Hello #{name}"
...(6)> {:bye, name} -> "Bye #{name}"
...(6)> after
...(6)> 1_000 -> "Nothing after 1s"
...(6)> end
"Nothing after 1s" 
```

Enter fullscreen mode Exit fullscreen mode

这里发生的情况是，超时功能在 1000 毫秒后执行，并且`receive/1`功能退出(因此停止阻塞)。这可以防止进程无休止地等待匹配的邮件到达它们的邮箱。

## 长时间运行的流程

到目前为止，我们一直在关注向同一个流程发送和接收消息——`IEx`流程本身。这是一个非常简单的例子，当我们想要在生产应用程序中将流程付诸实施时，它不会有所帮助。

此时，您可能想知道如何实际生成一个对多个传入消息作出反应的流程，而不仅仅是(ab)使用带有`receive/1`函数的`IEx`流程。

要做到这一点，我们必须让一个进程无限运行(或者直到我们让它死去)。怎么会？通过在流程本身中创建一个无限循环。

### WTF 你说的“无限循环”是什么意思？！

是的，我知道，这感觉很奇怪，不是吗？事情是这样的 BEAM 有一个优化，所谓的最后一次调用优化(阅读乔·阿姆斯特朗对这种优化的解释[这里](http://erlang.org/pipermail/erlang-questions/2016-October/090663.html))，如果一个函数中的最后一次调用是对同一个函数的调用，Elixir 不会在调用堆栈上分配新的堆栈框架。事实上，它将只是跳转到同一个函数的开头(而不是运行它的另一个实例)，这将防止我们的程序发生堆栈溢出。

这意味着，如果我们在编写这些自调用函数时稍微小心一点，几乎不可能在 BEAM 语言(Erlang & Elixir)中破坏堆栈。

### 长时间运行的流程，续

我们来看一个功能单一的小模块:

```
defmodule MyProcess do
  def start do
    receive do
      {:hello, name} ->
        IO.puts "Hello #{name}!"
        start()
      {:bye, name} ->
        IO.puts "Bye #{name}. Shutdown in 3, 2, 1..."
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

运行时,`MyProcess.start`函数将等待消息到达进程的邮箱。然后，它将尝试对到达的消息进行模式匹配，并执行相关的代码。一个技巧是，在第一个案例结束时，我们再次执行`start`函数，这将在流程中创建一个无限循环，因此让流程永远等待消息。

让我们看看这将如何在`IEx` :
中工作

```
iex(1)> pid = spawn(MyProcess, :start, [])
#PID<0.120.0>

iex(2)> send pid, {:hello, "Ilija"}
Hello Ilija!
{:hello, "Ilija"}

iex(3)> send pid, {:hello, "Jane"}
Hello Jane!
{:hello, "Jane"}

iex(4)> send pid, {:bye, "Jane"}
Bye Jane. Shutdown in 3, 2, 1...
{:bye, "Jane"} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们使用`spawn/3`创建一个运行`MyProcess.run`函数的进程。`spawn/3`有点像`spawn/1`——唯一的区别是`spawn/3`知道如何在进程中运行一个命名函数，而`spawn/1`只接受匿名函数作为参数。

然后，您可以看到，每次我们向流程发送一条`{:hello, "Name"}`消息(使用`send/2`，我们都会看到流程打印出问候。一旦我们发送了`{:bye, "Jane"}`消息，进程就会显示它正在关闭。

为什么嗯，如果你看一下`MyProcess.start`函数，你会注意到它在打印出关闭消息后并没有调用自己。这意味着一旦它处理了这个消息，`MyProcess.start`函数将会结束，进程将会终止。

让我们在`IEx`中测试一下，使用`Process.alive?/1`函数:

```
iex(1)> pid = spawn MyProcess, :start, []
#PID<0.109.0>

iex(2)> Process.alive? pid
true

iex(3)> send pid, {:hello, "Ilija"}
Hello Ilija!
{:hello, "Ilija"}

iex(4)> Process.alive? pid
true

iex(5)> send pid, {:bye, "Ilija"}
Bye Ilija. Shutdown in 3, 2, 1...
{:bye, "Ilija"}

iex(6)> Process.alive? pid
false 
```

Enter fullscreen mode Exit fullscreen mode

现在我们知道了如何在 inElixir 中向一个流程发送和接收多条消息，您可能会想知道什么是使用流程的好用例？

答案是:保持状态。

## 使用流程保持状态

你可能会觉得这很奇怪，但这是保持长生不老状态的经典例子。虽然我们在这里的例子不会考虑在磁盘上存储状态，涵盖所有可能发生的边缘情况，或者是一个防弹解决方案，但我希望它能让您对进程以及如何使用它们有所了解。

让我们写一个有一个`start`函数的`Store`模块。它应该产生一个进程，这个进程将调用同一个模块的一个函数，现在叫做`foo` :

```
# store.exs
defmodule Store do
  def start do
    spawn( __MODULE__ , :foo, [%{}])
  end

  def foo(map) do
    IO.puts "Nothing so far"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们简单地剖析一下`Store.start`函数:它所做的是`spawn`调用`foo/1`函数的一个新进程，并且它将一个空映射(`%{}`)作为参数传递给该函数。如果特殊的关键字`__MODULE__`让你迷惑，它只是模块名的别名:

```
iex(1)> defmodule Test do
...(1)> def eql?, do: __MODULE__ == Test
...(1)> end

iex(2)> Test.eql?
true 
```

Enter fullscreen mode Exit fullscreen mode

这意味着当我们调用`Store.start`时，我们将立即看到函数在进程中的输出。紧接着，该进程将终止:

```
iex(4)> pid = Store.start
Nothing so far...
#PID<0.117.0>

iex(5)> Process.alive? pid
false 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们需要让`foo/1`成为一个永远循环的函数。或者至少在我们停止之前。

我们把`foo/1`改名为`loop/1`，让它永远循环:

```
defmodule Store do
  def start do
    spawn( __MODULE__ , :loop, [%{}])
  end

  def loop(state) do
    receive do
      loop(state)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在在`IEx`会话中运行该模块，该过程将永远运行。不这样做，让我们添加一个特殊的“系统”消息，我们可以发送到进程，这样我们就可以强制它关闭:

```
defmodule Store do
  def start do
    spawn( __MODULE__ , :loop, [%{}])
  end

  def loop(state) do
    receive do
      {:stop, caller_pid} ->
        send caller_pid, "Shutting down"
      _ ->
        loop(state)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，你可以看到，当没有匹配时，`Store.loop/1`函数将会递归，但是当收到`:stop`消息时，它将会向调用 PID 发送`send`一条`"Shutting down"`消息。

```
iex(1)> pid = Store.start
#PID<0.125.0>

iex(2)> send pid, {:stop, self()}
{:stop, #PID<0.102.0>}

iex(3)> flush()
"Shutting down."
:ok 
```

Enter fullscreen mode Exit fullscreen mode

您在这里看到的是在两个流程之间发送消息的一个非常简单的例子——`Store`流程和我们的`IEx`会话流程。当我们发送`:stop`消息时，我们也发送`IEx`会话的 PID(`self`)，然后由`Store.loop/1`使用它来发送回复。最后，我们并没有为`IEx`会话编写一个完整的`receive`块，而是调用`flush`，刷新`IEx`进程邮箱并返回当时邮箱中的所有消息。

如果你现在感到深陷兔子洞，不要担心——我们马上就要解决保持进程状态的问题了！

假设我们的`Store`将接受四个命令:

1.  `stop` -我们已经实现的那个，它停止了`Store`过程
2.  `put` -向`state`映射添加一个新的键值对
3.  `get` -从`state`映射中获取给定键的值
4.  `get_all` -获取存储在`state`映射中的所有键值对

### 置位值

让我们实现`put`命令:

```
defmodule Store do
  def start do
    spawn( __MODULE__ , :loop, [%{}])
  end

  def loop(state) do
    receive do
      {:put, key, value} ->
        new_state = Map.put(state, key, value)
        loop(new_state)
      {:stop, caller} ->
        send caller, "Shutting down."
      _ ->
        loop(state)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当我们向流程发送一个包含`{:put, :foo, :bar}`的元组时，它会将`:foo => :bar`对添加到`state`映射中。这里的关键是它将使用更新后的状态(`new_state`)再次调用`State.loop/1`。这将确保我们添加的键-值对将在函数的下一次递归中包含在新状态中。

### 获取数值

让我们实现`get`，这样我们就可以通过`IEx`会话
一起测试`get`和`put`

```
defmodule Store do
  def start do
    spawn( __MODULE__ , :loop, [%{}])
  end

  def loop(state) do
    receive do
      {:stop, caller} ->
        send caller, "Shutting down."
      {:put, key, value} ->
        new_state = Map.put(state, key, value)
        loop(new_state)
      {:get, key, caller} ->
        send caller, Map.fetch(state, key)
        loop(state)
      _ ->
        loop(state)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

就像其他命令一样，`get`周围没有魔法。我们使用`Map.fetch/2`来获取传递的键的值。此外，我们获取`caller`的 PID，这样我们就可以将在地图中找到的值发送回调用者:

```
iex(1)> pid = Store.start
#PID<0.119.0>

iex(2)> send pid, {:put, :name, "Ilija"}
{:put, :name, "Ilija"}

iex(3)> send pid, {:get, :name, self()}
{:get, :name, #PID<0.102.0>}

iex(4)> flush
{:ok, "Ilija"}
:ok

iex(5)> send pid, {:put, :surname, "Eftimov"}
{:put, :surname, "Eftimov"}

iex(6)> send pid, {:get, :surname, self()}
{:get, :surname, #PID<0.102.0>}

iex(7)> flush
{:ok, "Eftimov"}
:ok 
```

Enter fullscreen mode Exit fullscreen mode

如果我们看看我们与`Store`流程的“对话”，在开始时，我们用值`"Ilija"`设置一个`:name`键，然后我们检索它(我们看到使用`flush`的回复)。然后我们通过在`Store`中的地图上添加一个新的键来做同样的练习，这次是值为`"Eftimov"`的`:surname`。

从“对话”的角度来看，这里的关键部分是我们发送`self()`——我们当前流程的 PID(`IEx`会话)——因此`Store`流程知道将回复发送到哪里。

### 把商店翻个底朝天

就在我们开始编写`Store`模块之前，我们提到过我们还将实现一个`get_all`命令，它将返回`Store`的所有内容。就这么办:

```
defmodule Store do
  def start do
    spawn( __MODULE__ , :loop, [%{}])
  end

  def loop(state) do
    receive do
      {:stop, caller} ->
        send caller, "Shutting down."
      {:put, key, value} ->
        new_state = Map.put(state, key, value)
        loop(new_state)
      {:get, key, caller} ->
        send caller, Map.fetch(state, key)
        loop(state)
      {:get_all, caller } ->
        send caller, state
        loop(state)
      _ ->
        loop(state)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果你期待这里有什么特别的东西，我很抱歉让你失望了。`get_all`命令的实现是将整个流程的`state`图返回给发送者。

我们来测试一下:

```
iex(1)> pid = Store.start
#PID<0.136.0>

iex(2)> send pid, {:put, :name, "Jane"}
{:put, :name, "Jane"}

iex(3)> send pid, {:put, :surname, "Doe"}
{:put, :surname, "Doe"}

iex(4)> send pid, {:get_all, self()}
{:get_all, #PID<0.102.0>}

iex(5)> flush
%{name: "Jane", surname: "Doe"}
:ok 
```

Enter fullscreen mode Exit fullscreen mode

正如预期的那样，一旦我们向`Store`添加了两个键-值对，当我们调用`get_all`命令时，`Store`进程就会发送回整个`state`映射。

虽然这是一个非常小的、人为设计的例子，但是我们所遵循的框架通过使用递归、向调用进程来回发送命令和回复来保持状态，实际上在 Erlang 和 Elixir 中使用得相当多。

## 小小的失望

首先，我很高兴你设法看到了这篇文章的结尾。我相信，一旦我理解了 Elixir 的并发模型的基础，通过这些练习，我大开眼界了，我希望它们对你也一样。

理解如何通过发送和接收消息来使用流程是最重要的知识，你可以在你的长生不老之旅中使用它。

现在，如我所承诺的-一个小小的失望。

在 90%以上的情况下，当你想在 Elixir 中编写并发代码时，你会**而不是**使用像这里这样的过程。事实上，你几乎不会用到`send/2`和`receive/1`函数。说真的。

为什么？这是因为 Elixir 附带了一个叫 OTP 的东西，它可以帮助你做更酷的事情，而不需要编写任何底层的流程管理代码。当然，这不应该阻止你在强烈需要过程的时候，或者当你想要试验和学习的时候使用过程。

但是，这是下一篇博文的主题，我们将深入探讨 OTP 及其一些行为。

在那之前，你认为过程在你的项目/工作中是一个潜在的用例？

### 点更阅读

如果你想多读一点，这里有几个链接值得一看:

*   Elixir“入门”指南中的[流程](https://elixir-lang.org/getting-started/processes.html)
*   德国人维拉斯科在 Thoughtbot 的博客上发表了《长生不老药的长寿过程》
*   Hexdocs.pm 上的[流程](https://hexdocs.pm/elixir/Process.html#content)
*   [无尽的递归](https://elixirforum.com/t/endless-recursion/10683)在 theElixir 论坛上
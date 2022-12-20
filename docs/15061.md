# Elixir 中的 OTP:通过构建自己的 URL 缩写程序来学习 GenServer

> 原文：<https://dev.to/fteem/otp-in-elixir-learn-genserver-by-building-your-own-url-shortener-5b0d>

看看任何编程语言，你会(希望！)找一个丰富有用的标准库。我的职业生涯始于 Ruby 的软件开发人员，Ruby 有一个非常易于使用、文档完善的标准库，有大量的模块和类可供使用。就个人而言，我觉得 Ruby 中的`Enumerable`模块及其所有的好方法简直太棒了。

您可能来自不同的语言，但我确信任何严肃的编程语言都有一套类、模块、方法和函数来简化您的生活(和工作)。

那么，Elixirs stdlib 呢？

这也不足为奇——Elixir 有一个记录良好且易于使用的标准库。但是，因为它工作在 BEAM 虚拟机之上，并且继承了 Erlang 丰富的历史，所以它还有更多的东西——叫做 *OTP* 。

[![](img/072851f52a3ac1a2634c5df0ca8e2a37.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--V1_-VcpU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ieftimov.com/img/posts/mario-caruso-770233-unsplash.jpg) <small>马里奥·卡鲁索在 Unsplash</small> 上的照片

## 遇见 OTP👋

来自[维基百科关于 OTP 的文章](https://en.wikipedia.org/wiki/Open_Telecom_Platform):

> OTP 是用 Erlang 编程语言编写的有用的中间件、库和工具的集合。它是 Erlang 开源发行版不可或缺的一部分。OTP 这个名字最初是 Open TelecomPlatform 的缩写，这是爱立信发布 Erlang/OTP 作为开源之前的一次品牌尝试。然而，Erlang 和 OTP 都不是特定于电信应用程序的。

接下来，它指出:

> 它(OTP)包含:
> 
> *   一个 Erlang 解释器(叫 BEAM)；
> *   一个 Erlang 编译器；
> *   用于服务器(节点)之间通信的协议；
> *   CORBA 对象请求代理；
> *   一个叫透析器的静态分析工具；
> *   分布式数据库服务器(Mnesia)；和
> *   许多其他图书馆。

尽管我并不认为自己是 Elixir、Erlang、BEAM 或 OTP 方面的专家，但我将带您踏上 OTP 中最有用、最著名的*行为*—`GenServer`之旅。

[![](img/86317baf4bb1305e6f3852c0413b2983.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--iOaRkU2r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ieftimov.com/img/posts/jon-flobrant-225260-unsplash.jpg) <small>我是一名 OTP 专家👆我知道
怎么开门。乔恩·弗洛布兰特在 Unsplash 上的照片</small>

接下来，我们将使用 BEAM 流程，因此如果您不熟悉生成新流程、向它们发送消息和从它们接收消息，那么最好前往“[了解 Elixir 并发模型的基础知识](https://dev.to/understanding-basics-elixir-concurrency-model)”并快速阅读一下。它将帮助您理解 Elixir 中的流程和并发性，以便您可以将这些知识应用到我们在本文中研究的项目中。我保证。

## 缩短一个环节✂️

让我们编写一个 URL shortener 模块，它将在一个 BEAM 进程中运行，并且可以接收多个命令:

*   `shorten`–获取一个链接，将其缩短并返回该短链接作为响应
*   `get`–获取一个短链接并返回原始链接
*   `flush`–清除网址缩写记忆
*   `stop`–停止该过程

```
defmodule URLShortener do
  def start do
    spawn( __MODULE__ , :loop, [%{}])
  end

  def loop(state) do
    receive do
      {:stop, caller} ->
        send caller, "Shutting down."
      {:shorten, url, caller} ->
        url_md5 = md5(url)
        new_state = Map.put(state, url_md5, url)
        send caller, url_md5
        loop(new_state)
      {:get, md5, caller} ->
        send caller, Map.fetch(state, md5)
        loop(state)
      :flush ->
        loop(%{})
      _ ->
        loop(state)
    end
  end

  defp md5(url) do
    :crypto.hash(:md5, url)
    |> Base.encode16(case: :lower)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

该模块所做的是当进程开始时，它将递归调用`URLShortener.loop/1`函数，直到它接收到`{:stop, caller}`消息。

如果我们放大到`{:shorten, url, caller}`的例子，我们注意到我们从 URL 生成了一个`MD5`摘要，然后我们更新了`state`地图，这创建了一个新的地图(称为`new_state`)。一旦我们得到摘要，我们就把它存储在一个映射中，密钥是 MD5，值是实际的 URL。`state`地图将会是这样的:

```
%{
  "99999ebcfdb78df077ad2727fd00969f" => "https://google.com",
  "76100d6f27db53fddb6c8fce320f5d21" => "https://elixir-lang.org",
  "3097fca9b1ec8942c4305e550ef1b50a" => "https://github.com",
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将 MD5 值发送回调用者。显然，这不是 howbit.ly 或 likes 的工作方式，因为它们的链接要短得多。(对于感兴趣的人，[这里有](https://stackoverflow.com/questions/742013/how-do-i-create-a-url-shortener)个关于这个话题的有趣讨论)。然而，出于本文的目的，我们将坚持 URL 的简单 MD5 摘要。

另外两个命令`get`和`flush`非常简单。`get`只从`state`映射中返回一个值，而`flush`用一个空映射调用`loop/1`，有效地从流程的状态(内存)中删除了所有缩短的链接。

让我们在`IEx`会话中运行我们的 shorter:

```
iex(22)> shortener = URLShortener.start
#PID<0.141.0>

iex(23)> send shortener, {:shorten, "https://ieftimov.com", self()}
{:shorten, "https://ieftimov.com", #PID<0.102.0>}

iex(24)> send shortener, {:shorten, "https://google.com", self()}
{:shorten, "https://google.com", #PID<0.102.0>}

iex(25)> send shortener, {:shorten, "https://github.com", self()}
{:shorten, "https://github.com", #PID<0.102.0>}

iex(26)> flush
"8c4c7fbc57b08d379da5b1312690be04"
"99999ebcfdb78df077ad2727fd00969f"
"3097fca9b1ec8942c4305e550ef1b50a"
:ok

iex(27)> send shortener, {:get, "99999ebcfdb78df077ad2727fd00969f", self()}
{:get, "99999ebcfdb78df077ad2727fd00969f", #PID<0.102.0>}

iex(28)> flush
"https://google.com"
:ok

iex(29)> send shortener, {:get, "8c4c7fbc57b08d379da5b1312690be04", self()}
{:get, "8c4c7fbc57b08d379da5b1312690be04", #PID<0.102.0>}

iex(30)> flush
"https://ieftimov.com"
:ok

iex(31)> send shortener, {:get, "3097fca9b1ec8942c4305e550ef1b50a", self()}
{:get, "3097fca9b1ec8942c4305e550ef1b50a", #PID<0.102.0>}

iex(32)> flush
"https://github.com"
:ok 
```

Enter fullscreen mode Exit fullscreen mode

按预期工作–我们发送三个不同的 URL 进行缩短，我们在流程邮箱中收到它们的 MD5 数字签名，当我们查询它们时，我们得到它们中的每一个。

尽管我们的`URLShortener`模块现在工作得非常整洁，但它实际上缺少相当多的功能。当然，它确实很好地处理了快乐路径，但是当涉及到错误处理、跟踪或错误报告时，它确实做得不够。此外，它没有一个标准的界面来为流程添加更多的功能——我们可以说是在进行过程中想出来的。

读完所有这些之后，你可能会想有一个更好的方法来做到这一点。你这样想是对的——让我们了解更多关于`GenServer` s 的知识。

## 进入 GenServer🚪

`GenServer`是一个 *OTP 行为*。这里的行为指的是三件事:

*   一个接口，它是一组函数；
*   实现，即特定于应用程序的代码，以及
*   容器，这是一个射束过程

这意味着一个模块可以实现一组特定的函数(接口或签名)，这些函数在幕后实现一些回调函数(这些函数特定于您所处理的行为)，这些函数在一个 BEAM 进程中运行。

例如，`GenServer`是一个 **gen** eric **server** 行为——它期望在其接口中定义的每一个函数都有一组回调来处理对服务器的请求。这意味着接口函数将由通用服务器的客户端使用，也称为客户端 API，而定义的回调本质上是服务器内部(“后端”)。

那么，`GenServer`是如何工作的呢？嗯，正如你所想象的，我们不能对`GenServer`的方法进行太深入的探讨，但是我们需要很好地掌握一些基本知识:

1.  服务器启动和状态
2.  异步消息
3.  同步消息

### 服务器启动&状态

就像我们实现的`URLShortener`一样，每个`GenServer`都能够保持状态。事实上，`GenServer` s 必须实现一个`init/1`函数，该函数将设置服务器的初始状态(更多细节参见`init/1`文档[此处](https://hexdocs.pm/elixir/GenServer.html#c:init/1))。

要启动服务器，我们可以运行:

```
GenServer.start_link( __MODULE__ , :ok, []) 
```

Enter fullscreen mode Exit fullscreen mode

`GenServer.start_link/3`将调用`__MODULE__`的`init/1`函数，将`:ok`作为参数传入`init/1`。这个函数调用将阻塞，直到`init/1`返回，所以通常在这个函数中，我们做任何需要的服务器进程设置(可能需要)。例如，在我们的例子中，为了使用`GenServer`行为重建`URLShortener`，我们将需要一个`init/1`函数来设置服务器的初始状态(空映射):

```
def init(:ok) do
  {:ok, %{}}
end 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。`start_link/3`将使用`:ok`参数调用`init/1`，这将返回一个`:ok`并将流程的状态设置为空图。

### 同步&异步消息📨

正如大多数服务器一样，`GenServer` s 也可以接收和回复请求(如果需要的话)。正如标题所示，`GenServers`处理两种类型的请求——一种是期望响应的请求(`call`,另一种是不期望响应的请求(`cast`)。因此，`GenServer` s 定义了两个回调函数——`handle_call/3`和`handle_cast/2`。

稍后我们将更深入地研究这些函数。

## 重新实现`URLShortener`，使用`GenServer` ♻️

让我们看看如何翻转实现来使用`GenServer`。

首先，我们添加模块的外壳，`start_link/1`将要调用的`start_link/1`函数和`init/1`函数:

```
defmodule URLShortener do
  use GenServer

  def start_link(opts \\ []) do
    GenServer.start_link( __MODULE__ , :ok, opts)
  end

  def init(:ok) do
    {:ok, %{}}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这里值得注意的变化是模块中`GenServer`行为的`use`，调用`GenServer.start_link/3`的`start_link/1`函数，实际上，该函数调用带有`:ok`原子作为参数的`init/1`函数。另外，值得注意的是，`init/1`函数在元组中返回的空映射是`URLShortener`进程的实际初始状态。

让我们在`IEx` :
中旋转一下

```
iex(1)> {:ok, pid} = URLShortener.start_link
{:ok, #PID<0.108.0>} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们目前唯一能做的。这里的不同之处在于，`GenServer.start_link/3`函数将返回一个带有 atom ( `:ok`)和服务器的 PID 的 tuple。

### 停止服务器✋

让我们添加`stop`命令:

```
defmodule URLShortener do
  use GenServer

  # Client API
  def start_link(opts \\ []), do: GenServer.start_link( __MODULE__ , :ok, opts)

  def stop(pid) do
    GenServer.cast(pid, :stop)
  end

  # GenServer callbacks
  def init(:ok), do: {:ok, %{}}

  def handle_cast(:stop, state) do
    {:stop, :normal, state}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

是的，我知道我说过我们将添加一个命令，但最终添加了两个函数:`stop/1`和`handle_cast/2`。现在请容忍我:

因为我们不想在`stop`命令上得到响应，我们将在`stop/1`函数中使用`GenServer.cast/2`。这意味着当该命令被服务器的客户端(用户)调用时，将在服务器上触发`handle_cast/2`回调。在我们的例子中，`handle_cast/2`函数将返回一个包含三个条目的元组—`{:stop, :normal, state}`。

返回这个元组会停止循环，并调用另一个名为`terminate/2`的回调函数(在行为中定义，但未由`URLShortener`实现)，原因为`:normal`，状态为`state`。进程将退出，原因是`:normal`。

这种使用`GenServer`的方式允许我们只定义回调，而`GenServer`行为将知道如何处理其余的。唯一的复杂性在于我们需要理解和知道回调函数可以拥有的大多数类型的返回。

另一件值得指出的事情是，客户端使用的每个函数都将把一个`PID`作为第一个参数。这将允许我们向正确的`GenServer`流程发送消息。展望未来，我们不会承认`PID`的存在——我们接受`URLShortener`工作是强制性的。稍后，我们将看看我们可以跳过作为参数的`PID`的方法。

让我们跳回到`IEx`中，启动和停止一个`URLShortener`服务器:

```
iex(1)> {:ok, pid} = URLShortener.start_link
{:ok, #PID<0.109.0>}

iex(2)> Process.alive?(pid)
true

iex(3)> URLShortener.stop(pid)
:ok

iex(4)> Process.alive?(pid)
false 
```

Enter fullscreen mode Exit fullscreen mode

这是开始和停止在所有的荣耀。

### 缩短网址

另一件事，我们希望我们的服务器有能力缩短 URL，通过使用他们的 MD5 摘要作为 URL 的短变量。让我们用`GenServer` :
来做这件事

```
defmodule URLShortener do
  use GenServer

  # Client API
  def start_link(opts \\ []), do: GenServer.start_link( __MODULE__ , :ok, opts)
  def stop(pid), do: GenServer.cast(pid, :stop)

  def shorten(pid, url) do
    GenServer.call(pid, {:shorten, url})
  end

  # GenServer callbacks
  def init(:ok), do: {:ok, %{}}
  def handle_cast(:stop, state), do: {:stop, :normal, state}

  def handle_call({:shorten, url}, _from, state) do
    short = md5(url)
    {:reply, short, Map.put(state, short, url)}
  end

  defp md5(url) do
    :crypto.hash(:md5, url)
    |> Base.encode16(case: :lower)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这次有三个函数，但至少`md5/1`是我们之前那个函数的复制品。那么，让我们看看另外两个。

您可能会看到一种模式——我们有一个将由客户端使用的函数(`shorten/2`)和一个将在服务器上调用的回调函数(`handle_call/3`)。这次使用的函数和命名略有不同:在`shorten/2`中我们调用`GenServer.call/2`而不是`cast/2`，回调名是`handle_call/3`而不是`handle_cast/2`。

为什么？区别在于响应- `handle_call/3`会向客户端发送一个回复(因此响应元组中有`:reply`原子)，而`handle_cast/2`不会这样做。基本上，`cast` ing 是一个异步调用，客户端不期待响应，而`call` ing 是一个同步调用，期待响应。

所以，我们来看看`handle_call/3`回调的结构。

它有三个参数:来自客户机的请求(在我们的例子中是一个元组)，描述请求的客户机的元组(我们忽略了它)，以及服务器的状态(在我们的例子中是一个映射)。

作为响应，它返回一个带有`:reply`的元组，表明将会有一个对请求的回复、回复本身(在我们的例子中是`short`en 链接)和`state`，后者是传递到服务器下一个循环的状态。

当然，`handle_call/3`有一点复杂，我们将在后面研究，但是你可以随时查看它的[文档](https://hexdocs.pm/elixir/GenServer.html#c:handle_call/3)来了解更多。

### 获取缩短的 URL🔗

让我们实现`get`命令，当提供链接的`short`版本时，它将返回完整的 URL:

```
defmodule URLShortener do
  use GenServer

  # Client API
  # ...

  def get(pid, short_link) do
    GenServer.call(pid, {:get, short_link})
  end

  # GenServer callbacks
  # ...

  def handle_call({:get, short_link}, _from, state) do
    {:reply, Map.get(state, short), state}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

再次使用双函数入口模式——我们添加了`URLShortener.get/2`和另一个头函数`URLShortener.handle_call/3`。

`URLShortener.get/2`将调用引擎下的`GenServer.call/2`，当被执行时将导致`handle_call/3`回调被触发。

这次的`URLShortener.handle_call/3`将把命令(`:get`)和`short_link`作为第一个参数。在内部，我们再次看到，这是一个简短的函数——它只返回一个带有`:reply`(表示调用将有回复)的元组，一个对`Map.get/2`的调用，其返回将是调用的实际响应，以及`state`，因此`GenServer`进程在下一个循环中保持状态。

此时，我们可以有把握地说，我们已经很好地了解了为实现`GenServer`行为的模块编写功能的基础知识。正如你可能会想的那样，有很多东西需要探索，但是这些基础知识将允许你创建`GenServer`并进行实验。

在继续之前，尝试再执行两个命令:

*   `flush`–将清除服务器状态的异步调用
*   `count`–返回服务器状态中链接数量的同步调用

## 更多配置🎛

如果我们回到`URLShortener.start_link/1`及其内部(即`GenServer.start_link/3`的调用)，我们还会注意到我们可以将选项(`opts`)传递给`GenServer.start_link/3`函数，这些选项默认为空列表(`[]`)。

我们可以在这里添加哪些选项？通过查看[和`GenServer.start_link/3`的文档](https://hexdocs.pm/elixir/GenServer.html#start_link/3)，你会注意到多个有趣的选项:

*   `:name` -用于名称注册。这意味着我们可以给它起一个名字，而不是通过`PID`来识别一个`GenServer`。
*   `:timeout` -设置服务器启动超时(以毫秒为单位)
*   `:debug` -通过调用`:sys`模块中的相应函数来启用调试
*   `:hibernate_after` -设置 serverprocess 在新请求到来之前自动进入休眠状态的时间(以毫秒为单位)。这是通过利用`:proc_lib.hibernate/3`完成的
*   `:spawn_opt` -允许向底层流程传递更多选项

其中大多数都是高级的，超出了我们这里的使用案例。然而，我们现在可以使用一种配置:`:name`。

### 命名服务器📢

让我们修改我们的`URLShortener`,在其`start_link/1`函数中加入一个`name`,并在`IEx`中测试它。此外，由于每个`URLShortener`进程都有一个名字，我们可以通过名字而不是`PID`来引用进程——让我们看看这在代码中是如何工作的:

```
defmodule URLShortener do
  use GenServer

  # Client API
  def start_link(name, opts \\ []) do
    GenServer.start_link( __MODULE__ , :ok, opts ++ [name: name])
  end

  def stop(name), do: GenServer.cast(name, :stop)
  def shorten(name, url), do: GenServer.call(name, {:shorten, url})

  def get(name, short_link) do
    GenServer.call(name, {:get, short_link})
  end

  # GenServer callbacks
  def init(:ok), do: {:ok, %{}}
  def handle_cast(:stop, state), do: {:stop, :normal, state}
  def handle_call({:shorten, url}, _from, state), do: {:reply, md5(url), Map.put(state, md5(url), url)}

  def handle_call({:get, short_link}, _from, state) do
    {:reply, Map.get(state, short_link), state}
  end

  defp md5(url), do: :crypto.hash(:md5, url) |> Base.encode16(case: :lower)
end 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。我们给`URLShortener.start_link/2`添加了一个新的参数，我们放弃了`PID`的所有用法，用`name`代替了它。

让我们在`IEx` :
里兜一圈吧

```
iex(1)> {:ok, pid} = URLShortener.start_link(:foo)
{:ok, #PID<0.109.0>}

iex(2)> URLShortener.shorten(:foo, "https://google.com")
"99999ebcfdb78df077ad2727fd00969f"

iex(3)> URLShortener.get(:foo, "99999ebcfdb78df077ad2727fd00969f")
"https://google.com"

iex(4)> URLShortener.stop(:foo)
:ok

iex(5)> Process.alive?(pid)
false 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到这很酷——我们没有使用`PID`而是给流程添加了一个名字`:foo`,这允许我们使用名字而不是`PID`来引用它。显然，你可以看到，为了以任何方式检查波束过程，我们仍然需要`PID`，但是对于客户端来说，`name`已经完成了。

名称和`PID`的组合允许我们引用 BEAM 过程，同时提高客户端的易用性。

如果我们想进一步简化事情，我们可以将`URLShortener`变成一个“单一”服务器。在你抓狂之前——它没有在面向对象编程中臭名昭著的[单例模式](https://ieftimov.com/singleton-pattern)的任何缺点。我们只是声明，我们可以通过为它设置一个静态名称:
来改变`URLShortener`，使其在某一时刻有且只有一个进程在运行

```
defmodule URLShortener do
  use GenServer

  @name :url_shortener_server

  # Client API
  def start_link(opts \\ []) do
    GenServer.start_link( __MODULE__ , :ok, opts ++ [name: @name])
  end

  def stop, do: GenServer.cast(@name, :stop)
  def shorten(url), do: GenServer.call(@name, {:shorten, url})

  def get(short_link) do
    GenServer.call(@name, {:get, short_link})
  end

  # GenServer callbacks
  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

您可以注意到，我们添加了一个模块属性`@name`，它保存了进程的名称。在来自客户端 API 的所有函数中，我们从参数列表中删除了`name`,我们简单地使用`@name`作为对流程的引用。这意味着只有一个名为`:url_shortener_server`的`URLShortener`进程。

让我们在`IEx` :
里兜一圈吧

```
iex(1)> {:ok, pid} = URLShortener.start_link
{:ok, #PID<0.108.0>}

iex(2)> URLShortener.shorten("https://google.com")
"99999ebcfdb78df077ad2727fd00969f"

iex(3)> URLShortener.shorten("https://yahoo.com")
"c88f320dec138ba5ab0a5f990ff082ba"

iex(4)> URLShortener.get("99999ebcfdb78df077ad2727fd00969f")
"https://google.com"

iex(5)> URLShortener.stop
:ok

iex(6)> Process.alive?(pid)
false 
```

Enter fullscreen mode Exit fullscreen mode

您可以注意到，尽管我们在第一行捕获了`PID`，但我们根本不需要它——所有的工作都由`URLShortener`为我们完成了。

在本节中，您看到了如何利用名称来更轻松地处理流程。让我们回顾一下`URLShortener`模块的完整实现。

## 其他□

在我们结束这篇冗长的教程之前，让我们最后看一下我们的新`URLShortener`模块，包括`count/1`和`flush/1`函数:

```
defmodule URLShortener do
  use GenServer

  # Client API
  def start_link(name, opts \\ []) do
    GenServer.start_link( __MODULE__ , :ok, opts ++ [name: name])
  end

  def shorten(name, url) do
    GenServer.call(name, {:shorten, url})
  end

  def get(name, short) do
    GenServer.call(name, {:get, short})
  end

  def flush(name) do
    GenServer.cast(name, :flush)
  end

  def stop(name) do
    GenServer.cast(name, :stop)
  end

  def count(name) do
    GenServer.call(name, :count)
  end

  # Callbacks
  def init(:ok) do
    {:ok, %{}}
  end

  def handle_cast(:flush, _state) do
    {:noreply, %{}}
  end

  def handle_cast(:stop, state) do
    {:stop, :normal, state}
  end

  def handle_call({:shorten, url}, _from, state) do
    shortened = md5(url)
    new_state = Map.put(state, shortened, url)
    {:reply, shortened, new_state}
  end

  def handle_call({:get, short}, _from, state) do
    {:reply, Map.get(state, short), state}
  end

  def handle_call(:count, _from, state) do
    count = Map.keys(state) |> Enum.count
    {:reply, count, state}
  end

  defp md5(url) do
    :crypto.hash(:md5, url)
    |> Base.encode16(case: :lower)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这两个回调相当简单- `flush`将只发送一个`noreply`并将状态设置为空映射。另一方面，`count`将会有一个`reply`来计算地图中的物品数量，也就是`state`地图中钥匙的数量。仅此而已。

虽然您已经读到了文章的结尾，但您与`GenServer`的旅程并没有就此结束。其实才刚刚开始。s 和 OTP 是非常强大的工具，您可以使用它们来构建通用服务器，这些服务器可以存在于小束进程中，并且具有非常通用的构建功能(调用和回调)的方法。

虽然我们在这里覆盖了很多领域，但是我们没有触及为什么我们将启动函数命名为`start_link`而不是仅仅命名为`start`(提示:管理约定)，或者我们将如何测试像`URLShortener`这样的`GenServer`。

你在什么样的场景下用过`GenServers`？或者，如果你没有使用它的经验，你认为你将来会在哪里使用它？
# Mix 和 OTP 04 :管理员和 APP 应用程序

> 原文：<https://dev.to/gumi/mix-otp-04-bj0>

本文在 Elixir 官方网站的许可下，根据“[Supervisor and Application](https://elixir-lang.org/getting-started/mix-otp/supervisor-and-application.html) ”的解说，加以修改，对 Elixir 中`Supervisor`的使用方法和 APP 应用程序的处理方法进行说明

软件发生失败时，大多数都会想“恢复”它。 但是，Elixir 不会进行为了消除例外的被动编程。 倒不如说是“任其坠落”。 即使进程因错误崩溃，也不用担心。 因为您只需要定义一个管理员，然后移动流程的新副本来代替它。

# 第一次做管理员

`Supervisor`的制作方法与`GenServer`几乎没有区别。 但是，使用的行为为[`Supervisor`](https://hexdocs.pm/elixir/Supervisor.html)。 请在新的文件`lib/kv/supervisor.ex`中如下确定模块`KV.Supervisor`。

```
defmodule KV.Supervisor do
  use Supervisor

  def start_link(opts) do
    Supervisor.start_link(__MODULE__, :ok, opts)
  end

  def init(:ok) do
    children = [
      KV.Registry
    ]

    Supervisor.init(children, strategy: :one_for_one)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

管理员总要给孩子一个`KV.Registry`。 决定子列表后，将列表和监视策略交给[`Supervisor.init/2`](https://hexdocs.pm/elixir/Supervisor.html#init/2)进行调用。 监视策略是其中一个孩子崩溃时怎么办的指示。 `:one_for_one`是指如果有一个孩子掉了，就只重新启动一个孩子。 现在只有一个孩子，这样就可以了吧。 `Supervisor`行为还具备很多其他的战略。

管理员启动后，对列表中存储的所有子项调用各模块的[`child_spec/1`函数](https://hexdocs.pm/elixir/Supervisor.html#module-child_spec-1)。 返回值正如名字所示，是子代的规格。 指示流程开始的方式，以及流程是工作人员还是主管，流程是临时的、临时的还是永久的。 例如，以下信息: `child_spec/1`函数在使用`Agent`、`GenServer`或`Supervisor`时会自动确定。

```
iex> KV.Registry.child_spec([])
%{
  id: KV.Registry,
  restart: :permanent,
  shutdown: 5000,
  start: {KV.Registry, :start_link, [[]]},
  type: :worker
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦管理员得到子代的所有规范，它就会按列表的顺序逐个开始子代。 开始方法如规格的`:start`键所示，以上示例调用`KV.Registry.start_link([])`。

在“[Mix 和 OTP 03: GenServer](https://dev.to/gumi/mixotp-03-genserver-2jhn) ”中，测试时使用`start_supervised！/2`函数开始了过程。 `start_supervised！/2`是在内部基于`ExUnit`框架规定的管理员开始进程的。 通过自行定义管理员，您可以配置初始化和退出 APP 应用程序、注册监视等，以优化最终代码和测试。

目前`start_link/1`作为选项总是接收空列表。

# 命名进程

APP 制作很多流程。 但是，注册它们的`KV.Registry`只有一个。 因此，给`KV.Registry`起个名字吧，而不是用 PID 参照。 这样，总是用名字得到参照。

制作的过程是根据用户的输入动态开始的。 因此，管理进程时，不应该给阿童木命名。 注册的`KV.Registry`不同。 APP 应用程序启动时，在管理员的指导下只启动一个

那么，上述模块`KV.Supervisor`(`lib/kv/supervisor.ex`)规定的管理员的孩子，为了成为元组的列表稍微修改一下吧。

```
def init(:ok) do
  children = [
    # KV.Registry
    {KV.Registry, name: KV.Registry}
  ]

  Supervisor.init(children, strategy: :one_for_one)
end 
```

Enter fullscreen mode Exit fullscreen mode

此前，管理员对子进程调用了`KV.Registry.start_link([])`。 根据修正，变成了`KV.Registry.start_link([name: KV.Registry])`的调用。 `KV.Registry.start_link/1`的安装如下。 在以上修改中添加的名称作为`:name`被传递给该[`GenServer.start_link/3`](https://hexdocs.pm/elixir/GenServer.html#start_link/3)的第 3 自变量的选项中(参照“[Name registration](https://hexdocs.pm/elixir/GenServer.html#module-name-registration) ”)。

```
def start_link(opts) do
  GenServer.start_link(__MODULE__, :ok, opts)
end 
```

Enter fullscreen mode Exit fullscreen mode

这样就在进程中登记了名称。 编译后用`iex -S mix`的外壳试试吧。 `KV.Registry.lookup/2`的第 1 自变量可以使用登录的名字。

```
iex> KV.Supervisor.start_link([])
{:ok, #PID<0.130.0>}
iex> KV.Registry.create(KV.Registry, "shopping")
:ok
iex> KV.Registry.lookup(KV.Registry, "shopping")
{:ok, #PID<0.134.0>} 
```

Enter fullscreen mode Exit fullscreen mode

启动管理员后，注册的`KV.Registry`会在指定名称后自动开始。 注册的过程，只要做好就开始了。

实际上，我很少写启动 APP 应用程序管理员的处理。 因为它是作为 APP 应用程序回调的一部分启动的。

# 理解 APP 配置

到目前为止，我一直在编写和运行 APP 应用程序的代码。 每次对代码进行修改时，文件都必须编译。 届时，将导出`_build/dev/lib/kv/ebin/kv.app`，打开后可以看到以下记述。

```
{application,kv,
             [{applications,[kernel,stdlib,elixir,logger]},
              {description,"kv"},
              {modules,['Elixir.KV','Elixir.KV.Bucket','Elixir.KV.Registry',
                        'Elixir.KV.Supervisor']},
              {registered,[]},
              {vsn,"0.1.0"}]}. 
```

Enter fullscreen mode Exit fullscreen mode

用 Erlang 的语法写了设定。 即使不知道 Erlang，也可以推测出是 APP 的规格吧。 显示了 APP 应用的版本和规定的模块、以及依赖的 APP 应用( Erlang 的`kernel`和`stdlib`、`elixir`自身以及`logger`)等。

每次添加新的模块时，都要添加到这个文件中很麻烦。 相反，Mix 会制作并更新文件。 此外，还可以通过在`mix.exs`项目文件中定义`application/0`进行自定义(参照“[The application environment](https://hexdocs.pm/elixir/Application.html#module-the-application-environment) ”)。

# 启动 APP 配置

`.app`一旦确定了文件，就可以根据其规格将 APP 应用程序作为整体启动并结束。

1.  Mix 可以自动启动 APP 应用程序。
2.  如果 Mix 未启动，则在启动 APP 应用程序之前不执行任何操作。

试着在 Mix 上启动 APP 应用程序吧。 请在`iex -S mix`中打开项目控制台。

APP 宣传以[`Application.start/2`](https://hexdocs.pm/elixir/Application.html#start/2)开始，以[`Application.stop/1`](https://hexdocs.pm/elixir/Application.html#stop/1)结束。 缺省情况下，Mix 会自动启动项目的 mix.exs 定义的整个 APP 应用程序层次结构。 如果存在其他依赖的 APP 应用程序，则它们也是如此。

```
iex> Application.start(:kv)
{:error, {:already_started, :kv}}
iex> Application.stop(:kv)

00:00:00.000 [info]  Application kv exited: :stopped
:ok
iex> Application.start(:kv)
:ok 
```

Enter fullscreen mode Exit fullscreen mode

也可以将选项传递给 Mix，以便不启动 APP 应用程序。 输入`iex -S mix run --no-start`打开控制台。

在`Application.start/2`处开始 APP 展。 `:logger`是 Elixir 默认启动的 APP 应用程序。 如果停止依赖的 APP 应用程序，则必须先启动它才能重新启动。

```
iex> Application.start(:kv)
:ok
iex> Application.stop(:kv)

00:00:00.000 [info]  Application kv exited: :stopped
:ok
iex> Application.stop(:logger)
=INFO REPORT==== 20-Aug-2018::00:00:00.000000 ===
    application: logger
    exited: stopped
    type: temporary
:ok
iex> Application.start(:kv)
{:error, {:not_started, :logger}}
iex> Application.start(:logger)
:ok
iex> Application.start(:kv)
:ok 
```

Enter fullscreen mode Exit fullscreen mode

或者，可以与在[`Application.ensure_all_started/2`](https://hexdocs.pm/elixir/Application.html#ensure_all_started/2)中依赖的 APP 应用程序一起启动。

```
iex> Application.stop(:kv)

00:00:00.000 [info]  Application kv exited: :stopped
:ok
iex> Application.stop(:logger)
=INFO REPORT==== 20-Aug-2018::00:00:00.000000 ===
    application: logger
    exited: stopped
    type: temporary
:ok
iex> Application.ensure_all_started(:kv)
{:ok, [:logger, :kv]} 
```

Enter fullscreen mode Exit fullscreen mode

> `iex -S mix`是`iex -S mix run`的省略法。 添加选项时，必须在`run`指令之后添加，不能省略。 `run`关于指令和可以使用的选项可以在`mix help run`中确认。

# APP 应用程序回调

知道启动 APP 应用程序后，能做什么呢？ 列举了一个，就是定义`application`回调函数。 在 APP 应用程序启动时调用回调。 函数必须返回`{:ok, pid}`。 `pid`是管理员进程的标识符。

APP 回调由两个步骤组成。 首先，在`mix.exs`的函数`application`中添加`:mod`选项的 APP 回调模块。 元组的第一个元素是模块，第二个元素是启动 APP 应用程序时传递的参数。 只要实现了[`Application`行为](https://hexdocs.pm/elixir/Application.html)，APP 回调模块都可以。

```
def application do
  [
    extra_applications: [:logger],
    mod: {KV, []}
  ]
end 
```

Enter fullscreen mode Exit fullscreen mode

第二，在 APP 回调模块中设置回调函数。 APP 程序开始时调用的是[`start/2`](https://hexdocs.pm/elixir/Application.html#start/2)。 结束时要调用的回调请添加为[`stop/1`](https://hexdocs.pm/elixir/Application.html#stop/1)。 在此，对项目中默认制作的模块`KV`的`lib/kv.ex`进行如下改写。 请不要忘记在模块中添加`use Application`。

编译项目后，在`iex -S mix`中打开控制台。 于是，可以确认`KV.Registry`已经在动了吧。

```
defmodule KV do
  use Application

  def start(_type, _args) do
    KV.Supervisor.start_link(name: KV.Supervisor)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> KV.Registry.create(KV.Registry, "shopping")
:ok
iex> KV.Registry.lookup(KV.Registry, "shopping")
{:ok, #PID<0.134.0>} 
```

Enter fullscreen mode Exit fullscreen mode

`KV.Registry`的过程确实使用了。 但是，`KV.Registry.create/2`呼叫的是`GenServer.cast/2`。 也就是说，无论有无消息的目标，都会立即返回`:ok`。 因此，到此为止，不知道是否有管理者或服务器在工作，制定了流程。 但是，`KV.Registry.lookup/2`使用的是`GenServer.call/3`。 也就是说，等待服务器的响应。 回复了响应，表示运转正常。

# 项目和 APP

Mix 区分项目和 APP 应用程序。 根据`mix.exs`的内容制作的是 APP 规定的 Mix 项目。 但是，也有不包含 APP 应用程序的项目。

项目用 Mix 制作。 Mix 是管理项目的工具。 可以编译和测试项目等。 此外，还可以编译和启动相关的 APP 应用程序。 在 APP 交流的时候，考虑的是 OTP。 APP 应用程序是运行时启动和结束的整个内容。

#### Mix 和 OTP 也抽签

*   [尝试使用 Mix 和 OTP 01:mix](https://dev.to/gumi/mixotp-01-mix-dg1)
*   MixとOTP 02:经纪人
*   MixとOTP 03:基因服务器
*   Mix 和 OTP 04 :管理员和 APP 应用程序
*   [Mix 和 OTP 05 :动态管理员](https://dev.to/gumi/mix-otp-05-l3p)
*   [MixとOTP 06: ETS](https://dev.to/gumi/mix-otp-06-ets-ol3)
*   [Mix 和 OTP 07 :依存关系和模糊项目](https://dev.to/gumi/mix-otp-07-1p5k)
*   MixとOTP 08: Taskとgen_tcp
*   [Mix 和 OTP 09: DocTest 和 with 的模式匹配](https://dev.to/gumi/mix-otp-09-doctest-with-533g)
*   [Mix 和 OTP 10 :分散处理的 Task 和设定](https://dev.to/gumi/mix-otp-10-3i31)
# 使用本地酏剂方法做背景工作

> 原文：<https://dev.to/appsignal/doing-background-work-using-a-native-elixir-approach-2id8>

如果你以前用过 Ruby，*后台作业队列*是你在 Elixir 中构建应用程序时可能会本能地用到的一个工具。虽然存在这样的解决方案，但你可能会发现它们在酏剂中用得不多，这可能会让你想知道为什么。我的意思是，Elixir 应用程序不执行异步作业吗？是啊！但是对它的思考有点不同。

今天，我们将了解 Elixir 用于执行后台工作的不同解决方案，以及何时使用哪种解决方案。在这一过程中，我们将了解 Elixir 的本机结构如何使后台工作成为一件产生进程的美妙事情。

## 一些后台:后台作业队列

几乎所有的 production Rails 应用程序都使用后台作业框架，比如 [Sidekiq](https://sidekiq.org/) 。这是用来做几件事的:

*   推迟昂贵的工作，这样用户就不会一直等待，例如，当使用从外部来源获取的数据设置帐户时
*   执行不是由用户发起的持续后台工作，例如获取加密货币的当前值
*   通过将不重要的工作(例如发送欢迎电子邮件)从用户请求周期中移出，加快响应速度

如果我们在长生不老药里做这件事，我们将如何利用长生不老药的优势来处理它？

Elixir 有几个执行后台工作的工具。主要有:*主管*、*发电服务器*、*任务*。您选择使用哪一个取决于您的应用程序和用例。让我们深入了解一下您何时会使用每一种工具！

## 延期工作

在第一个示例中，假设我们有一个连接到两个外部数据源的系统来完成用户的帐户设置:我们使用用户提供的地址来查找离他们的位置最近的订单执行中心，然后我们将用户提供的姓名和生日发送到一个单独的 CRM。然后，我们将用户的`fulfillment_center_id`和`crm_id`键存储在我们的主数据库中，这样我们可以在以后的某个时间获取这些外部记录。

此操作的关键方面是:1)这两项工作可以相互独立地完成，2)我们希望工作在失败时重试。我们也不关心这些任务的返回值——它们在操作的初始线程之外将副作用写入数据库。为此，我们将使用*监督任务*。

```
defmodule YourApp.Application do
  use Application

  def start(_type, _args) do
    import Supervisor.Spec

    children = [
      {Task.Supervisor, name: YourApp.AccountSetupSupervisor}
    ]

    opts = [strategy: :one_for_one, name: YourApp.Supervisor]
    Supervisor.start_link(children, opts)
  end
end 
```

我们定义了一个在应用程序启动时开始的`Task.Supervisor`。在这里，我们将其命名为`YourApp.AccountSetupSupervisor`，这为我们的用例赋予了一个很好的语义。你的系统可以有许多`Task.Supervisors`来管理不同类型的任务。

```
defmodule YourApp.AccountSetupSupervisor do
  def set_up_user_account(user) do
    opts = [restart: :transient]

    Task.Supervisor.start_child(__MODULE__, YourApp.CRM, :create_user, [user], opts)
    Task.Supervisor.start_child(__MODULE__, YourApp.Fulfillment, :set_nearest_location, [user], opts)
  end
end 
```

这是我们的任务主管，他有一个关键功能`set_up_user_account/1`。一旦基本用户数据保存到数据库中，控制器或帐户上下文就会调用它来完成帐户设置。该函数生成两个任务，每个任务执行指定的函数，以连接到我们的 CRM 和我们的履行服务。

`restart: :transient`选项告诉管理程序在任务异常退出时重启任务，例如，当连接失败和进程崩溃时。默认情况下，在放弃之前，supervisor 会在 5 秒钟内最多尝试重新启动进程 3 次。如果操作成功，进程正常退出，一切照常进行。

这段代码期望我们有一个带函数`create_user/1`的模块`YourApp.CRM`和一个带函数`set_nearest_location/1`的模块`YourApp.Fulfillment`。下面是其中一个模块的样子:

```
defmodule YourApp.CRM do

  def create_user(user) do
    crm_data =
      user
      |> fetch_crm_data()
      |> parse()

    user
    |> YourApp.User.crm_changeset(crm_data)
    |> YourApp.Repo.update!()
  end

  defp fetch_crm_data(user) do
    # your code here
  end

  defp parse(data) do
    # you will probably want to parse results for use in a changeset
  end
end 
```

看起来很相似。以这种方式构建我们的代码有助于清晰的接口、单一的职责和可重用的组件。

现在，当系统努力工作时，用户不会有任何延迟。控制器立即返回给用户，用户可以看到一个有用的屏幕，而系统继续在后台做它的事情。像巧克力一样甜！

## 正在进行的后台工作

好的，那么*正在进行的后台工作*呢，它定期发生，并且不是由用户发起的。为此，我会使用一台普通的*发电机服务器*。让 GenServers 变得伟大的两件事是，您不必像使用 Sidekiq 那样序列化它们的参数，并且由于 BEAM 的公平调度，资源密集型进程不会对您面向用户的响应造成巨大的消耗。另外，在`observer`的帮助下，通过将一些有用的统计数据放入它的`state`，你可以很容易地监控你的 Genserver 正在做什么。

让我们深入研究加密货币账户价值的例子。假设我们的前端通过 Phoenix LiveView 实时更新 DOM。我们需要每秒 ping 我们的节点以获取最新的数据，然后使用这些数据来刷新用户看到的内容。对于这种重复出现的任务，我使用了一种技术，在应用程序启动期间启动一个 GenServer。1000 毫秒后，GenServer 请求光束向其发送一条`work`消息，然后进入睡眠状态。时间间隔过后，光束将消息发送到 GenServer，后者唤醒并开始工作，计划在 1000 毫秒后发送另一条消息，然后返回睡眠状态。

关键是休眠进程不会影响系统性能，所以您可以让许多调度程序同时休眠，可能每个用户一个。

```
defmodule CryptoApp.AccountSync do
  use GenServer

  @interval 1_000

  def start_link do
    GenServer.start_link(__MODULE__, :ok, name: __MODULE__)
  end

  def init(:ok) do
    Process.send_after(self(), :work, @interval)
    {:ok, %{last_run_at: nil}}
  end

  def handle_info(:work, state) do
    fetch_account_balance()
    Process.send_after(self(), :work, @interval)

    # By storing last_run_at in state we get basic monitoring via process inspection.
    # It's just for convenience - don't use this technique for sophisticated instrumentation of your processes.
    {:noreply, %{last_run_at: :calendar.local_time()}}
  end

  defp fetch_account_balance do
    # your code here
  end
end 
```

在这里，我们的 GenServer 处理调度和工作本身，这会在我们的时间安排上产生一些偏差。这对于您的用例来说可能很好，但是，如果工作必须在定时间隔内发生，让调度 GenServer 创建一个*新的*进程来完成工作，让它只负责设置计时器。

请注意，如果您为每个用户启动一个 GenServer，您可能会向外部资源请求最新的数据。记住，GenServers 都是并发工作的！在 Elixir 中，调度和并发很容易——也许太容易了。作为程序员，您仍然必须了解您的系统是如何工作的，以及并发性是否适合您。

## 移动不必要的工作

现在来看第三个场景，我们希望通过去掉不必要的工作来加快对用户的响应速度。关键词是“非必要的”，包括一次性或一次性的操作。如果用户没有收到欢迎电子邮件，或者丢失了一个数据点，也没关系。您可以简单地将您的代码包装在一个`Task.start`块中，就这样！

```
defmodule App.UserService do

  def create_user(params) do
    %App.User{}
    |> App.User.changeset(params)
    |> App.Repo.insert()
    |> case do
      {:ok, user} ->
        Task.start(fn -> send_email_to_user(user) end) # 🎉
        {:ok, user}

      {:error, changeset} ->
        {:error, changeset}
    end
  end

  defp send_email_to_user(user) do
    # send the email
  end
end 
```

这种方法的缺点是，它不允许您在失败时重试。对于关键工作，启动一个管理程序，让它产生一个可以重新启动的任务。

## 其他注意事项和 RabbitMQ

不要认为这意味着你不应该使用后台工作框架。在某些情况下使用它是有意义的。例如，如果您的系统在作业过程中重新启动，工作可能会丢失。在这种情况下，您需要有一个独立的系统，能够经受住应用程序重启。像 [RabbitMQ](https://www.rabbitmq.com/) 这样的消息代理将是一个不错的选择。但那是另一天的话题。我们可以(也可能)为此写一整篇博客。

此外，在重试作业时要考虑周全。真的需要重审吗？通常，当我们的 Sidekiq 仪表盘出现故障时，我们只是清除它们。重要的重试通常是手动进行的，或者至少在工程师的密切监督下进行，而不是盲目地重新排队。

## 结论

后台作业系统有许多活动部件，因此也有故障点。它们可能是值得的，但是在我们仔细检查我们的用例之前，我们不应该在我们的应用程序中构建一个。Erlang 的过程模型为我们提供了一个解决这类问题的多样化工具包，我们不应该错误地把编写灵丹妙药代码当成 Ruby。不是的。我们对过程了解得越多，我们的架构决策就越好，越习惯。我们希望这是学习过程中有趣的一步。

*客座作家 Desmond 是 [EMPEX 系列会议](https://empex.co/)的创始人、 [ElixirTalk 播客](http://elixirtalk.com/)的共同主持人，以及精品软件咨询公司 [Crevalle](http://crevalle.io/) 的负责人。闲暇时，他喜欢冲浪、玩弹球和骑他的摩托车 Porco Rosso。*
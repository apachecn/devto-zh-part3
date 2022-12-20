# 实时视图:第一小步

> 原文：<https://dev.to/revent/live-view-first-tiny-steps-18dg>

前几天[凤凰 live view 上市](https://twitter.com/chris_mccord/status/1106291353670045696)。自从第一次[在 elixir conf 上先睹为快](https://www.youtube.com/watch?v=Z2DU0qLfPIY)以来，我就一直期待着它的发布。尽管处于如此早期的开发阶段，它已经有了伟大的[介绍](https://dockyard.com/blog/2018/12/12/phoenix-liveview-interactive-real-time-apps-no-need-to-write-javascript)、[文档](https://github.com/phoenixframework/phoenix_live_view)和[示例](https://github.com/chrismccord/phoenix_live_view_example)。也已经有许多其他的社区博客。Twitter 上的#liveview 标签是一个很好的资源，可以用来了解社区已经在用它建立什么。

##### 假设

*   你知道什么是[实时取景](https://www.youtube.com/watch?v=Z2DU0qLfPIY)并让它[设置](https://github.com/phoenixframework/phoenix_live_view)。

##### 在我的机器上工作

*   仙丹:1.8.1
*   凤凰城:1.4.1

* * *

我们将创建一个世界时钟应用程序，供用户选择并查看世界各地的时间。

让我们从直接在 routes 中注册实时视图开始。

```
defmodule MyAppWeb.Router do
  ...

  scope "/", MyAppWeb do
    pipe_through :browser

    # Registering live view handler for specific URL
    live "/time", TimeLive
  end
end 
```

在路线中注册实时视图的一个问题是提供默认布局。否则，您可能会遇到这样的情况:在最初的请求中将返回视图 HTML 本身，但周围没有 HTML。最重要的是，js 包括从前端处理实时视图更新将会丢失。

```
defmodule MyAppWeb.Router do
  ...

  pipeline :browser do
    plug Phoenix.LiveView.Flash
    ...
    # Add default layout
    plug :put_layout, {MyAppWeb.LayoutView, :app}
  end

  ...
end 
```

## 第一小步，显示 UTC 实时时间

```
defmodule MyAppWeb.TimeLive do
  @moduledoc """
  A module that renders and handles all interactions from ui
  """

  use Phoenix.LiveView

  @doc """
  Renders this simple html with ony one plain varaible output
  """
  def render(assigns) do
    ~L"<%= @time %>"
  end

  @doc """
  First initial view state setup
  - Registers tick every second
  - Delegates work to handle tick
  """
  def mount(_session, socket) do
    :timer.send_interval(1000, self(), :tick)

    {:ok, handle_tick(socket)}
  end

  @doc """
  Delegates work to handle tick
  """
  def handle_info(:tick, socket) do
    {:noreply, handle_tick(socket)}
  end

  @doc """
  Handles each tick.
  - Gets current time in a human readable format
  - saves it for view to use
  """
  defp handle_tick(socket) do
    time = NaiveDateTime.utc_now()
    assign(socket, :time, time)
  end
end 
```

工作流程:

*   `mount`回调是调用一个初始状态(当前时间)为视图准备的；
*   调用带有先前准备好的数据的回调，并呈现 HTML
*   HTML 被发送到浏览器；
*   每秒(每个滴答)`handle_info`调用带有`:tick`消息的回调，触发视图重新计算当前时间；
*   实时视图检测状态变化，并再次触发`render`回调，生成新的 HTML
*   HTML 被发送到浏览器(我假设有一些优化，比如发送唯一的 diff，或者类似的东西。将不得不对此进行调查)。

## 允许用户选择时区

我还是不太明白《长生不老药中的[时区。默认情况下，elixir 只支持`Etc/UTC`。关于](https://hexdocs.pm/elixir/DateTime.html#module-time-zone-database) [tzdata](https://github.com/lau/tzdata) 的发现。设置它超出了这个范围，但是很容易。

```
defmodule MyAppWeb.TimeLive do
  ...

  @doc """
  Renders html via phoenix tempaltes
  """
  def render(assigns) do
    MyAppWeb.LiveView.render("time.html", assigns)
  end

  @doc """
  Now when mounting we assign all timezones (for the user to choose from dropdown)
  Assign changeset (using changeset, because will accept input from the user)
  """
  def mount(_session, socket) do
    :timer.send_interval(1000, self(), :tick)

    socket =
      socket
      |> assign(:timezones, Tzdata.zone_list())
      |> assign(:changeset, LocalTime.timezone_changeset(%{timezone: "Etc/UTC"}))
      |> handle_tick()

    {:ok, socket}
  end

  @doc """
  Accepting dropdown value change.
  Creates new changeset with newly selected timezone.
  """
  def handle_event("update_timezone", %{"local_time" => new_timezone}, socket) do
    socket =
      socket
      |> assign(:changeset, LocalTime.timezone_changeset(new_timezone))
      |> handle_tick()

    {:noreply, socket}
  end

  @doc """
  Now handling tick means to update time, not with `NaiveDateTime`, but for selected timezone. 
  """
  defp handle_tick(socket) do
    assign(socket, :changeset, LocalTime.update_time(socket.assigns.changeset))
  end
end 
```

提取的视图模板`lib/my_app_web/templates/live/time.html.leex`如下所示:

```
<%= f = form_for @changeset, "#", [phx_change: "update_timezone"] %>
  <%= select f, :timezone, @timezones, promt: "Select timezone" %>
  <%= text_input f, :time, readonly: true %>
</form> 
```

您可能已经注意到有多个对`LocalTime`模块的引用。为了让变更集正常工作，我必须创建一个嵌入式模式。它还将负责验证用户输入。

```
defmodule LocalTime do
  @moduledoc """
  Handling data integrty
  """
  use Ecto.Schema

  alias Ecto.Changeset

  embedded_schema do
    field :timezone, :string
    field :time, :string
  end

  @doc """
  Changeset that does heavy lifting of validating if user input is correct
  """
  def timezone_changeset(params \\ %{}) do
    %__MODULE__{}
    |> Changeset.cast(params, [:timezone])
    |> Changeset.validate_inclusion(:timezone, Tzdata.zone_list())
    |> Map.put(:action, :insert)
  end

  @doc """
  Updates time for the specified changeset.
  If a changeset is invalid, there is no point on updating time, as the user has specified the wrong timezone.
  If timezone is correct, create calculate current datetime for specified timezone 
  """
  def update_time(%Changeset{valid?: false} = changeset) do
    changeset
  end

  def update_time(%Changeset{changes: %{timezone: timezone}} = changeset) do
    {:ok, time} = DateTime.now(timezone)
    Changeset.force_change(changeset, :time, time)
  end
end 
```

## 结论

直播看起来是很棒的技术。还有很多东西需要我去把握(可能更凤凰框架明智)。操纵变更集似乎很奇怪。`LocalTime`不应该通过`Changeset`更新时间。或者应该是这样，我只是想把事情变得更复杂。无论哪种情况，在大量使用 vuejs 一年后。这看起来如此相似，却又如此不同。

[![Same same, but different](img/a8b4290314be091881c585fd6408c6c7.png)](https://i.giphy.com/media/C6JQPEUsZUyVq/giphy.gif)

我想知道，实时视图堆栈是否也会像所有那些现代 js 框架一样增长。路由器为当你想去不同的网页，但只重新载入部分网页。如何将实时视图模板嵌套平移出来？一些共享的状态管理器，像 redux 或 vuex。是否最终会出现智能和愚蠢的实时视图组件的划分。

随着所有现代 js 热潮的兴起，无头 cms 和 T2 CRMs 似乎成为了一种趋势。当您不想维护两个堆栈(后端、前端)并通过 json API 进行通信时，实时视图似乎是一个明智的选择。有一个地方，不要误会我，但我已经看到了许多地方，在那里它是因为时尚而引入的。所有随之而来的复杂性开销都是不必要的。

对我来说，实时视图有一个甜蜜点，在那里我可以看到我如何用 jquery 做我能做的一切(数据相关的 dom 操作方式，不知道动画 jet)，我用 vuejs 能做的 80%，但是用最小的新技术栈开销(这将归结为组件可重用性，如果这样的事情甚至在路线图中)。

你对此有什么想法？会不会或者可能已经用过了？你喜欢吃吗?你看到它的位置了吗，在你的工具箱里？让我知道。

祝你愉快。

另外，如果我对某些问题不清楚的话。如果我的代码中有 bug，或者只是我跳过的东西，也请让我知道。

### 整个代码不带注释

```
defmodule MyAppWeb.TimeLive do
  @moduledoc """
  A module that renders and handles all interactions from ui
  """

  use Phoenix.LiveView

  @doc """
  Renders this simple html with ony one plain varaible output
  """
  def render(assigns) do
    MyAppWeb.LiveView.render("time.html", assigns)
  end

  def mount(_session, socket) do
    :timer.send_interval(1000, self(), :tick)

    socket =
      socket
      |> assign(:timezones, Tzdata.zone_list())
      |> assign(:changeset, LocalTime.timezone_changeset(%{timezone: "Etc/UTC"}))
      |> handle_tick()

    {:ok, socket}
  end

  def handle_info(:tick, socket) do
    {:noreply, handle_tick(socket)}
  end

  def handle_event("update_timezone", %{"local_time" => new_timezone}, socket) do
    socket =
      socket
      |> assign(:changeset, LocalTime.timezone_changeset(new_timezone))
      |> handle_tick()

    {:noreply, socket}
  end

  defp handle_tick(socket) do
    assign(socket, :changeset, LocalTime.update_time(socket.assigns.changeset))
  end
end

defmodule LocalTime do
  use Ecto.Schema

  alias Ecto.Changeset

  embedded_schema do
    field :timezone, :string
    field :time, :string
  end

  def timezone_changeset(params \\ %{}) do
    %__MODULE__{}
    |> Changeset.cast(params, [:timezone])
    |> Changeset.validate_inclusion(:timezone, Tzdata.zone_list())
    |> Map.put(:action, :insert)
  end

  def update_time(%Changeset{valid?: false} = changeset) do
    changeset
  end

  def update_time(%Changeset{changes: %{timezone: timezone}} = changeset) do
    {:ok, time} = DateTime.now(timezone)
    Changeset.force_change(changeset, :time, time)
  end
end

#
<%= f = form_for @changeset, "#", [phx_change: "update_timezone"] %>
  <%= select f, :timezone, @timezones, promt: "Select timezone" %>
  <%= error_tag f, :timezone %>
  <%= text_input f, :time, readonly: true %>
</form> 
```
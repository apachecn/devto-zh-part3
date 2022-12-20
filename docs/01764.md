# 凤凰网现场直播直斥

> 原文：<https://dev.to/tizpuppi/phoenix-live-view-debounce-4icf>

Phoenix Live View 仍处于测试阶段，但很多人都在用它制作有趣的东西。在[simplifier](https://simplificator.com)我们也在尝试将它用于外部客户和内部项目。

# 搜索表单

phoenix live view 的一个很好的用例是构建一个动态搜索表单，在输入时执行一些搜索。

[![](img/e609efd50d1996f208f1d4dc750e49bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PYVoN_Us--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5dtcg8l4bb96ht57lo3o.png)

正如 Chris McChord 在他的[例子](https://github.com/chrismccord/phoenix_live_view_example/blob/master/lib/demo_web/live/search_live.ex#L6)中所展示的，这很容易通过一个实时视图来实现:

```
def mount(_session, socket) do
  {:ok, assign(socket, query: nil, results: nil, loading: false)}
end

def handle_event("search", %{"query" => query}, socket) do
  send(self(), {:search, query})
  socket = socket
  |> assign(:query, query)
  |> assign(:loading, true)

  {:noreply, socket}
end

def handle_info({:search, query}, socket) do
  socket = socket
  |> assign(:loading, false)
  |> assign(results: search_results(query))
  {:noreply, socket}
end 
```

这个想法是通过向同一个进程发送消息来对每一次击键做出反应。该消息的形式为`{:search, query}`，并且异步执行必要的查询(例如在数据库上)。

这是一种实用的方法，但通常我们希望避免在输入时执行太多不必要的查询。在两次连续查询之间设置最小时间的可能性被称为去抖，这在大多数 javascript 框架中是一个非常常见的特性。

去抖功能目前在 phoenix live view 中不可用，但将在不久的将来实现，正如你从这个[讨论](https://elixirforum.com/t/phoenix-liveview-is-now-live/20889/13)中看到的。幸运的是，这个特性很容易实现。

## 去抖

与前面的代码片段最大的不同是，不是向当前流程发送消息，而是在某个“去抖时间”后发送执行查询的消息，并将查询词存储在流程的状态中(phoenix live 视图中的套接字)。

这是通过使用返回定时器参考的`Process.send_after/3`函数实现的。然后，计时器参考与一个`loading`标志和搜索词一起存储在实时视图的状态(套接字)中。

如果新的查询项在“去抖动时间”内到达，新的查询项被存储在状态中。一旦到达“去抖时间”，就会触发`:search`消息，并执行查询。

在这里你可以看到完整的代码片段:

```
# new query term arrives within the debounce time
def handle_event("search", %{"query" => q}, %{assigns: %{loading: true}} = socket) do
  socket = socket
  |> assign(:query, q)

  {:noreply, socket}
end

def handle_event("search", %{"query" => q}, %{assigns: %{loading: false}} = socket) do
  # debounce time of 300 ms
  timer_ref = Process.send_after(self(), :search, 300)
  socket = socket
  |> assign(:query, q)
  |> assign(:timer_ref, timer_ref)
  |> assign(:loading, true)

  {:noreply, socket}
end

def handle_event("search-final", %{"query" => q}, socket)  do
  Process.cancel_timer(socket.assigns.timer_ref)
  socket = socket
  |> assign(:query, q)

  send(self(), :search)
  {:noreply, socket}
end

def handle_info(:search, socket) do
  socket = socket
  |> assign(:loading, false)
  |> assign(:results, search_results(socket.assigns.query))

  {:noreply, socket}
end 
```

如你所见，当用户点击`search`按钮并“提交”一个搜索时，定时器引用被用于取消未来的搜索请求；取消任何将来的请求(如果有)并立即执行搜索。
# 使用 Phoenix 实时视图的多步表单

> 原文：<https://dev.to/tuacker/multi-step-form-using-phoenix-live-view-2i85>

在构建 [Newsletty](https://www.newsletty.com) 时，我想到的一件事是针对特定流程的多步骤表单。我认为即将到来的[实时景观](https://github.com/phoenixframework/phoenix_live_view/)将是一个完美的适合。你在[中看到的这个片段](https://www.markusbodner.com/videos/form_live_view_steps_test.mp4)是我自己不用写任何 JavaScript 就能实现的。

## 实现

我将在这里讨论视图模板以及管理它的实际代码。如果你想看到从一个新的 Phoenix 项目到最终结果的一步一步，你可以从最早的到最新的浏览我的[提交](https://github.com/tuacker/phoenix_live_view_form_steps/commits/master)。

首先是模板。这是一种非常普通的形式，就像你在凤凰城做的一样。唯一的例外是它有一个`.leex`扩展名。而且还有一些怪异的`if @current_step....`在里面。

```
# lib/live_view_form_web/templates/example/form.html.leex
<%= form_for @changeset, "#", [phx_change: :validate, phx_submit: :save], fn f -> %>
  <%= if @current_step == 1 do %>
    <div>
      <%= label f, :title %>
      <%= text_input f, :title, autofocus: true %>
      <%= error_tag f, :title %>
    div>

    <div>
      <%= label f, :description %>
      <%= text_input f, :description %>
      <%= error_tag f, :description %>
    div>

  <% else %>
    <%= hidden_input f, :title %>
    <%= hidden_input f, :description %>
  <% end %>

  <%= if @current_step == 2 do %>
    <div>
      <%= label f, :type %>

      <%= label do %>
        <%= radio_button f, :type, "thing_a" %>
        Thing A
      <% end %>

      <%= label do %>
        <%= radio_button f, :type, "thing_b" %>
        Thing B
      <% end %>

      <%= error_tag f, :type %>
   div>
  <% else %>
    <%= hidden_input f, :type %>
  <% end %>

  <%= if @current_step == 3 do %>
    <div>
      <%= label f, :something_else %>
      <%= text_input f, :something_else, autofocus: true %>
      <%= error_tag f, :something_else %>
    div>
  <% end %>

  <%= if @current_step > 1 do %><button phx-click="prev-step">Backbutton><% end %>

  <%= if @current_step == 3 do %>
    <%= submit "Submit" %>
  <% else %>
    <button phx-click="next-step">Continuebutton>
  <% end %>

<% end %> 
```

注意第二行的`[phx_change: :validate, phx_submit: :save]`和`phx-click="next-step"`以及继续&后退按钮上的`phx-click="prev-step"`。

现在有了实时视图代码，它变得令人兴奋。我会把它分开，一个功能一个功能地检查。

```
# lib/live_view_form_web/live/example_live.ex
defmodule LiveViewFormWeb.ExampleLive do
  use Phoenix.LiveView
  alias LiveViewFormWeb.Router.Helpers, as: Routes

  def render(assigns) do
    LiveViewFormWeb.ExampleView.render("form.html", assigns)
  end

  def mount(_session, socket) do
    socket =
      socket
      |> assign(:current_step, 1)
      |> assign(:changeset, LiveViewForm.change_example(%{}))

    {:ok, socket}
  end

  # ... 
```

这里我们定义了实时视图模块。在`render/1`中，我们所做的就是将当前赋值传递给我们刚刚在上面看到的视图模板。当用户第一次访问带有实时视图的页面时，调用`mount/2`。在这里，我们设置流程的初始分配。在我们的例子中，将`current_step`设置为 1，并为我们的例子创建一个空的`changeset`。

令人兴奋的是，您正在与一个长期存在的过程一起工作。当用户停留在页面上时，该进程在您的服务器上是活动的。它的所有状态就像上面的两个赋值一样。然后，实时视图使用指定来确定是否需要进行新的渲染。每当其中一个赋值发生变化时，`render/1`就会被再次调用。然后，它计算出 HTML 的哪一部分需要修改，并将其发送给客户端。不是完整的 HTML。只是什么变了。然后，客户端使用该补丁相应地更新 DOM。

```
def handle_event("validate", %{"example" => params}, socket) do
  changeset = LiveViewForm.change_example(params) |> Map.put(:action, :insert)

  {:noreply, assign(socket, :changeset, changeset)}
end

def handle_event("save", %{"example" => params}, socket) do
  # Pretending to insert stuff if changeset is valid
  changeset = LiveViewForm.change_example(params)

  case changeset.valid? do
    true ->
      {:stop,
       socket
       |> put_flash(:info, "Example inserted => #{inspect(changeset.changes)}")
       |> redirect(to: Routes.live_path(LiveViewFormWeb.Endpoint, LiveViewFormWeb.ExampleLive))}

    false ->
      {:noreply, assign(socket, :changeset, %{changeset | action: :insert})}
  end
end 
```

在`form_for/4`中我们设置了`[phx_change: :validate, phx_submit: :save]`。`:validate`和`:save`映射到上面的 2 个`handle_event/3`。在`"validate"`中，当前表单数据被传递给变更集，然后分配给套接字。如果表单有验证错误，模板将被重新呈现并推送到客户端。

与`save`中的类似，我们检查`changeset`是否有效。通常你会在这里插入数据到你的数据库，但是我忘记了。相反，我只是假装通过检查变更集是否有效。如果它是无效的，和在`"validate"`发生的事情一样。当有效时，在连接上闪烁，然后重定向。

```
def handle_event("prev-step", _value, socket) do
  new_step = max(socket.assigns.current_step - 1, 1)
  {:noreply, assign(socket, :current_step, new_step)}
end

def handle_event("next-step", _value, socket) do
  current_step = socket.assigns.current_step
  changeset = socket.assigns.changeset

  step_invalid =
    case current_step do
      1 -> Enum.any?(Keyword.keys(changeset.errors), fn k -> k in [:title, :description] end)
      2 -> Enum.any?(Keyword.keys(changeset.errors), fn k -> k in [:type] end)
      _ -> true
    end

  new_step = if step_invalid, do: current_step, else: current_step + 1

  {:noreply, assign(socket, :current_step, new_step)}
end 
```

后退/继续按钮上的`phx-click`映射到这些`handle_event/3`。在`prev-step`中，我们降低当前的步长级别。这允许用户返回。将更新的`current_step`分配给套接字后，实时视图将重新渲染并将更改推送到客户端。由于`changeset`由实时视图进程保存在内存中，我们不会丢失任何数据。

在`next-step`上，我们从分配中获取`changeset`,并检查当前步骤的输入字段是否有效。我不确定这是不是最好的方法，但是对于这样一个简单的例子来说，它已经足够好了。如果这一步有效，我们将`current_step`加 1，更新套接字，它将在下一步重新渲染。

如果你在开始时回头看看模板，你会看到 if-s 使用`@current_value`来确定哪些输入是可见的，哪些是隐藏的。

就是这样！只有 60 行现场代码(+ html 模板代码)，我有一个多步骤的形式。不用写一行 JavaScript 代码。所有的验证都发生在服务器端！我真的很喜欢这种简单。

## 告诫

尽管存在一些问题。有些是因为现场直播还是新的，有些是我自己做的。

*   如果仔细观察，您会发现在最后一个步骤中，在用户进行任何输入之前，输入验证就已经可见了。对此的一个解决方案是，每当当前步骤增加时，将变更集`action`重置为`nil`。
*   据我所知，每次按键后都会运行验证步骤。随着高延迟，这可能会破坏 UX，从而感觉真的 janky。
*   如果其中一个值必须是唯一的，则只能通过尝试插入来确定。然后你必须找到正确的步骤来展示；存在错误。或者在尝试插入失败后立即呈现整个表单。

这个例子的完整代码可以在 [GitHub](https://github.com/tuacker/phoenix_live_view_form_steps/) 上找到。
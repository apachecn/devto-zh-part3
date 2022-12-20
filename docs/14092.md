# 如何为凤凰城的用户存储“最后看到的”

> 原文：<https://dev.to/ukutaht/how-to-store-last-seen-for-users-in-phoenix-41jd>

本周，我对[似是而非的](https://plausible.io/)做了一些改进，让我更好地了解我的用户群。其中之一是为所有用户存储一个`last_seen`时间戳。这是一份私人数据，我用它来确定:

*   有多少用户主动登录并检查他们的分析
*   似是而非的平均使用频率是多少？
*   什么时候我应该将一个账户视为“腐烂”？这意味着作为用户/顾客，我将失去他们

让我们看看这在凤凰城是如何实现的。首先，我们将从模式开始。

### 图式

我们需要为`last_seen`列生成一个迁移:

```
$ mix ecto.gen.migration add_last_seen_to_users 
```

还有迁移本身:

```
defmodule Plausible.Repo.Migrations.AddLastSeenToUsers do
  use Ecto.Migration

  def change do
    alter table(:users) do
      add :last_seen, :naive_datetime, default: fragment("now()")
    end
  end
end 
```

在这一点上，您还需要将该字段添加到您的`User` Ecto 模式中。

### 塞

现在我想在每次使用网站时更新这个字段。这里一个自然的解决方案是添加一个拦截每个请求的插件。然而，在每次请求时更新数据库似乎相当浪费。此外，出于我的目的，时间戳不需要精确到毫秒。这是一个完美的节流案例，可以通过使用浏览器会话来实现。

```
defmodule PlausibleWeb.LastSeenPlug do
  import Plug.Conn
  use Plausible.Repo

  @one_hour 60 * 60

  def init(opts) do
    opts
  end

  def call(conn, _opts) do
    last_seen = get_session(conn, :last_seen)
    user = conn.assigns[:current_user]

    cond do
      user && last_seen && last_seen < (unix_now() - @one_hour) ->
        persist_last_seen(user)
        put_session(conn, :last_seen, unix_now())
      user && !last_seen ->
        put_session(conn, :last_seen, unix_now())
      true ->
        conn
    end
  end

  defp persist_last_seen(user) do
    q = from(u in Plausible.Auth.User, where: u.id == ^user.id)

    Repo.update_all(q, [set: [last_seen: DateTime.utc_now()]])
  end

  defp unix_now do
    DateTime.utc_now() |> DateTime.to_unix
  end
end 
```

我将这个插头添加到我路由器的`browser`管道中。请注意，在这个插件之前，我运行了另一个插件，它查找当前用户并使`conn.assigns[:current_user]`可用。

这种方法满足了我对这个特性的所有要求:

*   保持`last_seen`的小时精度
*   每个用户最多每小时运行一次数据库更新
*   我特别使用了`Repo.update_all`，所以`users`上的`updated_at`字段没有被改动。
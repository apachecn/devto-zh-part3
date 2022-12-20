# elixir :用于自动化和水平分割 Yacto 移植的库 Yacto

> 原文：<https://dev.to/gumi/elixir-ectoyacto-32gl>

[Ecto](https://hexdocs.pm/ecto/Ecto.html) 是处理数据库非常方便的库。 [Yacto](https://hex.pm/packages/yacto) 作为使其使用更加方便的库而被创建。 本文根据 Qiita 上公开的文档“[[Yacto] Ecto 的迁移自动化和水平分割所需的库](https://qiita.com/melpon/items/5c9b0645d5240cd22d0f)”进行介绍。

另外，文件会适当更新，所以补充一下这是本文执笔时的信息。 有关详细信息，请参阅文档。

# Yacto とは

Yacto 是一个支持 Ecto 不足部分的库。 主要有以下四个功能。

*   自动生成迁移文件
*   使用从其他 APP 应用程序移植
*   移植到水平拆分的数据库
*   跨多个数据库的事务( XA 事务)

# 迁移文件的自动生成

Yacto 与 Ecto 不同，尤其是在移植周围。 使用 Yacto，可以从模式中自动输出移植文件，而无需像 Ecto 那样专门定义移植。

#### Ecto 和 Yacto 的迁移差异

| 程序库 | 迁移 |
| --- | --- |
| 埃克托 | 单独定义模式和迁移 |
| 亚克托 | 从架构中自动输出迁移文件 |

例如，假设对`lib/my_app/player.ex`定义了以下架构。

```
defmodule MyApp.Player do
  use Yacto.Schema, dbname: :player

  schema @auto_source do
    # sharding key
    field :player_id, :string, meta: [null: false, size: 64]
    field :hp, :integer, default: 0, meta: [null: false]
    index :player_id, unique: true
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在此通过 Yacto 执行`mix yacto.gen.migration`。 然后，将输出以下迁移文件`priv/migrations/2017-11-22T045225_my_app.exs`。

```
defmodule MyApp.Migration20171122045225 do
  use Ecto.Migration

  def change(MyApp.Player) do
    create table("my_app_player")
    alter table("my_app_player") do
      add(:hp, :integer, [null: false, size: 64])
      add(:player_id, :string, [null: false])
    end
    create index("my_app_player", [:player_id], [name: "player_id_index", unique: true])
  end

  def change(_other) do
    :ok
  end

  def __migration_structures__() do
    [
      {MyApp.Player, %Yacto.Migration.Structure{fields: [:id, :player_id, :hp], meta: %{attrs: %{hp: %{null: false}, player_id: %{null: false, size: 64}}, indices: %{{[:player_id], [unique: true]} => true}}, source: "my_app_player", types: %{hp: :integer, id: :id, player_id: :string}}},
    ]
  end

  def __migration_version__() do
    20171122045225
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然后执行`mix yacto.migrate`，该迁移文件将反映在数据库中。 不需要再一个一个地写迁移文件了。

此外，如果在模式中添加了字段，则还可以仅将差异输出到移植文件，以反映在数据库中。

# 从其他 APP 应用的迁移的利用

假设另一个 APP 应用程序使用了刚才例子的 APP 应用程序`my_app`。 于是，`my_app`使用了数据库，所以在其 APP 应用上需要进行`my_app`的迁移。

在 Ecto 中，您必须自己编写其他 APP 应用程序需要的迁移，或者使用每个 APP 应用程序指定的不同方法进行迁移。

但是，使用 Yacto 的话，只要恰当地写上`config/config.exs`，在使用`my_app`的 APP 应用中执行以下命令即可。 这样就可以进行`my_app`的迁移了。 这意味着使用 Yacto 的所有 APP 应用程序都可以使用相同的方法进行移植。

```
mix yacto.migrate --app my_app 
```

Enter fullscreen mode Exit fullscreen mode

# 向水平分割的数据库迁移

例如，如果水平拆分`MyApp.Player`模式，则必须将该模式的迁移文件应用于多个 Repo。 这只需在设定文件`config/config.exs`中写如下即可。

```
config :yacto, :databases,
  %{
    default: %{
      module: Yacto.DB.Single,
      repo: MyApp.Repo.Default,
    },
    player: %{
      module: Yacto.DB.Shard,
      repos: [MyApp.Repo.Player0, MyApp.Repo.Player1],
    },
  } 
```

Enter fullscreen mode Exit fullscreen mode

使用`MyApp.Player`的`Yacto.Schema`的`use/2`中，规定了`dbname: :player`的选项，如下所示。 此`:player`为`MyApp.Player`所属的 Repo 的组名。

```
defmodule MyApp.Player do
  use Yacto.Schema, dbname: :player

  ... 
```

Enter fullscreen mode Exit fullscreen mode

`:player`Repo 组在设定文件中将`MyApp.Repo.Player0`和`MyApp.Repo.Player1`的 Repo 关联起来。 然后执行`mix yacto.migrate`，`MyApp.Player`的迁移文件适用于`MyApp.Repo.Player0`和`MyApp.Repo.Player1`。

使用水平分割的数据库时，可以使用`Yacto.DB.repo/2`(或`schema.repo/1`)由 Repo 取得。

```
repo = Yacto.DB.repo(:player, player_id)
MyApp.Player |> repo.all() 
```

Enter fullscreen mode Exit fullscreen mode

# 跨多个数据库的事务( XA 事务)

使用`Yacto.transaction/2`，可以指定多个数据库并发出事务。

```
# ２つ以上のRepoが指定されているので XA トランザクションを発行する
Yacto.transaction([:default,
                   {:player, player_id1},
                   {:player, player_id2}], fn ->
  default_repo = Yacto.DB.repo(:default)
  player1_repo = Yacto.DB.repo(:player, player_id1)
  player2_repo = Yacto.DB.repo(:player, player_id2)

  # このあたりでデータベースを操作する
  ...

# ここですべてのXAトランザクションがコミットされる
end) 
```

Enter fullscreen mode Exit fullscreen mode

将对以下三个 Repo 执行事务处理:

*   `:default`的`RepoMyApp.Repo.Default`
*   用`player_id1`进行了明暗处理的 Repo
*   用`player_id2`进行了明暗处理的 Repo

之后的两个根据阴影键的不同可能会变成相同的 Repo，所以使用的 Repo 是两个或三个中的一个。 如果使用两个或多个 Repo 启动事务，则会自动生成 XA 事务。

# 其他

我扼要地说明了 Yacto 的主要功能。 详情请参阅前文“[[Yacto] Ecto 移植的自动化和水平分割的库的](https://qiita.com/melpon/items/5c9b0645d5240cd22d0f)”。 还介绍了 Yacto 模式的确定方法和使用方法以及便利函数。

*   [Yacto 的模式](https://qiita.com/melpon/items/5c9b0645d5240cd22d0f#yacto-%E3%81%AE%E3%82%B9%E3%82%AD%E3%83%BC%E3%83%9E)
*   [convenience number](https://qiita.com/melpon/items/5c9b0645d5240cd22d0f#%E4%BE%BF%E5%88%A9%E9%96%A2%E6%95%B0)
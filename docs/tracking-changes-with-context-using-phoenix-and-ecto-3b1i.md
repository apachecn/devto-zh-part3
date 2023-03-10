# 使用 Phoenix 和 Ecto 跟踪上下文的变化

> 原文：<https://dev.to/luizdamim/tracking-changes-with-context-using-phoenix-and-ecto-3b1i>

在[之前的帖子](https://luizdamim.com/blog/tracking-changes-with-context/)中，我们讨论了为什么为应用程序的数据更改添加上下文很重要。现在我们来看看如何在凤凰和埃克托的《长生不老药》中做到这一点。

## 给上下文添加上下文

[![What does that even mean?](img/09b837fe82eb9d1c5ea8a79892f92a3d.png "What does that even mean?")](https://res.cloudinary.com/practicaldev/image/fetch/s--a8LK11w---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://luizdamim.com/static/02de9ce52b36f4dcf25742d1bfd775f8/48a11/what-does-that-even-mean.jpg)

上下文是一个超负荷的术语，特别是在编程中。让我们看看我们将使用的两个定义:

**上下文:**

> 1)某事存在或发生的情况，这有助于解释它； *2)与特定事件或情况相关的影响和事件*；
> 
> ([剑桥词典](https://dictionary.cambridge.org/dictionary/english/context))

**(凤凰)语境:**

> *上下文是展示和分组相关功能的专用模块。例如，无论何时调用 Elixir 的标准库，无论是`Logger.info/1`还是`Stream.map/2`，都是在访问不同的上下文。在内部，Elixir 的记录器由多个模块组成，但我们从不直接与这些模块交互。我们称 Logger 模块为上下文，正是因为它公开并分组了所有的日志功能。*
> 
> ([凤凰文档](https://hexdocs.pm/phoenix/contexts.html))

它们意味着不同的事情，但在组织和赋予可能发生的事情和为什么会发生的意义方面合作得非常好。从现在起，我将把*上下文*与*解释发生的情况或事情的含义*称为**事件**。

## 取消订单

按照我们前面的网上商店的例子，我们有两个 Phoenix 上下文:`Accounts`和`Sales`。在`Accounts`中，我们有一个`User`模式，在`Sales`、`Client`和`Order`模式中。

要取消订单，我们可以有一个简单的`Sales.cancel_order/1`函数:

```
def cancel_order(%Order{} = order) do
  order
  |> Order.changeset({status: "cancelled"})
  |> Repo.update()
end 
```

## 订单取消。但是为什么呢？

要了解订单被取消的原因，让我们重构函数，添加执行操作的`reason`和`user`。

```
def cancel_order(%Order{} = order, reason, %User{} = actor) do
  Multi.new()
  |> Multi.update(:order, Order.changeset(order, %{status: "cancelled"}))
  |> Multi.run(:audit, &cancel_order_audit(&1, &2, reason, actor))
  |> Repo.transaction()
end

defp cancel_order_audit(_repo, %{order: order}, reason, actor) do
  metadata = %{
    source: "manual",
    reason: reason
  }

  "order_cancelled"
  |> OrderAudit.event(order.id, actor.id, metadata)
  |> OrderAudit.save()
end 
```

我们还使用`Ecto.Multi`使操作原子化(这意味着只有当订单被取消而没有错误时，事件日志才会被保存)，并添加了一个`cancel_order_audit/4`回调函数来设置元数据并保存更改。

`OrderAudit`模块是一个标准的 [Ecto 模式](https://hexdocs.pm/ecto/Ecto.Schema.html)，有一个变化:

```
defmodule AlchemyReaction.Sales.OrderAudit do
  # highlight-range{1-6}
  use AlchemyReaction.Audit,
    repo: AlchemyReaction.Repo,
    schema: __MODULE__,
    events: [
      "order_cancelled"
    ]

  use Ecto.Schema
  import Ecto.Changeset

  alias AlchemyReaction.Accounts.User
  alias AlchemyReaction.Sales.Order

  schema "orders_audit" do
    field :event, :string
    field :metadata, :map
    belongs_to :row, Order
    belongs_to :actor, User

    timestamps(updated_at: false)
  end

  @doc false
  def changeset(%__MODULE__{} = order_audit, attrs) do
    order_audit
    |> cast(attrs, [:event, :row_id, :metadata, :actor_id])
    |> validate_required([:event, :row_id, :actor_id])
  end
end 
```

我们正在使用`Audit`模块并设置一些选项，包括`events`列表。

只有一个事件，`order_cancelled`，但是我们可以有更多:`item_removed`、`payment_type_changed`、`shipping_address_changed`等等。

我通常在一个上下文中有多个审计模块，每个*子上下文*一个。

**子语境？**

[![WE NEED TO GO DEEPER](img/39cb86e8cdac28feaa936283823a8258.png "WE NEED TO GO DEEPER")](https://res.cloudinary.com/practicaldev/image/fetch/s--kzCm7rPc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://luizdamim.com/static/869f770d3fd83f645f3ecede043b4f7f/41689/we-need-to-go-deeper.jpg)

对于熟悉 [DDD](https://en.wikipedia.org/wiki/Domain-driven_design) 的人来说，他们就像[的聚合根](https://martinfowler.com/bliki/DDD_Aggregate.html)。

`Order`模式由其他更小的模式组成，这些模式在它之外是没有意义的，比如订单项目和付款。所以`Order` *聚合*这个附加数据，而作为最重要的一个，就是*根*，因此*聚合根*。但是我喜欢称它们为*子上下文*，因为这个术语更接近于 Phoenix 上下文。:)

你能为`Sales`上下文中的子上下文找到另一个好的候选者吗？

**重要提示:**子上下文是一个内部概念，只能与其父上下文交互。在这个例子中，所有来自外部的调用仍然会发生在`Sales`上下文中。

警告:一个 Ecto 模式应该是一个数据源和一个 Elixir 结构之间的简单映射，但是我找不到一个更好的地方来放这个例子。在我的实际应用中，我组织我的上下文有点不同，所以这不会发生。以后我会写的。

## `Audit`模块

那是什么呢？它是如何工作的？

[![Teach me the ways of the force](img/4887e68c3f584cc5f941c021766e70ad.png "Teach me the ways of the force")](https://res.cloudinary.com/practicaldev/image/fetch/s--6VcrB_Np--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://luizdamim.com/static/a4c3ad8370fab1334ffff2c80af3d927/df2c7/teach-me-the-ways-of-the-force.jpg)

下面是完整的代码:

```
defmodule AlchemyReaction.Audit do
  require Logger

  defmacro __using__(opts) do
    repo = Keyword.fetch!(opts, :repo)
    schema = Keyword.fetch!(opts, :schema)
    events = Keyword.fetch!(opts, :events)

    if Enum.empty?(events), do: raise(ArgumentError, message: "event list empty")

    save_function =
      quote do
        # Output:
        #
        # def save(%Ecto.Changeset{} = changes) do
        #   AlchemyReaction.Audit.save(changes, repo)
        # end
        def save(%Ecto.Changeset{} = changes) do
          unquote(__MODULE__).save(changes, unquote(repo))
        end
      end

    event_signature =
      quote do
        def event(event, row_id, actor_id, metadata \\ nil)
      end

    event_log_functions =
      for event_name <- events do
        quote do
          # Output:
          #
          # def event("foo_event", row_id, actor_id, metadata) do
          #   AlchemyReaction.Audit.event(schema, "foo_event", row_id, actor_id, metadata)
          # end
          def event(unquote(event_name), row_id, actor_id, metadata) do
            unquote(__MODULE__).event(
              unquote(schema),
              unquote(event_name),
              row_id,
              actor_id,
              metadata
            )
          end
        end
      end

    [save_function, event_signature, event_log_functions]
  end

  @doc """
  Saves the event to the `Repo`.

  In case nothing changed, simply does nothing.

  It returns `{:ok, :no_changes}` if nothing changed, `{:ok, struct}` if the log
  has ben successfully saved or `{:error, changeset}` in case of error.
  """
  @spec save(Ecto.Changeset.t() | :no_changes, Ecto.Repo.t()) ::
          {:ok, :no_changes}
          | {:ok, Ecto.Schema.t()}
          | {:error, Ecto.Changeset.t()}

  def save(:no_changes, _repo) do
    {:ok, :no_changes}
  end

  def save(%Ecto.Changeset{} = changes, repo) do
    Logger.debug("Saving audit info...")
    repo.insert(changes)
  end

  @doc """
  Creates a `schema` changeset for the `event` identified by `row_id` and caused
  by `actor_id`.

  The given `metadata` can be either `nil`, `Ecto.Changeset`, struct or map.

  It returns `:no_changes` in case of an `Ecto.Changeset` metadata that changed nothing
  or an `Ecto.Changeset` with the event ready to be inserted.
  """
  @spec event(Ecto.Schema.t(), String.t(), non_neg_integer(), non_neg_integer(), Ecto.Changeset.t() | struct() | map() | nil) ::
          :no_changes | Ecto.Changeset.t()

  def event(schema, event, row_id, actor_id, metadata \\ nil)

  def event(_, _, _, _, %Ecto.Changeset{changes: changes} = _changeset)
      when map_size(changes) == 0 do
    :no_changes
  end

  def event(schema, event, row_id, actor_id, %Ecto.Changeset{} = changeset) do
    metadata = %{
      before: Map.take(changeset.data, Map.keys(changeset.changes)),
      after: changeset.changes
    }

    audit_changeset(schema, event, row_id, actor_id, metadata)
  end

  def event(schema, event, row_id, actor_id, %_{} = struct) do
    metadata =
      struct
      |> Map.from_struct()
      |> Map.drop([:__meta__])

    audit_changeset(schema, event, row_id, actor_id, metadata)
  end

  def event(schema, event, row_id, actor_id, metadata) when is_map(metadata) do
    audit_changeset(schema, event, row_id, actor_id, metadata)
  end

  defp audit_changeset(schema, event, row_id, actor_id, metadata) do
    schema
    |> struct()
    |> schema.changeset(%{
      event: event,
      row_id: row_id,
      actor_id: actor_id,
      metadata: metadata
    })
  end
end 
```

使用这个模块为每个列出的事件定义一对`event/3`和`event/4`函数，由于模式匹配，帮助您避免键入错误的事件名称。它们之间的区别在于`metadata`参数。大多数事件需要额外的元数据，但有些不需要，事件名称本身就足以描述发生了什么。

还定义了一个`save/1`函数。保存事件日志。真的。除非没什么可保存的，因为什么都没变。但是节省了大部分时间...

`event/3`和`event/4`函数将调用委托给`Audit`模块内部的`event/5`,后者创建日志变更集。再次感谢模式匹配，`metadata`可以从不同的来源构建:

*   一个`Ecto.Changeset`:在一个`%{before: %{}, after: %{}}`图中提取变化及其原始值；或者它返回一个`:no_changes`原子，以防什么都没有改变。后者绕过保存，因此不会创建无用的日志条目
*   a `struct`:来自该结构的所有字段都被添加到元数据中
*   a `map`:你需要的任何东西来描述事件的背景。注意只放必要的东西，加得太少或太多都会妨碍你看清发生了什么

起初，手动创建回调函数和构建事件似乎工作量太大，但这是值得的。事件日志为您提供了多种好处:您可以在时间线中显示更改，出于责任原因存储一段时间内的更改，出于安全原因对更改进行跟踪，等等。

我正在我的一个应用程序中使用它，它非常棒。
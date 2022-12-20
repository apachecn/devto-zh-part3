# 苦艾酒

> 原文：<https://dev.to/revent/graphql-with-absinthe-59kb>

已经有很多 graphql 教程了。这更像是固化了我对题目的理解。如果你想学它，大概有[更好的](https://hexdocs.pm/absinthe/overview.html) [资源](https://caskstrength.wordpress.com/2009/07/29/absinthe-what-it-is-how-to-serve-it-the-basics/)到[跟着](https://dev.to/timber/a-gentle-introduction-to-graphql-with-elixir-and-phoenix-736)。

## 骗到 1:一切都是场。

从字面上看，一切都是“场”。我什么意思？

> 每个字段可以是[预定义标量值](https://hexdocs.pm/absinthe/Absinthe.Type.Scalar.html) ( `string` `boolean`、`float`、`id`或`integer`)或[自定义标量值](https://hexdocs.pm/absinthe/custom-scalars.html)

```
defmodule MyApp.Schema do
  use Absinthe.Schema

  # All fields in this scope can be publicly accessible
  query do
    # Defining field `name` with type string
    field :name, :string
  end
end 
```

现在我们可以执行查询:

```
query  {  name  } 
```

并期待以下输出:

```
{  "data":  {  "name":  null  }  } 
```

这没什么，因为我们还没有告诉 absynthe 如何解决这个场。现在让我们告诉它返回硬编码的值。

```
defmodule MyApp.Schema do
  use Absinthe.Schema

  # All fields in the query scope can be publicly accessible
  query do
    # Defining field `name` with type string and resolver for it
    field :name, :string, resolve: fn _, _, _ -> {:ok, "John Doe"} end
  end
end 
```

现在执行同一个查询，我们得到`John Doe`作为 name 字段的值，而不是 null

```
{  "data":  {  "name":  "John Doe"  }  } 
```

如果类型以字符串形式提供，解析程序回调无法返回对象。

## 问题 2:字段也可以是复杂的结构(列表、映射)

通常，字段将包含比标量更复杂值。尤其是如果字段在根级别:

```
defmodule MyApp.Schema do
  use Absinthe.Schema

  # All fields in the query scope can be publicly accessible
  query do
    # Defining field `user` with type `user`
    field :user, :user
  end

  # Custom defined object `user` that has one field `name` and resolver for it
  object :user do
    field :name, :string, resolve: fn _, _, _ -> {:ok, "John Doe"} end
  end
end 
```

现在我们可以执行查询:

```
query  {  user  {  name  }  } 
```

并期待以下输出:

```
{  "data":  {  "user":  {  "name":  "John Doe"  }  }  } 
```

但实际上，我们得到了

```
{  "data":  {  "user":  {  "name":  null  }  }  } 
```

嗯...因此...我无法解析深度嵌套的结构。我需要从根本上解决问题。至少看起来是这样。

```
defmodule MyApp.Schema do
  use Absinthe.Schema

  # All fields in the query scope can be publicly accessible 
  query do
    # Defining field `user` with type `user` and resolver for it
    field :user, :user, resolve: fn _, _, _ -> {:ok, %{name: "John Doe"}} end
  end

  # Custom defined object `user` that has one field `name`
  object :user do
    field :name, :string
  end
end 
```

在执行前面的查询时，我们现在确实得到了预期的输出:

```
{  "data":  {  "user":  {  "name":  "John Doe"  }  }  } 
```

如果父级别不提供字段，则不会调用子字段解析器回调。因此，我们可以通过组合两个级别的解析器来获得相同的效果:

```
defmodule MyApp.Schema do
  use Absinthe.Schema

  # All fields in the query scope can be publicly accessible
  query do
    # Defining field `user` with type `user` and resolver for it
    field :user, :user, resolve: fn _, _, _ -> {:ok, %{name: nil}} end
  end

  # Custom defined object `user` that has one field `name` and resolver for it
  object :user do
    field :name, :string, resolve: fn _, _, _ -> {:ok, "John Doe"} end
  end
 end 
```

我们甚至可以组合两个级别的解析器，并期望得到相同的输出:

```
defmodule MyApp.Schema do
  use Absinthe.Schema

  # All fields in the query scope can be publicly accessible
  query do
    # Defining field `user` with type `user` and resolver for it
    field :user, :user, resolve: fn _, _, _ -> {:ok, %{name: "John"}} end
  end

  # Custom defined object `user` that has one field `name` and resolver for it
  object :user do
    field :name, :string, resolve: fn %{name: name}, _, _ -> {:ok, name <> " Doe"} end
  end
end 
```

### 明白 2.1:字段也可以是复杂的结构

我正在研究的所有教程都与 ecto 紧密结合，所以它不知何故忽略了场结构是如何工作的。这里你可以看到更深的嵌套结构。每个`user`都有一个带有两个值`is_admin?`和`role`的`permissions`映射字段。

```
defmodule MyApp.Schema do
  use Absinthe.Schema

  # All fields in the query scope can be publicly accessible
  query do
    # Defining field `user` with type `user` and resolver for it
    field :user, :user, resolve: &resolve_user/3
  end

  # Custom defined object `user` that has two fields `name` and `permissions` and resolver for the latter
  object :user do
    field :name, :string
    field :permissions, :permissions, resolve: &resolve_permisisons/3
  end

  # Custom defined object `permissions` that has two fields
  object :permissions do
    field :is_admin, :boolean
    field :role, :string
  end

  defp resolve_user(_, _, _) do
    # Here as well could happen DB query, or reading data from any imaginable data source possible
    # For now simulating that JSON value is retrieved from a data source in permissions field
    user = %{
      name: "John Doe",
      permissions: Jason.encode!(%{is_admin: true, role: "admin"})
    }

    {:ok, user}
  end

  defp resolve_permisisons(%{permissions: permissions}, _, _) do
    Jason.decode(permissions, keys: :atoms!)
  end
end 
```

不用说，这种等级制度可以深入到必要的程度。关于[深度](https://www.howtographql.com/advanced/4-security/)有一些安全问题。

可以从一个数据源中检索数据，并在解析其子节点时改变其值(在本例中是解码 JSON)，甚至可以在其他数据源中获取额外的数据。尽管如此，还是要小心，1+n 会爬得很快。

## 总之

从 graphql 的角度来看，没有一些特殊的根元素、根对象或类似的东西。一切都是字段，您可以完全控制每个字段代表什么(字符串、整数、一些自定义标量值或对象)以及如何检索和处理特定字段的数据。

与 erlang/elixir 本身一样，像带有邮箱的进程这样简单的概念已经发展成为如此稳定的环境，graphql 也走上了类似的道路，选择实现尽可能少的新概念。当正确的构建块被细化时，在这些块上构建的解决方案不仅更快，而且更稳定。

我有点理解围绕 graphql 炒作。将会看到我在突变、分页和其他看起来更复杂的用例中的旅程将会如何进行，但那是另外一个故事了。

附注:欢迎反馈
# 用宏和中间件编写自己的苦艾酒 DSL

> 原文：<https://dev.to/maartenvanvliet/writing-your-own-absinthe-dsl-with-macros-and-middleware-14ic>

苦艾酒是一个很好的库。然而，在编写解析器时，您可能会发现您在多次编写一些样板文件。因此，本着保持代码干燥的精神，在这篇文章中，我将展示我们如何利用中间件和宏为 Graphql api 编写自己的 DSL。

我假设有一些关于苦艾酒工作原理的基本知识，大部分信息可以在指南的[中找到](https://hexdocs.pm/absinthe/overview.html)

编写 api 的一个常见问题是确保只有登录的用户才能访问查询。为了在苦艾酒中做到这一点，我们需要在 graphql 查询的上下文中设置一个`current_user`，参见[指南](https://hexdocs.pm/absinthe/context-and-authentication.html#basic-usage)中的例子。

为了确保我们的解析器只有在设置了 current_user 时才是可访问的，我们可以在它存在时进行模式匹配。

```
query do
    field :profile_picture, :string do
      resolve fn
        _, %{context: %{current_user: current_user}} ->
        {:ok, current_user.profile_picture_url}
        _, _ ->
        {:error, "Not logged in"}
      end
    end
end 
```

我们匹配是否存在`current_user`,如果不存在，我们返回一个错误。但是，如果您有许多经过验证的字段，这可能会非常重复。通过一些中间件，我们可以解决这个问题。

```
# authenticated.ex
defmodule Example.Authenticated do
  @behaviour Absinthe.Middleware

  def call(resolution, _config) do
    case resolution.context do
      %{current_user: %{}} ->
        resolution

      _ ->
        Absinthe.Resolution.put_result(resolution, {:error, "Not logged in"})
    end
  end
end 
```

中间件实现了`Absinthe.Middleware`行为。这需要一个传递当前解析结构的`call/2`函数。在这个结构中，我们可以获得当前查询的上下文，并找出`current_user`是否存在。现在我们需要为我们的领域调用中间件

```
query do
    field :profile_picture, :string do
      middleware Example.Authenticated
      resolve fn
        _, %{context: %{current_user: current_user}} ->
        {:ok, current_user.profile_picture_url}
      end
    end
end 
```

我们将中间件放在解析器之前，任何未经认证的调用都会遇到错误，当`current_user`出现时，它会被简单地传递给解析器。

这已经是一个非常干净的选择，我们可以让它保持原样。尽管如此，为了展示如何使用宏为你的应用程序创建一个更简洁的 DSL，我将给出一个小例子。

用您的宏定义创建一个新文件:

```
defmodule Example.IsAuthenticated do
  defmacro is_authenticated() do
    quote do
      middleware(Example.Authenticated, %{})
    end
  end
end 
```

我声明这个宏做的很少，它唯一做的事情是调用我们之前看到的认证中间件的`middleware/2`。当宏被调用时，它将被重写到`quote`块中的所有内容中。

我们可以像这样重写解析器

```
import Example.IsAuthenticated 
query do
    field :profile_picture, :string do
        is_authenticated()
        resolve fn _, %{context: %{current_user: current_user}} ->
            {:ok, current_user.profile_picture_url}
        end
    end
end 
```

请注意，我们需要导入到宏才能工作。在我们的字段定义中，宏`is_authenticated`被调用并重写到中间件的调用中。可以想象`middleware/2`是[也是一个宏](https://hexdocs.pm/absinthe/Absinthe.Schema.Notation.html#middleware/2)。

这是一个非常简单的例子，说明如何使用宏来为您生成代码。对于 graphql，有许多关于授权或分页的选项，在这些选项中，宏可以让您的代码更加简洁。

转载自([https://maartenvanvliet.nl/2018/12/29/absinthe_macro_dsl/](https://maartenvanvliet.nl/2018/12/29/absinthe_macro_dsl/))

吉尔在 Unsplash 上拍摄的照片
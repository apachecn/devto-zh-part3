# Graphql 之旅。第 3 部分:分页

> 原文：<https://dev.to/revent/graphql-journey-part-3-pagination-3a8n>

每个系统都必须处理这样的情况:有**个零**数据( [404](https://lv.wikipedia.org/wiki/HTTP_404) )，有**个**实例(查看这个特定的帖子)，有**个**实例(分配给帖子的标签)和**个**实例(开发到帖子)。在这种情况下，人们可以有把握地认为在一个地方展示太多的东西。这篇文章是关于后一种也是最实用的处理无限大小数据集的方法:分页。但是让我们从一个更简单的用户案例开始。

###### 在我的机器上工作

*   **酏剂** : 1.8.1
*   **凤凰** : 1.4.2
*   苦艾酒 : 1.4.16

## 归还一切

```
defmodule MyAppWeb.Schema do
  use Absinthe.Schema

  @doc """
  Defining query posts which returns a list of posts from the repository
  """
  query do
    field :posts, list_of(:post) do
      resolve(&MyApp.PostsResolver.posts/3)
    end
  end

  @doc """
  Post structure
  """
  object :post do
    field :id, :integer
    field :title, :string
  end
end

defmodule MyApp.PostsRepository do
  @moduledoc """
  Represents the existing code boundary.
  Can be seen as internal code, that won't even know there is a graphql consumer.
  No graphql related code here
  """

  defmodule Post do
    @moduledoc """
    Simulates ecto schema
    """
    defstruct [:id, :title]
  end

  @doc """
  Simulates values coming from db (for example)
  """
  defp all_posts() do
    [
      %Post{id: 1, title: "1"},
      %Post{id: 2, title: "2"},
      %Post{id: 3, title: "3"},
      %Post{id: 4, title: "4"}
    ]
  end

  @doc """
  Returns all posts
  """
  def posts() do
    all_posts()
  end
end

defmodule MyApp.PostsResolver do
  @moduledoc """
  Resolves graphql query.
  Handles graphql call and maps it to necessary internal code.
  """

  @doc """
  Handles posts request and map it to internal posts repository to fetch results.
  """
  def posts(_, _, _) do
    {:ok, MyApp.PostsRepository.posts()}
  end
end 
```

在这个[开发到系列](https://dev.to/designpuddle/devto-has-implemented-series-series-are-cool-coding-concepts-is-now-a-series-cool-5g9f)的前几篇文章的基础上构建似乎是合理的。现在我们可以执行下面的 graphql 查询，并期待从`MyApp.PostsRepository` :
返回的结果

```
query {
  posts {
    id
    title
  }
} 
```

让我们继续对结果进行分页并访问更多的条目。

## 分页

```
defmodule MyAppWeb.Schema do
  ...

  query do
    field :posts, list_of(:post) do
      # Added required argument to provide page to fetch.
      arg(:page, non_null(:integer))
      resolve(&MyApp.PostsResolver.posts/3)
    end
  end
end

defmodule MyApp.PostsRepository do
  ...

  @doc """
  Returns posts for selected page
  """
  def posts(page, per_page) do
    offset = (page - 1) * per_page

    all_posts()
    |> Enum.drop(offset)
    |> Enum.take(per_page)
  end
end

defmodule MyApp.PostsResolver do
  @doc """
  How many posts show in a single page
  """
  @per_page 2

  @doc """
  Accept selected page and pass it to repository
  """
  def posts(_, %{page: page}, _) do
    {:ok, MyApp.PostsRepository.posts(page, @per_page)}
  end
end 
```

上面你可以看到支持分页的代码变化。它显示了这些变化实际上是多么小和微不足道。现在，我们可以为下面的 graphql 查询提供页面参数，并获得预期的输出。

```
query {
  posts(page: 3) {
    id
    title
  }
} 
```

大多数分页端点都有一些元信息。例如总共有多少页。让我们也实现它。

## 用元数据分页

```
defmodule MyAppWeb.Schema do
  ...

  query do
    # Changed to return paginated posts object
    field :posts, :paginated_posts do
      arg(:page, non_null(:integer))
      resolve(&MyApp.PostsResolver.posts/3)
    end
  end

  @doc """
  Paginated posts object contains list of data and meta information
  """
  object :paginated_posts do
    field :results, list_of(:post)
    field :meta, :page_info
  end

  @doc """
  Meta information object structure
  """
  object :page_info do
    field :page, :integer
    field :total_pages, :integer
  end
end

defmodule MyApp.PostsRepository do
  ...

  @doc """
  How many posts there are
  """
  def count() do
    Enum.count(all_posts())
  end
end

defmodule MyApp.PostsResolver do
  ...

  @doc """
  Returns custom data set to support graphql defined structure
  """
  def posts(_, %{page: page}, _) do
    results = MyApp.PostsRepository.posts(page, @per_page)
    total_pages = Float.ceil(MyApp.PostsRepository.count() / @per_page)

    {:ok, %{results: results, meta: %{page: page, total_pages: total_pages}}}
  end
end 
```

这是实现简单分页所需的全部更改代码。而现在 graphql 查询看起来是这样的:

```
query {
  posts(page: 1) {
    results {
      id
      title
    },
    meta {
      page,
      totalPages
    }
  }
} 
```

## 结论

*   分页对我来说似乎很容易掌握。基于对以前帖子的理解，这很自然。
*   Graphql 方法论是处理 HTTP 层通信的，我喜欢它的无干扰性。他们不会尝试给出一些惯用的分页或类似的东西。[一切都是一个字段](https://dev.to/revent/graphql-with-absinthe-59kb)，你可以随心所欲的解析每个字段。
*   每个项目都可以实现并需要不同的结构来支持所有需求。例如扩展的元信息。所以对这个例子要有所保留，因为这不是“最好”的方法，也不是它试图做到的。

P.S .如果您有任何意见或我对某些部分还不够清楚，请告诉我:)

##### 未注释的最终代码版本

```
defmodule MyAppWeb.Schema do
  use Absinthe.Schema

  query do
    field :posts, :paginated_posts do
      arg(:page, non_null(:integer))
      resolve(&MyApp.PostsResolver.posts/3)
    end
  end

  object :paginated_posts do
    field :results, list_of(:post)
    field :meta, :page_info
  end

  object :page_info do
    field :page, :integer
    field :total_pages, :integer
  end

  object :post do
    field :id, :integer
    field :title, :string
  end
end

defmodule MyApp.PostsRepository do
  defmodule Post do
    defstruct [:id, :title]
  end

  def all_posts() do
    [
      %Post{id: 1, title: "1"},
      %Post{id: 2, title: "2"},
      %Post{id: 3, title: "3"},
      %Post{id: 4, title: "4"}
    ]
  end

  def posts(page \\ 1, per_page) do
    offset = (page - 1) * per_page

    all_posts()
    |> Enum.drop(offset)
    |> Enum.take(per_page)
  end

  def count() do
    Enum.count(all_posts())
  end
end

defmodule MyApp.PostsResolver do
  @per_page 2

  def posts(_, %{page: page}, _) do
    results = MyApp.PostsRepository.posts(page, @per_page)
    total_pages = Float.ceil(MyApp.PostsRepository.count() / @per_page)

    {:ok, %{results: results, meta: %{page: page, total_pages: total_pages}}}
  end
end 
```
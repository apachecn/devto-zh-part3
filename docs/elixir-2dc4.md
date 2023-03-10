# Elixir :使用模式匹配

> 原文：<https://dev.to/gumi/elixir-2dc4>

模式匹配是 Elixir 的强大语法。 基本使用方法在 gumi TECH Blog「 [Elixir 入门 04 :模式匹配](https://dev.to/gumi/elixir-04--1346)”中进行了说明。 本文提供了一些本文中未涉及的代码示例。

# 关键词列表和地图

前文《Elixir 入门 04》介绍了在列表中使用模式匹配的代码。 当然，也可以在关键字列表中使用。 但是，要素的数量及其顺序也必须匹配。 因此，实际上很少被使用吧。

```
iex> [a: a] = [a: 1]
[a: 1]
iex> a
1
iex> [a: a] = [a: 1, b: 2]
** (MatchError) no match of right hand side value: [a: 1, b: 2]
iex> [b: b, a: a] = [a: 1, b: 2]
** (MatchError) no match of right hand side value: [a: 1, b: 2] 
```

Enter fullscreen mode Exit fullscreen mode

与关键字列表不同，模式匹配在地图中非常有用。 因为与列表相比，地图的键具有以下特征:

*   可以使用任何数据类型
*   不问顺序

地图的模式与子集匹配。 也就是说，只要匹配图案中包含的键就可以了。 因此，空贴图`%{}`将匹配所有贴图。

```
iex> %{} = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> %{:a => a} = %{2 => :b, :a => 1}
%{2 => :b, :a => 1}
iex> a
1
iex> %{:c => c} = %{:a => 1, 2 => :b}
** (MatchError) no match of right hand side value: %{2 => :b, :a => 1} 
```

Enter fullscreen mode Exit fullscreen mode

可以将变量用于引用、模式匹配或添加到地图的键。

```
iex> n = 1
1
iex> map = %{n => :one}
%{1 => :one}
iex> map[n]
:one
iex> %{^n => :one} = %{1 => :one, 2 => :two, 3 => :three}
%{1 => :one, 2 => :two, 3 => :three} 
```

Enter fullscreen mode Exit fullscreen mode

# condition

在`case/2`中决定是否符合条件的是模式匹配。 要承担剩下的所有情况，请使用`_`。

```
defmodule MyCase do
  def get_result(tuple) do
    case tuple do
      {:ok, value} -> value
      {:error, error} -> "error: " <> error
      _ -> :others
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> MyCase.get_result({:ok, "success"})
"success"
iex> MyCase.get_result({:error, "something wrong"})
"error: something wrong"
iex> MyCase.get_result({:oops})                    
:others 
```

Enter fullscreen mode Exit fullscreen mode

# Number of Holmium

函数的规定中加入了防护和多个子句。 多个子句由 Elixir 按从上到下的顺序尝试，并执行匹配的子句。 如果参数不匹配任何一个，则会出现错误。

```
defmodule Math do
  def zero?(0) do
    true
  end

  def zero?(x) when is_integer(x) do
    false
  end
end

IO.puts Math.zero?(0)   #=> true
IO.puts Math.zero?(1)   #=> false
IO.puts Math.zero?([1]) #=> ** (FunctionClauseError)
IO.puts Math.zero?(0.0) #=> ** (FunctionClauseError) 
```

Enter fullscreen mode Exit fullscreen mode

下面的代码是使用函数默认值和模式匹配的示例。 [`Enum.join/2`](https://hexdocs.pm/elixir/Enum.html#join/2)在列表(`Enumerable`)要素之间插入第 2 自变量的字符串，与二进制(字符串)相连。

```
defmodule Greeter do
  def hello(names, language \\ "en")

  def hello(names, language) when is_list(names) do
    hello(Enum.join(names, ", "), language)
  end

  def hello(name, language) when is_binary(name) do
    phrase(language) <> name
  end

  defp phrase("en"), do: "hello, "
  defp phrase("ja"), do: "こんにちは"
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Greeter.hello("alice")
"hello, alice"
iex> Greeter.hello(["alice", "carroll"])
"hello, alice, carroll"
iex> Greeter.hello(["桃太郎", "金太郎", "浦島太郎"], "ja")
"こんにちは桃太郎, 金太郎, 浦島太郎" 
```

Enter fullscreen mode Exit fullscreen mode

# Again

以下函数将列表元素的数值平方，并作为元素中存储的新列表返回。 这样取出列表元素并存储到新的列表元素中返回的处理被称为 map 算法，是函数型编程的重要想法之一。

```
defmodule Sum do
  def square([]), do: []
  def square([head | tail]), do:
    [head * head | square(tail)]
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Sum.square([1, 2, 3])
[1, 4, 9] 
```

Enter fullscreen mode Exit fullscreen mode

以下函数在列表为空时返回参数的合计值，递归调用结束。 如果不是空的话，第二个函数会递归调用尾部和合计值作为参数，并加上头部的值。 也就是说，每次递归时，都会将头的值加到合计值上。 从列表中依次取出元素并将其组合为一个值的过程称为 reduce 算法。

```
defmodule Sum do
  def up(list, accumulator \\ 0)
  def up([], accumulator), do: accumulator
  def up([head | tail], accumulator),
    do: up(tail, head + accumulator)
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Sum.up([1, 2, 3])
6
iex> Sum.up([4, 5], 6)
15 
```

Enter fullscreen mode Exit fullscreen mode

# 向不适当的自变量发出错误

假设您想从查询字符串中获得特定键的值。 于是我试着写了下面的函数。 我没有使用模式匹配。 另外，使用的函数如下所示。

*   [String.split/3](https://hexdocs.pm/elixir/String.html#split/3) :用第 2 参数的分隔字符串将第 1 参数的字符串分开，将分开的字符串作为要素列表返回。 第三个参数是可选的。
*   [Enum.find_value/3](https://hexdocs.pm/elixir/Enum.html#find_value/3) :依次取出第 1 参数的枚举型数据，处理并返回与函数条件匹配的元素。 第二个参数是可选的。
*   [Enum.at/3](https://hexdocs.pm/elixir/Enum.html#at/3) :从第 1 参数的列举型数据中取出第 2 参数的索引的要素并返回。 第三个参数是可选的。

```
defmodule Token do
  def get(string, token) do
    parts = String.split(string, "&")
    Enum.find_value(parts, fn pair ->
      key_value = String.split(pair, "=")
      Enum.at(key_value, 0) == token && Enum.at(key_value, 1)
      end)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果将字符串和键传递给函数，则检索并返回该值。 但是，即使查询字符串的格式不正确，也可能会返回值。

```
iex> Token.get("name=fumio&city=tokyo&lang=elixir", "name")
"fumio"
iex> Token.get("name=fumio=nonaka&city=tokyo&lang=elixir", "name")
"fumio"
iex> Token.get("name&city=tokyo&lang=elixir", "name")             
nil 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果查询字符串中包含键和值不在两个组中的内容，我想返回错误。 此时，按如下所示用列表进行模式匹配即可。 如果元素数量不匹配，则会返回错误。 代码也比上面的函数清晰了。

```
defmodule Token do
  def get(string, token) do
    parts = String.split(string, "&")
    Enum.find_value(parts, fn pair ->
      [key, value] = String.split(pair, "=")
      key == token && value
      end)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Token.get("name=fumio&city=tokyo&lang=elixir", "name")
"fumio"
iex> Token.get("name=fumio=nonaka&city=tokyo&lang=elixir", "name")
** (MatchError) no match of right hand side value: ["name", "fumio", "nonaka"]
iex> Token.get("name&city=tokyo&lang=elixir", "name")             
** (MatchError) no match of right hand side value: ["name"] 
```

Enter fullscreen mode Exit fullscreen mode

模式匹配有缩小取出数据的范围、根据条件和情况划分、数据的确认等各种各样的使用方法。 请一定要活用一下。
# Elixir 入门 16 :协议

> 原文：<https://dev.to/gumi/elixir-16--lif>

本文在 Elixir 官方网站的许可下，根据“[Protocols](https://elixir-lang.org/getting-started/protocols.html) ”的解说，加以修改，对 Elixir 的协议进行说明。 协议是用 Elixir 实现多态性的机制。 只要实现了协议，就可以调用任何数据。

# 协议

例如，[`String.Chars`协议](https://hexdocs.pm/elixir/String.Chars.html)具备[`to_string/1`](https://hexdocs.pm/elixir/String.Chars.html#to_string/1)。 但是，元组没有实现这个协议。

```
iex> to_string(1)
"1"
iex> to_string(:atom)
"atom"
iex> to_string({3.14, "apple", :pie})
** (Protocol.UndefinedError) protocol String.Chars not implemented for {3.14, "apple", :pie}. This protocol is implemented for: Atom, BitString, Date, DateTime, Float, Integer, List, NaiveDateTime, Time, URI, Version, Version.Requirement
    (elixir) /private/tmp/elixir-20180507-68757-17gx35t/elixir-1.6.5/lib/elixir/lib/string/chars.ex:3: String.Chars.impl_for!/1
    (elixir) /private/tmp/elixir-20180507-68757-17gx35t/elixir-1.6.5/lib/elixir/lib/string/chars.ex:22: String.Chars.to_string/1 
```

Enter fullscreen mode Exit fullscreen mode

协议的安装由[`defimpl/3`](https://hexdocs.pm/elixir/Kernel.html#defimpl/3)规定。 在协议之后添加用`for:`实现的数据，在`do`块中添加的是调用的函数。

```
defimpl String.Chars, for: Tuple do
  def to_string(tuple) do
    interior =
      tuple
      |> Tuple.to_list()
      |> Enum.map(&Kernel.to_string/1)
      |> Enum.join(", ")
    "{#{interior}}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

- [`Tuple.to_list/1`](https://hexdocs.pm/elixir/Tuple.html#to_list/1):将元组列为列表。
- [`Enum.map/2`](https://hexdocs.pm/elixir/Enum.html#map/2):将可枚举的项传递给函数，并返回返回值的列表。
- [`Enum.join/2`](https://hexdocs.pm/elixir/Enum.html#join/2):将可列举的项目制成字符串，用第 2 参数的字符串连接。

```
iex> to_string({3.14, "apple", :pie})
"{3.14, apple, pie}" 
```

Enter fullscreen mode Exit fullscreen mode

Elixir 在调查数据结构中有几个项目时，有以下两个惯用语。

*   `length`:计算得到信息。
    *   例如，[`length/1`](https://hexdocs.pm/elixir/Kernel.html#length/1)会计算并返回列表中的所有项目。
*   `size`:数据结构中包含已经计算过的信息。
    *   例如，[`tuple_size/1`](https://hexdocs.pm/elixir/Kernel.html#tuple_size/1)和[`byte_size/1`](https://hexdocs.pm/elixir/Kernel.html#byte_size/1)无论数据的大小如何，都可以立即取出值。

Elixir 内置了按类型获得大小(`size`)的函数。 但是，实现协议后，可以从所有具有预先计算大小的数据结构中得到该值。

制定新的协议时使用[`defprotocol/2`](https://hexdocs.pm/elixir/Kernel.html#defprotocol/2)。 接在后面的是协议名称和`do`块。 然后，请添加用`defimpl/3`实现的函数。 当然，如果传递未实现协议的数据类型，则会返回错误。

```
defprotocol Size do
  @doc "データ構造のサイズを求めます。"
  def size(data)
end
defimpl Size, for: BitString do
  def size(string), do: byte_size(string)
end
defimpl Size, for: Map do
  def size(map), do: map_size(map)
end
defimpl Size, for: Tuple do
  def size(tuple), do: tuple_size(tuple)
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Size.size("hello")
5
iex> Size.size({:hello, "world"})
2
iex> Size.size(%{hello: "world"})
1
iex> Size.size([:hello, "world"])
** (Protocol.UndefinedError) protocol Size not implemented for [:hello, "world"]
    size.exs:1: Size.impl_for!/1
    size.exs:3: Size.size/1 
```

Enter fullscreen mode Exit fullscreen mode

协议可以在 Elixir 的所有数据类型中实现。

*   `Atom`
*   `BitString`
*   `Float`
*   `Function`
*   `Integer`
*   `List`
*   `Map`
*   `PID`
*   `Port`
*   `Reference`
*   `Tuple`

# 协议和结构体

将协议与结构一起使用可以增加 Elixir 的可扩展性。 结构体是素的地图，没有实现与地图相同的协议。 作为结构体安装的是[`MapSet`](https://hexdocs.pm/elixir/MapSet.html)。

前款规定的`Size`协议没有安装在结构体上。 调查尺寸时使用[`MapSet.size/1`](https://hexdocs.pm/elixir/MapSet.html#size/1)。

```
iex> Size.size(%{})
0
iex> set = %MapSet{} = MapSet.new
#MapSet<[]>
iex> Size.size(set)
** (Protocol.UndefinedError) protocol Size not implemented for #MapSet<[]>
    size.exs:1: Size.impl_for!/1
    size.exs:3: Size.size/1
iex> MapSet.size(set)
0 
```

Enter fullscreen mode Exit fullscreen mode

`MapSet.size/1`也返回预先计算的大小。 `Size`将协议安装到`MapSet`中吧。

```
defimpl Size, for: MapSet do
  def size(set), do: MapSet.size(set)
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Size.size(%MapSet{})
0 
```

Enter fullscreen mode Exit fullscreen mode

可以在结构体上安装根据用途的协议。

```
defmodule User do
  defstruct [:name, :age]
end
defimpl Size, for: User do
  def size(_user), do: 2
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> john = %User{age: 27, name: "John"}
%User{age: 27, name: "John"}
iex> Size.size(john)
2 
```

Enter fullscreen mode Exit fullscreen mode

# any 的安装

将所有的协议一个一个地实施起来，很费工夫吧。 在 Elixir 上这样的时候，有两种方法。 首先，派生( derive )该类型的协议实现。 第二，也可以自动为所有类型实现协议。 在任何情况下，都必须在`Any`中安装协议。

## 派生

在 Elixir 中，可以基于`Any`的实现来派生协议的实现。

```
defimpl Size, for: Any do
  def size(_), do: 0
end 
```

Enter fullscreen mode Exit fullscreen mode

也有不适合安装该`Any`的协议的类型。 因此，想要进行该安装的结构体必须在`defstruct/1`之前用`@derive`属性明确地派生(参照“[Deriving](https://hexdocs.pm/elixir/Kernel.html#defstruct/1-deriving) ”)。

```
defmodule OtherUser do
  @derive [Size]
  defstruct [:name, :age]
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Size.size(%OtherUser{})
0
iex> Size.size([1, 2, 3])
** (Protocol.UndefinedError) protocol Size not implemented for [1, 2, 3]
    example.exs:1: Size.impl_for!/1    example.exs:3: Size.size/1 
```

Enter fullscreen mode Exit fullscreen mode

## 回退到纽约

也可以在找不到实现时，明示将协议回退到`Any`。 根据`defimpl/3`的协议规定，将`@fallback_to_any`属性设定为`true`。 这样，未安装协议的所有数据类型都不会根据`Any`的安装发生错误。

```
defprotocol Size do
  @fallback_to_any true
  def size(data)
end

defimpl Size, for: Any do
  def size(_), do: 0
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Size.size([1, 2, 3])
0 
```

Enter fullscreen mode Exit fullscreen mode

大多数协议都认为，在未实现时返回错误是合适的。 在这种情况下，用`@derive`明示比较好吧。 Elixir 的开发者似乎更喜欢明示，而不是默示。 很多库也采用`@derive`。

# 已嵌入的协议

Elixir 预先内置了几个协议。 其中之一是[`Enumerable`协议](https://hexdocs.pm/elixir/Enumerable.html)。 在安装该协议的数据结构中，可以使用[`Enum`模块](https://hexdocs.pm/elixir/Enum.html)的函数。

```
iex> Enum.map([1, 2, 3], fn(x) -> x * x end)
[1, 4, 9]
iex> Enum.reduce([1, 2, 3], 0, fn(x, acc) -> x + acc end)
6 
```

Enter fullscreen mode Exit fullscreen mode

另一个容易利用的例子是[`String.Chars`协议](https://hexdocs.pm/elixir/String.Chars.html)。 可以将包含字符的数据结构转换为字符串。 `to_string/1`作为函数公开。

```
iex> to_string(:hello)
"hello" 
```

Enter fullscreen mode Exit fullscreen mode

请注意，Elixir 的字符串插值调用`to_string/1`函数。 并且，数值安装了`String.Chars`协议。

```
iex> age = 25
25
iex> "age: #{age}"
"age: 25" 
```

Enter fullscreen mode Exit fullscreen mode

但是，元组没有安装`String.Chars`协议。

```
iex> tuple = {1, 2, 3}
{1, 2, 3}
iex> "tuple: #{tuple}"
** (Protocol.UndefinedError) protocol String.Chars not implemented for {1, 2, 3}
    (elixir) lib/string/chars.ex:3: String.Chars.impl_for!/1
    (elixir) lib/string/chars.ex:22: String.Chars.to_string/1 
```

Enter fullscreen mode Exit fullscreen mode

为了想输出复杂的数据结构，[`Inspect`协议](https://hexdocs.pm/elixir/Inspect.html)具备的是[`inspect/2`函数](https://hexdocs.pm/elixir/Inspect.html#inspect/2)。

```
iex> "tuple: #{inspect(tuple)}"
"tuple: {1, 2, 3}" 
```

Enter fullscreen mode Exit fullscreen mode

`Inspect`是将任意的数据结构转换为可读取的文本表现的协议。 IEx 等工具用于输出结果。

```
iex> {1, 2, 3}
{1, 2, 3}
iex> %User{name: "john", age: 27}
%User{age: 27, name: "john"} 
```

Enter fullscreen mode Exit fullscreen mode

按照惯例，输出的值以`#`开始时，表示是在 Elixir 语法上无效的数据结构。 `Inspect`意味着协议无法恢复原状，信息可能会丢失。

```
iex> inspect &(&1+2)
"#Function<6.71889879/1 in :erl_eval.expr/5>" 
```

Enter fullscreen mode Exit fullscreen mode

# 协议的综合

在 Elixir 项目中使用构建工具[Mix](https://elixir-lang.org/getting-started/mix-otp/introduction-to-mix.html) 时，可能会看到以下输出。

```
Consolidated String.Chars
Consolidated Collectable
Consolidated List.Chars
Consolidated IEx.Info
Consolidated Enumerable
Consolidated Inspect 
```

Enter fullscreen mode Exit fullscreen mode

这些是与 Elixir 一起提供的所有协议。 它们被合并在一起。 因为可以对任何数据类型执行协议，所以每次调用时都必须检查该类型是否实现。 于是，负荷会变大。

但是，如果使用 Mix 这样的工具进行编译，则所有定义的模块，包括协议及其实现都将变得显而易见。 这样，协议就会被合并，成为无浪费且执行迅速的模块。

从 Elixir 1.2 开始，所有项目都将自动进行协议集成。 关于项目的组建，请参阅[Elixir official Mix &OTP Guide](https://github.com/arafatm/edu_elixir#elixir-official-mix--otp-guide) 。

#### Elixir 入门彩票

*   [Elixir 入门 01 :编写代码并尝试](https://dev.to/gumi/elixir-01--2585)
*   [Elixir 入门 02 :型的基本](https://dev.to/gumi/elixir-02--30n1)
*   [Elixir 入门 03 :运算符的基本](https://dev.to/gumi/elixir-03--33im)
*   [Elixir 入门 04 :模式匹配](https://dev.to/gumi/elixir-04--1346)
*   [仙丹入門 05: 条件-情况/条件/条件](https://dev.to/gumi/elixir-05----casecondif-60o)
*   [Elixir 入门 06 :二进制和字符串以及字符列表](https://dev.to/gumi/elixir-06--35na)
*   [Elixir 入门 07 :关键词列表和地图](https://dev.to/gumi/elixir-07--39hi)
*   [Elixir 入门 08 :模块和函数](https://dev.to/gumi/elixir-08--1c4c)
*   [Introduction T0】 Elixir 09: Rethink](https://dev.to/gumi/elixir-09--1a0p)
*   [仙丹入門 10: EnumとStream](https://dev.to/gumi/elixir-10-enumstream-4fpb)
*   [Elixir 入门 11 :流程](https://dev.to/gumi/elixir-11--2mia)
*   [Elixir 入门 12 :输入输出和文件系统](https://dev.to/gumi/elixir-12--4og6)
*   [仙丹入門 13: aliasとrequireおよびimport](https://dev.to/gumi/elixir-13-aliasrequireimport-55c1)
*   [Elixir 入门 14 :模块的属性](https://dev.to/gumi/elixir-14--3511)
*   [T0】 Elixir Beginner 15: Structure
*   Elixir 入门 16 :协议
*   [T0】 Elixir Beginner's Level 17: Record of Internal Package
*   [Elixir 入门 18 :锡吉尔](https://dev.to/gumi/elixir-18--5791)
*   [仙丹入門 19: tryとcatchおよびrescue](https://dev.to/gumi/elixir-19-trycatchrescue-50i8)
*   [Elixir 入门 20 :类型规格和行为](https://dev.to/gumi/elixir-20--j50)
*   [Elixir 入门 21 :调试](https://dev.to/gumi/elixir-21--21a1)
*   [Elixir 入门 22: Erlang 库](https://dev.to/gumi/elixir-22-erlang-2492)
*   [Elixir 入门 23 :下一步](https://dev.to/gumi/elixir-23--50ik)

##### Fanwai

*   [Elixir 入门:使用 Plug 为](https://dev.to/gumi/elixir-plug-40lb)
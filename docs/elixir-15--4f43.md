# Elixir beginner-level 15: structure

> 原文：<https://dev.to/gumi/elixir-15--4f43>

本文在 Elixir 官方网站的许可下，根据“[Structs](https://elixir-lang.org/getting-started/structs.html) ”的解说，加以修改，对 Elixir 中结构体的规定和使用方法进行说明。

# 确定结构体

结构体由[`defstruct/1`](https://hexdocs.pm/elixir/Kernel.html#defstruct/1)确定。 参数是关键字列表。 请提供要添加的字段及其默认值。

```
defmodule User do
  defstruct name: "John", age: 27
end 
```

Enter fullscreen mode Exit fullscreen mode

在结构体上附加模块名称，用与地图相似的语法制作(参照“[Elixir 入门 07 :关键字列表和地图](https://dev.to/gumi/elixir-07--39hi)”)。

```
iex> %User{}
%User{age: 27, name: "John"}
iex> %User{name: "Meg"}
%User{age: 27, name: "Meg"} 
```

Enter fullscreen mode Exit fullscreen mode

在结构中规定的字段在编译时可以保证只有它们而且全部都有。

```
iex> %User{age: 20}
%User{age: 20, name: "John"}
iex> %User{name: 20}
%User{age: 27, name: 20}
iex> %User{oops: :field}
** (KeyError) key :oops not found in: %User{age: 27, name: "John"}
    (stdlib) :maps.update(:oops, :field, %User{age: 27, name: "John"})
    example.exs: anonymous fn/2 in User.__struct__/1
    (elixir) lib/enum.ex:1899: Enum."-reduce/3-lists^foldl/2-0-"/3 
```

Enter fullscreen mode Exit fullscreen mode

# 参照结构体进行更新

引用结构中的字段或重写值时，可以使用与映射相同的方法。 使用更新语法(`|`)时，VM 会确认结构中不包含永久字段。 此时确认的字段结构是一个参照，同一结构体共享内存。

```
iex> john = %User{}
%User{age: 27, name: "John"}
iex> john.name
"John"
iex> meg = %{john | name: "Meg"}
%User{age: 27, name: "Meg"}
iex> %{meg | oops: :field}
** (KeyError) key :oops not found in: %User{age: 27, name: "Meg"}
    (stdlib) :maps.update(:oops, :field, %User{age: 27, name: "Meg"})
    (stdlib) erl_eval.erl:255: anonymous fn/2 in :erl_eval.expr/5
    (stdlib) lists.erl:1263: :lists.foldl/3 
```

Enter fullscreen mode Exit fullscreen mode

结构也可以使用模式匹配。 不仅可以确认键是否一致，还可以确认是否是同一结构体的值。

```
iex> meg = %{john | name: "Meg"}
%User{age: 27, name: "Meg"}
iex> %User{name: name} = meg
%User{age: 27, name: "Meg"}
iex> name
"Meg"
iex> %User{} = %{age: 27, name: "John"}
** (MatchError) no match of right hand side value: %{age: 27, name: "John"} 
```

Enter fullscreen mode Exit fullscreen mode

# 结构体是像素的映射

结构是具有特定字段的元素的地图。 结构具有名称为[`__struct__/0`](https://hexdocs.pm/elixir/Task.html#__struct__/0)的特殊字段。

```
iex> is_map(john)
true
iex> john.__struct__
User 
```

Enter fullscreen mode Exit fullscreen mode

之所以说结构体是“素”的映射，是因为不能使用映射所具备的协议。 另外，[`Enum.each/2`](https://hexdocs.pm/elixir/Enum.html#each/2)取出可列举的值，传递给自变量的函数进行处理。

```
iex> john_map = %{age: 27, name: "John"}
%{age: 27, name: "John"}
iex> john_struct = %User{age: 27, name: "John"}
%User{age: 27, name: "John"}
iex> john_map[:name]
"John"
iex> john_struct[:name]
** (UndefinedFunctionError) function User.fetch/2 is undefined (User does not implement the Access behaviour)
    User.fetch(%User{age: 27, name: "John"}, :name)
    (elixir) lib/access.ex:308: Access.get/3
iex> john_map.name
"John"
iex> john_struct.name
"John"
iex> Enum.each(john_map, fn({field, value}) -> IO.puts(value) end)
27
John
:ok
iex> Enum.each(john_struct, fn({field, value}) -> IO.puts(value) end)
** (Protocol.UndefinedError) protocol Enumerable not implemented for %User{age: 27, name:"John"}
    (elixir) lib/enum.ex:1: Enumerable.impl_for!/1
    (elixir) lib/enum.ex:141: Enumerable.reduce/3
    (elixir) lib/enum.ex:1911: Enum.each/2 
```

Enter fullscreen mode Exit fullscreen mode

结构体可以用`Map`模块的函数处理。

```
iex> kurt = Map.put(%User{}, :name, "Kurt")
%User{age: 27, name: "Kurt"}
iex> takashi = Map.merge(kurt, %User{name: "Takashi"})
%User{age: 27, name: "Takashi"}
iex> Map.keys(takashi)
[:__struct__, :age, :name] 
```

Enter fullscreen mode Exit fullscreen mode

*   [`Map.put/3`](https://hexdocs.pm/elixir/Map.html#put/3):在第 1 自变量的映射中，用第 2 自变量的键加上第 3 自变量的值。
*   [`Map.merge/2`](https://hexdocs.pm/elixir/Map.html#merge/2):在第 1 参数的映射中加上第 2 参数的映射的键和值。 同一密钥的值将被第二个参数复盖。
*   [`Map.keys/1`](https://hexdocs.pm/elixir/Map.html#keys/1):将地图的所有键存储在列表中并返回。

结构及其协议带来了多态性，这是 Elixir 开发人员的重要功能。

# 默认值和必填键

确定结构时，可以省略键的缺省值。 在这种情况下，如果不传递值，就会得到`nil`。

```
defmodule User do
  defstruct [:name, :age]
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> %User{name: "John"}
%User{age: nil, name: "John"} 
```

Enter fullscreen mode Exit fullscreen mode

此外，如果在结构的规定中用`@enforce_keys`属性指定密钥列表，则必须提供该密钥的值。

```
defmodule User do
  @enforce_keys [:name]
  defstruct [:name, :age]
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> %User{name: "John"}
%User{age: nil, name: "John"}
iex> %User{age: 27}
** (ArgumentError) the following keys must also be given when building struct User: [:name]
    expanding struct: User.__struct__/1 
```

Enter fullscreen mode Exit fullscreen mode

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
*   Elixir beginner-level 15: structure
*   [Elixir 入门 16 :协议](https://dev.to/gumi/elixir-16--lif)
*   [T0】 Elixir Beginner's Level 17: Record of Internal Package
*   [Elixir 入门 18 :锡吉尔](https://dev.to/gumi/elixir-18--5791)
*   [仙丹入門 19: tryとcatchおよびrescue](https://dev.to/gumi/elixir-19-trycatchrescue-50i8)
*   [Elixir 入门 20 :类型规格和行为](https://dev.to/gumi/elixir-20--j50)
*   [Elixir 入门 21 :调试](https://dev.to/gumi/elixir-21--21a1)
*   [Elixir 入门 22: Erlang 库](https://dev.to/gumi/elixir-22-erlang-2492)
*   [Elixir 入门 23 :下一步](https://dev.to/gumi/elixir-23--50ik)

##### Fanwai

*   [Elixir 入门:使用 Plug 为](https://dev.to/gumi/elixir-plug-40lb)
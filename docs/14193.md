# Elixir 入门 20 :类型规范和行为

> 原文：<https://dev.to/gumi/elixir-20--j50>

本文在 Elixir 官方网站的许可下，根据“[Typespecs and behaviours](https://elixir-lang.org/getting-started/typespecs-and-behaviours.html) ”的解说，加以修改，对 Elixir 中类型的规格和行为的句法及使用方法进行说明。

# 型的规格

Elixir 是一种动态类型化语言。 所有类型的 Elixir 都是在运行时推理的。 尽管如此，Elixir 还是包含了类型的规范。 那是为了声明以下两个。

*   函数类型签名(规范)
*   自定义数据类型

## 函数的规格

默认情况下，Elixir 具有`integer`和`pid`等基本类型和更复杂的类型。 例如，[`round/1`函数](https://hexdocs.pm/elixir/Kernel.html#round/1)将数值舍入为整数后返回。 自变量为`number`(`integer`或`float`)，返回值为`integer`。 文档中的类型签名如下所示:

```
round(number) :: integer 
```

Enter fullscreen mode Exit fullscreen mode

`::`的意思是左边的函数返回右边所示类型的值。 函数的规格在定义之前附上`@spec`指令来写。 例如，如果是`round/1`函数，则为:

```
@spec round(number) :: integer
def round(number), do: # 実装... 
```

Enter fullscreen mode Exit fullscreen mode

以下示例为模块中指定的函数指定了规范。 如果将两个整数传递给函数，则会返回序号整数的平方和。 最后用[`round/1`](https://hexdocs.pm/elixir/Kernel.html#round/1)进行舍入是为了使返回值为整数(`integer`)。

```
defmodule Example do
  @spec square_sum(integer, integer) :: integer
  def square_sum(first, last) do
    for i <- first..last do
      i
    end
    |> Enum.map(fn num -> num * num end)
    |> Enum.sum()
    |> round
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.square_sum(1, 4)
30 
```

Enter fullscreen mode Exit fullscreen mode

Elixir 还支持复合型。 例如，整数列表为`[integer]`。 关于 Elixir 内置了什么样的型号，请参阅“[Typespecs](https://hexdocs.pm/elixir/typespecs.html) ”。

## 定制型的宣言

Elixir 内置了许多易于使用的类型。 而且，根据需要确定的是定制型。 在模块中用`@type`指令进行定义。

以下示例将函数的参数作为结构，并为函数提供了规范。 让我们为此结构声明定制类型。

```
defmodule SerialNum do
  defstruct first: nil, last: nil
end

defmodule Example do
  @spec square_sum(%SerialNum{first: integer, last: integer}) :: integer
  def square_sum(nums) do
    for i <- nums.first..nums.last do
      i
    end
    |> Enum.map(fn num -> num * num end)
    |> Enum.sum()
    |> round
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.square_sum(%SerialNum{first: 1, last: 4})
30 
```

Enter fullscreen mode Exit fullscreen mode

`@type`指令后用变量给出结构体的类型名称，括号`()`内用变量给出字段的类型。 决定其类型是对调用函数规定的规格。 在多个模块和函数中使用结构体时，规格的规定变得易懂简洁。

```
defmodule SerialNum do
  defstruct first: nil, last: nil
  @type t(first, last) :: %SerialNum{first: first, last: last}
end

defmodule Example do
  @spec square_sum(SerialNum.t(integer, integer)) :: integer
  def square_sum(nums) do
    for i <- nums.first..nums.last do
      i
    end
    |> Enum.map(fn num -> num * num end)
    |> Enum.sum()
    |> round
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

也可以在结构一侧确定模具。 以下示例添加自定义类型，并在函数规范未提供字段类型时将其作为结构的声明类型。

```
defmodule SerialNum do
  defstruct first: nil, last: nil
  @type t(first, last) :: %SerialNum{first: first, last: last}
  @type t :: %SerialNum{first: integer, last: integer}
end

defmodule Example do
  @spec square_sum(SerialNum.t()) :: integer
  def square_sum(nums) do
    for i <- nums.first..nums.last do
      i
    end
    |> Enum.map(fn num -> num * num end)
    |> Enum.sum()
    |> round
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在`@type`中声明的自定义类型将被导出，可以从其他模块使用。 请用`@typep`指令声明只在模块内使用的自定义类型。

## 静态代码分析

Elixir 是一种动态语言。 因此，虽然有关类型的信息对开发人员很有帮助，但我不看编译器。 但是，有确认类型声明的工具。 例如，Erlang 的[Dialyzer](http://www.erlang.org/doc/man/dialyzer.html) 是进行代码静态分析的工具。 因此，私有函数也有写规范的意义。

# 行为

许多模块共享同一个 API。 例如，[`Plug`](https://github.com/elixir-lang/plug)规定的是在 web APP 演示中组装的模块的规格。 各`Plug`作为模块被制作，必须安装`init/1`和`call/2`的至少两个公共函数。

行为承担以下两个功能。

*   确定模块必须实现的函数。
*   检查模块是否实现了所需的函数。

行为是面向对象语言(如 Java )中的接口。 模块应该实现的函数签名。

## 定义行为

假设想分析 JSON 和[消息包](https://msgpack.org/)等结构化数据。 如果为此分别制作解析器的话，即使数据的结构不同，所需的基本功能也是共通的吧。 在这种情况下，请用行为决定应该实现的函数。

接受行为的模块必须实现`@callback`指令规定的所有函数(请参见[`@behaviour`](https://hexdocs.pm/elixir/Module.html#module-behaviour-notice-the-british-spelling))。 语法与`@spec`指令基本相同。 `term`表示 Elixir 的任意类型。 关于将字符串的类型设为`String.t`，请参阅“Typespecs”的“[Notes](https://hexdocs.pm/elixir/typespecs.html#notes) ”。

```
defmodule Parser do
  @callback parse(String.t) :: {:ok, term} | {:error, String.t}
  @callback extensions() :: [String.t]
end 
```

Enter fullscreen mode Exit fullscreen mode

## 接受行为

要接受行为，请在模块中用[`@behaviour`](https://hexdocs.pm/elixir/Module.html#module-behaviour-notice-the-british-spelling)指定行为。

```
defmodule JSONParser do
  @behaviour Parser
  def parse(str), do: "" # ... JSONの解析処理
  def extensions, do: ["json"]
end

defmodule YAMLParser do
  @behaviour Parser
  def parse(str), do: "" # ... YAMLの解析処理
  def extensions, do: ["yml"]
end 
```

Enter fullscreen mode Exit fullscreen mode

如果模块没有实现行为中定义的`@callback`的所有函数，编译时会显示警告。

```
defmodule JSONParser do
  @behaviour Parser
  def parse(str), do: "" # ... parse JSON
  # def extensions, do: ["json"]
end 
```

Enter fullscreen mode Exit fullscreen mode

```
warning: function extensions/0 required by behaviour Parser is not implemented (in module JSONParser) 
```

Enter fullscreen mode Exit fullscreen mode

## 动态调用

行为经常与“动态调用”( dynamic dispatching )一起使用。 在以下示例中，将函数添加到了定义行为的模块中。 此函数接收的是接受行为的模块。

```
defmodule Parser do
  @callback parse(String.t) :: {:ok, term} | {:error, String.t}
  @callback extensions() :: [String.t]
  def parse!(implementation, contents) do
    case implementation.parse(contents) do
      {:ok, data} -> data
      {:error, error} -> raise ArgumentError, "parsing error: #{error}"
    end
  end
end

defmodule JSONParser do
  @behaviour Parser
  def parse(str), do: str # ... parse JSON
  def extensions, do: ["json"]
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Parser.parse!(JSONParser, {:ok, "データ"})
"データ" 
```

Enter fullscreen mode Exit fullscreen mode

为了进行动态调用，定义行为不是必须的。 但是，这两个经常一起使用。

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
*   [Elixir 入门 16 :协议](https://dev.to/gumi/elixir-16--lif)
*   [T0】 Elixir Beginner's Level 17: Record of Internal Package
*   [Elixir 入门 18 :锡吉尔](https://dev.to/gumi/elixir-18--5791)
*   [仙丹入門 19: tryとcatchおよびrescue](https://dev.to/gumi/elixir-19-trycatchrescue-50i8)
*   Elixir 入门 20 :类型规范和行为
*   [Elixir 入门 21 :调试](https://dev.to/gumi/elixir-21--21a1)
*   [Elixir 入门 22: Erlang 库](https://dev.to/gumi/elixir-22-erlang-2492)
*   [Elixir 入门 23 :下一步](https://dev.to/gumi/elixir-23--50ik)

##### Fanwai

*   [Elixir 入门:使用 Plug 为](https://dev.to/gumi/elixir-plug-40lb)
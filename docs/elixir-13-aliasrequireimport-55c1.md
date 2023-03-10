# 长生不老药入門 13: aliasとrequireおよびimport

> 原文：<https://dev.to/gumi/elixir-13-aliasrequireimport-55c1>

本文得到了 Elixir 官方网站的许可，根据“[alias，require，and import](https://elixir-lang.org/getting-started/alias-require-and-import.html) ”的解说，加以修改，修改了 Elixir 指令`alias`和`require`以及`import`

为了便于软件复用，Elixir 包含三个指令和一个宏: 指令具有[语言作用域](https://ja.wikipedia.org/wiki/%E9%9D%99%E7%9A%84%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%97)( lexical scope )。 `use`是标准的宏。

*   `alias`指令:赋予别名，以便模块可以用别名调用。
*   `require`指令:请求模块以便宏可以调用。
*   `import`指令:读取模块以便在没有模块名称的情况下调用函数。
*   `use`宏:调用模块中的代码作为扩展点。

# 别名

[`alias/2`指令](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#alias/2)给已有的模块赋予任意的别名。 以未使用以下别名的代码为例:

```
defmodule Sayings.Greetings do
  def basic(name), do: "hello, #{name}"
end

defmodule Example do
  def greeting(name), do: Sayings.Greetings.basic(name)
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.greeting("world")
"hello, world" 
```

Enter fullscreen mode Exit fullscreen mode

如果省略第 2 参数，则以点(`.`)分隔的模块名称的最后的识别符为别名。

```
defmodule Example do
  alias Sayings.Greetings
  def greeting(name), do: Greetings.basic(name)
end 
```

Enter fullscreen mode Exit fullscreen mode

在第 2 参数提供的名称中，请附上`as:`选项。 别名必须以大写字母开始。

```
defmodule Example do
  alias Sayings.Greetings, as: Hi
  def greeting(name), do: Hi.basic(name)
end 
```

Enter fullscreen mode Exit fullscreen mode

Elixir 的模块全部定义在名称空间`Elixir`中。 默认情况下可以省略那个。

```
iex> alias Example, as: String
Example
iex> String.greeting("tokyo")
"hello, tokyo"
iex> Elixir.String.length("hello")
5 
```

Enter fullscreen mode Exit fullscreen mode

别名具有语言作用域。 如果在模块中定义，则模块中的所有函数都可以引用该别名。

```
defmodule Example do
  alias Sayings.Greetings, as: Hi
  def greeting(name), do: Hi.basic(name)
  def greeting_ex(name), do: Hi.basic(name) <> "!!"
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.greeting_ex("world")
"hello, world!!" 
```

Enter fullscreen mode Exit fullscreen mode

如果在函数中定义别名，则无法被其他函数引用。

```
defmodule Example do
  def greeting(name) do
    alias Sayings.Greetings, as: Hi
    Hi.basic(name)
  end
  def greeting_ex(name), do: Hi.basic(name) <> "!!"
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.greeting("world")
"hello, world"
iex> Example.greeting_ex("world")
** (UndefinedFunctionError) function Hi.basic/1 is undefined (module Hi is not available)
    Hi.basic("world")
    example.exs: Example.greeting_ex/1 
```

Enter fullscreen mode Exit fullscreen mode

# 要求

Elixir 具有宏作为元编程机制。 元编程是指编写生成代码的代码。 宏是在编译时展开的。

公共函数可以全局使用。 但是，要使用宏，必须用[`require/2`指令](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#require/2)输入定义了它的模块。

`Integer.is_odd/1`在模块中被确定为宏(图 001 )。 因此，必须事先用`require/2`设定`Integer`模块才能使用。

#### t0.t1■在参考的 Integer.is_odd/1 的项目中显示 macro

[![elixir_13_001.png](img/398d29f49302ccb027fdeb38eafc9c78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pLVMyn6Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eanedky9c8y072id1l1y.png)T3】

```
iex> Integer.is_odd(5)
** (CompileError) iex: you must require Integer before invoking the macro Integer.is_odd/1
    (elixir) src/elixir_dispatch.erl:97: :elixir_dispatch.dispatch_require/6
iex> require Integer
Integer
iex> Integer.is_odd(5)
true 
```

Enter fullscreen mode Exit fullscreen mode

*   [`Integer.is_even/1`](https://hexdocs.pm/elixir/Integer.html#is_even/1):以逻辑值返回是否为奇数。
*   [`Integer.is_odd/1`](https://hexdocs.pm/elixir/Integer.html#is_odd/1):以逻辑值返回是否为偶数。

和指令`alias/2`一样，`require/2`也具有语言作用域。

# 导入

[`import/2`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#import/2)是不使用完全限定名就可以由函数或宏参照的指令。 可以在没有模块名称的情况下调用函数或宏。

```
iex> import List
List
iex> first([1, 2, 3])
1
iex> last([1, 2, 3])
3
iex> flatten([1, [[2], 3]])
[1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

*   [`List.first/1`](https://hexdocs.pm/elixir/List.html#first/1):返回列表的第一个元素。
*   [`List.last/1`](https://hexdocs.pm/elixir/List.html#last/1):返回列表的最后一个元素。

第 2 参数使用`only:`选项，缩小读取函数和宏的范围。

```
iex> import List, only: [first: 1, last: 1]
List
iex> first([1, 2, 3])
1
iex> flatten([1, [[2], 3]])
** (CompileError) iex:9: undefined function flatten/1 
```

Enter fullscreen mode Exit fullscreen mode

第 2 参数的选项除了函数宏的列表外，还给出了`:macros`和`:functions`。 另外，用`import/2`读取的宏在内部也会进行`require/2`。

```
iex> import Integer, only: :macros
Integer
iex> is_even(4)
true
iex> digits(123)
** (CompileError) iex: undefined function digits/1
iex> Integer.digits(123)
[1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> import Integer, only: :functions
Integer
iex> digits(123)
[1, 2, 3]
iex> is_odd(3)
** (CompileError) iex: undefined function is_odd/1 
```

Enter fullscreen mode Exit fullscreen mode

作为第 2 参数的选项，也用`except:`代替`only:`，相反规定从读取中排除的列表。

```
iex> import List, except: [first: 1, last: 1]
List
iex> flatten([1, [[2], 3]])
[1, 2, 3]
iex> last([1, 2, 3])
** (CompileError) iex: undefined function last/1 
```

Enter fullscreen mode Exit fullscreen mode

`import/2`指令也是语言作用域。 如果定在函数中，则不能被其他函数引用，会发生编译错误。

```
defmodule Example do
  def split(number) do
    import Integer, only: [digits: 1]
    digits(number)
  end
  def test(name), do: digits(name)  #コンパイルエラー
end 
```

Enter fullscreen mode Exit fullscreen mode

# 使用

[`use/2`使用](https://hexdocs.pm/elixir/Kernel.html#use/2)宏，可以从其他模块改变模块的规定。 `use/2`调用的是加在模块上的`__using__/1`回调的宏。 然后，将此宏的作用引入到另一个模块中。

以下代码是规定`__using__/1`回调的模块的示例。 此处仅用于测试，有关宏的详情，请参阅“[Macros](https://elixir-lang.org/getting-started/meta/macros.html) ”。

```
defmodule Hello do
  defmacro __using__(_opts) do
    quote do
      def greeting(name), do: "hello, #{name}"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果在其他模块中使用`use/2`，则`__using__/1`回调中规定的函数可以从该模块调用。

```
defmodule Example do
  use Hello
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.greeting("world")
"hello, world" 
```

Enter fullscreen mode Exit fullscreen mode

`use/2`的功能与使用`require/2`的功能相同，如下所示。

```
defmodule Example do
  # use Hello
  require Hello
  Hello.__using__(greeting: :value)
end 
```

Enter fullscreen mode Exit fullscreen mode

然后，如下改写`__using__/1`回调。 于是，函数的处理根据`use/2`的第 2 参数而改变。

```
defmodule Hello do
  defmacro __using__(opts) do
    hello = Keyword.get(opts, :hello, "hello")
    quote do
      def greeting(name), do: unquote(hello) <> "," <> name
    end
  end
end

defmodule Example do
  use Hello, hello: "こんにちは"
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.greeting("日本")
"こんにちは,日本" 
```

Enter fullscreen mode Exit fullscreen mode

# 理解别名

Elixir 的别名是编译时转换为原子的开头为大写的识别符(`String`和`Keyword`等)。

```
iex> is_atom(String)
true
iex> to_string(String)
"Elixir.String"
iex> String == :"Elixir.String"
true 
```

Enter fullscreen mode Exit fullscreen mode

使用`alias/2`时，别名会展开为原子。 因为在 Erlang VM (和 Elixir )中，模块由原子表示。

```
iex> :lists.flatten([1, [[2], 3]])
[1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

# 嵌套模块

模块可以嵌套。 要从外部引用子模块，必须使用来自父模块的完全限定名称。

```
defmodule Example do
  def greeting(name), do: "hello, #{name}"
  defmodule Greetings do
    def morning(name), do: "good morning, #{name}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.greeting("world")
"hello, world"
iex> Example.Greetings.morning("tokyo")
"good morning, tokyo" 
```

Enter fullscreen mode Exit fullscreen mode

如果在父模块中指定了子模块，则可以不使用完全限定名称来引用子模块。 因为子模块包含在父级的语言作用域中，所以会在内部创建别名。

```
defmodule Example do
  def greeting(name), do: "hello, #{name}"
  defmodule Greetings do
    def morning(name), do: "good morning, #{name}"
  end
  # alias Example.Greetings  #<- 内部的にエイリアスがつくられる
  def call_child(name), do: Greetings.morning(name)
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.call_child("japan")
"good morning, japan" 
```

Enter fullscreen mode Exit fullscreen mode

如果使用完全限定名称，则子模块也可以在父模块之外定义。 此时，子模块不一定写在父母之后也没关系。 此时，如果父模块只想以子模块的名称进行引用，请使用别名。

```
defmodule Example do
  def greeting(name), do: "hello, #{name}"
end

defmodule Example.Greetings do
  def morning(name), do: "good morning, #{name}"
end 
```

Enter fullscreen mode Exit fullscreen mode

此外，只要使用完全限定名称，即使没有父模块，也可以确定子模块。 因为所有的模块名称都将被转换为原子。

```
defmodule Example.Greetings.Japan do
  def greeting(name), do: "こんにちは, #{name}"
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.Greetings.Japan.greeting("日本")
"こんにちは, 日本" 
```

Enter fullscreen mode Exit fullscreen mode

# alias/import/require/useを複数使う

`alias`、`import`、`require`、`use`可以一次确定多个模块。 以下代码为两个子模块提供模块名称别名的示例。

```
iex> alias Example.Greetings.{US, Japan}
[Example.Greetings.US, Example.Greetings.Japan] 
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
*   长生不老药入門 13: aliasとrequireおよびimport
*   [Elixir 入门 14 :模块的属性](https://dev.to/gumi/elixir-14--3511)
*   [T0】 Elixir Beginner 15: Structure
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
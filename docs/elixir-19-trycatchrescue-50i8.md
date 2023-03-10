# 长生不老药入門 19: tryとcatchおよびrescue

> 原文：<https://dev.to/gumi/elixir-19-trycatchrescue-50i8>

本文在 Elixir 官网的许可下，根据“[try，catch and rescue](https://elixir-lang.org/getting-started/try-catch-and-rescue.html) ”的解说，加以修改，对 Elixir 中的错误处理进行说明。

Elixir 的错误机制有三种。 错误和慢速(`throw`)以及结束(`exit`)。 就各自的内容和使用方法进行解说吧。

# 错误

当代码中发生异常时，会使用错误或异常。 例如，在阿童木上添加数值时。 会出现公式参数不正确的错误。

```
iex> :atom + 1
** (ArithmeticError) bad argument in arithmetic expression
    :erlang.+(:atom, 1) 
```

Enter fullscreen mode Exit fullscreen mode

执行时的错误可以由[`raise/1`](https://hexdocs.pm/elixir/Kernel.html#raise/1)引起。 自变量是与错误一起显示的消息。

```
iex> raise "oops"
** (RuntimeError) oops 
```

Enter fullscreen mode Exit fullscreen mode

发生错误的函数中还有[`raise/2`](https://hexdocs.pm/elixir/Kernel.html#raise/2)。 第一个参数是错误模块名称，第二个参数是属性的关键字列表。

```
iex> raise(ArgumentError, message: "invalid argument")
** (ArgumentError) invalid argument 
```

Enter fullscreen mode Exit fullscreen mode

也规定了自己的错误。 在模块中用[`defexception/1`](https://hexdocs.pm/elixir/Kernel.html#defexception/1)制作作为错误返回的结构体。 这样就会产生模块名称的错误。 作为提供给异常结构体的字段，经常使用的是`message`。

```
defmodule MyError do
  defexception message: "default message"
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> raise MyError
** (MyError) default message
iex> raise MyError, message: "custom message"
** (MyError) custom message 
```

Enter fullscreen mode Exit fullscreen mode

在[`try/1`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#try/1)中加上[`rescue`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#try/1-rescue-clauses)，可以制定例外名称来恢复错误。 恢复自定义错误并检索错误是以下示例。

```
iex> try do
...>   raise MyError
...> rescue
...>   err in MyError -> err
...> end
%MyError{message: "default message"} 
```

Enter fullscreen mode Exit fullscreen mode

以下示例在发生运行时错误时检索错误。

```
iex> try do
...>   raise "oops"
...> rescue
...>   err in RuntimeError -> err
...> end
%RuntimeError{message: "oops"} 
```

Enter fullscreen mode Exit fullscreen mode

不特别使用错误的情况下，不取出也没关系。

```
iex> try do
...>   raise "oops"
...> rescue
...>   RuntimeError -> "Error!"
...> end
"Error!" 
```

Enter fullscreen mode Exit fullscreen mode

实际上，Elixir 开发人员很少使用`try`/ `rescue`语法。 在许多其他语言中，例如，如果文件未能正确打开，您可能需要恢复错误。 但是，Elixir 的[`File.read/1`函数](https://hexdocs.pm/elixir/File.html#read/1)只是用元组返回文件是否正确打开的信息。

```
iex> File.read "hello"
{:error, :enoent}
iex> File.write "hello", "world"
:ok
iex> File.read "hello"
{:ok, "world"} 
```

Enter fullscreen mode Exit fullscreen mode

不用`try`/ `rescue`也没关系。 想根据结果改变处理方式时，在`case`文中使用模式匹配就可以了。

```
defmodule Example do
  def read_file(file) do
    case File.read file do
      {:ok, body} -> IO.puts "Success: #{body}"
      {:error, reason} -> IO.puts "Error: #{reason}"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.read_file("hello")
Success: world
:ok
iex> Example.read_file("unknown")
Error: enoent
:ok 
```

Enter fullscreen mode Exit fullscreen mode

关于文件的读取，希望在文件存在的前提下，不存在时会发生错误时，请使用[`File.read!/1`](https://hexdocs.pm/elixir/File.html#read!/1)。

```
iex> File.read! "unknown"
** (File.Error) could not read file "unknown": no such file
 or directory
    (elixir) lib/file.ex:310: File.read!/1 
```

Enter fullscreen mode Exit fullscreen mode

标准库的许多函数有两种，一种是用`{:ok, result}`或`{:error, reason}`等元组返回结果，另一种是因错误而发生异常，后者采用固定的命名方法。 命名规则([Naming Conventions](https://hexdocs.pm/elixir/naming-conventions.html) )是对于返回元组的函数，在同一真实性中用同一名称后面加上`!`。 返回值不是元组而是直接结果，如果发生错误，则会引发异常(“[Trailing bang (foo！ )见](https://hexdocs.pm/elixir/naming-conventions.html#trailing-bang-foo))。

Elixir 中尽量不使用`try`/ `rescue`。 因为控制流中不使用错误。 正如文字所示，错误意味着意外的例外情况。 实际要求控制流程的结构时，请使用`throw`。

# 扔

在 Elixir 中，将值抛出[`throw/1`](https://hexdocs.pm/elixir/Kernel.html#throw/1)，之后可以通过[`catch`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#try/1-catch-clauses)接收。 使用`throw`和`catch`是在没有其他接收值的手段的情况下。

```
iex> try do
...>   for x <- 0..10 do
...>     if x > 4, do: throw(x)
...>     IO.puts(x)
...>   end
...> catch
...>   x -> "Caught: #{x}"
...> end
0
1
2
3
4
"Caught: 5" 
```

Enter fullscreen mode Exit fullscreen mode

在上例中，`Enum`模块具备合适的 API，因此实际上可以使用[`Enum.find/3`](https://hexdocs.pm/elixir/Enum.html#find/3)。

```
iex> Enum.find(0..10, &(&1 >  4))
5 
```

Enter fullscreen mode Exit fullscreen mode

# 退出

Elixir 的代码都在进程中运行，进程相互通信。 如果流程因“自然原因”(例如无法处理的例外)而结束，则会发出`exit`的信号。 过程还可以通过在[`exit/1`](https://hexdocs.pm/elixir/Kernel.html#exit/1)中明确发出信号来结束。 然后，elixir shell 会自动将消息输出到终端。

```
iex> spawn_link fn -> exit("oh no") end
** (EXIT from #PID<0.84.0>) shell process exited with reason: "oh no" 
```

Enter fullscreen mode Exit fullscreen mode

`exit`也可以用`try`/ `catch`来表示。

```
iex> try do
...>   exit "oh no!"
...> catch
...>   :exit, _ -> "exit blocked"
...> end
"exit blocked"
iex> 
```

Enter fullscreen mode Exit fullscreen mode

但是，很少使用`try`/ `catch`，而且因此很少能捕捉到`exit`。

`exit`信号在 Erlang VM 提供的容错系统中起着重要的作用。 进程通常在管理员的监视树下工作。 监视树也是一个过程，检测来自监视过程的`exit`信号。 `exit`收到信号后，监视系统将工作，重新启动监视过程。

监视系统正是建立`try`/ `catch`和`try`/ `rescue`这样的结构，所以很少在 Elixir 上使用。 因此，不是恢复错误，而是让其迅速失败。 这样，监视树在错误后，一定会将 APP 应用程序恢复到初始状态。

# [后](#after)

在某些情况下，在处理可能发生错误的操作后，必须释放资源。 对此进行处理的是[`try`/ `after`句法](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#try/1-after-clauses)。

```
iex> try do
...>   raise "Oh no!"
...> rescue
...>   err in RuntimeError -> IO.puts("An error occurred: " <> err.message)
...> after
...>   IO.puts "The end!"
...> end
An error occurred: Oh no!
The end!
:ok 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果打开文件后用`after`关闭，则无论打开的文件发生什么问题都一定会关闭。

```
defmodule Example do
  def handle_file(path, string) do
    {:ok, file} = File.open(path, [:utf8, :write])
    try do
      IO.write file, string
      raise "oops, something went wrong"
    after
      File.close(file)
      IO.puts "the file is closed"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.handle_file("hello", "world")
the file is closed
** (RuntimeError) oops, something went wrong 
```

Enter fullscreen mode Exit fullscreen mode

`after`节无论`try`区块是否成功都会执行。 但是，链接的进程结束后，当前的进程也将结束，不会处理`after`节。 不过，在 Elixir 中，文件链接到当前流程。 因此，如果该进程崩溃，则无论`after`节如何，总是被关闭。 这同样适用于其他资源，如 ETS、表、套接字和端口。

根据情况不同，也有可能希望将函数主体全部放入`try`语法中，用`after`规定后处理一定要进行。 那样的时候，可以省略`try`方框。 因为在函数中使用`after`、`rescue`或`catch`时，Elixir 会自动用`try`包装函数主体。

```
defmodule RunAfter do
  def without_even_trying do
    raise "oops"
  after
    IO.puts "cleaning up!"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> RunAfter.without_even_trying
cleaning up!
** (RuntimeError) oops 
```

Enter fullscreen mode Exit fullscreen mode

# 目不斜视

如果在`try`语法中加入[`else`子句](https://hexdocs.pm/elixir/master/Kernel.SpecialForms.html#try/1-else-clauses)，则在没有抛出或错误的情况下处理`try`块时，其结果与`else`块匹配。

```
iex> x = 2
2
iex> try do
...>   1 / x
...> rescue
...>   ArithmeticError ->
...>     :infinity
...> else
...>   y when y < 1 and y > -1 ->
...>     :small
...>   _ ->
...>     :large
...> end
:small 
```

Enter fullscreen mode Exit fullscreen mode

`else`无法捕捉到区块内的例外。 `else`如果在块中不能进行模式匹配，就会发生例外。 这个例外不能在现行的`try`/ `catch`/ `rescue`/ `after`块中处理。

# 变量的作用域

`try`/ `catch`/ `rescue`/ `after`块中规定的变量不能从外部参照。 `try`块可能会失败，所以不会将变量绑定到外部。

```
iex> what_happened = :outside
:outside
iex> try do
...>   raise "fail"
...>   what_happened = :did_not_raise
...> rescue
...>   _ -> what_happened = :rescued
...> end
:rescued
iex> what_happened
:outside 
```

Enter fullscreen mode Exit fullscreen mode

`try`想从外部参照语法结果时，只要将公式的值放入变量中即可。

```
iex> what_happened =
...>   try do
...>     raise "fail"
...>     :did_not_raise
...>   rescue
...>     _ -> :rescued
...>   end
:rescued
iex> what_happened
:rescued 
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
*   [T0】 Elixir Beginner 15: Structure
*   [Elixir 入门 16 :协议](https://dev.to/gumi/elixir-16--lif)
*   [T0】 Elixir Beginner's Level 17: Record of Internal Package
*   [Elixir 入门 18 :锡吉尔](https://dev.to/gumi/elixir-18--5791)
*   长生不老药入門 19: tryとcatchおよびrescue
*   [Elixir 入门 20 :类型规格和行为](https://dev.to/gumi/elixir-20--j50)
*   [Elixir 入门 21 :调试](https://dev.to/gumi/elixir-21--21a1)
*   [Elixir 入门 22: Erlang 库](https://dev.to/gumi/elixir-22-erlang-2492)
*   [Elixir 入门 23 :下一步](https://dev.to/gumi/elixir-23--50ik)

##### Fanwai

*   [Elixir 入门:使用 Plug 为](https://dev.to/gumi/elixir-plug-40lb)
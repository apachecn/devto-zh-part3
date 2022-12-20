# Elixir :关于管道运算符| >

> 原文：<https://dev.to/gumi/elixir-1g47>

[管道运算符`|>`](https://hexdocs.pm/elixir/Kernel.html#%7C%3E/2)在 gumi TECH Blog「 [Elixir 入门 10: Enum 和 Stream](https://dev.to/gumi/elixir-10-enumstream-4fpb) ”的“管道运算符”中进行了说明。 本文在分析的同时，补充了官方文档的信息等。

# 流水线运算

由管道运算符`|>`进行的运算有时也称为流水线运算。 将左侧表达式的值作为右侧函数的第一个参数传递。

> `左辺 |> 右辺`

例如，以下代码用`List.flatten/1`展平列表中的嵌套: 左边是第 1 参数，所以不添加到右边函数的参数中。

```
iex> list = [1, [[2], 3]] |> List.flatten 
[1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

有两个参数时，用括号`()`将第 2 参数括起来传递给函数。 在这种情况下，括号不能省略(如果省略，将显示警告)。

```
iex> square = list |> Enum.map(fn(x) -> x * x end)      
[1, 4, 9] 
```

Enter fullscreen mode Exit fullscreen mode

有两个以上的自变量时，也请同样省略第 1 自变量，将第 2 自变量以后加在括号`()`中。

```
iex> sum = square |> Enum.reduce(0, fn(x, acc) -> x + acc end)
14 
```

Enter fullscreen mode Exit fullscreen mode

如果在一行中编写上述三个运算而没有管道运算符，则: 因为嵌套，而且从内部进行运算，所以是难以读取的代码。

```
iex> sum = Enum.reduce(Enum.map(List.flatten([1, [[2], 3]]), fn(x) -> x * x end), 0, fn(x, acc) -> x + acc end)
14 
```

Enter fullscreen mode Exit fullscreen mode

如果使用管道运算符的话，可以从左到右按照运算的顺序写，很容易理解吧。

```
iex> [1, [[2], 3]] |> List.flatten |> Enum.map(fn x -> x * x end) |> Enum.reduce(0, fn(x, acc) -> x + acc end) 
```

Enter fullscreen mode Exit fullscreen mode

参数中有函数时，使用捕捉运算符`&`可以写得更短(参照“[Elixir 入门 08 :模块和函数](https://dev.to/gumi/elixir-08--1c4c)”“函数捕捉”)。

```
iex> [1, [[2], 3]] |> List.flatten |> Enum.map(&(&1 * &1)) |> Enum.reduce(0, &+/2)
14 
```

Enter fullscreen mode Exit fullscreen mode

# 管道运算符要注意

Elixir 官方文档的[`|>/2`](https://hexdocs.pm/elixir/Kernel.html#%7C%3E/2)一项中显示了[两个注意事项](https://hexdocs.pm/elixir/Kernel.html#%7C%3E/2-pitfalls)。

## 运算符的优先顺序

一个是与运算符的优先顺序相关的括号`()`。 以下代码将导致错误。

```
String.graphemes "Hello" |> Enum.reverse 
```

Enter fullscreen mode Exit fullscreen mode

根据运算符的优先顺序进行如下解释，因为对二进制没有规定[`Enumerable`](https://hexdocs.pm/elixir/Enumerable.html)协议。

```
iex> String.graphemes("Hello" |> Enum.reverse)
** (Protocol.UndefinedError) protocol Enumerable not implemented for "Hello" 
```

Enter fullscreen mode Exit fullscreen mode

因此，必须用括号表示优先顺序，如下所示。

```
iex> String.graphemes("Hello") |> Enum.reverse
["o", "l", "l", "e", "H"] 
```

Enter fullscreen mode Exit fullscreen mode

不过，好不容易使用了管道运算符，还是写下面这样比较好吧。

```
iex> "Hello" |> String.graphemes |> Enum.reverse
["o", "l", "l", "e", "H"] 
```

Enter fullscreen mode Exit fullscreen mode

## 用于无名函数时

另一个注意事项是在无名函数中使用管道运算符(对于函数捕获也是如此)。

```
iex> func = fn(str) -> str |> String.graphemes |> Enum.reverse end
#Function<6.127694169/1 in :erl_eval.expr/5> 
```

Enter fullscreen mode Exit fullscreen mode

不能忘记函数名后面的点(`.`)，即使只有一个参数也不能省略括号`()`。

```
iex> "Hello" |> func.()
["o", "l", "l", "e", "H"] 
```

Enter fullscreen mode Exit fullscreen mode

但是，[正如官方文档所述的](https://hexdocs.pm/elixir/Kernel.html#%7C%3E/2-pitfalls)所示，即使不使用管道运算符，也需要点(`.`)和括号(`()`)。 是为了不要不小心忘记的提醒吧。

```
iex> func.("Hello")
["o", "l", "l", "e", "H"] 
```

Enter fullscreen mode Exit fullscreen mode

虽然有一点需要注意的地方，但是为了编写易懂的代码，管道运算符会有帮助吧。 顺便说一下，《[编程 Elixir](https://shop.ohmsha.co.jp/shopdetail/000000004675/) 》(原书《[Programming Elixir 1.6](https://pragprog.com/book/elixir16/programming-elixir-1-6) )的作者 Dave Thomas 似乎很喜欢这个管道运算符，是书籍的副标题

> `Functional`
> `|> Concurrent`
> `|> Pragmatic`
# 关于仙丹的笔记。

> 原文：<https://dev.to/antonrich/notes-on-elixir-1b48>

Elixir 中的错误消息大多是令人愉快的:

警告:通过管道进入函数调用时需要括号。例如:

```
foo 1 |> bar 2 |> baz 3 
```

是不明确的，应该写成

```
foo(1) |> bar(2) |> baz(3) 
```

* * *

[https://stack overflow . com/questions/32024156/how-do-I-raise-a-number-to-a-power-in-elixir](https://stackoverflow.com/questions/32024156/how-do-i-raise-a-number-to-a-power-in-elixir)

```
:math.pow(2,3) |> round 
```

[https://blog . use journal . com/elixir-scenic-snake-game-b 8616 B1 D7 ee 0](https://blog.usejournal.com/elixir-scenic-snake-game-b8616b1d7ee0)

还没试过那个教程。当我试用它时，我会写一篇关于它的反馈文章。

[https://robots . thoughtbot . com/is-elixir-a-scripting-language](https://robots.thoughtbot.com/is-elixir-a-scripting-language)

我真的很喜欢尽可能多地尝试使用这种语言，即使是在它没有被设计来处理的领域。显然，Ruby 和 Python 是编写快速而肮脏的脚本的更好选择。但是为什么不用长生不老药试试呢？

下面是另一个关于在 Elixir 中编写脚本的教程:

[https://www . grok-interactive . com/blog/UNIX-scripting-with-elixir/](https://www.grok-interactive.com/blog/unix-scripting-with-elixir/)

* * *

IEX(1)>有没有办法把 iex 的设置改成 iex >

从 iex 直接复制到 doctests 会很好。否则你只需要删除所有这些(1)

* * *

[https://prograils . com/courses/elixir/ch/anonymous-functions-overview](https://prograils.com/courses/elixir/ch/anonymous-functions-overview)

我在 iex
试过这个功能

```
add_one = fn x -> x + 1 end 
```

我犯的错误是我调用了没有点的函数。

不正确的
add_one(1)

用匿名函数更正:
add_one。(1)

* * *

```
warning: Enum.chunk/2 is deprecated. Use Enum.chunk_every/2 instead
  iex:9

[[1, 2, 3], [4, 5, 6], 
```

在我的例子中，我必须使用 Enum.chunk_every(list，3，3，:discard)

奇怪的字符' \a\b\t']没什么好担心的。只是一些 iex 的东西。

[https://stack overflow . com/questions/30037914/elixir-lists-interpreted-as-char-lists/30039460 # 30039460](https://stackoverflow.com/questions/30037914/elixir-lists-interpreted-as-char-lists/30039460#30039460)

* * *

我只是有一些愚蠢的错误。

```
 Enum.each pixel_map, fn {start, stop} -> do
      :egd.filledRectangle(image, start, stop, fill)
    end 
```

你能在这里发现一只虫子吗？

以下是我运行 iex -S mix 时得到的结果:

```
== Compilation error in file lib/identicon.ex ==
** (TokenMissingError) lib/identicon.ex:80: missing terminator: end (for "do" starting at line 1)

    HINT: it looks like the "do" on line 17 does not have a matching "end"

    (elixir) lib/kernel/parallel_compiler.ex:206: anonymous fn/4 in Kernel.ParallelCompiler.spawn_workers/6 
```

我读到这里，看第 17 行。大概有 5-10 分钟，我不知道到底哪里出了问题，因为我确定我已经得到了我需要的所有结果，这是真的。但是我在匿名函数后面有一个多余的 do。

在以“def do end”风格写出函数之后,“do”变成了自动的。

* * *

在您的项目中安装 egd

{:egd，github: "erlang/egd"}

运行 mix deps.get

* * *

理解问题。

[https://www.codewars.com/kata/price-of-mangoes/train/elixir](https://www.codewars.com/kata/price-of-mangoes/train/elixir)

* * *

[https://www.codewars.com/kata/triple-trouble-2/train/elixir](https://www.codewars.com/kata/triple-trouble-2/train/elixir)

这里我有一个想法。做相反的练习。

反转问题。拿一个单词，用这个单词做练习。

* * *

```
defmodule Garden do
  def rainAmmount(mm) do
      case mm do
        mm < 40 -> "You need to give your plant #{40 - mm}ml of water."
        _ -> "Your plant has had more than enough water for today."
      end
  end
end 
```

会给你:

```
** (CompileError) before_the_garden_dies.exs:4: cannot invoke remote function :erlang.</2 inside match
    (stdlib) lists.erl:1354: :lists.mapfoldl/3
    (stdlib) lists.erl:1354: :lists.mapfoldl/3 
```

要解决这个问题，请在 mm < 40 时写入
mm

[https://elixirforum . com/t/cannot-invoke-remote-function-Erlang-length-1-inside-match/9571/2](https://elixirforum.com/t/cannot-invoke-remote-function-erlang-length-1-inside-match/9571/2)

* * *

没有局部应用在酏剂或 currying。

但这看起来像是部分应用。

iex> upcase = &String.upcase/1

iex > total _ cost = &(& 1+& 2)
iex>total _ cost。(1, 2)

iex> check = &(true)
会给出一个错误。捕获运算符的 arity 不能为零。

理解&符号(捕获运算符):

[https://dock yard . com/blog/2016/08/05/understand-capture-operator-in-elixir](https://dockyard.com/blog/2016/08/05/understand-capture-operator-in-elixir)

[https://toranbillups . com/blog/archive/2018/09/28/and-and-functions-in-elixir/](https://toranbillups.com/blog/archive/2018/09/28/ampersand-and-functions-in-elixir/)

iex> check = fn ->真结束
iex >检查。()
真的

括号是可选的。但是我应该小心。
iex>add =&&1+&2
iex>add。(3, 3)

* * *

酏剂有一个 pin 操作符。这只是我稍后需要深入研究的东西。

* * *

就这些了，我相信以后我会有更多的笔记可以保存/分享。
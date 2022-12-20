# Elixir :获取堆栈跟踪

> 原文：<https://dev.to/gumi/elixir--3b8g>

本文以“[通过 Elixir 获取堆栈跟踪的](https://qiita.com/melpon/items/03884ef46412d38bbea0)”为基础进行了修改，整理了文章。 介绍如何取出堆栈跟踪信息，包括 Elixir 发生异常时以及普通情况。

# 发生例外时

发生异常时，当然需要堆栈跟踪信息来发出详细的日志。 异常时的堆栈跟踪信息可以通过 Elixir 1.7 具备的[`__STACKTRACE__/0`](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#__STACKTRACE__/0)得到。 在此之前的版本中，请使用[`System.stacktrace/0`](https://hexdocs.pm/elixir/System.html#stacktrace/0)。 但是，不建议在 v1.7 以上版本中使用(请参阅“[Elixir v1.7 发行版的](https://dev.to/gumi/elixir-v17-1ohl)”和“__STACKTRACE__ 结构”)。 例如，将以下代码写入`test.exs`中。 <sup id="fnref1">[1](#fn1)</sup>

```
defmodule X do
  def f() do
    throw 42
  end
  def g() do
    f() + 1
  end
  def h() do
    g() + 1
  end
end

try do
  X.h()
catch
  42 ->
    # IO.inspect System.stacktrace()  # v1.7より前に対応させたいとき
    IO.inspect __STACKTRACE__  # v1.7 以降
end 
```

Enter fullscreen mode Exit fullscreen mode

在命令行工具中键入`iex test.exs`后，将输出堆栈跟踪信息。

```
[
  {X, :f, 0, [file: 'test.exs', line: 3]},
  {X, :g, 0, [file: 'test.exs', line: 6]},
  {X, :h, 0, [file: 'test.exs', line: 9]},
  {:elixir_compiler_0, :__FILE__, 1, [file: 'test.exs', line: 14]},
  {:elixir_compiler, :dispatch, 4, [file: 'src/elixir_compiler.erl', line: 79]},
  {:elixir_compiler, :compile, 3, [file: 'src/elixir_compiler.erl', line: 63]},
  {:elixir_lexical, :run, 2, [file: 'src/elixir_lexical.erl', line: 17]},
  {:elixir_compiler, :quoted, 2, [file: 'src/elixir_compiler.erl', line: 23]}
] 
```

Enter fullscreen mode Exit fullscreen mode

如果将`__STACKTRACE__/0`的返回值进一步传递给[`Exception.format_stacktrace/1`](https://hexdocs.pm/elixir/Exception.html#format_stacktrace/1)，则输出被整形。

```
try do
  X.h()
catch
  42 ->
    # IO.inspect __STACKTRACE__
    IO.puts Exception.format_stacktrace(__STACKTRACE__)
end 
```

Enter fullscreen mode Exit fullscreen mode

```
test.exs:3: X.f/0
test.exs:6: X.g/0
test.exs:9: X.h/0
test.exs:14: (file)
(elixir) src/elixir_compiler.erl:79: :elixir_compiler.dispatch/4
(elixir) src/elixir_compiler.erl:63: :elixir_compiler.compile/3
(elixir) src/elixir_lexical.erl:17: :elixir_lexical.run/2
(elixir) src/elixir_compiler.erl:23: :elixir_compiler.quoted/2 
```

Enter fullscreen mode Exit fullscreen mode

# 通常时

即使没有发生异常，也有想确认函数是从哪里被调用的事情。 例如，在通用处理的函数内通过`Logger.info`输出日志时，如果有堆栈跟踪的信息，问题应该会很快被查明吧。

通常情况下，获取堆栈跟踪信息的是[`Process.info/2`](https://hexdocs.pm/elixir/Process.html#info/2)。 按如下方式为函数赋予当前进程:

```
defmodule X do
  def f() do
    {:current_stacktrace, t} = Process.info(self(), :current_stacktrace)
    IO.inspect t
    0
  end
  def g() do
    f() + 1
  end
  def h() do
    g() + 1
  end
end

X.h() + 1 
```

Enter fullscreen mode Exit fullscreen mode

```
[
  {Process, :info, 2, [file: 'lib/process.ex', line: 767]},
  {X, :f, 0, [file: 'test.exs', line: 3]},
  {X, :g, 0, [file: 'test.exs', line: 8]},
  {X, :h, 0, [file: 'test.exs', line: 11]},
  {:elixir_compiler_0, :__FILE__, 1, [file: 'test.exs', line: 15]},
  {:elixir_compiler, :dispatch, 4, [file: 'src/elixir_compiler.erl', line: 79]},
  {:elixir_compiler, :compile, 3, [file: 'src/elixir_compiler.erl', line: 63]}
] 
```

Enter fullscreen mode Exit fullscreen mode

和例外时一样，可以通过`Exception.format_stacktrace/1`对输出进行整形。

```
def f() do
  {:current_stacktrace, t} = Process.info(self(), :current_stacktrace)
  # IO.inspect t
  IO.puts Exception.format_stacktrace(Enum.drop(t, 1))
  0
end 
```

Enter fullscreen mode Exit fullscreen mode

将从`Process.info/2`中得到的值传递给[`Enum.drop/2`](https://hexdocs.pm/elixir/Enum.html#drop/2)是因为想去除`Process.info/2`的呼叫信息。

```
test.exs:3: X.f/0
test.exs:8: X.g/0
test.exs:11: X.h/0
test.exs:15: (file)
(elixir) src/elixir_compiler.erl:79: :elixir_compiler.dispatch/4
(elixir) src/elixir_compiler.erl:63: :elixir_compiler.compile/3 
```

Enter fullscreen mode Exit fullscreen mode

[`Exception.format_stacktrace/1`的文档](https://hexdocs.pm/elixir/Exception.html#format_stacktrace/1)在没有自变量时，写着返回从[`Process.info/2`](https://hexdocs.pm/elixir/Process.html#info/2)中得到的当前进程的堆栈跟踪信息。 但是，在这种情况下，调用方不包含函数信息。

```
def f() do
  # {:current_stacktrace, t} = Process.info(self(), :current_stacktrace)
  # IO.puts Exception.format_stacktrace(Enum.drop(t, 1))
  IO.puts Exception.format_stacktrace()
  0
end 
```

Enter fullscreen mode Exit fullscreen mode

如下所示，漏掉了`X.f/0`的信息。

```
test.exs:7: X.g/0
test.exs:10: X.h/0
test.exs:14: (file)
(elixir) src/elixir_compiler.erl:79: :elixir_compiler.dispatch/4 
```

Enter fullscreen mode Exit fullscreen mode

[看`Exception.format_stacktrace/1`的代码](https://github.com/elixir-lang/elixir/blob/v1.8.0/lib/elixir/lib/exception.ex#L564)，就会发现在`Enum.drop/2`中删除了前 3 个扫描信息。

前两个是`Process.info/2`和`Exception.format_stacktrace/1`，所以我知道我会省略。 但是，不知为什么还有一个，调用方函数(`X.f/0`)的信息也被删除了。 如果有这个比较好的话，可以装载一个虚拟的堆栈信息调用。

```
def format_stacktrace() do  # スタック情報をひとつ積む
  Exception.format_stacktrace() <> ""
end
def f() do
  # IO.puts Exception.format_stacktrace()
  IO.puts format_stacktrace()
  0
end 
```

Enter fullscreen mode Exit fullscreen mode

```
test.exs:6: X.f/0
test.exs:10: X.g/0
test.exs:13: X.h/0
test.exs:17: (file) 
```

Enter fullscreen mode Exit fullscreen mode

另外，堆栈跟踪的信息好像只取得了 7 个。 本来就很少的 3 个信息被删除，可以说相当浪费。 如何取得比较好，好像稍微考虑一下比较好。

* * *

1.  像`f() + 1`那样加上`+1`是为了妨碍末尾调用优化。 详情请参阅“[Elixir 的堆栈跟踪和末尾调用优化](http://qiita.com/melpon/items/0eedc19d6aece782d13d)”。
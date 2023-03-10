# 测量酏剂中的堆叠大小

> 原文：<https://dev.to/bakenator/measuring-stack-size-in-elixir-10b1>

# 为什么测量？

在 Elixir 中有很多关于尾部调用优化的好文章。这使得 Elixir 可以永远递归调用函数，而不会使堆栈过载。

以下是其中一些例子:[https://dev.to/search?q=elixir%20tail](https://dev.to/search?q=elixir%20tail)

虽然大多数文章都说 Elixir 优化了递归，但我想亲眼看看。

# 代码

下面是看到一个优化递归函数的最少代码。它只是打印出当前进程的信息，然后返回一个新的数字。

```
defmodule MyApp.Example do
  def recursiveFunc(number \\ 1) do
    proc_info = Process.info(self()) 
    IO.inspect proc_info
    recursiveFunc(number + 1)
  end
end 
```

如果该功能在 iex 中运行，它将在终端中非常快速地打印进程信息。在信息对象中，它将列出当前 stack_size 和 heap_size。这是我在我的机器上得到的。

```
total_heap_size: 15143,
heap_size: 4185,
stack_size: 46, 
```

我们可以看到，即使它一遍又一遍地调用函数，stack_size 也应该保持不变。heap_size 将会变动，但不会增加太多。

# 非优化示例

下面是一些不会被自动优化的代码，因为递归函数调用不是函数
中最后评估的内容

```
defmodule MyApp.Example do
  def nonOptimizedFunc(number \\ 1) do
    proc_info = Process.info(self()) 
    IO.inspect proc_info
    1 + nonOptimizedFunc(number + 1)
  end
end 
```

运行时，正如我们所预期的那样，每次迭代栈的大小都会增加 1。随着每次新的迭代向堆中添加一个 int，heap_size 也会快速增长。

# 这会崩溃吗？

这个非优化代码最有趣的地方是堆栈可以有多大。

在 JavaScript 领域，堆栈限制可以很快达到。

在我的 Mac 上运行未优化的示例，我在一分钟内获得了 100k 的 stack_size，并且这个过程一直在持续！

这是因为默认情况下，Elixir 进程没有最大堆栈大小。它将继续增加堆栈和堆，直到进程耗尽内存。默认情况下，不管需要多少内存，都允许一个 Elixir 进程，直到它使 BEAM VM 崩溃。

如果我们想阻止这种情况发生，我们可以在我们的代码中添加下面一行代码
`Process.flag(:max_heap_size, 100_000)`
一旦这个进程占用了太多我们喜欢的内存，它就会终止这个进程，但是在整个应用程序崩溃之前。

感谢阅读！用 Elixir 编码很有趣！
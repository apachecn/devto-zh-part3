# 递归，尾部调用优化和递归。

> 原文：<https://dev.to/edisonywh/recursion-tail-call-optimization-and-recursion-2enh>

最近，我想尝试一下函数式语言，尝试一种不同的思维模式，我决定选择`Elixir`，因为它在语法上与`Ruby`非常相似。在我学习的时候，我发现了一些东西。

> **TLDR** :
> 
> *   长生不老药没有循环
> *   递归对调用堆栈进行了额外的调用(这就是为什么如果您忘记了基本情况，堆栈会溢出)
> *   尾部调用优化是一个优化递归的编译器特性(如果它做的最后一件事是调用它自己)！

## 仙丹中没有循环的概念。

是的，没有你的`for`循环或者`.each`。起初这是一个有点奇怪的概念，但让我们看看有什么替代方案。

如果你不能使用迭代方法来迭代一个循环，你如何遍历一个列表？*递归*。

> 简要回顾:递归是当一个函数调用自己，[像这样](https://dev.to/edisonywh/recursion-tail-call-optimization-and-recursion-2enh)

一个 Ruby 的例子:

```
def traverse(tail)
  head = tail.shift # shift takes out the first element
  puts "Element: #{head}"
  return if tail.empty?
  traverse(tail)
end 
```

Enter fullscreen mode Exit fullscreen mode

仙丹的例子:

```
defmodule Recursion do
  def traverse([]), do: IO.puts "Done"
  def traverse([head | tail]) do
    IO.puts "Element: #{head}"
    traverse(tail)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

递归遍历而不是迭代遍历很有趣，是吗？

## 大数！

接下来，我们来看看当数量很大的时候会发生什么！

*红宝石* :

```
large_array = Array.new(500_000).fill { |i| i + 1 }

traverse(large_array)
# Element: 1
# Element: ...
# Element: ...
# Element: 10918
#=> stack level too deep (SystemStackError) 
```

Enter fullscreen mode Exit fullscreen mode

一声`SystemStackError`就死机了！哇，是不是说`recursion`不好？这是否意味着`recursion`不好？意思是`recursion`不好吗？那是不是意味着`recursion`是...好吧，在我们发疯之前，让我们看看仙丹是如何处理它的。

*仙丹* :

```
start = 1
increment = 1
length = 500000
list = Stream.iterate(start, &(&1 + increment)) |> Enum.take(length)

Recursion.traverse(list)
# Element: 1
# Element: ...
# Element: ...
# Element: 500000
# Done
#=> :ok 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到，仙丹毫不费力地处理所有的**五十万**递归。但是为什么在`Elixir`有效而在`Ruby`无效呢？

因为`Tail Call Optimization`！

## 什么是尾调用优化？

**尾部调用优化(TCO)** 是一个编译器特性，如果一个函数做的最后一件事是调用自己，编译器会自动优化*堆栈*。

> *但是栈是什么呢？* *还有你说的‘优化堆栈’是什么意思？*

Stack 是一种采用后进先出思想的数据结构，可以把它看作一个固定的数组，但是你只能从一端插入，从同一端取出。我们在这里具体讨论的是调用堆栈。

为了更好地理解这一点，我们需要首先理解调用栈是如何工作的。

当您*调用*一个函数时，它被添加到调用堆栈中，当它执行完毕时，它被弹出堆栈，就像这样:

[![Call Stack Visualization](img/939b0679ee7cc92c61df09a95431c3c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X_WDYupJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vm6ringb5e2biqugrctt.gif)

当你有一个递归函数调用时，它基本上每次都会把自己添加到调用栈中。如果您忘记了您的基本情况/有太大的调用堆栈，它会抛出一个堆栈溢出错误！(我们之前看到的)，像这样。

[![Recursion Stack Visualization](img/eb1771064ebb518cf9f4a6e3fff55284.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lem2qDG3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s209q55x1gwq5usnjfo0.gif)

所有 TCO 的意思就是，编译器看到函数最后一次调用是在调用自己，而不是把函数调用加入调用栈，而是做一个`goto`，基本上重新启动同一个函数调用，需要 **O(1)** 而不是 **O(n)** 空间(这个我们后面会证明)。

这是降低总拥有成本后的情况:

[![TCO Call Stack Visualization](img/853d5b4b0cf3e77219b6edeedf32804a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vWdFV7Sz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qfm7l9u3vk3zx10r3l42.gif)

## Ruby 中有这个吗？

我们确实有！然而，TCO 在默认情况下是不启用的，所以您必须在运行时传递编译选项，您可以这样做:

```
RubyVM::InstructionSequence.compile_option = {
  tailcall_optimization: true,
  trace_instruction: false
}

require './tail_recursion.rb'

large_array = Array.new(500_000).fill { |i| i + 1 }
traverse(large_array)

# Element: 1
# Element: ...
# Element: ...
#=> Element: 500000 
```

Enter fullscreen mode Exit fullscreen mode

如果你必须用一个标志来启用它，这并不是很有用，但是如果你需要它，它就在那里！

## 证明？

支持你。在 RubyLand 中，我们可以通过调用`Kernel#caller`来检查当前的调用堆栈。让我们`.count`！

像这样修改我们的代码:

```
def traverse(tail)
  head = list.shift
  puts "Element: #{head}, Stack Count: #{caller.count}" #=> new line
  return if tail.empty?
  traverse(tail)
end

# Normal ruby
traverse(large_array)
# Element: 1,           Stack Count: 1
# Element: ...,         Stack Count: ...
# Element: ...,         Stack Count: ...
# Element: 10918,           Stack Count: 10918
# => stack level too deep (SystemStackError)

# TCO-enabled
traverse(large_array)
# Element: 1,           Stack Count: 1
# Element: ...,         Stack Count: 1
# Element: ...,         Stack Count: 1
# Element: 500000,          Stack Count: 1 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

好了，今天就到这里吧！虽然我想学长生不老药，但是却学了一些关于编译器的东西，这很酷！

也看看我以前的文章:

*   [![edisonywh image](img/388d2736cc1db9d9adde421c4812f753.png)](/edisonywh) [## Ruby has no class method

    ### Edison Yap November 9th 185 minutes to read

    #ruby #rails](/edisonywh/ruby-has-no-class-methods-39l5)
*   [![edisonywh image](img/388d2736cc1db9d9adde421c4812f753.png)](/edisonywh) 
     [## 我的红宝石之旅：连接事物

    ### 爱迪生 11 月 3 日 184 分钟阅读

    【红宝石】
    【编程】
    【轨道】](/edisonywh/my-ruby-journey-hooking-things-up--feg) 

## 归属

[俄罗斯娃娃封面图片来源](https://www.vecteezy.com)
# ruby–模式匹配–第二印象

> 原文：<https://dev.to/katafrakt/ruby-pattern-matching-second-impressions-3hkk>

自从 3 年前我在 Ruby 上发表了[一篇探索模式匹配的文章以来，我已经不止一次被称为“模式匹配专家”。所以很明显，当我得知 PM 不可避免地要来到 Ruby core 的时候，我很好奇想去看看。](http://katafrakt.me/2016/02/13/quest-for-pattern-matching-in-ruby/)[第一印象](https://medium.com/@baweaver/ruby-2-7-pattern-matching-first-impressions-cdb93c6246e6)已经发表了，所以这是“第二印象”，从我的角度来看。

**抬头**:非常主观。

我主要是通过[原红矿票](https://bugs.ruby-lang.org/issues/14912)中提供的例子来判断，比如:

```
class Array
  alias deconstruct itself
end

case [1, 2, 3, d: 4, e: 5, f: 6]
in a, *b, c, d:, e: Integer | Float => i, **f
  p a #=> 1
  p b #=> [2]
  p c #=> 3
  p d #=> 4
  p i #=> 5
  p f #=> {f: 6}
  e   #=> NameError
end 
```

首先，代码很难看，让人很难理解。它看起来像是被添加到了一种不是为支持模式匹配而设计的语言之上(事实正是如此)。从长远来看，当人们习惯了这一点后，这可能并不重要——但这就是，在第二轮印象中。

解构(为什么叫解构？)看起来不错，但我会删除管道的东西。而不是`e: Integer | Float => i`(这非常模糊——是`e: (Integer | Float) => i`还是`((e: Integer) | (Float)) => i`，还是别的什么？)最好能够像小马一样定义类型联合[。例如:](https://tutorial.ponylang.io/types/type-expressions.html) 

```
number = typeunion(Integer | Float) # hypothetic keyword typeunion
case n
in number
  puts "I'm a number"
in String
  puts "I'm string"
end 
```

除此之外，这很好，特别是对于从哈希中获取东西。

但这个提议最重要的问题可能是，它不允许我用不同类型的签名定义多个函数，由模式匹配来控制。这是我在使用 Ruby 的日常工作中最缺少的，尽管它在 Erlang 或 Elixir 中可用。为了让你体验一下我在说什么

```
class Writer
  def write_five_times(text => String)
    puts text * 5
  end

  def write_five_times(text => Integer)
    puts text.to_s * 5
  end

  def write_five_times(text => Object)
    raise NotImplementedError
  end
end 
```

当然，要实现上面代码清单中的内容，需要进行更大更复杂的更改。基本上这就像给 Ruby 引入合适的类型。它需要允许在一个类中多次定义一个方法，但不需要隐藏以前的定义。我不认为 Ruby 会走这条路，但这将在几个地方显著地清理我的代码。

我还意识到，在研究[诺艾迪](https://github.com/katafrakt/noaidi)时——我的模式匹配实现。我真的不想在代码中的某个地方使用简单的模式匹配，因为我可以在 Ruby 中使用传统的`case`来处理大多数情况。但是我希望能够编写行为有点像 Elixir 中的模块。

这在诺艾迪正成为可能。我有一个实验分支可以实现这一点，我希望有一天我能够完成它。这样的模块应该是这样的:

```
module Math
  extend Noaidi::DSL

  fun(:fib, 0..1)    { 1 }
  fun(:fib, Integer) { |n| add(fib(n-1), fib(n-2)) }
  fun(:fib, Object)  { raise NotImplementedError }
  funp(:add, Integer, Integer) { |a,b| a + b }
end

Math.fib(1)      #=> 1
Math.fib(20)     #=> 10946
Math.fib("test") #=> NotImplementedError
Math.add(1,3)    #=> NoMethodError (private method `add' called for Math:Module) 
```

我有点失望。探索还没有结束。

* * *

这也发布在我的个人博客上。
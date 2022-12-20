# 红宝石 vs 长生不老药

> 原文：<https://dev.to/joshhadik/ruby-vs-elixir---fizzbuzz-34la>

最近，作为日常工作的一部分，我一直在解决许多常见的面试问题，这是我每天都在做的事情，迫使我不断学习，磨练自己的技能。我决定不仅仅用我的母语 Ruby 来解决每个问题，而是将每个问题解决两次，一次用 Ruby，一次用 Elixir。你可以在这里读到更多关于我为什么选择这样做[的信息。](https://dev.to/joshhadik/are-ruby-and-elixir-the-perfect-yin-and-yang-425l)

我想，当我这么做的时候，写下我注意到的两者之间的一些关键差异可能会很有趣。这就是我要说的，一些关键差异的快速分解，而不是我如何找到解决方案的逐步指南。

在这些例子中，我尽最大努力以最真实的形式使用每种语言，这意味着在 Ruby 中，我遵循严格的面向对象方法，主要依赖于对象、变异、实例变量和迭代，而在 Elixir 中，我遵循严格的函数方法，主要依赖于函数、管道、模式匹配和递归。

我还尽了最大努力，通过使用相似的函数名、变量名和一般的操作顺序，使两种解决方案读起来尽可能相似。

# 红宝石 FizzBuzz

```
# ruby
class FizzBuzz
  def initialize(max)
    @max = max
    @fizz_buzz_array = []
  end

  def play
    populate_fizz_buzz_array
    display_fizz_buzz
  end

  private

  def populate_fizz_buzz_array
    1.upto(@max).each do |num|
      value = get_fizz_buzz_value(num)
      add_value_to_fizz_buzz_array(value)
    end
  end

  def get_fizz_buzz_value(num)
    if num % 15 == 0
      "FizzBuzz"
    elsif num % 3 == 0
      "Fizz"
    elsif num % 5 == 0
      "Buzz"
    else
      num.to_s
    end
  end

  def add_value_to_fizz_buzz_array(value)
    @fizz_buzz_array << value
  end

  def display_fizz_buzz
    puts @fizz_buzz_array
  end
end

## USAGE

fizzbuzz = FizzBuzz.new(15)
fizzbuzz.play

# 1
# 2
# Fizz
# ... 
```

# 药剂嘶嘶作响

```
# elixir
defmodule FizzBuzz do
  def play(max) do
    max
    |> create_fizz_buzz_list()
    |> display_fizz_buzz()
  end

  defp create_fizz_buzz_list(list, 0), do: list
  defp create_fizz_buzz_list(list \\ [], num) do
    num
    |> get_fizz_buzz_value()
    |> add_value_to_list(list)
    |> create_fizz_buzz_list(num - 1)
  end

  defp get_fizz_buzz_value(num) do
    cond do
      rem(num, 15) == 0 -> "FizzBuzz"
      rem(num, 3) == 0 -> "Fizz"
      rem(num, 5) == 0 -> "Buzz"
      true -> to_string(num)
    end
  end

  defp add_value_to_list(value, list) do
    [value | list]
  end

  defp display_fizz_buzz([]), do: :ok
  defp display_fizz_buzz([head | tail]) do
    IO.puts(head)
    display_fizz_buzz(tail)
  end
end

## USAGE

FizzBuzz.play(15)

# 1
# 2
# Fizz
# ... 
```

# 我注意到的事情

### **1。Elixir 鼓励声明式编程**

看看“创建 _ 嘶嘶 _ 嗡嗡 _ 列表”和“填充 _ 嘶嘶 _ 嗡嗡 _ 阵列”

```
# ruby
def populate_fizz_buzz_array
  1.upto(@max).each do |num|
    value = get_fizz_buzz_value(num)
    add_value_to_fizz_buzz_array(value)
  end
end 
```

```
# elixir
defp create_fizz_buzz_list(list \\ [], num) do
  num
  |> get_fizz_buzz_value()
  |> add_value_to_list(list)
  |> create_fizz_buzz_list(num - 1)
end 
```

在 Ruby 版本中，除非你是这种语言的专家，否则你几乎需要花一分钟来检查代码，仅仅是为了弄清楚发生了什么，而 Elixir 版本读起来几乎像一本书(尽管最后一行可能有点混乱。)

当然，我本可以将 Ruby 写得更具声明性，但重点是遵循常见的模式，并按照我在现实世界中实际使用的方式编写代码，我认为 Elixir 在鼓励使用更具声明性的编码风格方面做得更好。

### **2。药剂鼓励更好的抽象**

看看我将 fizz buzz 值添加到集合中的函数/方法:

```
# ruby
def add_value_to_fizz_buzz_array(value)
  @fizz_buzz_array << value
end 
```

```
# elixir
defp add_value_to_list(value, list) do
  [value | list]
end 
```

请注意，在 Ruby 中，我把它称为“添加值到嘶嘶声阵列”，但在 Elixir 中，我去掉了“嘶嘶声”，而只把它称为“添加值到列表”这是故意的。在 Ruby 中，方法调用是将值 **only** 添加到预定义的 fizz_buzz_array，然而，Elixir 函数更通用一些，它将元素添加到调用该函数的任何列表中。

这意味着 Elixir 函数比 Ruby 版本灵活得多，可以很容易地抽象到其他模块中，并在整个代码中重用。

同样，我知道我本可以用更函数化的方法编写 Ruby，并实现同样的行为，但事实是，在 Ruby 中，进行更改的标准方式是完全按照我所做的那样，通过使用方法在内部操纵实例状态。

### **3。仙丹“倒着用”效果更好**

因为 Elixir 中的列表实际上是链表，而不是像 Ruby 中那样的索引数组，所以在与它们交互时，我必须采用不同的方法。

在 Ruby 中，将元素添加到数组的 *end* 既便宜又容易，所以这已经成为向数组添加元素的实际方式，这正是我在这个例子中所做的。

另一方面，在 Elixir 中，向列表的开头添加元素更容易，需要的处理能力也少得多，这主要是由于链表的工作方式。

仅此一点并不值得注意，但我发现有趣的是它改变了我研究数字的整个方向。在 Ruby 中，因为我添加到数组的末尾，所以从 1 开始迭代到 15 最有意义:

```
# ruby
1.upto(@max).each do |num|
  # ...
end 
```

然而，在 Elixir 中，因为我添加到了列表的开头，所以从 15 开始递归到 1 更有意义。

```
# elixir
create_fizz_buzz_list(num - 1) 
```

### **4。Ruby 更容易引起意想不到的副作用**

在像 Elixir 这样的函数式语言中，如果某样东西工作一次，它就会工作两次。输入和输出之间几乎总是一对一的关系。另一方面，对于面向对象的语言，你必须更加小心。

请注意，我从未清除 Ruby 中的@fizz_buzz_array 变量，只是向它添加了新元素。如果我试着“玩”一次 FizzBuzz 的某个特定实例，它就像魔咒一样管用。但是如果我尝试两次播放同一个实例会发生什么呢？

```
# ruby
fizzbuzz = FizzBuzz.new(3)
fizzbuzz.play

# 1
# 2
# Fizz

fizzbuzz.play

# 1
# 2
# Fizz
# 1
# 2
# Fizz 
```

这是一个你可能不会想到要测试的小故障，除非你对 Ruby 和面向对象编程模式有深刻的理解，否则你甚至可能意识不到它正在发生。这个故障很容易解决。我可以在每次填充数组时清除它，或者我可以将整个“populate_fizz_buzz_array”方法调用从 play 函数移到 initialize 函数，这样数组只被填充一次，但是我决定将它作为一个例子，说明在 OOP 中，不太理想的副作用常常很容易被忽略。

# 我不讨厌红宝石

当我列出“我注意到的事情”清单时，已经写完了所有的代码。当我回头看代码时，我试图写下我所注意到的真实的意识流。我知道这些观点中有很多看起来像是我真的在炒作长生不老药，在嘲笑 Ruby，但这并不是我的本意，这只是我比较代码时突出出来的诚实的列表。

如果看起来我对 Elixir 有强烈的偏见，我想那是因为 Elixir 是我最近才开始使用的令人兴奋的新语言，而 Ruby 是我使用了多年的几乎令人厌烦的旧语言。灵丹妙药就是“另一边的草更绿”，所以现在我知道我倾向于只看到好的一面，而不是坏的一面。我确信一旦我对长生不老药更加熟悉，我会对两者的好与坏有一个更加平衡的看法，但是现在，我确实发现自己被长生不老药的工作方式所吸引。

# 你怎么看？

关于这两种解决方案之间的区别，您还有什么特别注意的吗？你喜欢哪个版本？

# 感谢阅读！

如果你觉得这篇文章很有趣或者内容丰富，并且想看更多类似的文章，请告诉我！请随时告诉我你认为什么是我下一篇 Ruby vs Elixir 文章的好主题！(斐波那契？阶乘？还有别的吗？)
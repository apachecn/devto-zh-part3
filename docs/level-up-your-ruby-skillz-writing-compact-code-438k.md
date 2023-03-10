# 提高您的 Ruby Skillz:编写紧凑代码

> 原文：<https://dev.to/molly/level-up-your-ruby-skillz-writing-compact-code-438k>

我们都有过这样的经历，我们发出一个我们觉得超级紧凑和令人敬畏的拉动请求(PR)。它被盖了“批准”的章，但是有一个问题。我们的代码实际上没有我们想象的那么严密。我们公开了我们的公共关系，听取了一堆非常好的建议，告诉我们如何更严格地执行我们的代码。

这在你刚开始的时候是很常见的，事实上这也是我学习这些技巧的原因。感谢许多优秀的高级开发人员从一开始就阅读并评论我的代码，我已经学会了如何编写非常简洁的代码。但我会让你知道一个秘密，它永远不会结束。我已经这样做了 7 年，我仍然能得到关于如何写更好的代码的建议。

下面是一些常见的方法，你可以用一些[语法糖](https://en.wikipedia.org/wiki/Syntactic_sugar)来强化基本的 Ruby 逻辑。语法糖是编程语言(如 Ruby)中的语法，旨在使内容更容易阅读或表达。

*   [布尔/条件](#booleans)
*   [变量赋值](#assignment)
*   [保护条款](#guard_clause)
*   [调用方法](#calling_methods)
*   [安全导航仪](#safety_navigator)
*   [散列和数组](#hashes_arrays)

# 布尔/条件句

### 三元运算符

为了设置变量，让我们从一些基本的布尔逻辑开始。如果`y`是偶数，我们希望设置`x`等于 1，如果`y`是奇数，我们希望设置`x`等于 2。这是我们在启动 Ruby 时被教导的方法。

```
if y.even?
  x = 1
else
  x = 2
end 
```

Enter fullscreen mode Exit fullscreen mode

上面给出了两个赋值语句。这意味着如果我们必须改变变量 x，我们必须在两个地方改变，这是不理想的。简化代码的一种方法是这样做:

```
x = if y.even?
  1
else 
  2
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们只做一次`x`赋值，这稍微简单一点。但即使有了以上内容，我们也要占用 5 行来完成。让我们看看如何把它简化到一行。

```
x = y.even? ? 1 : 2 
```

Enter fullscreen mode Exit fullscreen mode

`?`被称为[三元运算符](https://www.w3resource.com/ruby/ruby-ternary-operator.php)。三元运算符逻辑使用“(条件)？(真返回值):(假返回值)”语句来缩短 if/else 结构。它首先计算一个表达式的真值或假值，然后根据计算结果执行两个给定语句中的一个。语法如下:

```
test-expression ? if-true-expression : if-false-expression 
```

Enter fullscreen mode Exit fullscreen mode

这对于加强逻辑语句非常有用。

### 内联布尔/条件

除了巧妙的三元运算符之外，让我们看看另一种将布尔逻辑压缩成一行的方法。这里有一个基本的方法和一些布尔逻辑。

```
def say_number_type(number)
  if number.even?
    puts "I am even!"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

为了更简洁，我们可以这样写布尔值:

```
def say_number_type(number)
  puts "I am even!" if number.even?
end 
```

Enter fullscreen mode Exit fullscreen mode

同样可以用`unless`
来完成

```
def say_number_type(number)
  puts "I am even!" unless number.odd?
end 
```

Enter fullscreen mode Exit fullscreen mode

注意:使用内联布尔或条件有时被称为使用“修饰语语法”。

# 变量赋值

假设我们有这样一个场景，我们想根据另一个变量是否存在来给它赋值。例如，我们下面有变量`a`和`b`。如果`a`存在(不为零)，我们希望设置`x`等于`a`。如果`a`为零，我们希望设置`x`等于`b`。我们可以这样做。

```
if a.nil?
  x = b
else 
  x = a
end 
```

Enter fullscreen mode Exit fullscreen mode

或者，根据我们在上面学到的知识，我们可以做

```
x = a.nil? ? b : a 
```

Enter fullscreen mode Exit fullscreen mode

然而，在处理可能的零值时，我们可以通过使用管道操作符使其更加简洁，如下所示:

```
x = a || b 
```

Enter fullscreen mode Exit fullscreen mode

如果`a`不为零，赋值语句将把`a`的值赋给`x`。如果`a`为零，它会将`b`的值赋给 x。

```
irb(main)> a = 1
=> 1
irb(main)> b = 2
=> 2
irb(main)> x = a || b
=> 1

irb(main)> a = nil
=> nil
irb(main)> x = a || b
=> 2 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在两个变量之间使用管道运算符，也可以在等号两边使用。假设我们想让`x`等于`a`，但前提是`x`为零。如果`x`不为零，我们希望保留`x`的值。为了实现这一点，我们将做:

```
x ||= a 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个控制台示例，展示了这是如何工作的:

```
irb(main)> x = 1
=> 1
irb(main)> a = 2
=> 2
irb(main)> x ||= a
=> 1
irb(main)> x = nil
=> nil
irb(main)> x ||= a
=> 2 
```

Enter fullscreen mode Exit fullscreen mode

这是告诉 ruby，只有当`x`为零时，才将`a`的值赋给`x`。当你预计会有违约，但又不能指望它会出现时，这可能会有所帮助。假设你有下面的方法

```
def add_one(number)
  number + 1
end 
```

Enter fullscreen mode Exit fullscreen mode

如果`number`为零，那么当我们试图给它加 1 时，我们将得到一个`NoMethodError`。我们可以通过这样做来避免产生错误

```
def add_one(number)
  number ||= 0
  number + 1
end 
```

Enter fullscreen mode Exit fullscreen mode

`number ||= 0`将确保如果没有定义`number`,我们的方法不会产生错误，因为它将被设置为 0。如果你想更进一步，你可以把赋值操作符和参数放在一起！🤯

```
def add_one(number = 0)
  number + 1
end 
```

Enter fullscreen mode Exit fullscreen mode

当你调用这个方法时，如果你不给它传递一个数字，ruby 会把数字设置为 0。

```
irb(main)>  add_one
=> 1
irb(main)>  add_one(1)
=> 2 
```

Enter fullscreen mode Exit fullscreen mode

# 守卫条款

如果满足某些条件，那么[保护条款](https://medium.com/the-code-review/en-garde-how-you-can-refactor-nested-ifs-with-guard-clauses-in-ruby-1ff728b7a0cd)就是提前退出一个方法。保护子句可以帮助您精简方法逻辑并编写更优化的代码。让我们看一个例子，看看我们什么时候会用到它。比方说你有下面的方法`tester`

```
def tester(arg)
  if arg == 1
    # logic
  else
   'error'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，如果我们的参数`arg`不等于 1，那么我们将返回一个表示“错误”的字符串。我们可以通过使用如下所示的 guard 子句来简化这个`tester`方法。

```
def tester(arg)
 return 'error' unless arg == 1 # Guard Clause
 # logic
end 
```

Enter fullscreen mode Exit fullscreen mode

当我们开始我们的方法时，我们立即检查`arg`的值是否等于 1。如果是的话，我们立即返回。因为我们知道我们想要做什么，所以不需要在方法中到处摸索。这在优化代码时尤其有用。例如，留意这样的情况:

```
def tester(arg)
  user = User.find(123)
  event = Event.find(123)

  if arg == 1
    # logic
  else
    'error'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们甚至在到达 if/else 布尔之前就在收集数据和执行逻辑。最后，如果`arg`不等于 1，那么我们只是浪费了大量的时间和资源来收集我们不需要的数据。通过在方法的第一行检查您的条件，guard 子句可以确保这些资源不会被浪费。

我想补充的另一个小细节是，如果你想使用一个 guard 子句根据一个条件返回 nil，你可以这样做:

```
def tester(arg)
  return unless arg == 1
  # logic
end 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，单独使用`return`就好像你写了`return nil`一样。

```
def tester(arg)
  # nil is unnecessary 
  return nil unless arg == 1
  # logic
end 
```

Enter fullscreen mode Exit fullscreen mode

然而，在 Ruby 中，这种情况下不需要显式定义 nil。第一个例子，`return unless arg == 1`是你想如何写它，以及你将如何看待别人写的它。

# 调用方法

假设您正在处理一组响应某个方法的对象，并且您想要使用类似于`map`的方法来收集该方法的结果。通常，你会这样做:

```
a = [1, 2, 3, 4]
string_list = a.map{|obj| obj.to_s}
# string_list = ["1", "2", "3", "4"] 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用一点语法糖来进一步简化这一过程，但仍然会得到相同的结果。

```
string_list = a.map(&:to_s)
# string_list = ["1", "2", "3", "4"] 
```

Enter fullscreen mode Exit fullscreen mode

我们的对象集(在本例中是整数)响应的任何方法都可以使用`&:method`语法在每个对象上调用。如果你想了解 Ruby 到底在做什么，我建议你读一读这篇[博客文章](https://www.brianstorti.com/understanding-ruby-idiom-map-with-symbol/)。

# 安全导航仪

你们中有多少人以前写过这样的代码？✋我当然有！

```
def friends_name(user)
  return unless user.present?
  friend = user.friend
  if friend.present?
    friend.name
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这里进行了大量的存在检查，只是为了确保我们永远不会在一个 nil 对象上调用一个方法并得到可怕的`# NoMethodError`。我们可以使用 Ruby 的安全导航器，而不是做所有这些检查！如果在一个 nil 对象上调用这个方法，[安全导航器](https://rubyinrails.com/2017/11/17/safe-navigation-operator-ampersand-dot-in-ruby/)将简单地返回 nil。这意味着我们可以将我们的方法简化为:

```
def friends_name(user)
  user&.friend&.name
end 
```

Enter fullscreen mode Exit fullscreen mode

如果该链中的任何对象丢失，我们将简单地返回零。这里是另一个简单的控制台示例，希望有助于进一步阐明安全导航器的工作原理。

```
irb> [].last.to_s
=> ""
irb> [].last.even?
NoMethodError: undefined method `even?' for nil:NilClass
    from (irb):3
    from /usr/bin/irb:11:in `<main>'
irb> [].last&.even?
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

# 散列和数组

### 括号 vs do/end

在我的[哈希](https://dev.to/molly_struve/level-up-your-ruby-skillz-working-with-hashes-4bid)和[数组](https://dev.to/molly_struve/level-up-your-ruby-skillz-working-with-arrays-hnn)教程中，我选择使用大量 do/end 块符号来帮助弄清楚发生了什么逻辑。然而，括号符号是编写更紧凑代码的好方法。正如我们在教程中看到的，这是一个链接一堆逻辑的好方法。

```
result = [1, 2, 3, 4].map{|n| n + 2}.select{|n| n.even?}.reject{|n| n == 6} 
# result = [4] 
```

Enter fullscreen mode Exit fullscreen mode

现在你可能会想，既然有这么多选择，我怎么知道该用什么呢？！通常，一个好的经验法则是:

*   如果一行代码正在块中执行，那么使用括号。
*   如果你的逻辑不止一行，使用 do/end 符号。

单线括号示例:

```
a = [1, 2, 3]
a.map{|number| number + 10} 
```

Enter fullscreen mode Exit fullscreen mode

多行 do/end 示例:

```
a = [1, 2, 3]
a.map do |number|
  if number.even?
    puts "I am even"
    number/2
  else
    puts "I am odd"
    number
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

请记住，有些地方或人可能有不同的风格。当你在一个新的地方开始时，要时刻注意别人在做什么。当你开始的时候，你可能会想跟随他们的领导。随着你获得更多的经验，你会形成自己的风格和习惯。

### 链接

很快我想回到我上面使用的链接示例:

```
result = [1, 2, 3, 4].map{|n| n + 2}.select{|n| n.even?}.reject{|n| n == 6} 
# result = [4] 
```

Enter fullscreen mode Exit fullscreen mode

这段代码非常简单，这就是为什么将每一步链接起来是一个很好的选择，可以使它更加简洁。然而，如果每一步的逻辑都很复杂，有时你可能会为了可读性而把它们分开。这里有一个例子，说明了如何将它拆分以提高可读性。

```
plus_two_array = [1, 2, 3, 4].map{|n| n + 2}
even_numbers_array = plus_two_array.select{|n| n.even?}
remove_the_sixes_array = even_numbers_array.reject{|n| n == 6} 
```

Enter fullscreen mode Exit fullscreen mode

每个步骤及其结果都有明确的定义，这使得更复杂的逻辑更容易理解。我之所以要指出这一点，是因为有时候编写最紧凑的代码并不适合某种情况。所有这些策略都很棒，但是要知道有时候你可能会为了可读性而牺牲紧凑的代码。

# 但是等等，还有！

[![](img/c2463a7841b6c1c64cc13dd929fa51d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FCdbP5ya--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ydyqwc602h0ydzuoc9s9.gif)

显然，有很多方法可以让你写出更简洁紧凑的代码。这只是一个很小的列表，但我认为这是一个很好的起点。如果你发现了其他有助于你改进代码的技巧，请在评论中提出来。一如既往，如果您有任何问题或任何需要澄清的事情，请不要犹豫地问！

如果你想快速参考这些方法，我做了一个[方便的备忘单](https://paper.dropbox.com/doc/Tricks-For-Writing-Compact-Ruby-Code--AeAl8FR15SMDlf_JW9BrymB1AQ-IAfGvmx5cKZ6s38UM4Rrl)，里面有每个例子的代码。
# 使用 Ruby 的可枚举模块的 4 种鲜为人知的方法

> 原文：<https://dev.to/orbit/4-lesser-known-ways-to-use-ruby-s-enumerable-module-3d8b>

我喜欢 Ruby 的一个重要原因是，我可以用几个字符或几行代码完成很多工作，并确保代码对我的同行来说仍然易于阅读。最明显的一个领域是处理数组和散列，在 Ruby 世界中也称为**可枚举数**。

任何包含 Ruby 强大的[可枚举](https://ruby-doc.org/core-2.6.3/Enumerable.html)模块的对象都可以以各种方式进行迭代、遍历、操作、切片和切块。这个模块的灵活性使得复杂任务的代码出奇的简洁。

当 2018 年 12 月 Ruby 2.6 到来时，它带来了一些可枚举的新方法，以及对列表和序列处理的其他改进。在这篇文章中，我将向您展示一些最近添加到 Ruby 的 Enumerable 中的功能，以及一些以前的常用功能。

## 无穷无尽的范围

Ranges 是 Ruby 的一个很棒的特性，它允许您从数字或字母快速创建可迭代的范围，比如`1..10`或`('A'..'Z')`。在 Ruby 2.6 之前，双点范围语法需要一个开始和一个结束。现在，有一个直观的语法来创建这些无尽的范围——只需省略双点后的最后一个字符:`1..`。

为什么无穷区间有用？一个明显的用例是生成一个不断增长的整数列表:

```
(1..).each do |i|
  puts i
end
# 1
# 2
# 3
# ... 
```

Enter fullscreen mode Exit fullscreen mode

使用无限范围的另一种独特方式是与链接到 enumerable 上的其他方法一起使用。在这些情况下，范围并不完全是无限的，而是在满足链接方法的条件时结束。我们来看一个例子:

```
p (1..).step(5).take(100)
# [1, 6, 11, 16, 21, 26, 31, 36, 41, 46, …. 491, 496] 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了什么事？`step(5)`方法从该范围中取出第五个数字，该范围保持无限。`take`方法获取该序列的前 100 个元素，该序列现在变得有限。使用一个无限的范围来开始表达式可以确保后面的部分适用于任何一组输入。我们可以将 5 或 100 改为更大的数字，但仍然可以得到预期的结果。

提示:范围也适用于字母:

```
p ('A'..'Z').step(2)
# ["A", "C", "E", "G", "I", "K", "M", "O", "Q", "S", "U", "W", "Y"] 
```

Enter fullscreen mode Exit fullscreen mode

## `lazy`关键词

这对于 Ruby 2.6 来说并不陌生，但它功能强大且未被充分利用。可枚举数支持一种惰性迭代形式，这有助于避免在不需要的时候将整个文件或数据库记录集读入内存。例如，当您只需要找到文件中包含单词“jane”的前 10 行时。

```
File.open("names.txt") do |f|
  f.each_line.lazy.select { |line| 
    line.match(/jane/i) 
  }.first(10)
end 
```

Enter fullscreen mode Exit fullscreen mode

虽然这只是一个额外的方法，但是在`each_line`之后添加`lazy`改变了幕后发生的事情。整个文件不会被读入内存，如果没有 lazy 关键字就会发生这种情况。在表达式的末尾加上 lazy 和`first(10)`，文件被逐行读取，但是一旦找到 10 个匹配项，读取就会停止。

## 范围步进

对一个范围的`step`方法可以帮助你产生每第二、第三或第 n 个元素的子范围。Ruby 2.6 带来了更强大的功能和新的、更短的单步执行语法。

首先，步骤的新别名是可用的- `%` :

```
p ((1..10) % 2)
# [1, 3, 5, 7, 9] 
```

Enter fullscreen mode Exit fullscreen mode

接下来，现在有了可以在范围的步骤上调用的`first`和`last`方法，它们属于类型[算法序列](https://ruby-doc.org/core-2.6.0.preview2/Enumerator/ArithmeticSequence.html)。

```
p ((1..10) % 2).last
# 9 
```

Enter fullscreen mode Exit fullscreen mode

## 各 _ 弊

当你需要一次迭代多个**重叠**元素的数组时，`each_cons`就派上了用场。这种方法从类似于 slice 的连续元素中产生子数组。但是，下一个数组的第一个元素是上一个数组的第二个元素。通过一个例子可以很容易地看出这一点。

```
p (1..10).each_cons(2)
# [[1, 2], [2, 3], [3, 4], [4, 5], [5, 6], [6, 7], [7, 8], [8, 9], [9, 10]] 
```

Enter fullscreen mode Exit fullscreen mode

`each_cons`接受一个整数参数来指定每个数组应该有多大。

因为字符串只是字符数组，`each_cons`可以用来做一些字符串处理，否则可能会很繁琐。这里有一个有趣的例子可以让你的琴弦看起来更加怪异:

```
str = "arrays and collections are scary"
p str.chars.each_cons(2).map(&:join).join
# "arrrraayyss  aanndd  ccoolllleeccttiioonnss  aarree  ssccaarry" 
```

Enter fullscreen mode Exit fullscreen mode

使用`each_cons`的另一个可能更有用的方法是在迭代一个集合时保持前一个和下一个元素在上下文中。

```
primes = [2, 3, 5, 7, 11, 13]
primes.each_cons(3).each { |previous, current, next_| 
  p "#{current} is the prime number between #{previous} and #{next_}"
}
# "3 is the prime number between 2 and 5"
# "5 is the prime number between 3 and 7"
# "7 is the prime number between 5 and 11"
# "11 is the prime number between 7 and 13" 
```

Enter fullscreen mode Exit fullscreen mode

这里可以看到另一个方便的 Ruby 特性:参数析构。一个数组被传递给`each`函数的函数参数，但是我们可以提供三个参数:`previous`、`current`和`next_`让 Ruby 自动将第 0、第 1 和第 2 个数组元素放入这些变量中。

## 结论

我们只是触及了 Ruby 可枚举的表面。希望你已经学会了一些技巧来加强你当前或下一个代码库。结合无限的范围、步进和 lazy 关键字可以使整个循环处理用例家族变得更加容易。

如果你有兴趣了解更多关于如何提升你的 Ruby 代码，我强烈推荐 Aaron Patterson 的 Code[ish]播客和这一集关于 [Ruby 正则表达式以及开源开发者如何让你的公司变得更强大](https://www.heroku.com/podcasts/codeish/2-ruby-regexes-and-risk-aaron-patterson-explains-why-hiring-open-source-developers-will-make-your-company-stronger)。

感谢阅读！
# 如何学习一门新的编程语言

> 原文：<https://dev.to/jakenherman/how-to-learn-a-new-programming-language-321d>

> 这篇文章最初发表在我的网站[jakenherman.com](https://www.jakenherman.com/articles/2019-05/learning-a-new-programming-language)上。优雅主题的封面图片。

学习任何语言的最好方法就是简单地使用它(T2)。

话虽如此，我经常发现新开发人员只想学习最新、最炫、最“酷”的语言，比如 Python 或一些新的 JavaScript 库。如果您不首先熟悉与编程相关的基本概念和技术，这种努力可能是徒劳的。

我能提供的最好建议是学习算法、设计模式和编程基础。一旦你学会了这些，你就可以用任何语言编写任何程序。

例如，我不知道如何使用 Ruby 语言。但是，我将利用我的编程基础知识(循环、条件、操作符、io 等)带您浏览 Ruby 中流行的 FizzBuzz 程序。):

我需要做的第一件事是定义问题。这是编写任何程序的第一步。如果你不首先知道问题是什么，你就无法找到解决问题的办法。问题是这样的:对于从 1 到 100 的每个数字，如果这个数字能被 3 整除，我想打印“Fizz ”;如果这个数字能被 5 整除，我想打印“Buzz ”;如果这个数字能被两者整除，我想打印“FizzBuzz”。

我需要做的下一件事是检查 Ruby 文档中使用的"[循环](https://docs.ruby-lang.org/en/2.4.0/syntax/control_expressions_rdoc.html#label-until+Loop):

```
a = 0

until a > 100 do
  a += 1
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我有了一个`until`循环，我需要再次检查如何在 Ruby 中进行模运算，检查[文档](http://ruby-doc.org/core-1.9.3/Numeric.html#method-i-modulo)，以及检查[如何调用`if`语句](https://ruby-doc.org/core-2.3.0/doc/syntax/control_expressions_rdoc.html)，然后应用我所发现的，修改我的代码如下:

```
a = 0

until a > 100 do

  if a % 3 == 0
    print fizz
  end

  if a % 5 == 0
    print buzz
  end

  a += 1
end 
```

Enter fullscreen mode Exit fullscreen mode

最后，你会注意到我刚刚在 if 语句中写了“print fizz”和“print buzz”。现在我需要检查[如何通过文档编写我的结果](https://docs.ruby-lang.org/en/2.6.0/IRB/OutputMethod.html)，并修改我的代码:

```
a = 1

until a > 100 do

  if a % 3 == 0
    print "fizz"
  end

  if a % 5 == 0
    print "buzz"
  end

  a += 1
  puts ""
end 
```

Enter fullscreen mode Exit fullscreen mode

好了，我的第一个 ruby 程序已经写好了。你看到了我的思考过程，以及我找出如何完成项目的方法。现在我可以想出一个更难或者更复杂的程序，用同样的方法用 ruby 写出来。到时候，只要**使用** ruby，我就会学会了。

还要记住，这个程序可能不是处理事情的最佳方式——还有待改进。没关系。没有人会期望你在学习的时候写出完美的代码。

那么，你如何学习 Python、JavaScript、C++或者其他语言呢？替换这个答案中单词“Ruby”的每一个实例，用你想学的任何一种编程语言替换。最后，你会有一个 FizzBuzz 程序。然后，你所要做的就是想出另一个更复杂的问题，然后**去做。**
# Bash If 语句:初学者到高级

> 原文：<https://dev.to/rpalo/bash-if-statements-beginner-to-advanced-25e6>

Christy Mills 在 Unsplash 上拍摄的封面照片。

Bash 是一种有趣的语言。它主要设计用于在命令行上交互使用，本质上是在 REPL(读取-评估-打印-循环)中，一次处理一个用户命令。它被设计成强大而简洁地处理纯文本。这有一个副作用，那就是做一些非交互式或基于文本的事情变得更加困难，比如算术、控制流和操纵变量。

在本文中，我们将讨论其中一个有点令人困惑的话题:`if`语句。我们将深入了解它们是如何工作的，以及我们如何以非常简洁的方式使用这些机制。

*注意:这篇文章是专门关于 Bash 的，尽管大部分内容可以转化为 Zsh 和其他 shells。其中一些不符合 POSIX。有些人对此有自己的看法。我不会在这篇文章中讨论这个问题。如果你想在这个问题上变得迂腐，你可以直接`sh`。*🤫

## 基础知识

Bash 中的 if 语句的工作方式与大多数其他语言非常相似。它遵循基本形式:

```
if [[ "$some_variable" == "good input" ]]; then echo "You got the right input."
elif [[ "$some_variable" == "ok input" ]]; then echo "Close enough"
else echo "No way Jose."
fi 
```

Enter fullscreen mode Exit fullscreen mode

关键字`then`增加的语法，以及稍微奇怪的结束关键字`fi`使它看起来比其他语言 <sup id="fnref1">[1](#fn1)</sup> 更有异国情调，但基本原理是相同的:

如果某件事是真的，那就做这件事。否则，按顺序检查这些其他条件，并做同样的事情。如果都没有成功，那就做最后一件事。

你可以省去一个或者所有的`elif`分支，如果你不需要的话，你甚至可以省去`else`分支！

布尔运算符`!` (not)、`&&` (and)和`||` (or)可以用来组合布尔，就像其他语言一样。

```
if [[ "$name" == "Ryan" ]] && ! [[ "$time" -lt 2000 ]]; then echo "Sleeping"
elif [[ "$day" == "New Year's Eve" ]] || [[ "$coffee_intake" -gt 9000 ]]; then echo "Maybe awake"
else echo "Probably sleeping"
fi 
```

Enter fullscreen mode Exit fullscreen mode

但令人困惑的是。有时你会看到这些带有双方括号，就像我上面展示的。或者有时它们是单方括号。

```
if [ "$age" -gt 30 ]; then echo "What an oldy."
fi 
```

Enter fullscreen mode Exit fullscreen mode

或者有时他们会是圆的！

```
if (( age > 30 )); then echo "Hey, 30 is the new 20, right?"
fi 
```

Enter fullscreen mode Exit fullscreen mode

有时候他们根本不在那里！

```
if is_upper "$1"; then echo "Stop shouting at me."
fi 
```

Enter fullscreen mode Exit fullscreen mode

你怎么知道哪些是你应该使用的呢？你什么时候使用每个版本？为什么某些人在某些时候不工作？

## 到底发生了什么

下面是驱动关于`if`在 Bash 中如何工作的一切的神奇词汇:**退出代码**。这里真正发生的是模式:

```
if ANY_COMMAND_YOU_WANT_AT_ALL; then
  # ... stuff to do
fi 
```

Enter fullscreen mode Exit fullscreen mode

没错:紧跟在`if`后面的东西可以是世界上任何一个命令，只要它提供了退出代码，这几乎总是如此。如果该命令返回的退出代码为 0，这是成功的 Bash 退出代码，那么`then`分支内的代码将开始运行。否则 Bash 会转移到下一个分支并再次尝试。

*但是等等，这是不是意味着——*

没错。`[`“是命令。它实际上是内置命令`test`的语法糖，用于检查和比较它的参数。“`]`”实际上是`[`命令的一个参数，该命令告诉它停止检查参数！

```
if [ "$price" -lt 10 ]; then echo "What a deal!"
fi 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`[`命令接受参数`"$price"`(立即用变量值替换)、`-lt`、`10`和`]`。现在你知道了`-lt`、`-gt`以及类似的数值比较实际上是参数，奇怪的语法不是更有意义一点吗？它们看起来有点像选项！这就是为什么`>`和`<`在单个方括号内变得奇怪——Bash 实际上认为您试图在命令内进行输入或输出重定向！

### 那双方括号呢？

奇怪的是，`[[ double square brackets ]]`和`(( double parens ))`并不是*确切地说是*命令。它们实际上是 Bash 语言关键字，这使得它们的行为更容易预测。也就是说，它们仍然会根据内容返回一个退出代码。`[[ double square brackets ]]`的工作本质上和`[ single square brackets ]`一样，尽管有更多的超级能力，比如更强大的正则表达式支持。

如果你想了解 Bash 中不同的括号标点及其作用，请查看我整理的参考资料。

### 那双括号呢？

`(( double parentheses ))`实际上是一个允许在 Bash 内部进行算术运算构造。您甚至不需要将它们与`if`语句一起使用。我经常使用它来快速递增计数器并就地更新数值变量。

```
count=0
(( count++ ))
echo "$count"
# => 1
(( count += 4 ))
echo "$count"
# => 5 
```

Enter fullscreen mode Exit fullscreen mode

你没有看到的是，算术父母实际上在每次运行时都返回一个退出代码。如果里面的结果是零，它返回一个退出代码 1。(本质上，零是“假的”。)如果它是任何其他数字，它将是“truthy ”,退出代码将是 0。这里有一个奇怪的例子:

```
if (( -57 + 30 + 27 )); then echo "First one"
elif (( 2 + 2 )); then echo "Second one"
else echo "Third one"
fi
# => "Second one" 
```

Enter fullscreen mode Exit fullscreen mode

对我们来说幸运的是，大于号和小于号在算术运算中工作得很好。如果比较结果为真，结果将为 1。否则，就是零分。

```
if (( 5 > 3 )); then echo "Numbers make sense."
elif (( 3 <= 2 )); then echo "3 is less than or equal to 2\. wat."
else echo "Hwwaaa"
fi
# => "Numbers make sense" 
```

Enter fullscreen mode Exit fullscreen mode

该功能的一个奇怪但有趣的分支是:

```
echo $(( (5 > 3) + (0 == 0) ))
# => 2
# Each comparison is true, so we're effectively echoing
# 1 + 1\.  Fun, right? 
```

Enter fullscreen mode Exit fullscreen mode

## 用命令代替括号

很多时候，你会在你的`if`陈述中使用一些括号。然而，由于可以使用命令和它们的退出代码，命令行的全部功能都支持您的`if` s。

假设我们只想在文件中找到一行时做一些事情。您通常会使用什么命令来搜索文件中的文本？`grep`！

```
echo "Hello, welcome to bean house." >> dialogue.txt
echo "Would you like some coffee?" >> dialogue.txt

if grep -q coffee dialogue.txt; then echo "Found coffee, boss."
else echo "No coffee."
fi
# => "Found coffee, boss." 
```

Enter fullscreen mode Exit fullscreen mode

如果`grep`找到了它要找的东西，它将退出，退出代码为 0，表示成功。如果没有，它将退出，退出代码为 1，表示失败。我们用它来推动我们的`if`声明！`grep`的`-q`选项代表`--quiet`，它阻止`grep`输出它找到的任何行。如果我们把它拿掉，我们的输出会像这样:

```
Would you like some coffee?
Found coffee, boss. 
```

Enter fullscreen mode Exit fullscreen mode

> 你还有其他这样使用的命令吗？让我知道！我很难想出例子，我肯定有一些非常强大的用例。

### 使用自己的函数

这整件事情最好的部分是你可以写你自己的函数！这有助于您将逻辑封装到具有更高级名称的东西中，使您的脚本更具可读性，并阐明您的意图。如果有什么地方我们可以使用一些额外的清晰度，那就是 Jerry 去年写的“一次性”脚本，我们仍然在构建管道中使用它。

我需要再写一篇关于在 Bash 中编写函数的文章，因为它们很棒，我喜欢它们。目前，Spark Notes 版本就像它们自己的迷你脚本一样工作。你传递给它们的任何参数都可以被特殊变量按位置访问，`$1`、`$2`、`$3`等等。自变量的个数在变量`$#`中。

```
function is_number {
  if [[ "$1" =~ ^[[:digit:]]+$ ]]; then
    return 0
  else
    return 1
  fi
} 
```

Enter fullscreen mode Exit fullscreen mode

注意这里我们用`return`代替`exit`。他们做同样的事情，除了`exit`会杀死整个脚本，而不仅仅是完成函数。该功能可以这样使用:

```
function is_number {
  if [[ "$1" =~ ^[[:digit:]]+$ ]]; then
    return 0
  else
    return 1
  fi
}

age="$1"

if ! is_number "$age"; then echo "Usage: dog_age NUMBER"
  exit 1
fi 
```

Enter fullscreen mode Exit fullscreen mode

看看程序员的意图和逻辑是如何变得更加清晰，而没有实现细节和庞大的正则表达式的阻碍？Bash 函数是好东西。

事实上，如果一个函数没有显式地`return`一个值，函数中最后一个命令的返回值被隐式地使用，所以你可以把你的函数缩短为:

```
function is_number {
  [[ "$1" =~ ^[[:digit:]]+$ ]]
} 
```

Enter fullscreen mode Exit fullscreen mode

如果正则表达式有效，双方括号的返回码是 0，因此函数返回 0。如果不是，一切都返回 1。这是命名正则表达式的一个非常好的方法。

## `if`好

这就对了。继续前进，用您新发现的理智、惯用的分支能力清理您的 Bash 脚本。并与我分享您的使用案例！我喜欢看别人整洁的狂欢。

* * *

1.  还有，在脑子里看书很好玩。菲。 [↩](#fnref1)
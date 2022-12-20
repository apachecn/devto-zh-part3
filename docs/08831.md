# 编程初学者完全指南

> 原文：<https://dev.to/aspittel/a-complete-beginner-s-guide-to-programming-2ni4>

这篇文章对于程序员来说似乎不合适，但是我想从零开始写点东西。编程到底是什么？编程的组成部分是什么？

这篇文章将从概念层面讨论什么是计算机开始，然后讨论编程基础。在这篇文章中，我们将使用 Python，但是许多构建块跨编程语言工作，所以即使您最初对学习一种不同的语言感兴趣，这仍然会有帮助。

## 什么是编程？

你可能每天都与计算机打交道，但是让我们具体定义一下当我们谈论与编程相关的计算机时，我们指的是什么。计算机是一种处理和存储信息的机器。

编程是告诉计算机如何摄取、处理和存储这些数据。当有人编写程序时，这个人就给了计算机一组它必须遵循的命令。

当我们写程序时，我们写的是计算机将遵循的指令。计算机是非常字面化的，它们会接受我们的指令并严格执行，但我们必须以非常详细的方式将它们表达出来，以便它们能理解我们。

编程的核心是把一个大问题分解成越来越小的问题，直到它们小到我们可以告诉计算机去解决那个问题。

## 在哪里可以看到正在使用的程序？

到处都是！从你电脑上的操作系统到复杂的网站，都是用代码写的！旧的(和新的！)手机、花式咖啡机、无人驾驶汽车、脸书、亚马逊、自动取款机、Lyft 应用程序、地铁卡充值器、超市扫描仪和大多数电视都使用代码来运行，除了你的台式机或笔记本电脑。

## 什么是编程语言？

默认情况下，计算机无法理解自然语言，尽管它们越来越接近理解自然语言了！

从根本上说，计算机是靠一系列微小的开关运行的，当我们写代码时，我们就像电灯开关一样开关它们。计算机使用一种叫做二进制的数字系统来使用这些开关。二进制是由 1 和 0 组成的数字系统，与我们使用 0-9 的十进制相反。

谢天谢地，在我们这个时代之前，许多真正聪明的人想出了一种方法，我们可以不用 0 和 1 就能和我们的计算机对话。相反，我们使用可以被我们的计算机翻译的编程语言，类似于语言翻译人员可以将西班牙语翻译成英语，甚至将英语翻译成手语。这些看起来更像英语而不是二进制，但它们仍然比自然语言有更多的符号和更少的做事方式。

有大量的编程语言，就像世界上有许多语言一样。有些，像汇编或 C 语言是非常低级的，不像我们说话的方式。其他语言，如 Python 和 Ruby，与人类语言非常相似。这些语言用于不同的任务，例如 HTML、CSS 和 JavaScript 用于编写网站，而 C 用于编写操作系统。随着时间的推移，这些已经发生了很大的变化。老程序员过去不得不使用穿孔卡，并将它们输入计算机，而不是在他们的计算机上键入代码！在性能和易用性之间有所取舍，但是当你学习如何编码时，我强烈推荐一种更接近普通语言的语言！

## 编程的关键基础

有一些基本概念随着我们从一种编程语言转移到另一种编程语言。我们将使用 Python，但是几乎所有已知的语言都有这些基础知识，尽管它们可能以不同的方式编写(除了 HTML 和 CSS 之外，它们是非常不同的)。

> 简单地说，你可以使用 [Repl.it](https://repl.it) 在你的网络浏览器中运行 Python。您可以通过按下`new repl`按钮并选择 Python 来创建一个 Python 项目。然后，在`main.py`下的区域输入。然后你可以用绿色的`run`按钮运行你的代码。
> 
> 你也可以通过[下载](https://www.python.org/downloads/)Python，然后使用文本编辑器——我最喜欢的是 [VS 代码](https://code.visualstudio.com/)在你的电脑上安装 Python。然后，您可以通过[命令行](https://dev.to/maxwell_dev/the-shell-introduction-i-wish-i-had-551k)运行代码。您将创建一个扩展名为`.py`的文件，然后在编辑器中编写代码，最后，您将通过运行`python your_file_name.py`来运行该文件。

### 你好世界

当你学习一门新的编程语言时，写一个 hello world 程序是一个传统。所以我们用 Python 写一个吧！

Python 中有一个`function`允许我们在运行代码的任何地方写文本——所以如果你使用命令行，它会在那里打印出来，如果你使用 [Repl.it](https://repl.it) ，它会在你屏幕的右边打印出来。

> 我们一会儿就来说说都有哪些功能！

```
print("Hello, World!") 
```

Enter fullscreen mode Exit fullscreen mode[https://repl.it/@AliSpittel/CloseFuchsiaProfiler?lite=true](https://repl.it/@AliSpittel/CloseFuchsiaProfiler?lite=true)

**所有的代码示例也可以在 repl.its 中获得，但是是链接的而不是嵌入的**

### 变量

变量是编程中非常重要的一部分。变量存储了一条你可以反复使用的信息。如果你记得代数课上的变量，它们在概念上是一样的！

在 Python 中，为了设置一个变量，我们将编写一个变量名(在本例中为`name`和`age`)，然后使用等号将该变量设置为一个值——在本例中为“Ali”和 24。

如果我们用字母，我们需要把它们放在引号里。如果我们想要一个数字，那么我们就不用引号了。

```
name = "Ali"
age = 24

print(name)
print(age) 
```

Enter fullscreen mode Exit fullscreen mode

现在，无论何时我们都可以在代码中使用`name`或`age`！

[Repl.it](https://repl.it/@AliSpittel/BeautifulRedundantBusinesssoftware)

记住，从上面来看，编程语言是为人类设计的，而不仅仅是计算机。如果我们只是在和计算机对话，那么我们只会用 0 和 1 来写二进制！所以，记住使用对未来的你有意义的变量名——或者将来阅读你的代码的另一个程序员。

> 变量的经验法则:如果你要反复使用一个值，为它创建一个变量！

### 数据类型

编程时我们可以使用不同类型的数据。一些常见的是整数、浮点、布尔和字符串。

#### 整数

整数是另一个数学术语——它们基本上是没有小数的数字。所以 1，0，-100，200 等等。

我们在 Python 中只要输入数字就可以使用整数！

```
5
10
-20
30 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将它们存储在变量中，就像我们上面看到的那样，这样我们就可以反复使用它们。

```
my_favorite_number = 22 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以和他们一起做数学！这四个基本符号是:`*`表示乘法，`+`表示加法，`-`表示减法，`/`表示除法。

```
print(22 + 10)

pieces_of_candy = 20 - 5
print(pieces_of_candy) 
```

Enter fullscreen mode Exit fullscreen mode

[Repl.it](https://repl.it/@AliSpittel/PopularAnotherDrivers)

#### 浮动

浮点数是带小数的数字，比如 4.0，4.5，-19.6 等等。否则它们就像数字一样工作！

#### 布尔

在 Python 中，布尔值是`True`和`False`——它们通常用于表示“是”或“否”——还记得计算机是一堆开关吗？布尔也差不多！

我们可以将变量设置为布尔值，但比较值并将结果视为布尔值通常更有用。一个常见的用途是检查相等性。某个变量等于某个值吗？我们使用两个等号来检查等式，因为我们使用一个等号来设置变量。

```
age = 22
print(age == 22) # True 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以检查大于或小于——或者所有这些数学检查。我们用`>`表示较大，`<`表示较小，`>=`表示较大或相等，`<=`表示较小或相等！

```
print(5 > 10) # False print(10 >= 10) # True 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以将布尔值存储到变量中！

```
is_greater = 5 > 10
print(is_greater) # False 
```

Enter fullscreen mode Exit fullscreen mode

> Python 中标签后面的东西是注释——它们被计算机忽略，是给你自己或其他开发者的注释！

#### 字符串

字符串是 Python 中的文本。我们用引号将它们括起来！

```
my_string = "This is a string!"
print(my_string) 
```

Enter fullscreen mode Exit fullscreen mode

#### 列表

有时，我们希望在一个变量中存储多个值，比如一组数字、房间里的人的名字、twitter 上最近的推文，或者我们商店里的商品价格。

我们可以用 Python 将这些值存储在列表中！

```
items_in_store = [5, 10, 15, 8]
dev_employees = ["Ali", "Jess", "Ben", "Peter", "Andy", "Mac", "Liana", "Michael", "Anna", "Mario"] 
```

Enter fullscreen mode Exit fullscreen mode

> 列表的经验法则:如果你有一组相似的东西，把它们放在一个列表里

### 条件句

编程的另一个关键部分是条件。这些允许我们有时运行代码块，有时运行其他代码块。

所以，如果一个条件是`True`，运行一段代码。也许，如果其他事情是真的，运行不同的代码块。最后，如果所有其他的都不是`True`，运行另一个代码块。

> 在 Python 中，代码块是缩进的，所以“如果这个事情是真的，就在它后面运行缩进的代码”

```
name = "Ali"

if name == "Ali":
    print("Hi, Ali!") 
```

Enter fullscreen mode Exit fullscreen mode

[Repl.it](https://repl.it/@AliSpittel/UntimelyPiercingTag)

现在让我们添加一个条件，如果第一个条件是`False`，它将运行。我们将为此使用`else`关键字！

```
password = "hello!"
correct_password = "hi"

if password == correct_password:
    print("Welcome to the website!")
else:
    print("Permission denied") 
```

Enter fullscreen mode Exit fullscreen mode

[Repl.it](https://repl.it/@AliSpittel/StrongAgitatedHexagon)

我们还可以使用`elif` :
检查多个条件

```
age = 50

if age > 100:
    print("you are old")
elif age < 20 and age >= 13:
    print("You are a teenager")
elif age < 13:
    print("You are a child")
else:
    print("You are an adult") 
```

Enter fullscreen mode Exit fullscreen mode

给`age`放入不同的值，看看有什么变化！

> 可以用`and`一次检查多个条件！或者`or`来检查一件事情是真的还是另一件事情是真的。

[Repl.it](https://repl.it/@AliSpittel/PastelHumiliatingDisassembler)

> 条件的经验法则:如果你想让某些代码有时运行，而其他代码在其他时间运行:使用条件！

### 循环往复

循环允许我们针对不同的值反复运行相同的代码块。最常见的情况是循环遍历一个列表。

列表有两种主要类型——第一种是`for`循环。他们遵循公式`For item in list: do something`。该项可以是任何东西——它是一个随每个循环而变化的变量名。

例如:

```
dev_employees = ["Ali", "Jess", "Ben", "Peter", "Andy", "Mac", "Liana", "Michael", "Anna", "Mario"]

for employee in dev_employees:
    print("Hi" + employee + "!") # We can join multiple strings together with `+` signs! 
```

Enter fullscreen mode Exit fullscreen mode

起初，`employee`是阿里，然后移动到杰斯，然后是本等。

While 循环也存在，但是它们比较少见，并且有一些技巧，所以我们现在跳过它们！

[Repl.it](https://repl.it/@AliSpittel/LonelyDemandingLesson)

> 循环的经验法则:如果你想要相同的代码一遍又一遍的运行，使用循环！

### 功能

在我开始解释函数之前，先快速分解一下行话:

*Arguments*——每次调用时传递给函数。

*参数* -函数定义中的变量。

在`def myFunction(x, y)`中，x 和 y 是参数。当我们通过说`myFunction(1, 3)`来运行那个函数时，1 和 3 是参数。

当我教授函数时，我试图用两种方式来教授它们，以便对两种不同类型的思考者有意义。第一个是可重用的代码块，您可以将值插入其中，以使您的代码更加通用，并允许更少的重复代码。在这种情况下，参数是插入到代码块中的“动态”信息。所以，当你调用这个函数时，这个值可能会改变。几乎是一个变量，每次运行函数时都不一样。

我也喜欢把函数解释为一系列的输入和输出——有点像一个小机器。你把东西放进机器里，然后就有东西出来了。参数是你输入到机器中的东西，返回值是输出的内容。这更符合函数的代数定义——如果你还记得学校数学中的`f(x) = 2x + 1`,这些函数只是写在纸上，而不是以编程方式编写的。

在 Python 中，传递给函数的参数顺序对应于函数声明中参数的顺序。所以，如果我的函数声明看起来像`def add(x, y)`，然后我用`add(1, 2)`调用函数，在函数中 1 将是`x`，2 将是`y`。如果我改为跑`add(100, 50)`，`x`会是 100，`y`会是 50。由于`x`是我的第一个参数，我传递给函数的第一个参数将是`x`，由于`y`是第二个，我传递的第二个值将是`y`。有时候把这个用图表表示出来会有帮助。

`return`关键字之后的任何内容都是函数的输出。

```
def subtract(x, y):
  return x - y

print(subtract(5, 2)) # 3, 5 is x, 2 is y print(subtract(200, 50)) # 150, 200 is x, 50 is y print(subtract(20, 70)) # -50, 20 is x, 70 is y 
five = subtract(10, 5)
print(five) 
```

Enter fullscreen mode Exit fullscreen mode

| x | y | 减去(x，y) |
| --- | --- | --- |
| five | Two | three |
| Two hundred | Fifty | One hundred and fifty |
| Twenty | Seventy | -50 |

[Repl.it](https://repl.it/@AliSpittel/PlushNecessarySubweb)

再比如:

```
def say_hi(person):
    print("Hi " + person)
    return person

ali = say_hi("Ali")
print(ali) 
```

Enter fullscreen mode Exit fullscreen mode

[Repl.it](https://repl.it/@AliSpittel/MatureUnlinedOctal)

这个函数*输出* `person`(与输入相同)，但它还执行另一个动作——用`hi`打印出人名。第一个动作不会影响输出，也不会影响函数的`return`。如果打印出来`ali`就是“阿里”！

> 函数的经验法则:如果你想重用一段代码，可能包含不同的数据，使用函数！

## 学习如何编码的后续步骤

对于编程来说，这些是非常重要的基本概念，但是还有更多的！两个重要的是调试和解决问题。

当你写代码的时候，计算机真的很聪明，因为它完全按照它告诉你的去做。但是，如果您有一个打字错误或任何不正确的代码，您的代码将抛出一个错误！学会克服这些错误，喜欢它们告诉你发生了什么，而不是害怕它们！他们很有帮助！

当我们试图把拼图拼在一起制作不同的程序时，问题解决就出现了。实际上，我现在正在制作关于这个的系列片。

另外，如果你想要更多学习编程的免费资源，[这里有](https://dev.to/aspittel/my-favorite-free-resources-for-new-programmers-bia)一些很棒的资源！
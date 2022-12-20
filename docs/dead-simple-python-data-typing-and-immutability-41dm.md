# 非常简单的 Python:数据类型和不变性

> 原文：<https://dev.to/codemouse92/dead-simple-python-data-typing-and-immutability-41dm>

喜欢那些文章吗？买这本书！ [***死简单蟒*** 作者杰森·c·麦克唐纳可从无淀粉出版社购得。](https://nostarch.com/dead-simple-python)

* * *

我收到了达米安·里瓦斯对这个系列的可爱评论...

> 刚刚看了目前上映的前两部。我不得不说，随着 Python 成为我所钻研的第五种语言，我真的很欣赏这种教学风格！很难找到不以“什么是变量”开头的教材 lol。

讨厌失望，达米安，但我不能永远避免变量！

好吧，好吧，我不会用变量的解释# 2582596 来烦你们。你们是聪明人，我相信你们现在对他们了如指掌。

但是现在我们已经准备好写代码了，我认为有必要了解一下 Python 中的变量*是什么。当我们在做这个的时候，我们将会看一下函数、字符串和所有其他的枯燥的东西...这很可能并不那么无聊。这里有很多信息，但是我相信当把它们放在一起理解时最有意义。*

欢迎来到 Python。下兔子洞时请小心家具。

# 迂腐点

我在整个系列中使用术语“变量”，主要是因为这是跨语言的标准术语。在 Python 中使用这个术语是有效的，甚至在官方文档中也得到了承认。

*然而*，一个 Python 变量的技术术语其实是一个`name`；这与我稍后将提到的“名称绑定”的整个概念有关。

使用任何你觉得舒服的术语。只要理解 Python 的“变量”被正式称为“名称”，您可能会听到这两个词。

# 数据类型在哪里？！？

2011 年夏天，我坐在华盛顿州西雅图的门廊秋千上，登录了 Freenode IRC。我刚刚决定从 Visual Basic 中切换语言。NET 转换成 Python，我有一些问题。

我加入了进去。

> 如何在 Python 中声明变量的数据类型？

几分钟之内，我收到了一个回复，我认为这是我第一次真正进入奇异的编程世界。

> 你是一个数据类型

他和房间里的其他常客很快就告诉了我。Python 是一种动态类型语言，这意味着我不必告诉这种语言变量中包含什么样的信息。我甚至不必使用特殊的“变量声明”关键字。我只是分配。

```
netWorth = 52348493767.50 
```

Enter fullscreen mode Exit fullscreen mode

就在那一刻，Python 成了我最喜欢的语言。

然而，在我们得意忘形之前，我必须指出 **Python 仍然是一种强类型语言**。

嗯，动态类型的？强类型？这意味着什么？

*   动态类型:变量(对象)的数据类型是在运行时确定的。与“静态类型化”相反，在“静态类型化”中，我们首先声明对象的数据类型。(C++是静态类型的。)

*   强类型:这种语言对不同的数据类型有严格的规则，比如将一个整数和一个字符串相加。与“弱类型化”相比，这种语言可以让你做任何事情，而且它会帮你解决问题。(Javascript 是弱类型的。)

(如果你想要更深入的解释，请看[为什么 Python 是一种动态语言，也是一种强类型语言](https://wiki.python.org/moin/Why%20is%20Python%20a%20dynamic%20language%20and%20also%20a%20strongly%20typed%20language))。

所以，换句话说: **Python 变量*有*数据类型，但是语言自动计算出那个数据类型是什么。**

因此，我们可以重新分配一个变量来包含我们想要的任何数据...

```
netWorth = 52348493767.50
netWorth = "52.3B" 
```

Enter fullscreen mode Exit fullscreen mode

...但是我们能做的有限...

```
netWorth = 52348493767.50
netWorth = netWorth + "1 billion"
>>> Traceback (most recent call last):
>>>  File "<stdin>", line 1, in <module>
>>> TypeError: unsupported operand type(s) for +: 'float' and 'str' 
```

Enter fullscreen mode Exit fullscreen mode

如果我们需要知道某个东西是什么类型的变量，我们可以使用`type()`函数。这将打印出变量是哪个**类**的实例。(在 Python 中，一切都是对象，所以戴上面向对象的帽子。)

```
netWorth = 52348493767.50
type(netWorth)
>>> <class 'float'> 
```

Enter fullscreen mode Exit fullscreen mode

实际上，我们可能想在对数据类型做一些事情之前，检查一下*的数据类型是什么。为此，我们可以将`type()`函数与`is`操作符配对，就像这样:* 

```
if type(netWorth) is float:
    swimInMoneyBin() 
```

Enter fullscreen mode Exit fullscreen mode

然而，在许多情况下，使用`isinstance()`而不是`type()`可能更好，因为这样会考虑子类和继承(面向对象编程，有人吗？)奖金，函数本身返回真或假...

```
if isinstance(netWorth, float):
    swimInMoneyBin() 
```

Enter fullscreen mode Exit fullscreen mode

现在，事实是，我们很少检查`isinstance()`。皮斯塔斯更喜欢 [**鸭子打字**](https://docs.python.org/3/glossary.html#term-duck-typing) 的哲学；也就是说，我们不是检查类型是什么，而是简单地在对象上寻找我们需要的特性。如果它看起来像只鸭子，走路像只鸭子，叫起来像只鸭子，那它一定是只鸭子。不管它实际上是一只机器鸭，还是一只穿着鸭子服装的驼鹿；如果它有我们需要的特征，其余的通常是一个有争议的问题。

# 永恒不变的真理

既然我刚刚介绍了那个`is`操作符，我们最好弄清楚一点: **`is`和`==`做的不是同一件事！**

许多 Python 新手发现这是可行的...

```
richestDuck = "Scrooge McDuck"

if richestDuck is "Scrooge McDuck":
    print("I am the richest duck in the world!")

if richestDuck is not "Glomgold":
    print("Please, Glomgold is only the SECOND richest duck in the world.")

>>> I am the richest duck in the world!
>>> Please, Glomgold is only the SECOND richest duck in the world. 
```

Enter fullscreen mode Exit fullscreen mode

“哦，太酷了！”西雅图的一位年轻开发人员说。"所以，在 Python 中，我只使用`is`和`is not`进行比较."

**错，错，错。**这些条件语句起了作用，但不是因为我认为的原因。一旦你尝试这样做，围绕`is`的错误逻辑就会土崩瓦解...

```
nephews = ["Huey", "Dewey", "Louie"]

if nephews is ["Huey", "Dewey", "Louie"]:
    print("Scrooge's nephews identified.")
else:
    print("Not recognized.")

>>> Not recognized. 
```

Enter fullscreen mode Exit fullscreen mode

“等等，什么？”您可能会对此稍加探究，甚至确认`nephews is nephews`的计算结果为`True`。那么，在迪默唐斯发生了什么呢？

麻烦的是，**`is`操作符检查两个操作数是否是同一个实例**，Python 有这些有趣的东西叫做**不可变类型**。

简单来说，当你有一个整数或者一个字符串的时候，程序的内存中实际上一次只有一个数据。早先我创建字符串`"Scrooge McDuck"`的时候，只有一个存在(不是一直都有吗？)如果我说...

```
richestDuck = "Scrooge McDuck"
adventureCapitalist = "Scrooge McDuck" 
```

Enter fullscreen mode Exit fullscreen mode

...我们会说`richestDuck`和`adventureCapitalist`都被*绑定到内存中`"Scrooge McDuck"`的这个实例*。它们就像几个路标，都指向完全相同的东西，而我们只有一个。

换句话说，如果你熟悉*指针*，这个有点像那个(没有可怕的尖锐边缘)。你可以有两个指针指向内存中的同一个位置。

如果我们改变其中一个变量，比如说`richestDuck = "Glomgold"`，我们将**重新绑定** `richestDuck`指向内存中不同的东西。(我们还会因为声称格洛姆戈尔德那么有钱而满口胡言。)

**另一方面，可变类型**可以在内存中多次存储相同的数据。像`["Huey", "Dewey", "Louie"]`一样，列表也是可变类型之一，这就是为什么`is`操作符之前报告了它所做的事情。这两个列表虽然包含完全相同的信息，*却不是同一个实例*。

**技术提示:**你应该知道不变性并不是*实际上*只与共享一个事物的实例有关，尽管这是一个常见的副作用。想象它是一种有用的方式，但不要依赖它总是如此。多个实例*可以*存在。在一个交互式终端中运行这个程序来理解我的意思...

```
a = 5
b = 5
a is b
>>> True
a = 500
b = 500
a is b
>>> False
a = 500; b = 500; a is b
>>> True 
```

Enter fullscreen mode Exit fullscreen mode

不变性背后的实际真相要复杂得多。我的 Freenode 朋友 Ned Batchelder ( `nedbat`)有一个关于这一切的的[精彩演讲，你应该去看看。](https://www.youtube.com/watch?v=_AEJHKGk9ns)

那么，我们应该用什么来代替`is`？你会很高兴地知道，这只是很好的老式`==`。

```
nephews = ["Huey", "Dewey", "Louie"]

if nephews == ["Huey", "Dewey", "Louie"]:
    print("Scrooge's nephews identified.")
else:
    print("Not recognized.")

>>> Scrooge's nephews identified. 
```

Enter fullscreen mode Exit fullscreen mode

**按惯例，你应该*总是*用`==`(等等。)用于比较*值*，而`is`用于比较*实例*。**的意思是，虽然它们看起来工作相同，但前面的例子实际上应该是这样的...

```
richestDuck = "Scrooge McDuck"

if richestDuck == "Scrooge McDuck":
    print("I am the richest duck in the world!")

if richestDuck != "Glomgold":
    print("Please, Glomgold is only the SECOND richest duck in the world.")

>>> I am the richest duck in the world!
>>> Please, Glomgold is only the SECOND richest duck in the world. 
```

Enter fullscreen mode Exit fullscreen mode

有一个半例外...

```
license = "1245262"
if license is None:
    print("Why is Launchpad allowed to drive, again?") 
```

Enter fullscreen mode Exit fullscreen mode

用`foo is None`检查非值有点常见，因为只有一个`None`存在。当然，我们也可以用简写的方式...

```
if not license:
    print("Why is Launchpad allowed to drive, again?") 
```

Enter fullscreen mode Exit fullscreen mode

两种方式都可以，尽管后者被认为是更干净、更“Pythonic 化”的方式。

# 警告词:匈牙利符号

当我还不熟悉这门语言时，我有了一个“绝妙”的想法，使用系统匈牙利符号来提醒我想要的数据类型。

```
intFoo = 6
fltBar = 6.5
strBaz = "Hello, world." 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，这个想法既不新颖也不高明。

首先，系统匈牙利符号是对应用程序匈牙利符号的恶意误解，而应用程序匈牙利符号本身是微软开发人员查尔斯·西蒙尼的聪明想法。

在匈牙利语应用程序中，我们在变量名的开头使用一个简短的缩写来提醒我们这个变量的*目的*。例如，他在 Microsoft Excel 的开发工作中使用了这一点，他在与行相关的任何变量的开头使用了`row`，在与列相关的任何变量的开头使用了`col`。这使得代码更具可读性，并有助于防止名称冲突(例如，`rowIndex`对`colIndex`)。直到今天，我在 GUI 开发工作中使用匈牙利语的应用程序，来区分小部件的类型和用途。

然而，Systems Hungarian 忽略了这一点，在变量前面加上了一个*数据类型的缩写*，比如`intFoo`或`strBaz`。在静态类型语言中，这是非常多余的，但是在 Python 中，这可能是个好主意。

然而，*不是*好主意的原因是它剥夺了动态类型语言的优势！我们可以在某一时刻将一个数字存储在变量中，然后在下一时刻将一个字符串存储在变量中。只要我们在代码中以某种有意义的方式这样做，这就可以释放静态类型语言所缺乏的大量潜力。但是如果我们在思想上把自己锁定在每个变量的一个预先确定的类型上，我们实际上是把 Python 当作一种静态类型的语言，在这个过程中束缚了自己。

也就是说，系统匈牙利语在您的 Python 编码中没有位置。坦率地说，它在任何编码中都没有一席之地。立刻从你的军火库里把它拿走，我们再也不要提这件事了。

# 施法召唤

让我们暂时停止对不变性的绞尽脑汁，接触一些更容易理解的东西:类型转换。

不，不是那种让大卫·田纳特得到史高治·麦克老鸭配音的角色....虽然他在那个角色里完全牛逼。

我说的是*将数据从一种数据类型转换成另一种数据类型*，在 Python 中，这是最简单的，至少对于我们的标准类型来说是这样。

例如，要将整数或浮点数转换成字符串，我们可以只使用`str()`函数。

```
netWorth = 52348493767.50
richestDuck = "Scrooge McDuck"
print(richestDuck + " has a net worth of $" + str(netWorth))
>>> Scrooge McDuck has a net worth of $52348493767.5 
```

Enter fullscreen mode Exit fullscreen mode

在那个`print(...)`语句中，我能够**将所有三个片段**(组合)成一个要打印的字符串，因为所有三个片段都是*字符串*。由于 Python 是强类型的(还记得吗),`print(richestDuck + " has a net worth of $" + netWorth)`会因`TypeError`而失败。)，而且你不能直接把一个浮点和一个字符串结合起来。

你可能会有点困惑，因为*这个*有效...

```
print(netWorth)
>>> 52348493767.5 
```

Enter fullscreen mode Exit fullscreen mode

那是因为`print(...)`函数在后台自动处理类型转换。但是它不能对那个`+`操作符做任何事情——这发生在数据被传递给`print(...)`之前——所以我们必须自己在那里进行转换。

当然，如果您正在编写一个类，您将需要自己定义这些函数，但这超出了本文的范围。(提示，`__str__()`和`__int__()`分别处理将对象转换为字符串或整数。)

# 吊经一...线

当我们谈到弦的时候，有一些关于弦的事情需要了解。也许最令人困惑的是，有多种方法来定义一个**字符串文字**...

```
housekeeper = "Mrs. Beakley"
housekeeper = 'Mrs. Beakley'
housekeeper = """Mrs. Beakley""" 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用单引号`'...'`、双引号`"..."`或三引号`"""..."""`将文字括起来，Python 将(大部分)以同样的方式处理它。第三种选择有些特别，但我们将回头再讨论它。

Python 风格指南， [PEP 8](https://www.python.org/dev/peps/pep-0008/#string-quotes) ，解决了单引号和双引号的使用问题:

> 在 Python 中，单引号字符串和双引号字符串是一样的。本 PEP 对此不做推荐。选择一条规则并坚持下去。但是，当字符串包含单引号或双引号字符时，请使用另一个字符来避免字符串中的反斜杠。它提高了可读性。

当我们处理这样的事情时，这就派上用场了...

```
quote = "\"I am NOT your secretary,\" shouted Mrs. Beakley."
quote = '"I am NOT your secretary," shouted Mrs. Beakley.' 
```

Enter fullscreen mode Exit fullscreen mode

显然，第二种选择可读性更强。引号前的反斜杠意味着我们想要那个*文字字符*，而不是让 Python 把它当作一个字符串的边界。然而，因为我们包装字符串的引号必须*匹配*，如果我们包装在单引号中，Python 将假设双引号是字符串中的字符。

我们真正需要这些反斜杠的唯一时间是当字符串中同时有两种类型的引号时。

```
print("Scrooge's \"money bin\" is really a huge building.")
>>> Scrooge's "money bin" is really a huge building. 
```

Enter fullscreen mode Exit fullscreen mode

就我个人而言，在这种情况下，我更喜欢使用(并转义)双引号，因为它们不会像撇号那样逃过我的注意。

但是请记住，我们也有三重引号(`"""`)，我们也可以在这里使用。

```
print("""Scrooge's "money bin" is really a huge building.""")
>>> Scrooge's "money bin" is really a huge building. 
```

Enter fullscreen mode Exit fullscreen mode

然而，为了方便起见，在开始用三重引号将所有字符串括起来之前，请记住我说过它们有一些特殊之处。事实上，有两件事。

第一，三重引号是**多行**。换句话说，我可以用它们来做这件事...

```
print("""\ Where do you suppose
    Scrooge keeps his
        Number One Dime?""")
>>> Where do you suppose
>>>    Scrooge keeps his
>>>        Number One Dime? 
```

Enter fullscreen mode Exit fullscreen mode

包括换行符和前导空格在内的所有内容都在三重引号中。唯一的例外是，如果我们使用反斜杠(`\`)来转义某些东西，就像我在开头对换行符所做的那样。我们通常这样做，只是为了让代码更干净。

内置的 [`textwrap`模块](https://docs.python.org/3/library/textwrap.html)有一些处理多行字符串的工具，包括那些允许你有“适当的”缩进而不包含它的工具(`textwrap.dedent`)。

三重引号的另一个特殊用途是创建**文档字符串**，它为模块、类和函数提供基本的文档。

```
def swimInMoney():
    """
    If you're not Scrooge McDuck, please don't try this.
    Gold hurts when you fall into it from forty feet.
    """
    pass 
```

Enter fullscreen mode Exit fullscreen mode

这些经常被误认为是注释，但它们实际上是由 Python 评估的有效代码。docstring 必须出现在它所涉及的任何内容(比如一个函数)的第一行，并且必须用三重引号括起来。稍后，我们可以用两种方法之一访问这个文档字符串，两种方法都显示在这里:

```
# This always works print(swimInMoney.__doc__)

# This works in the interactive shell only help(swimInMoney) 
```

Enter fullscreen mode Exit fullscreen mode

## 特殊字符串类型

我想简单介绍一下 Python 提供的另外两种类型的字符串。实际上，它们并不是真正不同的*类型*的字符串——它们都是类`str`的不可变实例——但是语言对字符串文字的处理略有不同。

**原始字符串**前面有一个`r`，例如...

```
print(r"I love backslashes: \ Aren't they cool?") 
```

Enter fullscreen mode Exit fullscreen mode

在原始字符串中，反斜杠被视为文字字符。原始字符串中没有任何内容可以“转义”。这对你使用什么类型的引号有影响，所以要小心。

```
print("A\nB")
>>> A
>>> B
print(r"A\nB")
>>> A\nB
print(r"\"")
>>> \" 
```

Enter fullscreen mode Exit fullscreen mode

这对于正则表达式模式特别有用，在这种情况下，您可能需要大量反斜杠作为*模式*的一部分，而不是在到达之前由 Python 解释出来。**正则表达式模式总是使用原始字符串。**

**Gotcha Alert:** 如果反斜杠是原始字符串中的最后一个*字符，它仍然会转义右引号，从而导致语法错误。这与 Python 自己的语言词法规则有关，与字符串无关。*

 *字符串的另一种“类型”是一个**格式的字符串**，或 **f 字符串**，这是 Python 3.6 中新增的。它允许你以一种非常漂亮的方式将变量的值插入到一个字符串中，而不需要像我们之前所做的那样麻烦地进行连接或转换。

我们在字符串前面加上一个`f`。在内部，我们可以通过将变量包装在`{...}`中来替换它们。我们像这样把它们放在一起...

```
 netWorth = 52348493767.50
richestDuck = "Scrooge McDuck"
print(f"{richestDuck} has a net worth of ${netWorth}.")
>>> Scrooge McDuck has a net worth of $52348493767.5. 
```

Enter fullscreen mode Exit fullscreen mode

你也不仅仅局限于那些花括号(`{...}`)中的变量！实际上，你可以在里面放任何有效的 Python 代码，包括数学、函数调用、表达式...无论你需要什么。

与更老的`str.format()`方法和`%`格式化(这两者我都不会在这里介绍)相比，f 字符串要快得多。这是因为它们是在代码运行之前*被评估的。*

格式化字符串是由 [PEP 498](https://www.python.org/dev/peps/pep-0498/) 定义的，所以去那里了解更多信息。

# 功能

虽然我们已经了解了基本的东西，但还是让我们来谈谈 Python 函数。我不会再通过重新定义“功能”来炫耀你的智慧。提供一个基本的例子就足够了。

```
def grapplingHook(direction, angle, battleCry):
    print(f"Direction = {direction}, Angle = {angle}, Battle Cry = {battleCry}")

grapplingHook(43.7, 90, "") 
```

Enter fullscreen mode Exit fullscreen mode

表示我们正在定义一个函数，然后我们提供函数名，以及括号中的参数名。*哈欠*

让我们把这个变得更有趣一点。(以下在 Python 3.6 及更高版本中有效。)

```
def grapplingHook(direction: float, angle: float, battleCry: str = ""):
    print(f"Direction = {direction}, Angle = {angle}, Battle Cry = {battleCry}")

grapplingHook(angle=90, direction=43.7) 
```

Enter fullscreen mode Exit fullscreen mode

信不信由你，那是有效的 Python！里面有很多漂亮的小玩意，所以让我们来分解一下。

## 调用函数

当我们调用一个函数时，我们显然可以按照参数在函数定义中出现的顺序来提供参数，就像第一个例子:`grapplingHook(43.7, 90, "")`。

然而，如果我们愿意，我们实际上可以指定将哪些值传递给哪个参数。这使得我们的代码在很多情况下更具可读性:`grapplingHook(angle=90, direction=43.7)`。额外的是，我们实际上不需要按顺序传递参数*，只要它们都有一个值。*

## 默认参数

说到这里，你有没有注意到我在第二次调用中漏掉了`battleCry`的值，它没有对我发火？这是因为我为函数定义中的参数提供了一个默认值**T2...** 

```
def grapplingHook(direction, angle, battleCry = ""): 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，如果没有为`battleCry`提供值，则使用空字符串`""`。实际上，我可以在那里放置任何我想要的值:`"Yaargh"`、`None`或其他任何值。

使用`None`作为默认值是很常见的，因此您可以检查参数是否有指定的值，就像这样...

```
def grapplingHook(direction, angle, battleCry = None):
    if battleCry:
        print(battleCry) 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果你只是想做这样的事情...

```
def grapplingHook(direction, angle, battleCry = None):
    if not battleCry:
        battleCry = ""
    print(battleCry) 
```

Enter fullscreen mode Exit fullscreen mode

...此时，您还不如一开始就给`battleCry`默认值`""`。

**Gotcha Alert:** 默认参数被评估一次，并在所有函数调用之间共享。这对于可变类型有着奇怪的含义，比如空列表`[]`。不可变的东西对于缺省参数来说很好，但是你应该避免可变的缺省参数。

**Gotcha Alert:** 您必须在您的*可选*参数(具有默认值的参数)之前列出所有*必需的*参数(没有默认值的参数)**。`(direction=0, angle, battleCry = None)`不好，因为可选参数`direction`在必需参数`angle`之前。**

## 键入提示和功能注释

如果您熟悉静态类型语言，如 Java 和 C++，这可能会让您有点兴奋...

```
def grapplingHook(direction: float, angle: float, battleCry: str = "") -> None: 
```

Enter fullscreen mode Exit fullscreen mode

但是这并不像你想象的那样！

我们可以在 Python 3.6 和更高版本中提供**类型提示**，它提供的正是:*提示*关于应该传入什么数据类型。同样，冒号(`:`)前的`-> None`部分暗示了返回类型。

然而...

*   如果传递错误的类型，Python 不会抛出错误。
*   Python 不会尝试转换成那种类型。
*   Python 实际上会忽略这些提示，继续前进，就好像它们不存在一样。

那么这有什么意义呢？类型提示确实有一些优点，但是最直接的是*文档*。函数定义现在显示它需要什么类型的信息，这在您键入参数时 IDE 自动显示提示时特别有用。一些 ide 和工具甚至会在你做一些奇怪的事情时警告你，比如，把一个字符串作为一个整数传递给一个类型提示的东西；PyCharm 很擅长这个，事实上！**静态型跳棋**像 Mypy 也这样做。我不打算在这里讨论这些工具，但可以说，它们是存在的。

我应该特别说明一下，上面的那些类型提示是一种类型的**函数注释**，它有各种简洁的用例。这些在 [PEP 3107](https://www.python.org/dev/peps/pep-3107/) 中有更详细的定义。

使用 Python 3.5 中添加的 [`typing`模块](https://docs.python.org/3/library/typing.html)，除了函数定义之外，还有很多方式可以使用类型提示。

## 重载函数？

正如你可能猜到的，因为 Python 是动态类型的，所以我们不太需要重载函数。因此，Python 甚至没有提供它们！你通常只能有一个版本。如果你多次用相同的名字定义一个函数，我们定义的最后一个版本将会*遮蔽*(隐藏)所有其他的。

因此，如果您希望您的函数能够处理许多不同的输入，您将需要利用 Python 的动态类型特性。

```
def grapplingHook(direction, angle, battleCry: str = ""):
    if isinstance(direction, str):
        # Handle direction as a string stating a cardinal direction...
        if direction == "north":
            pass
        elif direction == "south":
            pass
        elif direction == "east":
            pass
        elif direction == "west":
            pass
        else:
            # throw some sort of error about an invalid direction
    else:
        # Handle direction as an angle. 
```

Enter fullscreen mode Exit fullscreen mode

注意，我在上面没有给出类型提示，因为我要处理多种可能性。老实说，这是一个可怕的例子，但你得到了这个想法。

**Gotcha Alert:** 现在，虽然这是完全正确的，但它几乎总是“代码味”——糟糕设计的标志。你应该尽可能地避免`isinstance()`，除非这绝对是解决你问题的最好方法...你的整个职业生涯可能都不会是这样！

## 返回类型

如果您是 Python 的新手，您可能还注意到缺少了一些东西:返回类型。我们实际上并没有直接指定一个:如果需要，我们只是简单地返回一些东西。如果我们想让函数在执行过程中不返回任何东西，我们可以只说`return`。

```
def landPlane():
    if getPlaneStatus() == "on fire":
        return
    else:
        # attempt landing, and then...
        return treasure 
```

Enter fullscreen mode Exit fullscreen mode

那个光秃秃的`return`和说`return None`是一样的，而`return treasure`会返回无论`treasure`的值是多少。(顺便说一句，那段代码行不通，因为我从来没有定义过宝藏。这只是一个愚蠢的例子。)

这个约定让我们很容易处理可选退货:

```
treasure = landPlane()
if treasure:
    storeInMoneyBin(treasure) 
```

Enter fullscreen mode Exit fullscreen mode

真是一件美妙的事情。

**Gotcha Alert:** 您会注意到，本指南中的所有其他功能都缺少`return`语句。一个函数如果到达末尾没有找到`return`语句，会自动返回`None`；没必要在末端钉一个。

## 输入提示和默认值

当使用类型提示时，您可能会尝试这样做...

```
def addPilot(name: str = None):
    if name is not None:
        print(name)
    else:
        print("Who is flying this thing?!?") 
```

Enter fullscreen mode Exit fullscreen mode

这曾经是可以接受的，但现在已经不再被认为是官方正确的了。相反，您应该使用`Optional[...]`来处理这种情况。

```
def addPilot(name: Optional[str] = None): 
```

Enter fullscreen mode Exit fullscreen mode

# 回顾

我希望您对 Python 的类型系统不那么困惑，并且在您的兔子洞之旅中没有撞到太多的椅子。这又是亮点:

*   Python 是动态类型的，这意味着它在运行时计算出对象的数据类型。

*   Python 是**强类型的**，这意味着对于任何给定的数据类型都有严格的规则。

*   Python 中的许多数据类型是**不可变的**，这意味着内存中只存在数据的副本，包含该数据的每个变量都指向一个主副本。另一方面，**的可变**类型则不会这样做。

*   `is`检查操作数是否是同一个对象的**实例**，而`==`比较值。不要混淆它们。

*   系统匈牙利符号(例如`intFoo`)是一个坏主意。请不要这样做。

*   可以用单引号(`'...'`)或双引号(`"..."`)将字符串括起来。

*   三重引号字符串(" " ... """)用于多行字符串。它们也可以用于文档字符串，记录函数、类或模块。

*   原始字符串(`r"\n"`)将任何反斜杠视为文字。这使得它们非常适合正则表达式模式。

*   格式化字符串(`f"1 + 1 = {1+1}"`)让我们神奇地将一些代码的结果代入一个字符串。

*   **可以为函数参数指定默认值**，使其成为**可选参数**。所有可选参数都应该在必需参数之后。

*   **类型提示**让你“提示”什么类型的数据应该被传递到一个函数参数中，但是这将被视为一个建议，而不是一个规则。

像往常一样，您可以在 Python 文档中找到更多关于这些主题的信息。

*   [Python 参考:内置类型](https://docs.python.org/3/library/stdtypes.html)

*   [Python 参考:内置类型-文本序列类型(`str` )](https://docs.python.org/3/library/stdtypes.html#text-sequence-type-str)

*   [Python 参考:内置函数- `isinstance()`](https://docs.python.org/3/library/functions.html#isinstance)

*   [Python 参考:表达式-身份比较](https://docs.python.org/3/reference/expressions.html#is)

*   [Python 引用:`textwrap`](https://docs.python.org/3/library/textwrap.html)

*   [Python 引用:`typing`](https://docs.python.org/3/library/typing.html)

*   [PEP 498:文字字符串插值(f 字符串)](https://www.python.org/dev/peps/pep-0498/)

*   [PEP 3107:函数注释](https://www.python.org/dev/peps/pep-3107/)

*   [Python Wiki:为什么 Python 是一种动态语言，也是一种强类型语言](https://wiki.python.org/moin/Why%20is%20Python%20a%20dynamic%20language%20and%20also%20a%20strongly%20typed%20language)

*   [Ned Batchelder:关于 Python 名称和价值的事实和神话(PyCon 2015)——YouTube](https://www.youtube.com/watch?v=_AEJHKGk9ns)

*   [stack overflow:Python 3.5 中的类型提示是什么](https://stackoverflow.com/a/32558710/472647)

*   [dev.to: 3 个棘手的 Python 细节](https://dev.to/thejessleigh/three-python-nuances-i-wish-id-known-earlier-547c)

* * *

*感谢`deniska`、`grym`和`ikanobori` (Freenode IRC `#python`)提出的修改建议。**
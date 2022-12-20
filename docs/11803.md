# 非常简单的 Python:迭代动力工具

> 原文：<https://dev.to/codemouse92/dead-simple-python-iteration-power-tools-3i3n>

喜欢那些文章吗？买这本书！ [***死简单蟒*** 作者杰森·c·麦克唐纳可从无淀粉出版社购得。](https://nostarch.com/dead-simple-python)

* * *

前一部分或多或少以一个悬念结束。

当我们最后一次离开我们的英雄时，我们已经发现了 Python 中的循环和迭代器，并尝到了它们提供的一点潜力。

我从我的编辑那里得到了一些精选词:“你漏掉了`zip()`和`enumerate()`，这两个词对于任何关于迭代器的讨论来说都是非常重要的*是的，他们是，但是这篇文章有点长。不过，不要害怕——我们即将解决它们，以及更多问题！*

顺便说一句，如果你还没有读过上一节“循环和迭代器”,你现在应该回去读一读了！别担心，我会等的。

用一整篇文章来介绍几个内置函数似乎很奇怪，但是这些函数为 Python 贡献了很多魔力。

# 重游`range`

还记得上一篇文章中的`range()`函数吗？我们简要介绍了如何用它来生成一个数字序列，但是它比乍看起来有更大的威力。

## 开始和停止

使用`range()`的第一个障碍是理解参数:`range(start, stop)`。`start`是*的包容性*；我们从实际数字开始。然而`stop`是*独占*，意思是我们还没到那里就停下来了！

所以，如果我们有`range(1, 10)`，我们得到`[1, 2, 3, 4, 5, 6, 7, 8, 9]`。我们从`1`开始，但我们从未真正到达`10`；我们停了一会儿。

如果我们想在序列中包含`10`，我们需要`range(1, 11)` : `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`。

顺便说一下，如果我们只指定一个参数，比如`range(10)`，它会假设范围的开始是`0`。在这种情况下，我们会得到`[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]`。当`range()`用于控制传统的 for 循环时，你会看到它经常以这种方式使用。

## 蹦蹦跳跳地前进

我最喜欢的关于`range()`的技巧是它可选的*第三个*参数:当你指定`range(start, stop, step)`时，`step`参数允许你增加大于`1`的值*。*

一种用途可能是打印出 7 的所有倍数，从`7`本身到`700`，包括在内:`range(7, 701, 7)`会这么做。(请注意，我将`701`指定为结尾，以确保`700`和*都包含在内。)*

另一个用途可能是打印所有小于 100 的奇数:`range(1, 100, 2)`。

## 存储范围

如果您正在尝试扩大范围，您可能会注意到，这并不符合您的预期:

```
sevens = range(7, 701, 7)
print(sevens) 
```

Enter fullscreen mode Exit fullscreen mode

print 命令打印文字短语`range(7, 701, 7)`。这不是我们想要的！

记住，`range()`返回一个类似于迭代器的*对象(但不完全是)。为了将它直接存储为一个列表，我们需要通过将它包装在`list()`函数:
中，将它*显式地转换为*列表*

```
sevens = list(range(7, 701, 7))
print(sevens) 
```

Enter fullscreen mode Exit fullscreen mode

现在*那个*输出就是我们想要的——前 100 个 7 的倍数的列表！

# 切片

在我们进入所有这些新的迭代优点之前，我想向您介绍一下**扩展索引符号**，它允许我们从有序容器(比如列表)中更有力地选择元素。

说到列表，我们来凑一个:

```
dossiers = ['Sir Vile', 'Dr. Belljar', 'Baron Grinnit', 'Medeva', 'General Mayhem', 'Buggs Zapper', 'Jacqueline Hyde', 'Jane Reaction', 'Dee Cryption'] 
```

Enter fullscreen mode Exit fullscreen mode

不管你是否意识到，你已经知道正常的索引符号。

```
print(dossiers[1])
>>> Dr. Belljar 
```

Enter fullscreen mode Exit fullscreen mode

它返回了`dossiers`容器的第二个元素(索引`1`)。很简单，对吧？几乎所有语言都提供这种行为。

那么，如果我们想要第二个和第三个元素呢？

```
print(dossiers[1:3])
>>> ['Dr. Belljar', 'Baron Grinnit'] 
```

Enter fullscreen mode Exit fullscreen mode

刚刚发生了什么？在扩展索引符号中，我们有三个参数，用冒号分隔:`start`、`stop`和`step`。嘿，听起来熟悉吗？应该是——这些是`range()`使用的相同参数！它们的工作方式也完全一样。(当然，在上面的例子中，我们省略了第三个参数[ `step` ]。)

注意，这个例子打印出了`Dr. Belljar`(索引`1`)和`Baron Grinnit`(索引`2`)，但是*没有* `Medeva`，因为`stop`参数是排他；我们差一点就停下来了。

请注意，`start`必须小于`stop`*才能获得任何结果！不过，有一个例外，我们很快就会谈到。*

现在，如果您希望从第二个档案开始，每隔一个的档案都有*呢？* 

```
print(dossiers[1::2])
>>> ['Dr. Belljar', 'Medeva', 'Buggs Zapper', 'Jane Reaction'] 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到我们没有指定一个`stop`。我们其实不需要！扩展索引符号允许您省略任何参数，只要您有冒号来分隔所有内容。因为第二个参数被省略了，我们只是把多余的`:`放在它应该在的地方。

## 倒退

扩展索引符号通过允许您向后工作，将`(start, stop, step)`逻辑向前推进了一步！不过，这一开始有点脑筋急转弯，所以抓紧了...

```
print(dossiers[-1]) 
```

Enter fullscreen mode Exit fullscreen mode

这样就打印出了**列表中的最后一项**。负数从列表末尾开始计数！这可能感觉有点奇怪，因为我们习惯于在索引中从`0`开始计数，但是负零实际上并不存在，所以我们从`-1`开始。

考虑到这一点，我们如何打印最后三项呢？我们可能会尝试这样做，但实际上是行不通的....

```
print(dossiers[-1:-4])
>>> [] 
```

Enter fullscreen mode Exit fullscreen mode

这将返回一个空列表。为什么？请记住，`start`必须比 `stop`小*，即使在使用负指数时也是如此。所以，我们必须把`-4`当成我们的`start`，从`-4 < -1`开始。* 

```
print(dossiers[-4:-1])
>>> ['Buggs Zapper', 'Jacqueline Hyde', 'Jane Reaction'] 
```

Enter fullscreen mode Exit fullscreen mode

差不多了，但是还有一个问题。是我们的最后一件物品，她在哪里？**记住，`stop`是排他的；我们就差一点停下来。但是我们不能只说`dossiers[-4]`，因为那只会给我们`Buggs Zapper`。而`dossiers[-4:-0]`是无效的。**

解决这个问题的方法是告诉 Python 我们显式地省略了第二个参数:*在第一个参数*后面加一个冒号！

```
print(dossiers[-4:])
>>> ['Buggs Zapper', 'Jacqueline Hyde', 'Jane Reaction', 'Dee Cryption'] 
```

Enter fullscreen mode Exit fullscreen mode

很好，现在我们看到了最后，除了现在我们有太多的信息。我们想要最后的*三个*，所以我们把`-4`改成`-3`...

```
print(dossiers[-3:])
>>> ['Jacqueline Hyde', 'Jane Reaction', 'Dee Cryption'] 
```

Enter fullscreen mode Exit fullscreen mode

她吹了！

说到魔法，如果我们在第三个参数`step`中放一个负数，你认为会发生什么？让我们试试`-1`，前面有两个冒号，表示我们想要整个列表。

```
print(dossiers[::-1])
>>> ['Dee Cryption', 'Jane Reaction', 'Jacqueline Hyde', 'Buggs Zapper', 'General Mayhem', 'Medeva', 'Baron Grinnit', 'Dr. Belljar', 'Sir Vile'] 
```

Enter fullscreen mode Exit fullscreen mode

嘿，那会把所有东西都印反的！事实上，`-1`中的一个`step`颠倒了列表。

现在让我们试试`-2`...

```
print(dossiers[::-2])
>>> ['Dee Cryption', 'Jacqueline Hyde', 'General Mayhem', 'Baron Grinnit', 'Sir Vile'] 
```

Enter fullscreen mode Exit fullscreen mode

这不仅颠倒了列表，而且跳过了所有其他元素。一个否定的`step`的行为和一个肯定的`step`完全一样，除了它反过来工作！

那么，如果我们想把所有东西放在一起呢？也许我们想以相反的顺序列出第二、第三和第四个元素...

```
print(dossiers[2:5:-1])
>>> [] 
```

Enter fullscreen mode Exit fullscreen mode

**Gotcha Alert:** `start`和`stop`必须按照遍历的顺序。如果`step`为正，`start`一定小于`stop`；但是，如果`step`为负，`start`一定大于`stop`！

你可以把它想象成一次摄影旅行的步行指南。告诉你走哪条路，以及你的步幅应该是多少。一到`start`就开始拍照，一遇到`stop`就把相机收起来。

因此，为了解决这个问题，我们需要交换我们的`start`和`stop`。

```
print(dossiers[5:2:-1])
>>> ['Buggs Zapper', 'General Mayhem', 'Medeva'] 
```

Enter fullscreen mode Exit fullscreen mode

**附注:** Python 也提供了`slice()`和`itertools.islice()`函数，它们的行为方式非常相似。然而，它们都比扩展索引符号更受限制，所以最好使用扩展索引符号而不是函数。

# 玩弄迭代

我们将在本节中探索的其余函数与 *iterables* 一起工作。虽然我将在大多数例子中使用列表，但是请记住，您可以在列表中使用任何 iterable，包括`range()`函数。

## `all`和`any`

假设您在一个可迭代的容器(比如一个列表)中获得了一大堆数据，比如一个包含数百个名字的列表。在你把这个列表输入到你的超级聪明的算法中之前，你想通过检查你实际上在每一个单独的元素中有一些字符串值来节省一些处理时间，没有例外。

这就是`all`函数的用途。

```
dossiers = ['Sir Vile', 'Dr. Belljar', 'Baron Grinnit', 'Medeva', 'General Mayhem', 'Buggs Zapper', '', 'Jane Reaction', 'Dee Cryption']
print(all(dossiers))
>>> False 
```

Enter fullscreen mode Exit fullscreen mode

您可能还记得，在 Python 中，空字符串(`''`)的计算结果是`False`。`all()`函数评估每个元素，并确保它返回`True`。即使有一个评估为`False`，函数`all()`也将返回 false。

`any()`以几乎相同的方式工作，除了它只需要一个元素来评估`True`。

乍看之下，这些工具似乎并不十分有用，但是当与其他一些工具结合起来，甚至与列表理解(后面的部分)结合起来时，它们可以节省很多时间！

## `enumerate`

在一个循环中，如果你需要访问一个列表的*值*和它们的*索引*，你可以用`enumerate()`函数来完成。

```
foo = ['A', 'B', 'C', 'D', 'E']

for index, value in enumerate(foo):
    print(f'Element {index} is has the value {value}.') 
```

Enter fullscreen mode Exit fullscreen mode

然而，并不局限于列表。像所有其他函数一样，它作用于任何可迭代对象，对每个返回值进行编号(或枚举)。比如我们可以用在`range()`上。让我们用它来打印出从 10 到 100 的每一个 10 的倍数(`range(10,101,10)`)。我们会一一列举...

```
for index, value in enumerate(range(10,101,10)):
    print(f'Element {index} is has the value {value}.') 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们...

```
Element 0 is has the value 10.
Element 1 is has the value 20.
Element 2 is has the value 30.
Element 3 is has the value 40.
Element 4 is has the value 50.
Element 5 is has the value 60.
Element 6 is has the value 70.
Element 7 is has the value 80.
Element 8 is has the value 90.
Element 9 is has the value 100 
```

Enter fullscreen mode Exit fullscreen mode

嗯，相当有趣。我们可以制作一个简洁的模式，但是我们必须从 *1* 开始枚举，而不是从 *0* 开始。当然，我们可以通过将起始计数作为第二个参数来传递。我们还会稍微调整一下我们的消息，只是为了利用这个模式来做一些更好的事情。

```
for index, value in enumerate(range(10,101,10), 1):
    print(f'{index} times 10 equals {value}') 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行时，我们得到...

```
1 times 10 equals 10
2 times 10 equals 20
3 times 10 equals 30
4 times 10 equals 40
5 times 10 equals 50
6 times 10 equals 60
7 times 10 equals 70
8 times 10 equals 80
9 times 10 equals 90
10 times 10 equals 100 
```

Enter fullscreen mode Exit fullscreen mode

## `filter`

让我们想象一下，我们正在跟踪我们在一堆地点找到的线索的数量，也许把它们存储在一个字典里。对于这个例子，我将借用并修改上一节中的字典...

```
locations = {
    'Parade Ground': 0,
    'Ste.-Catherine Street': 0,
    'Pont Victoria': 0,
    'Underground City': 3,
    'Mont Royal Park': 0,
    'Fine Arts Museum': 0,
    'Humor Hall of Fame': 2,
    'Lachine Canal': 4,
    'Montreal Jazz Festival': 1,
    'Olympic Stadium': 0,
    'St. Lawrence River': 2,
    'Old Montréal': 0,
    'McGill University': 0,
    'Chalet Lookout': 0,
    'Île Notre-Dame': 0
    } 
```

Enter fullscreen mode Exit fullscreen mode

也许我们需要找到所有有线索的地点，忽略其他的。我们将首先编写一个函数来测试一个特定的键值元组对。这可能看起来像是一个荒谬的过度复杂化，但是马上就会有意义

```
def has_clues(pair):
    return bool(pair[1]) 
```

Enter fullscreen mode Exit fullscreen mode

我们将把字典中的每一对作为元组提交给函数，所以`pair[1]`将是*值*(例如`('Underground City', 3)`)。如果数字是`0`，内置函数`bool()`将返回`False`，如果是其他数字，则返回`True`，这正是我们想要的。

我们使用`filter()`函数来缩小字典的范围，使用我们刚刚编写的函数。回想一下上一节，我们需要使用`locations.items()`来获得成对的键和值。

```
for place, clues in filter(has_clues, locations.items()):
    print(place) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们不包括`has_clues`后面的括号。我们将实际函数*作为对象*进行传递！`filter`将进行实际呼叫。

果然，运行这段代码打印出了我们有线索的五个地方(values > `0`)...

```
Underground City
Humor Hall of Fame
Lachine Canal
Montreal Jazz Festival
St. Lawrence River 
```

Enter fullscreen mode Exit fullscreen mode

在本系列的后面，我们将学习 lambdas，匿名函数，它允许我们完全去掉额外的函数。作为预览，下面是实际情况...

```
for place, clues in filter(lambda x:bool(x[1]), locations.items()):
    print(place) 
```

Enter fullscreen mode Exit fullscreen mode

## `map`

`map()`的功能与`filter()`相似，除了不用函数从 iterable 中省略元素，而是用*改变*元素。

假设我们有一个华氏温度的列表:

```
temps = [67.0, 72.5, 71.3, 78.4, 62.1, 80.6] 
```

Enter fullscreen mode Exit fullscreen mode

我们想把它们都转换成摄氏度，所以我们写了一个函数。

```
def f_to_c(temp):
    return round((temp - 32) / 1.8, 1) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用`map()`函数将它应用于`temps`中的每个值，产生一个可以在循环中(或任何地方)使用的迭代器。

```
for c in map(f_to_c, temps):
    print(f'{c}°C') 
```

Enter fullscreen mode Exit fullscreen mode

记住，我们将传递*函数对象* `f_to_c`作为`map()`的第一个参数，所以我们去掉了括号！

运行该循环得到:

```
19.4°C
22.5°C
21.8°C
25.8°C
16.7°C
27.0°C 
```

Enter fullscreen mode Exit fullscreen mode

## `min`和`max`

让我们继续研究这些温度。如果我们想找到列表中*最低的*或*最高的*，我们可以分别使用`min()`或`max()`函数。真的没什么大不了的。

```
temps = [67.0, 72.5, 71.3, 78.4, 62.1, 80.6]
print(min(temps))
>>> 62.1
print(max(temps))
>>> 80.6 
```

Enter fullscreen mode Exit fullscreen mode

**附注:**与 iterables 无关，您也可以使用这些函数来查找您给定的参数列表中的最小或最大参数，例如`min(4, 5, 6, 7, 8)`，它将返回`4`。

## `sorted`

通常，您会想要对一个 iterable 进行排序。Python 通过`sorted()`内置函数非常高效地做到了这一点。

```
temps = [67.0, 72.5, 71.3, 78.4, 62.1, 80.6]
for t in sorted(temps):
    print(t) 
```

Enter fullscreen mode Exit fullscreen mode

产生了...

```
62.1
67.0
71.3
72.5
78.4
80.6 
```

Enter fullscreen mode Exit fullscreen mode

## `reversed`

大多数时候，扩展的索引符号`[::-1]`将允许你反转一个列表或者其他有序的可重复项。但是如果这不是一个选项，您也可以使用`reversed()`函数。

例如，我将把它与刚才的`sorted()`函数结合起来...

```
temps = [67.0, 72.5, 71.3, 78.4, 62.1, 80.6]
for t in reversed(sorted(temps)):
    print(t) 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们...

```
80.6
78.4
72.5
71.3
67.0
62.1 
```

Enter fullscreen mode Exit fullscreen mode

## `sum`

另一个快速的内置函数是`sum()`，它将 iterable 中的所有元素加在一起。自然，这只有在所有元素*可以*相加的情况下才有效。

这种方法的一个用途是找到这些温度的平均值。您可能还记得,`len()`函数告诉我们一个容器中有多少个元素。

```
temps = [67.0, 72.5, 71.3, 78.4, 62.1, 80.6]
average = sum(temps) / len(temps)
print(round(average, 2))
>>> 71.98 
```

Enter fullscreen mode Exit fullscreen mode

## `zip`

还记得之前那个关于地点和线索的例子吗？想象一下，我们不是在字典中，而是在两个列表中获得这些信息

```
locations = ['Parade Ground', 'Ste.-Catherine Street', 'Pont Victoria', 'Underground City', 'Mont Royal Park', 'Fine Arts Museum', 'Humor Hall of Fame', 'Lachine Canal', 'Montreal Jazz Festival', 'Olympic Stadium', 'St. Lawrence River', 'Old Montréal', 'McGill University', 'Chalet Lookout', 'Île Notre-Dame']
clues = [0, 0, 0, 3, 0, 0, 2, 4, 1, 0, 2, 0, 0, 0, 0] 
```

Enter fullscreen mode Exit fullscreen mode

呸！这并不好玩，尽管在现实世界中，我们肯定会以这种方式获得数据。

幸运的是，`zip()`函数可以帮助我们理解这些数据，它通过*使用迭代器将*聚集成元组，给我们`(locations[0], clues[0]), (locations[1], clues[1]), (locations[2], clues[2])`等等。

`zip()`函数甚至不限于两个可迭代的对象；我们给它多少它就能有多少！如果可重复项的长度不一样，那么任何“多余的”都会在最后出现。

当然，在这种情况下，两个列表的长度相同，因此结果相当明显。让我们使用 zip 中的数据创建一个新列表，并将其打印出来。

```
data = list(zip(locations, clues))
print(data) 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们一个与我们之前从字典的`.items()`函数中得到的结构没有什么不同的结构！

```
[('Parade Ground', 0), ('Ste.-Catherine Street', 0), ('Pont Victoria', 0), ('Underground City', 3), ('Mont Royal Park', 0), ('Fine Arts Museum', 0), ('Humor Hall of Fame', 2), ('Lachine Canal', 4), ('Montreal Jazz Festival', 1), ('Olympic Stadium', 0), ('St. Lawrence River', 2), ('Old Montréal', 0), ('McGill University', 0), ('Chalet Lookout', 0), ('Île Notre-Dame', 0)] 
```

Enter fullscreen mode Exit fullscreen mode

事实上，如果我用 lambda 调用我的`filter()`函数，我可以调整它来使用`zip`，让我们纯粹从两个列表开始工作:

```
for place, clues in filter(lambda x:bool(x[1]), zip(locations, clues)):
    print(place) 
```

Enter fullscreen mode Exit fullscreen mode

和以前一样，输出...

```
Underground City
Humor Hall of Fame
Lachine Canal
Montreal Jazz Festival
St. Lawrence River 
```

Enter fullscreen mode Exit fullscreen mode

# [T1】ITER tools](#itertools)

我已经介绍了几乎所有 Python 的内置函数来处理 iterables，但是在`itertools`模块中还有更多。我强烈建议[阅读文档](https://docs.python.org/3/library/itertools.html)以了解更多信息。

# 回顾

与本系列的其他部分相比，这一部分在本质上更像是百科全书，但我希望它能让您体会到使用新的迭代器技巧可以做的一些不可思议的事情。

如果你还在等待那些承诺已久的生成器和列表理解，不要害怕！它们将出现在接下来的章节中。

一如既往，我建议您阅读文档:

*   [Python 文档:内置函数](https://docs.python.org/3/library/functions.html)
*   [Python 词汇表:切片](https://docs.python.org/3/glossary.html#term-slice)
*   [Python 文档:表达式切片](https://docs.python.org/3/reference/expressions.html#slicings)
*   [Python 库:itertools](https://docs.python.org/3/library/itertools.html)
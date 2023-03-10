# 非常简单的 Python:循环和迭代器

> 原文：<https://dev.to/codemouse92/dead-simple-python-loops-and-iterators-15bp>

喜欢那些文章吗？买这本书！ [***死简单蟒*** 作者杰森·c·麦克唐纳可从无淀粉出版社购得。](https://nostarch.com/dead-simple-python)

* * *

还记得你上次丢东西是什么时候吗？

你可能为了找它把你的房子翻了个底朝天。你一个房间一个房间地走过去，而你周围的人问一些毫无意义的问题，比如“你最后一次带他们去哪里了？”(说真的，如果我知道，我就不会找他们了！)优化你的搜索会很棒，但是你的房子没有排序...或者特别有条理，如果你和我一样的话。你陷入了线性搜索。

在编程中，就像在现实生活中一样，我们通常不会以任何有意义的顺序获得数据。我们从一堆乱七八糟的东西开始，我们必须在上面执行任务。搜索无序数据可能是第一个浮现在脑海中的例子，但是您可能还想做数百件其他事情:将所有华氏温度记录转换为摄氏温度，找到所有数据点的平均值，等等。

“是啊，是啊，这就是循环的作用！”

但这是 Python。这里的循环完全不同。他们太棒了，简直就是罪犯。

# 循环概述

让我们把这些无聊的东西扔掉，好吗？

在 Python 中，和大多数语言一样，我们有两个基本循环:`while`和`for`。

## `while`

一个`while`循环是非常基本的。

```
clue = None
while clue is None:
    clue = searchLocation() 
```

Enter fullscreen mode Exit fullscreen mode

只要循环条件(本例中为`clue is None`)的计算结果为`True`，循环的代码就会被执行。

在 Python 中，我们也有几个有用的关键字:`break`立即停止循环，而`continue`跳到循环的下一个*迭代*。

`break`最有用的一个方面是如果我们想要运行相同的代码，直到用户提供有效的输入。

```
while True:
    try:
        age = int(input("Enter your age: "))
    except ValueError:
        print(f"Please enter a valid integer!")
    else:
        if age > 0:
            break 
```

Enter fullscreen mode Exit fullscreen mode

一旦遇到`break`语句，我们就退出循环。诚然，这是一个相当复杂的例子，但它证明了这一点。你也经常看到`while True:`用在游戏循环中。

**Gotcha Alert:** 如果你曾经在任何语言中使用过循环，你应该已经熟悉了*无限循环*。这通常是由一个`while`条件引起的，该条件在循环中总是评估为`True` *和*无`break`语句。

## `for`

来自 Java、C++或许多类似的 ALGOL 风格的语言，您可能熟悉三方`for`循环:`for i := 1; i < 100; i := i + 1`。我不知道你是怎么想的，但是当我第一次遇到它的时候，它把我吓坏了。我现在对它很满意，但是它没有 Python 优雅的简单性，不是吗？

Python 的`for`循环看起来非常不同。与上面伪代码等价的 Python 代码是...

```
for i in range(1,100):
    print(i) 
```

Enter fullscreen mode Exit fullscreen mode

[`range()`](https://docs.python.org/3/library/functions.html#func-range) 是 Python 中一个特殊的“函数”，返回一个序列。(从技术上来说，它根本不是一个函数，但这太学究气了。)

这是 Python 令人印象深刻的地方——它迭代一种特殊类型的序列，称为**可迭代**，我们将在后面讨论。

目前，最容易理解的是，我们可以迭代一个顺序数据结构，比如一个数组(Python 中称为“列表”)。

因此，我们可以这样做...

```
places = ['Nashville', 'Norway', 'Bonaire', 'Zimbabwe', 'Chicago', 'Czechoslovakia']
for place in places:
    print(place)

print("...and back!") 
```

Enter fullscreen mode Exit fullscreen mode

...我们得到了这个...

```
Nashville
Norway
Bonaire
Zimbabwe
Chicago
Czechoslovakia
...and back! 
```

Enter fullscreen mode Exit fullscreen mode

### `for...else`

Python 的循环中还有另一个独特的小技巧:`else`子句！循环完成后，如果*没有*遇到`break`语句，它将运行`else`中的代码。但是，如果手动中断循环，它将完全跳过`else`。

```
places = ['Nashville', 'Norway', 'Bonaire', 'Zimbabwe', 'Chicago', 'Czechoslovakia']
villain_at = 'Mali'

for place in places:
    if place == villain_at:
        print("Villain captured!")
        break
else:
    print("The villain got away again.") 
```

Enter fullscreen mode Exit fullscreen mode

由于“马里”不在列表中，我们看到消息“恶棍再次逃脱。”然而，如果我们将`villain_at`的值改为`Norway`，我们将看到“反派被抓获！”*改为*。

## `do`在哪？

Python 没有`do...while`循环。如果你正在寻找一个，典型的 Python 惯例是使用带有内部`break`条件的`while True:`，就像我们之前演示的那样。

# 几个容器

Python 有许多保存数据的**容器**，或者数据结构。我们不会深入讨论这些问题，但我想快速浏览一下最重要的几个:

## `list`

一个 [`list`](https://docs.python.org/dev/library/stdtypes.html#list) 是一个可变序列(基本上是一个数组)。

它用方括号`[ ]`定义，你可以通过 index 访问它的元素。

```
foo = [2, 4, 2, 3]

print(foo[1])
>>> 4

foo[1] = 42
print(foo)
>>> [2, 42, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

尽管没有严格的技术要求，典型的惯例是列表只包含相同类型的项目(“同类”)。

## `tuple`

[`tuple`](https://docs.python.org/dev/library/stdtypes.html#tuple) 是一个不可变的序列。一旦你定义了它，你*在技术上*不能改变它(回想一下之前不变性的含义)。这意味着在定义了元组之后，就不能添加或删除元素了。

tuple 在括号`( )`中定义，你可以通过 index 访问它的元素。

```
foo = (2, 4, 2, 3)

print(foo[1])
>>> 4

foo[1] = 42
>>> TypeError: 'tuple' object does not support item assignment 
```

Enter fullscreen mode Exit fullscreen mode

与列表不同，标准约定允许元组包含不同类型的元素(“异类”)。

## `set`

一个 [`set`](https://docs.python.org/dev/library/stdtypes.html#set) 是一个*无序的*可变集合，保证不会有重复。记住“无序”的部分很重要:不能保证单个元素的顺序！

集合被定义在花括号`{ }`中，尽管如果你想要一个空集，你必须说`foo = set()`，因为`foo = {}`创建了一个`dict`。你*不能*通过索引访问它的元素，因为它是无序的。

```
foo = {2, 4, 2, 3}

print(foo)
>>> {2, 3, 4}

print(foo[1])
>>> TypeError: 'set' object does not support indexing 
```

Enter fullscreen mode Exit fullscreen mode

对于要添加到集合中的对象，它也必须是**可散列的**。在下列情况下，对象是可散列的:

1.  它定义了方法 [`__hash__()`](https://docs.python.org/3/reference/datamodel.html#object.__hash__) ，该方法返回一个作为整数的**散列**。(见下文)

2.  它定义了比较两个对象的方法 [`__eq__()`](https://docs.python.org/3/reference/datamodel.html#object.__eq__) 。

一个有效的**散列**对于同一个对象(值)应该总是相同的，并且它应该是合理地唯一的，这样另一个对象返回相同的散列就不太常见了。(两个或更多的对象具有相同的哈希被称为*哈希冲突，*，它们仍然会发生。)

## 字典(`dict`)

[`dict`](https://docs.python.org/dev/library/stdtypes.html#dict) 是一种键值数据结构。

它被定义在花括号`{ }`中，使用`:`来分隔键和值。它是无序的，所以不能通过索引访问它的元素；然而，你可以用同样的方式在方括号`[ ]`中指出这个键。

```
foo = {'a' : 1, 'b' : 2, 'c' : 3, 'd' : 4}

print(foo['b'])
>>> 2

foo['b'] = 42
print(foo)
>>> {'a': 1, 'b': 42, 'c': 3, 'd': 4} 
```

Enter fullscreen mode Exit fullscreen mode

只有**可散列的**对象可以用作字典键。(有关哈希能力的更多信息，请参见上文`set`部分。)

## 其他数据结构

除了基本的容器之外，Python 还提供了额外的容器。你可以在 [`collections`内置模块](https://docs.python.org/dev/library/collections.html#module-collections)中找到它们。

# 开箱

Python 语法中有一个重要的部分我们还没有谈到，但是很快就会有用。我们可以将一个容器中的每一个项目分配给一个变量！这叫**拆包**。

当然，我们需要确切地知道要解包多少项才能工作，否则我们会得到一个`ValueError`异常。

让我们看一个基本的例子，使用一个元组。

```
fullname = ('Carmen', 'Sandiego')
first, last = fullname
print(first)
>>> Carmen
print(last)
>>> Sandiego 
```

Enter fullscreen mode Exit fullscreen mode

秘制调味汁在第二行。我们可以列出多个变量来赋值，用逗号分隔。Python 会在等号右边的*解包容器，从左到右依次给变量赋值。*

**Gotcha Alert:** 记住，`set`是无序的！虽然从技术上讲，你可以用集合来做这件事，但是你不能确定什么值被赋给什么变量。它不保证按任何顺序排列；集合通常以排序的顺序解包它们的值，这是偶然的，不能保证！

# [T2`in`的事情](#the-raw-in-endraw-thing)

Python 提供了一个漂亮的关键字`in`，用于检查是否在容器中找到了特定的元素。

```
places = ['Nashville', 'Norway', 'Bonaire', 'Zimbabwe', 'Chicago', 'Czechoslovakia']

if 'Nashville' in places:
    print("Music city!") 
```

Enter fullscreen mode Exit fullscreen mode

这适用于许多容器，包括列表、元组、集，甚至字典键(但不适用于字典值)。

如果你想让你的自定义类支持`in`操作符，你只需要定义`__contains__(self, item)`方法，它应该返回`True`或`False`。(参见[文档](https://docs.python.org/3/reference/datamodel.html#object.__contains__))。

# 迭代器

Python 的循环被设计成与我之前提到的 **iterables** 一起工作。这些是可以使用**迭代器**迭代的对象。

蟋蟀的声音。

好吧，我们从头开始。一个 Python 容器对象，比如一个`list`，也是一个**可迭代的**，因为它定义了一个`__iter__()`方法，该方法返回一个迭代器对象。

一个定义为`__next__()`方法的**迭代器**，在容器迭代器的情况下，返回下一项。甚至无序的容器，比如`set()`，也可以使用迭代器遍历。

当`__next__()`没有返回任何东西时，它抛出一个名为 [`StopIteration`](https://docs.python.org/3/library/exceptions.html#StopIteration) 的特殊异常。这可以用典型的`try...except`来捕捉和处理。

例如，让我们再来看一个遍历`list`的`for`循环...

```
dossiers = ['The Contessa', 'Double Trouble', 'Eartha Brute', 'Kneemoi', 'Patty Larceny', 'RoboCrook', 'Sarah Nade', 'Top Grunge', 'Vic the Slick', 'Wonder Rat']

for crook in dossiers:
    print(crook) 
```

Enter fullscreen mode Exit fullscreen mode

`dossiers`是一个`list`对象，是可迭代的。当 Python 到达`for`循环时，它做三件事:

1.  调用`iter(dossiers)`，依次执行`dossiers.__iter__()`。这将返回一个迭代器对象，我们称之为`list_iter`。这个迭代器对象将被循环使用。

2.  对于循环的每次迭代，它调用`next(list_iter)`，T0 执行`list_iter.__next__()`，并将返回值赋给`crook`。

3.  如果迭代器抛出了特殊异常`StopIteration`，循环结束，我们退出。

如果我在一个`while True:`循环中重写该逻辑，可能会更容易理解这一点...

```
list_iter = iter(dossiers)
while True:
    try:
        crook = next(list_iter)
        print(crook)
    except StopIteration:
        break 
```

Enter fullscreen mode Exit fullscreen mode

如果你尝试两个循环，你会看到他们做完全相同的事情！

理解了`__iter__()`、`__next__()`和`StopIteration`异常是如何工作的，你现在可以让你自己的类可迭代了！

**Hack Alert:** 虽然将迭代器类与可迭代类分开定义是相当典型的做法，但这并不是必须的！只要这两个方法都在你的类中定义了，并且`__next__()`行为适当，你就可以只定义`__iter__()`到`return self`。

值得注意的是迭代器本身是可迭代的:它们有一个返回`self`的`__iter__()`方法。

## 字典的奇案

假设我们有一本想要使用的字典...

```
locations = {
    'Parade Ground': None,
    'Ste.-Catherine Street': None,
    'Pont Victoria': None,
    'Underground City': None,
    'Mont Royal Park': None,
    'Fine Arts Museum': None,
    'Humor Hall of Fame': 'The Warrant',
    'Lachine Canal': 'The Loot',
    'Montreal Jazz Festival': None,
    'Olympic Stadium': None,
    'St. Lawrence River': 'The Crook',
    'Old Montréal': None,
    'McGill University': None,
    'Chalet Lookout': None,
    'Île Notre-Dame': None
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果我们只想看到其中的每一项，我们只需使用一个`for`循环。所以，这应该是可行的，对吧？

```
for location in locations:
    print(location) 
```

Enter fullscreen mode Exit fullscreen mode

哎呀！这只向我们显示了*键*，而不是值。绝对不是我们想要的，是吗？这到底是怎么回事？

`dict.__iter__()`返回一个`dict_keyiterator`对象，它执行类名所暗示的任务:遍历键，但不遍历值。

为了让*得到*的键和值，我们需要调用`locations.items()`，它返回`dict_items`对象。`dict_items.iter()`返回一个`dict_itemiterator`，它将把字典中的每个键值对作为一个元组返回。

**遗留注意:**如果你正在使用 Python 2，你应该调用`locations.iteritems()`来代替。

还记得之前我们谈到的拆包吗？事实上，我们将每一对都作为一个元组来处理，这意味着我们可以*将它们分解成两个变量。* 

```
for key, value in locations.items():
    print(f'{key} => {value}') 
```

Enter fullscreen mode Exit fullscreen mode

它打印出以下内容:

```
Parade Ground => None
Ste.-Catherine Street => None
Pont Victoria => None
Underground City => None
Mont Royal Park => None
Fine Arts Museum => None
Humor Hall of Fame => The Warrant
Lachine Canal => The Loot
Montreal Jazz Festival => None
Olympic Stadium => None
St. Lawrence River => The Crook
Old Montréal => None
McGill University => None
Chalet Lookout => None
Île Notre-Dame => None 
```

Enter fullscreen mode Exit fullscreen mode

啊，这还差不多！现在我们可以处理数据了。例如，我可能想在另一本字典中记录重要的信息。

```
information = {}

for location, result in locations.items():
    if result is not None:
        information[result] = location

# Win the game! print(information['The Loot'])
print(information['The Warrant'])
print(information['The Crook'])

print("Vic the Slick....in jaaaaaaaaail!") 
```

Enter fullscreen mode Exit fullscreen mode

这将找到战利品，搜查令，骗子，并列出他们在适当的顺序:

```
Lachine Canal
Humor Hall of Fame
St. Lawrence River
Vic the Slick....in jaaaaaaaaail! 
```

Enter fullscreen mode Exit fullscreen mode

看，循环和迭代器的强大威力！

# 你自己的迭代器

我之前已经提到过，你可以创建自己的 iterables 和 iterators，但是展示比讲述更好！

假设我们想在手边保留一个代理列表，这样我们就可以通过他们的代理编号来识别他们。但是，有*一些*代理商我们就谈不上了。我们可以通过将代理 id 和名称存储在一个字典中，然后维护一个分类代理的列表来非常容易地实现这一点。

**Gotcha Alert:** 记住我们对类的讨论，Python 中实际上没有私有变量这种东西。如果您真的想要保守秘密，请使用行业标准的加密和安全实践，或者至少不要将您的 API 暴露给任何恶意的操作人员。；)

首先，这个类的基本结构如下:

```
class AgentRoster:
    def __init__(self):
        self._agents = {}
        self._classified = []

    def add_agent(self, name, number, classified=False):
        self._agents[number] = name
        if classified:
            self._classified.append(name)

    def validate_number(self, number):
        try:
            name = self._agents[number]
        except KeyError:
            return False
        else:
            return True

    def lookup_agent(self, number):
        try:
            name = self._agents[number]
        except KeyError:
            name = "<NO KNOWN AGENT>"
        else:
            if name in self._classified:
                name = "<CLASSIFIED>"
        return name 
```

Enter fullscreen mode Exit fullscreen mode

我们可以继续测试，只是为了子孙后代:

```
roster = AgentRoster()

roster.add_agent("Ann Tickwitee", 2539634)
roster.add_agent("Ivan Idea", 1324595)
roster.add_agent("Rock Solid", 1385723)
roster.add_agent("Chase Devineaux", 1495263, True)

print(roster.validate_number(2539634))
>>> True
print(roster.validate_number(9583253))
>>> False

print(roster.lookup_agent(1324595))
>>> Ivan Idea
print(roster.lookup_agent(9583253))
>>> <NO KNOWN AGENT>
print(roster.lookup_agent(1495263))
>>> <CLASSIFIED> 
```

Enter fullscreen mode Exit fullscreen mode

太好了，这完全符合预期！现在，如果我们希望能够遍历整个字典，也许是作为一些令人敬畏的代码的一部分，在一个时髦的全球地图上显示他们的名字和当前位置，会怎么样呢？

然而，我们不想直接访问`roster._agents`字典，因为这将忽略这个类的整个“分类”方面。我们该如何处理？

正如我之前提到的，我们*可以*让这个类也作为它自己的迭代器，这意味着它有一个`__next__()`方法。在这种情况下，我们只会返回`self`。然而，这是非常简单的 Python，所以让我们跳过令人讨厌的简单的东西，实际上创建一个单独的迭代器类。

在这个例子中，我将把字典转换成元组列表，这将允许我使用索引。(记住，字典是无序的。)我也会算出有多少特工*不是*的机密。所有这些逻辑都属于`__init__()`方法，当然:

```
class AgentRoster_Iterator:

    def __init__(self, container):
        self._roster = list(container._agents.items())
        self._classified = container._classified
        self._max = len(self._roster) - len(self._classified)
        self._index = 0 
```

Enter fullscreen mode Exit fullscreen mode

要成为迭代器，类必须有一个`__next__()`方法；这是唯一的要求！记住，一旦我们没有更多的数据要返回，这个方法就需要抛出`StopException`。

我将`AgentRoster_Iterator`的`__next__()`方法定义如下:

```
class AgentRoster_Iterator:

    # ...snip... 
    def __next__(self):
        if self._index == self._max:
            raise StopIteration
        else:
            r = self._roster[self._index]
            self._index += 1
            return r 
```

Enter fullscreen mode Exit fullscreen mode

现在我们返回到`AgentRoster`类，我们需要添加一个`__iter__()`方法来返回一个适当的迭代器对象。

```
class AgentRoster:

    # ...snip... 
    def __iter__(self):
        return AgentRoster_Iterator(self) 
```

Enter fullscreen mode Exit fullscreen mode

这一点点魔法就足够了，现在我们的`AgentRoster`类的行为完全符合循环的预期！这个代码...

```
roster = AgentRoster()

roster.add_agent("Ann Tickwitee", 2539634)
roster.add_agent("Ivan Idea", 1324595)
roster.add_agent("Rock Solid", 1385723)
roster.add_agent("Chase Devineaux", 1495263, True)

for number, name in roster:
    print(f'{name}, id #{number}') 
```

Enter fullscreen mode Exit fullscreen mode

...生产...

```
Ann Tickwitee, id #2539634
Ivan Idea, id #1324595
Rock Solid, id #1385723 
```

Enter fullscreen mode Exit fullscreen mode

# 向前看

我听到后面的皮托尼斯塔说:“等等，等等，我们还没完呢！你甚至还没有触及列表理解！”

Python 确实在循环和迭代器的基础上增加了一个全新的魔法级别，它有一个叫做**生成器**的特殊工具。这种类型的类提供了另一个不可思议的工具，叫做**理解**，它就像一个创建数据结构的美味紧凑的循环。

我还故意跳过了`zip()`和`enumerate()`这样的优点，它们使得循环和迭代更加强大。我本想把它们放在这里，但我不想让文章太长。(已经在推了。)我稍后也会谈到这些。

我看到你们中的一些人已经兴奋得发抖了，但是，唉，你们必须等到下一篇文章才能了解更多。

# 回顾

让我们回顾一下本节中最重要的概念:

*   只要条件评估为`True`，循环`while`就会运行。
*   您可以使用`break`关键字来中断循环，或者使用`continue`关键字跳到下一个迭代。
*   一个`for`循环遍历一个 iterable(一个可以被遍历的对象)，比如一个 list。
*   `range()`函数返回一个可迭代的数字序列，可以在`for`循环中使用，例如`for i in range(1, 100)`。
*   Python 没有`do...while`循环。使用带有显式 break 语句的`while True:`循环。
*   Python 有四种基本的数据结构，或者说**容器**:
    *   **列表**是可变的、有序的、顺序的结构...基本上就是数组。
    *   **元组**是不可变的、有序的、顺序的结构。思考列表，但不能修改内容。
    *   **集合**是可变的、无序的结构，保证永远不会有任何重复的元素。他们只能储存易损坏的物品。
    *   **字典**是可变的无序结构，存储键值对。你通过关键字*而不是索引*来查找条目。只有可哈希对象可以用作键。
*   您可以使用约定`a, b, c = someContainer`将容器的值**解包**到多个变量中。左边变量的数量和右边容器中元素的数量*必须*相同！
*   您可以使用关键字`in`快速检查一个元素是否在一个容器中。如果您希望您的类支持这一点，请定义`**contains**()`方法。
*   Python 的容器是 **iterables** 的例子:它们返回可以遍历其内容的迭代器。iterable 对象总是通过它的`**iter**()`方法返回 iterator 对象。
*   一个**迭代器**对象总是有一个`**next**()`方法，它返回一个值。容器迭代器的`**next**()`方法将返回容器中的下一个元素。当没有更多的东西要返回时，迭代器抛出`StopIteration`异常。

Ned Batchelder 有一个关于迭代器和循环的惊人演讲，题目是[“像本地人一样循环”](https://nedbatchelder.com/text/iter.html)。强烈推荐去看看！

另外，像往常一样，一定要阅读文档。使用循环、容器和迭代器还可以做更多的事情。

*   [Python Wiki: While 循环](https://wiki.python.org/moin/WhileLoop)
*   [Python Wiki: For 循环](https://wiki.python.org/moin/ForLoop)
*   [Python 参考:复合语句-`while`语句](https://docs.python.org/3/reference/compound_stmts.html#the-while-statement)
*   [Python 参考:复合语句-`for`语句](https://docs.python.org/3/reference/compound_stmts.html#the-for-statement)

*   [Python 参考:内置函数- `range()`](https://docs.python.org/3/library/functions.html#func-range)

*   [Python 参考:数据模型- `object.__contains__()`](https://docs.python.org/3/reference/datamodel.html#object.__contains__)

*   [Python 教程:数据结构](https://docs.python.org/3/tutorial/datastructures.html)

*   [Python 引用:内置类型- `list`](https://docs.python.org/dev/library/stdtypes.html#list)

*   [Python 引用:内置类型- `set`](https://docs.python.org/dev/library/stdtypes.html#set)

*   [Python 引用:内置类型- `tuple`](https://docs.python.org/dev/library/stdtypes.html#tuple)

*   [Python 引用:内置类型- `dict`](https://docs.python.org/dev/library/stdtypes.html#dict)

*   [Python 标准库:`collections`](https://docs.python.org/dev/library/collections.html#module-collections)

*   [Python 教程:类-迭代器](https://docs.python.org/3/tutorial/classes.html#iterators)

*   [Python 参考:异常-停止迭代](https://docs.python.org/3/library/exceptions.html#StopIteration)

*   StackOverflow:迭代器、可迭代和迭代到底是什么？

* * *

*感谢`deniska`、`grym`和`ikanobori` (Freenode IRC `#python`)提出的修改建议。*
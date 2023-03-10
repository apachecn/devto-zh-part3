# 两个排序列表的故事:比较继承和组合

> 原文：<https://dev.to/bgschiller/a-tale-of-two-sorted-lists-comparing-inheritance-and-composition-5fm3>

正如建议所说，“偏爱组合胜过继承”是简洁而没有争议的。不幸的是，它也没有多大意义，除非你已经知道它的意思。我们将探索解决同一问题的两种不同方法:继承和组合。乍一看，继承的例子看起来更正确，也更短一些。我想说服你，作文的方法更正确。

### 什么成分？什么是遗传？

看一个例子会更容易理解，但是对你所关注的有一些概念是有用的。组合和继承都是依靠现有代码而不是复制逻辑的技术。一会儿我们会看到这在代码中是什么样子，但是作为一种简化，你可以这样说

*   继承用于建模“是-是”关系，例如:“一个`PoissonRandomNumberGenerator`是一个`RandomNumberGenerator`的(特定情况)”
*   组合用于建模“Has-a”关系，例如:“A `PoissonRandomNumberGenerator` has-a(利用)a `RandomNumberGenerator`”

对于随机数的例子，这两种选择都是合理的。然而，这句格言引导我们走向写作。你可能还不知道为什么会这样，但是我们会明白的:)

### 构建:一个排序列表

我们想要制作一个排序列表数据结构。通过对元素进行排序，我们可以做出“这个列表包含`x`吗？”操作*真快*。插入和删除操作会慢一点，但是我们愿意为一些用例付出这样的代价。

我们将使用 Python 标准库的`bisect`模块来完成大部分繁重的工作。`bisect`函数告诉你用什么索引来查找一个条目，假设列表已经排序。`insort`函数(双关语:“插入已排序的”)在合适的位置执行对已排序列表的插入，以保持列表的排序。这正是我们所需要的！这意味着我们几乎可以完全专注于我们两种方法之间的差异。

下面是我们的排序列表应该如何表现:

```
sl = SortedList()
for elem in (12, 4, 15, 2):
    sl.insert(elem)
print(4 in sl) # True print(18 in sl) # False print(', '.join(elem for elem in sl)) # 2, 4, 12, 15 
```

你可能会说:“为什么要写一个`SortedList`类呢？难道`insort`和`bisect`函数不是一起做我们需要的事情吗？它们做了正确的动作，但是如果我们在没有包装类的情况下使用它们，我们就有点像在没有防护栏的情况下编码。记住:那些函数*假设*列表从一开始就是排序的——它们不会检查它！我们必须注意*永远不要*在使用我们想要使用`bisect`和`insort`的列表时不小心破坏了任何代码中的排序顺序，否则我们会在代码中引入非常棘手的错误。这是如何伤害我们的一个例子:

```
from bisect import bisect, insort
keep_me_sorted = []

insort(keep_me_sorted, 4)
insort(keep_me_sorted, 10)

# Danger! did the writers of include_fav_numbers know they have to
# keep the list in sorted order? Will they continue to remember in the future? include_fav_numbers(keep_me_sorted)

# if they forgot, bisect and insort will no longer work properly on the list 
```

我们的快速搜索只有在列表被排序的情况下才有效，这很难仅仅通过规则来保证。我们可以在使用`bisect`或`insort`之前对列表进行排序。这可以工作，但它会抹去我们从使用`bisect`中获得的所有性能优势！相反，让我们使用*封装*来保护列表不被破坏排序顺序的修改。

### 实现 InheritanceSortedList

使用继承来制作一个`SortedList`看起来是一个自然的选择。一个`SortedList`就是——一个单子！完了，完了。让我们从那里开始。

```
from bisect import insort, bisect

class InheritanceSortedList(list):
    def insert(self, elem):
        insort(self, elem)

    def __contains__(self, elem):
        """
        __contains__ is the method that is called when
        code like `x in collection` runs. This is where
        we get our performance boost over a regular list.
        """
        # Find the index where elem *might* be found
        ix = bisect(self, elem)
        return ix != len(self) and self[ix] == elem 
```

因此...完事了吗？这似乎正是我们想要的。这个列表适用于简单的例子，但是我们将在后面看到它没有提供我们想要的封装。首先，我们来看看作文选项。

### 创建组合排序列表

为了使用 Composition，我们将创建一个包含列表作为隐藏属性的对象。

```
from bisect import insort, bisect

class CompositionSortedList:
    def __init__(self):
        self._lst = []

    def insert(self, elem):
        insort(self._lst, elem)

    def __contains__(self, elem):
        ix = bisect(self._lst, elem)
        return ix != len(self._lst) and self._lst[ix] == elem 
```

好的，比继承的例子长一点，但不算太差。不幸的是，这个没有通过我们的测试。具体来说，`(elem for elem in sl)`将失败，并出现一个错误:

> TypeError:非序列上的迭代

类似于`__contains__`魔术方法，对象可以使用`__iter__`方法定义如何循环访问它们。在制作`InheritanceSortedList`的时候，我们通过继承`list`的方式得到了`__iter__`的定义。对于组合，我们必须定义一个:

```
class CompositionSortedList:
    # ...
    def __iter__(self):
        return iter(self._lst) 
```

幸运的是，这并不复杂。我们基本上说“要迭代一个`CompositionSortedList`，只需迭代隐藏的`_lst`位”。这是足够普遍的，它有一个特殊的名字。我们可以说一个`CompositionSortedList` *将*委托给它的`_lst`属性。我们稍后会看到如何让它变得更好。

### InheritanceSortedList 分解

不管我们愿不愿意，`InheritanceSortedList`已经继承了`list`的所有行为——包括我们不想要的东西。例如，我们有一个`append`方法:

```
lst = InheritanceSortedList()

lst.append(4)
lst.append(5)
print(5 in lst)  # prints 'False' 
```

我们还有一个我们没有预料到的事情:

```
lst = InheritanceSortedList([5, 3, 2])
print(2 in lst)  # prints 'False' 
```

`__init__`和`append`方法直接来自于`list`父类。他们不*知道*他们应该维持有序的秩序！他们正在做正确的事情来初始化或附加到一个常规的`list`，而不管事实上它是一个错误的`SortedList`。

我们不需要太多的工作就可以解决这些问题:

```
class InheritanceSortedList(list):
    def __init__(self, iterable=()):
        super().__init__(self)
        for elem in iterable:
            self.insert(elem)
    def append(self, elem):
        raise RuntimeError("Appending to a sorted list could break sorted-order. Use insert instead")
    def extend(self, iterable):
        raise RuntimeError("extending a sorted list could break sorted-order. Use insert instead")
    # ...are there others? 
```

但我觉得这很脆弱。我们是否已经捕获并修复了所有可能破坏排序顺序的方法？即使我们有，未来版本的`list`能增加一个新的吗？这有时被称为[脆弱基类问题](https://en.wikipedia.org/wiki/Fragile_base_class)，其中对父类(`list`)的更改可能会无意中破坏派生类(`InheritedSortedList`)。

拥有一堆除了引发异常之外什么都不做的方法也让人感觉很混乱。我希望我们可以跳过定义这些。让我们看看它是如何与合成一起工作的。

### 组合排序列表建立

在某种程度上，`CompositionSortedList`有相反的问题。列表上有一堆我们*希望*提供的功能。就像我们对`__iter__`所做的一样，我们可以创建委托给私有列表属性的方法:

```
class CompositionSortedList:
    # ...
    def __iter__(self):
        return self._lst.__iter__()
    def __len__(self):
        return self._lst.__len__()
    def __getitem__(self, ix):
        return self._lst.__getitem__(ix)
    def __reversed__(self):
        return self._lst.__reversed__() 
```

与继承实现相比，在继承实现中，我们不得不选择那些会破坏我们的排序顺序的方法，我们在中选择- *我们希望可用的行为。这保护我们免受脆弱的基类问题！*

可能是我个人的喜好，但是我感觉构图也是*更清晰*。我不必挖掘继承层次结构来找出方法是在哪里定义的。使用组合的类上的任何功能在类的定义上都是*。*

### 额外积分:一种更好的委托模式

我开始考虑是否有更简洁的方法来编写所有这些委托方法。它们是相当重复的，似乎应该有可能摆脱它们。我编写了一个名为 [superdelegate](https://github.com/bgschiller/superdelegate) 的库，使之成为可能。下面是使用超级委托的情况

```
from superdelegate import SuperDelegate, delegate_to

class CompositionSortedList(SuperDelegate):
    # ...
    __iter__ = __len__ = __getitem__ = __reversed__ = delegate_to('_lst') 
```

我觉得挺好的！

### 你什么时候会使用继承？

构图更安全、更清晰，而且(在`superdelegate`的帮助下)几乎一样简洁。那么你什么时候会使用继承呢？继承在我能想到的少数情况下是合适的(如果有其他推荐请发邮件给我)。

*   当你真的要接受*父类的所有*行为(甚至是我们还不知道的未来变化)。我在 [bgschiller/citrus](https://github.com/bgschiller/citrus) 中这样做是因为我希望派生的对象能够在所有情况下都被视为父类型。
*   当框架希望你定制行为的时候。Django 基于阶级的观点就是一个很好的例子。
*   当出现一些奇怪的元类时。想想 SqlAlchemy 的 DeclarativeBase 模型，或 WTForms 类。

### 参考文献

*   [https://code . active state . com/recipes/577197-sorted collection/](https://code.activestate.com/recipes/577197-sortedcollection/)
*   [https://docs.python.org/2/library/bisect.html](https://docs.python.org/2/library/bisect.html)
# Python 集:什么、为什么和如何

> 原文：<https://dev.to/wilfredinni/python-sets-what-why-and-how-5a1n>

Python 配备了几个内置的数据类型来帮助我们组织数据。这些结构包括列表、字典、元组和**集合**。

来自 Python 3 文档:

> 集合是一个没有*重复元素*的*无序集合*。基本用途包括*成员资格测试*和*消除重复条目*。Set 对象还支持数学运算，如*并集*、*交集*、*差集*和*对称差集*。

在本文中，我们将回顾并查看上述定义中列出的每一个元素的示例。让我们马上开始，看看我们如何创建它们。

### 初始化一组

创建集合有两种方法:一种是为内置函数`set()`提供元素列表，另一种是使用花括号`{}`。

使用`set()`内置函数:
初始化器械包

```
>>> s1 = set([1, 2, 3])
>>> s1
{1, 2, 3}
>>> type(s1)
<class 'set'> 
```

Enter fullscreen mode Exit fullscreen mode

使用花括号`{}`
初始化集合

```
>>> s2 = {3, 4, 5}
>>> s2
{3, 4, 5}
>>> type(s2)
<class 'set'>
>>> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这两个选项都有效。当我们想要的是空的时，问题就来了:

```
>>> s = {}
>>> type(s)
<class 'dict'> 
```

Enter fullscreen mode Exit fullscreen mode

没错，如果我们使用空的花括号，我们将得到一个字典而不是一个集合=)

为了简单起见，现在应该提一下，本文中提供的所有例子都将使用一位数整数，但是集合可以包含 Python 支持的所有[散列](https://docs.python.org/3/glossary.html#term-hashable)数据类型。换句话说，整数、字符串和元组，而不是像*列表*或*字典* :
这样的*可变*项

```
>>> s = {1, 'coffee', [4, 'python']}
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list' 
```

Enter fullscreen mode Exit fullscreen mode

既然你已经知道了如何创建一个集合，以及它可以包含什么类型的元素，那么让我们继续来看看*为什么*我们应该在我们的工具箱中一直拥有它们。

## 为什么要使用它们

编写代码时，您可以用多种方式来完成。有些被认为是相当糟糕的，而另一些则是清晰、简洁和可维护的。或者“ [*蟒*](http://docs.python-guide.org/en/latest/writing/style/) ”。

从[搭便车指南到 Python](http://docs.python-guide.org/en/latest/) :

> 当一个经验丰富的 Python 开发人员(Pythonista)称部分代码不是“Python 化的”时，他们通常意味着这些代码行没有遵循通用的准则，并且没有以被认为是最好的(听起来:最可读的)方式表达其意图。

让我们开始探索 Python 集合不仅可以帮助我们提高可读性，还可以加快我们程序的执行时间。

### 元素的无序集合

首先:你不能使用索引来访问集合元素。

```
>>> s = {1, 2, 3}
>>> s[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'set' object does not support indexing 
```

Enter fullscreen mode Exit fullscreen mode

或者用切片修改:

```
>>> s[0:2]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'set' object is not subscriptable 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果我们需要的是删除重复项，或者做像合并列表(联合)这样的数学运算，我们可以，而且*应该*总是使用集合。

我不得不提到，当迭代时，列表胜过集合，所以如果这是你所需要的，你最好选择列表。为什么？嗯，这篇文章并不打算解释集合的内部工作原理，但是如果你感兴趣的话，这里有几个链接，你可以在那里读到它:

*   [时间复杂度](https://wiki.python.org/moin/TimeComplexity)
*   [set()是如何实现的？](https://stackoverflow.com/questions/3949310/how-is-set-implemented)
*   [Python 集合 vs 列表](https://stackoverflow.com/questions/2831212/python-sets-vs-lists)
*   使用集合而不是列表组合来确保唯一条目列表有什么优点或缺点吗？

### 无重复项

在写这篇文章的时候，我一直在想，我一直在使用 *for* 循环和 *if* 语句来检查和删除列表中的重复元素。我的脸变红了，想起我不止一次地写过这样的话:

```
>>> my_list = [1, 2, 3, 2, 3, 4]
>>> no_duplicate_list = []
>>> for item in my_list:
...     if item not in no_duplicate_list:
...             no_duplicate_list.append(item)
...
>>> no_duplicate_list
[1, 2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

还是用了一个列表理解:

```
>>> my_list = [1, 2, 3, 2, 3, 4]
>>> no_duplicate_list = []
>>> [no_duplicate_list.append(item) for item in my_list if item not in no_duplicate_list]
[None, None, None, None]
>>> no_duplicate_list
[1, 2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

但是没关系，这些都不重要了，因为我们现在已经有了这些设备

```
>>> my_list = [1, 2, 3, 2, 3, 4]
>>> no_duplicate_list = list(set(my_list))
>>> no_duplicate_list
[1, 2, 3, 4]
>>> 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们使用 *timeit* 模块，查看删除重复项时列表和集合的执行时间:

```
>>> from timeit import timeit
>>> def no_duplicates(list):
...     no_duplicate_list = []
...     [no_duplicate_list.append(item) for item in list if item not in no_duplicate_list]
...     return no_duplicate_list
...
>>> # first, let's see how the list perform: >>> print(timeit('no_duplicates([1, 2, 3, 1, 7])', globals=globals(), number=1000))
0.0018683355819786227 
```

Enter fullscreen mode Exit fullscreen mode

```
>>> from timeit import timeit
>>> # and the set: >>> print(timeit('list(set([1, 2, 3, 1, 2, 3, 4]))', number=1000))
0.0010220493243764395
>>> # faster and cleaner =) 
```

Enter fullscreen mode Exit fullscreen mode

我们不仅用集合比用列表理解写了*更少的行*，我们还获得了更多的*可读*和*执行*代码。

注意:记住集合是无序的，所以不能保证在将它们转换回列表时，元素的顺序会保持不变。

来自 Python 的[禅:](https://www.python.org/dev/peps/pep-0020/)

> 漂亮总比难看好。
> 
> 显性比隐性好。
> 
> 简单胜于复杂。
> 
> 平不如巢。

集合不就是漂亮的，露骨的，简单的，平面的吗？=)

### 会员测试

每当我们使用一个 *if* 语句来检查一个元素是否在一个列表中，你就在做一个成员测试:

```
my_list = [1, 2, 3]
>>> if 2 in my_list:
...     print('Yes, this is a membership test!')
...
Yes, this is a membership test! 
```

Enter fullscreen mode Exit fullscreen mode

而且集合在做的时候比列表更有性能:

```
>>> from timeit import timeit
>>> def in_test(iterable):
...     for i in range(1000):
...             if i in iterable:
...                     pass
...
>>> timeit('in_test(iterable)',
... setup="from __main__ import in_test; iterable = list(range(1000))",
... number=1000)
12.459663048726043 
```

Enter fullscreen mode Exit fullscreen mode

```
>>> from timeit import timeit
>>> def in_test(iterable):
...     for i in range(1000):
...             if i in iterable:
...                     pass
...
>>> timeit('in_test(iterable)',
... setup="from __main__ import in_test; iterable = set(range(1000))",
... number=1000)
0.12354438152988223
>>> 
```

Enter fullscreen mode Exit fullscreen mode

注:以上测试来自[这个](https://stackoverflow.com/questions/2831212/python-sets-vs-lists) StackOverflow 线程。

所以，如果你在一个大的列表中做这样的比较，如果你把列表转换成一个集合，应该会加快你的速度。

## 如何使用它们

现在你知道什么是集合，为什么你应该使用它们，让我们快速浏览一下，看看我们如何修改和操作它们。

### 添加元素

根据要添加的元素数量，我们必须在`add()`和`update()`方法之间进行选择。

`add()`将添加单个元素:

```
>>> s = {1, 2, 3}
>>> s.add(4)
>>> s
{1, 2, 3, 4} 
```

Enter fullscreen mode Exit fullscreen mode

和`update()`多个一:

```
>>> s = {1, 2, 3}
>>> s.update([2, 3, 4, 5, 6])
>>> s
{1, 2, 3, 4, 5, 6} 
```

Enter fullscreen mode Exit fullscreen mode

记住，集合删除重复。

### 删除元素

如果您想在代码试图移除不在集合中的元素时得到提醒，请使用`remove()`。否则，`discard()`提供了一个很好的替代:

```
>>> s = {1, 2, 3}
>>> s.remove(3)
>>> s
{1, 2}
>>> s.remove(3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 3 
```

Enter fullscreen mode Exit fullscreen mode

`discard()`不会引发任何错误:

```
>>> s = {1, 2, 3}
>>> s.discard(3)
>>> s
{1, 2}
>>> s.discard(3)
>>> # nothing happens! 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用`pop()`随机丢弃一个元素:

```
>>> s = {1, 2, 3, 4, 5}
>>> s.pop()  # removes an arbitrary element 1
>>> s
{2, 3, 4, 5} 
```

Enter fullscreen mode Exit fullscreen mode

或`clear()`删除集合中的所有值:

```
>>> s = {1, 2, 3, 4, 5}
>>> s.clear()  # discard all the items >>> s
set() 
```

Enter fullscreen mode Exit fullscreen mode

### 工会()

`union()`或`|`将创建一个新的集合，包含我们提供的集合中的所有元素:

```
>>> s1 = {1, 2, 3}
>>> s2 = {3, 4, 5}
>>> s1.union(s2)  # or 's1 | s2' {1, 2, 3, 4, 5} 
```

Enter fullscreen mode Exit fullscreen mode

### 交点()

`intersection`或`&`将返回一个只包含所有元素的集合:

```
>>> s1 = {1, 2, 3}
>>> s2 = {2, 3, 4}
>>> s3 = {3, 4, 5}
>>> s1.intersection(s2, s3)  # or 's1 & s2 & s3' {3} 
```

Enter fullscreen mode Exit fullscreen mode

### 差异()

使用`diference()`或`-`，用“s1”中的值而不是“s2”中的值创建一个新集合:

```
>>> s1 = {1, 2, 3}
>>> s2 = {2, 3, 4}
>>> s1.difference(s2)  # or 's1 - s2' {1} 
```

Enter fullscreen mode Exit fullscreen mode

### 对称 _ 差分()

`symetric_difference`或`^`将返回集合之间不通用的所有值。

```
>>> s1 = {1, 2, 3}
>>> s2 = {2, 3, 4}
>>> s1.symmetric_difference(s2)  # or 's1 ^ s2' {1, 4} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我希望读完这篇文章后，你知道什么是集合，如何操作它们的元素以及它们可以执行的操作。知道什么时候使用 set 肯定会帮助你写出更干净的代码，加速你的程序。

如果你有任何疑问，请留下评论，我会很乐意尝试回答他们。另外，不要忘记，如果你已经理解了集合，它们在 [Python 备忘单](https://www.pythoncheatsheet.org/)中有自己的[位置](https://www.pythoncheatsheet.org/#sets)，在那里你可以快速查阅并更新你已经知道的内容。
# 如何在 Python 中检查列表是否为空

> 原文：<https://dev.to/renegadecoder94/how-to-check-if-a-list-is-empty-in-python-1mb7>

为了开始交叉发布，我想与大家分享另一篇简短的 Python 文章。今天，我们将学习如何在 Python 中检查列表是否为空。

## 问题介绍

到目前为止，我们已经使用了很多数据结构，比如列表和字典。在列表方面，我们已经学会了[如何对两个列表的元素求和](https://therenegadecoder.com/code/how-to-sum-elements-of-two-lists-in-python/)，我们甚至学会了[如何将两个列表转换成一个字典](https://therenegadecoder.com/code/how-to-convert-two-lists-into-a-dictionary-in-python/)。然而，在所有这些列表操作中，我们从未真正讨论过如何检查列表是否为空。

检测空列表是 Python 开发人员需要学习的一项重要技能。它允许我们处理在试图操作空列表时可能发生的错误。例如，我们都知道当我们试图索引一个空列表时会发生什么:

```
Python 3.6.3 (v3.6.3:2c5fed8, Oct  3 2017, 17:26:49) [MSC v.1900 32 bit (Intel)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> my_list = list()
>>> my_list[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range 
```

Enter fullscreen mode Exit fullscreen mode

如果我们碰巧有一个做一些索引的函数，也许为空列表添加一个保护性检查是个好主意。毕竟，对于任何一个程序员来说，处理边缘情况都是一项重要的技能。

## 方案

在 Python 中检查列表是否为空时，有几种解决方案。让我们深入探讨几个。

### 根据长度检查列表是否为空

好了，检查列表是否为空的第一个方法是验证列表的长度是否为零。作为一个初学 Java 的人，我觉得这个解决方案非常直观。看一看:

```
my_list = list()
if len(my_list) == 0:
    pass  # the list is empty 
```

Enter fullscreen mode Exit fullscreen mode

这种方法的缺点是它不是很 pythonic 化。毕竟，Python 社区对其习惯用法非常严格，所以如果可能的话，避免这种方法可能是个好主意。

### 通过直接比较检查列表是否为空

正如 [vorsprung](https://dev.to/vorsprung/comment/9192) 所提到的，另一个解决方案是直接比较一个列表和一个空列表:

```
my_list = list()
if my_list == []:
    pass  # the list is empty 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案和以前的版本一样有效，但是也不被认为是 pythonic。原因是 Python 有两种不同类型的类似列表的集合:元组和列表。元组是不可变的列表，比较空元组和空列表`() == []`，将返回 false。

无论如何，这两种解决方案都是有效的。在下一节中，我们将讨论社区接受列表空性测试。

### 通过类型灵活性检查列表是否为空

动态类型是 Python 的一个有趣的特性，因为它允许变量采用多种形式。虽然具有静态类型背景的人可能会觉得这令人沮丧，但其他人会发现 Python 中的动态类型有其好处。这些额外的好处之一是，[空序列的计算结果为假](https://www.python.org/dev/peps/pep-0008/#programming-recommendations)。

那么，这对于代码来说意味着什么呢？这意味着我们可以像对待布尔值一样对待一个列表。例如:

```
my_list = list()
if not my_list:
    pass  # the list is empty 
```

Enter fullscreen mode Exit fullscreen mode

现在，这可能看起来不是很直观，但它是公认的 PEP 8 标准。换句话说，这是我们应该在产品代码中使用的方法。

## 稍微重述一下

今天的主题是如何在 Python 中检查一个列表是否为空。和往常一样，在这一部分，我们对上面分享的解决方案做一点回顾:

```
my_list = list()

# Check if a list is empty by its length if len(my_list) == 0:
    pass  # the list is empty 
# Check if a list is empty by direct comparison if my_list == []:
    pass  # the list is empty 
# Check if a list is empty by its type flexibility **preferred method** if not my_list:
    pass  # the list is empty 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们结束了。再次感谢你的来访。希望这是有帮助的！
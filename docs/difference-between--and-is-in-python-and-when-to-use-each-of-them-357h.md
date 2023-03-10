# Python 中` == '和` is '的区别，以及何时使用它们

> 原文：<https://dev.to/emadmokhtar/difference-between--and-is-in-python-and-when-to-use-each-of-them-357h>

在 Python 中有很多[比较运算符](https://realpython.com/python-operators-expressions/#comparison-operators)；您总是使用它们来检查代码中的某些内容，并让代码根据比较做出决定

在本文中，我想深入讨论两个运算符`==`来检查等式，以及`is`来检查等式。

## `==`符

相等比较运算符。

```
>>> 1 == 1
True 
```

在上面的代码中，我们检查 int **1** 的值是否与 int **1** 的值相等，换句话说，我们在这里检查值是否相等。

```
class Number:
    def __init__(self, number):
        self.number = number

>>> Number(1) == Number(1)
False # Wooot 
```

在上面的代码中，我们检查两个对象的值是否相等。他们不平等。Python 中的类🐍拥有 [dunder 方法](https://wiki.python.org/moin/DunderAlias)来实现特殊逻辑的魔法。

[通过 GIPHY](https://giphy.com/gifs/shia-labeouf-12NUbkX6p4xOO4)

例如，`__init__()`是启动一个新对象或类的构造函数的 dunder 方法。对于我们上面的例子，有一个用于检查等式的 dunder 方法`__eq__()`，如果我们正确实现了它，代码中的表达式应该可以工作。

```
class Number:
    def __init__(self, number):
        self.number = number

    def __eq__(self, another_number):
        if isinstance(another_number, Number):
            return self.number == another_number.number
        return self.number == another_number

>>> Number(1) == Number(1)
True 
>>> Number(1) == 1
True 
```

好了，现在我们班有了正确的等式逻辑。等式逻辑在`__eq__`方法中实现。它也在 Python 内置类型中实现，如整数。

```
import inspect
from pprint import pprint

# 1 is object of int class pprint(inspect.getmembers(1))
pprint(inspect.getmembers(int)) 
```

## `is`符

身份检查操作员

```
>>> none_obj = None
>>> none_obj is None
True
>>> number_one = 1
>>> number_one is 1 
```

在 Python 中，创建的每个对象都会存储一个对它的引用，就像上面的代码 **none_obj** 中存储的是对 **None** 的引用，而 **number_one** 对象存储的是对 **1** 的引用。让我们看看 Python 是如何做到这一点的。

```
>>> none_obj = None
>>> id(none_obj)
4304631824
>>> id(None)
4304631824
>>> id(number_one)
4304948352
>>> id(1)
4304948352 
```

当您创建一个存储对象或值的新变量时，该变量名存储对它的引用，因此如果您创建另一个变量来存储同一对象，Python 会存储一个指向该对象的指针，而不是创建一个新的指针。

```
a = Number(1)
b = a
>>> id(a), id(b)
(4520963656, 4520963656)
>>> a is b
True 
```

技术上来说，你检查身份的时候，Python 检查`id(a) == id(b)`，意思是:
对象 **a** 和 **b** 是指同一个对象吗？因此，如果它们指的是同一个对象，那么答案将是 **True** 。

## 结论

如果需要检查值是否相等，使用`==`操作符，如果需要检查两个对象是否相同，使用`is`。

快乐的蟒蛇😉
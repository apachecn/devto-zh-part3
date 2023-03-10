# Python 数据模型再探

> 原文：<https://dev.to/shaikhul/python-data-model-revisited-2fno>

Python 的双下划线(又名 dunder)方法(例如`__len__`)魔法的方法可不止这些。python 的每个[内置](https://docs.python.org/3/library/functions.html)函数都有对应的 dunder 方法。官方将它们记录在 python 数据模型下。试试谷歌`python data model`，第一个结果会指向 python [文档](https://docs.python.org/3/reference/datamodel.html)

这有什么大不了的？有没有想过为什么 python 的`len`方法几乎可以接受任何东西？

```
>>> len(range(5))
5 
```

Enter fullscreen mode Exit fullscreen mode

现在想象这样一个简单的类

```
>>> class Foo:
...     pass
... 
```

Enter fullscreen mode Exit fullscreen mode

如果我给`len`方法提供一个`Foo`实例，它会返回什么？

```
>>> f = Foo()
>>> len(f)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: object of type 'Foo' has no len() 
```

Enter fullscreen mode Exit fullscreen mode

它抛出一个异常`TypeError`，抱怨`Foo`没有`len`。对，如果我们在`Foo`对象上实现一个`__len__`并返回任何东西，python 的`len`将为该对象寻找一个对应的`__len__`方法。

```
>>> class Foo:
...     def __len__(self):
...             return 5
...
>>> f = Foo()
>>> len(f)
5 
```

Enter fullscreen mode Exit fullscreen mode

嘣！现在它工作了，并通过实现`__len__`方法返回我们希望它返回的内容。

其他最常见的邓德方法有

#### 字符串表示法

```
In [11]: class Point:
    ...:     def __init__(self, x, y):
    ...:         self.x = x
    ...:         self.y = y
    ...:
    ...:     def __str__(self):
    ...:         return 'Stringified Point: ({x}, {y})'.format(x=self.x, y=self.y)
    ...:
    ...:     def __repr__(self):
    ...:         return 'Representing Point: ({x}, {y})'.format(x=self.x, y=self.y)
    ...:

In [12]: p = Point(2,3)

In [13]: p
Out[13]: Representing Point: (2, 3)

In [14]: str(p)
Out[14]: 'Stringified Point: (2, 3)' 
```

Enter fullscreen mode Exit fullscreen mode

#### 运算符重载

我们可以实现二进制算术运算(例如`+`、`-`等)，实现特定的数据方法。

```
In [21]: class Point:
    ...:     # continuing from previous example   
    ...:     def __add__(self, other):
    ...:         return Point(self.x + other.x, self.y + other.y)
    ...:

In [22]: p1 = Point(1, 2)

In [23]: p2 = Point(3,5)

In [26]: p3
Out[26]: Representing Point: (4, 7) 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于模拟数字类型的信息，请查看[文档](https://docs.python.org/3/reference/datamodel.html#emulating-numeric-types)

#### 物体比较

我们可以实现`__eq__`来比较对象。

```
In [36]: class Point:
    ...:     # continuing from previous example
    ...:     def __eq__(self, other):
    ...:         # custom comparison
    ...:         return self.x == other.x and self.y == other.y
    ...:
In [37]: p1 = Point(2, 3)

In [38]: p2 = Point(2, 3)

In [39]: p1 == p2
Out[39]: True

In [40]: p3 = Point(2, 4)

In [41]: p1 == p3
Out[41]: False 
```

Enter fullscreen mode Exit fullscreen mode

要检查其他对象定制，请检查[文档](https://docs.python.org/3/reference/datamodel.html#basic-customization)

#### 对象属性访问

```
In [44]: class Point:
    ...:     # continuing from previous example
    ...:     def __getattr__(self, attr):
    ...:         if (attr == 'X'):
    ...:             return self.x
    ...:         elif (attr == 'Y'):
    ...:             return self.y
    ...:         else:
    ...:             raise AttributeError('Point object has no attribute {attr}'.format(attr=attr))
    ...:
In [45]: p = Point(2, 3)

In [46]: p.x
Out[46]: 2

In [47]: p.X
Out[47]: 2

In [48]: p.Xx
--------------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-48-9a49f22e192d> in <module>
---------> 1 p.Xx

<ipython-input-44-f5f17ce1e8d6> in __getattr__(self, attr)
     23             return self.y
     24         else:
--------> 25             raise AttributeError('Point object has no attribute {attr}'.format(attr=attr))
     26

AttributeError: Point object has no attribute Xx 
```

Enter fullscreen mode Exit fullscreen mode

更多属性访问可以在[文档](https://docs.python.org/3/reference/datamodel.html#customizing-attribute-access)中找到

#### 仿真容器/集合

我们还可以将对象视为集合，并执行接受集合的各种函数调用。

```
In [49]: class Point:
    ...:     # continuing from previous example
    ...:     def __getitem__(self, index):
    ...:         if index == 0:
    ...:             return self.x
    ...:         elif index == 1:
    ...:             return self.y
    ...:         else:
    ...:             raise IndexError('No item found with index {index}'.format(index=index))
    ...:
In [50]: p = Point(2, 3)

In [51]: p[0]
Out[51]: 2

In [53]: p[1]
Out[53]: 3

In [54]: p[2]
--------------------------------------------------------------------------------
IndexError                                Traceback (most recent call last)
<ipython-input-54-21c545b39f61> in <module>
---------> 1 p[2]

<ipython-input-49-6dd2485f38f5> in __getitem__(self, index)
     31             return self.y
     32         else:
--------> 33             raise IndexError('No item found with index {index}'.format(index=index))
     34

IndexError: No item found with index 2 
```

Enter fullscreen mode Exit fullscreen mode

检查[文档](https://docs.python.org/3/reference/datamodel.html#emulating-container-types)以获得模拟集合的 dunder 方法的完整列表。

**python 数据模型最好的地方就是程序员几乎不需要调用 dunder 方法(除了`__init__`和少数其他定制方法)，python 解释器会根据需要调用这些方法。**

我已经把完整的`Point`类放到了这个[要点](https://gist.github.com/shaikhul/316687d44952187875d4c10051f33190)中，你可以随意使用所有其他的邓德方法，并与我们分享你所学到的。
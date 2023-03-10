# 掌握 Python 的 __getitem__ 和切片

> 原文：<https://dev.to/beef_and_rice/mastering-python-s-getitem-and-slicing-2cn0>

在 Python 的数据模型中，当你写`foo[i]`时，`foo.__getitem__(i)`被调用。你可以在你的类中实现`__getitem__`方法，并定义切片的行为。

示例:

```
class Foo:
    def __getitem__(self, key):
        print(key)
        return None

foo = Foo()
foo[1] # => print(1) foo["aaa"] # => print("aaa") 
```

如您所知，Python 可以切片更复杂的对象。

```
# comma separated slicing d = {}
d[1, "hello"] = 3

# colon separated slicing ls = [0, 1, 2, 3]
ls[:2]
ls[3:]
ls[2:4]
ls[::2]
ls[1:4:-1]
ls[:]
ls[::]

# both comma and colon import numpy as np
a = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
a[:2, 1:3] 
```

让我们检查一下传递给`__getitem__`参数的是什么类型的对象。

# 逗号分隔切片

首先，我们检查逗号分隔切片。
为了进行检查，我们定义了`Inspector`类。

```
class Inspector:
    def __getitem__(self, key):
        return key 
```

然后，我们可以检查切片。

```
a = Inspector()
a[1] # => 1: int 
a[1, 2] # => (1, 2): tuple 
a[1, 2, 3] # => (1, 2, 3): tuple 
a[] # => SyntaxError 
```

这种行为非常类似于赋值表达式的右边。

```
t = 1 # 1: int t = 1, 2 # (1, 2): tuple t = 1, 2, 3 # (1, 2, 3): tuple t = # SyntaxError 
```

需要更多例子吗？

```
a[(1, 2, 3)] # (1, 2, 3): tuple assert a[(1, 2, 3)] == a[1, 2, 3]
t = (1, 2, 3)
u = 1, 2, 3
assert t == u

# tuple of one element. a[1,] # (1,): tuple t = 1, # (1,): tuple a[(1,)] # (1,): tuple t = (1,) # (1,): tuple 
# empty tuple a[()] # (): tuple t = () # (): tuple 
t = 1, 2
assert a[t] == a[1, 2]

a[(1, 2), 3] # ((1, 2), 3): tuple t = (1, 2), 3 # ((1, 2), 3): tuple 
```

但是，带星号的项和 yield 表达式不允许用于切片。

```
t = *iter([]), 1 # (1,): tuple a[*iter([]), 1] # SyntaxError: invalid syntax 
def g():
    t = yield 1 # Valid.
    a = Inspector()
    a[yield 1] # SyntaxError: invalid syntax 
```

## 逗号分隔切片的结论

与赋值表达式的右侧相同，逗号分隔的切片被解释为元组。

# 冒号分隔切片

让我们检查结肠分离切片。

```
# As same as above part. class Inspector:
    def __getitem__(self, key):
        return key

a = Inspector() 
```

冒号分隔的切片构成一个切片对象。

```
a[1:2:3] # => slice(1, 2, 3) assert a[1:2:3] == slice(1, 2, 3) 
```

切片对象的文档字符串为:

```
slice(stop)
slice(start, stop[, step])

Create a slice object.  This is used for extended slicing (e.g. a[0:10:2]). 
```

冒号分隔切片的通知有`[start:]`、`[start::]`、`[:stop]`、`[:stop:]`、`[::step]`、`[start:stop]`、`[start:stop:]`、`[start::step]`、`[:stop:step]`和`[start:stop:step]`。

```
start = 1
stop = 2
step = 3

assert a[start:] == a[start::] == slice(start, None, None) == slice(start, None)

assert a[:stop] == a[:stop:] == slice(None, stop, None) == slice(stop)

assert a[start:stop] == a[start:stop:] == slice(start, stop, None) == slice(start, stop)

assert a[:stop:step] == slice(None, stop, step)

assert a[start:stop:step] == slice(start, stop, step) 
```

现在，我们了解了冒号分隔切片和切片对象之间的关系。

开始、停止和步进类型不仅仅是整数。任何类型都可以接受。

```
a[[]:{}:"abc"] # => slice([], {}, "abc")

class MyClass:
    pass

a[MyClass():MyClass()] # => slice(MyClass(), MyClass()) 
```

## 切片对象的处理

切片对象具有开始、停止和步进属性。

```
sl = slice(1, '2', [3])

sl.start # => 1
sl.stop # => '2'
sl.step # => [3]

sl = slice('stop')

sl.start # => None
sl.stop # => 'stop'
sl.step # => None 
```

切片对象有`indices(len)`方法。

文档字符串为:

```
S.indices(len) -> (start, stop, stride)

Assuming a sequence of length len, calculate the start and stop
indices, and the stride length of the extended slice described by
S. Out of bounds indices are clipped in a manner consistent with the
handling of normal slices. 
```

如果 start、stop、step 属性不为 None 且在[0，len]范围内，则 indices 方法返回，否则返回合适的值。

它有助于实现与列表对象相同的切片。

```
li = [i for i in range(10)]
def make_list(start, stop, stride):
    t = []
    if stride > 0:
        while start < stop:
            t.append(start)
            start += stride
    else:
        while start > stop:
            t.append(start)
            start += stride
    return t

start, stop, stride = a[6:].indices(10)
# start, stop, stride = 6, 10, 1 assert li[6:] == make_list(start, stop, stride) == [6, 7, 8, 9]

start, stop, stride = a[:7:2].indices(10)
# start, stop, stride = 0, 7, 2 assert li[:7:2] == make_list(start, stop, stride) == [0, 2, 4, 6]

start, stop, stride = a[:].indices(10)
# start, stop, stride = 0, 10, 1 assert li[:] == make_list(start, stop, stride) == [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

start, stop, stride = a[15:].indices(10)
# start, stop, stride = 10, 10, 1 assert li[15:] == make_list(start, stop, stride) == []

start, stop, stride = a[-3:].indices(10)
# start, stop, stride = 7, 10, 1 assert li[-3:] == make_list(start, stop, stride) == [7, 8, 9]

start, stop, stride = a[:5:-2].indices(10)
# start, stop, stride = 9, 5, 2 assert li[:5:-2] == make_list(start, stop, stride) == [9, 7] 
```

## 结肠分离切片的结论

冒号分隔切片返回切片对象。它有三个属性。开始，停止，迈步。如果您想像 list 对象的实现一样实现切片，slice.indices 方法很有帮助。

# 既用逗号又用冒号分隔切片

在这种情况下，它被解释为片的元组。

```
class Inspector:
    def __getitem__(self, key):
        return key

a = Inspector()

a[1:2,3:4] # (slice(1, 2, None), slice(3, 4, None)) a[1,2:3,4] # (1, slice(2, 3, None), 4) a[1:(2,3):4] # slice(1, (2, 3), 4) 
```

# `__index__`法

要成为切片大师，方法是非常重要的。

```
import numpy as np

i = np.int32(3)
print(i) # => '3' print(type(i)) # => 'np.int32' 
```

这段代码中，`ia`是整数吗？

是的。它是一个整数，但不是 Python 的内置`int`类型对象。

可能会给你的`__getitem__`实现带来一些麻烦。您可能想转换成 Python 的内置`int`类型。

你可能认为`int(i)`返回内置的`int`类型。是的，在 Python 中常用。但是，如果`i`是浮点数，你可能不想转换成整数值。在这种情况下，`__index__`方法是有用的。

> `object.__index__(self)`
> 调用实现`operator.index()`，每当 Python 需要无损转换数值对象为整数对象时(比如在切片中，或者在内置的`bin()`、`hex()`、`oct()`函数中)。此方法的存在表明数字对象是整数类型。必须返回一个整数。
> 
> 注意
> 为了有一个连贯的整数类型类，当`__index__()`被定义时`__int__()`也应该被定义，并且两者应该返回相同的值。
> (Python 文档[数据模型](https://docs.python.org/3/reference/datamodel.html#object.__index__))

numpy.int{8，16，32，64}，uint{8，16，32，64}实现了`__index__`方法，它返回 Python 的内置`int`类型。`float`，numpy 的浮点数不实现`__index__`方法。

当您想要将变量转换为 int 类型以便切片时，您应该使用此方法。

也可以使用`operator.index`功能。当`__index__`未实现时`foo.__index__()`提高`AttributeError`，`operator.index(foo)`提高`TypeError`。

```
import operator
import numpy as np

i = np.int32(3)
i.__index__() # Returns 3: int operator.index(i) # Returns 3: int 
# builtins int type also implements __index__ method. i = 3
i.__index__() # Returns 3: int operator.index(i) # Returns 3: int 
# floating point type doesn't implement __index__ method. i = 3.0
i.__index__() # AttributeError: 'float' object has no attribute '__index__' operator.index(i) # TypeError: 'float' object cannot be interpreted as an integer 
```

# `__setitem__`和`__delitem__`

`__setitem__`和`__delitem__`是与`__getitem__`类似的方法。

评估`foo[key] = value`时调用`foo.__setitem__(key, value)`。评估`del foo[key]`时，调用`foo.__delitem__(key)`。您可以为您的类实现这些方法。

# 结论

在这篇文章中，我们学习了以下内容。

*   `foo[key]`来电`foo.__getitem__(key)`
*   `foo[key] = value`来电`foo.__setitem__(key, value)`
*   `del foo[key]`来电`foo.__delitem__(key)`
*   `foo[k1, k2]`来电`foo.__getitem__((k1, k2))`
*   `foo[k1:k2:k3]`来电`foo.__getitem__(slice(k1, k2, k3))`
*   整数类型实现`__index__`方法

这些知识对于实现您的可订阅类非常有用。
# Python 生成器表达式和列表理解到底是什么？

> 原文：<https://dev.to/vmesel/what-the-heck-are-python-generator-expressions-and-list-comprehensions-55hh>

你有没有想过用一行表达式初始化一个列表？在 Python 中使用列表理解是完全可能的。但是有时你暂时不会使用列表存储值，你只是想初始化这些值以备将来使用...这是 or 生成器表达式的一个简单用例。

PS:如果你读过来自 [@ramalhoorg](https://dev.to/ramalhoorg) 的《流利的 Python》，这里并没有什么新的东西，但是你可以把这篇文章分享给一个朋友，这样她将能够了解更多关于 Python 的知识。

PS 2:感谢 [@ramalhoorg](https://dev.to/ramalhoorg) 书中的例子，它们非常有用，其中一些就用在这里！

## 什么是列表理解？

List comprehensions 是一个列表表达式，它创建一个包含已有值的列表，请看下面的例子:

```
>>> my_incredible_list = [x for x in range(5)]
>>> my_incredible_list
[0, 1, 2, 3, 4] 
```

这种对列表的理解就好像你在做一个 for 循环，把值追加到一个列表中。

```
>>> my_incredible_list = []
>>> for x in range(5):
>>>     my_incredible_list.append(x)
>>> my_incredible_list
[0, 1, 2, 3, 4] 
```

有了列表理解，你可以生成列表，其中元组迭代另外两个列表。

```
>>> foods = "apple banana sausages".split() # We declare a list here with foods >>> beverages = "coca-cola pepsi guarana".split() # We declare a list of beverages >>> [(food, beverage) for food in foods for beverage in beverages]
[('apple', 'coca-cola'), ('apple', 'pepsi'), ('apple', 'guarana'), ('banana', 'coca-cola'), ('banana', 'pepsi'), ('banana', 'guarana'), ('sausages', 'coca-cola'), ('sausages', 'pepsi'), ('sausages', 'guarana')] 
```

## 什么是生成器表达式？

所以生成器表达式就像一个列表理解，但是它不在内存中存储它的值，它只在生成器被调用时生成你迭代的值。看看下面的例子:

```
>>> my_incredible_list = (x for x in range(5))
>>> my_incredible_list
<generator object <genexpr> at 0x7f14149a3db0> 
```

要检查生成器上的值，可以使用 for 循环迭代生成器，或者使用`next()`操作符检查值。

```
 >>> # Using the next operator
>>> next(my_incredible_list)
0
>>> next(my_incredible_list)
1
>>> next(my_incredible_list)
2 
>>> next(my_incredible_list)
3
>>> next(my_incredible_list)
4
>>> next(my_incredible_list)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
>>>
>>> # Using a for loop
>>> for item in my_incredible_list:
...     print(item)
... 
0
1
2
3
4 
```

## 检查两者的装配情况，看看有什么不同

### 来自 Python 反汇编程序的汇编指令

```
>>> import dis
>>> my_incredible_list = [x for x in range(5)]
>>> dis.dis(my_incredible_list)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/lib/python3.6/dis.py", line 67, in dis
    type(x).__name__)
TypeError: don't know how to disassemble list objects
>>> my_incredible_list = (x for x in range(5))
>>> dis.dis(my_incredible_list)
  1           0 LOAD_FAST                0 (.0)
        >>    2 FOR_ITER                10 (to 14)
              4 STORE_FAST               1 (x)
              6 LOAD_FAST                1 (x)
              8 YIELD_VALUE
             10 POP_TOP
             12 JUMP_ABSOLUTE            2
        >>   14 LOAD_CONST               0 (None)
             16 RETURN_VALUE
>>> 
```

正如我们在这里看到的，列表理解的反汇编是不可能的，因为我们不能反汇编一个数据结构，但是我们仍然可以反汇编生成器表达式。

当我们运行`dis.dis(<genexpr> right here)`时，我们能够看到表达式被当作一个队列，我们产生值，然后我们弹出它，所以它不再被调用。

如果我们调用`dis.dis(<listcomp> here)`，我们能够看到使用 listcomp 表达式创建列表的运行过程:

```
>>> dis.dis("[x for x in range(5)]")
  1           0 LOAD_CONST               0 (<code object <listcomp> at 0x7f14149c84b0, file "<dis>", line 1>)
              2 LOAD_CONST               1 ('<listcomp>')
              4 MAKE_FUNCTION            0
              6 LOAD_NAME                0 (range)
              8 LOAD_CONST               2 (5)
             10 CALL_FUNCTION            1
             12 GET_ITER
             14 CALL_FUNCTION            1
             16 RETURN_VALUE 
```

正如我们所看到的，MAKE_FUNCTION 部分正在为列表生成值，我们实例化函数`range`，向它加载一个值(5)，我们调用它，然后我们将它保存到列表中，而不是每次调用生成器时都生成值。
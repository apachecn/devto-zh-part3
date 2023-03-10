# python 中的内存管理——第 2 部分

> 原文：<https://dev.to/pradhvan/memory-managment-in-python--part-2-56dc>

在上一部分中，我们研究了变量是如何在 Python 中存储的，以及 Python 如何通过引用计数和垃圾收集器来处理内存管理。如果你还没有检查出来，并想在这里是[链接](https://dev.to/pradhvan/memory-managment-in-python--part-1-1c98)。

在这一部分，我们将深入探讨引用计数是如何工作的，以及在不同的情况下它是如何增加或减少的。让我们从上次停下来的地方开始，python 中的每个对象都有三样东西

```
Type
Reference Count
Value 
```

引用计数是一个值，显示一个对象被其他名称(变量)引用(指向)了多少次。引用计数有助于垃圾收集器释放空间，以便程序可以高效运行。尽管我们可以增加或减少引用计数的值，并可以使用名为 getrefcount()的内置函数来检查该值。

我们来看一小段代码:

```
import sys

a = []

# Two reference one from the variable and one from the getrefcount() function 
print(sys.getrefcount())

2 
```

虽然这些例子看起来很棒，一切似乎都在工作，但我确实有点欺骗你，首先是不是所有的引用计数值都从 0 开始，所以如果你用不同的值做相同的例子，输出可能会不同。引用计数值是根据两个因素计算的对象在字节码中被使用的次数和它被引用的次数这也包括你以前的程序。

让我们看另一个例子:

```
 import sys

a = 100

print(sys.getrefcount(a))

4

b = 100

print(sys.getrefcount(b))

5

c = 100

print(sys.getrefcount(c))

6 
```

当更多的变量引用同一个值时，引用计数增加。但是，当我们考虑像列表和常量这样的容器对象时，情况就不一样了。

```
import sys

ex_list = [a,b,c,d]

print(sys.getrefcount(a))

8

print(sys.getrefcount(b))

9

print(sys.getrefcount(c))

10

print(sys.getrefcount(d))

11

del ex_list

print(sys.getrefcount(a))

7

print(sys.getrefcount(b))

8

print(sys.getrefcount(c))

9

print(sys.getrefcount(d))

10

# Same thing goes with constants 
print(sys.getrefcount(10))

12

const = 10

print(sys.getrefcount(10))

13

const = const + 10

print(sys.getrefcount(10))

12 
```

如我们所见，这里的容器对象列表引用了引用该值的其他对象，因此当我们删除它们时，引用链接也被删除，因此列表中的对象将引用计数减 1。常量也是如此，当引用的变量增加时，引用计数减少。

到目前为止，您一定已经意识到 del 并没有真正删除对象，相反，它删除了作为该对象引用的变量(name ),并将引用计数减 1。

我们看到的所有例子都有点类似，考虑到它们都在全局命名空间中，但是当有函数时，引用计数会发生什么呢？那么，让我们通过这段代码来找出答案。

```
import sys

num = 100

print(sys.getrefcount(num))

4

def ytf(number):

print(sys.getrefcount(num))

ytf(num)

6

print(sys.getrefcount(num))

4 
```

我们看到，当 ytf()进入作用域时，引用计数增加，而当函数超出作用域时，引用计数减少。请记住，我们应该小心在全局命名空间中使用大型或复杂的对象，因为全局命名空间中的对象不会超出范围，除非我们减少引用计数的值，这样大型对象会消耗更多的内存，从而降低程序的效率。

这就是本部分的全部内容，在下一部分中，我们将仔细研究垃圾收集器，以及它如何在 python 程序中释放内存。
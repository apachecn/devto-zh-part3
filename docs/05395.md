# 列表:Python 中的线性容器

> 原文：<https://dev.to/dandyvica/lists-arrays-vectors-linear-containers-in-python-ruby-and-rust-17mn>

这是我第一篇深入研究我称之为线性容器的文章。在第二篇文章转到 Ruby 数组之前，我将首先描述 Python 列表，最后一篇文章以 Rust 向量结束。

为什么只有 Python、Ruby 和 Rust？这是一个主观的选择，答案是因为这些是我最喜欢的语言。当然，类似的元素集合也存在于其他语言中:Java、C++，你能想到的都有。

Python 和 Ruby 是动态语言，而 Rust 是强类型语言，这带来了一个主要区别:Python 列表和 Ruby 数组可以包含各种元素，不一定具有相同的类型，而 Rust vectors 是相同类型元素的容器。

在第一篇文章中，我将处理 Python 列表。我用的是 Linux Mint 19 的最新版本，版本 3.6.7:

```
Python 3.6.7 (default, Oct 22 2018, 11:32:17) 
[GCC 8.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

Enter fullscreen mode Exit fullscreen mode

# 基本 Python 列表

我将给出一些在 Python 中使用列表的例子，但我只是略读一些有用的特性。我将关注列表的其他有趣方面，而不是基本方面。有关列表的详细操作和函数列表，请参考官方 Python 文档。

用 Python 创建一个列表很简单:

```
digits = [0,1,2,3,4,5,6,7,8,9] 
a = ["one", "two", "three", "four"]

# an empty list empty = [] 
```

Enter fullscreen mode Exit fullscreen mode

您可以混合类型:

```
mixed_list = ["one", 2, "three", 4] 
```

Enter fullscreen mode Exit fullscreen mode

甚至创建一个列表列表:

```
binomial_coefficients = [
    [1],
    [1,1],
    [1,2,1],
    [1,3,3,1],
    [1,4,6,4,1],
    [1,5,10,10,5,1]
] 
```

Enter fullscreen mode Exit fullscreen mode

要用相同的元素初始化一个列表，只需使用* :

```
['A'] * 5       # gives ['A', 'A', 'A', 'A', 'A'] 
```

Enter fullscreen mode Exit fullscreen mode

列表元素的数量由内置函数 *len()* :
给出

```
len(binomial_coefficients)          # gives 6 
```

Enter fullscreen mode Exit fullscreen mode

你也可以在一个列表中存储对象、类或函数:

```
import math

# list of functions trigo = [math.sin, math.cos, math.tan]

# list of lambdas powers = [
    lambda x: x*x,
    lambda x: x*x*x,
    lambda x: x**4
]

# list of classes collections = [list, dict, set]

# list of objects empty_collections = [list(), dict(), set()] 
```

Enter fullscreen mode Exit fullscreen mode

## 访问元素

访问列表元素和往常一样，有一些非常方便的特性:

```
first_binomial = binomial_coefficients[0]
last_binomial = binomial_coefficients[-1]
fifth_binomial = binomial_coefficients[-2]
len(binomial_coefficients[4]) # gives 5 
```

Enter fullscreen mode Exit fullscreen mode

子列表可以使用索引片:

```
first3_binomials = binomial_coefficients[0:3] 
```

Enter fullscreen mode Exit fullscreen mode

## 列表操作

*   添加元素

```
digits.append(10) 
```

Enter fullscreen mode Exit fullscreen mode

*   通过索引删除元素

```
del digits[10] 
```

Enter fullscreen mode Exit fullscreen mode

*   连接列表

```
digits = [0,1,2,3,4] + [5,6] + [7,8,9] 
```

Enter fullscreen mode Exit fullscreen mode

*   测试元素成员资格

```
if 9 in digits:
    print("9 is a digit! Such a surprise ;-)") 
```

Enter fullscreen mode Exit fullscreen mode

## 循环遍历一个列表

使用 for-in 构造:

```
for d in digits:
    print(d) 
```

Enter fullscreen mode Exit fullscreen mode

要在循环时获取元素索引，请使用内置的 *enumerate()* 函数:

```
for i,d in enumerate(digits):
    print(f"{d} is the {i}-th digit") 
```

Enter fullscreen mode Exit fullscreen mode

# 更高级的用法

## 列出一些有用的函数

```
digits = [0,1,2,3,4,5,6,7,8,9]
sum(digits)         # gives 45 max(digits)         # gives 9 min(digits)         # gives 0 
# min & max could also be used with other types, with the key keyword argument lipsum = "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.".split()

# get any word being the longest max(lipsum, key=len)           # gives 'consectetur' 
```

Enter fullscreen mode Exit fullscreen mode

内置函数 *zip()* 组合几个列表来创建一个元组的 iterable，通过获取每个列表的第 I 个元素来创建:

```
a = [0,1,2,3]
b = [0,1,2,3]

# display tuples for i in zip(a,b):
    print(i) 
```

Enter fullscreen mode Exit fullscreen mode

## 列举理解

列表理解是创建新列表或从列表中提取元素的有效方法:

```
# extract words ending with 't' lipsum = "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.".split()
end_with_t = [w for w in lipsum if w.endswith('t')]  # gives ['sit', 'incididunt', 'ut', 'et'] 
# convert to uppercase [w.upper() for w in lipsum]

# get only words of length 5 (including commas) [w for w in lipsum if len(w) == 5]    # gives ['Lorem', 'ipsum', 'dolor', 'amet,', 'elit,', 'magna'] 
# create new objects from a list of classes [c() for c in [list, dict, set]]

# get pi/4 value from trigonometric functions list import math
trigo = [math.sin, math.cos, math.tan]
[f(math.pi/4) for f in trigo] 
```

Enter fullscreen mode Exit fullscreen mode

你也可以使用嵌套迭代和理解:

```
a = [0,1,2,3]
b = [0,1,2,3]
[[i,j] for i in a for j in b] # gives [[0, 0], [0, 1], [0, 2], [0, 3], [1, 0], [1, 1], [1, 2], [1, 3], [2, 0], [2, 1], [2, 2], [2, 3], [3, 0], [3, 1], [3, 2], [3, 3]] 
```

Enter fullscreen mode Exit fullscreen mode

但是请注意这是一个笛卡尔积:

```
a = [0,1,2,3]
b = [0,1,2,3]

# this doesn't give the summation of both lists [i+j for i in a for j in b] # gives [0, 1, 2, 3, 1, 2, 3, 4, 2, 3, 4, 5, 3, 4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

使用这个来获得元素的位置求和:

```
a = [0,1,2,3]
b = [0,1,2,3]
[x+y for i,x in enumerate(a) for j,y in enumerate(b) if i == j] # gives [0, 2, 4, 6] 
# gives the same result [i+j for (i,j) in list(zip(a,b))] 
```

Enter fullscreen mode Exit fullscreen mode

## 使用内置的 *list()* 函数

内置的 *list()* 函数非常强大，可以简洁高效地创建列表。一旦你传入 *list()* 的项是可迭代的，它就使用它的迭代器创建一个列表:

```
# this creates a list of a-z chars a_to_z = list("abcdefghijklmnopqrstuvwxyz")

# create digits and the first 100 even numbers digits = list(range(10))
even = list(range(0,100,2))

# list() is idempotent, but this creates another list, not another reference list(digits)  # gives back a copy of digits 
```

Enter fullscreen mode Exit fullscreen mode

这显然适用于用户定义的迭代器:

```
class One:
    def __iter__(self):
        yield "one"
        yield "un"
        yield "ein"
        yield "uno"

# gives ['one', 'un', 'ein', 'uno'] print(list(One())) 
```

Enter fullscreen mode Exit fullscreen mode

## 继承列表类

没有什么可以阻止你子类化 *list* 类来创建你自己的用户定义列表。这个例子实现了一种通过给定几个索引来访问列表元素的方法:

```
""" Based on list, but accept sparse indexes """
class MyList(list):
    def __getitem__(self, *args):
        # if only one index, just return the element as usual
        if type(args[0]) == int or type(args[0]) == slice:
            return list.__getitem__(self, args[0])
        # otherwise build a list with asked indexes
        else:       
            return [x for i,x in enumerate(self) if i in args[0]]

my_list = MyList(list("abcdefghijklmnopqrstuvwxyz"))
print(my_list[0])           # gives 'a' print(my_list[0:2])         # gives ['a', 'b'] print(my_list[0,24,25])     # gives ['a', 'y', 'z'] 
```

Enter fullscreen mode Exit fullscreen mode

## 按单办事

使用函数式编程内置函数，您可以从一个列表中提取值，或者从源列表中获取另一个列表。

### *地图()*

使用 *map()* 内置函数，可以获得列表上映射的图像。如果你把一个列表看作一组数学元素，*map()通过所考虑的函数给出图像集。* 

```
a_to_z = list("abcdefghijklmnopqrstuvwxyz")
A_to_Z = list(map(str.upper, a_to_z)) 
```

Enter fullscreen mode Exit fullscreen mode

*map()* 本身返回一个可迭代的 map 对象，这就是为什么要得到一个列表，需要 *list()* 函数。

当然，作为第一个参数传递的 map 函数可以是任何函数，任何有一个参数的 lambda 都是可能的:

```
digits = [0,1,2,3,4,5,6,7,8,9]
list(map(lambda x: x*10, digits))        # gives tenths 
# refer to binomial_coefficients above list(map(sum, binomial_coefficients))   # gives [1, 2, 4, 8, 16, 32] 
```

Enter fullscreen mode Exit fullscreen mode

甚至是用户自定义函数:

```
# contrived example def square(x):
    return x*x

# calculate first 9 perfect squares digits = [0,1,2,3,4,5,6,7,8,9]
print(list(map(square, digits))) 
```

Enter fullscreen mode Exit fullscreen mode

*   *过滤器()*

顾名思义，这个内置函数用于使用一些标准从列表中筛选元素。保留的元素是作为第一个参数给 *filter()* 的函数返回 *True* 的元素。

```
# extract even numbers digits = [0,1,2,3,4,5,6,7,8,9]
list(filter(lambda x: x%2 == 0, digits))        # gives [0, 2, 4, 6, 8] 
# extract words less than 4 chars lipsum = "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.".split()
print(list(filter(lambda w: len(w) < 4, lipsum)))   # gives ['sit', 'sed', 'do', 'ut', 'et'] 
```

Enter fullscreen mode Exit fullscreen mode

*   *reduce()*

从 Python3 开始， *reduce()* 函数被移到了 *functools* 模块中。它通过累积对函数的调用，将一个 iterable 缩减为一个值。

所以 *reduce(函数，可迭代的，初始化器)*可以定义为:

```
let f a Python function, a=(a0,a1,...., an) an iterable giving (n+1) values, then:

reduce(f,a,init) = f(.....f(f(f(init,a0), a1), a2)....., an) 
```

Enter fullscreen mode Exit fullscreen mode

例子:

```
from functools import reduce

digits = list(range(10))

# sum of first 10 digits reduce(lambda x,y: x+y, digits)   # gives 45 
# this uses an initializer reduce(lambda x,y: x+y, digits, 10) # gives 55 = 45+10 
# a more sophisticated example: this uses the nested multiplication to compute the value of a polynomial, given its coefficients and the unknown value z (uses type hinting btw) def nested(z: int, coeff: list) -> int:
    res = reduce(lambda x,y: z*x+y, coeff)
    return res

# easy computation of the nested square root which converges to the golden ratio import math
reduce(lambda x,y: math.sqrt(x+y), [1]*100)   # gives 1.618033988749895 
# same for the canonical continued fraction reduce(lambda x,y: y+1/x, [1]*100)

# this sums all columns of a matrix matrix = [[i]*4 for i in range(4)]
reduce(lambda a,b: [x+y for i,x in enumerate(a) for j,y in enumerate(b) if i == j], matrix) # gives [6, 6, 6, 6] 
```

Enter fullscreen mode Exit fullscreen mode

在下一篇文章中，我将解释 Ruby 数组。

> 苏珊·尹在 Unsplash 上的照片*
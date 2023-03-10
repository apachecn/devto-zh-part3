# Python 中列表、元组和集合的区别以及何时使用它们

> 原文：<https://dev.to/flaviabastos/difference-among-list-tuple-and-set-in-python-and-when-to-use-each-one-4i6e>

Python 有三种类似的数据结构，可以保存无序的元素集合:列表、元组和集合。

以下是它们的不同之处:

## 初始化

**列表**:使用内置`list()`或方括号(`[]` ):

```
list1 = list() # creates an empty listlist2 = [] # also creates an empty listlist3 = ['a', 'b', 'c', 4] 
```

**元组:**逗号分隔的元素:

```
t = 'one', 'two', 'three', 4 
```

**设置**:使用内置`set()`或花括号(`{}` ):

```
set1 = set() # creates an empty setset2 = {'one', 'two', 'three', 4} 
```

## 访问

使用索引
访问**列表**和**元组**元素

```
>>> list3[0]'a'>>> t[0]'one' 
```

无法通过索引
访问**集合**

```
>>> set2[0]Traceback (most recent call last): File "<stdin>", line 1, in <module>TypeError: 'set' object does not support indexing 
```

## 易变性

一个**列表**是可变的:

```
>>> list3[0] = 1>>> list3[1, 'b', 'c', 4] 
```

一个**元组**是不可变的:

```
>>> t[0] = 1Traceback (most recent call last): File "<stdin>", line 1, in <module>TypeError: 'tuple' object does not support item assignment 
```

集合也是不可变的，因为你不能通过索引访问它的元素。

## 基本用法

**list** :用来存储可以变异的同构数据集合(但是也可以用来存储异构数据，就像我上面的`list3`例子！). **list** 非常通用，实现了所有的[公共](https://docs.python.org/3.7/library/stdtypes.html#typesseq-common)和[可变](https://docs.python.org/3.7/library/stdtypes.html#typesseq-mutable)序列操作符。它们也可以用作[堆栈](https://docs.python.org/3.7/tutorial/datastructures.html#using-lists-as-stacks)和[队列](https://docs.python.org/3.7/tutorial/datastructures.html#using-lists-as-queues)。

**tuple:** 用来存储异构数据的集合，就像我上面的例子`t`一样，或者用来存储不能变异的同构数据。它们用于创建[枚举()](https://docs.python.org/3.7/library/functions.html#enumerate)内置:
的输出

```
>>> doggos = ['labrador', 'german shepherd', 'poodle'] # regular list>>> list(enumerate(doggos))[(0, 'labrador'), (1, 'german shepherd'), (2, 'poodle')] # list of tuples, where the first element in the tuple is the index# and the second is the value from the list 
```

> 我在这里写了更多关于使用`enumerate()`映射字符串[的内容。](https://dev.to/flaviabastos/map-a-string-in-python-with-enumerate-34kg)

**set:** 用于消除重复元素并进行成员资格测试:

```
>>> 4 in set3 # membership testingTrue>>> set4 = set('banana') # eliminate duplicates>>> set4{'a', 'b', 'n'}>>> set5 = {'one', 'two', 'three', 'one', 'three'} >>> set5{'three', 'one', 'two'}>>> set6 = set(['one', 'two', 'three', 'two', 'one'])>>> set6{'three', 'one', 'two'} 
```

> *帖子*[Python 中列表、元组和集合的区别以及何时使用每一个](https://wp.me/pa0b0y-3V) _ 最初发表在 _ [flaviabastos.ca](https://flaviabastos.ca/)
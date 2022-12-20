# 用 Python3 编写最小堆

> 原文：<https://dev.to/therealdarkmage/writing-a-minimum-heap-in-python3-5d47>

在我开始之前，如果你需要一些什么是堆的背景知识:

[https://en . Wikipedia . org/wiki/Heap _(data _ structure)](https://en.wikipedia.org/wiki/Heap_(data_structure))

这个讨论是关于*最小堆*的。

* * *

最近一周，我帮助我的一个付费学生用 Python3 编写了一个最小堆数据结构。

给定节点的以下类定义:

```
class Node:
    """
    Class definition shouldn't be modified in anyway
    """
    __slots__ = ('_key', '_val')

    def __init__(self, key, val):
        self._key = key
        self._val = val

    def __lt__(self, other):
        return self._key < other._key or (self._key == other._key and self._val < other._val)

    def __gt__(self, other):
        return self._key > other._key or (self._key == other._key and self._val > other._val)

    def __eq__(self, other):
        return self._key == other._key and self._val == other._val

    def __str__(self):
        return '(k: {0} v: {1})'.format(self._key, self._val)

    __repr__ = __str__

    @property
    def val(self):
        """
        :return: underlying value of node
        """
        return self._val 
```

能够在最小堆数据结构上定义缺少的方法:

```
class Heap:
    """
    Class definition is partially completed.
    Method signatures and provided methods may not be edited in any way.
    """
    __slots__ = ('_size', '_capacity', '_data')

    def __init__(self, capacity):
        self._size = 0
        self._capacity = capacity + 1  # additional element space for push
        self._data = [None for _ in range(self._capacity)]

    def __str__(self):
        return ', '.join(str(el) for el in self._data if el is not None)

    __repr__ = __str__

    def __len__(self):  # allows for use of len(my_heap_object)
        return self._size

#    DO NOT MODIFY ANYTHING ABOVE THIS LINE
#    Start of Student Modifications

    def _percolate_up(self):
        pass

    def _percolate_down(self):
        pass

    def _min_child(self, i):
        pass

    def push(self, key, val):
        pass

    def pop(self):
        pass

    @property  # do not remove
    def empty(self):
        pass

    @property  # do not remove
    def top(self):
        pass

    @property  # do not remove
    def levels(self):
        pass 
```

此外，我们希望在`Heap`类本身之外编写一个函数`def most_common_x(vals, X):`，它将获取一个值列表`vals`，然后返回列表中最常见的`X`值的一个`set`。

```
def most_common_x(vals, X):
    pass 
```

* * *

我将逐一介绍每种方法，就像我对我的学生所做的那样。

重要的是在进行过程中测试你的代码，不要仅仅依赖自动测试解决方案。在假设某个方法有效之前，知道如何手动测试和验证该方法的输出是您想要的。

我们开始吧！

* * *

### 推()

`push()`依赖于`_percolate_up()`，否则我们可以只写:

```
def push(self, key, val):
        self._data[self._size] = Node(key,val)
        self._size += 1 
```

结束了。

这将允许我们创建一个真正简单的堆:

```
h = Heap(4)
h.push(4, 'a')
h.push(2, 'b')
h.push(3, 'c')
h.push(1, 'd') 
```

此时，我们应该开始看`_percolate_up()`。

* * *

### _ 渗滤液 _ 上升()

`_percolate_up()`就是将底部节点与其父节点交换，直到其父节点不再大于底部节点。

```
 4
   / \
  2   3
 /
1 
```

拿上面那堆。如果我们对它调用`_percolate_up()`,它应该产生一个看起来更像:

```
 1
   / \
  2   3
 /
4 
```

*   所以，我们需要从堆的末尾开始。
*   让我们创建一个索引，并将其设置为数据列表中的最后一个有效索引。
*   现在，让我们遍历列表，直到到达根节点:
    *   获取当前索引的节点。这是`child`。
    *   算出`parentIndex`，就是指数`i`减去`1`除以`2`作为一个整体`int`。
    *   获取给定索引的`parent`节点。
    *   如果`child`和`parent`都不是`None`:
        *   如果`child`小于`parent`:
            *   交换它们
    *   更新索引以指向`parent`

```
def _percolate_up(self):
    i = self._size - 1
    while i/2 > 0:
        child = self._data[i]
        parentIndex = (i-1)//2
        parent = self._data[parentIndex]
        if child is not None and parent is not None:
            if child < parent:
                tmp = parent
                self._data[parentIndex] = child
                self._data[i] = tmp 
        i = parentIndex 
```

* * *

### _ 渗滤液 _ 下降()

原来`push()`也取决于`pop()`，T1 又取决于`_percolate_down()`。

原来`_percolate_down()`要靠`_min_child()`！

* * *

### _min_child()

这一条就是耐心处理好每一个案例。

1.  如果为空
2.  如果“子”索引超出界限
3.  如果给定的索引没有节点
4.  如果节点没有子节点
5.  如果节点只有左子节点
6.  如果节点只有正确的子节点
7.  如果节点有两个子节点

这实际上取决于`empty()`,但我们一会儿就会这么做。

```
def _min_child(self, i):
    if self.empty:
        return -1
    if i*2+1 > self._size:
        return -1
    if self._data[i] is None:
        return -1
    left = i*2+1
    right = i*2+2
    left_val = self._data[left]
    right_val = self._data[right]
    if left_val is None and right_val is None:
        return -1
    if left_val is not None and right_val is None:
        return left
    if left_val is None and right_val is not None:
        return right
    if self._data[left] < self._data[right]:
        return left
    return right 
```

* * *

### 回 _ 渗滤液 _ 下降()

所以，这一次，以与`_percolate_up()`相反的方式穿过树枝:

*   虽然我们还没有到达终点:
    *   抓住最小的孩子
    *   如果有效并且当前节点大于最小子节点:
        *   交换它们
    *   更新索引以指向最小的子索引

```
def _percolate_down(self):
    i = 0
    while i*2 <= self._size-1:
        mc = self._min_child(i)
        if mc == -1:
            break
        if self._data[i] > self._data[mc]:
            tmp = self._data[i]
            self._data[i] = self._data[mc]
            self._data[mc] = tmp
        i = mc 
```

* * *

### 【空】

超级简单让我们把它敲出来:

```
def empty(self):
    if self._size == 0:
        return True
    return False 
```

* * *

### 流行()

现在我们已经写了`_percolate_down()`和`empty()`，我们可以处理这个了。

*   如果堆是空的，返回`None`
*   我们的返回值只是头节点，或者说是数据列表的前端
*   将前面的节点与堆的末尾/最后一个值/节点交换
*   减量大小
*   清除最后一个元素(实际上我们交换了最小和最大的元素，使最后一个节点无效，然后...)
*   将头节点向下过滤到它的新位置

```
def pop(self):
    if self.empty:
        return None
    rv = self._data[0].val
    self._data[0] = self._data[self._size-1]
    self._size -= 1
    self._data[self._size] = None
    self._percolate_down()
    return rv 
```

* * *

### 返回推送()

`pop()`和`_percolate_up()`都被照顾好了，现在是我们照顾`push()`的时候了:

*   在我们堆的最后一个位置创建一个新的`Node`
*   增加堆大小
*   将新节点过滤到适当的位置
*   如果我们已经达到容量，弹出最小的节点

```
def push(self, key, val):
    self._data[self._size] = Node(key,val)
    self._size += 1
    self._percolate_up()
    if self._size == self._capacity:
        self.pop() 
```

* * *

### 顶端()

也超级容易。只要堆不为空，就抓取顶部节点。

```
def top(self):
    if self.empty is False:
        return self._data[0].val
    return None 
```

* * *

### 关卡()

这个有点长。

Levels 将返回一个列表列表，每个堆级别的节点都在每个列表中(因此，每个级别一个列表)。

边缘情况 1:空堆。返回一个空列表。

*   从堆的开始处开始
*   为此级别创建新列表
*   如果这是我们要添加的第一个列表:
    *   将根节点添加到 levelOne 列表中
    *   将 levelOne 列表添加到我们的退货列表中
*   否则:
    *   计算我们堆的数据表中该级别的`startIndex`和`endIndex`的索引
    *   将每个节点添加到我们的级别列表
    *   如果 levelList 不为空，将其添加到我们的返回列表中

```
def levels(self):
    ourList = []
    if self.empty:
        return ourList
    prevStartIndex = 0
    while prevStartIndex < len(self._data):
        thisLevel = []
        if len(ourList) == 0:
            thisLevel.append(self._data[0])
            prevStartIndex = 0
            ourList.append(thisLevel)
        else:
            startIndex = (prevStartIndex * 2) + 1
            endIndex = startIndex * 2
            j = startIndex 
            while j < len(self._data) and j <= endIndex:
                node = self._data[j]
                if node is not None:
                    thisLevel.append(self._data[j])
                j += 1
            prevStartIndex = startIndex 
            if len(thisLevel) != 0:
                ourList.append(thisLevel)
    return ourList 
```

* * *

最后，`Heap`类之外的函数:

### most_common_x(vals，X)

*   创建一个空字典
*   对于 val 中的每个元素:
    *   通过增加元素键的值，在字典中对其进行计数
*   创建新堆
*   给定字典键:
    *   从字典中获取每个键的值，这是该元素在列表中出现的次数`vals`
    *   把它推到堆上
*   弹出足够多的节点，使得剩余节点的数量为 X
*   将剩余节点添加到 return `set`

带有一些注释的代码:

```
def most_common_x(vals, X):

    # step 1 - build the dictionary
    d = {}

    for element in vals:
        # check to see if there is a count for our given element
        # if not, return 0 for default
        count = d.get(element, 0)

        # increment count
        count += 1

        # re-assign new count back to dictionary
        d[element] = count

    # step 2 - build the heap from the dictionary
    d_keys = d.keys()
    heapSize = len(d_keys)
    heap = Heap(heapSize)

    for key in d_keys:
        count = d[key]
        heap.push(count, key)

    # step 3 - grab the leaf nodes and add to our return set
    returnSet = set()
    while len(heap) > X:
        heap.pop()

    while not heap.empty:
        returnSet.add(heap.pop())

    return returnSet 
```

* * *

诚然，这是一篇很快的文章，但是如果你需要澄清，尽管问我，我可以进一步充实它。:D

* * *

# [如果你需要一名计算机科学导师、代码审核员，或者仅仅是一个可以和程序配对的人，请联系我](https://codementor.io/mikebell66)
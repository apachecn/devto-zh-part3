# Python 的“heapq”模块

> 原文：<https://dev.to/wangonya/pythons-heapq-module-3203>

通常，在处理数据集合时，您可能希望找到最小或最大的项目。很容易编写一个函数来遍历条目并返回最小或最大的条目，或者使用内置的`min()`、`max()`或`sorted()`函数。另一个有趣的方法可能是实现一个堆(优先级)队列。

Python 提供了一个非常方便的模块`heapq`来帮你做这件事。`heapq`带有一套很酷的内置函数，你可以在[文档](https://docs.python.org/3.0/library/heapq.html)中读到更多。在这篇短文中，我只想告诉你如何在一个收藏中找到最小和最大的物品

## 寻找最小的物品

使用`nsmallest`函数找到 3 个最小的项目:

```
import heapq

numbers = [9, 45, 21, 4, 63, 3, 109]

print(heapq.nsmallest(3, numbers)) # [3, 4, 9] 
```

Enter fullscreen mode Exit fullscreen mode

同样，使用`heapify`函数将一个列表转换成一个堆会自动将最小的项设置为第一个:

```
import heapq

numbers = [9, 45, 21, 4, 63, 3, 109]

heapq.heapify(numbers)
print(numbers)
# Output: [3, 4, 9, 45, 63, 21, 109] 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以用`heappop()` :
从堆中弹出

```
heapq.heappop(numbers) # 3 print(numbers)
# Output: [4, 45, 9, 109, 63, 21] 
heapq.heappop(numbers) # 4 print(numbers)
# Output: [9, 45, 21, 109, 63] 
```

Enter fullscreen mode Exit fullscreen mode

## 寻找最大的物品

使用`nlargest`函数找到 3 个最大的项目:

```
import heapq

numbers = [9, 45, 21, 4, 63, 3, 109]

print(heapq.nlargest(3, numbers)) # [109, 63, 45] 
```

Enter fullscreen mode Exit fullscreen mode

## 更实际的例子

```
import heapq

people = [
    {'fname': 'John', 'lname': 'Doe', 'age': 30},
    {'fname': 'Jane', 'lname': 'Doe', 'age': 25},
    {'fname': 'Janie', 'lname': 'Doe', 'age': 10},
    {'fname': 'Jane', 'lname': 'Roe', 'age': 22},
    {'fname': 'Johnny', 'lname': 'Doe', 'age': 12},
    {'fname': 'John', 'lname': 'Roe', 'age': 45}
]

oldest = heapq.nlargest(2, people, key=lambda s: s['age'])
print(oldest)
# Output: [{'fname': 'John', 'lname': 'Roe', 'age': 45}, {'fname': 'John', 'lname': 'Doe', 'age': 30}] 
youngest = heapq.nsmallest(2, people, key=lambda s: s['age'])
print(youngest)
# Output: [{'fname': 'Janie', 'lname': 'Doe', 'age': 10}, {'fname': 'Johnny', 'lname': 'Doe', 'age': 12}] 
```

Enter fullscreen mode Exit fullscreen mode

应当注意的是，`nsmallest(n, iterable)`和`nlargest(n, iterable)`功能在`n`值较小时表现最佳。对于更大的值，使用`sorted()`功能更有效。还有，当`n==1`时，使用内置的`min()`和`max()`函数更有效率。
# 如何在 Python 中对字典列表进行排序

> 原文：<https://dev.to/renegadecoder94/how-to-sort-a-list-of-dictionaries-in-python-5d05>

您可能还记得，我最近发表了一篇关于[解析电子表格](https://therenegadecoder.com/code/how-to-parse-a-spreadsheet-in-python/)的文章，其结果是一个字典列表。当然，出于数据处理的目的，能够对数据进行排序总是很好的，所以我认为分享一些用 Python 对字典列表进行排序的选项会很有趣。

## 问题介绍

如前所述，我正在解析一个 CSV 文件以实现数据可视化，最终我得到了以下格式的所有内容:

```
csv_mapping_list = [
  {
    "Name": "Jeremy",
    "Age": 25,
    "Favorite Color": "Blue"
  },
  {
     "Name": "Ally",
     "Age": 41,
     "Favorite Color": "Magenta"
  },
  {
    "Name": "Jasmine",
    "Age": 29,
    "Favorite Color": "Aqua"
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

当然，拥有格式良好的数据和实际使用这些数据进行可视化是完全不同的问题。换句话说，我们有自己的数据，但是我们可能想要使用它的一个子集。同样，数据的顺序也很重要。

例如，我们可以按年龄对数据点进行排序。这样我们就可以按照年龄的增加或减少来绘制它们，看看我们是否能发现任何趋势。例如，也许年长的人更喜欢某种颜色，或者也许年轻的人有某种类型的名字。

无论如何，我们总是要从数据处理开始。今天我想重点整理一份字典清单。

## 方案

一如既往，我喜欢分享许多可能的解决方案。对我来说，分享一个强力方法后面跟着几个更优雅的方法是很正常的，所以如果需要的话，请小心跳过。

### 手工排序字典列表

排序可能是计算机科学中研究最多的领域之一，所以我们不会深究其中的原理。相反，我们将利用一种更流行的算法，选择排序:

```
size = len(csv_mapping_list)
for i in range(size):
    min_index = i
    for j in range(i + 1, size):
        if csv_mapping_list[min_index]["Age"] > csv_mapping_list[j]["Age"]:
            min_index = j    
    temp = csv_mapping_list[i]
    csv_mapping_list[i] = csv_mapping_list[min_index]
    csv_mapping_list[min_index] = temp 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们按照年代对字典列表进行了排序。为此，我们利用每个字典的“年龄”字段，如第 5 行所示。

自从研究了这个主题之后，我发现 Python 有一种很好的方法，可以在一行代码中处理变量交换:

```
size = len(csv_mapping_list)
for i in range(size):
    min_index = i
    for j in range(i + 1, size):
        if csv_mapping_list[min_index]["Age"] > csv_mapping_list[j]["Age"]:
            min_index = j
    csv_mapping_list[i], csv_mapping_list[min_index] = csv_mapping_list[min_index], csv_mapping_list[i] 
```

Enter fullscreen mode Exit fullscreen mode

很明显，我没有为交换选择一个很好的变量名，但是你明白了。为了完成交换，我们利用元组打包和解包。换句话说，我们在表达式的右边创建一个元组，在表达式的左边解包。很酷的东西！

### 用排序功能对字典列表进行排序

幸运的是，我们不必在 Python 中手工实现排序。相反，我们可以对列表使用内置的排序函数。在下面的代码片段中，我们按照年代对字典列表进行排序。

```
csv_mapping_list.sort(key=lambda item: item.get("Age")) 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们必须指定 key 参数，因为字典不能自然排序。或者，正如 Python 解释器所报告的:

```
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    csv_mapping_list.sort()
TypeError: '<' not supported between instances of 'dict' and 'dict' 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，我们使用了关键参数。key 参数允许我们提供一个函数，为列表中的每一项返回一些值。在这种情况下，使用内联 lambda 函数将每个字典的自然排序映射到每个项目的 age 字段。

不出所料，字典列表按如下顺序排列:

```
[
  {
    'Name': 'Jeremy', 
    'Age': 25, 
    'Favorite Color': 'Blue'
  }, 
  {
    'Name': 'Jasmine', 
    'Age': 29, 
    'Favorite Color': 'Aqua'
  }, 
  {
    'Name': 'Ally', 
    'Age': 41, 
    'Favorite Color': 'Magenta'
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

而且，就这一点而言，通过任何其他关键字进行排序也同样容易:

```
csv_mapping_list.sort(key=lambda item: item.get("Name"))
csv_mapping_list.sort(key=lambda item: item.get("Favorite Color")) 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下，列表将按“字母顺序”排序，因为值是字符串。但是，请注意这种排序方法是区分大小写的。如果你感兴趣的话，我为[写了一整篇关于字符串排序的文章。](https://therenegadecoder.com/code/how-to-sort-a-list-of-strings-in-python/)

如果你不是 lambda 函数的粉丝，欢迎你利用包含`itemgetter`函数的`operator`模块。简而言之，`itemgetter`函数以更方便的语法提供了同样的功能和更好的性能:

```
from operator import itemgetter
f = itemgetter('Name')
csv_mapping_list.sort(key=f) 
```

Enter fullscreen mode Exit fullscreen mode

感谢， [dmitrypolo](https://dev.to/dmitrypolo/comment/9lip) 的提示！

### 排序具有排序功能的字典列表

内置`sort`函数的一个更通用的版本是内置`sorted`函数。它的工作方式与`sort`函数完全一样，但是它适用于所有的可迭代对象。换句话说，如果我们的列表实际上是一个元组，我们将有另一个选择:

```
csv_mapping_list = sorted(csv_mapping_list, key=lambda item: item("Age")) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`sorted`与常规的`sort`方法略有不同，因为它返回一个新的排序列表。需要说明的是，`sorted`并没有对列表进行排序。相反，它构造了一个全新的列表。因此，我们能够对包括元组在内的任何可迭代对象进行排序。

像`sort`，`sorted`有大量的定制选项，所以如果你有更具体的情况，我建议查看[Python 文档](https://docs.python.org/3/howto/sorting.html)。或者，可以在评论里伸手！

## 稍微重述一下

在写这篇文章的时候，我开始有种似曾相识的感觉。然后，我想起我已经写过一篇关于[在 Python](https://therenegadecoder.com/code/how-to-sort-a-list-of-strings-in-python/) 中对字符串列表排序的文章。显然，那里的所有方法都同样适用于这里。无论如何，这里是本文讨论的所有解决方案:

```
# Custom sorting size = len(csv_mapping_list)
for i in range(size):
    min_index = i
    for j in range(i + 1, size):
        if csv_mapping_list[min_index]["Age"] > csv_mapping_list[j]["Age"]:
            min_index = j
    csv_mapping_list[i], csv_mapping_list[min_index] = csv_mapping_list[min_index], csv_mapping_list[i]

# List sorting function csv_mapping_list.sort(key=lambda item: item.get("Age"))

# List sorting using itemgetter from operator import itemgetter
f = itemgetter('Name')
csv_mapping_list.sort(key=f)

# Iterable sorted function csv_mapping_list = sorted(csv_mapping_list, key=lambda item: item("Age")) 
```

Enter fullscreen mode Exit fullscreen mode

像往常一样，我感谢你的支持。如果你对以后的文章有什么建议，请在评论中告诉我！
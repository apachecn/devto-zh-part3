# 如何用 Python 解析电子表格

> 原文：<https://dev.to/renegadecoder94/how-to-parse-a-spreadsheet-in-python-4ag5>

偶尔，我会需要将电子表格中的数据加载到 Python 程序中，但总会出现一个问题:用 Python 解析电子表格的最佳方式是什么？今天这篇文章的目标就是找出答案！

## 问题介绍

最近，我在学习 Python 中一个名为 VTK 的可视化库，我需要找到一种方法来可视化电子表格中的一些数据。不幸的是，我有两个问题:

*   我不知道如何阅读电子表格
*   我不知道如何解析我读到的数据

换句话说，有哪些读取电子表格数据的好方法？在 Python 程序中建模这些数据有哪些好方法？

### 解析 CSV 文件

第一个问题将是本文的目标。特别是，我们将研究从 CSV 文件中读取数据的各种方法。例如，尝试编写我们自己的解析实现有意义吗？毕竟，CSV 是更容易解析的文件格式之一(如下所示)，Python 非常适合处理字符串:

```
Name,Age,Favorite Color
Jeremy,25,Blue
Ally,41,Magenta
Jasmine,29,Aqua 
```

Enter fullscreen mode Exit fullscreen mode

也就是说，我们可能更喜欢使用 Python 提供的一些工具，比如 csv 包。当语言中内置了一个久经考验的库时，重新发明轮子有什么意义呢？

像往常一样，我们将处理一些解决方案，并讨论它们的利弊。到本文结束时，您应该可以轻松地自己解析 CSV 文件了。

### 内部表象

至于第二个问题，我们有几个选择。首先，我们可以将电子表格解释为一个巨大的列表列表。在这个例子中，我们可以给每一行数据一个自己的列表，并将这些行存储在一个列表中:

```
# Heading: Name, Age, Favorite Color csv_matrix = [
  ["Jeremy", 25, "Blue"],
  ["Ally", 41, "Magenta"],
  ["Jasmine", 29, "Aqua"]
] 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可以给每一列数据一个列表，并将这些列表存储在一个列表中:

```
# Heading: Name, Age, Favorite Color csv_matrix = [
  ["Jeremy", "Ally", "Jasmine"],
  [25, 41, 29],
  ["Blue", "Magenta", "Aqua"]
] 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下，我们都有一个巨大的数据矩阵，与原始的 CSV 文件非常相似。

当然，我有点偏爱字典，所以我可能会喜欢用其中的一本。例如，是什么阻止我们创建一个字典，其中每个键都为我们提供一整列数据？

```
csv_dict = {
  "Name": ["Jeremy", "Ally", "Jasmine"],
  "Age": [25, 41, 29],
  "Favorite Color": ["Blue", "Magenta", "Aqua"]
} 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可以翻转关系，这样我们就存储了一个字典列表。这样，数据行就是映射:

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

就个人而言，我更喜欢最后一种表示，因为数据样本粘在一起。换句话说，在排序这样的操作中，没有扭曲原始数据集的风险。同时，其他三种表示有独立的列表，必须一起排序。这足以让最后一个代表获胜。

在下一节中，我们将开始研究一些解析解决方案，它们都将利用最后一种表示。如果你对其他三个有任何问题，欢迎在下面的评论中提出。

## 方案

和往常一样，我们将讨论一些解决今天问题的常用方法。请随意拿起您需要的东西运行，但是我建议通读每个解决方案的优缺点。毕竟，你永远不知道这些东西什么时候会有用。

### 用蛮力解析电子表格

本质上，电子表格是一个逗号分隔的文件。如果我们想要手动解析一个，我们需要注意一些事情:

*   标题行
*   行尾
*   分隔符(在这种情况下是逗号)

在我们最初的例子中，我们有一个类似如下的电子表格:

```
Name,Age,Favorite Color
Jeremy,25,Blue
Ally,41,Magenta
Jasmine,29,Aqua 
```

Enter fullscreen mode Exit fullscreen mode

要手动解析它，我们需要读取每一行并用逗号将其分开。之后，我们需要做一些后期处理来得到我们想要的格式:

```
csv_mapping_list = []
with open("/path/to/data.csv") as my_data:
  line_count = 0
  for line in my_data:
    row_list = [val.strip() for val in line.split(",")]
    if line_count == 0:
      header = row_list
    else:
      row_dict = {}
      for i, key in enumerate(header):
        row_dict[key] = row_list[i]
      csv_mapping_list.append(row_dict)
    line_count += 1 
```

Enter fullscreen mode Exit fullscreen mode

下面是我手动解析电子表格的粗略尝试。在本例中，我们打开 CSV 并逐行读取。对于每一行，我们用逗号将它分开，并使用列表理解系统地修剪每个值。

从那里，我们决定我们刚刚解析的行是否是标题。如果是，我们保存它的值以备后用。否则，我们遍历该行值，并使用头部将它们映射到一个字典中。我想使用我们的[如何在 Python](https://therenegadecoder.com/code/how-to-convert-two-lists-into-a-dictionary-in-python/) 文章
中将两个列表转换成一个字典中的方法可能更简单

```
csv_mapping_list = []
with open("/path/to/data.csv") as my_data:
  line_count = 0
  for line in my_data:
    row_list = [val.strip() for val in line.split(",")]
    if line_count == 0:
      header = row_list
    else:
      row_dict = {key: value for key, value in zip(header, row_list)}
      csv_mapping_list.append(row_dict)
    line_count += 1 
```

Enter fullscreen mode Exit fullscreen mode

无论哪种情况，这个解决方案都有很大的修改空间。例如，如果您的文件有某种其他类型的分隔符，这可能是您的解决方案。

也就是说，要知道强力解决方案有一个主要缺点。根据 [Juha-Matti Santala](https://dev.to/hamatti/comment/9806) 的说法，如果其中一行包含带有逗号的文本(即`"Grifski, Jeremy",25,Blue`)，则通过逗号进行拆分可能会失败。更糟糕的是，这个问题会根据您使用的分隔符而变化。幸运的是，有更好的解决方案可以遵循！

### 用 CSV 阅读器对象解析电子表格

如前所述，如果我们不愿意，我们不必编写自己的 CSV 解析器。相反，我们可以使用 [csv](https://docs.python.org/3/library/csv.html) 包。正如您可能想象的那样，它有大量的 CSV 解析功能。特别是，它包含了 reader 对象，我们可以像以前一样使用它来读取 CSV 文件:

```
import csv
csv_mapping_list = []
with open("/path/to/data.csv") as my_data:
  csv_reader = csv.reader(my_data, delimiter=",")
  line_count = 0
  for line in csv_reader:
     if line_count == 0:
       header = line
     else:
       row_dict = {key: value for key, value in zip(header, line)}
       csv_mapping_list.append(row_dict)
     line_count += 1 
```

Enter fullscreen mode Exit fullscreen mode

有了 reader 对象，我们还没能把代码简化那么多。事实上，我们所做的只是用读者实例化代替了列表理解。无论如何，对于那些不愿意编写自己的解析器的人来说，这是一个不错的选择。

### 用 CSV DictReader 对象解析电子表格

在这一点上，您可能想知道我们为什么要使用 csv 库。毕竟，它在最后两个例子中几乎没有什么不同。幸运的是，有一种方法可以使用 csv 库中一个名为 DictReader 的特殊类来减少我们的代码:

```
import csv
with open("/path/to/dict.csv") as my_data:
  csv_mapping_list = list(csv.DictReader(my_data)) 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们做到了！我们在前两节中所做的所有解析现在已经被大幅缩减到只有 3 行代码。

也就是说，在这个解决方案中有一个微小的区别。我们没有创建字典列表，而是创建了一个由 [OrderedDict](https://docs.python.org/3/library/collections.html#collections.OrderedDict) 对象组成的列表。它们可以像字典一样使用，但是它们的键序是固定的。无论如何，这种解决方案的优雅不仅仅弥补了这个事实，在某些情况下，我们甚至希望保留键的顺序。

## 稍微重述一下

在这一点上，我发现一次查看所有可能的解决方案很不错:

```
# Brute force solution csv_mapping_list = []
with open("/path/to/data.csv") as my_data:
  line_count = 0
  for line in my_data:
    row_list = [val.strip() for val in line.split(",")]
    if line_count == 0:
      header = row_list
    else:
      row_dict = {key: value for key, value in zip(header, row_list)}
      csv_mapping_list.append(row_dict)
    line_count += 1

# CSV reader solution import csv
csv_mapping_list = []
with open("/path/to/data.csv") as my_data:
  csv_reader = csv.reader(my_data, delimiter=",")
  line_count = 0
  for line in csv_reader:
     if line_count == 0:
       header = line
     else:
       row_dict = {key: value for key, value in zip(header, line)}
       csv_mapping_list.append(row_dict)
     line_count += 1

# CSV DictReader solution import csv
with open("/path/to/dict.csv") as my_data:
  csv_mapping_list = list(csv.DictReader(my_data)) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我打算写一篇关于如何对字典列表进行排序的文章，所以请留意！在那之前，谢谢你留下来。希望这篇文章是有帮助的。如果有，为什么不给它分一杯羹？
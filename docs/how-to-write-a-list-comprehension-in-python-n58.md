# 如何用 Python 写列表理解

> 原文：<https://dev.to/renegadecoder94/how-to-write-a-list-comprehension-in-python-n58>

欢迎回到 Python 入门系列的另一篇文章。这一次，我想稍微回顾一下 Python 的一个内置特性，叫做列表理解。虽然我们在这个系列中使用过几次，但直到现在我才真正想到要解释它们。

## 问题介绍

与本系列中的其他文章不同，本文中没有我们试图解决的具体问题。相反，目标是理解列表理解语法:

```
nums = [2, 6, 10, -4] 
negative_nums = [x for x in nums if x < 0] 
```

Enter fullscreen mode Exit fullscreen mode

这个奇怪的语法是什么，它是如何工作的？这就是今天这篇文章的目标。具体来说，我们将看几个列表理解有用的场景，例如:

*   复制列表
*   修改列表
*   过滤列表
*   过滤和修改列表
*   从两个列表中生成所有对
*   复制嵌套列表

如果你知道任何我们可以用列表理解来做的事情，请告诉我！

## 方案

在我们深入研究解决方案之前，让我们先讨论一下语法。下面是我阐述这个概念的最佳尝试:

```
output = [expression(item) for item in some_list] 
```

Enter fullscreen mode Exit fullscreen mode

在最基本的层面上，我们可以构建一个列表理解，它遍历列表中的每一项，对该项执行一些表达式，并将新项放入输出列表中。或者作为一个循环:

```
output = [] 
for item in some_list: 
  output.append(expression(item)) 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们可以做的不仅仅是用列表理解从其他列表中创建一个列表。在下面的小节中，我们将看几个例子。

### 复制一个列表

也许理解一个列表最简单的用途就是复制另一个列表

```
my_list = [2, 5, -4, 6] 
output = [item for item in my_list] # [2, 5, -4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`output`将等同于`my_list`。为了完整起见，这里有一个与循环相同的解决方案:

```
my_list = [2, 5, -4, 6] 
output = [] 
for item in my_list: 
  output.append(item) 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，清单理解要简洁得多。在这两种情况下，我们都将只执行一次浅拷贝——也就是说，新列表中的项目可能指向旧列表中的相同项目——因此，最好只将此语法用于拷贝像数字这样的不可变值列表。

### 修改列表*

现在我们知道了如何复制一个列表，让我们在将它们添加到输出列表之前尝试修改它们:

```
my_list = [2, 5, -4, 6] 
output = [2 * item for item in my_list] # [4, 10, -8, 12] 
```

Enter fullscreen mode Exit fullscreen mode

我们没有直接复制原始列表，而是在存储到新列表之前，通过将每个项目乘以 2 来修改它。结果，我们得到了一个列表，其中的每一项都是原来列表的两倍。下面是使用循环的相同概念:

```
my_list = [2, 5, -4, 6] 
output = [] 
for item in my_list: 
  output.append(item * 2) 
```

Enter fullscreen mode Exit fullscreen mode

需要澄清的是，正如星号可能暗示的那样，我们实际上并没有*改变*原始列表。相反，我们创建了一个全新的列表，其中的项目增加了一倍。

如果`my_list`包含对象或者其他可变的数据类型，比如列表，就没有什么可以阻止我们修改它们。当然，这被认为是不好的做法，所以我忽略了分享一个例子，万一有人偶然将它复制到生产系统中。

### 过滤列表

虽然复制和修改列表很有趣，但有时过滤列表也很有帮助:

```
my_list = [2, 5, -4, 6] 
output = [item for item in my_list if item < 0] # [-4] 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们在列表理解的最右边部分添加了一个新的表达式:`if item < 0`。当然，循环的等价形式可能如下所示:

```
my_list = [2, 5, -4, 6] 
output = [] 
for item in my_list: 
  if item < 0: 
    output.append(item) 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，对于列表中的每一项，只有当它小于零时才考虑它。如果是，将其转储到新列表中。结果，我们得到了一个只包含负值的列表。

### 过滤和修改列表

自然地，我们可以通过组合语法来同时修改和过滤列表:

```
my_list = [2, 5, -4, 6] 
output = [2 * item for item in my_list if item < 0] # [-8] 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们决定在将结果转储到一个列表之前将所有负值加倍。同样，与循环相同的语法可能类似于:

```
my_list = [2, 5, -4, 6] 
output = [] 
for item in my_list: 
  if item < 0: 
    output.append(item * 2) 
```

Enter fullscreen mode Exit fullscreen mode

因此，输出列表只包含`-8`。再一次，重要的是提及我们实际上没有*修改*原始列表。

### 从两个列表中生成所有对

现在，我们开始进入列表理解的一些更高级的特性。特别是，我们希望在两个列表之间生成成对的值:

```
# [(1, 2), (1, 4), (1, 6), (3, 2), (3, 4), (3, 6), (5, 2), (5, 4), (5, 6)] output = [(a, b) for a in (1, 3, 5) for b in (2, 4, 6)] 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们创建了一个列表，其中包含两个列表中所有配对的组合。像往常一样，我们可以用下面的一组循环实现同样的事情:

```
output = [] 
for a in (1, 3, 5): 
  for b in (2, 4, 6): 
    output.append((a, b)) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想让事情变得更有趣，我们可以应用一些过滤:

```
# [(3, 2), (5, 2), (5, 4)] output = [(a, b) for a in (1, 3, 5) for b in (2, 4, 6) if a > b] 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，如果第一个列表中的数字大于第二个列表中的数字，我们只生成一个对。

### 重复嵌套列表

对于前面提到的浅层复制例子，我们不能复制嵌套列表，比如二维矩阵。为此，我们可以利用嵌套列表理解:

```
my_list = [[1, 2], [3, 4]] 
output = [[item for item in sub_list] for sub_list in my_list] 
print(output) # Prints [[1, 2], [3, 4]] 
```

Enter fullscreen mode Exit fullscreen mode

我们检索每个列表，并使用与之前相同的理解来复制它们，而不是执行表层复制。正如你可能想象的那样，我们可以将这个概念抽象成一个递归函数，它在矩阵的每个维度上执行列表理解:

```
def deep_copy(to_copy): 
  if type(to_copy) is list: 
    return [deep_copy(item) for item in to_copy] 
  else: 
    return to_copy 
```

Enter fullscreen mode Exit fullscreen mode

多酷啊。当然，如果在矩阵的最深层有除了数字或字符串之外的任何东西，您必须自己处理克隆过程的其余部分。

## 稍微重述一下

和往常一样，这里有一个巨大的转储，包含了本文中涉及的所有示例，并附有简短解释每个片段的注释。随意拿上你需要的东西就走！

```
# Define a generic 1D list of constants my_list = [2, 5, -4, 6] 

# Duplicate a 1D list of constants [item for item in my_list] 

# Duplicate and scale a 1D list of constants [2 * item for item in my_list] 

# Duplicate and filter out non-negatives from 1D list of constants [item for item in my_list if item < 0] 

# Duplicate, filter, and scale a 1D list of constants [2 * item for item in my_list if item < 0] 

# Generate all possible pairs from two lists [(a, b) for a in (1, 3, 5) for b in (2, 4, 6)] 

# Redefine list of contents to be 2D my_list = [[1, 2], [3, 4]] 

# Duplicate a 2D list [[item for item in sub_list] for sub_list in my_list] 

# Duplicate an n-dimensional list def deep_copy(to_copy): 
  if type(to_copy) is list: 
    return [deep_copy(item) for item in to_copy] 
  else: 
    return to_copy 
```

Enter fullscreen mode Exit fullscreen mode

我希望你在阅读这篇关于列表理解的文章时和我写这篇文章时一样开心。我想在这个系列的这一点上，我将开始探索像这样的基本概念，并将它们延伸到极限。你有想要探索的 Python 概念吗？让我知道！

与此同时，为什么不看看其他一些精彩的 Python 文章呢:

*   [石头剪子布使用模运算](https://dev.to/renegadecoder94/rock-paper-scissors-using-modular-arithmetic-2j4p)
*   [如何在 Python 中检查文件是否存在](https://therenegadecoder.com/code/how-to-check-if-a-file-exists-in-python/)
*   [如何用 Python 解析电子表格](https://dev.to/renegadecoder94/how-to-parse-a-spreadsheet-in-python-4ag5)

而且，如果你觉得特别慷慨，就去[会员页面](https://therenegadecoder.com/members/)看看你有哪些选择。欢迎您在购买前试用。这就是为什么有[一个免费选项](https://therenegadecoder.com/register/basic/)。无论如何，再次感谢你的支持。早点回来！
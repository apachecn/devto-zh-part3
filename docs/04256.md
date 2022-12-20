# 复制可变数据类型时要小心

> 原文：<https://dev.to/renegadecoder94/be-careful-when-copying-mutable-data-types-2loa>

最近，我在写一篇关于 Python 中的列表理解的文章，当时我觉得谈一谈制作变量副本会有所帮助。特别是，我想花点时间来解决复制可变数据类型时的一些风险。

## 永恒性

在我们讨论复制变量之前，讨论一个重要的编程语言特性是很重要的，这个特性叫做不变性。[不变性](https://therenegadecoder.com/code/the-coolest-programming-language-features/#immutability)描述一个不可改变的变量。换句话说，不可变变量是常量。

更具体地说，不变性意味着变量不能变异。例如，一个不可变的字符串不能改变或删除任何字符，除非在这个过程中创建一个全新的字符串。在 Java 或 Python 这样的语言中处理数字时，我们经常会看到这种情况:

```
num = 5 
copy = num 
```

Enter fullscreen mode Exit fullscreen mode

自然地，我们会期望发生在`copy`身上的任何事情对`num`都没有影响。这是因为数字通常是不可变的。换句话说，存储在`num`中的 5 有一个与存储在`copy`中的 5 不同的身份。

不幸的是，在大多数编程语言中，对不变性的支持非常有限。因此，数字和字符串以外的变量通常是可变的，这意味着上面的代码片段将无法创建副本。相反，你会在量子纠缠中得到所谓的“幽灵般的超距作用”。换句话说，无论你对一个变量做什么，都会影响到另一个变量。

## 临摹的基础知识

由于大多数语言缺乏对不变性的支持，我们不得不在创建副本时处理后果。特别是，我们必须创建与我们想要手动复制的变量具有相同属性的新变量。在接下来的小节中，我们将看看这是如何发生的。

### 在 Python 中复制列表

如果我们想用 Python 复制一个列表，我们可以尝试下面的方法:

```
my_list = [1, 2, 3] 
my_copy = my_list 
```

Enter fullscreen mode Exit fullscreen mode

如果我们仔细查看，我们会注意到两个列表实际上是相同的。多么大的成功啊，对吧？也许我们应该再看一看:

```
my_copy[1] = 7 
print(my_list) # Prints [1, 7, 3]... uh oh! 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所见，Python 中的列表是可变的。当我们创建一个“副本”时，我们实际上复制了引用——而不是列表的内容。

为了创建一个合适的副本，我们需要遍历列表并将每个元素添加到一个新列表中:

```
my_copy = [item for item in my_list] 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用[一个列表理解](https://dev.to/renegadecoder94/how-to-write-a-list-comprehension-in-python-n58)来创建一个原始列表的副本。现在，当我们操作新列表时，我们不必担心破坏旧列表。但是，这就够了吗？

### 在 Python 中复制嵌套列表

事实证明，列表理解并不能保证执行正确的复制。例如:

```
my_list = [[1, 2], [2, 7]] 
my_shallow_copy = [item for item in my_list] 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们创建了`my_list`的**浅拷贝**。虽然新列表具有不同于原始列表的唯一标识，但是两个列表的内容是相同的。换句话说，下面是安全的:

```
my_shallow_copy.append([5, -4]) 
print(my_list) # Prints [[1, 2], [2, 7]] 
```

Enter fullscreen mode Exit fullscreen mode

但是，更改任何嵌套元素都会导致两个列表都损坏:

```
my_shallow_copy[0][1] = -4 
print(my_list) # prints [[1, -4], [2, 7]]... uh oh! 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想要执行一个**深度复制**在这种情况下，我们必须复制嵌套列表:

```
my_deep_copy = [[item for item in sub_list] for sub_list in my_list] 
```

Enter fullscreen mode Exit fullscreen mode

自然地，这导致我们编写一个递归函数，可以处理一个 n 维矩阵:

```
def deep_copy(item): 
  if type(item) is list: 
    return [deep_copy(sub_list) for sub_list in item] 
  else: 
    return item 
```

Enter fullscreen mode Exit fullscreen mode

当然，即使是这种深度复制功能也只能到此为止。如果我们的列表包含可变对象怎么办？

### 在 Python 中复制可变对象

在这一点上，我们对复制不可变的数据类型(如数字和字符串)以及可变的数据类型(如列表)相当满意，但是如果我们处理的数据类型是其他类型呢？例如，如果我们创建自己的类如下:

```
class Votes: 
  def __init__(self): 
    self.pro = list() 
    self.anti = list() 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们创建了一些类来表示一组投票，这些投票维护两个列表:赞成和反对。我们可以用代表投票者的唯一 id 来填充这些列表:

```
town_votes = Votes() 
town_votes.pro.append("109437139") 
town_votes.pro.append("476524275") 
town_votes.pro.append("794314532") 
town_votes.anti.append("420901790") 
```

Enter fullscreen mode Exit fullscreen mode

太好了，现在我们可以做一些有趣的事情，比如统计赞成和反对的票数:

```
len(town_votes.pro) # 3 len(town_votes.anti) # 1 
```

Enter fullscreen mode Exit fullscreen mode

现在，假设我们有几个人在计票，那么我们可以确保我们得到了正确的结果。出于安全目的，我们希望创建一个`town_votes`对象的深层副本，这样腐败的个人就不会破坏每个人的计数。如果他们尝试，他们应该在最终检查中失败。

当然，我们如何复制我们的`town_votes`对象呢？例如，类似这样的东西会起作用吗:

```
duplicate = town_votes 
```

Enter fullscreen mode Exit fullscreen mode

当然不是。我们只是复制了引用，这导致了与列表相同的问题。但是，如果我们创建一个新的`Votes`对象并复制它的引用:
呢

```
duplicate = Votes() 
duplicate.pro = town_votes.pro 
duplicate.anti = town_votes.anti 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们现在有了一个新的`Votes`对象，但是仍然有一个问题:赞成和反对列表是相同的。换句话说，我们只创建了一个`Votes`对象的浅层副本。幸运的是，我们对克隆列表略知一二:

```
duplicates.pro = [id for id in town_votes.pro] 
duplicates.anti = [id for id in town_votes.anti] 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有了`town_votes`对象的深层副本。如果有人过来篡改副本，我们还是会没事的。

### 复制构造函数

我们刚刚设法用`Votes`对象完成的被称为深度拷贝。当然，这个过程会根据我们的对象存储了多少引用而迅速扩展。更糟糕的是，如果这些引用存储引用。为了解决这个问题，库实现所谓的复制构造函数并不罕见:

```
def __init__(self, to_copy=None): 
  if to_copy: 
    self.pro = [id for id in to_copy.pro] 
    self.anti = [id for id in to_copy.anti] 
  else: 
    self.pro = list() 
    self.anti = list() 
```

Enter fullscreen mode Exit fullscreen mode

然后，如果我们想要我们的`Votes`对象的深层副本，我们将把它作为输入提供给构造函数。而且，如果我们的投票列表包含引用(比如假设的`Voter`对象)，我们可以直接从列表理解中调用它们的复制构造函数:

```
def __init__(self, to_copy=None): 
  if to_copy: 
    self.pro = [Voter(id) for id in to_copy.pro] 
    self.anti = [Voter(id) for id in to_copy.anti] 
  else: 
    self.pro = list() 
    self.anti = list() 
```

Enter fullscreen mode Exit fullscreen mode

当然，在执行深度复制时存在挑战。也许最危险的是循环引用，其中一个对象指向另一个对象，而另一个对象指向后面。在复制过程中，两个对象需要在一个无限循环中互相构造。为了处理这个问题，通常需要维护某种引用查找表，以查看过去是否复制过该对象。

无论如何，Python 确实提供了[复制库](https://docs.python.org/2/library/copy.html)，可以在合理的范围内为你处理所有这些有趣的事情。我不会在这里深入讨论这个问题，因为我不打算写一篇 Python 文章，但是欢迎您自己深入研究文档。

## 克隆人的进攻

在这一点上，我希望您对不变性和克隆这样的概念更加熟悉。这些概念几乎适用于当今使用的每一种流行语言，如 C、C++、JavaScript 和 Java。你很难找到一种实现完全不变性的语言，但是确实有一些。我相信大多数函数式语言都试图避免状态的概念，所以使用 Haskell 这样的语言也许可以避免这个克隆问题。

既然你在这里，我建议你看看下面的一些文章:

*   [石头剪子布使用模运算](https://dev.to/renegadecoder94/rock-paper-scissors-using-modular-arithmetic-2j4p)
*   [如何在 Python 中对字符串列表进行排序](https://therenegadecoder.com/code/how-to-sort-a-list-of-strings-in-python/)
*   [如何在 Python 中克隆列表](https://therenegadecoder.com/code/how-to-clone-a-list-in-python/)

而且，如果你觉得特别慷慨，可以查看[会员页面](https://therenegadecoder.com/members/)获取订阅信息。一点一滴都有帮助！
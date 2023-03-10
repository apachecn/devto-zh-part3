# 如何在 Python 中反转字典:Comprehensions、Defaultdict 等等

> 原文：<https://dev.to/renegadecoder94/how-to-invert-a-dictionary-in-python-2150>

*文章最初发布在[therenegadecoder.com](https://therenegadecoder.com/code/how-to-invert-a-dictionary-in-python/)上，在那里它得到了积极的维护。*

在本系列中，我将几篇关于小 Python 问题的文章放在一起，这些问题可以用几行代码解决。这个系列的灵感来自于我每天用谷歌搜索来解决自己工作中的问题。今天，我在写一篇关于如何倒查字典的文章。

## 问题介绍

最近，我在做一个 Python 项目，在这个项目中我需要反转一个字典。对于所有的博库非英雄学术界(BNHA)的粉丝们，我基本上希望能够做到以下几点:

```
my_dict = {
  'Izuku Midoriya': 'One for All', 
  'Katsuki Bakugo': 'Explosion', 
  'All Might': 'One for All', 
  'Ochaco Uraraka': 'Zero Gravity'
}

my_inverted_dict = {
  'One for All': ['Izuku Midoriya', 'All Might'], 
  'Explosion': ['Katsuki Bakugo'], 
  'Ochaco Uraraka': ['Zero Gravity']
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个场景中，我们有一个来自 BNHA 的字符字典，映射到他们的怪癖，我们想把它转换成一个字典，映射到拥有他们的字符的怪癖。

不幸的是，原始字典具有非唯一值，因此翻转字典会导致键的丢失。相反，我们希望在执行反转时为每个非唯一值累积一个键列表。事实证明，这很容易做到。

## 方案

如果我们想颠倒字典，我们有几种选择。从理解到循环，我们将看看所有实用的解决方案。

### 用地图反转字典并反转

让我们从一个更简洁的选项开始我们的一系列选项:

```
my_inverted_dict = dict(map(reversed, my_dict.items())) 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用 map 函数，它将反转函数应用于字典中的所有条目。然后，我们将地图对象转换成一个字典。结果如下所示:

```
my_inverted_dict = {
  'One for All': 'All Might', 
  'Explosion': 'Katsuki Bakugo', 
  'Zero Gravity': 'Ochaco Uraraka'
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，如果要确保不失去 Midoriya，就不应该用这种方法。否则，这将是一个完美的解决方案。

### 用理解来颠倒字典

在 Python 2.7 及以上版本中，我们可以使用字典理解来反转字典。不幸的是，它也陷入了前面提到的同样的问题，但是它为惟一的值完成了工作:

```
my_inverted_dict = {value: key for key, value in my_dict.items()} 
```

Enter fullscreen mode Exit fullscreen mode

再一次，这里是颠倒我们的示例字典的结果:

```
my_inverted_dict = {
  'One for All': 'All Might', 
  'Explosion': 'Katsuki Bakugo', 
  'Zero Gravity': 'Ochaco Uraraka'
} 
```

Enter fullscreen mode Exit fullscreen mode

如我们所见，我们丢了一把钥匙。因此，必须有一个更好的方法来颠倒字典。

### 用 Defaultdict 反转字典

幸运的是，有一个更好的方法！感谢我们的朋友，[尼尔斯·范·盖伦最后一次](https://dev.to/nielsgl/comment/92eo)，我们可以用三行代码完成我们需要的东西:

```
from collections import defaultdict
my_inverted_dict = defaultdict(list)
{my_inverted_dict[v].append(k) for k, v in my_dict.items()} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们选择利用来自`collections`的`defaultdict`对象。它允许我们为键设置默认值。在本例中，我们选择了缺省值 list，这样我们就可以在没有运行时错误风险的情况下追加数据。查看结果:

```
my_inverted_dict = {
  'One for All': ['Izuku Midoriya', 'All Might'], 
  'Explosion': ['Katsuki Bakugo'], 
  'Zero Gravity': ['Ochaco Uraraka']
} 
```

Enter fullscreen mode Exit fullscreen mode

由于`defaultdict`像普通的`dict`一样工作，我们仍然可以与它互动。如果你没有任何严格的`dict`要求，并且你的价值观不是唯一的，这是一个很好的解决方案。

### 用 For 循环反转字典

另一种反转字典的方法是使用 for 循环。这允许我们迭代映射集，并手工构建新的映射。看一看:

```
my_inverted_dict = dict()
for key, value in my_dict.items():
    my_inverted_dict.setdefault(value, list()).append(key) 
```

Enter fullscreen mode Exit fullscreen mode

使用这种方法，我们可以在保留所有原始键的同时反转字典。让我们看看如果我们运行这个代码片段会发生什么:

```
my_inverted_dict = {
  'One for All': ['Izuku Midoriya', 'All Might'], 
  'Explosion': ['Katsuki Bakugo'], 
  'Zero Gravity': ['Ochaco Uraraka']
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们已经有了我们需要的东西，但是如果我们想把这本字典恢复到原来的形式，会发生什么呢？

### 回复反转

在所有键和值都是惟一的基本情况下，我们可以使用我们已经讨论过的相同的字典理解将字典恢复到它的原始映射:

```
my_dict = {value: key for key, value in my_inverted_dict.items()} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这对于将键映射到列表的字典来说是行不通的。这是因为 Python 中的列表是不可共享的类型。换句话说，Python 不允许列表作为字典中的键，因为[列表不是不可变的](https://docs.python.org/2/reference/datamodel.html)。

幸运的是，还原我们的字典比一开始就颠倒要容易得多。我们可以用下面的字典理解:

```
my_dict = {value: key for key in my_inverted_dict for value in my_map[key]} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，我们使用这个双循环结构为每个列表中的每个值创建了一个新的键-值对。

## 稍微重述一下

使用上面的方法，我们可以倒排任何字典。

```
# Use to invert dictionaries that have unique values my_inverted_dict = dict(map(reversed, my_dict.items()))

# Use to invert dictionaries that have unique values my_inverted_dict = {value: key for key, value in my_dict.items()}

# Use to invert dictionaries that have non-unique values from collections import defaultdict
my_inverted_dict = defaultdict(list)
{my_inverted_dict[v].append(k) for k, v in my_dict.items()}

# Use to invert dictionaries that have non-unique values my_inverted_dict = dict()
    for key, value in my_dict.items():
        my_inverted_dict.setdefault(value, list()).append(key)

# Use to invert dictionaries that have lists of values my_dict = {value: key for key in my_inverted_dict for value in my_map[key]} 
```

Enter fullscreen mode Exit fullscreen mode

几乎所有其他类型的字典转换都超出了本教程的范围。但是，如果你有任何具体的问题，请在下面的评论中提出。我总是乐意为某人写一篇文章！
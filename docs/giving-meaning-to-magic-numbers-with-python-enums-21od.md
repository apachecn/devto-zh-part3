# 用 Python 枚举赋予幻数以意义

> 原文：<https://dev.to/rpalo/giving-meaning-to-magic-numbers-with-python-enums-21od>

本文不*超*深入。我刚刚在 Python 标准库中发现了一个很酷的模块，想分享一下以激发你的兴趣。如果你遇到任何问题，有任何疑问，我很乐意尽力帮助你解决！

* * *

## 使用 Python 的`enum`模块

我正在做一个关于[exercisem](https://exercism.io)的练习，这个练习与找到最好的扑克手有关，我发现自己有很多“神奇的价值”在浮动。当我说“神奇的价值”时，我指的是一个硬编码的常量，它应该具有某种语义。在这个特殊的例子中，我有可以取 13 个不同特定值之一的扑克牌，其中一些值有名字。然后是*然后是*，我的扑克手牌正在得分，这些不同类型的牌也有内在的价值，有些牌比其他的好。

我的第一次迭代非常神奇，看起来像这样:

```
if self.is_straight() and self.is_flush():
    return 8
elif self.biggest_group() == 4:     # 4 of a Kind
    return 7
elif self.is_full_house():
    return 6
elif self.is_flush():
    return 5
elif self.is_straight():
    return 4
elif self.biggest_group() == 3:     # 3 of a kind
    return 3
elif self.is_two_pair():
    return 2
elif self.biggest_group() == 2:     # One pair
    return 1
else:                               # High Card
    return 0 
```

这感觉很奇怪，让我的代码蜘蛛感觉刺痛。但我让它过去了。然后我写了这个代码:

```
if value == "A":
    self.value = 14
elif value == "K":
    self.value = 13
elif value == "Q":
    self.value = 12
elif value == "J":
    self.value = 11
else:
    self.value = int(value) 
```

更多神奇的数字！我模糊地记得 Python 的标准库中有一个`enum`模块，我也模糊地记得`enums`应该适用于一个值可以具有特定类别/类型的情况。所以我做了些调查。结果，他们比我想象的还要酷。

`enum`模块有一堆不同类型的`enums`，但是，因为我做了大量的排序、比较和相对排序，以及在这些数字和正常数字之间转换，所以我决定使用一个`IntEnum`。这仅仅意味着`enum`的值将是整数。但是那看起来像什么？

你可以用函数语法定义一个枚举(就像你实例化一个类或者创建一个类型`NamedTuple`)，或者你可以用类语法创建它。出于不同的原因，您将在本文中看到这两种情况。

我将首先展示扑克牌局分数的功能。我要走这条路，因为我不在乎数字是多少。我只是想把相对排名说得很清楚。

```
from enum import IntEnum

Score = IntEnum('Score', [
    'HIGH_CARD',
    'PAIR',
    'TWO_PAIR',
    'THREE_OF_A_KIND',
    'STRAIGHT',
    'FLUSH',
    'FULL_HOUSE',
    'FOUR_OF_A_KIND',
    'STRAIGHT_FLUSH',
]) 
```

这就是我们所需要的。现在我们可以看到它们的相对值，并进行比较！

```
>>> Score.STRAIGHT.value
5
>>> Score.FOUR_OF_A_KIND > Score.THREE_OF_A_KIND
True
>>> Score.FLUSH < Score.HIGH_CARD
False 
```

这让我们可以语义化(我的新词，谢谢！)我们上面的代码:

```
if self.is_straight() and self.is_flush():
    return Score.STRAIGHT_FLUSH
elif self.biggest_group() == 4:
    return Score.FOUR_OF_A_KIND
elif self.is_full_house():
    return Score.FULL_HOUSE
elif self.is_flush():
    return Score.FLUSH
elif self.is_straight():
    return Score.STRAIGHT
elif self.biggest_group() == 3:
    return Score.THREE_OF_A_KIND
elif self.is_two_pair():
    return Score.TWO_PAIR
elif self.biggest_group() == 2:
    return Score.PAIR
else:
    return Score.HIGH_CARD 
```

可读性更强！你知道我们正在做正确的事情的一个方法是，我能够删除一些不太清楚的条件的解释注释，而丝毫不损失可读性。很酷，对吧？

现在，让我们看看另一种情况下的牌面价值。对于这一个，我使用了类语法，因为我想添加更多的功能。一分钟后会有更多。这是开始的代码。

```
class FaceCard(IntEnum):
    """Numeric values of face cards"""
    JACK = 11
    QUEEN = 12
    KING = 13
    ACE = 14 
```

现在我们可以像普通数字一样使用它，但是它*有名字！*

输入以字符串的形式提供:`"AH"`表示红心 a，`"8C"`表示梅花 8，等等。我开始是这样定义一张卡的:

```
class Card:
    def __init__(self, label):
        self.suit = label[-1]
        value = label[:-1]
        if value == "J":
            self.value = FaceCard.JACK
        elif value == "Q":
            self.value = FaceCard.QUEEN
        elif value == "K":
            self.value = FaceCard.KING
        elif value == "A":
            self.value = FaceCard.ACE
        else:
            self.value = int(value)

    def __lt__(self, other):
        return self.value < other.value 
```

`__init__`方法正在初始化实例，`__lt__`方法告诉这些对象如何在`<`操作符周围运行。我们现在可以这样对比:

```
>>> king = Card("KS")  # King of Spades >>> three = Card("3S")  # Three of Spades >>> three < king
True 
```

尽管 king 的值实际上是一个`FaceCard.KING`，但它是用它的整数值来比较的，所以在大多数情况下，我可以把它当作一个普通的数字！枚举很酷！

要了解更多信息，标准库文档非常好。

## 加成:转换回字符串！

我认为`enum`很酷。然后我发现我需要将我的卡值转换回它们的编码字符串以便输出。事实证明，枚举有两个主要属性:类似于“钥匙”的`name`和`value`。检查一下这个牛逼:

```
# The same FaceCard class as before: class FaceCard(IntEnum):
    """Numeric values of face cards"""
    JACK = 11
    QUEEN = 12
    KING = 13
    ACE = 14

    # This is new:
    # Define a custom method to call when a case gets "stringified"
    def __str__(self):
        return self.name[0] 
```

现在在我们的卡类:

```
class Card:
    # ...
    def __str__(self):
        return str(self.value) + self.suit 
```

因为我们用这种方式为`FaceCard`定义了自定义的`__str__`方法，所以不管这张卡是不是正面卡，这段代码都将有效！

```
>>> eight = Card("8H")
>>> ace = Card("AD")
>>> eight.value
8
>>> eight.suit
"H"
>>> ace.value
FaceCard.ACE
>>> ace.suit
"D"
>>> str(eight)
"8H"            # str(eight) == str(8) + "H" >>> str(ace)
"AD"            # str(ace) == str(FaceCard.ACE) + "D"
                # str(FaceCard.ACE) == "ACE"[0] == "A" 
```

对吗？？对吗？！？！我是说，你能有多优雅？

* * *

我想知道*你以前是如何*使用`enum`的。你找到一个简洁的用例了吗？在`enum`模块中还有其他我甚至还没有发现的宝石吗？
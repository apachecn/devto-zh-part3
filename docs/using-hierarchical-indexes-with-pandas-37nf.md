# 对熊猫使用层次索引

> 原文：<https://dev.to/hackersandslackers/using-hierarchical-indexes-with-pandas-37nf>

[![Using Hierarchical Indexes With Pandas](img/338f21e0949ea961f18fad0af8b6a6ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--luj4xZCc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/hackersandslackers-cdn/2019/05/pandas-reindexing%402x.jpg)

我最近陷入了很多尴尬的对话，其中大部分都是关于我如何度过空闲时间的。很明显，“浏览 Python 库文档，希望找到 dope 特性”并不被大多数人认为是一个相关的爱好。至少你在这里，所以我必须偶尔做一些正确的事情。

今天我们将冒险进入熊猫指数的世界。不只是任何旧的索引...*分级*指标。分层索引采用了为行设置标识符的思想，并通过允许我们灵活地设置多个标识符来扩展这一概念:这些索引保持彼此之间的父/子关系。这些关系使我们能够做一些很酷的事情，比如立即将我们的数据组织成组，而不需要执行分组。如果使用正确，当我们需要做一些真正强大的分析时，这些关系会非常有帮助。

分层索引(也称为多索引)帮助我们更快地组织、查找和聚集信息，而几乎没有成本。以这种方式组织数据非常酷，但一开始也很难掌握。我们一步一步来。

## 创建具有分层索引的数据帧

有许多方法可以在一个数据帧上声明多个索引——可能比您需要的多得多。最直接的方法就像设置一个单一的索引；我们传递给`index=`的是一个列数组，而不是一个字符串！

为了展示索引的艺术，我们将使用一个包含几年 NHL 游戏数据的数据集。让我们开始吧:

```
import pandas as pd

nhlDF = pd.read_csv('data/nhl.csv')
nhlDF['date'] = pd.to_datetime(nhlDF['date'])
print(nhlDF.head(5))
print(nhlDF.tail(5)) 
```

| 季节 | 团队名称 | 游戏 id | 获胜 | 稳定的 | 手别碰-自动的(Hands Off-Automatic)ˌ(美)屋主协会(Home Owner Association) | 客场进球 | 主页 _ 目标 | away 团队名称 | 家庭团队 _ 名称 | 日期 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Twenty million one hundred and two thousand and eleven | 雪崩 | Two billion ten million twenty thousand and four | 真实的 | 仇恨失控 | 家 | three | four | Blackhawks | 雪崩 | 2010 年 10 月 8 日 |
| Twenty million one hundred and two thousand and eleven | 雪崩 | Two billion ten million twenty thousand and thirty-one | 错误的 | 车辆注册号 | 离开 | Two | four | 雪崩 | 飞行者 | 2010 年 10 月 11 日 |
| Twenty million one hundred and two thousand and eleven | 雪崩 | Two billion ten million twenty thousand and thirty-four | 真实的 | 因此 | 离开 | five | four | 雪崩 | 红翼 | 2010 年 10 月 12 日 |
| Twenty million one hundred and two thousand and eleven | 雪崩 | Two billion ten million twenty thousand and forty-eight | 真实的 | 车辆注册号 | 离开 | three | Two | 雪崩 | 魔鬼 | 2010 年 10 月 15 日 |
| Twenty million one hundred and two thousand and eleven | 雪崩 | Two billion ten million twenty thousand and fifty-seven | 错误的 | 车辆注册号 | 离开 | Two | five | 雪崩 | 岛民 | 2010 年十月 16 日 |
| Twenty million one hundred and eighty-two thousand and nineteen | 荒野 | Two billion eighteen million twenty-one thousand two hundred and two | 真实的 | 车辆注册号 | 离开 | three | Two | 荒野 | 黄金骑士 | 2019 年 3 月 30 日 |
| Twenty million one hundred and eighty-two thousand and nineteen | 荒野 | Two billion eighteen million twenty-one thousand two hundred and seventeen | 错误的 | 车辆注册号 | 离开 | Zero | four | 荒野 | 土狼 | 2019 年 3 月 31 日 |
| Twenty million one hundred and eighty-two thousand and nineteen | 荒野 | Two billion eighteen million twenty-one thousand two hundred and thirty-four | 真实的 | 车辆注册号 | 家 | one | five | 喷射 | 荒野 | 2019 年 4 月 3 日 |
| Twenty million one hundred and eighty-two thousand and nineteen | 荒野 | Two billion eighteen million twenty-one thousand two hundred and fifty | 错误的 | 车辆注册号 | 家 | three | Zero | Bruins | 荒野 | 2019 年 4 月 5 日 |
| Twenty million one hundred and eighty-two thousand and nineteen | 荒野 | Two billion eighteen million twenty-one thousand two hundred and sixty-seven | 错误的 | 车辆注册号 | 离开 | Zero | three | 荒野 | 明星 | 2019 年 4 月 7 日 |

数据集中的每一行都包含关于曲棍球比赛结果的信息。我们有一个名为**季节**的行，值为`20102011`。这个整数代表游戏进行的 NHL 赛季(在这个例子中，`20102011`指的是 *2010-2011* 赛季)。我们还有像 **team_name** 和 **game_id** 这样的列，它们是索引的很好的候选者。

成功使用分层索引的关键是首先考虑我们希望如何看待我们的数据，以及我们试图回答哪些问题。很快，我已经知道我将首先通过钻研**赛季**来查看这些数据:球队随着赛季的不同而发生巨大的变化，我更感兴趣的是看看我们的球队在每个赛季的表现，而不是 8 年的表现。接下来，我将按 **team_name** 整理我们的数据，看看 X 队在给定赛季的表现。一个团队的表现是由游戏决定的，因此 **game_id** 将是我们的第三个也是最后一个指标。

我们是这样设置索引的:

```
# Create Multiindex nhlDF.set_index(['season', 'team_name', 'game_id'], inplace=True)
nhlDF.sort_index(inplace=True)
print(nhlDF.head(5))
print(nhlDF.tail(5)) 
```

让我们看看事情是如何变化的:

[![Using Hierarchical Indexes With Pandas](img/249a0e23bd56520751aff6577c51d51d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iD_xKH4p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackersandslackers.com/conteimg/2019/05/pands-multiindex-initial.jpg) 

<figcaption>我们的数据带有层次索引。</figcaption>

这已经让*看起来*更有条理了，但是多重索引不仅仅是外表。我们在数据中创建了一个关系层次！有了这些索引，我们已经悄悄地在数据中做了细微的区分。例如，我们已经区分出 2018-2019 赛季的黄金骑士与 2017-2018 赛季进入斯坦利杯的黄金骑士是一个*不同的实体*。我们还将黄金骑士这一季玩的所有游戏与这一季的黄金骑士联系起来，而这一季的黄金骑士又是`2018-2019`的子代。结果，我们了解了每款游戏属于哪个赛季。

虽然有了这种索引层次结构，但是存储在所有 3 个索引中的信息与每行中的值是不可分的。我会告诉你我的意思。

### 2D 系列

让我们来看看新索引数据中的一个系列。“你对熊猫系列了如指掌”:它们是很好的一维数据列，没有任何有趣的事情:

```
# Demonstrate 2D Series print(nhlDF['date'].head(10)) 
```

```
season team_name game_id    
20102011 Avalanche 2010020004 2010-10-08
                     2010020031 2010-10-11
                     2010020034 2010-10-12
                     2010020048 2010-10-15
                     2010020057 2010-10-16
                     2010020070 2010-10-18
                     2010020090 2010-10-22
                     2010020108 2010-10-24
                     2010020122 2010-10-27
                     2010020136 2010-10-29
Name: date, dtype: datetime64[ns] 
```

哇，这是什么有趣的事情？！我们打印了一个带有`nhlDF['date']`的单列，那么 4 列是怎么回事呢？正如我前面提到的，我们用层次索引创建的关系使得这些关系不可分割:无论我们如何处理数据，索引都会随之而来。本质上，这使得我们的系列是二维的。这真的很酷，因为我们可以像平常一样操作我们的序列(`nhlDF['date'][0]`仍然获得第一个值，等等)，但是现在我们有了关于每一行的关联元数据。

如上所述，有很多其他方法来定义熊猫的多重指数。Pandas 有一些方法，比如`MultiIndex.from_arrays`和`MultiIndex.from_tuples`，它们给了我们一些灵活性，但是我个人没有兴趣让任何人对这些感到厌烦。如果你觉得无聊，读读[文件](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.MultiIndex.html)。

## 检查和修改数据帧索引

在我们变得疯狂之前，让我们快速了解一些基本的多指数基础。看看当我们检查数据帧上的索引时会发生什么:

```
# Inspect indexes print(nhlDF.index) 
```

```
MultiIndex(levels=[[20102011, 20112012, 20122013, 20132014, 20142015, ...],
                   ['Avalanche', 'Blackhawks', 'Blue Jackets', ...],
                   [2010020001, 2010020002, 2010020003, 2010020004, ...]],
           labels=[[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...]],
           names=['season', 'team_name', 'game_id']) 
```

一个百搭`MultiIndex`出现！在熊猫中，`MultiIndex`本身就是一个数据类型。如果您需要动态创建具有高度复杂逻辑的索引，这可能会很有用。非常酷的是，我们可以从任何数据帧中取出索引方案，并将该方案传递到新的数据帧中。

我已经截断了由`print(nhlDF.index)`返回的值，以避免粘贴成千上万的结果。我们来分析一下。**名称**属性重申了我们的层次结构:`['season', 'team_name', 'game_id']`。我们还看到我们的指数的*值*被填充到**级别**。

一个**级别**是指我们层次结构中的一个索引的名称。我们最左边的指标是我们最高级别的指标，可以称为**0 级**。在我们的例子中，**季**是 0 级。等级也可以通过它们的名字来引用，因此`level=0`可以与`level='season'`互换。修改级别的语法与处理列的语法相同。

让我们看看我们的指数值 tho:

```
# Inspect index values print(nhlDF.index.values) 
```

```
[(20102011, 'Avalanche', 2010020004) (20102011, 'Avalanche', 2010020031)
 (20102011, 'Avalanche', 2010020034) ... (20182019, 'Wild', 2018021234)
 (20182019, 'Wild', 2018021250) (20182019, 'Wild', 2018021267)] 
```

哦，太好了，元组！一般来说，层次索引喜欢被当作元组值来处理。现在没什么用，但以后可能会有用。

### 按指标排序数据

你可能已经注意到，当我们创建索引的时候，我超过了`nhlDF.sort_index(inplace=True)`。在设置了层次索引之后，对我们的索引进行排序是非常重要的；如果我们没有这样做，选择和汇总我们的数据实际上会导致错误。如果我们考虑索引被“分组”的方式，就能理解为什么会发生这种情况:如果数据仍然分散在各处，我们如何以一种视觉上令人愉快的方式对数据进行分组？

### 重置指标

请记住，列和索引并不相同，即使它们看起来很相似。当一个列成为一个索引时，原来的“列”被删除，一个索引被添加到我们的数据帧中，该索引包含在所述列中的值。虽然我们仍然有**赛季**、**团队名称**和**游戏 id** 的值，但从技术上讲，我们的数据帧比原始导入少了 3 列。我*做过什么*？！这是不是意味着我偷偷销毁了你一直跟踪的数据集？*怎么可能*我！

冷静点。我们到目前为止所做的一切都可以在任何时候使用`nhlDF.reset_index(inplace=True)`立即撤销。重置多索引数据帧上的索引会拆分数据并重新添加原始列。

值得考虑的是，如果使用正确，这将会多么强大。我能够在一瞬间在一个空闲集群上索引 22，694 行，并且可以同样快速轻松地忽略这一点。如果我们愿意，我们可以仅仅为了回答几个问题而随意使用数据帧索引，并在任何时候恢复。让我们看看这些索引如何帮助我们解决这些问题。

### 其他指标修改

我们可以通过索引定制做更多的事情。了解这些东西的用处需要一点时间，但是了解这些并没有坏处:

*   **互换指数等级** : `df.swaplevel(i='level_name_1', j='level_name_2')`
*   **重命名索引** : `df.index.names = ['name1', 'name2']`
*   **删除单个指标等级** : `df.unstack(level=0)`

## 选择分层索引中的数据

现在我们可以开始做一些有意义的事情了。让我们看看使用层次索引如何帮助我们找到我们要找的数据。

### 利用。loc()

是我们的好朋友`.loc()`！让我们用`.loc()`来查找 *2010-2011* 赛季的所有比赛:

```
# Select 2010-2011 season using iloc print(nhlDF.loc[20102011, :].head(10)) 
```

我们在这里搜索`20102011`的实例。我们通过`:`为匹配的每一行指定“所有列”。如果我们愿意，我们可以用列标签替换`:`来查找“行标签为 20102011 的[列]中的所有值”。

| 团队名称 | 获胜 | 客场进球 | 主页 _ 目标 |
| --- | --- | --- | --- |
| 闪电 | Sixty-two | Two hundred and sixty | Two hundred and eighty-seven |
| 火焰 | Fifty | Two hundred and fifty | Two hundred and sixty-six |
| Bruins | forty-nine | Two hundred and nineteen | Two hundred and fifty-five |
| 岛民 | Forty-eight | Two hundred and six | Two hundred and eighteen |
| 资本 | Forty-eight | Two hundred and fifty-two | Two hundred and seventy-five |
| 食肉动物 | Forty-seven | Two hundred and twenty | Two hundred and thirty-four |
| 喷射 | Forty-seven | Two hundred and thirty-nine | Two hundred and seventy-seven |
| 蓝色夹克 | Forty-seven | Two hundred and fifty | Two hundred and forty |
| 鲨鱼 | Forty-six | Two hundred and sixty | Two hundred and ninety |
| 飓风 | Forty-six | Two hundred and twenty-five | Two hundred and forty-three |
| 枫叶 | Forty-six | Two hundred and sixty-three | Two hundred and seventy-four |
| 布鲁斯音乐 | Forty-five | Two hundred and thirty-four | Two hundred and thirty-six |
| 加拿大人 | forty-four | Two hundred and twenty-three | Two hundred and sixty-two |
| 企鹅 | forty-four | Two hundred and sixty-three | Two hundred and fifty-one |
| 明星 | Forty-three | One hundred and eighty-six | Two hundred and twenty-six |
| 黄金骑士 | Forty-three | Two hundred and eighteen | Two hundred and sixty-one |
| 土狼 | Thirty-nine | Two hundred and three | Two hundred and thirty-three |
| 雪崩 | Thirty-eight | Two hundred and forty-five | Two hundred and sixty-one |
| 飞行者 | Thirty-seven | Two hundred and fifty-eight | Two hundred and sixty-seven |
| 荒野 | Thirty-seven | Two hundred and twenty-five | Two hundred and twenty-three |
| Blackhawks | Thirty-six | Two hundred and sixty-eight | Two hundred and ninety-four |
| 豹 | Thirty-six | Two hundred and fifty | Two hundred and ninety-seven |
| 加油工 | Thirty-five | Two hundred and forty-one | Two hundred and sixty-five |
| 鸭 | Thirty-five | Two hundred and seven | Two hundred and forty-three |
| 法裔加拿大人 | Thirty-five | Two hundred and twenty-nine | Two hundred and fifty |
| 军刀 | Thirty-three | Two hundred and thirty-three | Two hundred and sixty-four |
| 兰杰斯 | Thirty-two | Two hundred and twenty-eight | Two hundred and seventy-one |
| 红翼 | Thirty-two | Two hundred and thirty-nine | Two hundred and sixty-five |
| 《列王纪》 | Thirty-one | Two hundred and twenty-two | Two hundred and forty-three |
| 魔鬼 | Thirty-one | Two hundred and eighteen | Two hundred and seventy-nine |
| 参议员 | Twenty-nine | Two hundred and forty-two | Three hundred and two |

嘣。这些都是 2010-2011 年的游戏。注意到我们的**赛季**指数似乎不见了吗？当我们选择和/或聚合到一个索引中时，我们正在处理的索引级别会从结果中省略，以避免冗余。

### 利用。xs()

`.xs()`代表[横截面](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.xs.html):它接受一个要在索引中找到的值，允许通过索引更容易地选择行。下面的代码片段将产生与我们用`.loc()`完成的相同的结果:

```
# Select 2010-2011 season using .xs() print(nhlDF.xs(20102011).head(10)) 
```

如果我们用一个队名而不是一个赛季来尝试这个会怎么样？

```
# Select Blackhawks Games print(nhlDF.xs('Blackhawks').head(10)) 
```

我对此有不好的预感...

```
KeyError 'Blackhawks' 
------------------------------------------------------------------
TypeError Traceback (most recent call last)
pandas/_libs/index.pyx in pandas._libs.index.IndexEngine.get_loc()

pandas/_libs/hashtable_class_helper.pxi in pandas._libs.hashtable.Int64HashTable.get_item()

TypeError: an integer is required

... 
```

哪里出了问题？注意错误:`TypeError: an integer is required`。默认情况下，`.xs()`总是根据级别 0 进行搜索，除非另有说明。我们**意外地**根据我们的赛季指数寻找`Blackhawks`，而不是我们的 **team_name** 指数。我们可以通过设置我们的**级别**属性来解决这个问题:

```
# Select Blackhawks (in level 2) using .xs() print(nhlDF.xs('Blackhawks', level=1).head(10)) 
```

或者可选地...

```
# Select Blackhawks (in level 2) using .xs() print(nhlDF.xs('Blackhawks', level='team_name').head(10)) 
```

这两行中的任何一行都将以相同的结果结束:

| 季节 | 获胜 | 游戏 id | 稳定的 | 手别碰-自动的(Hands Off-Automatic)ˌ(美)屋主协会(Home Owner Association) | 客场进球 | 主页 _ 目标 | away 团队名称 | 家庭团队 _ 名称 | 日期 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Twenty million one hundred and two thousand and eleven | 错误的 | Two billion ten million twenty thousand and four | 仇恨失控 | 离开 | three | four | Blackhawks | 雪崩 | 2010-10-08 |
| Twenty million one hundred and two thousand and eleven | 错误的 | Two billion ten million twenty thousand and twenty-one | 车辆注册号 | 家 | three | Two | 红翼 | Blackhawks | 2010-10-10 |
| Twenty million one hundred and two thousand and eleven | 真实的 | Two billion ten million twenty thousand and thirty | 车辆注册号 | 离开 | four | three | Blackhawks | 军刀 | 2010-10-11 |
| Twenty million one hundred and two thousand and eleven | 错误的 | Two billion ten million twenty thousand and forty | 车辆注册号 | 家 | three | Two | 食肉动物 | Blackhawks | 2010-10-14 |
| Twenty million one hundred and two thousand and eleven | 真实的 | Two billion ten million twenty thousand and fifty-one | 车辆注册号 | 离开 | five | Two | Blackhawks | 蓝色夹克 | 2010-10-15 |
| Twenty million one hundred and two thousand and eleven | 真实的 | Two billion ten million twenty thousand and sixty-three | 车辆注册号 | 家 | three | four | 军刀 | Blackhawks | 2010-10-17 |
| Twenty million one hundred and two thousand and eleven | 真实的 | Two billion ten million twenty thousand and seventy-three | 仇恨失控 | 家 | Two | three | 布鲁斯音乐 | Blackhawks | 2010-10-19 |
| Twenty million one hundred and two thousand and eleven | 真实的 | Two billion ten million twenty thousand and eighty | 因此 | 家 | one | Two | 法裔加拿大人 | Blackhawks | 2010-10-21 |
| Twenty million one hundred and two thousand and eleven | 错误的 | Two billion ten million twenty thousand and ninety-six | 车辆注册号 | 离开 | Two | four | Blackhawks | 布鲁斯音乐 | 2010-10-23 |
| Twenty million one hundred and two thousand and eleven | 错误的 | Two billion ten million twenty thousand one hundred and seven | 车辆注册号 | 家 | three | Two | 蓝色夹克 | Blackhawks | 2010-10-24 |

## 多指标聚合

您可能已经开始考虑多索引数据帧和使用`.groupby()`分组的数据帧之间的相似之处。很好！让我们从上个赛季开始，通过提取每个球队的统计数据来研究一下总体数据:

```
# Get team stats for last season lastSeasonDF = nhlDF.xs(20182019, level='season') # Group by last season lastSeasonDF = lastSeasonDF.groupby(level=0).sum() # Leader board of teams lastSeasonDF.sort_values(by=['won'], ascending=False, inplace=True)
print(lastSeasonDF) 
```

注意我们是如何使用`groupby(level=0)`按照索引级别进行分组的。我们按**队名**分组，通常是 1 级，但是我们通过了 0 级:这是因为在上面的线上运行`.xs()`从这个选择的索引中省略了**赛季**，因此**队名**变成了新的 0 级。

| 团队名称 | 获胜 | 客场进球 | 主页 _ 目标 |
| --- | --- | --- | --- |
| 闪电 | Sixty-two | Two hundred and sixty | Two hundred and eighty-seven |
| 火焰 | Fifty | Two hundred and fifty | Two hundred and sixty-six |
| Bruins | forty-nine | Two hundred and nineteen | Two hundred and fifty-five |
| 岛民 | Forty-eight | Two hundred and six | Two hundred and eighteen |
| 资本 | Forty-eight | Two hundred and fifty-two | Two hundred and seventy-five |
| 食肉动物 | Forty-seven | Two hundred and twenty | Two hundred and thirty-four |
| 喷射 | Forty-seven | Two hundred and thirty-nine | Two hundred and seventy-seven |
| 蓝色夹克 | Forty-seven | Two hundred and fifty | Two hundred and forty |
| 鲨鱼 | Forty-six | Two hundred and sixty | Two hundred and ninety |
| 飓风 | Forty-six | Two hundred and twenty-five | Two hundred and forty-three |
| 枫叶 | Forty-six | Two hundred and sixty-three | Two hundred and seventy-four |
| 布鲁斯音乐 | Forty-five | Two hundred and thirty-four | Two hundred and thirty-six |
| 加拿大人 | forty-four | Two hundred and twenty-three | Two hundred and sixty-two |
| 企鹅 | forty-four | Two hundred and sixty-three | Two hundred and fifty-one |
| 明星 | Forty-three | One hundred and eighty-six | Two hundred and twenty-six |
| 黄金骑士 | Forty-three | Two hundred and eighteen | Two hundred and sixty-one |
| 土狼 | Thirty-nine | Two hundred and three | Two hundred and thirty-three |
| 雪崩 | Thirty-eight | Two hundred and forty-five | Two hundred and sixty-one |
| 飞行者 | Thirty-seven | Two hundred and fifty-eight | Two hundred and sixty-seven |
| 荒野 | Thirty-seven | Two hundred and twenty-five | Two hundred and twenty-three |
| Blackhawks | Thirty-six | Two hundred and sixty-eight | Two hundred and ninety-four |
| 豹 | Thirty-six | Two hundred and fifty | Two hundred and ninety-seven |
| 加油工 | Thirty-five | Two hundred and forty-one | Two hundred and sixty-five |
| 鸭 | Thirty-five | Two hundred and seven | Two hundred and forty-three |
| 法裔加拿大人 | Thirty-five | Two hundred and twenty-nine | Two hundred and fifty |
| 军刀 | Thirty-three | Two hundred and thirty-three | Two hundred and sixty-four |
| 兰杰斯 | Thirty-two | Two hundred and twenty-eight | Two hundred and seventy-one |
| 红翼 | Thirty-two | Two hundred and thirty-nine | Two hundred and sixty-five |
| 《列王纪》 | Thirty-one | Two hundred and twenty-two | Two hundred and forty-three |
| 魔鬼 | Thirty-one | Two hundred and eighteen | Two hundred and seventy-nine |
| 参议员 | Twenty-nine | Two hundred and forty-two | Three hundred and two |

## 列带有分层索引

直到现在都是有趣的游戏...这种情况即将改变。是时候摘下手套了。到目前为止，我们一直认为行是 Pandas 中唯一可以索引的元素。我们不仅可以索引列，还可以创建一个数据帧，同时跨*行和*列使用层次索引。

我将使用 NHL 球员每场比赛表现的数据集进行演示(我已经将此限制为传单上的球员)。这是原始数据的预览:

| 月 | 游戏 id | 名字 | 助攻 | 堵塞的 | 目标 | 打击 | 发射 | 外卖食品 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 安德鲁·麦克唐纳 | Zero | three | Zero | Zero | Two | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 克劳德·吉罗 | Zero | Zero | Zero | Zero | one | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 戴尔·维斯 | Zero | Zero | Zero | Zero | one | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 伊万·普罗沃罗夫 | Zero | Zero | Zero | Two | Two | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 雅各布·沃拉切克 | Zero | Zero | Zero | Zero | three | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 詹姆斯·范·里姆斯迪克 | Zero | Two | Zero | Zero | Two | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 约旦福利 | Zero | Zero | Zero | one | Zero | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 迈克尔·拉夫尔 | Zero | Zero | Zero | Zero | one | one |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 奥斯卡·林德姆 | Zero | Zero | Zero | Zero | one | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 菲尔·瓦龙 | Zero | Zero | Zero | Zero | Two | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 拉德科·古达斯 | Zero | Zero | Zero | three | one | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 罗伯特·哈格 | Zero | one | Zero | four | Two | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 斯科特·劳顿 | Zero | Zero | Zero | one | three | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 肖恩·库蒂耶 | Zero | one | Zero | Zero | four | one |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 沙恩·戈斯蒂舍雷 | Zero | four | Zero | Two | one | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 特拉维斯·科尼克尼 | Zero | Zero | Zero | one | four | one |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 特拉维斯·桑海姆 | Zero | five | Zero | Zero | Zero | Zero |
| 一月 | Two billion eighteen million twenty thousand six hundred and thirteen | 韦恩·西蒙兹 | Zero | Zero | Zero | Two | Two | Zero |

我们如何一次跨两个轴设置多个索引？当然是数据透视表啦！没错，您一直在使用多索引数据帧播放:

```
import pandas as pd

playersDF = pd.read_csv('flyers_players_20182019.csv') # Load CSV playersDF.fillna(method='ffill', inplace=True) # Clean empty values pivotDF = playersDF.pivot_table(index=['month', 'game_id'],
                                columns='name',
                                aggfunc='sum',
                                fill_value=0).swaplevel(axis=1).sort_index(1)
print(pivotDF) 
```

我们能够在数据透视表上设置多个行索引，就像我们之前所做的一样:通过`index=['month', 'game_id']`传递一个列列表。数据透视表的其余部分看起来很标准，但是我们用`.swaplevel()`做了一点小魔术。以下是我们得到的结果:

[![Using Hierarchical Indexes With Pandas](img/529cdd3abf8a4f437c94f20a023018da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RB7nZFqL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackersandslackers.com/conteimg/2019/05/pandas-multiindex-pivot.png) 

<figcaption>具有跨行和列的分层索引的数据框架。</figcaption>

我们的每个列现在都有*两个*标签！通过重复团队中每个球员的每个统计数据，我们可以更容易地得出个人球员表现的结论！例如，让我们看看 Giroux 在 10 月份提供了什么样的数据:

```
# Get Claude Giroux's stats for October 2018 girouxDF = pivotDF.loc['October', 'Claude Giroux']
print(girouxDF) 
```

这给出了行标签匹配**十月**和列标签匹配**吉鲁**的值的横截面:

|  |  |  |  |  |  |  | 克劳德·吉罗 |
| --- | --- | --- | --- | --- | --- | --- | --- |
|  | 游戏 id | 助攻 | 堵塞的 | 目标 | 打击 | 发射 | 外卖食品 |
| 十月 | Two billion eighteen million twenty thousand and fourteen | one | Zero | Zero | Zero | Zero | Zero |
|  | Two billion eighteen million twenty thousand and twenty-six | one | Zero | Zero | Zero | Two | Zero |
|  | Two billion eighteen million twenty thousand and thirty-six | Two | Zero | Zero | Zero | four | Zero |
|  | Two billion eighteen million twenty thousand and forty-two | one | Zero | one | Zero | seven | Zero |
|  | Two billion eighteen million twenty thousand and fifty-eight | Zero | Zero | Zero | Zero | Two | Zero |
|  | Two billion eighteen million twenty thousand and eighty | Zero | Zero | Two | Two | eight | one |
|  | Two billion eighteen million twenty thousand and ninety-two | Two | Zero | Zero | Zero | five | Zero |
|  | Two billion eighteen million twenty thousand one hundred and two | one | Zero | Zero | Zero | Zero | one |
|  | Two billion eighteen million twenty thousand one hundred and eighteen | Zero | Zero | Zero | Zero | five | three |
|  | Two billion eighteen million twenty thousand one hundred and thirty-four | Zero | one | Zero | one | five | Zero |
|  | Two billion eighteen million twenty thousand one hundred and forty-nine | Zero | Zero | Zero | one | Two | Zero |
|  | Two billion eighteen million twenty thousand one hundred and seventy-six | Two | Zero | Zero | Zero | four | Zero |

真漂亮。我们刚刚完成的令人惊叹的事情是，我们没有破坏或修改任何数据来达到这一点。我们仍然使用我们开始时使用的数据集:唯一的不同是我们添加了索引关系。

最后，让我们合计这些值，得出 10 月份的总数，仅供参考:

```
# Aggregate Giroux's stats girouxDF = pivotDF.loc['October', 'Claude Giroux'].sum()
print(girouxDF) 
```

```
assists 10
blocked 1
goals 3
hits 4
shots 44
takeaways 5
dtype: int64 
```
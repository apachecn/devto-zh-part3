# 使用 Python 正确区分 Excel 数据的大小写

> 原文：<https://dev.to/katiekodes/proper-casing-excel-data-with-python-2p0n>

[Helen Anderson](https://dev.to/helenanders26) 的文章“ [Excel 系列:公式和函数](https://dev.to/helenanders26/happy-birthday-ms-excel-part-1-m0a)”今天在 Twitter 上流传开来，她的第一步非常简单，我想，“嘿，让我们来看看它们在 Python 中是什么样子，做一点比较和对比。”

我需要回去工作了，所以我只学到了她的第一个例子`=PROPER(A1)`，但这本身就很有趣。

## 样本数据

首先，我建立了一个非常小的表格，并将其保存在一个我决定称为`df`的变量中:

| fname | 公司 |
| --- | --- |
| 阿努什 | 苹果 |
| 贝尼塔 | 他们喝酒了 |
| 凯茜 | cvs |
| 达尔维什 | （里面或周围有树的）小山谷 |

在我的例子中，完成这项工作的 Python 代码如下，因为我将示例数据作为代码的一部分:

```
from collections import OrderedDict
import pandas

initdata = OrderedDict([('fname', ['anush','benita','cathy','darweesh']),
          ('company', ['apple','boiron','cvs','dell']) ])
df = pandas.DataFrame(initdata) 
```

但通常情况下，如果我的数据在一个名为`sample.xlsx` :
的电子表格中，它看起来更像这样

```
import pandas
df = pandas.read_excel('sample.xlsx') 
```

用`print(df)` *显示在我的控制台上(在包含了一行代码`pandas.set_option('expand_frame_repr', False)`来美化输出之后)*，看起来是这样的:

```
 fname company
0     anush   apple
1    benita  boiron
2     cathy     cvs
3  darweesh    dell 
```

## ——正确区分大小写在 Python 中比在 Excel 中“更快”

Python 的 Pandas 模块 *("module" =扩展命令集)*适合使用最少的代码一次性对一整组数据(比如一列)重复相同的过程。

因此，尽管 Helen 的`=PROPER(A1)` Excel 公式将仅用单元格“A1”的内容的“标题大小写”副本填充您放入的任何单个单元格，但是当您向电子表格中添加一个全新的列并且将*的每个*的名字全部标题大小写时， **Pandas 命令是最简单的。**

请注意，在熊猫中，您通常通过它们的名称来引用列，而不是通过字母表中指示它们出现顺序的字母来引用。

本 Python 代码:

```
df['fname_proper'] = df['fname'].str.title()
print(df) 
```

修改存储在变量`df`中的表格内容，并显示输出:

```
 fname company fname_proper
0     anush   apple        Anush
1    benita  boiron       Benita
2     cathy     cvs        Cathy
3  darweesh    dell     Darweesh 
```

这些数据是:

| fname | 公司 | fname_proper |
| --- | --- | --- |
| 阿努什 | 苹果 | 阿努什 |
| 贝尼塔 | 他们喝酒了 | 贝尼塔 |
| 凯茜 | cvs | 凯茜 |
| 达尔维什 | （里面或周围有树的）小山谷 | 达尔维什 |

### 一个“速度”字

[![Slicing bread with a chainsaw](img/c932409cfa3e3816c5ce0a750f4bf7b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3XnwDV5y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/baguette_chainsaw.jpg)

我在这一节的标题中用引号将“更快”括起来，因为编写一个完整的 Python 程序向电子表格中添加一列是否真的比在 Excel 的额外空白处键入一个公式并将公式复制到页面末尾更“快”是有争议的。

不，你知道吗？这其实没什么可争论的。

在现实世界中，应该是用电锯切面包。

我所说的“更快”是指 Python 的 Pandas 模块的命令旨在促进在大型数据集中的思考，而当您考虑如何使用 Excel 命令时，您更多的是“一次一个单元格”地工作。

注意:在 Python 中，*做的事情*开始变得更快是因为重复或复杂。示例:

*   您有一个 300 列的电子表格，其中 100 列的标题中有单词“Name”。您需要为标题中包含单词“Name”的每一列创建一个副本——复制副本的内容。你可以通过编码来节省大量的时间，而不是手工完成。我刚才展示的 Python 就是这些代码的一部分。
*   你已经在用 Python 处理你的电子表格了，因为你正在做一些其他种类的复杂操作*(比如电子表格之间的多列`VLOOKUP`)*。为什么不在 Python 程序中增加一个操作，而不是在 Python 完成“困难”的工作后，让自己在 Excel 中手工完成工作？我经常发现这种情况，例如，将“人员”数据与“活动”数据结合起来生成“活动成员”文件以插入到 Salesforce 中。

## 只加“Anush”

但也许我有点冒昧地假设海伦曾经打算“复制”更多的行。

也许她真的只是想复制一个单细胞。

让我们看看 Python 中两种非常相似的方法。

对于初学者来说，它们之间的差异可以忽略不计——由于被称为“[数据类型](https://en.wikipedia.org/wiki/Data_type)的概念，根据您键入“左侧”的方式，您必须键入`=`命令的“右侧”的方式略有不同

这两种方法的共同点是它们都很复杂。

(Python 的“Pandas”命令在进行这种单细胞操作时并没有针对简单输入进行优化。对于大多数人想做的事情来说，这被认为是例外，而不是常态。)

此外，在海伦的例子中，它们都将“行`1`”称为行`0`。

### 接近 1

本 Python 代码:

```
df.loc[0,'fname_1st_row_only_proper_v1'] = df.loc[0,'fname'].title()
print(df) 
```

修改存储在变量`df` *(记住，现在还有一个`fname_proper`列)*中的表的内容，并显示这个输出:

```
 fname company fname_proper fname_1st_row_only_proper_v1
0     anush   apple        Anush                        Anush
1    benita  boiron       Benita                          NaN
2     cathy     cvs        Cathy                          NaN
3  darweesh    dell     Darweesh                          NaN 
```

这些数据是:

| fname | 公司 | fname_proper | fname_1st_row_only_proper_v1 |
| --- | --- | --- | --- |
| 阿努什 | 苹果 | 阿努什 | 阿努什 |
| 贝尼塔 | 他们喝酒了 | 贝尼塔 |  |
| 凯茜 | cvs | 凯茜 |  |
| 达尔维什 | （里面或周围有树的）小山谷 | 达尔维什 |  |

### 接近 2

本 Python 代码:

```
df.loc[[0],'fname_1st_row_only_proper_v2'] = df['fname'].str.title()
print(df) 
```

修改存储在变量`df` *(记住，在我们运行命令之前已经有 4 列)*中的表的内容，并显示这个输出:

```
 fname company fname_proper fname_1st_row_only_proper_v1 fname_1st_row_only_proper_v2
0     anush   apple        Anush                        Anush                        Anush
1    benita  boiron       Benita                          NaN                          NaN
2     cathy     cvs        Cathy                          NaN                          NaN
3  darweesh    dell     Darweesh                          NaN                          NaN 
```

这些数据是:

| fname | 公司 | fname_proper | fname_1st_row_only_proper_v1 | fname_1st_row_only_proper_v2 |
| --- | --- | --- | --- | --- |
| 阿努什 | 苹果 | 阿努什 | 阿努什 | 阿努什 |
| 贝尼塔 | 他们喝酒了 | 贝尼塔 |  |  |
| 凯茜 | cvs | 凯茜 |  |  |
| 达尔维什 | （里面或周围有树的）小山谷 | 达尔维什 |  |  |

## 外卖

我希望你能从这篇博文中学到的是:

1.  一旦您熟悉了某些电子表格修改任务，用 Python 编写它们的代码并不比用 Excel 复杂多少。现在你知道了！(添加新的标题大小写一致的列副本。)
2.  为工作选择合适的工具。与点击 Excel 中的单元格并编辑它们的公式相比，使用 Python 编码不仅有开销，而且**Python 中的一些命令比其他命令更简单**，*就像 Excel 中的*。

## 全码

这是完整的脚本，你可以复制并粘贴到[一个 Repl.it 编辑器](https://repl.it/languages/python3)中，为你自己运行:

```
from collections import OrderedDict
import pandas
pandas.set_option('expand_frame_repr', False)

def p(x):
    print(x)
    print('---DIVIDER---')

initdata = OrderedDict([('fname', ['anush','benita','cathy','darweesh']),
          ('company', ['apple','boiron','cvs','dell']) ])
df = pandas.DataFrame(initdata)
p(df)

# "=PROPER(A1)" imitation #1 of 3 (entire "fname" column) df['fname_proper'] = df['fname'].str.title()
p(df)

# "=PROPER(A1)" imitation #2 of 3 (single cell approach #1 of 2) df.loc[0,'fname_1st_row_only_proper_v1'] = df.loc[0,'fname'].title()
p(df)

# "=PROPER(A1)" imitation #3 of 3 (single cell approach #2 of 2) df.loc[[0],'fname_1st_row_only_proper_v2'] = df['fname'].str.title()
p(df) 
```

及其全部输出:

```
 fname company
0     anush   apple
1    benita  boiron
2     cathy     cvs
3  darweesh    dell
---DIVIDER---
      fname company fname_proper
0     anush   apple        Anush
1    benita  boiron       Benita
2     cathy     cvs        Cathy
3  darweesh    dell     Darweesh
---DIVIDER---
      fname company fname_proper fname_1st_row_only_proper_v1
0     anush   apple        Anush                        Anush
1    benita  boiron       Benita                          NaN
2     cathy     cvs        Cathy                          NaN
3  darweesh    dell     Darweesh                          NaN
---DIVIDER---
      fname company fname_proper fname_1st_row_only_proper_v1 fname_1st_row_only_proper_v2
0     anush   apple        Anush                        Anush                        Anush
1    benita  boiron       Benita                          NaN                          NaN
2     cathy     cvs        Cathy                          NaN                          NaN
3  darweesh    dell     Darweesh                          NaN                          NaN
---DIVIDER--- 
```
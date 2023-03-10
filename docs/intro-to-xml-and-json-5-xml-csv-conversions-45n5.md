# XML 和 JSON 介绍#5: XML/CSV 转换

> 原文：<https://dev.to/katiekodes/intro-to-xml-and-json-5-xml-csv-conversions-45n5>

现在您可以阅读 XML 并手写，让我们来谈谈 XML 和 CSV 之间的转换。

*(提示:CSV 到 XML 总是很容易的。XML 到 CSV 需要你很聪明。)*

* * *

[![](img/15b3518feb8181bb6cc92f483aaee8cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X-Ig0rYM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/booksmany.jpg)

## 本系列中的帖子

1.  概述[![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) [## XML 和 JSON 简介

    ### 凯蒂 3 月 18 日 194 分钟阅读

    # XML# JSON#教程#初学者](/katiekodes/intro-to-xml-and-json-56co)
2.  表格与列表*(本帖)*[![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) [## XML 和 JSON 介绍#2:数据的形状

    T12】凯蒂 3 月 25 日 198 分钟阅读# XML# JSON#教程#初学者](/katiekodes/intro-to-xml-and-json-2-datas-shape-3iji)
3.  XML 1[![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes)[T8】XML 和 JSON #3 简介:XML 条目和键

    ### 凯蒂四月 3 日 197 分钟阅读

    # XML# JSON#教程#初学者](/katiekodes/intro-to-xml-and-json-3-xml-items-keys-1m7i)
4.  XML 2[![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes)[T8】XML 和 JSON #4 简介:XML 值

    ### 凯蒂四月 3 日 197 分钟阅读

    # XML# JSON#教程#初学者](/katiekodes/dying-intro-to-xml-and-json-4-xml-values-5bi1)
5.  XML 3[![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) [## XML 和 JSON 介绍#5: XML/CSV 转换

    ### 凯蒂四月 3 日 194 分钟阅读

    # XML# JSON#教程#初学者](/katiekodes/intro-to-xml-and-json-5-xml-csv-conversions-45n5)
6.  JSON[![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes)[T8】XML 和 JSON 介绍#6: JSON

    ### 凯蒂四月 26 日 1911 分钟阅读

    # XML# JSON#教程#初学者](/katiekodes/intro-to-xml-and-json-6-json-30h3)
7.  重述[![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) [## XML 和 JSON 介绍#7:概述和实际应用

    ### 凯蒂四月 26 日 196 分钟阅读

    # XML# JSON#教程#初学者](/katiekodes/intro-to-xml-and-json-7-recap-and-real-world-use-5586)

* * *

[![](img/ee2baa85a3cf85eb33ec0d688e565808.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aEuiSrMd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/currencies-wide.jpg)

## 将 XML 转换为 CSV

我们看到的一些例子是如此的“单调”和“重复”,以至于它们可以成为很好的 CSV 文件。

比如，还记得我们的车队吗？

```
<RootElement>
 <Car VIN="First car's Vehicle Identification Number here"/>
 <Car VIN="Second car's Vehicle Identification Number here"/>
</RootElement> 
```

1.  每个项目都有与其他项目相同的**键**。
2.  每个**键**只有 1 个**值**。

这将是一个非常好的 1 列 CSV 文件，不是吗？

| 车辆识别号码 |
| --- |
| 第一辆车的车辆识别号在这里 |
| 第二辆车的车辆识别号在这里 |

* * *

[![](img/25ebc5109e7e7fca95fd29afd17e9b6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t5vE8W6u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/financial_ledger-wide.jpg)

### 寻找重复

这个故事的寓意是:

> **每次查看 XML，*寻找重复*。**

这封邮件是写给所有非程序员和业务分析师的。

即使你从来没有学过编程，识别数据的形状可以帮助你评估你是否认为花时间让程序员为你写代码是值得的。

* * *

[![](img/642fa10c88a4ad5906e81ec4b177382c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZcGoO8rn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/tools-wide.jpg)

### 编码

如果你真的想自己编写一个 XML 到 CSV 的转换脚本，让我告诉你一个秘密:

> 将 XML 文件转换成 CSV 文件最难的不是“学习编码”。T3 是**知道谷歌什么**。*   It is **that decides whether it is worth trying.****T11】**

 **识别数据的“形状”是这一过程的重要组成部分。

我使用了大量的 Google 来编写下面的 5 行 Python 程序，该程序将上面的 XML 文件作为输入，并将上面的 CSV 文件作为输出。

```
import xml.etree.ElementTree
import pandas
car_details = [x.attrib for x in xml.etree.ElementTree.parse(r'C:\example\fleet.xml').findall('Car')]
df = pandas.DataFrame.from_dict(car_details)
df.to_csv(r'C:\example\fleet.csv', index=False) 
```

当我在谷歌上搜索“XML 到 CSV Python”时，找到正确搜索结果的困难部分是:

*   概念的💭：
    *   发现“`Car`”项是“平的”&“重复的”足以构成一个好的“循环”
    *   发现“每个`Car`的属性足够重复，可以构成一个好的“循环”
*   技术的💻：
    *   通过阅读文档和谷歌搜索，我了解到 Python 的“ElementTree”插件提供了一种方法，可以列出项目*(“`.attrib`”)*的所有键值属性对，这种方法与“Pandas”包的“`.from_dict()`”命令兼容
    *   理解 Python 的“列表”、“字典”、循环、ElementTree 和 Pandas，直到我得到我想要的输出

重要的一点是，是概念上的工作帮助我认识到这种转变“在我的头脑中很容易做到”

一旦我知道它“在我的脑海中很容易实现”，我就可以为“用代码实现它”制定一个游戏计划

* * *

[![](img/17c07867fe111308b410a0ef99ec038e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--89wVN7J1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/holdingeuros-wide.jpg)

## 将 CSV 转换为 XML

正如我在本系列的第二篇文章中提到的，关于数据的形状，将“表格形状”的数据转换成“列表形状的列表”数据在概念上并不困难。

因此，从 CSV 到 XML 更多的是纯粹的技术任务。

真的没有什么大的概念性问题要问。

**业务分析师**:“我的程序员能做到吗？”永远是“是的！！!"从 CSV 到 XML。

* * *

[![](img/642fa10c88a4ad5906e81ec4b177382c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZcGoO8rn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/tools-wide.jpg)

### 编码

对于那些对代码感兴趣的人来说，这里有一种用 Python 实现的方法，使用一个名为" [lxml](https://lxml.de/tutorial.html) "的插件，我喜欢用它来用*编写* XML，因为它的"`pretty_print`"设置方便了换行符和制表符，便于人们阅读:

```
import lxml.etree
import pandas
xmlroot = lxml.etree.Element('RootElement')
df = pandas.read_csv(r'C:\example\fleet.csv')
for row in df.to_dict(orient='records'):
    current_car = lxml.etree.Element('Car')
    xmlroot.append(current_car)
    for colname, cellvalue in row.items():
        current_car.set(colname, cellvalue)
with open(r'C:\example\fleet2.xml', 'wb') as filepath:
    filepath.write(lxml.etree.tostring(xmlroot, pretty_print=True)) 
```

(我们将不得不改天再解释它是如何工作的。)

* * *

[![](img/9e9a42350b141ae6e94235b07ed69cb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WdD4DMbJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/shoppingbags.jpg)

## 外卖

*   从概念上来说，XML 到 CSV 可能很难编码，原因与本系列第二篇文章[中的“生日礼物清单”](https://dev.to/katiekodes/intro-to-xml-and-json-2-datas-shape-3iji)为*制作的非常难看的电子表格相同。*
    *   XML 到 CSV 通常很难编码，用纸和笔也很难。
    *   为了弄清楚这样的转换是否可行，重要的是在读取 XML 文件*时观察它看起来像什么。*
*   另一方面，CSV 到 XML 在概念上很容易，因为我们的“[簿记员的分类账](https://dev.to/katiekodes/intro-to-xml-and-json-2-datas-shape-3iji/#example-table-data-in-csv-xml-amp-json)”很容易用纸和笔草草记下，作为一个相对平坦、重复的项目符号列表。

一旦你完成了概念性的工作，剩下的问题就可以通过询问你的开发人员或者从技术方面着手编写你自己的代码来解决。

快乐转换！💱**
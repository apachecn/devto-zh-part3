# XML 和 JSON 介绍#2:数据的形状

> 原文：<https://dev.to/katiekodes/intro-to-xml-and-json-2-datas-shape-3iji>

我提到过 XML & JSON 是用于列表的列表，而 CSV 是用于表格的。让我们来探讨一下这个概念。

如果你只看了这个系列的一篇文章，那就写这篇吧！)

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

[![](img/88af6c7361741331eec7148cfc8717e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--knD9zkSC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/bouquet-wide.jpg)

## 查看“漂亮”的 XML & JSON

在这个系列中我们会有很多例子。我建议你编辑它们，并以一种“漂亮”的格式观看它们！ [XML](http://codebeautify.org/xmlviewer) ，[JSON](http://codebeautify.org/jsonviewer)——粘贴&点击“树形视图”。

警告:仅将样本数据放入上面的“美化”链接中。不要将公司的机密数据放入陌生人的网站。

* * *

[![](img/25ebc5109e7e7fca95fd29afd17e9b6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t5vE8W6u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/financial_ledger-wide.jpg)

## 表数据

表格样式数据的关键属性是:

1.  🔑“表格”具有特定数量的列*(“键”)*，这些列对于表格中的每一行*(“项目”)*都是相同的。
    *   也就是说，每一行的**列**与其他行的*、【键】、*、**完全相同。**
2.  😶不仅每个行列交叉点都能够存储一个值…而且如果没有值，还需要指示一个**空白**占位符*。*
**   ☝️一个行列交集只能有一个值。
    *   也就是说，如果只有一个“名字”列，则任何行都不能有两个名字。*   ♭因为一行*【item】*+列*【key】*交集只能有一个值，每一行*【item】*都很“**平**”
    *   没有一种自然的方法可以在多个“级别”上“嵌套”数据*

 *### 举例

这里有一个漂亮的小“簿记员的分类账”，代表“自然表格形状”的数据:

| 采购 ID | 日期 | 小贩 | 税收类别 | 价格 |
| --- | --- | --- | --- | --- |
| 000001 | 01/01/2010 | 苹果 | 办公用品 | $60,000.00 |
| 000002 | 01/01/2010 | 苹果蜂的 | 早(或午、晚)餐 | $50.87 |
| 000003 | 01/02/2010 | 苹果 | 修理 | $928.20 |
| 000004 | 01/10/2010 | 钢制外壳 | 办公用品 | $20,289.98 |

* * *

[![](img/ccdab4d41f49d06aa529be0f235daf04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MjEt17rs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/grocerylist.jpg)

## 可变/嵌套列表数据

随着以下两种情况的发生，数据变得不像“表格”而更像“项目符号列表”:

1.  列表中不同“项目”的“细节”与列表中其他项目的细节完全无关。
2.  你在彼此内部“嵌套”列表。

假设你有一份好友名单，方便给他们买生日礼物(你真好！)

你会想写下每个朋友的一些事实，帮助你记住他们喜欢什么。

影响你给每个朋友的生日礼物的“事实”会不一样，因为每个朋友的生活都不一样。

### 举例

例如，您可以写:

> 1.  Uwe
>     *   BDAY: November 8th
>     *   孩子们:
>         *   约翰尼(4)
>         *   玛蒂尔达(2)
>     *   Collection: Cat Meme
> 2.  但
>     *   BDAY: January 27th
>     *   Gourmet:
>         *   wine
>         *   pickled vegetables

Uwe 和 Hani 有孩子，但是 Dan 和 Ridhi 没有。Uwe 和 Ridhi 收集东西，但是 Dan 和 Hani 不收集。丹是个美食家。哈尼忙于工作，照顾年迈的父母和孩子，她不想要这些东西。她想要自由时间。但是……如果你很忙，你也许可以带一个印有护士可爱标语的咖啡杯。

虽然你们有一些重叠，但你名单上的每个人都有不同于其他人的特点是非常正常的。

下面看看将你的“朋友列表”存储为一个表格需要多少空间以及**有多少单元格最终是空白的**。只是 *[没有意义](https://en.wikipedia.org/wiki/Sparse_matrix#Storing_a_sparse_matrix)* 把你的好友列表存成表格！

你绝不会在纸上这样写:

| 名字 | Bday | 收藏品 | 小孩 1 | 小孩 2 | 食物 1 | 食物 2 | 职位 | 礼物 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Uwe | 11 月 8 日 | 猫迷因 | 约翰尼(4) | 玛蒂尔达(2) |  |  |  |  |
| 但 | 1 月 27 日 |  |  |  | 葡萄酒 | 泡菜 |  |  |
| Ridhi | 9 月 16 日 | 青蛙 |  |  |  |  |  |  |
| 哈尼 | 4 月 9 日 |  | 李(8) |  |  |  | 护士 | 帮手/保姆 |

那么你为什么要在电脑里这样写呢？

“生日事实列表”自然是“列表列表”，而不是“表格”

记住我们的两个关键属性，它们中的任何一个都会影响从“表”结构转移到“列表的列表”结构的决定:

1.  列表中不同“项目”的“细节”与列表中其他项目的细节完全无关。
2.  你在彼此内部“嵌套”列表。

* * *

[![](img/d69bb13b45df9613ae3e60bcf9b8b335.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V78VIA4Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/keys_jumbled-wide.jpg)

## 项，按键&值

从现在开始，我们将把数据称为“项目”、“键”、“值”

### 在一个“表格状”(CSV)文件中

1.  “**项**是完整的行
    *   它们通过**换行符**彼此分开
    *   例如`"000001", "01/01/2010", "Apple", "Office Supplies", "$60,000.00"`
2.  “**键**是列标题
    *   例如“`Purchase ID`”或“`Vendor`”或“【T2”)
    *   它们被逗号分隔开来
3.  “**值**是行和列交叉处的单个单元格中的数据。
    *   例如“`01/02/2010`”或“`Steelcase`”或“【T2”)
    *   它们被逗号分隔开来

### 在一个“列表列表形状的列表”(XML/JSON)文件中

#### 物品

显示“**项**是什么很容易，但很难定义。现在，让我们说它们是代表现实世界**事物**或**想法**的“簇”。

*   例如:
    *   Uwe 的存在
    *   Ridhi 的存在
    *   我们知道 Uwe 的生日
    *   我们知道 Ridhi 喜欢收集什么
    *   Uwe 有孩子的事实
    *   乌韦的孩子约翰尼的存在
    *   我们知道 Uwe 的孩子 Johnny 的年龄
*   在 **XML** 中，一个“项目”被称为一个“**元素**，并通过将其包围在一个“标签集”中来与其他项目区分开来:`<item_name> ... </item_name>`
*   在 **JSON** 中，一个“项目”被称为一个“**对象**，通过用花括号将它括起来来与其他项目区分开来:`{ ... }`

#### 键

随着我们深入细节，XML/JSON 项中的“**键**也更容易显示，但是它们可能是这样的概念:

*   朋友
*   bday
*   名字
*   收藏品
*   食物
*   工作
*   礼物

在我们更深入地研究 XML 和 JSON 之前，我们将讨论用什么标点符号来表示“键”。

#### 值

随着我们深入细节，XML/JSON 项中的"**值**"也更容易显示，但它们可能是这样的概念:

*   Uwe
*   11 月 8 日
*   青蛙
*   four
*   泡菜
*   帮手/保姆

在我们更深入地研究 XML 和 JSON 之前，我们将讨论用什么标点符号来表示“值”。

* * *

[![](img/b892aff1bb983ca733c3d5b59be727ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LPOwEBZi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/example-chalkboard.png)

## 举例:XML vs. JSON

我们不得不**做出选择**当编写 XML 或 JSON *时，到底如何组织我们的数据(就像我不得不决定，在我的例子“礼物列表”中，是将孩子的年龄放在他们名字旁边的括号中，还是将年龄作为子项目符号)*。

但是有一种方法可以用 XML 和 JSON 来表示这个“礼物清单”。

```
<AllFriends>
    <friend name="Uwe" bday="Nov. 8">
        <kid name="Johnny">
            <age>4</age>
        </kid>
        <kid name="Matilda">
            <age>2</age>
        </kid>
        <collection>
            cat memes
        </collection>
    </friend>
    <friend name="Dan" bday="Jan. 27">
        <food>wine</food>
        <food>pickles</food>
    </friend>
    <friend name="Ridhi" bday="Sep. 16">
        <collection>
            frogs
        </collection>
    </friend>
    <friend name="Hani" bday="Apr. 9">
        <Job>nurse</Job>
        <Gift>help / babysitter</Gift>
        <kid name="Lee">
            <age>8</age>
        </kid>
    </friend>
</AllFriends> 
```

```
[  {  "Name"  :  "Uwe",  "Bday"  :  "Nov. 8",  "Kids"  :  [  {  "Name"  :  "Johnny",  "Age"  :  4  },  {  "Name"  :  "Matilda",  "Age"  :  2  }  ],  "Collection"  :  "cat memes"  },  {  "Name"  :  "Dan",  "Bday"  :  "Jan. 27",  "Food"  :  ["wine","pickles"]  },  {  "Name"  :  "Ridhi",  "Bday"  :  "Sep. 16",  "Collection"  :  "frogs"  },  {  "Name"  :  "Hani",  "Bday"  :  "Apr. 9",  "Job"  :  "nurse",  "Gift"  :  "help / babysitter",  "Kids"  :  [  {  "Name"  :  "Lee",  "Age"  :  8  }  ]  }  ] 
```

注意**换行和缩进**只是为了**人**的方便。

无论是 XML 还是 JSON，只要把所有的东西都放在一行中，计算机就可以了。

想要证据吗？

*   将 XML 的两个例子粘贴到[这个漂亮的](http://codebeautify.org/xmlviewer)中，然后点击“树形视图”。
*   在[这个更漂亮的](http://codebeautify.org/jsonviewer)中对 JSON 的两个例子做同样的操作。

他们应该生产出和“人类友好”的同类一样的“树”。

```
<AllFriends><friend name="Uwe" bday="Nov. 8"><kid name="Johnny"><age>4</age></kid><kid name="Matilda"><age>2</age></kid><collection>cat memes</collection></friend><friend name="Dan" bday="Jan. 27"><food>wine</food><food>pickles</food></friend><friend name="Ridhi" bday="Sep. 16"><collection>frogs</collection></friend><friend name="Hani" bday="Apr. 9"><Job>nurse</Job><Gift>help / babysitter</Gift><kid name="Lee"><age>8</age></kid></friend></AllFriends> 
```

```
[{"Name":"Uwe","Bday":"Nov. 8","Kids":[{"Name":"Johnny","Age":4},{"Name":"Matilda","Age":2}],"Collection":"cat memes"},{"Name":"Dan","Bday":"Jan. 27","Food":["wine","pickles"]},{"Name":"Ridhi","Bday":"Sep. 16","Collection":"frogs"},{"Name":"Hani","Bday":"Apr. 9","Job":"nurse","Gift":"help / babysitter","Kids":[{"Name":"Lee","Age":8}]}] 
```

* * *

[![](img/b892aff1bb983ca733c3d5b59be727ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LPOwEBZi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/example-chalkboard.png)

## 示例:CSV、XML、& JSON 中的表格数据

请记住，尽管“列表中的列表”数据不能很好地转换成表格，但是表格数据可以很好地转换成 XML 或 JSON。

还记得我们的账簿吗？让我们看看这三种格式:

CSV:

```
"Purchase ID","Date","Vendor","Tax Category","Price"
"000001","01/01/2010","Apple","Office Supplies","$60,000.00"
"000002","01/01/2010","Applebee's","Meals","$50.87"
"000003","01/02/2010","Apple","Repairs","$928.20"
"000004","01/10/2010","Steelcase","Office Supplies","$20,289.98" 
```

XML:

```
<Ledger>
    <Entry><Purchase_ID>000001</Purchase_ID><Date>01/01/2010</Date><Vendor>Apple</Vendor><Tax_Category>Office Supplies</Tax_Category><Price>$60,000.00</Price></Entry>
    <Entry><Purchase_ID>000002</Purchase_ID><Date>01/01/2010</Date><Vendor>Applebee's</Vendor><Tax_Category>Meals</Tax_Category><Price>$50.87</Price></Entry>
    <Entry><Purchase_ID>000003</Purchase_ID><Date>01/02/2010</Date><Vendor>Apple</Vendor><Tax_Category>Repairs</Tax_Category><Price>$928.20</Price></Entry>
    <Entry><Purchase_ID>000004</Purchase_ID><Date>01/10/2010</Date><Vendor>Steelcase</Vendor><Tax_Category>Office Supplies</Tax_Category><Price>$20,289.98</Price></Entry>
</Ledger> 
```

JSON:t0 版

```
[  {"Purchase ID":"000001",  "Date":"01/01/2010",  "Vendor":"Apple",  "Tax Category":"Office Supplies",  "Price":"$60,000.00"},  {"Purchase ID":"000002",  "Date":"01/01/2010",  "Vendor":"Applebee's",  "Tax Category":"Meals",  "Price":"$50.87"},  {"Purchase ID":"000003",  "Date":"01/02/2010",  "Vendor":"Apple",  "Tax Category":"Repairs",  "Price":"$928.20"},  {"Purchase ID":"000004",  "Date":"01/10/2010",  "Vendor":"Steelcase",  "Tax Category":"Office Supplies",  "Price":"$20,289.98"}  ] 
```

1.  你最喜欢什么？
2.  为什么？
3.  你能看出 XML 和 JSON 有多重复吗？
    *   这始终是一个强有力的暗示，数据 T2 可以很容易地转换回 CSV 或 Excel 表格。

* * *

[![](img/9e9a42350b141ae6e94235b07ed69cb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WdD4DMbJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/shoppingbags.jpg)

## 外卖

1.  只是文字而已！
    *   XML、JSON 和 CSV 都是在纯文本中输入数据的标准化方式，赋予数据结构和含义。
2.  XML ≈ JSON
    *   XML 和 JSON 应该表示彼此相同的数据。
    *   他们标点符号的不同导致了微妙的优势和劣势，但他们更多的是相似而不是不同。
3.  CSV =表格
    *   理想情况下，您会使用 **CSV** 来表示**“极其相似”的“表格状”数据**，就像上面的“**记账员的总账**的例子。
4.  XML/JSON =列表
    *   理想情况下，您可以使用 **XML** 或 **JSON** 来表示**一个“列表列表”和/或“极其多样”的数据**，就像上面的“**生日跟踪器**的例子一样。
    *   必要时，比如当您以不喜欢“换行”的方式传输数据时，您也可以使用 XML 或 JSON 来表示数据，就像“簿记员的分类账”
5.  👀 = ↔
    *   如果你学会了区分这两种形式的数据，你就可以使用工具在 CSV/Excel 和 XML/JSON 之间进行转换。*
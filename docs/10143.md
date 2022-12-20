# XML 和 JSON 简介

> 原文：<https://dev.to/katiekodes/intro-to-xml-and-json-56co>

XML、JSON 和 CSV。

所有 3 种都是*令人难以置信的*普通的“纯文本”**文件格式，用于在计算机之间传输数据**，尤其是通过互联网*(例如通过“[API](https://dev.to/katiekodes/salesforce-rest-apis-a-high-level-primer-2i9h)”*)。但是有什么区别，什么时候应该使用每一个？

(程序员、Salesforce 业务分析师和数据库集成架构师:你会想知道这些的！)

[![](img/2f46bf112f350ef9a188d1219f4a8514.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ALyKjjcl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/grocery_vs_ledger.png)

## XML & JSON 是列表的列表。CSV 是用于表的。

这是一个痛苦的过度简化，但我认为如果你对这些概念完全陌生，这是一个好的开始。

* * *

## 核心原则

让我们介绍一些关于 XML 和 JSON 文件格式的关键原则。

### 原则 1: XML ≈ JSON

XML 和 JSON 在很大程度上可以互换。

### 原则二:XML/JSON > CSV

XML 和 JSON 能够容纳比 CSV 文件更复杂的数据。

XML & JSON 文件能够存储 CSV 文件能够存储的任何“表格状”数据*(尽管通常不如 CSV 文件简洁)*。

### 原则三:CSV < XML/JSON

相反，CSV 文件是一种更严格、更简单的文件格式。

CSV 文件不能轻松存储所有适合 XML/JSON 的数据。

**问:**为什么不呢？

**答:**

1.  CSV 文件在存储数据方面很糟糕，数据的每个“项目”都可能与数据集中的任何其他“项目”有不同的“特征”。
2.  CSV 文件不擅长在彼此内部“嵌套”项目。

### 原则 4:换行符≠互联网友好

CSV 文件在某些情况下*(例如“HTTP 请求”)*不能很好地在互联网上传输，因为它们在数据格式规范中包含了 ***换行符*** 。

换行并不总是能很好地在计算机之间传输。

*   如前所述，XML 和 JSON 对于存储表格形式的数据都不是特别简洁。 *(JSON 有点“不太坏”。)*

> 如果您需要看到一些东西才能相信它，请查看 Salesforce 在通过互联网传输表格数据时使用 JSON 而不是 CSV 的输出，如下所示:
> 
> *   使用 Salesforce dev org 登录[sales force Developer work bench 的 REST Explorer](https://workbench.developerforce.com/restExplorer.php) ，该组织在“Account”表中有数据。
> *   将 HTTP 方法保留为“GET”
> *   在方框中填入:

```
/services/data/v44.0/query/?q=SELECT+Name+FROM+Account+LIMIT+5 
```

> *   点击“执行”
> *   点按“显示原始响应”
> 
> 屏幕右侧蓝框中的数据是用“JSON”语法格式化的！

* * *

## 材料

学习编写 CSV 文件很容易——参见他们的[维基百科文章](https://en.wikipedia.org/wiki/Comma-separated_values)。

因此，本系列将重点教你在**概念**层面上读写 **XML** 和 **JSON** 文件。

您将了解到:

1.  XML 和 JSON 标准之间的细微差异影响程序员偏好的方式。
    *   *(尽管他们经常选择一个标准，因为“那是我同事喜欢的”——这也是[一个不坏的理由](https://en.wikipedia.org/wiki/Coding_conventions)！)*
2.  识别什么时候格式化为 XML 或 JSON 的数据只是作为“表格状”CSV 格式数据的“互联网友好”包装。
    *   在这些情况下，有工具可以帮助你将数据转换成电子表格*(例如 Python 编程语言的“熊猫”模块)*
3.  XML 和 JSON 格式的结构细节，这样编辑它们就不会感到害怕。

我建议阅读整个系列，因为我从 XML 开始，然后在解释 JSON 时采取某种“比较和对比”的方法，以避免重复。

* * *

## 讲究“形！”

但在我们开始之前，请务必在阅读时记住这一点:

1.  如果你的数据自然地适合一个 **Excel 电子表格**，它可能是“**表格形式**， **CSV** 可能是它最自然的格式。
    *   不过，如果需要避免换行，可以使用 XML 或 JSON *(JSON 会更简洁)*。
2.  如果你的数据很自然地符合“购物清单”或“**嵌套项目符号**的格式，那么它可能是**“清单列表”形状的**和 **XML/JSON** 可能是它最自然的格式。

* * *

[![](img/88af6c7361741331eec7148cfc8717e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--knD9zkSC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/bouquet-wide.jpg)

## 查看“漂亮”的 XML & JSON

在这个系列中我们会有很多例子。

我建议你编辑它们，并以一种“漂亮”的格式观看它们！

*   要以图形方式查看我的 XML 示例，请将它们粘贴到[这里](http://codebeautify.org/xmlviewer)并单击“树形视图”。
*   要以图形方式查看我的 JSON 示例，请将它们粘贴到[这里](http://codebeautify.org/jsonviewer)并单击“树形视图”。

警告:仅将样本数据放入上面的“美化”链接中。不要将公司的机密数据放入陌生人的网站。

* * *

[![](img/15b3518feb8181bb6cc92f483aaee8cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X-Ig0rYM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/booksmany.jpg)

## 本系列中的帖子

1.  概述[![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) [## XML 和 JSON 简介

    ### 凯蒂 3 月 18 日 194 分钟阅读

    # XML# JSON#教程#初学者](/katiekodes/intro-to-xml-and-json-56co)
2.  表格对比列表*(本帖)*
     [![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) 
     [## XML 和 JSON 介绍#2:数据的形状

    ### 凯蒂 3 月 25 日 198 分钟阅读

    【XML】
    【JSON】
    【教程】新手](/katiekodes/intro-to-xml-and-json-2-datas-shape-3iji) 

3.  XML 1
     [![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) 
     [## XML 和 JSON #3 简介:XML 条目和键

    ### 凯蒂四月 3 日 197 分钟阅读

    【XML】
    【JSON】
    【教程】新手](/katiekodes/intro-to-xml-and-json-3-xml-items-keys-1m7i) 

4.  XML 2
     [![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) 
     [## 和 JSON #4 简介:XML 值

    ### 凯蒂四月 3 日 197 分钟阅读

    【XML】
    【JSON】
    【教程】新手](/katiekodes/dying-intro-to-xml-and-json-4-xml-values-5bi1) 

5.  XML 3
     [![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) 
     [## XML 和 JSON 介绍#5: XML/CSV 转换

    ### 凯蒂四月 3 日 194 分钟阅读

    【XML】
    【JSON】
    【教程】新手](/katiekodes/intro-to-xml-and-json-5-xml-csv-conversions-45n5) 

6.  JSON
     [![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) 
     [T16】XML 和 JSON 介绍#6: JSON

    ### 凯蒂四月 26 日 1911 分钟阅读

    【XML】
    【JSON】
    【教程】新手](/katiekodes/intro-to-xml-and-json-6-json-30h3) 

7.  重述
     [![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) 
     [## XML 和 JSON 介绍#7:概述和实际应用

    ### 凯蒂四月 26 日 196 分钟阅读

    【XML】
    【JSON】
    【教程】新手](/katiekodes/intro-to-xml-and-json-7-recap-and-real-world-use-5586)
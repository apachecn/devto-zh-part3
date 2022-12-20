# XML 和 JSON 介绍#7:概述和实际应用

> 原文：<https://dev.to/katiekodes/intro-to-xml-and-json-7-recap-and-real-world-use-5586>

这是一个总结！让我们回顾一下“XML vs. JSON”之战，讨论它们在现实世界中是如何使用的，最后看一眼大图。

毕竟，你可能仍然会问这样的问题:

*   哪个更好？
*   我什么时候会真正使用 XML 或者 JSON？

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

[![](img/b07dd1cf567ebb5e4e61ad9b0350ddb2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KTxlv2zQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/catfight.jpg)

## XML vs. JSON

每种格式的优势源于它们独特的特性。

*   XML 的特殊才能
    *   给项目命名
    *   评论
    *   项目 id
*   JSON 的特殊才能
    *   不给项目命名
    *   列表

* * *

[![](img/c3e515df0016a118afa131c8d3759cb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wHyAflYM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/catfight-lowercat.jpg)

### XML 的特殊才能

#### 赋予物品名称

在具有大量内部可变性的数据中，给项目命名可以使 XML 容易被人看到。

例如，下面是我的 sales force“Contacts”表的定义后面的 XML 中的前两级嵌套项的外观*(只有 7000 行)* :

```
<CustomObject>
    <actionOverrides>...(nested stuff)...</actionOverrides>
    <actionOverrides>...(nested stuff)...</actionOverrides>
    <actionOverrides>...(nested stuff)...</actionOverrides>
    <actionOverrides>...(nested stuff)...</actionOverrides>
    <compactLayoutAssignment>SYSTEM</compactLayoutAssignment>
    <enableFeeds>true</enableFeeds>
    <enableHistory>true</enableHistory>
    <fieldSets>...(nested stuff)...</fieldSets>
    <fieldSets>...(nested stuff)...</fieldSets>
    <fields>...(nested stuff)...</fields>
    <fields>...(nested stuff)...</fields>
    <fields>...(nested stuff)...</fields>
    <fields>...(nested stuff)...</fields>
    <fields>...(nested stuff)...</fields>
    <listViews>...(nested stuff)...</listViews>
    <searchLayouts>...(nested stuff)...</searchLayouts>
    <sharingModel>ReadWrite</sharingModel>
    <webLinks>...(nested stuff)...</webLinks>
</CustomObject> 
```

特别是当给定级别的项目按字母顺序排序时，这些数据有一些天生可以理解的东西。

我*喜欢*的方式 ***英文单词*** 主导了结构，当我使用 Notepad++之类的软件“折叠”XML 并一次只查看一层时，就埋没了细节。

我怀疑，在这个由`{}`和`[]`组成的海洋中，确定结构的哪一部分控制这个表上的“自定义字段”会这么快，在这个海洋中，项目“名称”只是与项目数据的其余部分一起埋藏的附加键值对。

这里，很明显，要向 Salesforce 添加一个新字段，我需要向表示我的表的 XML 添加一个新的`<fields>...</fields>`东西。

#### 注释

我忘记介绍了，但是 XML 允许使用注释。

这个 XML:

```
<Dan Bday="Jan. 27">
    <!-- 
        This is a multi-line XML comment.
        The computer won't read any of this.
    -->
    <food>wine</food>
    <!--
        Nifty: I can "comment out" pickles 
        so that the computer won't think that Dan likes pickles, 
        but it will still be in this document 
        in case I change my mind later.
        See next line.
    -->
    <!--<food>pickles</food>-->
</Dan> 
```

就计算机而言，相当于这个 XML:

```
<Dan Bday="Jan. 27">
    <food>wine</food>
</Dan> 
```

当然，如果 Dan 不再喜欢 pickles，在 XML 中留下“pickles”是很麻烦的。

现在你的文本文件中有了人眼会看到的东西，比如“pickles”和“nifty”，但它的大脑必须记住忽略这些东西。

即使要使用注释，也要谨慎小心。

#### 物品编号

我忘记介绍它了，但是当您使用第二种*【属性】*方法在 XML 中表示它们时，任何您称为“`ID`”*(不区分大小写)*的东西都必须满足一个特殊的规则，您的 XML 才有效:

您不能在整个 XML 文件的任何地方使用相同的值两次。

这对于编写可以“跳转”到 XML 文件中某项的代码来说极其重要。

例如，控制这篇博客文章的 HTML 包含以下元素:

```
<h2 id="posts-in-this-series">
    Posts In This Series
</h2> 
```

如果你在这篇博文的 URL 后面添加了“`#posts-in-this-series`”，你的网络浏览器就会直接跳转到这篇文章后面的 HTML 代码段。

这就是我在解释 JSON on objects 时，如何将您链接到 Marijn Haverbeke 关于 octopi 的引用的精确段落。

https://eloquentjavascript.net/2nd_edition/04_data.html 是一个非常大的网页。

不过 Marijn 还是很有前瞻性的*(谢谢！)*并在每段的开头嵌入一个名为“`a`”的元素，并给它一个惟一的 ID，如下:

```
<p>
    <a class="p_ident" id="p_FolCMJfte3" href="#p_FolCMJfte3"></a>
    To briefly return to our tentacle model of variable bindings—property bindings are similar.
    They <em>grasp</em> values, but other variables and properties might be holding onto those same values.
    You may think of objects as octopuses with any number of tentacles, each of which has a name inscribed on it.
</p> 
```

因此，要提供他的书的各个段落的链接，我所要做的就是用我的网络浏览器检查 […/04_data.html](https://eloquentjavascript.net/2nd_edition/04_data.html) 的源代码，找到附在我感兴趣的段落上的`p_FolCMJfte3`，链接到[…/04 _ data . html # p _ folcmjfte 3](https://eloquentjavascript.net/2nd_edition/04_data.html#p_FolCMJfte3)。

* * *

[![](img/da614324bb57fe3f84ae90807b896ba8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xWKReO5l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/catfight-uppercat.jpg)

### JSON 的特殊才能

#### 不给物品命名

当我[向 Salesforce Pardot 的 API 请求 JSON 表示其“前景”表](https://katiekodes.com/python-pardot-null-clean/)中的数据时，Pardot 发给我的文本看起来像这样:

```
[  {  "id":  01010101,  "address_one":  "123 Sunny St"  },  {  "id":  02020202,  "address_one":  "null"  }  ] 
```

我**已经知道**整个文件都是来自“prospect”表的记录，所以我*喜欢*我的眼睛和大脑可以锁定代表**数据**的**英文单词**，而不是“Prospect”这个单词的一堆重复

#### 列表

类似于“不给项目命名”的好处，JSON 的列表可以减少冗余，非常简洁地存储某些类型的数据。

当存储简单值列表*(例如，明文、数字)*时尤其如此。

```
[  "wine",  "pickles",  "cream cheese",  "herring",  "tabbouli"  ] 
```

然而，JSON 的列表格式并不总是比 XML 节省空间。在 XML 或 JSON 中列出背对背的项目可能会更短，这取决于项目本身在每个标准中的长度。

* * *

[![](img/73126b675db74145f1fac6ffd7bc163c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u5meXJ2G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/frenchtaunter-wide.jpg)

## 真实世界用法:API

实话实说:**你很少能选择如何编写 JSON 或 XML** 。

通常，你试图从某种云产品的" [API](https://dev.to/katiekodes/salesforce-rest-apis-a-high-level-primer-2i9h) "下载和处理数据，或者上传数据到其中。

在这种情况下，对你来说最重要的是能够**识别“API”已经给了你哪种类型的数据，或者期望从你那里得到哪种类型的数据。**

毕竟，你不可能打电话和一家公司争论他们应该选择哪种格式！

提示:

*   "**REST**API 通常希望以 **JSON** 的形式从您那里接收数据。
*   "**SOAP**API 通常希望以 **XML** 的形式从您那里接收数据。
*   他们**给**你的是哪种类型的数据，这是一个更大的变数。

在我的许多关于 API 的[帖子中，我谈到了编写 Python 代码或其他能够以这种方式与云数据库对话的代码。](https://katiekodes.com/tags#api)

* * *

[![](img/9e9a42350b141ae6e94235b07ed69cb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WdD4DMbJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/shoppingbags.jpg)

## 外卖

1.  XML 和 JSON 是用于以纯文本形式存储数据的标准，它们非常相似，并且为存储“自然列表形式”的数据而优化。
    *   然而，当希望避免依赖换行符来分隔数据时，它们有时被用来存储“自然表格形状”的数据。
2.  CSV 是一个用于以纯文本形式存储数据的标准,它是为存储“自然表格形式”的数据而优化的。
3.  XML 与 JSON: XML 非常适合需要名称、注释的项目，并为每个项目提供一个惟一的 ID。
4.  XML vs. JSON: JSON 非常适合不需要名称和值就可以列出的项目。
5.  XML 与 JSON:哪个对哪个好并不重要。你通常会将这两者与其他人的软件一起使用，并不得不接受他们的选择。

感谢您坚持阅读整个教程。希望你已经学会了:

1.  如何识别“自然表格形式”和“自然列表形式”的数据，以及两者之间的差异如何影响 CSV 和 XML/JSON 格式之间的数据转换。
2.  帮助您轻松读写 XML 和 JSON 文件的细节。

一如既往，我很高兴听到你的成功，我相信你会有很多成功。

如果你用 XML 或 JSON 做了什么了不起的项目，一定要让我知道。
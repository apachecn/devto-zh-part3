# XML 和 JSON #3 简介:XML 条目和键

> 原文：<https://dev.to/katiekodes/intro-to-xml-and-json-3-xml-items-keys-1m7i>

我们已经从" [30，000 英尺的高度](https://dev.to/katiekodes/intro-to-xml-and-json-56co)看到了 XML

我们知道 XML 可以帮助我们处理什么样的数据。

让我们来学习如何编写 XML！

这变得有点详细，所以我把它分成在同一天发表的 3 篇文章，给你一个喝咖啡的休息时间。

这是关于 XML 的三篇文章中的第一篇，我们将讨论三个主题:

1.  用来表示“项目”的标点符号
2.  用于将项目“嵌套”在彼此内部的标点符号
3.  XML 为您提供了两个标点符号选项，可以将“键-值对”添加到项目中

如果你需要复习一下我所说的条目、键和值，请点击这里。)

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

[![](img/86ec17afeca95b5629eeb5bffbf2c03d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BCAqSWTu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/c/c0/XML_icon.svg/500px-XML_icon.svg.png)

## XML 中的条目:“元素”/“标记集”

在上一篇文章中，我们讨论了具有“条目”、“键”和“值”的数据。

在 XML 中,“项目”被称为“元素”

XML 用来定义“元素”开始和结束的标点符号是“标记集”。

一个“标签集”看起来像这样:

```
<Person></Person> 
```

**格式为:**

1.  一个词重复两次。
2.  该单词的每个出现处都被小于号和大于号包围，形成一个“标签”
3.  因此，它们一起被称为“标签集”
4.  第二个“标记”与第一个不同，它的小于号后有一个正斜杠*(“`/`”)*。
5.  第一个“标签”表示元素的开始。
6.  第二个“标签”表示元素的结束。

**注意:**标签集*存在于你的文本文件*中的事实意味着它*在你的数据*中作为一个概念上的**项**存在。

### 空元素

在标签*(第一个大于号之后，最后一个小于号之前)*之间是否输入了任何内容都无关紧要。

“元素”仍然是“存在于”数据中的一个概念项目，这仅仅是因为您创建了一个“标记集”来表示它。

如果标签之间没有任何东西，你可以把它想象成 CSV 文件中除了逗号什么都没有的一行。

它还在那里！

只是一片空白。

事实上，甚至有一个输入“空”标签集的奇特快捷方式。

这个*单个*标签相当于上面那个中间什么也没有的标签集。

注意**的大于号** :
前的正斜杠

```
<Person/> 
```

* * *

[![](img/7bd00426b534af071ba9be429af478ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B1oYdL5F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/nestedratsinbread-wide.jpg)

## 嵌套元素

我将我们的“空白”**元素** *(或“**项目**”*比作一个空的 CSV 文件**行**，但是它与一个空的 CSV 文件行有一个非常大的区别:它有一个名称！

**元素有名字**是 XML 的一个**关键方面。**

事实上，每个元素都有一个名称，这也使得 XML 有两种方法来表示元素的“键+值”，而 JSON 只有一种方法。

这可以给 XML 带来灵活性，同时也让它变得令人困惑。

更糟糕的是，出于一致的原因，人们并不总是在这两种方法之间进行选择。

1.  有时，他们选择的方式有助于他们准确地**表示他们的数据**。我们会在接下来的文章中多次谈到这一点。
2.  其他时候，他们似乎只是选择“方法#1”，因为它占用了垂直方向的空间，而避免选择“方法#2”，因为当您为了可读性而在 XML 中包含换行符和空白时，它占用了水平方向的空间。
    *   当阅读大数据文件时，人们通常不喜欢双向滚动。🤷🏿

这里有一位 W3Schools 老师关于最佳实践的观点。

知道我们将要学习的一切可能会被其他人完全忽略，让我们继续深入下去，详细了解 XML 定义元素“键”和“值”的两种方法。

* * *

[![](img/4891632701b06c315e6354cd3a31db19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yZxR12SL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/numbercircle-01.png)

### 【关键点】&他们的“价值观”——方法 1(“更多标签集”)

第一种方法是将一个元素嵌套在一个元素中。

因此，内部元素的名字不仅仅是它的名字。

由于被嵌套，内部元素的名字是*也是*，作为一个**键**，指示**外部元素**的一些**方面**。

下面是 XML 中“嵌套”元素的一个例子:

```
<Shirt>
 <Color></Color>
 <Fabric></Fabric>
</Shirt> 
```

在这个数据中有 3 个概念性的“项目”或“元素”，每个都有一个名称。

即使两个元素嵌套在第三个元素中，**在 XML** 的语法中，这三个元素都可以作为“元素”独立存在。

*   **问:**不相信我？
*   **A:** 英语语法也可以这么做！看:

> 你可以写一个包含多个完整句子的英语句子；要写一个里面有多个完整句子的句子，只需用分号将两者分开。

名为“color”和“fabric”的元素嵌套在名为“shirt”的元素的标签之间，这意味着它们也是*和*，表明这件特定的衬衫具有名为“color”和“fabric”的关键字*(这两个关键字的值目前都为空)*。

* * *

[![](img/484820006139a5cdb1bd58bb38a5dd13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_uwLcpFo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/numbercircle-02.png)

### 【键】&它们的“值”——方法二(“属性”)

第二种方法是将键定义为“元素”*(“项目”)*的“属性”。

*   “属性”是在标记集的开始标记中键入的，在元素名称之后，通过空格与元素或其他属性分开。
*   一个“属性”由一个**键**和一个**值**组成，用等号(`=`)隔开。
    *   **键**位于`=`的左边，不需要任何引号。
    *   在单引号或双引号中，**值**位于`=`的右边。

这里有一些关于从微软编写 XML 元素属性的建议。

这一次使用方法 2，让我们再次指出我们有一件带有"**颜色**和"**面料**的**衬衫**，但是"颜色"&"面料"的值是**空白**。

```
<Shirt Color="" Fabric=""></Shirt> 
```

或者，用快捷方式表示，因为现在“衬衫”标记集内什么也没有:

```
<Shirt Color="" Fabric=""/> 
```

关于第二种方法，需要记住的主要事情是“颜色”和“面料”是“T2”，而不是独立的元素。

它们是名为“Shirt”的元素的属性。

[![](img/9d8314bafb8f157c11914959b7f19970.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WciBrTYz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/russiandolls-wide.jpg)

#### “属性”:不允许进一步嵌套

你**不能**在一个“属性”的“=”后面的引号之间放置更多独立的“元素”你完了。只有纯文本值可以放在那里。

你**不能**这样:

```
<Shirt Color="" Fabric="<Washable></Washable>"></Shirt> 
```

然而，如果您使用方法#1 来嵌套，您可以这样做:

```
<Shirt>
 <Color></Color>
 <Fabric>
  <Washable></Washable>
 </Fabric>
</Shirt> 
```

[![](img/740a4a277047263b3dba296efe40797b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---DRVKQ_y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/twinbabies.jpg)

#### 【属性】:不允许重复键

你也不能给任何元素一个以上相同名称的属性。

你**不能**这样:

```
<Shirt Color="" Color="" Fabric=""></Shirt> 
```

然而，如果您使用方法#1 来嵌套，您可以这样做:

```
<Shirt>
 <Color></Color>
 <Color></Color>
 <Fabric></Fabric>
</Shirt> 
```

* * *

[![](img/01a252e05da6a3f91379f8ad3e99767a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OD0VzJg4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/nightstars-wide.jpg)

## 谨防“真空”对“0 字文字”

请注意，方法#1 中的“颜色”和“面料”实际上是 ***空*** ，而方法#2 中的“空白文本” *( `""` )* 。

如果您使用计算机来处理现有的 XML 文件，这些概念可能会有不同的解释。

有人可能会说:

1.  在嵌套标签示例*(它确实没有颜色)*中有更多的“虚无”，而:
2.  在属性示例中，更多的是“没有任何字母的值”。

如果您正在用一个工具处理 XML 格式的数据来进行这样的区分，您可能必须明确地指示您的工具寻找**【真正空白】 ***或**【空文本】，只要您认为它们应该被视为彼此等价，作为一条业务规则。***

 *** * *

[![](img/80be862feee348a659b5ee36d721e357.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TlcPXgxu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/question-confused.jpg)

## 不要混淆风格

关于混合这两种方法的警告——这是*有效的* XML，但是很难理解。

```
<Shirt Color="" Fabric="">
 <Color></Color>
 <Color></Color>
 <Fabric>
  <Washable></Washable>
 </Fabric>
</Shirt> 
```

人类可能会看着上面的衬衫，认为它有:

1.  3 种颜色
2.  2 面料

然而，试图读取 XML 的计算机会与人类意见相左。

所以请不要像上面的例子那样写 XML，因为它会让人困惑。

如果您不幸需要阅读其他人以这种方式编写的 XML，那么养成以计算机认为的方式阅读它的习惯是一个好主意。

换句话说，把这件衬衫想象成:

1.  1 种颜色属性
2.  1 结构属性
3.  嵌套在其中的两个完整元素，每个都命名为“颜色”
4.  1 个名为“fabric”的完整元素嵌套在其中

这样，如果您需要编写或理解帮助读取 XML 文件的代码，您将能够更有效地设计或遵循代码的逻辑。

* * *

[![](img/9e9a42350b141ae6e94235b07ed69cb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WdD4DMbJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/shoppingbags.jpg)

## 外卖

通过给项目命名并把它们放在称为“标记集”的标点符号内来定义项目

一旦定义了一个项目，它就被称为“元素”

要为给定的“元素”定义键值对，XML 允许您选择两种方法之一:

1.  将元素嵌套在元素内部。内部元素的名称充当其父元素的“**键**，内部元素的内容充当该键的“**值**”。
    *   你的数据看起来越像俄罗斯娃娃或兄弟姐妹，你就越需要这种方法。
2.  给元素的开始标记“属性”
    *   当您的数据允许您使用它时，它是简洁的，但是这种方法不允许“嵌套”或“重复键”。

对于任何给定类型的键*(比如衬衫的“颜色”】*，最好从两种方法中选择一种，并在您编写的整个 XML 中坚持使用它。这将有助于避免混淆人们。**
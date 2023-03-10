# XML 和 JSON #4 简介:XML 值

> 原文：<https://dev.to/katiekodes/dying-intro-to-xml-and-json-4-xml-values-5bi1>

在本系列的前一篇文章中，我们使用 XML 定义了一个“衬衫”项目*(或者可以用 XML 中的“元素”)*，带有一些关键字，如“颜色”、“面料”和“可洗”

让我们学习如何为这些键填充一些值。

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
4.  XML 2[![katiekodes image](img/91ff852c8568473209156ff5bd632209.png)](/katiekodes) [## XML 和 JSON #4 简介:XML 值

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

[![](img/2253653dc22f2ee8f3ba2ca0b054f743.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iCSGEHyR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/writingintensely-wide.jpg)

## 为“键”填写“值”

既然我们已经学会了如何使用 XML 来定义一个“衬衫”项目*(或者，正如人们在 XML 中所说的，“元素”)*，其中包含一些关键字，如“颜色”、“织物”和“可洗”，我们可能希望填充一些值。

警告:我真的不应该说“填入一些值”，好像我们的键没有任何值。

从技术上讲，上一篇文章示例中的所有键都已经有了值。

1.  它们只是*空白*值。
2.  或者键值是某个其他的项/元素。
    *   (想想这样的例子，名为“可洗”的元素嵌套在名为“织物”的元素中，而后者又嵌套在名为“衬衫”的元素中从计算机的角度来看,“shirt”元素的“fabric”键的值并不真的为空。“fabric”关键字的值是“一个称为‘可洗’的元素。””

[![](img/c1849403f0615ab1b2361ebde039ce00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DXWmN9ys--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/coloringbookpencils-wide.jpg)

然而，当我说“颜色”、“面料”或“可洗”等关键词的“价值”时，**你可能会想到**纯文本**短语，如“蓝色”/“红色”/“皮革”/“是”/“否”**

所以我们来谈谈那些。

我们为“键”填充纯文本值的方式将取决于我们使用 XML 定义“键-值对”的两种方法中的哪一种。

* * *

[![](img/4891632701b06c315e6354cd3a31db19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yZxR12SL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/numbercircle-01.png)

### 【关键点】&他们的“价值观”——方法 1(“更多标签集”)

在本系列的前一篇文章中，我们看到每个元素的 *(item's)* 名称都是元素“父元素”的“键”,可以有相应的值。

要设置这个值，我们可以在两个标签之间键入 **1 段纯文本** *(比如“皮革”或“蓝色”)* **，这两个标签表示我们使用其名称作为“键”的元素的边界**

除了它的*(可选)*纯文本“值”之外，**唯一可以进入元素“内部”的其他东西**是 ***更多元素*** 。

这里有一个非常简单的元素，它有一个纯文本值:

```
<Shape>
 Rectangle
</Shape> 
```

这里有一个只有一个纯文本值，但是也有一些嵌套元素:

```
<Shape>
 Rectangle
 <Side>1</Side>
 <Side>2</Side>
 <Side>3</Side>
 <Side>4</Side>
</Shape> 
```

* * *

[![](img/4f7b2c454b5474fe8a0101bda43030b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BiBXJcna--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/teacup-wide.jpg)

#### 礼仪

为了便于阅读，请将“纯文本值”放在任何“嵌套元素”之前。

下面的 XML 相当于前面的例子，但是对任何试图阅读你的 XML 的人来说都是非常粗鲁的，因为它将单词“rectangle”隐藏在标点符号的海洋中:

```
<Shape>
 <Side>1</Side>
 <Side>2</Side>
 Rectangle
 <Side>3</Side>
 <Side>4</Side>
</Shape> 
```

下面是更多计算机可以轻松阅读的 XML。

(它可能会被计算机解释为明文**“最佳 EverRectangle** ”，可能在“Ever”和“Rectangle”之间有一个换行符)

然而，它对人类的无礼令人难以置信:

```
<Shape>
 <Side>1</Side>
 <Side>2</Side>
 Best Ever
 <Side>3</Side>
 Rectangle
 <Side>4</Side>
</Shape> 
```

* * *

[![](img/bbdfaec4cb25c6c81ee4b402951bddda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v7ERAATn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/carsparked.jpg)

#### 省略明文值

在 XML 中，跳过为元素定义明文值是很常见的。

很多时候，根本就没有人可以输入有用的东西。

例如，**有效的 XML 总是只有一个最外面的元素**。

因此，在没有自然的单一最外层元素的情况下编写数据的人通常只是将所有内容放在一个任意的标记集中，并给它起一个名字，如“`root`”或“`RootElement`”

真的有什么东西可以有意义地输入来给单词“`RootElement`”赋值，从而简明地描述整个 XML 数据集吗？

大概不会。

元素没有明文值的另一个原因是，元素表示真实世界的对象，其“本质”很难用明文值来定义。

例如，你*可以*描述像这样的车队

```
<RootElement>
 <Car>
  First car's Vehicle Identification Number here
 </Car>
 <Car>
  Second car's Vehicle Identification Number here
 </Car>
</RootElement> 
```

但是让我们从哲学的角度来思考这些车。

上面的代码暗示了你给“汽车”命名的概念“项目”实际上是它们的车辆识别号。

*   **问:**一辆车真的是它的 VIN 吗？
*   答:我不这么认为。我认为汽车是现实世界中占据空间的一块沉重的钢铁。

看看为什么没有一个真正的纯文本值来描述汽车**？**

 **类似地...如果我们有 XML 描述一个装满衬衫的柜子...我们真的能写出一件东西来总结每件“衬衫”真正的本质吗*？*

 *也许我们应该把它留在“`shirt`”，只为“`color`”:
定义明文值

```
<Closet>
 <Shirt>
  <Color>
   Blue
  </Color>
 </Shirt>
 <Shirt>
  <Color>
   Red
  </Color>
 </Shirt>
</Closet> 
```

同样，这里有一种更好的方法来为我们的车队编写 XML:

```
<RootElement>
 <Car>
  <VIN>
   First car's Vehicle Identification Number here
  </VIN>
 </Car>
 <Car>
  <VIN>
   Second car's Vehicle Identification Number here
  </VIN>
 </Car>
</RootElement> 
```

* * *

[![](img/484820006139a5cdb1bd58bb38a5dd13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_uwLcpFo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/numbercircle-02.png)

### 【键】&它们的“值”——方法二(“属性”)

在本系列的前一篇文章中，我们看到元素*(项目)*的附加“键”可以放在定义该元素的开始“标签”中。

要设置该值，我们可以在键名后面的`=` 右边的引号之间键入 **1 段纯文本** *(如“皮革”或“蓝色”)* **。**

例如:

```
<Shirt Color="Blue">
</Shirt> 
```

或者简称:

```
<Shirt Color="Blue"/> 
```

* * *

[![](img/afdab6fe9dc0f962aebe297e3212a638.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ogiUJ1T9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/paintjars-wide.jpg)

## 如果有意义，混合是可以的

记住使用“属性”符号的键-值对必须是*唯一键*并且*不能再嵌套*。

因此，我们不能使用“属性”来指定一个衣柜中的所有衬衫，或者一个车队中的所有汽车。

在本系列的前一篇文章中，我警告过您不要混淆使用两种方法*，但是如果有意义的话，在任何一个 XML 格式的数据中同时使用“元素嵌套”和“属性”是完全正常的。*

 *例如:

```
<Closet>
 <Shirt Color="Blue"/>
 <Shirt Color="Red"/>
</Closet> 
```

或者:

```
<RootElement>
 <Car VIN="First car's Vehicle Identification Number here">
 </Car>
 <Car VIN="Second car's Vehicle Identification Number here">
 </Car>
</RootElement> 
```

或者简称:

```
<RootElement>
 <Car VIN="First car's Vehicle Identification Number here"/>
 <Car VIN="Second car's Vehicle Identification Number here"/>
</RootElement> 
```

或者，穿上一件特别复杂的衬衫:

```
<Shirt>
 <Color>
  Blue
 </Color>
 <Color>
  Red
 </Color>
 <Color>
  Green
 </Color>
 <Fabric Washable="No">
  Leather
 </Fabric>
 <Fabric Washable="Yes">
  Cotton
 </Fabric>
</Shirt> 
```

注意，根据我对这件衬衫的描述，你必须看两个不同的地方来判断你是否能洗它！

我当然不会争辩说，将“可洗”作为每种面料的属性，而不是作为衬衫整体的属性，是一个好的设计选择。

但是在阅读 XML 时，要准备好注意这些问题。您可能会在现实世界的 XML 中遇到这样令人沮丧的设计选择。

* * *

[![](img/d18c28f21b64805e1efc84adcb9e7442.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v3WjqiAB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/doorschoice-wide.jpg)

## 你的选择

如果您要从头开始编写 XML，您需要根据您的数据将如何被使用来做出设计选择。

例如，代表“T0”可洗的“T1”的最佳方式是“T2”你要做的决定。

*   “可洗”应该是衬衫的一部分还是每种面料的一部分？
*   “可清洗”应该被写成独立元素还是仅仅作为一个属性？

您将尝试优化:

1.  使用代码获取/修改值
2.  人类可读性

所有的信息组织都包括做出判断，权衡灵活性和简单性。

如果你为自己的需求做计划，就没有错误的答案！

[![](img/a75142b170bfb888b74d2f1118c54878.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JZwyFwFg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/doorschoice1answer-wide.jpg)

## 不是你的选择

当要编写软件来处理别人已经编写好的 XML 时，您实际上没有任何选择。

在这种情况下，您只需能够识别出 XML 的作者选择了哪种方法来存储**键值对**。

*   **问:**为什么？
*   **A:** 很大程度上是因为**命令**用于**提取**在帮助你阅读 XML 的工具和编程语言中，编写键-值对的两种方法风格**互不相同**。认识到其中的区别会让你省去很多麻烦！

例如，在 Python 的“ [ElementTree](https://docs.python.org/2/library/xml.etree.elementtree.html) ”包中:

*   “`.text`”命令提取两个标签之间写入的明文值。
*   “`.get('key_name_here')`”命令提取属性的明文值。

[![](img/9e9a42350b141ae6e94235b07ed69cb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WdD4DMbJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://katiekodes.cimg/shoppingbags.jpg)

## 外卖

在“元素”上为“键-值”对填充纯文本值的方式取决于您采用了两种方法中的哪一种来定义“键-值对”:

1.  使用项目的名称作为其“父”项目的键
2.  赋予项目属性

语法:

1.  当使用项目的名称作为其“父”项目的键时，您可以在定义元素的“标记集”中的两个“标记”之间键入一些文本。
    *   最好是在上面，这样人们就不会讨厌你了。
    *   让元素的标签之间没有明文值是很常见的，因为元素通常只是用来“容纳更多的元素”
2.  使用属性时，您可以在“`=`”后面键入一些引用文本****
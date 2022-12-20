# 学着休息一下

> 原文：<https://dev.to/nicolasini/learn-yourself-some-r-3a1i>

## 简介

我们生活在后大数据时代。如今，问题不再是如何存储我们捕获的大量数据，而是如何从中获得洞察力。

现在，看到招聘广告招聘新的职位非常普遍，比如数据科学家和类似的职位。这种新工作的内容是能够分析我们组织可以访问的数据，以有用的交流方式呈现数据，对数据建模以做出预测，并最终做出更明智的决策。

## Aarrrr (R)

过去，我们可以快速制作电子表格，并在其中绘制一些图表。试图加载一个有数百万行、数千列的电子表格最多只能让你的个人电脑停下来。我们需要通过编写代码来更有效地处理这些数据。

编程语言的前景一直在变化。在过去的几年里，R 编程语言在数据分析师/科学家中间越来越受欢迎。其原因是它提供了一套用于数据处理、计算和图形显示的集成软件设施。它包括:

*   有效的数据处理和存储设施，
*   一套用于计算数组(尤其是矩阵)的运算符，
*   用于数据分析的大型、连贯、集成的中间工具集合，
*   用于数据分析和屏幕或硬拷贝显示的图形设备，以及
*   一种开发良好、简单而有效的程序设计语言，包括条件、循环、用户定义的递归函数和输入输出设施。

## 让代码！

说够了，给我看看代码！

r 有三种基本的数据类型:数字、字符串和逻辑值。在此基础上，我们可以创建向量或列表，以及一个更受*限制的*数据结构，称为 Factors。

我们可以对数据进行基本的算术和逻辑运算。

```
1+1  # [1] 2  1  >  2  # [1] FALSE 
```

我们可以创建值的向量或列表。

```
# create a vector  numbers  =  c(1,  2,  3)  words  =  c("one",  "two",  "three")  logits  =  c(True,  False,  T,  F) 
```

我们可以创造因素。一个因子就像一个向量，但是我们预先定义了它可以包含的有效值，称为*级别*。默认情况下，它将使用提供的值列表来生成有效的集合，但是我们也可以指定它。显式指定级别的目的是为了向因子添加更多的值，即使这些值在原始向量中不存在。

```
# create a factor with default levels  myFactors  =  factor(c("one",  "two",  "three"))  # [1] one   two   three  # Levels: one three two 
```

```
# create a factor with specific levels  myFactors  =  factor(c("one",  "two",  "three"),  levels  =  c("one",  "two",  "three",  "four"))  # [1] one   two   three  # Levels: one two three four 
```

我们可以创建*数据帧*。可以把它们想象成表格或矩阵，其中每一列都是一个向量或因子。我们可以标记这些列，并在稍后添加更多的*行*到其中。

```
# Data Frame Example  Example.df  =  data.frame(Num  =  c(1,2,3),  Char  =  c("One",  "Two",  "Three"),  Fact  =  factor(c("a",  "b",  "c"),  levels  =  c("a",  "b",  "c",  "d")))  # the Char column has automatically been converted to a Factor, but we can reinstate it as a simple Vector  Example.df$Char  =  as.character(Example.df$Char)  # we can create new "rows" of data  NewRow  =  c(4,"Four",  "d")  # and append it to the data set  rbind(Example.df,  NewRow) 
```

数据框非常重要，它们是我们用来操作和绘制数据的主要数据结构。我们可以从文件(如 CSV 或其他格式)加载数据框，也可以从数据库、URL 等加载数据框。一旦我们的数据在数据框中，我们可以通过多种方式访问它。

```
# Rows  Example.df[1,]  Example.df[2,]  Example.df[c(1,2),]  # Columns  Example.df[,1]  Example.df[,2]  Example.df[,c(1,2)]  # Import Data  Mydata  <-  read.csv(file.choose(),  header  =  T)  # Plot Data with out of the box tools  plot(Sales  ~  TV,  data  =  Mydata)  boxplot(Mydata) 
```

r 自带一套开箱即用的实用功能电池。特别是围绕能够*探索*我们的数据。有两个函数特别值得一提:`summary`和`str`。

使用我们的第一个示例数据框，我们可以获得其值的汇总。它将在任何数字列中计算有用的统计分析，并对因子和字符串向量进行频率分析。

```
# Summary  summary(Example.df)  Num  Char  Fact  Min.  :1.0  One  :1  a:1  1st  Qu.:1.5  Three:1  b:1  Median  :2.0  Two  :1  c:1  Mean  :2.0  d:0  3rd  Qu.:2.5  Max.  :3.0 
```

*str* 函数将描述数据集中的每一列，并给出每一列的前几个值。

```
# str  str(Example.df)  'data.frame':  3  obs.  of  3  variables:  $  Num  :  num  1  2  3  $  Char:  Factor  w/  3  levels  "One","Three",..:  1  3  2  $  Fact:  Factor  w/  4  levels  "a","b","c","d":  1  2  3 
```

## 结论

这只是对 R 的简单介绍，它的能力超出了一篇短文所能包含的范围。值得注意的是，R 的优势在于使用库，就像任何其他编程语言一样，可以进一步扩展核心功能。
我们可以找到用于绘图、数据管理等的库。一些最受欢迎的库分别是`ggplot`和`tidyverse`。
我们也可以用 R 做更多的事情，它开箱即用，支持机器学习。我们可以使用线性或逻辑回归来拟合模型，并使用它们来预测新值。然后，我们可以绘制训练数据和预测输出，以显示我们的模型工作得有多好。或者我们可以使用`summary`函数得到模型的详细描述。但是，这是留给另一个职位。
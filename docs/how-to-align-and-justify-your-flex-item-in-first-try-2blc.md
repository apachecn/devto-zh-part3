# 如何在第一次尝试中对齐和调整您的 flex 项目

> 原文：<https://dev.to/iamshadmirza/how-to-align-and-justify-your-flex-item-in-first-try-2blc>

## **出主意**

这个想法是选择一个你有困难的话题，你认为其他人可能也会觉得有点难。看看这条由[@ erinfranmc](https://twitter.com/erinfranmc)
[![Tweet](img/9f61be83381e2de56fad384995ada8aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EOS0XHKL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/align-and-justify/tweet.png)
发的推文吧，嗯，我以前也有这个问题，现在我想出办法了。听起来是个不错的话题。

## **怎么了困惑**

所以基本上，在 React 中，我们使用 Flex 框来对齐我们的组件和属性: ***justifyContent*** 和 ***alignItems*** 完全令人困惑，你很难第一次就理解它们。就像第一次尝试插 USB 一样。

所以，我要告诉你我是如何了解这两个特性的，并努力确保在读完这篇博文后，你也能在第一次尝试中获得正确的排列。我们开始吧。

## **轴的概念**

这是我们要思考的核心概念。 ***轴。*T3】**

通常特别有两个轴:

1.  主茎

2.  横轴

**主轴**是视图或者说屏幕周围的水平线(暂且默认为主轴= >水平)。

**横轴**将垂直于主轴，并且在我们的情况下是视图或屏幕周围的垂直线(现在让我们将横轴= >垂直作为默认)。

让我们看看它看起来是什么样子:
[![The Two Axis](img/475d404140a027f7c066601a284a17b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--51Eo-Xq_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/align-and-justify/axis.jpg) 
我们将我们的视觉化视图滑动到这两条线上。

## **默认情况:**

我们将使用组合****align items***和 ***justifyContent*** 来实现正确的布局。*

 *React 医生是这样说的:

> 将 flexDirection 添加到组件的样式决定了其布局的主轴。应该横向(行)还是纵向(列)组织孩子？
> 
> *默认的 flexDirection 是“列”,即当你没有给视图分配任何属性时。*

为了便于记忆，我们将首先考虑***flex direction:【row】***(水平方向)，并看看 ***justifyContent*** 和 ***alignItems*** 在这种情况下如何表现。

> 请记住，这些事情已经记录在案，但这里的目标是不要混淆。

### **1。flexDirection: 'row':-**

通过赋予这个属性，我们可以确保孩子将被水平地分配给**。**

 **好吗？那么 ***justifyContent*** 和 ***alignItems*** 呢？他们仍然很困惑，对吗？

这就是我要说的，我是如何消除困惑并弄明白的。

请尽量记住列出属性的顺序，并按照顺序进行操作。

#### 1。调整内容:-

这是我们要学的第一件事。 ***justifyContent*** 的威力是:-

***调整内容*** 可让您沿 ***主轴*** 即水平方向移动视图。

让我们看看它是什么样子…
[![Row Justify](img/239ce49fb8e3dc94d81552572b37192e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lvEetDy_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/align-and-justify/row-justify.jpg)

看看球是如何水平滑动的，即在*主轴*上

简单吧？现在让我们转到下一件事。

#### 2。对齐项目:-

你可以把它记为 ***justifyContent*** 的反义词。在 ***中，我们沿着 ***主轴移动视图。*T11】*****

所以， ***alignItems*** 的功率为:-

***对齐项目*** 允许您沿 ***横轴*** 即垂直方向移动视图。

> *横轴总是垂直于主轴。只要记住*主轴*的位置，*十字*就已经到了 90 度。*

让我们看看它是什么样子…
[![Row Align](img/0ca608a20e0a3ad66f7c18dde329b189.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S7YxGyMO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/align-and-justify/row-align.jpg)

查看球如何垂直滑动，即在*横轴*上滑动

简单吧？在进入 *flexDirection:“专栏”*之前，我想让你回忆一下我们学习的顺序。相信我，这很重要。

*   flex direaction:' row ' _ horizontal)。

*   调整内容(沿水平主轴移动视图)。

*   alignItems(沿横轴移动视图)。

### **2。flexDirection:'列':-**

还记得我们在上一节是如何学习顺序的吗？我们将在这里做同样的事情，如果你得到了前一部分的权利，然后这将顺利进行。

> *假设 flexDirection: 'column '与 flexDirection: 'row '属性相反，并应用到目前为止所学的一切。*

***主轴*** 变为 ***垂直*** 。(行弯曲方向相反)

***横轴*** 变成了 ***横轴*** 。(行弯曲方向相反)

> *主轴和横轴随着弯曲方向的改变而改变位置。其他属性(justifyContent 和 alignItems)仍将同样工作。*

**1。对齐内容:-**

还记得 **justifyContent** 的威力吗？
可以让你沿着 ***主轴移动视角。***

那个东西仍然工作，但是现在*主轴*是*垂直*并且滑动视图将看起来像这样:-
[![Column Justify](img/b7712d2559d1b74c3e9c9063cc0059f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8umbqTr4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/align-and-justify/column-justify.jpg)

**1。对齐项目:-**

Align Items 之前是沿着 ***横轴*** 移动视图，所以它的行为应该还是一样的吧？

没错和以前一样但是现在 ***横轴*** 是*横*滑动视图会是这样:-
[![Column Align](img/539539ac7f695b4e6a07f5cdf77ceb4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aZFnmaeH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/align-and-justify/column-align.jpg) 
看多简单明了。

# **让我们更进一步:-**

现在，当我们知道了基础知识，让我们来看看其他两个不常用的弯曲方向。

### **1。flex direction:' row-reverse ':-**

工作原理几乎和 ***一样 flexDirection:【行】*** 和轴还是一样的。你只需要颠倒灵活开始和灵活结束的顺序。

记住，这里的基本思想是:-

> *颠倒开始和结束的顺序。*

顾名思义，它是 row 的反义词。

让我们看看它是如何工作的。

**1。自圆其说的内容:**
[![Row Justify Reverse](img/026a40a21b2f4c18f8349e7fe606b547.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5xSP-KFI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/align-and-justify/row-justify-reverse.jpg) 
**2。对齐项目:-**
[![Row Align Reverse](img/25b81bf05fe7ef986475047eb14820ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G0hM0BGv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/align-and-justify/row-align-reverse.jpg)

### **1。flexDirection:'列-反向':-**

这没什么奇怪的。只要记住基本的想法:

> *颠倒开始和结束的顺序。*

**1。自圆其说的内容:**
[![Column Justify Reverse](img/d0a48fc6eb5ad47bf78b38584b822c58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eazh_STg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/align-and-justify/column-justify-reverse.jpg) 
**2。对齐项目:-**
[![Column Align Reverse](img/0461d635105451837dc14489bb58463a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lcKX6VdJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/iamshadmirza/Blogs-by-Shad/master/blogs/align-and-justify/column-align-reverse.jpg)

## **结论:-**

*   只要记住*主轴*和*横轴*的概念就可以了。
*   在*挠曲方向下:【行】**主轴*水平，*横轴*垂直，即垂直于*主轴*。
*   *对齐内容*在*主轴*上滑动视图，始终。
*   *对齐项目*始终在*横轴*上滑动视图。
*   *flexDirection:【立柱】*反转*主轴*和*横轴*的方向。

*   *对齐内容*和*对齐项目*仍然和以前一样工作。

*   *反转*伸缩方向的基本思想是，你必须反转开始和结束的顺序，其他一切照常工作。

按顺序记忆帮助我清除了困惑，我能够在第一次尝试时就获得正确的排列。虽然第一次尝试正确插入 USB 对我来说仍然是个问题，也许有些事情真的没办法。

希望对你有帮助。

河鲱***
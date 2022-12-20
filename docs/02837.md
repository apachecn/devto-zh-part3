# 使用 ES6 语法循环(JavaScript)

> 原文：<https://dev.to/edezekiel/looping-using-es6-syntax-javascript-4b1>

[![](img/833badce589c054db6bf67c731b8e3e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DdMcrsMP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXCvcHF70YhwT6wReeG1_MA.jpeg)

### JavaScript 循环使用 for...的和为的...英寸

本周早些时候，我在处理一个编码问题，涉及到字符串中字符的循环。我想遍历两个字符串中的所有字符，以确定它们是否是字谜。这种任务在技术面试中很常见。

这让我更仔细地研究 Javascript for loops。我对森林特别感兴趣...的和为的...所以我写了这篇文章来分享我的发现。

### 使用 for 循环遍历字符串中的元素...关于

那么，在字符串中循环遍历字符的好方法是什么呢？一种方法是使用传统的 for 循环:

> *for(设 I = 0；我<弦长；i++){ console . log(string . charat(I))}*

这种 for 循环自二十年前出现以来就一直存在于 Javascript 中。然而，ES6 引入了一些漂亮的新语法。

自从 ES6 推出以来，您可以使用...循环的:

> *for(字符串的字符){console.log(字符)}*

难道不是为了...语句比传统的 for 循环更容易阅读！它还需要更少的输入，这意味着更少的错误空间。

### 均为...的和为的...可互换的？

TLDR:不，他们迭代不同的东西。

Per MDN:

> **为...of 语句迭代* [*可迭代对象*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators#Iterables) *定义要迭代的值。**
> 
>  ***为...in 语句以任意顺序迭代对象的* [*可枚举属性*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties) *。***

 **### 什么是可迭代对象？

如果一个对象定义了它的迭代行为，比如在 for 中循环什么值，那么它就是可迭代的...的构造。

当一个对象(或其原型链上的一个原型)具有一个带有 Symbol.iterator 键的属性时，该对象定义其迭代行为。

以下是 Javascript 的内置迭代程序:

*   线
*   排列
*   TypedArray
*   地图
*   一组

### 什么是对象的可枚举属性？

根据 MDN，可枚举属性是那些内部可枚举标志设置为 true 的*属性*。

乍一看，这对我来说有点神秘。如何判断属性的内部可枚举标志是否设置为 true？

事实证明，Object.prototype 中有一个方法可以回答这个问题。该方法称为 propertyIsEnumerable()。

MDN 再次给出了一个很好的例子，展示了如何使用这个方法来检查指定的属性是否是可枚举的。[这里的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/propertyIsEnumerable)是 MDN 例子的链接。

### 回到我的例子

让我们回头看看我们的例子，以确认是否为...属于和/或为了...在工程中:

> *let myString = " valar morghulis "*
> 
> *【hello world】
> 
> //StringIterator { }*
> 
> *console . log(mystring . propertyisenumerable([1])
> 
> //true*

如上所述，字符串有 Symbol.iterator 方法。此外，字符串也按字符进行索引。因此，您可以使用 for...奥弗尔的...迭代字符串中的字符。**
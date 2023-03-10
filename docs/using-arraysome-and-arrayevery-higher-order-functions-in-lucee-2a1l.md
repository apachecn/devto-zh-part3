# 在 Lucee 中使用 ArraySome 和 ArrayEvery 高阶函数

> 原文：<https://dev.to/mikeborn/using-arraysome-and-arrayevery-higher-order-functions-in-lucee-2a1l>

在这篇文章中，我将采用两个 *Lucee only* 高阶数组函数`arraySome()`和`arrayEvery()`，并解释它们的用途和目的。我还将回顾高阶函数的概念，以及 Lucee 5 中非常酷(但很少使用)的 lambda 表达式。

## 什么是高阶函数？

根据[维基百科](https://en.wikipedia.org/wiki/Higher-order_function)的说法，高阶函数是要么 1。取一个函数作为参数或 2。返回一个函数作为结果。我们将看到的这两个函数，`arraySome()`和`arrayEvery()`，只是 Coldfusion 中定义的许多“高阶函数”中的两个。CF 中其他一些高阶函数的例子有`arrayMap()`、`listEach()`或`structFilter()`，所有这些都将一段数据和一个函数一起，并以某种方式将函数应用于每个数据项。

现在我们已经稍微理解了这个概念，让我们深入研究一下`arraySome()`和`arrayEvery()`！

## 数组

对给定数组中的每个元素执行给定的函数。如果函数曾经返回过`true`，那么`arraySome()`本身将返回`true`——这使得这个函数非常适合条件。

这里有一个简单的例子:

```
var checkBadWord = function(badWord) {
    return form.comments CONTAINS badWord
};
var isVulgar = arraySome(allBadWords, checkBadWord); 
```

这个例子对`allBadWords`数组中的每一项运行`checkBadWord`函数。然后，从评论表单发布的提交的`comments`字段被检查是否存在不良单词，如果发现任何不良单词，则`isVulgar`变量被设置为 true。

想要完整的参考资料，去看看 CFDocs.org/arraysome 的。

## ArrayEvery

与`arraySome()`非常相似，这个`arrayEvery()`函数非常适合条件句，因为它的计算结果是一个布尔值——它迭代所提供的数组，执行给定的函数，如果*所有的*迭代(闭包执行)都返回真，则返回`true`。

这里有一个真实世界的例子，说明如何使用`arrayEvery()`解析联系信息:

```
var contacts = [
    { name: "Michael", email: "info@test.com" },
    { name: "Travis", email: "xyz" },
];
var isValidEmail = function( contact ) {
    return isValid("email",contact.email);
}
if ( arrayEvery(contacts, isValidEmail) ) {
    // SEND EMAIL
} 
```

这向我们展示了在我们盲目地向这些地址发送电子邮件之前，如何使用`arrayEvery()`来确认数组中的*所有*项都有一个有效的电子邮件地址。

## λ表达式

现在您已经知道了这些是如何工作，让我们再看一下代码片段，看看我们是否可以稍微清理一下。

```
var isValidEmail = function( contact ) {
    return isValid("email",contact.email);
}
if ( arrayEvery(contacts, isValidEmail) ) {
    // SEND EMAIL
} 
```

如您所见，函数定义等开销相当大。这是 Lucee 5 中引入的 [lambda 表达式](https://docs.lucee.org/guides/lucee-5/lambda.html)的完美例子。Lambda 表达式提供了一种更短的编写函数的方式，对于函数式编程尤其有用。让我们看一个例子。

```
if ( arrayEvery(contacts, (contact) => isValid("email",contact.email) ) ) {
    // SEND EMAIL
} 
```

使用 lambda 表达式是编写闭包的一种更简洁的方式。

## 总结起来

现在你有了高阶函数的简要概述，如何使用`arraySome()`和`arrayEvery()`，甚至如何使用 lambda 表达式来简化代码示例。有什么问题吗？随时[联系我](https://michaelborn.me)！
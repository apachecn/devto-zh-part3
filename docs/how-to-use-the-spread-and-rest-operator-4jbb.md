# 如何使用 spread and rest 运算符

> 原文：<https://dev.to/napoleon039/how-to-use-the-spread-and-rest-operator-4jbb>

## 它们是什么？

ES6 中引入的高级特性之一，spread 运算符和 rest 参数允许我们分别展开和组合多个元素。
两者都用 3 个句点`...`表示。尽管它们可能看起来一样，但它们的工作方式不同。当 spread 操作符扩展一个数组、对象、字符串或任何其他可迭代对象时，rest 参数的作用正好相反——它将多个元素组合成一个可迭代对象。

## 如何使用它们？

让我们通过一些例子来看看 spread 运算符的用法

[![Example without using spread operator](img/eab47248a5b7cf67972c616a80a4ff76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vk8pKRjs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jyngeaa8dsrrs3zvlc5w.png)

这里有一系列水果。它将以这种方式打印到屏幕上

[![Result without using spread operator](img/8ab8047c1b9ca1fc22c743b6f44ea3be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eRzMLQ_L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8dy46rrz4r1lxa6l0erc.png)

并使用扩展运算符

[![Using spread operator](img/e1e36db197da37eabe071283a2f0bfde.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f57uzrb7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h8wrw2ovp54pdvfuyil7.png)

数组是这样打印的。

[![Result of using spread operator](img/6911bd7b5795c250667ad4669af1800e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QhxRuTXw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0k29svaroj4skwxcc3b5.png)

spread 运算符将数组的内容扩展为单个元素。我们还可以在添加新元素时创建该数组的副本:

[![Second example with spread operator](img/bae979de9aa68703024e03b866a0fab1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8IqCYYXr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4iatyub7y95f1fqdrdai.png)

因此，扩展运算符有许多用途。

* * *

rest 运算符用于组合多个元素，在数组和对象析构期间特别有用。

[![Array destructuring with rest operator](img/400622e2d114a0964cb41a350eae30ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S4ledPyv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/47s5kwb47fpvgv873uz9.png)

在上面的例子中，数组`people`的前两个元素分别被*析构*或者分解成两个变量`doctor`和`lawyer`。其余的元素在 rest 操作符的帮助下以数组的形式组合到`students`变量中。

这是另一个对象的例子:

[![Object destructuring with rest operator](img/8e299e9511596683c909e4aa32983080.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ub9JHspM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mrbn2g8nbbjjezygx9c9.png)

这里，我们获取一个对象，然后将其分解并分配给不同的变量，然后将剩余的对象属性合并到一个对象中。让我们把它打印出来，看看结果:

[![Object destructuring result](img/a2e565ebb0560221f55d0fda2c0e2676.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XLbtZBB_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bjncsj09r1vuq4ep3m8r.png)

## 在哪里使用它们？

我们已经看到了如何使用 spread 和 rest 操作符。我们可以使用 spread 操作符有效地分割数组、对象和字符串。将字符串拆分成字符变得更加容易。处理物品变得不再麻烦。无论是复制、克隆还是连接，spread 操作符都可以轻松地处理数组和对象。

至于 rest 操作符，在处理变量函数参数时，不再需要做那些复杂的事情，比如访问`arguments`对象。简单地使用 rest 操作符就能解决所有问题:

[![Variable function arguments handled with rest parameter](img/81fa0bff728f1fd3dd8ea53a40da3d2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nBd5pehu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m259uxn2z1z9i9rscy4m.png)

这样做很好，因为 rest 操作符将传递给函数的所有参数组合在一个数组中。我们所要做的就是遍历数组，我们可以访问所有的参数。

* * *

## 结束语

这里有另一篇好文章[解释了由](https://dev.to/guin/a-guide-to-rest-and-spread-part-2-38fm)[安吉丽卡·雅罗斯兹](https://dev.to/guin)撰写的 rest 和 spread 操作符。她非常详细地解释了这些操作符。

在我看来，rest 和 spread 操作符是 ES6 最好的特性之一，还有 [Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 。除了使代码可读性更好之外，使用它们还有很多好处。如果你还没有使用它们，你还在等什么？
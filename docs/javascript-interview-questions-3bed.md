# JavaScript 面试问题

> 原文：<https://dev.to/vigowebs/javascript-interview-questions-3bed>

**1。null 和 undefined 有什么区别？**
在 JavaScript 中，undefined 的意思是，变量的值还没有定义。而 typeof undefined 也是“未定义”。在 JavaScript 中，我们在某些方面变得不明确，比如:声明一个变量而不赋予它任何值，存储一个函数返回值给一个变量，但是函数不返回任何东西，return 语句不返回任何值，函数参数不传递，全局变量不明确。

null 表示空值或不存在的值，用于表示“无值”。尽管 typeof null 返回 object，但 null 是基元类型而不是对象。

**2。==和===有什么区别？**
= =不检查操作数的类型，而===检查操作数的类型和值。

另一种说法是，==将把操作数转换成相同的类型，然后进行比较。但是===不做任何转换。如果它们中的任何一个属于不同的类型，它将简单地返回 false。

**3。如何检查给定的变量是否是数组？**
我们可以用 Array.isArray()来判断传递的值是否是数组。但是它不能在 IE8 及以下版本上运行。要支持老版本我们可以用 object . prototype . tostring . call(arr)= = '[object Array]'；

**4。变量的范围是什么？什么是全局范围和局部范围？**
作用域是指编译器在程序中寻找变量或解析器如何解析变量值的一组规则。

在 JavaScript 中，我们有两种作用域:局部和全局。如果我们在函数之外创建了一个变量，它就是一个全局变量，可以从程序的任何地方访问。在函数内部声明的变量有局部作用域。它们可以从函数内部访问，但不能从函数外部访问。

**5。对象中的原型是什么？对象中的构造函数是什么？**
JavaScript 可谓是基于原型的语言。每个对象都有从另一个对象继承方法和属性的原型对象。

每个函数都有一个原型属性，其值是一个包含构造函数属性的对象。此构造函数属性指向原始构造函数。

**6。运算符的类型是什么？**
它是一元运算符，意味着它将只有一个操作数。它将返回操作数的数据类型，如“字符串”、“数字”或“布尔”。结果类型总是字符串。

**附加:**空返回对象的类型，即使它不是一个对象。从 JavaScript 的第一个版本开始，typeof 检查操作数的类型标记，该标记是 1-3 位的(比如，000 表示对象，1 表示整数，100 表示字符串)存储值。null 是机器代码空指针或对象类型标签，所以它返回 object。

要阅读更多内容，请点击[此处](http://blog.vigowebs.com/post/2017/javascript-interview-questions/)或在[安卓应用](https://play.google.com/store/apps/details?id=com.vigowebs.interviewquestions)上下载

**奖励:**提升你的 JavaScript 代码示例技能，以在面试中获得或学习试试我们的 [JS 代码示例](https://play.google.com/store/apps/details?id=com.vigowebs.jscodesamples)应用程序
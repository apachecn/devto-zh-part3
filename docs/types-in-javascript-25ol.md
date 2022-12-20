# JavaScript 中的类型

> 原文：<https://dev.to/kartik2406/types-in-javascript-25ol>

[![Scope](img/f4a3b23407782fc1bc38ed628dcee176.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--87wXHbTQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./https://thepracticaldev.s3.amazonaws.com/i/o75igcwodrs7ug6hry01.jpg)

数据可以是不同的形式，数字，字符串，布尔等。编程语言的类型系统帮助我们存储各种数据。在这篇文章中，我们将讨论类型化语言的类型，Javascript 中的类型和类型，一些有用的特性。

### 不同语言如何处理类型？

编程语言中的输入可以是静态的，也可以是动态的。

*   静态类型化:在声明过程中为变量指定类型。
*   动态类型:你只需要声明变量，在运行时给它分配任何类型的数据。您还可以在运行时更改分配给它的数据类型。

### Javascript 如何处理类型？

Javascript 是一种动态类型语言。这样你就不需要指定类型，你只需要声明变量并随意使用它们。虽然这很有趣，但是你必须小心，如果你给一个变量分配了错误的类型，就会导致错误。在 Javascript 中，您还可以使用 [typeof](https://bitsofco.de/javascript-typeof/) 操作符在运行时检查变量的类型。

### Javascript 中的不同类型

Javascript 中有 [7 个原语类型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)。

它们是:

1.  String: String 允许你存储一组字母。
2.  数字:数字数据对算术运算很有用，Javascript 也有一个[数学库](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math)，使用它你可以很容易地进行幂等运算。
3.  Boolean:值可以是真或假，对标志很有用。
4.  空
5.  不明确的
6.  Object:键值对的集合。
7.  符号:根据 MDN“符号值可用作对象属性的标识符；这是数据类型的唯一目的”。在这一点上你不需要学习它，你可以在弄清楚其他 Javascript 概念之后再来看一看。

Null 和 Undefined 是变量可以具有的值。它们都表示变量不存储任何数据。两者之间有一些不同，null 是显式的，undefined 是隐式的。如果你想有一个没有数据的变量，删除它的数据，只需将它赋为 null。如果你创建了一个变量，但没有给它赋值，它将是未定义的。关于 null 类型的另一个有趣的事情是，**类型的 null** 返回**【对象】**(你可以在这里阅读更多关于它的[)。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof#Additional_information)

### 类型转换

通常，您会希望将一种类型的数据转换成另一种类型的数据。【Javascript 为这些提供了许多帮助函数。例如，要将一个字符串转换成一个数字，你可以使用 [parseInt()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseInt) 方法，要将一个数字转换成字符串，你可以使用 [toString()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toString) 方法。

### 真值和假值

Javascript 还有另一个有用的特性，它将值视为真和假。
这是在布尔表达式中使用的值的处理方式。真值表示该值被认为是真的，假值表示假的。然后，在处理变量之前，您可以使用这个特性来检查变量是否已定义。

除了基本类型，Javascript 还有数组、集合和映射等数据结构，我们将在下一篇文章中讨论它们。如果你喜欢这篇文章，请分享它。
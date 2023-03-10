# 在编程语言之间切换

> 原文：<https://dev.to/programmingdecoded/moving-between-programming-languages-4b6m>

最近我在读 V. Anton Spraul 写的《像程序员一样思考》一书。我决定用我知道的所有 4 种编程语言(PHP、JavaScript、C#和 Python)做编码练习，以便更好地将它们分开。在许多方面，语法非常相似，但很容易被微小的差异绊倒。你可以在这里查看代码:[https://github.com/phil4literacy/coding_challenges](https://github.com/phil4literacy/coding_challenges)

以下是我学到的一些东西:

输出:

*   Javascript 没有提供在同一行记录新输出的方法，所以我需要将输出添加到一个变量中，并在创建最终输出后打印。在 Python 中，你必须使用 **print(item，end = " "**,以便将输出打印在同一行上。
*   每种语言使用不同的函数名打印到终端(PHP: echo，JavaScript: console.log，C#: Console。写，Python: print)。

对于循环:

*   c#需要定义变量(如字符串或整数)。在 Python 中，使用“范围”而不是计数语法。

字符串实用程序:

*   在所有语言中，重复一个字符串的方式完全不同。在 PHP 中，可以用 str_repeat()来完成。我喜欢 Python 中的打印方式(string * times)。

类别:

*   所有 4 种编程语言都使用“class”来声明一个类，但是 JavaScript 类中的方法并不使用“function”来声明。你只需写出函数名。
*   在 JavaScript、C#和 Python 中，调用一个方法要用一个点，而在 PHP 中要用一个箭头。c#:myhashdrawing . create halfx(row)；PHP:$ myHashDrawing-> create halfx($ row)；
*   构造函数使用与 C#中的类相同的名称创建。在 PHP 中使用公共函数 __construct()，在 JavaScript 中使用它的构造函数()，在 Python 中使用它的 def __init__(self)
*   Python 不使用 **new** 关键字来创建对象。

当然，这两种语言之间有更多的差异和相似之处，有时从另一种语言切换过来需要一点时间来热身。
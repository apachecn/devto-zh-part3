# 面向 Javascript 开发人员的 C#介绍，第 1 部分

> 原文：<https://dev.to/scottslatton/intro-to-c-for-javascript-developers-part-1-2gg2>

目前我在熨斗学校的代码营的最后一段，在这一点上，我已经学会了 Ruby 和 Javascript 作为我的主要编程语言。开始申请工作的时间就像一颗燃烧的流星一样向我的同学们走来，冒名顶替综合症已经在我的同龄人中相当严重地存在了。在查看工作申请时，他们会看到一堆他们不知道的技术，以及他们可能没有见过或没有时间看一眼的缩写。我知道这些人非常聪明，他们以接近光速的速度获得概念并开始应用。因此，他们需要接触这些其他技术，才能意识到他们已经很好地掌握了核心概念，转移这些概念不会有任何问题。

[![I believe in you!](img/fa844cbc2820d06d34cbc275779dc234.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kcqHdgoL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://memegenerator.net/img/instances/28323103/believe.jpg)

也就是说，我想现在就尝试向我的同行展示另一种编程语言，一种他们还没有接触过的语言。我的目标不是让任何人坐在座位上，对铺天盖地的信息量感到气馁和崩溃，而是要表明，如果你已经通过了最后一轮 Flatiron(或者如果你可以轻松地用 Javascript 编码)，学习另一种语言真的是微不足道的。

有一种说法是“一旦你知道如何编码，剩下的就是语法了。”我想证明这有多真实。这不仅仅是听起来很好的陈词滥调，我将通过并排展示 javascript 的常见函数和语法及其 C#对应物来证明这一点。

**C#背景**

[![Don't cut yourself on this #edge](img/66a0e8b93030c73b0ea0e76e856e2152.png)](https://i.giphy.com/media/Zvgb12U8GNjvq/giphy.gif)

C#(发音为 See-Sharp)是在 2002 年发布的。微软的. NET 框架。如果你出于某种原因不喜欢微软，没关系，C#和 Java 在语法上非常相似，所以你可以很容易地在它们之间转换。这些语言的目的是提高生产率，易于使用和部署。如果你有游戏开发的倾向([像我](https://dev.to/scottslatton/game-development-pipeline-and-technologies-h0b))，可以在 Unity 引擎中找到 C#，也可以用来制作原生移动应用。它们是强类型语言，这意味着您必须显式声明变量的数据类型。

然而，C#确实有像 Javascript 那样的隐式转换，这使得它非常动态。C#也是一种编译语言，而不是解释语言，这意味着在运行程序之前，你必须首先将你的代码转换成临界大小的字节。这些程序仍然是从上到下解释的，尽管就像 JS 一样，所以在声明和调用变量和函数时要小心。另一件需要注意的事情是，C#使用 camelCase 处理局部变量和参数，使用 PascalCase 处理类名和方法。自动垃圾收集是另一个特性。C#和 Javascript 都是基于 C 的，所以你会看到很多相似之处。

**解剖类文件**

[https://repl.it/@ScottSlatton/JollyFrenchCores?lite=true](https://repl.it/@ScottSlatton/JollyFrenchCores?lite=true)

好了，不要被上面的代码吓到了，我会很快地带你看一遍，告诉你你已经知道所有这些代码在做什么了。

顶部写着“[使用系统；](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/using-directive)“这只是一个扩展，所以它就像“从导入 X”。/Xfile "的库，或者 Ruby 中的" Require Gem"。

继续往下，我们有“名称空间测试”,正如您所猜测的，它将文件中的所有文本作为“测试”,以保护它免受外部干扰，并防止它污染整个应用程序的名称空间。你想怎么叫都行。

接下来我们有“类 MainClass ”,正如你所料，它是“MainClass”类代码的主体。这可以改为“用户”或“皮卡丘”,这应该没有什么意外。

第 5 行是看起来不熟悉的地方，但是一旦我把它分解，你会意识到你知道每个单词的作用。

***【Public】***这是对传入函数范围的声明，它或者是公开可用的，或者是私有的。

***“静态”*** 是一个关键字，声明该方法是全局的，不需要创建类的实例就可以调用。

***【Void】***是函数的返回值，大家可能还记得 JS 中 Console.log()没有返回值，它只是打印到控制台就完成了。

***【Main()】***是函数的名称，充当应用程序的入口点。应用程序中只有一个类需要 Main 方法，如果有多个，您可以指定要在 IDE 中使用哪个类的 Main 方法。

***【string[]args】***是运行时传递给 Main()函数的任何参数的参数。这是完全可选的，如果你不打算在运行时使用任何参数，你可以省略它们。您可以随意命名“args ”,它只是一个参数名。

***控制台。WriteLine("Hello World")*** 正如你所料，只是控制台。Log()或 JS 或 Ruby 中的“puts”。

希望它看起来不再那么可怕。

**变量声明**

C#和 JS 中变量声明的主要区别在于，变量的类型需要在给出变量名之前立即声明。

所以在 JavaScript 中你会看到。

```
let greeting = "Hello" 
```

但是在 C#中，同样的变量需要声明为:

```
string greeting = "Hello" 
```

这里是 C#中常用数据类型的简短列表，更长的列表可以在这里找到[。](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/built-in-types-table)

```
string greeting = "Hello World";
int number = 100;
double trouble = 10.2;
char grade = 'A';
bool beef = true; 
```

您也可以声明一个变量，然后在以后对其赋值，如下所示。

[https://repl.it/@ScottSlatton/QuietInternalCertification?lite=true](https://repl.it/@ScottSlatton/QuietInternalCertification?lite=true)

ReadLine()函数将用户的输入值赋给“name”变量，然后将最后一个连接的字符串记录到我们的日志中。很基础，不过这个应该很熟悉。

**循环遍历一个数组**

[https://repl.it/@ScottSlatton/IroncladMeanTask?lite=true](https://repl.it/@ScottSlatton/IroncladMeanTask?lite=true)

在 C#中，有许多不同的方法来声明数组的。在上面的例子中，这是我个人最喜欢的创建新数组的方法，但是我们也可以在使用之前声明数组的大小，这是该语言的一个节省内存的特性。使用中的 for 循环也与您在 Javascript 中看到的几乎相同。

希望这为你揭开了编译语言的神秘面纱，并且当你在工作申请中看到这种语言时，你会感觉更舒服一些。

在我的下一篇文章中，我将讨论 MVC、对象和迭代器。我们还没完呢！
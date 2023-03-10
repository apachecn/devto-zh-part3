# JavaScript 优雅的错误处理

> 原文：<https://dev.to/bnevilleoneill/elegant-error-handling-with-the-javascript-either-monad-3cdg>

[![](img/9b628fee7d3cf95ef740b0cd37b495f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LyJjNF9M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGO1OCHjYxiRXTiowKhNUUA.jpeg)

让我们来谈谈如何处理错误。JavaScript 为我们提供了处理异常的内置语言特性。我们在 try 中包装有问题的代码...catch 语句。这让我们可以在 try 部分编写“快乐路径”,然后在 catch 部分处理任何异常。这不是一件坏事。它让我们专注于手头的任务，而不必考虑每一个可能发生的错误。这肯定比在我们的代码中加入无尽的 if 语句要好。

没有尝试...catch，检查每个函数调用的结果是否有意外值会变得很乏味。异常和尝试...catch 块服务于一个目的，但他们有一些问题。它们不是处理错误的唯一方式。在这篇文章中，我们将看看使用“要么单子”作为一种替代尝试...接住。

在我们继续之前有几件事。在本文中，我们假设你已经知道了[函数组合](https://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-functions/#composition)和[奉承](https://jrsinclair.com/articles/2016/gentle-introduction-to-functional-javascript-functions/#currying)。如果你需要一分钟来温习这些，那完全没问题。还有一个警告，如果你以前没有遇到过像单子这样的东西，它们可能看起来真的…不一样。使用这样的工具需要思维的转变。

如果你一开始感到困惑，不要担心。每个人都这样。我在最后列出了一些可能有帮助的参考资料。但是不要放弃。一旦你进入其中，这东西是令人陶醉的。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 一道例题

在我们深入探讨异常的问题之前，让我们先讨论一下它们为什么存在。这就是我们有异常和尝试…捕捉块的原因。他们并不是一直都是坏的 T2。

为了探究这个主题，我们将尝试解决一个示例问题。我试图让它至少半真实。假设我们正在编写一个函数来显示通知列表。我们已经设法从服务器上取回了数据。但是，不管出于什么原因，后端工程师决定以 CSV 格式而不是 JSON 格式发送。原始数据可能如下所示:

[https://medium . com/media/340 b 19 BF 941 a0c 198 BD 6 abda 56 FD 78 a 2/href](https://medium.com/media/340b19bf941a0c198bd6abda56fd78a2/href)

现在，最终，我们希望将这段代码呈现为 HTML。它可能看起来像这样:

[https://medium . com/media/b 95503 fbfdaeb 6 b5 e 261 AC 54 df 36 b 165/href](https://medium.com/media/b95503fbfdaeb6b5e261ac54df36b165/href)

为了使问题简单，现在，我们只关注处理 CSV 数据的每一行。我们从几个简单的函数开始处理行。我们将使用第一个来分割字段:

[https://medium . com/media/9041 c 20 bb 82 f 981 BF 081 ff 83 b 07 bdeb 4/href](https://medium.com/media/9041c20bb82f981bf081ff83b07bdeb4/href)

现在，这个函数被过分简化了，因为这是一个关于错误处理的教程，而不是 CSV 解析。如果其中一条消息中有一个逗号，那就大错特错了。请不要使用这样的代码来解析真实的 CSV 数据。如果您确实需要解析 CSV 数据，请使用经过测试的 CSV 解析库。

分割数据后，我们希望创建一个对象，其中的字段名与 CSV 标题相匹配。我们假设已经以某种方式解析了标题行。请注意，如果行的长度与标题行(和 _ 不匹配，我们将抛出一个错误。zipObject 是一个 [lodash](https://lodash.com/) 函数):

[https://medium . com/media/4447001360 FCC 70927994771 b 1836155/href](https://medium.com/media/4447001360fcc70927994771b1836155/href)

之后，我们将向对象添加一个人类可读的日期，这样我们就可以在模板中将其打印出来。这有点冗长，因为 JavaScript 没有强大的内置日期格式支持。请注意，对于无效日期，它会引发一个错误:

[https://medium . com/media/c 8 E0 A8 c 09127 c 69 b 12 ff 7b 405 b 7 A8 db 6/href](https://medium.com/media/c8e0a8c09127c69b12ff7b405b7a8db6/href)

最后，我们将对象传递给一个模板函数，得到一个 HTML 字符串:

[https://medium . com/media/6269155 E3 C2 AC 365 E3 b 314303923760 b/href](https://medium.com/media/6269155e3c2ac365e3b314303923760b/href)

如果我们最终发现了一个错误，最好也能打印出来:

[https://medium . com/media/35c 26805 f7f 48939 D5 AEF 59020 a 5299 a/href](https://medium.com/media/35c26805f7f48939d5aef59020a5299a/href)

一旦所有这些都准备好了，我们就可以把它们放在一起创建我们的函数来处理每一行:

[https://medium . com/media/6d 05637 F3 d 6423 ba F3 e 69 e 82 b49 a 7745/href](https://medium.com/media/6d05637f3d6423baf3e69e82b49a7745/href)

我们有了示例函数。就 JavaScript 代码而言，这还不算太糟。但是让我们仔细看看我们是如何管理这里的异常的。

### 例外:好零件

那么，尝试有什么好处呢...接住？需要注意的是，在上面的例子中，try 块中的任何步骤都可能抛出错误。在 zipRow()和 addDateStr()中，我们故意抛出错误。如果出现问题，我们只需捕获错误，并在页面上显示错误消息。没有这种机制，代码会变得非常难看。下面是没有异常的*可能的样子。我们不会抛出异常，而是假设我们的函数将返回 null:*

[https://medium . com/media/BDF 53951 b44c 25097664 e 1911 a 251 FB 7/href](https://medium.com/media/bdf53951b44c25097664e1911a251fb7/href)

如您所见，我们最终得到了许多样板 if 语句。代码更加冗长。而且很难跟上主要的逻辑。此外，我们没有办法让每一步都告诉我们错误消息应该是什么，或者它们失败的原因。(除非，我们对全局变量做了些手脚。)所以，我们要猜测，如果函数返回 null 就显式调用 showError()。毫无例外，代码更加混乱，更难理解。

但是再看看带有异常处理的版本*。它给了我们一个清晰的‘快乐路径’和异常处理代码的分离。尝试的部分是快乐之路，捕捉的部分是悲伤之路(可以这么说)。所有的异常处理都发生在一个地方。我们可以让单个函数告诉我们失败的原因。总的来说，看起来还不错。事实上，我认为我们大多数人会认为第一个例子是一段简洁的代码。为什么我们需要另一种方法？*

### try…catch 异常处理的问题

异常的好处是让你忽略那些讨厌的错误条件。但不幸的是，他们做得有点太好了。你只需抛出一个异常，然后继续前进。我们可以稍后想出在哪里抓到它。我们都*打算*把那个 try…catch 块放在适当的位置。真的，我们有。但是它应该去哪里并不总是很明显*。忘记一个太容易了。在您意识到之前，您的应用程序就崩溃了。*

另一件要考虑的事情是异常使我们的代码不纯。[为什么功能纯度是一件好事](https://drboolean.gitbooks.io/mostly-adequate-guide-old/content/ch3.html)是一个完全不同的讨论。但是让我们考虑一下函数纯洁性的一个小方面:引用透明性。对于给定的输入，引用透明的函数总是给出相同的结果。但是对于抛出异常的函数就不能这么说了。在任何时候，它们都可能抛出一个异常，而不是返回值。这使得思考一段代码实际上在做什么变得更加复杂。但是如果我们可以两者兼得呢？如果我们能想出一个*纯*的方法来处理错误会怎么样？

### 想出一个替代方案

如果我们要编写自己的纯错误处理代码，那么我们需要始终返回值。那么，作为第一次尝试，如果我们失败时返回一个错误对象会怎么样呢？也就是说，无论我们在哪里*抛出*一个错误，我们只是*返回*它。可能看起来像这样:

[https://medium . com/media/38a 818917 a3 be 3473 f 045 a6ee 38 FB 798/href](https://medium.com/media/38a818917a3be3473f045a6ee38fb798/href)

这只是版本上非常轻微的改进，没有例外。但是这样更好。我们已经将错误消息的责任转移到了单独的函数中。但仅此而已。我们仍然有这些假设语句。如果有某种方法可以封装模式，那就太好了。换句话说，如果我们知道我们有一个错误，就不要去运行剩下的代码了。

### 多态性

那么，我们该怎么做呢？这是个棘手的问题。但是这是可以通过*多态性*的魔力实现的。如果你以前没有遇到过多态性，不要担心。它的意思是“为不同类型的实体提供一个单一的接口。”在 JavaScript 中，我们通过创建具有相同名称和签名的方法的对象来实现这一点。但是我们给它们不同的行为。一个典型的例子是应用程序日志。我们可能希望根据所处的环境将日志发送到不同的地方。因此，我们定义了两个 logger 对象:

[https://medium . com/media/37 eff fc 0d 3492 fa 4b 6490 a 8309 EDFA a6/href](https://medium.com/media/37efffc0d3492fa4b6490a8309edfaa6/href)

这两个对象都定义了一个需要单个字符串参数的 log 函数。但是他们的行为不同。它的美妙之处在于我们可以编写代码来调用。log()，但不关心它使用的是哪个对象。它可能是一个 consoleLogger 或 ajaxLogger。两种方式都可以。例如，下面的代码将同样适用于这两个对象:

[https://medium . com/media/df 6 AC 0 da 3521 f 57 cebe 2 Fe 60 b 8d 31 b 8 b/href](https://medium.com/media/df6ac0da3521f57cebe2fe60b8d31b8b/href)

另一个例子是。所有 JS 对象上的 toString()方法。我们可以在我们创建的任何类上编写一个. toString()方法。所以，也许我们可以创建两个类来实现。toString()不同。我们称它们为左和右(一会儿我会解释为什么):

[https://medium . com/media/d35a 622 b 9 e 35d 9 f 458 d6b 521 f 503 a 171/href](https://medium.com/media/d35a622b9e35d9f458d6b521f503a171/href)

现在，让我们创建一个调用。这两个对象上的 toString():

[https://medium . com/media/b 48360 f1 a 44 c8 faec c03 ff 558 a2 fb1 b 2/href](https://medium.com/media/b48360f1a44c8faecc03ff558a2fb1b2/href)

我知道这并不令人惊讶。但关键是我们有两种不同的行为使用同一个接口——这就是多态性。但是请注意一些有趣的事情。我们使用了多少 if 语句？零。没有。我们已经创建了两种不同的行为*，没有一个 if 语句*。也许我们可以用这样的东西来处理我们的错误…

### 左右

回到我们的问题，我们想为我们的代码定义一条快乐的路径和一条悲伤的路径。在快乐的道路上，我们只是快乐地运行我们的代码，直到错误发生或我们完成。如果我们最终走上了这条可悲的道路，我们就不会再费心去尝试运行代码了。现在，我们*可以*称我们的两个类为‘Happy’和‘Sad’来代表两条路径。但是我们将遵循其他编程语言和库使用的命名约定。这样，如果你进一步阅读，就不会那么困惑了。因此，为了符合惯例，我们将我们的悲伤路径称为“左”,将我们的快乐路径称为“右”。

让我们创建一个方法，如果我们在快乐的道路上，它将接受一个函数并运行它，但是如果我们在悲伤的道路上，则忽略它:

[https://medium . com/media/18f 667743 a 59920 F2 a 78 da 1165 c 5885 e/href](https://medium.com/media/18f667743a59920f2a78da1165c5885e/href)

然后我们可以这样做:

[https://medium . com/media/ca 19 fa 021 F2 b 0983 E4 ef 4821 AAC 883d 3/href](https://medium.com/media/ca19fa021f2b0983e4ef4821aac883d3/href)

### **地图**

我们离有用的东西越来越近了，但还没到那一步。我们的。runFunctionOnlyOnHappyPath()方法返回 _value 属性。这很好，但是如果我们想要运行多个函数，这就很不方便了。为什么？因为我们不再知道我们是在快乐的道路上还是悲伤的道路上。一旦我们在左或右之外取值，这些信息就消失了。所以，我们可以做的是返回一个包含 new _value 的 Left 或 Right。我们会缩短这个名字。我们正在做的是将一个函数从平面值的世界映射到左右世界。所以我们调用方法。地图():

[https://medium . com/media/6be 531475 ef 2871 a 287 f 0 fed 15 D3 c8 af/href](https://medium.com/media/6be531475ef2871a287f0fed15d3c8af/href)

有了这些，我们就可以使用 Left 或 Right 和一个[流畅风格的语法](https://en.wikipedia.org/wiki/Fluent_interface):

[https://medium . com/media/d 3100 AE 4 de 5b 9 a 9 b 204 a 0 Fe 9202226 f 8/href](https://medium.com/media/d3100ae4de5b9a9b204a0fe9202226f8/href)

我们有效地创造了两条赛道。我们可以通过调用 new Right()将一段数据放在右声道，通过调用 new new Left()将一段数据放在左声道。

[![](img/7b6c558653d17b2f3fca278fc1a6285d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K16Ahmg6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuVPoiOggTv9bfUJzQjbTMQ.png)

如果我们沿着正确的轨迹绘制地图，我们会沿着快乐的路径处理数据。如果我们最终走上了左边的路，什么也不会发生。我们只是不断地传递值。如果我们说，把一个错误放在左边的轨道，那么我们有一些非常相似的东西去尝试…捕捉。

[![](img/b952f24ad9222d91cf309718a2ef95f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FSZPnu0i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A14rnWjqFcOxKuvMnZErUnA.png)

随着我们的继续，一直写“左或右”会有点痛苦。所以我们将左右组合一起称为“任一”。不是向左就是向右。

### **制作任一对象的快捷键**

因此，下一步是重写我们的示例函数，使它们返回一个*或者*。向左表示错误，向右表示值。但是，在我们这样做之前，让我们去掉一些乏味的东西。我们将写一些小的快捷方式。第一个是名为。()的。它所做的只是返回一个新的左或右。代码可能如下所示:

[https://medium . com/media/e 9585 cc 2 a9 be 5c 136553 F2 b 9 e 4593 ad 2/href](https://medium.com/media/e9585cc2a9be5c136553f2b9e4593ad2/href)

老实说，我甚至觉得 Left.of()和 Right.of()写起来都很乏味。所以我倾向于创建更短的快捷方式，称为 left()和 right():

[https://medium . com/media/DD 69 baec 53300493 f1 b 8870 a 0 e 5a 2d 67/href](https://medium.com/media/dd69baec53300493f1b8870a0e5a2d67/href)

有了这些，我们就可以开始重写我们的应用程序功能了:

[https://medium . com/media/ad 5 ef 59873 a 69 Abe 89 b 648701 ca 177 be/href](https://medium.com/media/ad5ef59873a69abe89b648701ca177be/href)

修改后的功能与旧的没有太大区别。我们只是将返回值包装在左边或右边，这取决于我们是否发现了错误。

完成后，我们可以开始重新设计处理单行的主函数。我们首先用 right()将行字符串放入一个要么，然后映射 splitFields()来拆分它:

[https://medium . com/media/9 a2 DD 858 D5 cabc 13 B4 cf 6 B3 a93d 7871 a/href](https://medium.com/media/9a2dd858d5cabc13b4cf6b3a93d7871a/href)

这工作得很好，但是当我们用 zipRow()做同样的事情时，我们遇到了麻烦:

[https://medium . com/media/b 6076 b 2d 08 e 1c cf 20 eed 0 bfd 6 deb 15 DC/href](https://medium.com/media/b6076b2d08e1ccf20eed0bfd6deb15dc/href)

这是因为 zipRow()需要两个参数。而是我们传递的函数。map()只从。_value 属性。解决这个问题的一个方法是创建一个 zipRow()的 curried 版本。它可能看起来像这样:

[https://medium . com/media/30 e 571 A8 DAE 1508d 724 b 6051 a3 cc 14 BF/href](https://medium.com/media/30e571a8dae1508d724b6051a3cc14bf/href)

这一微小的变化使得转换 zipRow()变得更加容易，因此它可以很好地与。地图():

[https://medium . com/media/6034 ed 8 b 7 b 4930 D8 e 86099 f 068 b 7 FEC 9/href](https://medium.com/media/6034ed8b7b4930d8e86099f068b7fec9/href)

### **加入**

使用。map()来运行 splitFields()是没问题的，因为 splitFields()既不返回。但是当我们开始运行 zipRow()时，我们遇到了一个问题。调用 zipRow()会返回一个要么。所以如果我们使用。map()我们最终将一个要么放入一个要么中。如果我们再往前走，我们就会被卡住，除非我们跑。地图()在里面。地图()。这不会有什么效果的。我们需要某种方法将那些嵌套的连接在一起。所以，我们要写一个新的方法，叫做。加入():

[https://medium . com/media/e 0887 a1 b 66254 ef 18d 9 CD 43 ce 749 e 564/href](https://medium.com/media/e0887a1b66254ef18d9cd43ce749e564/href)

现在我们可以自由地解开我们的价值观:

[https://medium . com/media/7 f 49 b 6 b 32 e 6 efcc 4 ab 0585 a 27 a9 b 15 c 6/href](https://medium.com/media/7f49b6b32e6efcc4ab0585a27a9b15c6/href)

### **链条**

我们已经走得更远了。但是记得打电话。每次 join()都很烦。这种通话模式。map()和。join()很常见，所以我们将为它创建一个快捷方法。我们称之为。chain()，因为它允许我们将返回 Left 或 Right 的函数链接在一起:

[https://medium . com/media/45be 202 fc 4d a 5001 d6b 3832528407 a9b/href](https://medium.com/media/45be202fc4da5001d6b3832528407a9b/href)

回到我们的铁路轨道类比。chain()允许我们在遇到错误时切换轨道。不过用图表来展示更容易。

[![](img/417b5f0cb0a5ce49e615825bd042f177.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2RtjMSoF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyLmeLKqNon48WB0a0o4Fdg.png)

有了这些，我们的代码就更清晰了:

[https://medium . com/media/62496340 fceceaf 82 f 1908 ed 4 b 602 b 64/href](https://medium.com/media/62496340fceceaf82f1908ed4b602b64/href)

### **用价值观做某事**

我们几乎完成了对 processRow()函数的修改。但是当我们返回值的时候会发生什么呢？最终，我们想要根据我们是左撇子还是右撇子采取不同的行动。因此，我们将编写一个相应地采取不同行动的函数:

[https://medium . com/media/a 8 e 48 b5 c8 d 573 c 3d 48 abfbf 40 a6 d 4697/href](https://medium.com/media/a8e48b5c8d573c3d48abfbf40a6d4697/href)

我们欺骗并利用了左边或右边物体的内在价值。但我们会假装你没看见。我们现在能够完成我们的功能:

[https://medium . com/media/08800 e 7 cf 451 e 7 e 40 aceaac 729 a 45 E8 e 9/href](https://medium.com/media/08800e7cf451e7e40aceac729a45e8e9/href)

如果我们觉得特别聪明，我们可以用流畅的语法写出来:

[https://medium . com/media/46 CBF 2 a 13813 e 27 a2 f 59136d 9 fa 84 c 28/href](https://medium.com/media/46cbf2a13813e27a2f59136d9fa84c28/href)

两个版本都非常简洁。不是尝试…在望。并且在我们的顶层函数中没有 if 语句。如果某一行有问题，我们只在最后显示一条错误消息。请注意，在 processRow()中，我们唯一一次提到 Left 或 right 是在调用 Right()的最开始。对于其余部分，我们只使用。map()和。chain()方法来应用下一个函数。

### **Ap 和 lift**

这看起来不错，但我们还需要考虑最后一种情况。继续这个例子，让我们看看如何处理整个 CSV 数据，而不仅仅是每一行。我们需要一个或三个助手函数:

[https://medium . com/media/46091 A8 ef 5f 506 c 27973433 ec0b 40820/href](https://medium.com/media/46091a8ef5f506c27973433ec0b40820/href)

因此，我们有一个助手函数，它将 CSV 数据分成行。我们得到一个要么回来。现在，我们可以使用。map()和一些 [lodash](https://lodash.com/) 函数将标题行从数据行中分离出来。但是我们最终陷入了一个有趣的境地…

[https://medium . com/media/887372191 b 27 ceee 29778d 154 CD 1 FDD 3/href](https://medium.com/media/887372191b27ceee29778d154cd1fdd3/href)

我们已经准备好用 processRows()映射我们的头字段和数据行。但是 headerFields 和 dataRows 都被包装在一个要么。我们需要某种方法来将 processRows()转换成一个既能工作又能工作的函数。作为第一步，我们将处理以下流程:

[https://medium . com/media/fb5c 29429951 ea 316 ebc 6 f 80 f 74 f 62 b 4/href](https://medium.com/media/fb5c29429951ea316ebc6f80f74f62b4/href)

现在，有了这个，我们可以运行一个实验。我们有 headerFields，它是一个包装在数组周围的。如果我们去海德菲尔德打电话。用 processRows()在上面映射()。

[https://medium . com/media/7 ed 7d da 23 AAAA 2c C2 D4 fa 945 fa 676812 f/href](https://medium.com/media/7ed7dda23aaa2cc2d4fa945fa676812f/href)

使用。这里的 map()调用 processRows()的外部函数，而不是内部函数。换句话说，processRows()返回一个函数。因为这是。map()，我们仍然得到一个要么返回。所以我们最终得到了一个要么里面的函数。我用变量名透露了一点。funcInEither 是一个要么。它包含一个接受字符串数组并返回不同字符串数组的函数。我们需要某种方法来获取该函数，并用 dataRows 中的值调用它。要做到这一点，我们需要在我们的左右类中再添加一个方法。我们称之为。ap()因为[标准告诉我们](https://github.com/fantasyland/fantasy-land#ap-method)。记住它的方法是回忆 ap 是' apply '的缩写。它帮助我们将值应用于函数。

像往常一样，左派的方法什么也做不了。对于正确的类，变量名表明我们期望另一个包含函数:

[https://medium . com/media/f 8168d 658463585 c 9 b 081 eeeeee 712 CD 6/href](https://medium.com/media/f8168d658463585c9b081eeeee712cd6/href)

有了这些，我们就可以完成我们的主要功能了:

[https://medium . com/media/3993 a 21 f1 cbb 9d 0 ee 9 f 95d 0 cf 81870/href](https://medium.com/media/3993a21f1cbb9d0ee9f9f95d0cf81870/href)

现在，[我在](https://jrsinclair.com/articles/2018/how-to-deal-with-dirty-side-effects-in-your-pure-functional-javascript/#combiningeffects)之前提到过这个，但是我发现。ap()使用起来有点混乱。另一种思考方式是说:“我有一个函数，它通常有两个普通值。我想把它变成一个取两者之一的函数。现在我们有了。ap()，我们可以写一个函数来完成这个任务。我们称它为 liftA2()，因为这是一个标准名称。它采用一个需要两个参数的普通函数，并“提升”它以与“应用程序”一起工作。(应用程序是具有。ap()方法和一个。of()方法)。因此，liftA2()是“lift applicative，two parameters”的缩写。

因此，liftA2()可能如下所示:

[https://medium . com/media/E6 CBD 8 e F3 ebfcb 335 Fe 58 ace 8 AFC 917 a/href](https://medium.com/media/e6cbd8ef3ebfcb335fe58ace8afc917a/href)

所以，我们的顶级函数会这样使用它:

[https://medium . com/media/26 da 1c 3515222 F9 e4a 073 f 765907 CB 57/href](https://medium.com/media/26da1c3515222f9e4a073f765907cb57/href)

你可以在 CodePen 上看到整个过程。

### 真的？就这样吗？

为什么这比抛出异常要好呢？好吧，让我们先想想为什么我们喜欢异常。如果我们没有异常，我们将不得不到处写很多 If 语句。我们将永远按照“如果最后一件事有效，就继续下去，否则就处理错误”的思路编写代码。我们必须在整个代码中处理这些错误。这使得很难了解正在发生的事情。抛出异常允许我们在出错时跳出程序流。所以我们不需要写那些如果语句。我们可以专注于快乐的道路。

但是有一个问题。例外隐藏的有点太多了。当你抛出一个异常时，你把处理错误变成了其他函数的问题。但是忽略异常太容易了，让它一路冒泡到程序的顶部。这两种方法的好处在于，它允许您跳出主程序流，就像遇到异常时一样。但它是诚实的。你要么往右，要么往左。你不能假装左不是一种可能性，最终，你必须用类似于要么()的调用来提取值。

我知道这听起来很痛苦。但是看看我们写的代码(不是这两个类，而是使用它们的函数)。那里没有很多异常处理代码。事实上，几乎没有，除了在 csvToMessages()和 processRow()末尾的要么()调用。这才是重点。无论使用哪种方法，您都可以获得不会意外忘记的纯粹的错误处理。但是没有它践踏你的代码，到处添加缩进。

这并不是说你不应该使用 try…catch。有时这是工作的正确工具，这没什么。但它不是唯一的工具。使用任一种都给了我们一些 try…catch 无法比拟的优势。所以，也许什么时候可以试一试。即使一开始有点棘手，我想你会慢慢喜欢上它的。如果你尝试一下，请不要使用本教程中的实现。尝试一个历史悠久的图书馆，如[鳄鱼馆](https://evilsoft.github.io/crocks/)、[避难所](https://sanctuary.js.org/)、[民间故事馆](https://folktale.origamitower.com/)或[莫奈](https://monet.github.io/monet.js/)。它们得到了更好的维护。为了简单起见，我掩盖了一些事情。如果你真的尝试了，请给我发一条推文让我知道。

### 进一步阅读

*   弗里斯比教授的函数式编程指南，作者[布莱恩·朗斯多夫](https://twitter.com/drboolean)(以及其他人)
*   [奇幻之地说明书](https://github.com/fantasyland/fantasy-land)
*   JavaScript 中单子的实用介绍:由[雅各布·斯特罗杰夫斯基](https://twitter.com/ulfryk)撰写的
*   [神奇神秘的 JavaScript 也许是单子](https://jrsinclair.com/articles/2016/marvellously-mysterious-javascript-maybe-monad/)作者[你真正的](https://twitter.com/jrsinclair)

1.  stroustup，b，2012，[bjarne stroustup 的 C++术语表](http://www.stroustrup.com/glossary.html#Gpolymorphism)
2.  事实上，幻想世界规范定义了。ap()以一种令人困惑的方式。它使用与大多数其他语言定义它的方式相反的顺序。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *
# Java 和 JavaScript 的执行有多相似？

> 原文：<https://dev.to/alexhwoods/how-similar-is-the-execution-of-java-and-javascript-3548>

在本文中，我将比较 Java 和 JavaScript 的执行情况。我有意回避了很多关于记忆的细节，因为那是一个完全不同的话题。

# 在处理器级执行

随着每个时钟周期的流逝，处理器执行一条微指令。这些字符串的位驱动 CPU 的控制线。我们通过翻译一条指令(通常称为机器码)得到一系列微指令。处理器所有可用指令的集合称为指令集架构(ISA)，在某种意义上，它是计算机处理器的 API。

`source code -> instructions (now within processor) -> microinstructions (executed each clock cycle)`

程序中的语句可以被编译器接受并转换成机器代码(指令)。最好的例子是 c 语言。然而，不同的处理器提供不同的指令集架构。这是否意味着我们必须为每个处理器编写不同的编译器？(是的，确实如此。)这是一个问题，因为编译器为程序员做了越来越多的工作。

我们可以通过保持程序执行器中所有增加的特性不变来解决这个问题，除了处理器。如果我们对处理器进行虚拟化，并提供一个专门为执行所讨论的语言而设计的 ISA，那么当我们从一个操作系统转移到另一个操作系统时，我们现在就有了一个可以进出的组件(假设使用不同的处理器)。

# 虚拟化处理器

如上所述，这两种语言(Java 和 JavaScript)都在机器代码之上提供了一个抽象来管理这种复杂性。这种“机器代码的抽象”被称为字节码。在 Java 中，[是。类文件](https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-6.html)，对于 JavaScript，它根据所讨论的引擎而变化。要查看 V8 引擎使用的字节码(Node.js 和 Chrome 中使用的字节码)，用`--print-bytecode`标志执行它。

> 要纯粹用 V8
> (不包括 Node 的运行时)运行一个 JavaScript 程序，安装 V8 并运行
> `v8 hello.js`(其中`hello.js`是你的程序)
> 例如`v8 --print-bytecode hello.js`
> (因为看到 Node 运行时的字节码不是最好的教育目的)。

当我说“虚拟化处理器”时，我的意思是我们正在虚拟机中执行程序。我们有一些实体(JVM 解释器或点火字节码执行器)，知道如何执行他们的字节码版本。我们在这里使用的称为[进程虚拟机](https://en.wikipedia.org/wiki/Virtual_machine#Process_virtual_machines)。这与[系统虚拟机](https://en.wikipedia.org/wiki/Virtual_machine#System_virtual_machines)形成鲜明对比，后者拥有自己的客户操作系统(想想 VMWare)。进程虚拟机要轻得多，它的唯一目的是执行一个程序。明确地说，它确实进一步虚拟化了其他东西，比如内存，这意味着存在映射。为了简单起见，[映射只是一个直接映射](http://www.ittc.ku.edu/~kulkarni/teaching/EECS768/slides/chapter3.pdf)。请注意，内存已经在主机操作系统上虚拟化；分配给进程的内存块看起来是连续的，但不能保证如此。我们抽象出来的最复杂的东西是处理器和它的机器码。

例如，在 JVM 中，解释器获取一行字节码，并且知道如何执行它，而不需要进一步翻译成机器码。

在 V8 中，点火解释器以类似的方式工作。它从解析后的 [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) 中取出一条语句，将其转换成字节码，然后自己执行。

让我们更深入地了解这两者的细节。

# JVM 如何执行程序

假设我们有一些 Java 源代码。我们使用 Java 编译器(javac)将其转换成字节码(。类文件)。然后我们把它输入 JVM。

JVM 接受它，并在一些初始化步骤(类加载、字节码验证等)之后。)，字节码的语句到达解释器，解释器执行它。

随着执行的继续，如果某段代码(例如函数)已经被执行了一定次数(即它是“热的”)，JVM 执行引擎包含 JIT 优化编译器，该编译器将它转换成机器代码，允许它更快地执行。

[![jvm](img/3c1f6724616a1129f3842bf87d0b9a4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7_3BsSW---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vqpfb5u2n08uw12r4mim.png)

> 注意，这里的“JIT 编译器”是从字节码到机器码的。相反，JavaScript 的 JIT 编译是从源代码到字节码。

# V8 发动机如何执行程序

相比之下，JavaScript 是 JIT 编译的。这意味着在它被送入执行引擎之前没有预编译步骤；我们给引擎提供原始源代码。

比方说，我们有一些 JavaScript 源代码。它被解析并转换成 AST。然后输入点火系统，V8 引擎的解释器。 [Ignition](https://v8.dev/docs/ignition) 将它转换成字节码，并执行它。

就像以前一样，如果我们发现一个热函数，我们有一个优化的编译器把它变成机器码。在 V8 中，这个实体被称为[涡扇](https://v8.dev/docs/turbofan)。

去偷 Franziska Hinkelmann 的照片，从她关于这个话题的博客文章中。

[![v8](img/72a866d3826753d35578a7330ae0eeff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qWVjXvMo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mal1egmbkxhqs8t4mpbz.png)

注意——上面我谈到了流程虚拟机。V8 在这类内部细节上相当稀疏，但我确实读了足够多的资料来说服自己，他们使用了一个进程虚拟机。(见[此处](https://codeburst.io/node-js-v8-internals-an-illustrative-primer-83766e983bf6)、此处，此处)。

# Java 与 JavaScript 执行的相似之处

*   两者都在进程虚拟机内部运行。它们都有自己的“字节码”，只是机器码的抽象。那些进程虚拟机执行它们相应的字节码。
*   他们都采用相同的方法来处理“热”代码。也就是说，优化编译器使用执行过程中积累的分析信息，将字节码转换成机器码。

# Java 和 JavaScript 执行的区别

这里有一个主要的区别——JavaScript 是 JIT 编译的，而 Java 不是。因此，在 JavaScript 语言和它的执行引擎(这里是 V8)之间没有抽象。这意味着不可能使用 V8 来执行另一种语言。

另一方面，JVM 只知道`.class`文件字节码。这为其他可以编译成的语言提供了空间。类文件。输入 Scala、Groovy、Clojure 和 Kotlin。

具有讽刺意味的是，这一抽象层的存在仅仅是因为在 Java 出现的时候(早期万维网时代，浏览器支持 Java)，有必要通过网络发送字节码。如今，这根本不重要。如今，我们通过网络发送 JavaScript 源代码，浏览器使用执行引擎(如 V8)来执行它。

我发现这一点很吸引人，并且我相信这种间接性可能会导致 JVM 在很长一段时间内继续存在，因为即使我们不想使用 Java，我们也可以选择使用它。这对语言设计师来说很有吸引力，因为超过一半的工作已经为他们完成了——他们所要做的就是设计一个语言规范和一个编译成字节码的编译器。

# 结论

经过研究和写作，我现在的观点是，JVM 语言和 JavaScript 的执行方式比我最初想象的要接近得多。希望你学到了一些东西。

原贴[此处](https://www.alexhwoods.com/how-similar-is-the-execution-of-java-and-javascript)。
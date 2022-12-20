# 电话面试中最常问的 50 个问题和答案

> 原文：<https://dev.to/abhishekalbert/top-50-question-and-answer-asked-in-phone-interview--3pkc>

你好。！希望你们都做得很好。

今天我将分享我在 Kimberly Cook 的博客文章“50 大编程语言、数据结构和算法电话面试问题及答案”中读到的内容。
正如文章标题所解释的，让我们开始吧。

正如你所看到的，在过去的几年里，公司开始了一种新的面试方式，那就是电话面试，也被称为电话面试。电话面试是在编程语言工作面试中筛选候选人的最受欢迎的方式。双方都很容易衡量对方，候选人不需要去潜在雇主的所在地，面试官也不需要做任何必要的安排。为了完成电话面试并进入下一轮，你必须回答与你的工作描述相关的所有问题。

在大多数针对 Java 或 C++开发人员的电话访谈中，您不仅会发现来自相应编程语言的问题，还会发现来自其他技术的问题，例如 SQL、XML、UNIX、通用编程、面向对象编程、数据结构、算法、网络、编码和其他工作领域。由于编程工作面试的电话回合的巨大性质，你需要想出一个特殊的策略来以面试官期望的方式展示你自己。

在电话面试中回答问题时，要记住的最重要的事情之一是尽早提及关键点，并始终给出中肯的答案。由于大多数面试官喜欢涵盖很多话题来筛选候选人，他们通常喜欢重点回答，而不是废话连篇，好吧，我知道这些东西，你会有机会在面对面的面试中深入解释事情。顺便说一句，这不是硬性规定，你可以通过观察面试官对你回答的反应来了解他对你的期望。如果他问了后续问题，或者希望你说得更多，但很快就跳到下一个问题，那么就要简洁明了。

在这篇文章中，我将与你分享一些特别为电话面试量身定制的流行而有趣的编程问题。其中大多数实际上来自各种技术公司的电话会议，包括巴克莱、花旗、野村等银行，以及各种基于服务的公司，如 Infosys、TCS、CTS、Tech Mahindra 和 HCL。

正如我之前提到的，问题是从各种话题中随机选取的，但大多基于基本原则，因为这是面试官在电话面试中测试的内容。虽然这些问题主要是针对缺乏经验的开发人员，例如 2 到 5 年的开发人员，但是资深和有经验的程序员也可以在他们的面试中使用这些问题。

如果你正在认真准备工作面试，我也建议你看看 Wrox 曝光的[编程面试或者](https://aax-us-east.amazon-adsystem.com/x/c/QsaTrVd7jf2ZyulzDpU2q-YAAAFni621SgEAAAFKAS1QjBw/https://assoc-redirect.amazon.com/g/r/http://www.amazon.com/dp/1118261364/?creativeASIN=1118261364&linkCode=w61&imprToken=qPS6t.cZ8KGl0KAt6i.q2w&slotNum=0&tag=javamysqlanta-20)[破解代码面试](https://aax-us-east.amazon-adsystem.com/x/c/QsaTrVd7jf2ZyulzDpU2q-YAAAFni621SgEAAAFKAS1QjBw/https://assoc-redirect.amazon.com/g/r/http://www.amazon.com/dp/098478280X/?creativeASIN=098478280X&linkCode=w61&imprToken=qPS6t.cZ8KGl0KAt6i.q2w&slotNum=1&tag=javamysqlanta-20)，这是我找到的两本准备编程工作面试的好书。第一本书是我最喜欢的，我已经读了将近 7 年了，但它仍然很有意义，因为它很好地解释了数据结构和算法问题。

如果您是面试官，您也可以使用这些问题来快速筛选发展职位候选人。我在这里提供了简短的答案和更详细的答案。

# 电话面试的 50 大编程语言问题

以下是编程工作面试电话中的近 50 个问题。这些问题对任何程序员、开发人员、软件工程师、QA 和支持工程师都有好处，因为它们基于编程的基础，但最适合程序员和开发人员。顺便说一句，如果您是 Java 开发人员，正在为电话面试寻找 Java 问题，请查看这个列表。这个列表更加通用，适用于所有程序员，包括 Python、Ruby、Perl 和 C#开发人员。

**1。如果存储在 HashMap、二叉树和链表中，检索一个元素需要多少时间？如果你有数百万条记录，它会如何变化？**
在 HashMap 中需要 O(1)时间，在二叉树中需要 O(logN)时间，其中 N 是树中节点的数量，在链表中需要 O(n)时间，其中 N 是列表中元素的数量。如果数据结构按预期工作，数百万条记录不会影响性能，例如 HashMap 没有或相对较少的冲突，或者二叉树是平衡的。如果不是这样，那么随着记录数量的增加，它们的性能会下降。

**2。重写和重载的区别是什么？** ( [详细回答](http://javarevisited.blogspot.sg/2011/12/method-overloading-vs-method-overriding.html))

重写在运行时解决，重载在编译时解决。此外，重写和重载的规则是不同的，例如在 Java 中，重载方法的方法签名必须不同于原始方法，但在重写的情况下，它必须与重写方法完全相同。

**3。分叉一个进程和生成一个线程有什么区别？**

当您派生一个进程时，新进程将运行与父进程相同的代码，但在不同的内存空间中，但当您在现有进程中生成一个新线程时，它只是创建了另一个独立的执行路径，但共享相同的内存空间。

**4。什么是临界区？** ( [答](http://javarevisited.blogspot.sg/2012/05/counting-semaphore-example-in-java-5.html))

临界区是代码的一部分，非常重要，在多线程中必须由任何线程专门修改。信号量或互斥量用于保护临界区。在 Java 中，可以使用 synchronized 关键字或 ReentrantLock 来保护临界区。

**5。值类型和引用类型有什么区别？** ( [答](http://javarevisited.blogspot.sg/2012/12/does-java-pass-by-value-or-pass-by-reference.html))

值类型是更优化的类型，并且总是不可变的，例如 Java 中的原始 int、long、double 和 float，而引用类型指向可变或不可变的对象。你也可以说值类型指向一个值，而引用类型指向一个对象。

**6。什么是进程中的堆和栈？** ( [详细回答](http://javarevisited.blogspot.sg/2012/12/does-java-pass-by-value-or-pass-by-reference.html))

它们是同一个进程中两个独立的内存区域。说到 Java，栈是用来存储原始值和对象的引用类型的，但是实际的对象总是在堆中创建的。堆和栈的一个关键区别是堆内存由所有线程共享，但是每个线程都有自己的栈

**7。什么是修订/版本控制？** ( [答](http://javarevisited.blogspot.sg/2013/04/difference-between-trunk-tags-and-branch-svn-cvs-git-scm-subversion.html))

版本控制是用于存储代码和管理代码库版本的软件，如 SVN、CVS、Git、Perforce 和 ClearCase。在比较代码、审查代码和从以前的稳定版本创建构建时，它们非常有效。所有专业开发都使用某种修订或版本控制工具，没有它们，你就不能有效地管理代码，尤其是如果 20 个开发人员同时在同一个代码库上工作。版本控制工具在保持代码库一致性和解决代码冲突方面起着非常重要的作用。

**8。什么是强类型编程语言？** ( [答](http://javarevisited.blogspot.sg/2013/11/java-vs-python-which-programming-laungage-to-learn-first.html))

在强类型语言中，编译器确保类型的正确性，例如，你不能将数字存储在字符串中，反之亦然。Java 是一种强类型语言，这就是为什么你有不同的数据类型，比如 int，float，String，char，boolean 等等。您只能在各自的类型中存储兼容的值。另一方面，弱类型语言在编译时不强制进行类型检查，它们基于上下文树值。Python 和 Perl 是弱类型编程语言两个流行的例子，在这两种语言中，可以用数字类型存储数字字符串。

**9。你能描述有效的和格式良好的 XML 之间的区别吗？**

一个结构良好的 XML 应该有根元素，所有的标签都被正确地关闭，属性被正确地定义，它们的值也被正确地引用。另一方面，有效的 XML 是可以根据 XSD 文件或模式进行验证的 XML。因此，XML 可能是格式良好的，但却是无效的，因为它们包含模式不允许的标记。

10。DOM 和 SAX 解析器有什么区别？ ( [详细回答)](http://java67.blogspot.sg/2012/09/dom-vs-sax-parser-in-java-xml-parsing.html)

DOM 解析器是一个内存解析器，所以它在内存中加载整个 XML 文件，并创建一个 DOM 树来解析。SAX 解析器是一个基于事件的解析器，所以它基于收到的事件解析 XML 文档，例如开始标记、结束标记、属性的开始或结束。由于它们的工作方法，DOM 解析器不适合大型 XML 文件，因为它们将占用大量内存空间，并且您的进程可能会耗尽内存，SAX 应该用于解析大型文件。对于小文件，DOM 通常比 SAX 快得多。

**11。线程和进程的关系是什么？** ( [详细回答](http://java67.blogspot.sg/2012/12/what-is-difference-between-thread-vs-process-java.html))

一个进程可以有多个线程，但是一个线程总是属于一个进程。两个进程不能共享内存空间，除非它们有目的地通过共享内存进行进程间通信，但是来自同一个进程的两个线程总是共享同一个内存。

**12。不可变类是什么意思？** ( [详细回答](http://javarevisited.blogspot.sg/2013/03/how-to-create-immutable-class-object-java-example-tutorial.html))

如果一个类的状态一旦被创建就不能被改变，那么这个类就是不可变的，例如，Java 中的 String 就是不可变的。一旦你创建了一个字符串，比如“Java”，你就不能改变它内容。此字符串中的任何修改(例如转换为大写字母、与另一个字符串连接)都会产生新的对象。不可变对象对于并发编程非常有用，因为它们可以在多个线程之间共享，而不用担心同步问题。事实上，函数式编程的整个模型都是建立在不可变对象之上的。

13。为什么您会想要创建一个模拟对象呢？ ( [答](http://javarevisited.blogspot.sg/2014/04/difference-between-stub-and-mock-object-java-junit.html))

模拟对象对于测试软件中的单个单元非常有用，事实上，存根和模拟是创建自动化单元测试的强大工具。假设你写了一个程序来显示货币兑换率，但是你没有一个 URL 来连接，现在如果你想测试你的代码，你可以使用模拟对象。在 Java 世界中，有很多框架可以为你创建强大的模拟对象，例如 Mockito 和 PowerMock。

**14。什么是 SQL 注入？**

SQL 注入是一个安全漏洞，它允许入侵者从系统中窃取数据。任何接受用户输入并创建 SQL 查询而不验证或净化输入的系统都容易受到 SQL 注入的攻击。在这种系统中，入侵者可以注入 SQL 代码而不是数据来检索比预期更多的数据。利用此漏洞窃取用户 id、密码和个人详细信息等敏感信息的例子很多。在 Java 中，可以通过使用预处理语句来避免 SQL 注入。

15。SQL 中的内连接和左连接有什么区别？ ( [答](http://javarevisited.blogspot.sg/2013/05/difference-between-left-and-right-outer-join-sql-mysql.html))

在 SQL 中，主要有两种类型的连接，内部连接和外部连接。同样，外部联接可以有两种类型:左外部联接和右外部联接。内连接和左连接的主要区别在于，前者只选择两个表中匹配的记录，而在左连接中，除了匹配两个表中的记录之外，还选择左表中的所有记录。始终注意包含“all”的查询，它们通常需要左连接，例如编写 SQL 查询来查找所有部门和员工。如果使用 inner join 来解决这个查询，就会错过无人工作的空部门。

16。MVC 中的 V 代表什么，它意味着什么？ ( [答](http://javarevisited.blogspot.co.uk/2011/11/struts-interview-questions-answer-j2ee.html))

v 代表 MVC 模式中的视图。视图是用户看到的内容，例如网页。这是一个非常重要的 web 开发设计模式，它基于关注点的分离，这样每个区域都可以被修改而不会影响到其他区域。在 Java 世界中，有许多开源框架提供了 MVC 模式的实现，例如 Struts 2 和 Spring MVC。顺便说一下，M 代表型号，C 代表控制器。模式是实际的业务对象，例如用户、员工、订单；而控制器用于将请求路由到正确的处理器。

**17。类和对象的区别是什么？** ( [详细回答](http://javarevisited.blogspot.sg/2012/12/difference-between-class-and-object-in-oops-java.html))

类是在其上创建对象的蓝图。一个类有代码和行为，但是一个对象既有状态又有行为。如果不创建表示对象结构的类，就无法创建对象。该类还用于映射内存中的对象，在 Java 中，JVM 会为您完成这项工作。

18。什么是松耦合？

松耦合是软件的一个可取的品质，它允许软件的一部分进行修改而不影响软件的另一部分。例如，在一个松散耦合的软件中，UI 布局的改变不应该影响后端的类结构。

**19。组合、聚合和关联之间的区别是什么？** ( [详细回答](http://javarevisited.blogspot.sg/2014/02/ifference-between-association-vs-composition-vs-aggregation.html))

关联是指两个对象彼此相关，但可以彼此独立存在，组合是一种关联形式，其中一个对象由多个对象组成，但它们只一起存在，例如人体是器官的组合，单个器官不能存活，它们只在体内有用。聚集是物体的集合，例如城市是市民的聚集。

20。接口和抽象类的区别是什么？ ( [详细回答](http://javarevisited.blogspot.sg/2013/05/difference-between-abstract-class-vs-interface-java-when-prefer-over-design-oops.html))

这是所有编程面试中最经典的问题。接口是最纯粹的抽象形式，没有任何具体的东西，而抽象类是一些抽象和具体事物的组合。不同的语言会有不同的区别，例如在 Java 中，你可以扩展多个接口，但是你只能在抽象类上扩展。更全面的讨论见详细回答。

**21。什么是单元测试？** ( [答](http://javarevisited.blogspot.sg/2013/03/how-to-write-unit-test-in-java-eclipse-netbeans-example-run.html))

单元测试是一种测试单个单元功能的方法，而不是测试整个应用程序。有很多工具可以在不同的编程语言中进行单元测试，比如在 Java 中，你可以使用 JUnit 或者 TestNG 来编写单元测试。它通常在构建过程中自动运行，或者在像 Jenkins 这样的连续环境中运行。

**22。您能描述一下在应用程序上线之前可能会对其执行的三种不同的测试吗？**

单元测试、集成测试和冒烟测试。单元测试用于测试单个单元，以验证它们是否按预期工作，集成测试用于验证单独测试的模块是否可以一起工作，冒烟测试是测试软件最常见功能是否正常工作的一种方法，例如，在航班预订网站上，您应该可以预订、取消或更改航班。

**23。迭代和递归有什么区别？** ( [详细回答](http://javarevisited.blogspot.sg/2012/12/recursion-in-java-with-example-programming.html))

迭代使用循环一次又一次地执行相同的步骤，而递归调用函数本身来执行重复的任务。很多时候，递归可以清晰简洁地解决复杂的问题，例如汉诺塔、反转链表或反转字符串本身。递归的一个缺点是深度，因为递归将中间结果存储在堆栈中，你只能到达某一深度，之后你的程序将因 StackOverFlowError 而死亡，这就是为什么在生产代码中迭代比递归更受欢迎。

**24。&和& &符有什么区别？** ( [详细回答](http://javarevisited.blogspot.sg/2015/01/difference-between-bitwsie-and-logical.html))

&是位运算符，而&&是逻辑运算符。&和&&之间的一个区别是，按位运算符(&)可以应用于整数和布尔，但逻辑运算符(&&)只能应用于布尔变量。当你做 a & b 时，AND 运算符应用于两个整数的每一位，而在 a && b 的情况下，第二个参数可能会也可能不会被计算，这就是为什么它也被称为短路运算符，至少在 Java 中是这样。我喜欢这个问题，经常问大三或开发人员和大学毕业生。

**25。1 异或 1 的结果是什么？**

答案是零，因为如果两个操作数不同，XOR 返回 1，如果两个操作数相同，则返回 0，例如，0 XOR 0 也是零，但 0 XOR 1 或 1 XOR 0 始终是 1。

**26。如何得到一个整数的最后一位数字？** ( [详细回答](http://javarevisited.blogspot.sg/2012/04/java-program-to-reverse-number-example.html))

通过使用模数运算符，number % 10 将返回数字的最后一位，例如，2345%10 将返回 5，567%10 将返回 7。类似地，除法运算符可用于去除数字的最后一位，例如 2345/10 将得出 234，567/10 将得出 56。这是一个重要的技巧，知道并使用它来解决像数字回文或反转数字这样的问题。

**27。什么是测试驱动开发？**

测试驱动是一种流行的开发方法，在这种方法中，测试是在编写任何功能代码之前编写的。事实上，测试驱动程序的结构。纯粹主义者从来不会在没有编写测试的情况下编写一行应用程序代码。它极大地提高了代码质量，这通常归功于 rockstar 开发人员的素质。

**28。什么是利斯科夫替代原理？** ( [详细回答](http://javarevisited.blogspot.sg/2012/03/10-object-oriented-design-principles.html))

利斯科夫替代原则是鲍勃大叔作为固体设计原则推出的五大原则之一。这是固体中的 L。Liskov 替换原则断言，每个子类型都应该能够作为父类型的代理。例如，如果一个方法除了父类的对象之外，那么如果你传递一个子类的对象，它应该像预期的那样工作。任何不能代替其父类的类都违反 LSP 或 Liskov 替换原则。这实际上是一个很难回答的问题，如果你这样做了，你最终会给面试官留下一个好印象。

**29。开闭式设计原理是什么？** ( [答](http://javarevisited.blogspot.sg/2011/11/great-example-of-open-closed-design.html))

Open closed 是 SOLID 的另一个原则，它主张系统应该对扩展开放，但对修改关闭。这意味着如果在一个稳定的系统中需要一个新的功能，那么你的经过测试的代码不应该被修改，新的功能应该通过添加新的类来提供。

三十岁。二叉树和二叉查找树有什么区别？

二叉查找树是有序二叉树，其中左树中所有节点的值小于或等于节点，而右子树中所有节点的值大于或等于节点(例如根)。这是一个重要的数据结构，可以用来表示一个有序的结构。

31。可以举一个递归算法的实际例子吗？ ( [例](http://javarevisited.blogspot.sg/2014/08/quicksort-sorting-algorithm-in-java-in-place-example.html))

递归算法有很多适合的地方，例如与二进制和链表相关的算法。递归算法的几个例子是反转字符串和计算斐波那契数列。其他示例包括反转链表、树遍历和快速排序算法。

32。一个算法的时间复杂度是多少？

时间复杂度指定时间与输入的比率。它显示了对于给定数量的输入，完成一个算法需要多少时间。这是一个近似值，但足以让您知道，如果输入数量从 1000 万增加到 1000 万，您的算法会如何执行？

33。链表和数组之间有什么重要的区别？ ( [详细回答](http://javarevisited.blogspot.sg/2013/07/difference-between-array-and-linked-list-java.html))

链表和数组是编程世界中最重要的两种数据结构。它们之间最大的区别是数组把它的元素存储在连续的位置，而链表把它的数据存储在内存的任何地方。这给了链表极大的灵活性来扩展自己，因为内存总是分散的。你总是有可能无法创建一个数组来存储 1M 的整数，但可以通过使用链表来实现，因为空间是可用的，但不是连续的块。所有其他的差异都是这个事实的结果。例如，如果知道索引，可以用 O(1)时间搜索数组中的元素，但在链表中搜索需要 O(n)时间。更多的差异见详细回答。

34。解决哈希表中冲突的几种方法是什么？

线性探测、双重散列和链接。在线性探测中，如果桶已经被占用，那么函数线性检查下一个桶，直到找到一个空桶，而在链接中，多个元素被存储在相同的桶位置。

35。什么是正则表达式？ ( [答](http://java67.blogspot.sg/2014/01/java-regular-expression-to-check-numbers-in-String.html))

正则表达式是对文本数据执行模式匹配的一种方式。这是一个非常强大的工具，可以用来查找某个东西，比如一个长字符串中的某个字符，比如查找一本书是否包含某个单词。几乎所有主流编程语言都支持正则表达式，但 Perl 以其巨大的能力而闻名。Java 也支持使用 java.util.regex 包的类 Perl 正则表达式。您可以使用正则表达式来检查电子邮件是否有效，电话号码是否有效，邮政编码是否有效，甚至 SSN 号码是否有效。正则表达式的一个最简单的例子是检查一个字符串是否是一个数字。

36。什么是无状态系统？

无状态系统是不维护任何内部状态的系统。这样一个系统在任何时间点都会对相同的输入产生相同的输出。编写和优化无状态系统总是更容易，所以如果可能的话，您应该一直努力去做。

37。编写 SQL 查询在 employee 表中查找第二高的薪水？ ( [解](http://java67.blogspot.sg/2015/01/second-highest-salary-in-mysql-and-sql-server.html))

这是 SQL 面试中的经典问题之一，这是一个很老的问题，但仍然很有趣，有很多后续问题可以用来检查候选人的知识深度。通过使用相关和非相关子查询，可以找到第二高的薪金。如果你使用的是 SQL Server 或 MySQL，如果面试官允许，你也可以使用 TOP 或 LIMIT 这样的关键字。找到第二高工资的最简单方法如下:

**从薪金不在的员工中选择最大(薪金)**

该查询首先查找最大薪金，然后将其从列表中排除，并再次查找最大薪金。显然第二次，这将是第二高的工资。

38。您能描述一下相关子查询和非相关子查询之间的区别吗？ ( [答](http://javarevisited.blogspot.sg/2012/07/subquery-example-in-sql-correlated-vs.html))

在相关子查询中，内部查询依赖于外部查询，并针对外部查询中的每一行执行。而非相关子查询不依赖于外部查询，可以独立执行。由于这个原因，前者慢，后者快。顺便说一下，相关子查询有一些很好的应用，其中之一是在雇员表中找到第 n 个最高的薪水，就像在前面的 SQL 问题中看到的一样。

39。不使用算术运算符，如何发现一个数是否是 2 的幂？ ( [解](http://javarevisited.blogspot.sg/2013/05/how-to-check-if-integer-number-is-power-of-two-example.html))

假设当你听到不允许使用算术运算符的限制时，这是一个使用按位运算符的问题。如果没有这种限制，那么您可以通过使用模和除法运算符轻松检查一个数是否是 2 的幂。通过使用按位运算符，有一个很好的技巧可以做到这一点。你可以用下面的代码来检查一个数是否是 2 的幂

public static boolean powerOfTwo(int x){
return(x&(x-1))= = 0；
}

x & (x-1)是一个很好的技巧，如果它是开着的话，可以把右边最多的一位转换为零，我是从黑客快乐书上得知的。

40。如何在 UNIX 上找到一个正在运行的 Java 进程？ ( [命令](http://javarevisited.blogspot.sg/2011/06/10-examples-of-grep-command-in-unix-and.html))

你可以使用' ps '和' grep '命令的组合来查找 UNIX 机器上运行的任何进程。假设您的 Java 进程有一个名称或任何文本，您可以使用下面的命令进行匹配。

PS-ef \ grep“洗车文件夹”

ps -e 将列出每个进程，即来自所有用户的进程，而不仅仅是您，ps -f 将为您提供包括 PID 在内的所有详细信息，如果您想进一步调查或想使用 kill 命令终止该进程，将需要这些信息。

41。如何在 UNIX 中找到大文件，例如 1GB 以上的文件？ ( [命令](http://javarevisited.blogspot.sg/2011/03/10-find-command-in-unix-examples-basic.html))

使用 find 命令可以很容易地找到大文件，因为它提供了一个根据文件大小搜索文件的选项。如果您的文件系统已满，并且 Java 进程因没有更多空间而崩溃，请使用此选项。此命令将列出所有大于 1GB 的文件。您可以轻松调整大小，例如，要查找所有超过 100 MB 的文件，只需使用+100M。

找到。-f 型尺寸+1G 型印刷

**42。shell 脚本是什么？**

shell 脚本是一组带有一些编程结构的 shell 命令，例如 if 和 for 循环，它允许您自动执行一些重复的任务。例如，您可以为日志文件的日常清理编写一个 shell 脚本，用于备份数据以供历史使用，以及用于其他日常工作、发布和监控。

这些都在电话面试的编程问题列表中。你可能已经注意到只有 42 个问题，但是标题提到了大约 50 个问题，剩下的 8 个问题在哪里？我不是分享 8 个问题，而是与你们分享 8 篇文章，你们可以从中找到剩余的编程问题。给你:

程序员的 20 个字符串编码问题([此处阅读](http://javarevisited.blogspot.sg/2015/01/top-20-string-coding-interview-question-programming-interview.html))

软件开发人员的 15 个数据结构和算法问题([见此处](http://javarevisited.blogspot.sg/2013/03/top-15-data-structures-algorithm-interview-questions-answers-java-programming.html))

每个开发人员都应该知道的 10 个面试问题([阅读更多](http://javarevisited.blogspot.sg/2014/09/10-questions-to-make-programming-interviews-cheaper.html))

20 个核心 Java 问题，来自有 2-3 年经验的程序员([查看此处](http://java67.blogspot.sg/2012/10/java-interview-questions-for-2-to-3-4-years-experienced.html))

21 SQL 查询面试问题及答案([阅读更多](http://java67.blogspot.sg/2013/04/10-frequently-asked-sql-query-interview-questions-answers-database.html))

Java 程序员的 23 个棘手问题([在此阅读](http://java67.blogspot.sg/2012/09/top-10-tricky-java-interview-questions-answers.html)

程序员的 10 个 XML 面试问题([见此处](http://javarevisited.blogspot.sg/2013/01/10-xml-interview-questions-and-answers.html))

来自 Java 访谈的 50 个多线程和并发问题([点击此处](http://javarevisited.blogspot.sg/2014/07/top-50-java-multithreading-interview-questions-answers.html))

来自编程面试的 20 个软件设计问题([阅读更多](http://javarevisited.blogspot.sg/2012/06/20-design-pattern-and-software-design.html)

18 个 Java 设计模式面试问题。([看这里](http://java67.blogspot.sg/2012/09/top-10-java-design-pattern-interview-question-answer.html))

嗯，这是一个很长的列表。这就是这篇文章的内容。下一集再见，谢谢你的阅读。

祝你好运，编码愉快。

来源::[滚刀](http://houseofbots.com/news-detail/4238-1-top-50-programming-language-data-structure-algorithms-phone-interview-questions-with-answers?fbclid=IwAR1vhGYMf3SNIUZAxlU95tY7EiufIjgits_8LXM0d_ExpVvEzqEzhy4R6gU)
# 最酷的编程语言特性

> 原文：<https://dev.to/renegadecoder94/the-coolest-programming-language-features-1953>

随着[样本程序集合](https://github.com/TheRenegadeCoder/sample-programs)的增长，我经常喜欢反思我所学到的东西。特别是，我喜欢比较和对比语言及其特征。在我自己玩过 100 多种语言之后，我想我应该分享一下我个人列出的最酷的编程语言特性。

现在，通常这些类型的文章被设计成点击诱饵。例如，我可以很容易地将这篇文章命名为 10 大最酷的编程语言特性——公平地说，我考虑过它——但是，我觉得这篇文章更适合作为一个动态文档。换句话说，随着我对编程语言了解的越来越多，我计划做一些补充和修改。

所以，确保你每隔一段时间就回来看看，别忘了分享一下。无论如何，让我们开门见山吧。

## 功能列表

为了方便起见，我提供了每个酷功能的链接列表。

*   [扩展方法](#extension-methods)
*   [宏](#macros)
*   [自动属性](#automatic-properties)
*   [可选链接](#optional-chaining)
*   [λ表达式](#lambda-expressions)
*   [渐进打字](#gradual-typing)
*   [不变性](#immutability)
*   [多次调度](#multiple-dispatch)
*   [解构](#destructuring)
*   [在线测试](#inline-testing)
*   [在线汇编器](#inline-assemblers)

在接下来的章节中，您会发现越来越多的酷编程特性供您阅读。当你完成浏览后，请在评论中分享你的一些最爱。

### 扩展方法

我有机会探索的最酷的编程语言特性之一是扩展方法。当我第一次听说扩展方法时，我正在 Kotlin 中实现 [Hello World。当然，对于这样一个微不足道的程序来说，它们是完全不必要的，但是我确实有机会了解它们的存在。](https://therenegadecoder.com/code/hello-world-in-kotlin/)

如果你不确定什么是扩展方法，它们允许我们在不直接扩展类的情况下将方法附加到现有的类上。例如，假设我们真的很喜欢 Java 中的 String 类，但是我们认为这个类可以从一个突变方法中受益。目前，我们创建变异方法的唯一方法是创建我们自己的类，它扩展了 String:

```
public class StringPlusMutation extends String {
  public String mutate() {
    // Add mutation code
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

同时，在 Kotlin 中，我们所要做的就是编写一个直接扩展 String 类的方法:

```
fun String.mutate():
  // Add mutation code 
```

Enter fullscreen mode Exit fullscreen mode

现在，任何时候我们创建一个字符串，我们都可以对它调用 mutate，就好像 mutate 是 String 中原始公共方法集的一部分一样。

当然，我应该提到扩展方法有其缺点。例如，使用我们上面的例子，如果 String API 获得了一个 mutate 方法，会发生什么呢？这可能是一个棘手的问题，但可以肯定地说，这种情况很少发生。

不管怎样，我个人只会考虑使用扩展方法进行快速原型开发。当你在评论中使用它们时，请告诉我。

### 宏

我遇到的另一个最酷的编程语言特性是宏。当我第一次发现宏时，我正在玩 Rust 中的 [Hello World。毕竟，rust 中的打印功能是以宏的形式实现的。](https://therenegadecoder.com/code/hello-world-in-rust/)

对于那些不知道的人来说，宏实际上是元编程的一个特性。更具体地说，宏允许我们通过向[抽象语法树](https://ruslanspivak.com/lsbasi-part7/)添加规则来直接修改语言。这些规则是通过模式匹配生成的。

也就是说，上面的解释相当密集，所以让我们来看看 Rust 中的一个例子:

```
macro_rules! print {
    ($($arg:tt)*) => ($crate::io::_print(format_args!($($arg)*)));
} 
```

Enter fullscreen mode Exit fullscreen mode

这条规则直接取自 Rust 源代码[。正如我们所看到的，print 宏有一个模式匹配 case，它基本上接受任意数量的参数，并试图在打印前对它们进行格式化。](https://doc.rust-lang.org/src/std/macros.rs.html#119-121)

如果模式匹配语法令人困惑，那么学习一些关于[正则表达式](http://web.mit.edu/6.005/www/fa15/classes/17-regex-grammars/)的知识可能是个好主意。正则表达式语法类似于模式匹配语法。

当然，正如您可能看到的，宏是一个极其复杂的特性。事实上，它们通常很难编写，同样也很难调试。这就是为什么，即使在 Rust 的文档中，[宏也被认为是最后的手段](https://doc.rust-lang.org/book/ch19-06-macros.html)的一个特性。

### 自动属性

除了宏和扩展方法，最酷的编程语言特性之一是自动属性。我第一次了解自动属性是在我发现它们是 C#中的一点语法糖的时候。从那以后，我在 C# 的 [Hello World 中写了一点关于他们的内容。](https://therenegadecoder.com/code/hello-world-in-c-sharp/)

基本上，自动属性只是面向对象编程语言中 getters 和 setters 的简写。例如，假设我们有一个 Person 类，我们希望这个类有一些 name 字段。毕竟人是有名字的，所以给类这个字段是有意义的。让我们继续用 Java 实现它:

```
public class Person {
  private String name;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们希望能够设置名称，我们可能需要编写一个公共方法来更新私有名称字段。通俗地说，我们称这些方法为 setters，因为它们设置了一个对象的属性。然而，官方称它们为 [mutator 方法](https://en.wikipedia.org/wiki/Mutator_method)。

在 Java 中，下面创建了一个 mutator 方法:

```
public setName(String name) {
  this.name = name;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经有了六行代码，但是我们甚至还不能请求这个人的名字。为此，我们编写了一个 getter 或 accessor 方法:

```
public getName() {
  return this.name;
} 
```

Enter fullscreen mode Exit fullscreen mode

在具有自动属性的语言中，我们可以完全去掉这六行[样板代码](https://en.wikipedia.org/wiki/Boilerplate_code)。例如，在 C#中，下面的类与我们刚刚在 Java 中创建的类相同:

```
public class Person
{
  public string Name { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了自动属性，我们可以将六行代码减少为一行，用于我们想要公开的每个字段——太棒了！

### 可选链接

最酷的编程语言特性列表中的下一个是可选链接。我第一次发现可选链接是在我用 Swift 写 [Hello World 的时候。当然，这个特性对于实现 Hello World 并不十分有用，但它仍然是一个值得探索的有趣概念。](https://therenegadecoder.com/code/hello-world-in-swift/)

在我真正解释可选链接之前，我们必须了解什么是可选值。在 Swift 中，变量不能为空。换句话说，变量不能存储零值，至少不能直接存储。这是一个很好的特性，因为我们可以假设所有的变量都包含一些值。

当然，有时变量需要为零。幸运的是，Swift 通过名为 optionals 的打包功能提供了这一功能。可选件允许用户将一个值包装在一个容器中，该容器可以被打开以显示值或零:

```
var printString: String?
printString = "Hello, World!"
print(printString!) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们声明了一个可选的字符串，并赋予它一个值“Hello，World！”因为我们知道变量存储一个字符串，所以我们可以无条件地打开这个值并打印它。当然，无条件解包通常是不好的做法，所以我展示它只是为了展示可选性。

无论如何，可选链接采用了可选性的概念，并将其应用于方法调用和字段。例如，假设我们有一些长长的方法调用链:

```
important_char = commandline_input.split('-').get(5).charAt(7) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们接受一些命令行输入，并用连字符将其分割。然后，我们抓住第五个令牌，抽出第七个字符。如果在任何时候，这些方法调用之一失败，我们的程序将崩溃。

使用可选的链接，我们实际上可以在链中的任何一点捕捉 NIL 返回值并优雅地失败。我们得到的不是崩溃，而是 NIL 的 important_char 值。现在，这比处理末日金字塔更令人向往。

### λ表达式

如果不谈论 lambda 表达式，我们很可能无法列出最酷的编程语言特性。公平地说，lambda 表达式并不是一个新概念(参见:[Lisp 中的 Hello World](https://therenegadecoder.com/code/hello-world-in-lisp/))。事实上，它们比计算机还要古老。也就是说，lambda 表达式继续出现在现代语言中——甚至作为 Java 等成熟语言中的新特性。

说实话，我第一次听说 lambda 表达式大概是三四年前，当时我在教 Java。那时候，我并不真正知道 lambda 表达式是什么，而且当时我对它们几乎不感兴趣。

然而，几年后，我开始玩 Python，它有大量利用 lambda 表达式的开源库。所以，在某些时候，我不得不使用它们。

如果你从未听说过 lambda 表达式，你可能听说过匿名函数。为了简单起见，它们是同义词。细微的区别是，lambdas 可以作为数据使用。更具体地说，我们可以将 lambda 表达式打包到一个变量中，并将其作为数据进行操作:

```
increment = lambda x: x + 1
increment(5)  # Returns 6 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们实际上创建了一个函数，将它存储在一个变量中，并像调用其他函数一样调用它。事实上，我们甚至可以让一个函数返回一个函数，[从而动态生成函数](https://docs.python.org/3.5/tutorial/controlflow.html#lambda-expressions) :

```
def make_incrementor(n):
  return lambda x: x + n
addFive = make_incrementor(5)
addFive(10)  # Returns 15 
```

Enter fullscreen mode Exit fullscreen mode

现在，那很酷！

### 渐进打字

如果你对编码有所了解，你可能熟悉两种主要的类型:静态和动态。当然，我们不要将这些术语与显式和隐式类型化甚至强类型化和弱类型化混淆。所有三对术语都有不同的含义。

对我来说，最酷的编程语言特性之一是静态和动态类型之间的交集，称为渐进类型。基本上，渐进式键入是一种语言特性，它允许用户准确地决定何时需要静态键入，否则，就假设动态键入。

在许多支持逐步键入的语言中，这一特性通过类型注释表现出来。类型注释与您通常在显式类型语言中看到的非常相似，但是注释是可选的:

```
def divide(dividend: float, divisor: float) -> float:
  return dividend / divisor 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们有一个完全带注释的 Python 函数，它指出了预期的输入和输出类型。然而，我们可以轻松地删除注释:

```
def divide(dividend, divisor):
  return dividend / divisor 
```

Enter fullscreen mode Exit fullscreen mode

现在，我相信在这两种情况下，没有什么可以阻止我们向这个函数传递任何东西。然而，第一个选项为静态类型检查提供了机会，它可以集成到 ide 和其他静态分析工具中。我觉得这是双赢。

虽然我选择使用 Python 作为例子，但我第一次遇到渐进式输入是在我为 Hack 文章中的 [Hello World 进行研究的时候。显然，脸书真的想改进 PHP 打字系统。](https://therenegadecoder.com/code/hello-world-in-hack/)

### 永恒性

另一个最酷的编程语言特性是不变性。当我第一次发现不变性时，我实际上是在一门关于高级语言的课程中。在这门课中，我们有一个任务，我们必须比较和评价大约 10 种不同语言的特征。在这些特性中，其中之一是[值语义](https://akrzemi1.wordpress.com/2012/02/03/value-semantics/)，这是一个暗示不变性的特性。

当然，什么是不变性？事实证明，不变性描述的是变量——或者更确切地说是常量——在创建后不能改变。通常，不变性在许多语言的字符串中表现出来。这就是为什么在循环中使用串联通常被认为是一个坏主意:

```
my_string = ""
for i in range(10):
  my_string += str(i)  # Generates a new string every iteration print(my_string)  # Prints "0123456789" 
```

Enter fullscreen mode Exit fullscreen mode

事实上，你可能会注意到，正是因为这个原因，我从来不在我的 [Reverse 字符串中使用串联。当然，语言开发人员知道典型的陷阱，所以他们在优化我们的错误方面做得很好。](https://sample-programs.therenegadecoder.com/projects/reverse-a-string/)

无论如何，在我在 Elm 中玩了 [Hello World 之前，我从未真正见过一种语言在字符串之外实现不变性。在 Elm 中，变量是不可变的，就像在数学中一样，所以下面的代码片段抛出了一个错误:](https://therenegadecoder.com/code/hello-world-in-elm/) 

```
a = 2
a = a - 5 
```

Enter fullscreen mode Exit fullscreen mode

在这里，这个问题是一个递归问题。首先，我们定义`a`。然后，我们试图将`a`重新定义为其自身的函数。对于我们这些通常使用命令式语言的人来说，我们甚至对这种语法不屑一顾。但是对于一个数学家来说，第二个方程有很大的问题。

当然，不变性的美妙之处在于能够在价值被创造出来之后信任它。在任何时候你都不必担心它会在你背后改变。

### 多重分派

像 lambda 表达式一样，多重分派并不是一个新的编程语言特性，但却是最酷的特性之一。这是因为，它允许我们做有趣的事情，比如在一个对象层次结构中有相同方法的不同实现。

最近，当我在 Julia 的文章中写 [Hello World 时，我想起了这个特性。显然，Julia 是一种类似 Python 和 R 的数值分析语言，而且 Julia 支持通用编程。然而，与 Python 不同，Julia 支持多个分派。](https://therenegadecoder.com/code/hello-world-in-julia/)

在这一点上，我可能应该区分单个和多个分派。单一分派是一种编程语言特性，它允许用户在对象层次结构中以不同的方式定义相同的方法。例如，考虑以下情况:

```
pikachu.tackle(charmander);
charmander.tackle(pikachu); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们假设在某个神奇宝贝层次结构中有两个对象，皮卡丘和查尔曼德。这两个对象都覆盖了一些通用的处理方法。当我们运行这段代码时，JVM 能够分派适当的处理方法。

当然，在单一调度语言中事情会变得很糟糕，因为方法选择对于调用者来说是动态的。至于参数，我们依赖静态类型。

所以，假设 charmander 被实例化为一个通用的神奇宝贝。使用单一派遣，皮卡丘将使用通用的神奇宝贝阻截方法，而不是 charmander 专用的方法。因此，皮卡丘可能会错过铲球。

对于多重分派，这几乎不是问题，因为方法选择过程发生在运行时。自然是发出适当的铲球方法，皮卡丘落地铲球。

如果我糟蹋了这个解释，请在评论中告诉我。为了更好的解释，我推荐 Eli Bendersky 的文章，标题是[多种语言的多重派遣指南](https://eli.thegreenplace.net/2016/a-polyglots-guide-to-multiple-dispatch/)。

### 解构

记得我提过模式匹配吗？析构——也称为可迭代解包——是另一种形式的模式匹配，用于从数据集合中提取数据。看看下面这个 python 的例子:

```
start, *_ = [1, 4, 3, 8]
print(start)  # Prints 1 print(_)  # Prints [4, 3, 8] 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们能够提取列表的第一个元素，忽略其余的元素。同样，我们可以轻松地提取列表的最后一个元素:

```
*_, end = ["red", "blue", "green"]
print(end)  # Prints "green" 
```

Enter fullscreen mode Exit fullscreen mode

事实上，通过模式匹配，我们可以从数据集中提取我们想要的任何东西，只要我们知道它的结构:

```
start, *_, (last_word_first_letter, *_) = ["Hi", "How", "are", "you?"]
print(last_word_first_letter)  # Prints "y" 
```

Enter fullscreen mode Exit fullscreen mode

现在，这是最酷的编程语言特性之一。我们可以编写一个模式来匹配我们想要解包或析构的值，而不是使用索引手工提取数据。

### 在线测试

老实说，我不确定该如何称呼这个特性，因为我不确定它是否有一个正式的名字。也就是说，内联测试是我见过的最酷的编程语言特性之一。

我第一次接触到 Pyret 中的内联测试，这是一种为编程教育而设计的语言。在 Pyret 中，单元测试是基本语法的一部分。换句话说，我们不需要导入任何库或者构建任何套件来运行测试。

相反，Pyret 在源代码中包含了几个用于测试的子句:

```
fun sum(l):
  cases (List) l:
    | empty => 0
    | link(first, rest) => first + sum(rest)
  end
where:
  sum([list: ]) is 0
  sum([list: 1, 2, 3]) is 6
end 
```

Enter fullscreen mode Exit fullscreen mode

这里我们可以看到[一个牛逼的列表求和函数](https://www.pyret.org/)。在函数中，有两种基本情况:空和非空。在空的情况下，函数返回 0。否则，该函数执行求和。

到那时，大多数语言都将完成，测试将是事后的想法。嗯，在派雷特不是这样。为了添加测试，我们包含了一个 where 子句。在这种情况下，我们测试一个空列表和一个期望和为 6 的列表。

当代码被执行时，测试运行。然而，测试是非阻塞的，所以代码将继续运行，除非出现任何灾难性的问题。

不管怎样，我喜欢这个特性，仅仅是因为它的可维护性。在使用 Pyret 的开发过程中，测试永远不会被遗忘。

### 内嵌汇编器

哦，你认为内嵌测试是唯一酷的内嵌特性？认识内联汇编器:这是我在 D 中编写 [Hello World 时第一次了解到的最酷的编程语言特性之一。](https://therenegadecoder.com/code/hello-world-in-d/)

事实证明，内联汇编程序是一种编程语言特性，它允许开发人员直接利用系统架构。当使用具有内联汇编支持的语言时，下面的代码是完全合法的:

```
void *pc;
asm
{
    pop  EBX         ;
    mov  pc[EBP], EBX ; 
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们有看起来像 C/C++和汇编编程的混合体，本质上就是这样。我们已经编写了直接与底层架构一起工作的代码，所以我们确切地知道发生了什么。

我发现这个特性非常有趣，因为当有机会时，语言倾向于选择抽象。有了 D，我们可以完全放弃内置功能，自己编写一个定制的汇编级实现。

## 你最喜欢的特色是什么？

你最喜欢的特性没有出现在最酷的编程语言特性列表中吗？在下面的评论里告诉我，我会去看看的！当然，如果你喜欢这篇文章，别忘了分享一下。我相信其他人也会的。

无论如何，感谢你花时间来检查我的工作。如果你喜欢你所看到的，[考虑订阅叛徒编码器](https://therenegadecoder.com/register/basic/)。那是我写作的地方。事实上，[这篇文章](https://therenegadecoder.com/code/the-coolest-programming-language-features/)是来自那个网站的交叉帖子，为什么不去看看呢？
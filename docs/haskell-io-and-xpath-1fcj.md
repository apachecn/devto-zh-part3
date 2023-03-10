# Haskell I/O 和 XPath

> 原文：<https://dev.to/adamretter/haskell-io-and-xpath-1fcj>

[![Haskell I/O and XPath](img/33e39b281052561217324e86946e0b9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--136Ugsdz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1500591377218-2484ab05eeaf%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

因为我自己的错误，过于热情，并且总认为任何基于技术的东西都有更好的方法...只是现在还没人实现！我决定看看是否能做些什么来改善在 xpdl(XPath 派生语言)中副作用和异步处理的使用和管理，例如 XQuery、XSLT、XProc 等。

继我的帖子 [EXPath 和异步 HTTP](https://dev.to/adamretter/expath-and-asynchronous-http-1n3g-temp-slug-7821267) 之后，黛比·洛科特同意与我合作为 [XML 布拉格](http://www.xmlprague.cz) 2019 撰写一篇论文，题为“*XPath 派生语言的任务抽象*”，我想知道她现在是否后悔这个决定？无论如何...

我们论文的一个方面着眼于 XPDL 实现目前为 I/O 提供了什么设施，以及这些设施是否会导致[副作用](https://en.wikipedia.org/wiki/Side_effect_(computer_science))。对于那些不*知情的人*，像 XPath 和 XQuery 这样的 XPDLs 是函数式编程语言，这意味着副作用是禁止的！XPath 和 XQuery 甚至有一个[形式语义](https://www.w3.org/TR/xquery-semantics/)禁止这些，所以任何允许副作用的实现首先是为了易用性而牺牲纯粹性，其次...很调皮！

### 回顾——为什么副作用是禁忌？

最简单的，考虑三个函数`getChar(): Char`、`putChar(Char): ()`和`putString(String): ()`。第一个从 *stdin* 中消费一个字符并将其返回，第二个向 *stdout* 输出一个字符，类似地，第三个向 *stdout* 输出一个字符串。

如果我们有一个应用程序，它在屏幕上打印一个提示，检索一个字符，然后在最终打印“Done”之前将其回显到屏幕上。，它的伪代码可能看起来像:

```
putChar('>')
let $x = getChar()
putChar($x)
putString("Done.") 
```

在命令式编程语言中，我们可以确定这些语句将从上到下顺序执行，一切都会好的。

然而，在纯函数式编程语言中，编译器可以自由地对不依赖的表达式的求值重新排序。也可以自由地删除那些其结果不需要用于未来计算的表达式。现在我们有问题了...第一个`putChar`表达式与任何其他表达式都没有关系，所以它可以在接下来的两个表达式(通过`$x`相关)之前或之后被求值，或者它可能永远不会被求值，这意味着我们的提示可能在我们输入并回显一个字符之后出现，或者它可能永远不会出现。对于`putString`来说也是一样，“完成”实际上可以先打印到屏幕上！

### 哈斯克尔？哈斯克尔怎么办？

如果您想知道为什么我谈论的是 XQuery 而不是 Haskell，那么请坐下来放松一下，我正在谈论 Haskell...

在过去，我曾涉猎过 Haskell，并且非常喜欢学习[为了更大的利益，向你学习 Haskell！](http://learnyouahaskell.com/)。关于 Haskell，你可以很快了解到的一点是，它是一种纯函数式编程语言。Haskell 禁止副作用，但不知何故仍然提供了安全*的 I/O 设施*...这是什么妖术？哈斯克尔的[木卫一](https://wiki.haskell.org/IO_inside)。

Haskell 的 IO Monad 和 XPath 有什么关系？这位先生:

<center>[![Haskell I/O and XPath](img/c9745fb081ef5b32cf6d1d93389a6c6a.png)](https://www.flickr.com/photos/drjason/280369547 "Philip Wadler")

菲利普·瓦德勒(又名λ人)。别被骗了，他穿那件斗篷是有原因的！凭借他头脑中抽象的力量，他可以融化你的脸！

</center>

Philip Wadler 负责开发 Haskell 中 I/O 的[单子，正如我最近联系到的，他也是](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.100.9674&rep=rep1&type=pdf) [W3C XQuery 1.0 和 XPath 2.0 形式语义规范](https://www.w3.org/TR/xquery-semantics/)的编辑之一。巧合吗？我觉得不是！

我知道 Haskell 有一种叫做 *IO Monad* 的东西，这使它能够保持一种纯粹的函数式语言(没有副作用)，同时仍然提供 I/O 操作，我现在知道 Philip Wadler 在这两方面以及 XPath 的正式规范中都有大量的参与。

也许如果我能理解 Haskell 中 I/O *实际上是如何工作的，那么我就可以利用这些知识来帮助我避免/管理 XPDLs 中 I/O 的副作用。*

### 哈斯克尔的 IO 行动

Haskell 允许应用程序通过使用 IO 动作来利用 I/O。事实上，它实际上实施了这一点，并且没有为 I/O 提供其他机制！请注意，我在这里说的是“IO 行动”而不是“IO 单子”，因为有人认为“IO 单子”一词容易引起误解和/或混淆，参见文章 [IO 单子被认为有害](https://blog.jle.im/entry/io-monad-considered-harmful.html)。然而，历史上使用的术语是“木卫一”。

我不会对单子做深入的解释，有很多解释比我在这里做的更全面和准确。更确切地说，在 Haskell 中，IO 类型的一元法则允许您将 IO 操作组合在一起。我将只涉及单子上最少的内容，允许我解释 Haskell 如何在允许 I/O 的同时仍然是一门纯语言(没有副作用)

在 Haskell 中，所有的 I/O 函数都必须返回一个 IO 动作，所以我们早期的函数看起来会像这样(用伪代码)- `getChar(): IO[Char]`，`putChar(Char): IO[()]`和`putString(String): IO[()]`。

好吧，那么...当我们现在调用函数时，我们得到的不是一个原始值，而是一个叫做`IO`的东西，乍看之下，它似乎包含了我们的结果。

然而，这里发生的事情实际上要微妙得多！考虑一下`getChar`的返回值`IO[Char]`，它并没有封装结果，而是封装了一个函数，当应用该函数时，它将从 stdin 中提取一个字符并返回结果字符。

通过封装动作来获得字符，`IO`动作推迟了对我们希望发生的实际 I/O 的评估。在这一点上没有执行任何 I/O，我们没有读取一个字符，我们只是表达了我们想要读取一个字符。

当所有的东西都返回一个`IO`时，我们可能会想，我们如何真正地读取一个字符，然后把这个字符放回屏幕上？记住我们的`putChar(Char)`函数接受一个`Char`类型的值参数，但是我们的`getChar(): IO[Char]`函数现在返回一个`IO[Char]`...所以我们不能再把`getChar`的输出直接传给`putChar`！

不要担心，通过`IO`类型的一元法则，我们可以将许多这样的 I/O 操作链接在一起。如果我们重写我们之前的应用程序，我们可能会有这样的结果:

```
let $w: IO[()] = putChar('>')
let $x: IO[Char] = getChar()
let $y: (a: Char -> IO[()]) = putChar(a)
let $z: IO[()] = putChar("Done.")

$w >> $x >>= $y >> $z 
```

我们不会在这里深入讨论`>>`(称为*然后*)和`>>=`(称为*绑定*)如何工作的具体细节，只说它们各自将两个`IO`动作(单子)组合成一个`IO`动作(单子)就足够了。尽管我在简化，但这两者的区别在于`>>`在构造新动作时丢弃了第一个动作的结果，而`>>=`提取第一个动作的结果并将其传递给新动作的构造器。

上面的应用程序代码故意显得冗长，以明确显示所涉及的类型。它可以简单地重写为`putChar('>') >> getChar >>= putChar >> putString("Done.")`。无论哪种方式，上面的应用程序的结果将是`IO[()]`，因为这是评估`$z`的结果。

但是我们仍然没有结果，我们有这个`IO[()]`东西，这意味着我们实际上还没有做我们描述的任何 I/O，我们只是有一个抽象，它模拟了我们希望发生的事情。现在让我们看看如何让有意义的事情发生...

### 如此...Haskell 中的 I/O 到底是如何工作的？

首先，当你写一个 Haskell 程序时，你必须定义一个名为`main`的函数，这个主函数必须返回一个`IO`。

你们中的一些人可能会说....啊哈哈！

所有 Haskell 程序都是一个`IO`！Haskell 编译器知道如何处理你的 *main* `IO`。

行...更令人挠头...这怎么是纯粹的功能性？让我们看看窗帘后面...

`IO`类型实际上是一个函数，其定义类似于`IO(RealWorld): (Result, RealWorld)`。这个函数只是一个状态转换器，它获取(真实世界*的)状态，执行一些计算，并返回一个由计算结果和(真实世界的)新状态组成的元组。*

Haskell 将`RealWorld`定义为一个抽象类型，关于它我们真的不用说太多。它的作用是线程化您的 IO 操作，以确保事情按照您期望的顺序执行。在内部，Haskell 编译器可能不会传递任何“真实世界”的状态。相反，它可以优化这一点，因为它可以将你漂亮的纯函数程序编译成一个命令性的副作用程序，可以由你的计算机的 CPU 执行。

如果您仍然感到困惑，简单地说，您在 Haskell 中表达的所有 I/O 都是构造纯函数。一元部分帮助你组成这些函数，而不必考虑`RealWorld`。`RealWorld`作为参数传递给每个 I/O 函数，该函数只对其参数进行计算，然后返回一个*结果*和一个新的`RealWorld`，该结果将被传递给下一个 I/O 函数，依此类推。

如果我们减少所有类型，`getChar(): IO[Char]`看起来会像:

```
getChar(): (RealWorld -> (Char, RealWorld)) 
```

### 我们能在 XPDLs 中利用这一点吗？

Haskell 的 IO 操作让用户既能轻松地描述我们希望执行的 I/O 操作，又能明确操作的执行顺序。

我们可以想象为 XPDLs 定义类似的东西。为了推动采用，我们必须努力在现有 XPDL 规范的设施内定义这一点。任何建议新的非标准化构造的东西，都可能导致实现者拒绝承担进一步的复杂工作，并最终导致缺乏采用。

例如，与 Haskell 不同，XQuery 的输入是 XDM (XPath 和 XQuery 数据模型)的实例，输出也是 XDM。XDM 确实有一个函数类型，所以我们可以返回类似于`IO`的东西吗？

在 Haskell 中,`RealWorld`是一个抽象类型。这意味着开发者不能实例化它(忽略`unsafePerformIO`)，因此不能自己完成 IO 功能。这可以防止开发人员任意执行有副作用的 IO。

XDM 类型系统是密封的，这意味着我们不能声明新的类型，无论是抽象类型还是其他类型！那么我们能做什么呢？由于我们不能从 XPDL 中创建抽象的`RealWorld`类型，这给我们留下了两个选择:

1.  我们简单地使用一个非抽象类型来表示现实世界，我们要求用户不要实例化这种类型的值，并将其应用于他们的`IO`。
2.  我们可以定义一个返回`item()`的函数签名，但实际上是返回一些专有类型。如果用户试图将专有类型之外的任何参数应用到他们的`IO`中，我们会引发一个错误。不幸的是，这将意味着跳出 XDM，并强制在 XPDL 处理器的宿主语言中实现。

如果我们考虑最简单的方法，并对现实世界使用非抽象类型，因为 IO 实际上只不过是一个函数，XPath 中适合它的签名可能是:

```
declare  function  io:IO($realworld  as  element(io:realworld))  as  item()+ 
```

在这个例子中，我将真实世界的类型表示为名为`io:realworld`的类型元素的节点。遗憾的是，XPath 中的类型系统不够强大，不允许我严格执行返回类型是值结果和新的真实世界的元组。作为一种折衷，我使用了一个总是有一个或多个值的序列，第一个值必须是新的真实世界，其余的是 IO 函数的值结果。

我们用 XQuery 表达的示例程序可能看起来像:

```
(: Side-effecting functions provided by an external processor or library :)  declare  function  stdin:get-char()  as  xs:integer  external;  declare  function  stdout:put-char($codepoint  as  xs:integer)  as  empty-sequence()  external;  declare  function  stdout:put-str($s  as  xs:string)  as  empty-sequence()  external;  (: IO version of get-char :)  declare  function  local:get-char()  as  function(element(io:realworld))  as  item()+  {  function($realworld  as  element(io:realworld))  {  ($realworld,  stdin:get-char())  }  };  (: IO version of get-char :)  declare  function  local:put-char($codepoint  as  xs:integer)  as  function(element(io:realworld))  as  item()+  {  function($realworld  as  element(io:realworld))  {  ($realworld,  stdout:put-char($codepoint))  }  };  (: IO version of put-str :)  declare  function  local:put-str($s  as  xs:string)  as  function(element(io:realworld))  as  item()+  {  function($realworld  as  element(io:realworld))  {  ($realworld,  stdout:put-str($s))  }  };  (: Implementation of `bind` i.e. `>>=` :)  declare  function  local:bind($m  as  function(element(io:realworld))  as  item()+,  $binder  as  function(item()*)  as  function(element(io:realworld))  as  item()+)  as  function(element(io:realworld))  as  item()+  {  function($realworld  as  element(io:realworld))  {  let  $mr  :=  $m($realworld)  let  $new-realworld  :=  $mr[1]  let  $computed  :=  subsequence($mr,  2)  return  $binder($computed)($new-realworld)  }  };  (: Implementation of `then` i.e. `>>` :)  declare  function  local:then($m1  as  function(element(io:realworld))  as  item()+,  $m2  as  function(element(io:realworld))  as  item()+)  as  function(element(io:realworld))  as  item()+  {  local:bind($m1,  function($ignored)  {  $m2  })  };  (: Express our chain of IO :)  let  $x  :=  local:put-char(fn:string-to-codepoints(">")[1])  =>  local:then(local:get-char())  =>  local:bind(local:put-char#1)  =>  local:then(local:put-str("Done."))  (: Apply the IO function... or??? :)  return  $x(<io:realworld/>) 
```

在上面的代码中，我们将 IO 函数作为 XQuery 的最后一个表达式。只要 IO 总是作为最后一个表达式被求值，我们就不必担心副作用。

与 Haskell 类似，理想情况下，我们会将 IO 函数作为查询结果返回。这里的问题是，查询的 XDM 结果可能遵循 W3C XSLT 和 XQuery 序列化规范，该规范不提供对任何返回函数的评估。

另一个考虑是，我们一直在传递的`IO`(函数)只是一个普通的旧 XPath 函数，所以即使我们确实将它作为查询的结果返回，XPDL 处理器也不知道应该对它进行不同的处理。一个潜在的解决方案是在函数中添加注释，但不幸的是，这些注释只在 XQuery 中可用，在 XPath 中不可用；这将无法接受地排除其他 XPDLs。另一种选择是，在描述特定的函数定义时，W3C XPath 函数规范和各种 EXPath 函数规范都已经使用了诸如“*”这样的语言...*”。我们也许可以用类似的规范语言来定义我们的 IO 函数，比如“*函数产生一个 IO 动作，这个动作应该只在查询完成后进行评估*”。

最后值得一提的是，您不会因为认为上面的 XQuery 代码相当冗长而受到责备！我有意将每一个副作用函数包装在一个安全的 IO 函数中，以便读者可以清楚地看到其中的机制。然而，通过一点小魔法，我们可以编写*提升*函数，将任何不安全的(副作用)函数提升为安全的 IO 函数。*提升*的目标是获取`a -> b` *、*类型的函数，并给我们一个`IO a -> IO b`类型的函数。为了简洁起见，省略了`bind`和`then`函数，我们的实现现在看起来像:

```
(: Side-effecting functions provided by an external processor or library :)  declare  function  stdin:get-char()  as  xs:integer  external;  declare  function  stdout:put-char($codepoint  as  xs:integer)  as  empty-sequence()  external;  declare  function  stdout:put-str($s  as  xs:string)  as  empty-sequence()  external;  (: Implemention of "lift"
 :
 : Lifts a function into an IO safe function.
 :
 : Converts a f():b to an f(IO[()]):IO[b]
 :)  declare  function  local:liftM0($f  as  function()  as  item()*)  as  function(function(element(io:realworld))  as  item()+)  as  function(element(io:realworld))  as  item()+  {  function  ($iob  as  function(element(io:realworld))  as  item()+)  {  $iob  =>  local:then(function($realworld  as  element(io:realworld))  {  ($realworld,  $f())  })  }  };  (: Implemention of "lift"
 :
 : Lifts a function into an IO safe function.
 :
 : Converts a f(a):b to an f(IO[a]):IO[b]
 :)  declare  function  local:liftM1($f  as  function(item()*)  as  item()*)  as  function(function(element(io:realworld))  as  item()+)  as  function(element(io:realworld))  as  item()+  {  function  ($iob  as  function(element(io:realworld))  as  item()+)  {  $iob  =>  local:bind(function($b  as  item()*)  {  function($realworld  as  element(io:realworld))  {  ($realworld,  $f($b)  )  }  })  }  };  (:
 : Implementation of `unit` a.k.a. `return`
 :
 : Just places a pure value into an IO
 :)  declare  function  local:unit($u  as  item()*)  as  function(element(io:realworld))  as  item()+  {  function($realworld  as  element(io:realworld))  {  ($realworld,  $u)  }  };  (: Express our chain of IO :)  let  $x  :=  local:unit(fn:string-to-codepoints(">")[1])  =>  (local:liftM1(stdout:put-char#1))()  =>  (local:liftM0(stdin:get-char#0))()  =>  (local:liftM1(stdout:put-char#1))()  =>  local:then(local:unit("Done"))  =>  (local:liftM1(stdout:put-str#1))()  (: Apply the IO function... or??? :)  return  $x(<io:realworld/>) 
```

我们可以更进一步，创建一个通用的提升函数，而不是每个函数都需要一个 arity，但是现在我只实现了 0 和 1 的 arity。

### 结论

我们当然可以在 XQuery 中表达来自 Haskell 的一元 IO 操作。像 Haskell 一样，*真实世界*通过 IO 操作的线程化允许我们确保 IO 的计算顺序，并且通过在程序结束时评估 IO，理想情况下由处理器应用，我们可以确保无副作用的计算。XQuery 中缺乏更具表达性的类型系统确实带来了潜在的问题，参数和返回值的类型不像 Haskell 中那样严格(可执行),这限制了可以执行的静态检查。

就个人而言，这是一个有趣的项目，看看我们能在 XQuery 中把这些 Haskell 概念推进多远。关于我们即将发布的会议论文，这些结果有助于我们形成一些强有力的观点，即我们希望为 XPDLs 设计什么。
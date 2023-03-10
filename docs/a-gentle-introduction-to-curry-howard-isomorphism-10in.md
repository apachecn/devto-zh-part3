# 库里-霍华德同构简介

> 原文：<https://dev.to/donaldkellett/a-gentle-introduction-to-curry-howard-isomorphism-10in>

*注意:本文最适合对至少一种纯函数式编程语言(如 Haskell)有基本了解的程序员。不需要 Idris 知识(本文使用的主要编程语言)；如果您能够阅读 Haskell 或类似的语言，那么您应该能够在阅读代码示例时理解 Idris。*

从前，数学家必须手工证明数学定理。一个数学家会花上几天、几个月甚至几年的时间，在一页页的论证中写下一页页的内容，仅仅是为了完成一个证明。在被社区接受之前，证明将由著名的数学家同行评审。通常情况下，一个训练有素的数学家要花好几年时间才能发现一个看似正确的证明中的逻辑谬误，而这个逻辑谬误会破坏整个证明。

直到 20 世纪中期数学家/计算机科学家发现了[库里-霍华德对应关系](https://en.wikipedia.org/wiki/Curry%E2%80%93Howard_correspondence)，有时被称为“库里-霍华德同构”。它本质上说明了编程语言中的类型可以被看作是数学定理，并且某种类型的程序可以被看作是该类型所表示的定理的证明。从那以后，某些主要的定理已经被计算机化的证明助手成功地证明了，其中一个(可能是)最著名的证明是对[四色定理](https://en.wikipedia.org/wiki/Four_color_theorem)的证明。虽然一些数学家提出了这样的担忧，即所使用的证明助理可能有问题，从而导致折衷的证明，但其他人指出，证明助理的可信度不亚于也容易出现人为错误的人类数学家。

你可能会问，这是怎么回事？考虑一个在传统 Java 编程语言中不进行类型检查的简单程序:

```
// Wrong.java

public class Wrong {
  public static void main(String[] args) {
    System.out.print("Hello!\n" * 10);
  }
} 
```

考虑另一个在 Java 中进行类型检查的简单程序:

```
// Right.java

public class Right {
  public static void main(String[] args) {
    for (int i = 0; i < 10; ++i)
      System.out.println("Hello!");
  }
} 
```

第一个程序不进行类型检查，因为将一个字符串乘以一个整数是没有意义的(尽管在 Python/Ruby 中可能有意义，并将字符串`"Hello!\n"`复制 10 次)。第二个程序进行类型检查，因为它有意义。例如，不会尝试从一个字符串中减去另一个字符串。从某种意义上说，当 Java 编译器对你的程序进行类型检查时，它证明了关于你的程序的某些定理，即你的程序不是完全无意义的。但是为了见证 Curry-Howard 通信的真正力量，我们需要一个比 Java 中的*更强的*类型系统。

输入 [Idris](https://www.idris-lang.org) ！Idris 是一种函数式编程语言，有点类似于 [Haskell](https://www.haskell.org) ，除了它的类型系统更加丰富。更重要的是，它有[依赖类型](https://en.wikipedia.org/wiki/Dependent_type)，这意味着类型是一流的，可以依赖值。这允许我们将值提升到类型级别，使我们能够证明关于它们的定理。但是在我们开始之前，让我们回顾一下数学归纳法的公理。

假设我们有一架无限高的梯子，从地面开始一直伸向天空。阶梯的连续阶梯之间的距离是恒定的，例如，第 4 和第 5 级阶梯之间的距离与第 10 和第 11 级阶梯之间的距离相同。那么，我们怎么知道我们能到达梯子上的任何一级呢？只要知道我们能到达梯子的第一级就够了，并且假设我们能到达梯子的某一级 *k* ，那么无论如何我们总能到达梯子的下一级 *k + 1* 。简而言之，这就是归纳法的证明——如果一个陈述对于某个初值 *v* (我们的基础)为真，并且如果我们可以证明它对于某个第 *k + 1* 个值为真，假定它对于某个第 *k* 个值(我们的归纳法步骤)为真，那么这个陈述对于所有值> = *v* 都必须成立。

现在，对于这个有趣的话题——假设我们有两个计数数字(例如 1，2，3) *n* 和 *m* ，我们把它们加在一起。它们的添加顺序和原因有关系吗？直觉上，顺序并不重要——例如`3 + 5 = 5 + 3 = 8`和`2 + 3 = 3 + 2 = 5`。但是我们如何确定我们可以以任何顺序将两个任意的计数相加，并且仍然得到相同的结果呢？也许你会说这是显而易见的；然而，一个数学家会拒绝接受这样一个站不住脚的论点。那么，我们如何一劳永逸地解决`n + m = m + n`的问题呢？

十九世纪晚期，一位名叫[朱塞佩·皮亚诺](https://en.wikipedia.org/wiki/Peano_axioms)的数学家将自然数(包括零在内的计数)的编码公式化如下:

1.  `0`是一个自然数
2.  对于每个自然数`n`，它的后继者`S n`也是一个自然数
3.  没有其他自然数(隐含)

这些现在被称为[皮亚诺公理](https://en.wikipedia.org/wiki/Peano_axioms)。虽然乍看之下它们似乎很奇怪且没有必要，但它们简单的结构实际上让数学家很容易推断出它们的性质。

为了直观地了解上面的符号是如何表示自然数的，请考虑以下内容:

*   `1`是`0`之后的下一个自然数。所以在皮亚诺公理中，我们把它写成`1 = S 0`。
*   `2`是`1`之后的下一个自然数。所以`2 = S 1 = S (S 0)`。
*   同理，`3 = S 2 = S (S 1) = S (S (S 0))`以此类推。

直观上，任何自然数`n`的后继者`S n`都可以被认为是“继`n`之后的下一个自然数”。你可能会奇怪，为什么我不把继任者`S n`简单地称为`1 + n`。这是因为我们还没有定义自然数的加法，我们现在要做的是:

1.  `0 + m = m`
2.  `S n + m = S (n + m)`(LHS 应读作`(S n) + m`)

那么这一切意味着什么呢？第一条规则`0 + m = m`应该是相当直观的——当然零加到任何数上等于它本身！第二条规则可能不那么明显，但可以这样理解，“假设我们知道`n + m`的结果。那么`n`之后的下一个数和另一个数`m`之和就是`n + m`之后的下一个数。”我们怎么知道这精确地描述了加法？我们可以注意到它涵盖了所有的情况- `m`假设是一个任意的自然数，左边的自然数只能是零或任意自然数的后继！这里有一个使用阿砣加法定义评估`2 + 3`的例子，以确保定义是正确的:

```
 2 + 3
= S (S 0) + S (S (S 0))
= S (S 0 + S (S (S 0)))
= S (S (0 + S (S (S 0))))
= S (S (S (S (S 0))))
= 5 
```

在 Idris 中，自然数的阿砣公式是在标准前奏中定义的[，它被隐式导入到每个 Idris 文件中。上面强调的加法的阿砣定义也在](https://github.com/idris-lang/Idris-dev/blob/master/libs/prelude/Prelude/Nat.idr#L15)[中定义](https://github.com/idris-lang/Idris-dev/blob/master/libs/prelude/Prelude/Nat.idr#L61)。声明`total plus : (n, m : Nat) -> Nat`中的关键字`total`仅仅意味着阿砣加法覆盖了`n`和`m`的所有可能值，并且总是产生一个结果(我们会直观地期望两个任意计数的数相加)。

请注意，在加法的定义中，我们没有为所有的`n`声明`n + 0 = n`。但是直觉上它必须是真的！那是因为我们不需要明确地陈述它——相反，我们可以从上面给出的加法的定义中推导(证明)`n + 0 = n`。但是怎么做呢？这就是归纳派上用场的地方。我们将首先在纸上做它，然后看它如何能被翻译成 Idris。

为了用数学归纳法证明`n + 0 = n`，我们首先需要确定一个我们可以归纳的变量。`n`是这里唯一的变量，所以我们将引入`n`。我们的依据将是`n = 0`:

```
When n = 0,
    n + 0
  = 0 + 0
  = 0 (by definition of Peano addition)
  = n 
```

所以这个基础是千真万确的。请注意，在加法定义中，对于所有的`m`，表达式`0 + 0`是如何按照规则`0 + m = m`自动简化为`0`的。所以我们得到`0 = 0`，这显然是真的——显然任何事物都必须等于它自身！在数学术语中，我们称这个规则为(等式的)反身性，即对于任何`x`的`x = x`。

既然已经踏上了我们概念阶梯上的第一步，是时候证明我们总能到达下一步了。假设`n + 0 = n`对于某个自然数`n`为真(这被称为我们的归纳假设——我们为了进行数学归纳而假设为真)。然后显示接下来的情况`S n + 0 = S n`足以证明`n + 0 = n`对于所有自然数`n`:

```
 S n + 0
= S (n + 0) (by definition of Peano addition)
= S n       (by our inductive hypothesis n + 0 = n) 
```

我们完了。它仍然需要翻译成 Idris。

在我们开始之前，快速制作一个文件:

```
module Main

%default total

main : IO ()
main = do
  putStrLn "Hello World!" 
```

如果你了解 Haskell，你应该对它的语法很熟悉。唯一的区别是:

1.  `module Main`之后没有`where`
2.  有一个额外的声明`%default total`(解释如下)
3.  对于`main`的类型声明，我们用单冒号代替了双冒号。

区别(1)和(3)是微不足道的——我们唯一应该感兴趣的是(2)。回想一下`total`是指一个函数覆盖所有情况，并且总是产生一个结果(在有限时间内)。`%default total`简单来说就是默认情况下，这个模块中的所有函数都被假设为 total。这对证明很重要，因为证明应该对所有指定的可能输入值*和*成立，并且“有限时间”限制确保我们不会建立任何阻止我们得出结论的循环论证，例如`n + 0 = n`因为`n + 0 = n`因为...

首先，让我们算出这个定理的类型。我们刚刚证明的本质上是“对于所有自然数`n`，`n + 0 = n`”的陈述。这也可以理解为“如果我们有一个自然数`n`，那么`n + 0 = n`”。所以我们定理的类型(姑且称之为`zeroRightId`)应该是:

```
zeroRightId : (n : Nat) -> n + 0 = n 
```

注意*值* `n`是如何出现在`zeroRightId`的类型签名中的。由于 Idris 中的依赖类型，这是可能的，也就是说，类型可以依赖于值。事实上，类型可以被传递给函数，在 Idris 中也可以被计算并从函数返回，也就是说，它们是一级的，但这不会在这篇博文中探讨。

还要注意我们如何将自然数定义为`data Nat = Z | S n`，但是我们的类型签名用`n + 0 = n`代替了`n + Z = n`。事实上，Idris 在某些情况下可以将非负整数文字识别为自然数，有时甚至会优化它们的表示，以帮助提高计算效率(毕竟，计算阿砣加法所需的时间与最左侧自然数的大小成正比，这远非有效)。另一件事——当`n`和`m`是自然数时，Idris 将`n + m`识别为`plus n m`。

现在为基础。还记得基本情况`0 + 0 = 0`是如何减少到`0 = 0`的吗？如上所述，这在数学上叫做反身性。Idris 也将平等定义如下:

```
data (=) : a -> b -> Type where
  Refl : x = x 
```

Idris 中的`Refl`数据构造函数代表数学中等式的自反性，即`x = x`对于任何`x`。至于`x`是什么，它是在编译时根据上下文推断出来的(如果这种推断失败，就会导致类型错误)。

因此，对于`n = 0`的基础来说，证明`n + 0 = n`简单地简化为纯粹的自反性:

```
zeroRightId Z = Refl 
```

这里，`x`的值被推断为`Z`并且我们的等式进行类型检查。在这种情况下，我们不能用`0`替换`Z`——由于某种未知的原因，我这边的 Idris 编译器将`0`识别为`Integer`而不是`Nat`。无论如何，你可以把`Z`读成`0`，反之亦然。

所以现在我们的代码变成了:

```
module Main

%default total

zeroRightId : (n : Nat) -> n + 0 = n
zeroRightId Z = Refl

main : IO ()
main = do
  putStrLn "Hello World!" 
```

除非我们用`idris Main.idr -o main`编译它，否则它仍然会因为编译错误而失败。这是因为我们还没有完成我们证明的归纳步骤——Idris 在这种情况下抱怨说我们的程序(proof) `zeroRightId`并不完整。

回忆一下我们的归纳步骤:

```
 S n + 0
= S (n + 0) (by definition of Peano addition)
= S n       (by our inductive hypothesis n + 0 = n) 
```

根据阿砣加法的定义，第一个等式`S n + 0 = S (n + 0)`成立。在 Idris 中，定义为真的等式可以由类型检查器推导出来，所以我们不需要在 Idris 中明确地声明它们。有待证明的是说法`S (n + 0) = S n`。

让我们首先填充 Idris 中归纳步骤的左侧。我们想证明我们的语句`n + 0 = n`对于第`S n`行中的*下一个*自然数成立，所以这将是:

```
zeroRightId (S n) = ?proof 
```

在 Idris 中被称为类型化的洞——当你还没有弄清楚它应该是什么的时候，用它来填充一个方程。

为了证明`S (n + 0) = S n`，我们调用了我们的假设(归纳假设)即`n + 0 = n`在复杂项`S (n + 0)`内将`n + 0`转化为`n`。Idris 中的归纳假设仅仅是`zeroRightId n`(练习:说服自己它的类型确实是`n + 0 = n`)因此使用它给出:

```
zeroRightId (S n) = zeroRightId n 
```

除了它不进行类型检查！注意我们等式的左边有类型`S n + 0 = S n`不同于`n + 0 = n`——毕竟，它们是完全不同的语句！那么我们如何在 `S (n + 0)`内使用我们的归纳假设`zeroRightId n` *？我们可以将后继操作`S`视为某种函数——它接收一个自然数`n`作为参数，并返回一个新的结果`S n`，它也是一个自然数。在 Idris 中有一个引理(lemma:一个用来证明另一个感兴趣的主要定理的定理)`cong : a = b -> f a = f b`说明等式尊重函数应用([来源](https://github.com/idris-lang/Idris-dev/blob/master/libs/prelude/Prelude/Basics.idr#L47))。同样，`f`是从上下文中推断出来的——我们可以用`f = S`将`cong`应用到`n + 0 = n`，将`zeroRightId n : n + 0 = n`转换成`cong (zeroRightId n) : S (n + 0) = S n`。所以我们的归纳步骤变成了:*

```
zeroRightId (S n) = cong (zeroRightId n) 
```

完整的文件目前如下所示:

```
module Main

%default total

zeroRightId : (n : Nat) -> n + 0 = n
zeroRightId Z = Refl
zeroRightId (S n) = cong (zeroRightId n)

main : IO ()
main = do
  putStrLn "Hello World!" 
```

用`idris Main.idr -o main`再次编译它，确认它确实进行了类型检查。运行可执行文件`main`(比如在我的 Mac 上运行`$ ./main`)会像预期的那样打印`Hello World!`。恭喜你，你已经成功证明了你在 Idris 中的第一个定理！

如果你感兴趣，这里有一些练习:

1.  证明`n + S m = S (n + m)`为任意自然数`n`、`m`。一个好的方法是首先在纸上证明它，然后一步一步地将其转化为 Idris。
2.  证明自然数加法的交换律，即`n + m = m + n`对所有自然数`n`、`m`。提示:你刚刚证明的引理`n + 0 = n`和`n + S m = S (n + m)`会派上用场；)

请注意，上面的证明(包括我带您看的第一个证明)都是 Idris 标准库的一部分，但是在您至少尝试构造证明之前，请不要偷看它们。

如果你真的*感兴趣，可以做一些进一步的练习:*

1.  证明`n * m = m * n`为自然数。不过，你必须首先定义乘法；)
2.  证明`a * (b + c) = a * b + a * c`为自然数。
3.  证明`(a + b) * c = a * c + b * c`为自然数。请注意，这是与(2)不同的*语句！*
**   列出幂运算的一些性质，并尝试证明它们，最后将它们翻译成 Idris。同样，你必须首先定义指数运算，并确认你的定义与直觉相符。*

 *在这篇博文中，你通过用编程语言 Idris 编码一个适当的数学证明(尽管是一个相当简单的证明),亲身见证了 Curry-Howard 同构。我希望你喜欢它，:D

延伸阅读:

1.  [Idris 中的定理证明](http://docs.idris-lang.org/en/latest/tutorial/theorems.html)
2.  一系列合适的[校对助手](https://en.wikipedia.org/wiki/Proof_assistant#Comparison_of_systems) -有些甚至允许你像在纸上一样一步一步地(而不是一个方程)写校对！
3.  [Coq](https://coq.inria.fr) 校对助手*
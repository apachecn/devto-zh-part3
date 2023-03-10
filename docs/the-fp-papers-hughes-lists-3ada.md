# 外交政策文件:休斯清单

> 原文：<https://dev.to/glebec/the-fp-papers-hughes-lists-3ada>

坐在我的驱动器上的是一个不断扩大的关于函数式编程和相关主题的论文集。这些论文中的一些被认为是该领域的经典，介绍或推广了现在是 FP 的主要内容的技术。其他人对熟悉的主题提供了丰富的观点，揭示了嵌入在常见编程模式中的深层数学原理。

一部分是为了真正通读这些论文，一部分是因为我喜欢为更广泛的社区分解和翻译这样的材料(例如，用 JavaScript)，我开始了一个新的文章系列，暂定名为“FP 论文”(我知道是富有想象力的)。这是该系列的第一篇文章。

# 一种新颖的列表表示法及其在函数“Reverse”中的应用

| 作者 | 年 | 数字对象标志符 | 页 |
| --- | --- | --- | --- |
| R.约翰·缪尔·休斯 | One thousand nine hundred and eighty-six | [https://doi . org/10.1016/0020-0190(86)90059-1](https://doi.org/10.1016/0020-0190(86)90059-1) | four |

> 提出了作为一级函数的列表表示。以这种方式表示的列表可以在固定时间内追加在一起，并且可以在与它们的长度成比例的时间内转换回普通列表。使用 append 构造列表的程序通常可以通过使用这种表示来改进。例如，可以使简单反向以线性时间运行，然后可以容易地导出传统的“快速反向”。KRC(特纳，1982)给出了例子，符号被解释为它被介绍。该方法可以与 Sleep 和 holm strm(1982)的建议相比，通过改变解释器来实现类似的效果。

[约翰·休斯](https://en.wikipedia.org/wiki/John_Hughes_(computer_scientist))是我最喜欢的几篇 FP 论文的作者和合著者，包括《为什么函数式编程很重要》和《QuickCheck:随机测试 Haskell 程序的轻量级工具》。在这篇论文中，他介绍了一个相当聪明的数据结构——一个带有*常量时间(`O(1)`)追加操作*的不可变列表。这些列表经常被称为*差异列表*(d 列表)或者有时被称为*休斯列表*。

## 突变？

即使在多变的环境中，这也是一个挑战。考虑一个单链表类，第一个和最后一个节点都有句柄；可以想象，通过将第一个列表的`last`节点“重新连接”到第二个列表的`first`节点:
，可以在恒定的时间内完成追加

```
List A:                       List B:
first         last            first         last
<1> -> <2> -> <3>             <a> -> <b> -> <c>
               |               ^
               —————append—————| 
```

Enter fullscreen mode Exit fullscreen mode

列表 A 的`last`指针将被相应地更新(指向`<c>`)。然而，上面的实现有一个明显的缺陷；它在可变设置中使用了[结构共享](https://en.wikipedia.org/wiki/Persistent_data_structure)。如果有什么东西修改了列表 B，那么现在*也会*在列表 A 中显示出来！

```
List A:              (List B)
<1> -> <2> -> <3> -> <a> -> <b> -> <c>
                             |
                           change (List B)
                             |
List A:                      V
<1> -> <2> -> <3> -> <a> -> <x> -> <c>
             (oops — List A now modified too) 
```

Enter fullscreen mode Exit fullscreen mode

在某些情况下，这可能是有意的，但很容易意外地开始改变列表。即使我们从不改变节点*的值*，追加本身的行为也是一种变异，所以举例来说，如果有人将一个列表 C 追加到列表 B，他们将会意外地影响列表 A *以及*。哎呀！

为了规避这种风险，一个可变链表通常会**复制**个要追加的节点:

```
List A:                       List B:
first         last            first         last
<1> -> <2> -> <3>             <a> -> <b> -> <c>
               |               |      |      |
               —————append————copy   copy   copy
                               |      |      | 
                               V      V      V
                              <a> -> <b> -> <c> 
```

Enter fullscreen mode Exit fullscreen mode

操作现在是“安全的”,因为修改原始列表 B 不会影响 A(反之亦然),但是它相对于列表 B 的长度是线性的(`O(n)`时间)。

## 纯度？

也许存在一个合理的常量时间可追加可变链表的实现，但这不是 Hughes 论文的重点。在纯功能代码中，没有任何(直接)突变。经典的函数式链表结构(又名 [Cons list](https://en.wikipedia.org/wiki/Cons) )是一个作为嵌套对实现的单链表；每对的第一个单元格是一个值，第二个单元格是指向下一对(或空列表)的指针。分别在 Scheme，Haskell，和 JS:

```
; Scheme
(cons 1 (cons 2 (cons 3 nil)))
; (list 1 2 3) 
```

Enter fullscreen mode Exit fullscreen mode

```
-- Haskell
1 : 2 : 3 : []
-- [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

```
// JavaScript
const cons = (val, next) => ({ val, next })
cons(1, cons(2, cons(3, null)))
// { val: 1, next: { val: 2, next: { val: 3, next: null }}} 
```

Enter fullscreen mode Exit fullscreen mode

没有突变的限制意味着附加两个这样的列表(产生新的列表 C)是`O(n)`，其中`n`是第一个*列表*的长度。

```
List A:                       List B:
first         last            first         last
<1> -> <2> -> <3>       ———>  <a> -> <b> -> <c>
 |      |      |        |
copy   copy   copy   append
 |      |      |        | 
 V      V      V        |
<1> -> <2> -> <3> ——————|
List C 
```

Enter fullscreen mode Exit fullscreen mode

我们*必须*复制`<3>`来改变它的`next`指针，我们必须反过来复制`<2>`来改变*它的* `next`指向新的`<3>`，等等。然而，我们可以安全地共享列表 B 的节点，因为实际上没有什么会改变它们。

总的来说，这与我们第一次尝试追加可变列表非常相似，但是反过来了——我们必须复制左边的*列表，而不是右边的*列表。

## 论文

### 交涉

有了背景，我们就可以开始真正阅读这篇论文了。Hughes 从讨论抽象数据类型的一般概念开始，用具体数据类型的 T2 来表示。也许令人困惑的是，休斯选择的“抽象”和“具体/具象”将分别指熟悉的*反对列表*和有名无实的*小说列表表示*。

> 每当抽象数据类型 A 由具体数据类型 R 表示时，必须定义两个函数:
> 
> `abs: R -> A`
> 
> `rep: A -> R`

在这种情况下，`abs`函数会将 Hughes 列表(又名 DList)转换为 Cons 列表；函数将把一个 Cons 列表转换成一个 DList。`abs`函数必须是`rep`的[左逆](https://en.wikipedia.org/wiki/Inverse_function#Left_and_right_inverses)；也就是说，当往返一个缺点列表到列表(通过`rep`)和返回列表(通过`abs`)时，你必须到达你开始的地方。从这个意义上来说，`abs`“撤销”`rep`。

现在，假设我们想要一个`append`函数用于缺点列表。Hughes 指出，如果我们可以为 DLists 定义一个`append`函数，我们就可以免费获得 Cons 版本——我们可以将 Cons 列表转换成 DLists，`append`它们，然后将得到的 DList 转换回 Cons 列表。

```
 ————— ConsListAppend ————————————————————————————————
             |                                                   |
ConsListA —> | —rep—> DListA —> ———————————————                  |
             |                  | DListAppend | —> DListC —abs—> | —> ConsListC
ConsListB —> | —rep—> DListB —> ———————————————                  |
             |                                                   |
             ————————————————————————————————————————————————————— 
```

Enter fullscreen mode Exit fullscreen mode

Hughes 用一个有趣的普遍规律来描述这种关系:

> [给定`f: A -> A`和`g: R -> R`，`g`精确地“实现”`f`如果:]
> 
> 对于所有 r: R.f(abs r) = abs(g r)

这条定律是用部分应用的函数来表述的，这个主题超出了本文的范围。幸运的是，部分应用对于理解 DLists 本身并不是绝对必要的；这条定律的主要结论是，一个“正确的”数据列表将*给我们*如前所述。

### DList，定义

休斯现在放下了承诺的“列表的新颖表现”，几乎没有任何仪式。

> …表示函数 rep 只是 append。
> 
> 重复 L =附加 L
> 
> (在这里，我们使用了 KRC 惯例，即所有功能都被 curried。)

小心点。这个简短的片段中包含了一些微妙之处。Hughes 用`append L`来表示*一个**前置** `L`* (到一个隐式列表`L2`)的函数。这可能会澄清为什么会这样:

```
append A B -- a list     (A ++ B / append B to A / prepend A to B)
append A   -- a function (A ++ ? / append ? to A / prepend A to ?) 
```

Enter fullscreen mode Exit fullscreen mode

在 JavaScript 中，我们可以这样写这个`rep`函数:

```
const rep = consList1 => (consList2 => append(consList1, consList2))
//              ^        \                                         /
//              |         —————————————————————————————————————————
//              |                            |
//         input list L                output DList 
```

Enter fullscreen mode Exit fullscreen mode

这强调了非常重要的一点——**DList 是一个函数**。如果我们调用`rep(cons(1, cons(2, cons(3, null)))`，我们将得到一个代表缺点列表[1，2，3]的 DList*；然而，DList 的值将是`'function'`。怎么会这样呢？嗯，正如 Hughes 指出的，我们可以很容易地将 DList *转换回*为 Cons 列表——通过传入空列表`null`。试试下面的:*

`// Cons list implementation const cons = (val, next) => ({ val, next }) const append = (c1, c2) => { if (!c1) return c2 return cons(c1.val, append(c1.next, c2)) // recursively copies c1 nodes } // convert Cons List -> DList const rep = consList1 => (consList2 => append(consList1, consList2)) // convert DList -> Cons List const abs = DList => DList(null) // create a Cons list and its DList equivalent const cl1 = cons(1, cons(2, cons(3))) const dl1 = rep(cl1) console.log(typeof dl1) // 'function' // convert from the DList back to a Cons list const cl2 = abs(dl1) console.log(cl2) // { val: 1, next: { val: 2, next: { val: 3, next: null }}}`

或者在 Haskell:

```
-- (:) is Cons, ([]) is empty list "Nil"
-- [1, 2, 3] is a Cons list (1 : 2 : 3 : [])
-- (++) is append

type DList a = [a] -> [a]

repr :: [a] -> DList a
repr cl = (cl ++)

abst :: DList a -> [a]
abst dl = dl []

cl1 = [1, 2, 3]

dl1 = repr cl1
cl2 = abst dl1

main :: IO ()
main = print (cl1 == cl2) -- True 
```

Enter fullscreen mode Exit fullscreen mode

“诀窍”是 DList 函数通过[闭包](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)捕获用于构造它的传入 Cons 列表。因此，DList“包含”一个缺点列表，可以通过应用 DList 来恢复。

### 常数时间追加

休斯再次低调地介绍了 DLists 的杀手锏:

> 新的表示很有趣，因为通过将两个表示组合成函数，可以将它们附加在一起，所以我们可以定义
> 
> appendR f g = f · g
> 
> (函数合成在 KRC 是用点号写的。)

函数组合是创建新函数的行为，该新函数将函数`g`的输出连接到函数`f`的输入。在 JS:

```
const compose = (f, g) => (x => f(g(x))
//              \    /    \           /
//               ————      ———————————
//                |             |
//           input funcs     the composition (a fn) 
```

Enter fullscreen mode Exit fullscreen mode

注意，函数组合*返回了一个新函数*，但是它没有调用`f`或`g`来这样做。相反，当应用时，结果组合*依次调用`g`和`f`。因此，不管`f`或`g`有多贵，创作它们的行为都是免费的——也就是说，时间不变。然而，实际上使用这种组合物仍然很昂贵。* 

```
//                   O(1)   O(f) + O(g)
//                   ————   ———————————
//                     |     |
const compose = (f, g) => (x => f(g(x))) 
```

Enter fullscreen mode Exit fullscreen mode

这如何适用于数据列表？由于数据列表是函数，它们可以在常数时间内组合在一起…结果是*一个新的数据列表*:

`// Cons list implementation const cons = (val, next) => ({ val, next }) const append = (c1, c2) => { if (!c1) return c2 return cons(c1.val, append(c1.next, c2)) // recursively copies c1 nodes } // convert Cons List -> DList const rep = consList1 => (consList2 => append(consList1, consList2)) // convert DList -> Cons List const abs = DList => DList(null)` `// 'cons', 'append', 'rep', and 'abs' in scope const compose = (f, g) => (x => f(g(x))) const c12 = cons(1, cons(2, null)) const c34 = cons(3, cons(4, null)) const d12 = rep(c12) const d34 = rep(c34) // O(1) const d1234 = compose(d12, d34) // O(n) const c1234 = abs(d1234) console.log(c1234)`

```
-- in Haskell:
c12 = [1, 2]
c34 = [3, 4]

d12 = repr c12
d34 = repr c34

d1234 = d12 . d34

main = print (abst d1234) -- [1, 2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

这是如何工作的？Hughes 在他的论文中给出了一个简短而清晰的逻辑证明，但是我们可以非正式地注意到，由于一个 DList *在它的内部 list* 之前，组合两个 DList 是一个函数，它首先在右边的*list*之前，然后在左边的*list*之前，对传入的任何 list:

```
DListA:                   DListB:
prepend (<a> -> <b>)      prepend (<c> -> <d>)

DListC = (DListA · DListB):
prepend (<c> -> <d>) then prepend (<a> -> <b>)

abs DListC (i.e., apply DListC to Nil):
prepend (<c> -> <d>) to Nil
then prepend (<a> -> <b>) to the above
result is (<a> -> <b> -> <c> -> <d>) 
```

Enter fullscreen mode Exit fullscreen mode

细心的读者在这一点上可能会有一些顾虑。正如休斯指出的:

> 函数组合是一种高效的操作。它总是可以在恒定的时间内完成，因此 abs 是否合理有效还有待证明。
> 
> 假设一个“函数列表”是仅使用 rep 和 appendR 构建的，abs 可以在与它的长度成比例的时间内将其转换成普通列表。

这看起来像是作弊。当然，数据列表可以在`O(1)`时间内完成，但是我们仍然需要`O(n)`时间来将一个缺点列表转换成数据列表，反之亦然！有什么意义？

答案是函数*懒*。数据列表“内部”的代码——函数体，包含追加操作——不会仅仅因为构造了数据列表就执行。我们可以建立一个清单*而不是从一个缺点清单*开始！

`// Cons list implementation const cons = (val, next) => ({ val, next }) const append = (c1, c2) => { if (!c1) return c2 return cons(c1.val, append(c1.next, c2)) // recursively copies c1 nodes } // convert Cons List -> DList const rep = consList1 => (consList2 => append(consList1, consList2)) // convert DList -> Cons List const abs = DList => DList(null)` `// 'cons', 'append', 'rep', and 'abs' in scope // O(1) creation of two DLists: remember, function body does not run YET const d12 = c2 => append(cons(1, cons(2, null)), c2) const d34 = c2 => append(cons(3, cons(4, null)), c2) const compose = (f, g) => (x => f(g(x))) const appendD = compose const d1234 = appendD(d12, d34)`

```
-- Haskell
d12 = \c2 -> [1, 2] ++ c2
d34 = \c2 -> [3, 4] ++ c2

d1234 = d12 . d34 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，没有构建实际的缺点列表。定义了两个*可以*产生 Cons 列表的函数，但是两个函数体都没有实际运行，所以运行时成本仍然是假设的。然而，我们定义了一个新的 DList，它表示两个假设的 Cons 列表的连接，并且所有这些都在恒定时间内进行。

当然，将这个列表转换回缺点列表需要线性时间。

```
// JS: O(n)
d1234(null) // cons(1, cons(2, cons(3, cons(4, null)))) 
```

Enter fullscreen mode Exit fullscreen mode

```
-- HS: O(n)
d1234 [] -- [1, 2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

> 当 abs 将这样的函数应用于空列表以将其转换回普通列表时，它计算
> 
> 附录 L1(附录 L2 ...)(附录 Ln [])。
> 
> 计算成本(追加 A B)与 B 无关——它只是 A 的长度，因此，计算上面表达式的成本是 L1，L2，…，Ln 的长度之和，也就是最终列表的长度。

这是 DList 的主要权衡之一:如果您期望执行*许多*附加操作，并且只需要最后的最终结果，DList 可以防止重复工作；这通常会将整个`O(n^2)`算法简化为`O(n)`。

## 反转

“列表的新颖表示”只是本文标题的前半部分；后半部分是“及其对‘反转’函数的应用”。Hughes 使用一个简单的单链表函数来说明一个可以通过 DLists 改进的算法。

```
// JS
const reverse = cl => {
    if (!cl) return cl
    return append(reverse(cl.next), cons(cl.val, null))
} 
```

Enter fullscreen mode Exit fullscreen mode

```
-- Haskell
reverse :: [a] -> [a]
reverse [] = []
reverse (x:xs) = reverse xs ++ [x] 
```

Enter fullscreen mode Exit fullscreen mode

考虑 cons 列表[1，2，3，4]上的`reverse`调用的以下伪代码扩展:

```
reverse [1, 2, 3, 4]

// recursive applications of reverse
append(reverse [2, 3, 4], [1])
append(append(reverse [3, 4], [2]), [1])
append(append(append(reverse [4], [3]), [2]), [1])

// sequential applications of append
append(append(append([4], [3]), [2]), [1])
  - append(append([_, 3], [2]), [1])
  - append(append([4, 3], [2]), [1])
append(append([4, 3], [2]), [1])
  - append([_, _, 2], [1])
  - append([_, 3, 2], [1])
  - append([4, 3, 2], [1])
append([4, 3, 2], [1])
  - [_, _, _, 1]
  - [_, _, 2, 1]
  - [_, 3, 2, 1]
  - [4, 3, 2, 1] 
```

Enter fullscreen mode Exit fullscreen mode

因为缺点列表`append`与左边列表的长度成比例，并且随着我们不断建立越来越大的列表，我们最终会执行相同的工作。`append`平均花费`O(n/2)`时间，我们对`n`元素也这样做，总的结果是`O(n^2)`时间。

使用 DLists，我们切换到一个`O(1)`追加:

`// Cons list implementation const cons = (val, next) => ({ val, next }) const append = (c1, c2) => { if (!c1) return c2 return cons(c1.val, append(c1.next, c2)) // recursively copies c1 nodes } // convert Cons List -> DList const rep = consList1 => (consList2 => append(consList1, consList2)) // convert DList -> Cons List const abs = DList => DList(null) // DList append const compose = (f, g) => (x => f(g(x))) const appendD = compose` `// 'cons', 'append', 'rep', 'abs', and 'appendD' in scope // Cons List -> reversed Cons List const reverse = consList => { return abs(rev(consList)) } // Cons List -> reversed DList const rev = consList => { if (!consList) return (c2 => c2) // O(1) empty DList, prepends nothing return appendD( // O(1) append (function composition) rev(consList.next), // O(n) recursive reversal c2 => cons(consList.val, c2) // O(1) DList of one value ) } const c1234 = cons(1, cons(2, cons(3, cons(4, null)))) const c4321 = reverse(c1234) // O(n) console.log(c4321)`

```
-- Haskell

reverseC :: [a] -> [a]
reverseC c = abst (rev c)

rev :: [a] -> DList a
rev [] = id
rev (x:xs) = (rev xs) . (x :)

main = print (reverseC [1, 2, 3, 4]) -- [4, 3, 2, 1] 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

应该注意的是，这不是定义高效`reverse`函数的唯一方式；这个问题通常作为一个小的、熟悉的例子来说明数据列表是如何工作的。DList 解决方案概括了一个手工编码的尾递归`reverse` :

```
const reverse = initList => {
    const go = (oldList, newList) => {
        if (!oldList) return newList
        const newerList = cons(oldList.val, newList)
        return go(oldList.next, newerList)
    }
    return go(initList, null)
} 
```

Enter fullscreen mode Exit fullscreen mode

或者在 Haskell:
中使用`foldl`

```
reverse :: [a] -> [a]
reverse = foldl (flip (:)) [] 
```

Enter fullscreen mode Exit fullscreen mode

Hughes 还继续演示了数据列表的另一个用例，将一行文本(以字符列表的形式)分解成单词(用空格分隔)。

这篇论文以一些关于性能的注释结束——也就是说，手工编码的效率`reverse`比使用 DLists 更快，但是当然 DLists 比简单的方法要快得多。在发表时(1986 年)，发现 DLists 对某些应用有实际的性能改进。

今天的列表怎么样？正如流行的哈斯克尔包 [`dlist`](http://hackage.haskell.org/package/dlist) 所言:

> 差异列表是一种类似列表的类型，支持 O(1) append。这对于高效的日志记录和漂亮的打印(例如使用 Writer monad)特别有用，因为列表追加很快就会变得非常昂贵。

这个包将 DLists 巧妙而微小的实现隐藏在一个易于使用的 API 后面；用 JavaScript 也可以很容易地完成类似的工作。

README 有关于这个主题的其他有趣资源的链接。最终，我认为 DList 实现是值得研究的，因为它展示了如何通过函数惰性地表示数据，可以释放新的和令人惊讶的能力。这在其他功能性表现中也有体现，比如[教堂编码](https://en.wikipedia.org/wiki/Church_encoding)。

休斯的论文是一个 4 页的小宝石，而这篇文章是…不是。不过，希望它能让典型的 JavaScript 程序员更容易理解这个想法。如果是这样的话，将来我可能会在这个系列中添加更多的条目。
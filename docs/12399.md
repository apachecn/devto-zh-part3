# F#中的纯函数式编程

> 原文：<https://dev.to/kspeakman/mere-functional-programming-in-f-do8>

***结构化编程遇上函数式编程***

现在，我一直在练习 F#函数式编程的简化版本。到目前为止，这种风格的 FP 对我的同事来说比较容易掌握，也很好维护。以下是我对自己如何保持简单的解释。

除非另有说明，所有代码都是 F#代码。

# 配料

仅仅进行函数式编程的基本方法是解决数据类型和函数的问题。这两个元素以结构化编程的三种基本方式相互作用:排序、选择和迭代。

## 数据类型

数据类型特指记录和有区别的联合。您可以将这两种类型组合在一起来表示任何数据结构。

> **🔔没有类**
> 我不使用面向对象类写代码。来自 F#的信息是，当有意义时，使用对象编程是可以的。但是我只在必要的时候使用对象来与现有的集成。网络图书馆。

### 记录

记录用于将多条数据捆绑在一起。它们是不可变的(一旦创建就不能更改),默认情况下值相等。例如:

```
type Person =
    {
        FirstName: string
        LastName: string
    } 
```

Enter fullscreen mode Exit fullscreen mode

`Person`的每个实例都有`FirstName`和`LastName`。如果不提供这两项数据，您将无法创建人员记录。

```
let joebob = { FirstName = "Joe"; LastName = "Bob" }
let jimbob = { FirstName = "Jim"; LastName = "Bob" } 
```

Enter fullscreen mode Exit fullscreen mode

此外，记录有一个“更新”语法。由于原始记录无法更改，因此此更新语法会为您指定的属性创建一个包含不同数据的新记录副本。例如，我们可以这样定义`jimbob`:

```
let joebob = { FirstName = "Joe"; LastName = "Bob" }
// jimbob created with update syntax
let jimbob = { joebob with FirstName = "Jim" }

// { FirstName = "Jim"; LastName = "Bob" } = jimbob 
```

Enter fullscreen mode Exit fullscreen mode

### 受歧视的工会

du 用于表示几种不同可能性中的一种。每个 DU 盒可以携带它自己不同种类的数据。Scott Wlaschin 在他的 [DDD 视频](https://fsharpforfunandprofit.com/ddd/)中给出的一个很好的例子是支付信息。

```
type PaymentType =
    | Cash
    | Check of checkNumber:int
    | CreditCard of cardNumber:string * expiration:string 
```

Enter fullscreen mode Exit fullscreen mode

可能有几种不同的付款方式，但给定的付款方式只能是其中一种。

```
let payment1 = Cash
let payment2 = Check 12345
// even though payment1 and payment2 are different cases,
// they are the same type
let payments : List<PaymentType> =
    [ payment1; payment2 ] 
```

Enter fullscreen mode Exit fullscreen mode

> **🔔与其他语言概念的比较**
> 都与其他语言中的枚举在目的上非常相似。区别在于枚举不能携带任何额外的数据。而 DU 病例可能各自保存不同种类的数据。
> 
> 在面向对象编程中，这种数据类型类似于具有浅继承的抽象类。区别在于每个子对象传统上都携带数据和行为，而 DU cases 只携带值。

## 功能

函数接受值，执行一些计算，然后返回值。关于函数，我想做一个主要的区分。函数是确定性的吗？确定性意味着函数不会产生副作用，其输出值仅取决于其输入值。确定性函数也被称为“纯”函数或“参照透明”函数。

这是 F#中非确定性函数与确定性函数的一个很好的例子。

```
// non-deterministic
let sortableTimeStamp () =
    DateTimeOffset.UtcNow.ToString("yyyyMMddHHmmss")

// deterministic
let sortableTimeStamp (now : DateTimeOffset) =
    now.UtcDateTime.ToString("yyyyMMddHHmmss") 
```

Enter fullscreen mode Exit fullscreen mode

第一个函数每次都会给出不同的结果，因为它是从一个不断变化的全局时钟中读取的。但是第二个函数每次输入相同的数据都会得到相同的结果。因此很容易测试。

总是需要一些有副作用的函数。但是**所有重要的决策都应该用确定性函数**来做。这样，他们的行为可以通过简单的测试来验证。

这个想法听起来很容易，但生活很少是直截了当的。我经常发现我需要将副作用和决策交织在一起。例如，做一个初始决策，然后加载一些数据(一个副作用)，然后使用这些数据做进一步的决策。找出实现确定性决策目标的方法是一次有趣的旅程。对此我有一个策略，我将在以后的文章中介绍。那个帖子可以在找到[。](https://dev.to/kspeakman/testable-back-end-programming-in-f-41n5)

# 相互作用

类型和功能是基本成分。这些有一个明显的核心相互作用。也就是说，您将值传递给函数以获得返回值。除此之外，我们可以使用结构化编程中定义的交互来建模程序。让我们走一遍。

## 结构化编程

根据结构化编程[定理](https://en.wikipedia.org/wiki/Structured_program_theorem)，有 3 种基本编程模式:

*   顺序——一条语句接着一条语句执行
*   选择——又名分支，通常使用`if`和`switch` / `case`
*   迭代——又名循环，通常使用`for`和`while`

这三者的组合足以表达任何可计算的函数。这个定理最初是使用命令式编程开发的。为了适应 FP，一些翻译是必要的。

### 语句 vs 表情

结构化编程被设计成使用“语句”。这些都是副作用——尤其是变异一个变量。但是在 FP 中，我们用表达式代替语句。主要区别在于表达式总是返回值，而语句会变异并返回 void。*甚至命令式编程中的`return`也是执行变异(设置堆栈帧的返回值)并退出的语句。*

> 一个基于表达式的函数调用说:这里有一些值——计算一个答案并返回给我。一个基于语句的函数调用说:这里有一些位置，从这些位置读取数据进行计算，然后把结果放到另一个位置，当你完成后，我会检查结果的位置。

当然，现代命令式语言已经抽象出了基于位置的思想，并且似乎足够接近返回值。但是核心语言原语仍然要求你运行语句，鼓励基于突变的思维。相反，在 F#中，一切都返回值的事实是如此普遍，以至于`return`不是一个标准的关键字 ***** 。因为这就像你每次呼气时都说“呼气”。

> ***** 从技术上讲，`return`是 F#中的特征级关键字，只在计算表达式内部使用。否则，这意味着返回值。为了比较:
> 
> ```
> // C#
> int add(int a, int b)
> {
>     return a + b;
> }
> // add(1,2) == 3 
> ```
> 
> ```
> // F#
> let add a b =
>     a + b
> // add 1 2 = 3 
> ```

### 测序

在结构化编程中，顺序意味着一个接一个地运行语句。为了理解 FP 的顺序，我必须先解释一下语句和表达式。在命令式语言中，序列非常明显。在一个代码块中，我可以执行任意多的语句。但是在 FP 中，表达式总是返回值。这导致了两种表示“顺序”的主要方式:`let`语句和管道。

#### 同`let`

```
let calcHyp a b =
    let aSquared = a ** 2.0
    let bSquared = b ** 2.0
    let cSquared = aSquared + bSquared
    sqrt cSquared

// calcHyp 3.0 4.0 = 5.0 
```

Enter fullscreen mode Exit fullscreen mode

上面，缩进到`calcHyp`下面的所有内容都被认为是一个表达式。每个中间值都使用关键字`let`命名。最后一行是返回值。

#### 用管道

下面是一个使用管道操作符(`|>`)来执行一系列步骤的例子。

```
let totalLength stringList =
    stringList
    |> List.map String.length
    |> List.sum

// totalLength [ "foo"; "bazaar" ] = 9 
```

Enter fullscreen mode Exit fullscreen mode

管道操作符(`|>`)获取之前的所有内容，并将其作为最后一个参数传递给下一个函数。这对于处理列表非常有用，因为它们通常是人类按顺序思考步骤的方式。备选方案可能更难阅读:

```
// without pipes
let totalLength stringList =
    List.sum (List.map String.length stringList) 
```

Enter fullscreen mode Exit fullscreen mode

### 选择

在结构化编程中，通常会使用“选择”或分支在特定条件下执行一组语句，否则执行另一组语句。这些通常采用`if`和`switch` / `case`语句的形式。

所以就像上面的“排序”一样，FP 中的分支不执行语句。相反，每个分支返回一个表达式(一个值或返回值的代码)。

```
let isEven i =
    if i % 2 = 0 then
        "affirmative"
    else
        "negative"
// isEven 2 = "affirmative"
// isEven 3 = "negative" 
```

Enter fullscreen mode Exit fullscreen mode

但是正如你在“排序”中看到的，一个表达式仍然可以在分支中执行多个步骤。

```
let weirdIsEven i =
    if i % 2 = 0 then
        let digits = [ "1"; "0"; "0" ]
        let s = String.concat "" digits
        sprintf "%s percent affirmative" s
    else
        "negatory"

// weirdIsEven 2 = "100 percent affirmative"
// weirdIsEven 3 = "negatory" 
```

Enter fullscreen mode Exit fullscreen mode

F#将分支提升到一个全新的水平，因为`match`带有 DUs。您不再需要弄清楚如何将数据分解成一系列布尔校验。你可以申报你在阿杜的所有案件。然后用`match`分支。

```
type My2dShape =
    | Circle of radius:float
    | Square of side:float
    | RightTriangle of base:float * height:float

let area shape =
    match shape with
    | Circle radius ->
        Math.PI * (radius ** 2.0)
    | Square side ->
        side ** 2.0
    | RightTriangle (base, height) ->
        0.5 * base * height 
```

Enter fullscreen mode Exit fullscreen mode

实际上，你可以使用`match`作为唯一的分支原语。它适用于布尔、枚举和其他基本类型。

```
let isEven i =
    match i % 2 = 0 with
    | true -> "affirmative"
    | false -> "negative"

// isEven 2 = "affirmative"
// isEven 3 = "negative" 
```

Enter fullscreen mode Exit fullscreen mode

这里就不赘述了，但是`match`有非常能干的模式匹配特性。包括使用`when`的保护语句、记录/DU 分解等。

### 迭代

循环遍历集合是如此普遍，以至于为此有一整套预建的函数。包括常见的`map`、`filter`、`reduce`。

> **🔔`for`和`while`**
> F#有命令式循环结构`for`和`while`。但是我避免使用这些。

有些情况下，我需要继续执行一些操作，直到遇到退出条件。在这些情况下，我使用递归函数。我记得在 CS 课上很讨厌这些，但是在 FP 里我觉得没那么繁重。尤其是当我用确定性函数来写它们的时候。这里有一个简单的。递归函数需要关键字`rec`。

```
/// Sums the first N positive integers, starting with an initial sum.
let rec sumInts sum n =
    match n <= 0 with
    | true ->
        sum
    | false ->
        let nextSum = sum + n
        let nextN = n - 1
        sumInts nextSum nextN

// sumInts 0 3 = 6 
```

Enter fullscreen mode Exit fullscreen mode

该函数要求您提供一个初始和(此处为零)以及要求和的整数个数。有时不需要提供初始值。因此，您经常会看到递归循环被包装在隐藏初始值的外部函数中。

```
/// Sums the first N positive integers.
let sumInts n =
    let rec loop sum i =
        match i <= 0 with
        | true ->
            sum
        | false ->
            let nextSum = sum + i
            let nextI = i - 1
            loop nextSum nextI

    // initiate the loop starting with sum = 0 
    loop 0 n

// sumInts 3 = 6 
```

Enter fullscreen mode Exit fullscreen mode

从历史上看，如果迭代次数过多，递归循环会导致堆栈溢出错误。但是这个递归函数的两个版本在编译期间都会被转换成`while`循环。因此它们可以循环任意次而不会溢出。这被称为“尾部调用优化”或 TCO。

> 很容易确保你的递归函数得到尾部调用优化。简单地预计算下一次循环迭代所需的值，就像我在这个例子中用`nextSum`和`nextI`所做的那样。然后用这些值调用循环。

## 结果

我相信这种纯粹的函数式编程正是结构化编程的初衷。但是结构化编程使得编写具有可变共享状态的有问题的代码变得非常容易。命令式的、基于语句的语言进一步鼓励了这一点。函数式编程通过增加做错事的难度来改善等式。尤其是当您用不可变类型和确定性函数来编码所有重要决策时。

# 未选择的路径

我上面描述的可以支持很多不同的模式。以至于我想明确地列出一些我倾向于避免放在代码库中的东西。

## 范畴论

在我在网上看到的许多函数式编程材料中，学习范畴理论被认为是先决条件。但这并不是完成实际工作的必要知识。当我向开发人员展示如何对整数列表求和时，我不会从解释[环理论](https://en.wikipedia.org/wiki/Ring_theory)开始。*(你呢？)*那么，在向开发者展示如何使用`reduce`和`map`之前，我为什么需要先解释幺半群和幺半群呢？

到目前为止，我们已经在我工作的 FP 中从头开始培训开发人员(现在有 3 到 4 个)。范畴理论不是我们训练过程的一部分，我们也不会在代码库中为它创建抽象。没有一个开发者注意到它的缺失。在完成真正工作的道路上，这甚至不是一个减速带。

这并不是说范畴理论不重要。我确实期待开发人员注意到一种模式并询问它。向一个饥饿的人揭示宇宙的奥秘是多么令人兴奋啊！但是写软件的实际业务不需要它。更糟糕的是，引入范畴理论往往会在初学者中造成更陡峭的学习曲线——以及过于聪明的代码。

## `private`关键词

开发人员可能出于不同的原因想要使用`private`关键字。我会仔细检查我能想到的。在大多数情况下，我已经找到了更好的选择。我甚至没有把它作为一个特性，所以我的开发人员没有使用过它。

### 月数据

私有数据总是会创建一个对象。也就是私有数据要和公共行为绑定；数据和行为绑定在一起就是一个对象。

私有数据通常用公共行为(修改私有数据)的方法表示为一个类。但是您也可以通过将 record 或 DU 细节设为私有来在函数 F#中使用私有数据。这允许定义数据的模块具有可以访问私有数据的函数，但是其他地方不能。尽管这是用函数表达的，但它仍然是行为与数据的绑定，并且由于访问模式的原因，它最终开始将使用它的代码变得更加基于对象。

函数式风格优于面向对象的一个优点是数据和行为是分离的。因此，我可以将数据与其他数据组合在一起。或者我可以将行为与其他行为分开。但是对于对象，一个对象中的数据和行为的组合必须与另一个对象的组合组合在一起。随着程序的增长，这种二维组合变得越来越难以理解，甚至难以解决。

所以，我们避免使用私人数据。

### 开启功能

我倾向于使用私有函数的常见情况是为了避免助手函数污染名称空间。(基本上是为了限制 Intellisense 显示的内容。)你也可以使用。FSI 文件隐藏的东西，但我觉得这些交易一种污染的另一种(额外的文件)。相反，我通常使用嵌套模块来实现这个目的。

```
module Cool =

    module Helpers =
        // stuff that won't show up
        ...

    // shows up in intellisense as "Cool.<whatever>"
    let trick x =
        ...

    let idea y =
        ... 
```

Enter fullscreen mode Exit fullscreen mode

如果你习惯了面向对象的“封装”,你可能会指出这实际上并没有隐藏助手函数。仍然可以通过调用`Cool.Helpers.<whatever>`从外部访问它们。你可能是正确的，而且(对我来说)这是一种可取的行为。

我发现在使用图书馆的时候——尤其是在。NET 中的大多数库都是基于面向对象的——我想“重新组合”这个库。我想使用实际完成工作的核心函数，但是我想以不同的方式打包它。大多数时候，我很失望地发现，通过`private`，真正起作用的核心步骤对我来说是隐藏的。

保持函数的公共性，但是在主名称空间之外进行适当的组织有助于解决这个问题。它避免了名称空间污染，但允许真正的重用。

更不用说私有函数的可测试性这个经典问题了。

* * *

# 结论

亲爱的读者，感谢你的耐心。我很高兴有机会与你分享我对函数式编程的直接方法。我们在 web 应用程序的前端和后端都使用它。这对新开发人员来说并不难理解。实现解决方案非常简单。这在我的团队中取得了巨大的成功。

除了这些卑微的开始，我们还使用了其他模式。我强烈推荐 UI 编程的 MVU 模式(又名 Elm 架构)。对 MVU 的改编甚至可以用来表示后端工作流。

虽然这篇文章很长，但我确信我留下了一些没有回答的问题。下面随意问。

/∞

* * *

#### 后记

我把这篇文章提交给了 2019 年[申请的 F#挑战赛](http://foundation.fsharp.org/applied_fsharp_challenge)。它被选为[出类拔萃主题的获奖者之一](http://foundation.fsharp.org/results_applied_fsharp_2019)！
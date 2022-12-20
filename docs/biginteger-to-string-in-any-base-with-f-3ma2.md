# 用 F#将 BigInteger 转换为任意基数的字符串

> 原文：<https://dev.to/stuartblang/biginteger-to-string-in-any-base-with-f-3ma2>

我花了很多时间说服人们，F#不仅仅是数学和金融，它是一种伟大的通用语言，在许多领域都大放异彩(比如领域建模)。然而，我最近一直在玩一些数学，F#是我的首选，所以这里有一个帖子。

随着我们进行算术运算的数字变得越来越大，。网很快功亏一篑，`Int32.MaxValue`是`2147483647`，`Int64.MaxValue`是`9223372036854775807`。当我们需要超越这一点时，我们可以使用 [`BigInteger`](https://docs.microsoft.com/en-us/dotnet/api/system.numerics.biginteger?view=netstandard-2.0) 又名`bigint`(F #中的[类型缩写](https://fsharpforfunandprofit.com/posts/type-abbreviations/))，它可以表示任意大的整数。

我需要以任何给定的基数(不仅仅是十进制、二进制、十六进制)打印一个`bigint`...)，令我惊讶的是，没有现成的东西可以做到这一点。

### 代码

首先我想把一个`bigint`变成一个数字列表。

```
// int -> bigint -> int list
let bigintToDigits b source =
    let rec loop (b : int) num digits =
        let (quotient, remainder) = bigint.DivRem(num, bigint b)
        match quotient with
        | zero when zero = 0I -> int remainder :: digits
        | _ -> loop b quotient (int remainder :: digits)
    loop b source [] 
```

需要注意的事项；

*   内部函数是递归的(由`rec`关键字给出)，外部函数只是用初始的空数字列表来启动它。这里的`b`是基础(`base`是一个关键词，很不幸)。
*   在函数式语言中，递归经常被用来替代循环中的可变状态，在很多情况下，递归可以使算法/例程的表达更加自然。F#中的递归不会溢出堆栈，而且速度很快，前提是它像这种情况一样是尾部调用可优化的(即函数做的最后一件事是调用自己，或者返回)。
*   我们除以基数，取余数作为最低有效数字，然后我们将累加的数字和商一起传递到循环中，并继续下去，直到得到商`0`。
*   `bigint.DivRem`返回一个商和一个余数`out`参数，使用 F#我们可以把没有参数的方法视为返回结果和`out`参数的元组。
*   通常，我们可以将`0`作为文字进行模式匹配，但是因为`0I` (bigint zero)是一个“非主文字”，我们不允许将其作为常量模式使用。因此有了`when zero = 0I`语法。
*   `::`是 cons 运算符，我们可以用它来构造 F#列表，左边的部分被加到右边的部分之前。
*   F#直言不讳- `list<'a>`是一个链表，而 C# `List<T>`是 type 在 F#中缩写成它真正的样子，一个`ResizeArray`:

```
type ResizeArray<'T> = System.Collections.Generic.List<'T> 
```

现在我们有了一个数字列表，让我们用一个函数把它转换成一个字符串(目前只支持 16 进制)。

```
let digitsToString source =
    source
    |> List.map (fun (x : int) -> x.ToString("X").ToLowerInvariant())
    |> String.concat "" 
```

把这些放在一起，如果我们想打印一个基数为 16 的`bigint`，我们做:

```
let printBigintAsHex source =
    let bigintToHex = bigintToDigits 16
    source |> bigintToHex |>  digitsToString |> printf "%s" 
```

我在 Google 或 Stack Overflow 上找不到任何可以做到这一点的东西，所以我想我应该在这里分享它，这样下次有人寻找它时它就会显示出来。
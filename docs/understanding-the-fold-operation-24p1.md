# 了解折叠操作

> 原文：<https://dev.to/kspeakman/understanding-the-fold-operation-24p1>

我最近偶然看到唐·赛姆的一个视频，名为 [F# Code I Love](https://www.youtube.com/watch?v=26_W8MmUA60) 。它详细描述了一些可爱的(和顽皮的🎄)F#中的使用模式。我非常惊讶地看到`fold`被认为是一种不太好的使用模式。但我完全理解为什么。

> 🔎**什么是折叠？**
> 折叠是对一组数据的操作。列表、集合、散列表等都是“可折叠”的。Fold 负责遍历集合中的每个项目，并允许您定义循环内部的行为。事实上，大多数其他集合操作(过滤、映射、归约等)都可以写成 fold 语句。所以 fold 是一种“低级”方法，用您自己的定制行为来处理列表中的项目。它适用于内置集合函数没有您需要的行为的情况。在 Javascript 中， [`Array.reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 与用初始值调用时的 fold 相同。在 C#中，LINQ 方法 [`Aggregate`](https://docs.microsoft.com/en-us/dotnet/api/system.linq.enumerable.aggregate?view=netcore-2.2) 与 fold 相同。

刚开始学 F#的时候，发现 folds 很难理解。因此，我通常会避免使用它们，而是编写一个递归函数，用我的自定义行为遍历集合。(我的意思是，当递归是简单的方法时，这是非常糟糕的，对吗？)但是当我重构递归函数以将自定义行为从循环中分离出来时，我注意到递归循环部分可以很容易地用 fold 替换。

随着时间的推移，我想出了一种方法来处理折叠，使它们更容易开始，而不是在最后进行重构优化。我将用一个[编码挑战](https://www.codewars.com/kata/count-of-positives-slash-sum-of-negatives)来演示。

> 🏎️ **挑战**
> 给定一个整数列表，返回正数的计数和负数的和。
> 当提供列表`[1; 2; 3; 4; 5; 6; 7; 8; 9; 10; -11; -12; -13; -14; -15]`时，解决方案应该返回计数 10 和总和-65。

## #1 用一个起始值定义你期望的答案类型。

在这种情况下，我想要两个值作为结果。

```
let (count, sum) = ... 
```

两者都是整数。对于这两个答案，我们从零开始，然后加上。

```
let (count, sum) = (0, 0) 
```

如果你不确定具体问题的起始值应该是多少，你现在可以选择一个。你可以以后再尝试。

我可能只对一对值使用一个名称，而不是将值析构回 count 和 sum。

```
let initial = (0, 0) 
```

## #2 根据单项更新答案。

既然我们知道答案是什么样子(计数和求和)，我们可以定义一种基于单个输入值更新答案的方法。我首先描绘了`update`函数及其参数。

```
let update (count, sum) value =
    ... 
```

然后，我填写步骤，根据值返回更新后的计数和总和。

```
let update (count, sum) value =
    if value > 0 then
        (count + 1, sum)
    elif value = 0 then
        (count, sum)
    else
        (count, sum + value) 
```

这读起来非常好，几乎和我们如何向另一个人解释一样。"给定值时更新计数和总和。如果该值为正，则增加计数，但保持总和不变。如果值为零，则保持相同的计数和总和。否则(值为负)保持相同的计数，但将该值添加到总和中。

*注意:我们可以通过删除`elif`子句来节省几行代码，并且它会完全一样地工作。(总和加零相当于原样返回。)*

## #3 现在折叠是容易的部分。

所以剩下唯一要做的就是处理列表本身。由于提供了输入列表，我们已经为`fold`定义了所有必需的片段。*折叠步骤看起来总是与此相似。*T3】

```
let runChallenge inputList =
    inputList |> List.fold update initial 
```

这是所有东西放在一起的地方。

```
let initial = (0, 0)

let update (count, sum) value =
    if value > 0 then
        (count + 1, sum)
    elif value = 0 then
        (count, sum)
    else
        (count, sum + value)

let runChallenge inputList =
    inputList |> List.fold update initial 
```

这里是你如何运行它。

```
let input = [1;2;3;4;5;6;7;8;9;10;-11;-12;-13;-14;-15]
let (count, sum) = runChallenge input
printfn "Count: %i, Sum: %i" count sum
// Outputs
// Count: 10, Sum: -65 
```

## 结论

Fold 是处理数据集合的一个非常强大的工具。它可以让您专注于您的行为逻辑，而不必编写循环代码。但使用起来可能会令人困惑——你真的必须以正确的心态对待它，才能充分利用它。我在这里介绍的方法帮助我以一种富有成效的方式使用`fold`。希望它也能帮助其他人。

/∞
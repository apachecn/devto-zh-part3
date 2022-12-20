# F#很酷

> 原文：<https://dev.to/deciduously/f-is-pretty-cool-23dj>

我决定在 [F#](https://fsharp.org/) 中解决今年出现的代码问题。这不仅是我第一次使用这种语言，也是我第一次使用. NET。我对使用公共语言基础设施一无所知。当我习惯了一个全新的环境时，我期待着一个艰难而缓慢的开始，当我学会如何摆脱自我时，我将能够写下这个过程。

还没有发生。事实证明 F#很棒，非常容易使用，而且我没有被卡住。事实上，这可能是我从“全新的语言”到“解决 AoC 类型的问题”最快的速度。所以我打算改写那篇文章。

我确实被这些问题困住了，而且我不一定对我目前为止的实现感到满意——所有的都可以被优化——但是我的问题与 F#无关。文档是彻底的，我通常是一个谷歌远离。我要找的 NET 函数。如果你找不到任何与 F#相关的资料，大量的 C#文档也同样适用——我唯一的问题是不懂任何 C#,但它是 Java——足够让我跟上了！

公平地说，如果这是你的第一门母语，我不认为你会有同样的经历。但是即使我自己在 Haskell 和 OCaml 中有一点点经验，我也没有发现什么令人惊讶的地方。

几乎所有我需要的东西我都在 F# 的[之旅中找到了，而](https://docs.microsoft.com/en-us/dotnet/fsharp/tour)[语言参考](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/)涵盖了所有其他的东西。

还有一个很棒的网站，可以作为离线电子书下载: [F#寻找乐趣和利润](https://fsharpforfunandprofit.com/)。

我喜欢的一些东西:

管道:

```
util.applyClaims fileName
|> Seq.filter (fun el -> List.length el > 1)
|> Seq.length 
```

Enter fullscreen mode Exit fullscreen mode

列表计算:

```
// https://docs.microsoft.com/en-us/dotnet/fsharp/tour
let daysList = 
    [ for month in 1 .. 12 do
          for day in 1 .. System.DateTime.DaysInMonth(2017, month) do 
              yield System.DateTime(2017, month, day) ] 
```

Enter fullscreen mode Exit fullscreen mode

活跃模式:

```
// https://fsharpforfunandprofit.com/posts/convenience-active-patterns/
open System.Text.RegularExpressions
let (|FirstRegexGroup|_|) pattern input =
   let m = Regex.Match(input,pattern) 
   if (m.Success) then Some m.Groups.[1].Value else None  

// create a function to call the pattern
let testRegex str = 
    match str with
    | FirstRegexGroup "http://(.*?)/(.*)" host -> 
           printfn "The value is a url and the host is %s" host
    | FirstRegexGroup ".*?@(.*)" host -> 
           printfn "The value is an email and the host is %s" host
    | _ -> printfn "The value '%s' is something else" str

// test
testRegex "http://google.com/test"
testRegex "alice@hotmail.com" 
```

Enter fullscreen mode Exit fullscreen mode

我刚刚发现它用起来又好又光滑。从思想到代码并让它按预期工作并不难。过了一段时间，学习了所有关于在 Rust 中构建图形的知识后，还记得那是什么感觉真好！

我不喜欢的一些事情:

*   编译器错误，但是我被 Rust/Reason 宠坏了
*   必须使用 CLI 向解决方案添加内容，并引用包和内容。这是我自己对生态系统的不熟悉。

真的是这样，其他的我都很喜欢。至少这是我迄今为止使用 ML 语言的最大乐趣。

今天试试你的 F#！

题外话，有人有使用 Clojure CLR 的经验吗？似乎不太受欢迎，但总的来说是个好主意。

这篇文章并不是真的关于 AoC，但是如果你想玩的话，这里有一个“必须的”[回购](https://github.com/deciduously/aoc2018)链接。
# 用 Elm 解析圣经引用

> 原文：<https://dev.to/monty5811/parsing-bible-references-with-elm-3029>

*本文最初发布于[deanmontgomery.com](https://www.deanmontgomery.com)。*

这篇文章将简要介绍如何在 Elm 中编写一个简单的解析器。我们要解析的是圣经参考文献:圣经参考文献是一种速记，让我们可以快速查找圣经文本中的一段或多段经文。

听起来很简单...

## 参考是什么样子的？

圣经参考可以分为开始位置和结束位置。每个位置由一本书、一章和一节组成。

所以引用可能看起来像`Genesis 1:1 - Exodus 2:1`，它告诉我们从创世纪第一章的第一节开始，到出埃及记第二章的第一节结束。

但是引用也可能看起来像以下任何一种:

*   `Genesis 1` -整整一章
*   `Genesis 1:1`——一首诗
*   `Genesis 1:1-20`
*   `Genesis 1:20-2:24`
*   `Genesis 1-5` -多个完整章节
*   `Genesis 1 - Exodus 5`
*   `Genesis 1:1 - Exodus 5:20`
*   `Genesis 1:1 - Exodus 5`
*   `Genesis 1 - Exodus 5:20`

此外，圣经的一些书只有一个章节(如犹大书)，按照惯例，章节号从参考文献中删除。所以`Jude 2`是《犹大书》第一章(也是唯一一章)的第二节，而不是《犹大书》第二章的全部。

当我们编写解析器时，我们的目标是处理所有这些情况。

## 我们如何在 Elm 中做解析？

[elm/parser](https://package.elm-lang.org/packages/elm/parser/latest/) 是 elm 的创建者和维护者写的一个超级好看的解析库。这里我就不赘述了——如果你想深入了解，有很好的[教程](https://korban.net/posts/elm/2018-09-07-introduction-elm-parser/)和[会议演讲](https://2018.elm-conf.us/schedule/tereza-sokol/)。

我们将分两步解析圣经参考:

1.  我们将字符串解析成一系列语句
2.  然后，我们验证语句列表，检查它是否是有效的引用

### 获取报表列表

一个圣经参考可以有一个空格，一个冒号，一个分号，一个书名和一个数字，所以我们定义:

```
type Statement
    = BookName Book
    | Num Int
    | Dash
    | Colon 
```

Enter fullscreen mode Exit fullscreen mode

和一个将字符串转换成语句列表的解析器:

```
{-| A `List Statement` parser. We use `P.loop` to consume the whole string
-}
parser : P.Parser (List Statement)
parser =
    P.loop [] statementsHelp

statementsHelp : List Statement -> P.Parser (P.Step (List Statement) (List Statement))
statementsHelp revStmts =
    P.oneOf
        [ P.succeed (\stmt -> P.Loop (stmt :: revStmts))
            |. P.spaces
            |= statement
            |. P.spaces
        , P.succeed ()
            |> P.map (\_ -> P.Done (List.reverse revStmts))
        ]

{-| A `Statement` parser
-}
statement : P.Parser Statement
statement =
    P.oneOf
        [ P.map BookName (P.oneOf bookTokensList)
        , P.map (\_ -> Dash) (P.symbol "-")
        , P.map (\_ -> Colon) (P.symbol ":")
        , P.map Num P.int
        ] 
```

Enter fullscreen mode Exit fullscreen mode

有了这个解析器，我们现在可以把一个字符串变成一个`List Statement` :

```
parse : String -> Result String (List Statement)
parse str =
    P.run parser str 
```

Enter fullscreen mode Exit fullscreen mode

### 验证报表列表

现在我们要么有一个语句列表，比如`[Book Genesis, Colon, Num 1]`或者`[Book John, Colon, Num 2, Dash, Num 2]`等等。但是不能保证我们有一个有效的语句集合。例如，我们可能有`[Colon, Colon, Colon]`这显然是无效的，或者`[Book Genesis, Num 52]`看起来是有效的，但是《创世纪》只有 50 本书——所以它是无效的。

首先我们将定义一个`Reference`类型:

```
type alias Reference =
    { startBook : Book
    , startChapter : Int
    , startVerse : Int
    , endBook : Book
    , endChapter : Int
    , endVerse : Int
    } 
```

Enter fullscreen mode Exit fullscreen mode

还有一个函数`processStatements : List Statement -> Result String Reference`将验证我们的语句列表。考虑到所有可用的潜在格式和处理单章书籍，这个函数相当大，但是这个函数本质上是一个`case`语句:

```
processStatementsHelp : List Statement -> Result String Reference
processStatementsHelp stmts =
    case stmts of
        -- Gen
        [ BookName bk ] ->
            reference
                bk
                1
                1
                bk
                (numChapters bk)
                (numVerses bk (numChapters bk))

        -- Gen 1
        [ BookName bk, Num ch ] ->
            if numChapters bk == 1 then
                reference
                    bk
                    1
                    ch
                    bk
                    1
                    ch

            else
                reference
                    bk
                    ch
                    1
                    bk
                    ch
                    (numVerses bk 1)

    -- truncated for brevity (full function can be seen: https://github.com/monty5811/elm-bible/blob/2.0.0/src/Internal/Parser.elm#L38-L243)

        -- Genesis - Revelation
        [ BookName startBk, Dash, BookName endBk ] ->
            reference
                startBk
                1
                1
                endBk
                (numChapters endBk)
                (numVerses endBk (numChapters endBk))

        [] ->
            Err "No reference found"

        _ ->
            Err <| "No valid reference found" 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个`Reference`，它包含了开始书、开始章节、开始章节、结束章节和结束章节，但是我们没有检查所有这些都是有序的(例如，引用不能在开始之前结束)，所以我们使用最后一个函数来验证引用:

```
validateRef : Reference -> Result String Reference
validateRef ref =
    validateBookOrder ref
        |> Result.andThen validateChapterOrder
        |> Result.andThen validateVerseOrder
        |> Result.andThen validateChapterBounds
        |> Result.andThen validateVerseBounds

-- see each validate function here: https://github.com/monty5811/elm-bible/blob/2.0.0/src/Internal/Parser.elm#L363 
```

Enter fullscreen mode Exit fullscreen mode

终于！我们有一个有效的圣经参考！

**注意**我*认为*应该有可能将所有的验证都转移到解析器中，并且在一个步骤中完成所有的事情，但是我认为这是一种更干净的方法。

## 结论

这篇文章向你展示了如何在 Elm 中创建一个解析器，这样我们就可以验证圣经引用。希望这将帮助您开始构建一个解析器。

如果你不关心构建一个解析器，而只是想要一个 elm 包来为你做这件事，那么看看 [monty5811/elm-bible](https://package.elm-lang.org/packages/monty5811/elm-bible/latest/) 吧，它提供了一个解析器、漂亮的格式和一个紧凑的编码器/解码器。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[【monty 5811】](https://github.com/monty5811)/[【elm-bible】](https://github.com/monty5811/elm-bible)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 榆树-圣经

[![Build Status](img/c31d68a8ccd07dc7cd56f31b2888475e.png)](https://semaphoreci.com/monty5811/elm-bible)

解析和格式化 Elm 中的圣经引用。

## 特征

*   从字符串中解析引用
*   很好地将引用格式化为字符串
*   将引用转换为编码表示，以便排序/比较/存储

可以解析以下参考格式:

*   创世纪 1
*   创世纪 1:1
*   创世纪 1:1-20
*   创世纪 1:20-2:24
*   创世纪 1-5
*   创世纪 1 -出埃及记 5
*   创世纪 1:1 -出埃及记 5:20
*   创世纪 1:1 -出埃及记 5
*   创世纪 1 -出埃及记 5:20

## 例子

```
(fromString "Gen 1:1" |> Result.map format)
     == Ok "Genesis 1:1" 

(fromString "Gen 1:1 - Rev 5") |> Result.map format)
    == Ok "Genesis 1:1 - Revelation 5:14" 

(fromString "Gen 1:1 - Rev 5") |> Result.map encode) 
    == Ok {start = 1001001, end = 66005014}

```

Enter fullscreen mode Exit fullscreen mode

## 贡献的

欢迎投稿，请[开一期](https://github.com/monty5811/elm-bible/issues/new)开始。

</article>

[View on GitHub](https://github.com/monty5811/elm-bible)
# 用 Elm 编写单词记忆游戏——第 3 部分:重新思考模型

> 原文：<https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-3-rethinking-the-model-2ajp>

这是“在 Elm 中编写单词记忆游戏”系列的第 3 部分，查找:

*   [第 1 部分:使用 Parcel 设置 Elm 应用程序](https://dev.to/mickeyvip/writing-a-word-memory-game-inelm---part-1-setting-up-an-elm-application-with-parcel-1ppk)
*   [第二部分:建模和构建一个基本的单词记忆游戏](https://dev.to/mickeyvip/writing-a-word-memory-game-inelm---part-2-modeling-and-building-a-basic-word-memory-game-583g)
*   第 3 部分:反思模型
*   [第四部分:用随机性增加趣味](https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-4-spicing-things-up-with-randomness-58ei)
*   [第 5 部分-更多的随机性和更多的游戏](https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-5-more-randomness-and-more-game-1i37)

* * *

游戏现在可以玩了。酷毙了。但是看着现在的`Model`

```
type Word
    = SentenceWord ( Int, String )
    | HiddenWord ( Int, String )

type alias Words =
    List Word

type alias Model =
    { sentence : String
    , chosenWords : Words
    , chosenSentence : Words
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以发现几个问题:

*   `chosenSentence`和`chosenWords`是`Model`的两个独立部分，我们可能会不小心让它们不同步
*   由于`chosenWords`属于`Words`类型，它也可能包含`SentenceWord`
*   `sentence`也可能变得与其余的`Model`不同步
*   我们在`viewHiddenWord`中也有一些代码味道:

```
viewHiddenWord : Word -> List Word -> Html msg
viewHiddenWord hiddenWord chosenWords =

            -- omitted code

            wordElement : Word -> Html Msg                
                wordElement word =
                    case word of
                        HiddenWord ( _, wordString ) ->
                            viewOption wordString

                        SentenceWord ( _, wordString ) ->
                            viewOption wordString

            -- omitted code 
```

Enter fullscreen mode Exit fullscreen mode

可能更多。

在 Elm 中，我们希望投入额外的时间来对我们的模型进行良好的规划，并使它尽可能地接近消除应用程序无效状态的形式。

这个想法，我想并不新鲜，是由 Richard Feldman 在 Elm-Conf 2016 上的精彩(一如既往)演讲中提出的:

[https://www.youtube.com/embed/IcgmSRJHu_8](https://www.youtube.com/embed/IcgmSRJHu_8)

从那以后，我们经常在不同的社区听到*“让不可能的状态变得不可能”*，不仅仅是 Elm。

我们如何改进我们的`Model`并消除无效状态和代码味道的可能性？

代替拥有`chosenWords`和`sentenceWords`——我们可以只拥有`sentence`属性并将`sentence`重命名为`sentenceOriginal` :

```
type Word
    = SentenceWord ( Int, String )
    | HiddenWord ( Int, String )

type alias Words =
    List Word

type alias Model =
    { sentenceOriginal : String
    , sentence : Words
    } 
```

Enter fullscreen mode Exit fullscreen mode

看起来更好，但现在我们失去了原始句子的单词或玩家的输入，因为只有 1 个`String`占位符。良好的...我们可以将玩家的输入和原词放在同一个类型的构造函数中:

```
type Word
    = SentenceWord ( Int, String )
    | HiddenWord ( Int, String, String ) -- index, answer, player's choice 
```

Enter fullscreen mode Exit fullscreen mode

此外，也许我们可以在渲染`sentence`时使用`List.indexedMap`,这将为我们提供索引:
,而不是使用也会失去同步的`index`

```
type Word
    = SentenceWord String
    | HiddenWord String String  -- answer, player's choice

type alias Words =
    List Word

type alias Model =
    { originalSentence : String
    , sentence : Words
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以更进一步建模，用具体的类型
代替`String`

```
type PlayerChoice =
    PlayerChoice String    

type Answer =
    Answer String    

type Word
    = SentenceWord String
    | HiddenWord Answer PlayerChoice

type alias Words =
    List Word

type alias Model =
    { originalSentence : String
    , sentence : Words
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将防止我们把答案(也就是`String`)和玩家的输入(也是`String`)以错误的顺序传递给`HiddenWord`。代码现在可读性也更好了。

我们现在需要的几乎都有了。然而，仍然缺少随机选择单词的能力。为了解决这个问题，我们可以在`HiddenWord`上加一个`SortKey`:

```
type PlayerChoice =
    PlayerChoice String    

type Answer =
    Answer String    

type SortKey =
    SortKey Int

type Word
    = SentenceWord String
    | HiddenWord SortKey Answer PlayerChoice

type alias Words =
    List Word

type alias Model =
    { originalSentence : String
    , sentence : Words
    } 
```

Enter fullscreen mode Exit fullscreen mode

当类型构造函数增长时，将其转换为记录可能会更好。让我们把`HiddenWord`转换成一条记录:

```
type alias HiddenWord =
    { sortKey : Int
    , playerChoice : PlayerChoice
    , answer : Answer
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们不需要`SortKey`，只要`Int`就够了:

```
type PlayerChoice =
    PlayerChoice String    

type Answer =
    Answer String    

type alias HiddenWord =
    { sortKey : Int
    , playerChoice : PlayerChoice
    , answer : Answer
    }

type Word
    = SentenceWord String
    | HiddenWord HiddenWord

type alias Words =
    List Word

type alias Model =
    { originalSentence : String
    , sentence : Words
    } 
```

Enter fullscreen mode Exit fullscreen mode

此时 Elm 会抱怨 2 个`HiddenWord`类型定义。

[![Compiler complains](img/af776a6125002aa789b7f1ece23df025.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8kNSFRsm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jl32fxkb7nh1q237igit.png)

我们需要不同的名字。我把`SentenceWord`和`HiddenWord`改名为`SentenceWrd`和`HiddenWrd` :

```
type Word
    = SentenceWrd String
    | HiddenWrd HiddenWord 
```

Enter fullscreen mode Exit fullscreen mode

在这次改变之后，我们有很多需要重构的地方。幸运的是，我们的背后覆盖着 Elm 编译器。让我们更新当前的`initialModel` :

```
type alias Model =
    { sentence : String
    , chosenWords : Words
    , chosenSentence : Words
    }

initialModel : Model
initialModel =
    { originalSentence = "The pen is mightier than the sword"
    , chosenWords =
        [ HiddenWord ( 1, "pen" )
        , HiddenWord ( 6, "sword" )
        , HiddenWord ( 3, "mightier" )
        ]
    , chosenSentence =
        [ SentenceWord ( 0, "The" )
        , HiddenWord ( 1, "" )
        , SentenceWord ( 2, "is" )
        , HiddenWord ( 3, "" )
        , SentenceWord ( 4, "than" )
        , SentenceWord ( 5, "the" )
        , HiddenWord ( 6, "" )
        ]
    } 
```

Enter fullscreen mode Exit fullscreen mode

对此:

```
type alias Model =
    { sentence : Words
    }

initialModel : Model
initialModel =
    { sentence =
        [ SentenceWrd "The"
        , HiddenWrd
            { sortKey = 1
            , answer = Answer "pen"
            , playerChoice = PlayerChoice ""
            }
        , SentenceWrd "is"
        , HiddenWrd
            { sortKey = 3
            , answer = Answer "mightier"
            , playerChoice = PlayerChoice ""
            }
        , SentenceWrd "than"
        , SentenceWrd "the"
        , HiddenWrd
            { sortKey = 2
            , answer = Answer "sword"
            , playerChoice = PlayerChoice ""
            }
        ]
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们通过设置`sortKey`对所选单词的“随机”顺序进行了硬编码，还从模型中移除了`originalSentence`(目前)，并将`chosenSentence`重命名为`sentence`。

`update`函数是第一个我们可以轻松重构的函数。让我们改变:

*   `newSentence`使用`List.indexedMap`
*   `updateWord`将索引作为第一个参数

```
update : Msg -> Model -> Model
update msg model =
    case msg of
        WordChanged index wordString ->
            let
                updateWord : Int -> Word -> Word
                updateWord wordIndex word =
                    case word of
                        HiddenWrd hiddenWord ->
                            if wordIndex == index then
                                HiddenWrd
                                    { hiddenWord
                                        | playerChoice = PlayerChoice wordString
                                    }

                            else
                                word

                        _ ->
                            word

                newSentence : Words
                newSentence =
                    List.indexedMap updateWord model.sentence
            in
            { model | sentence = newSentence } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有一个单词的`index`来更新玩家的输入，还有当前单词的`wordIndex`来运行`List.indexedMap`。如果两个索引相等，我们需要更新`HiddenWrd`的`playerChoice`字段。

现在有很多编译器错误。让我们一步一步地注释掉所有有错误的函数，即`viewSentence`、`viewHiddenWord`、`viewChosenWords`以及它们在`view` :
中的调用

```
view : Model -> Html Msg
view model =
    main_ [ class "section" ]
        [ div [ class "container" ]
            [ viewTitle
            , div [ class "box" ]
                [ p
                    [ class "has-text-centered" ]
                    [ text model.sentence ]

                --                , viewSentence model.chosenSentence model.chosenWords
                --                , viewChosenWords model.chosenWords model.chosenSentence
                ]
            ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

编译器抱怨`text model.sentence`，因为`text`期望一个`String`作为输入，但是重构后`model.sentence`不是简单的`String`而是`Words`(它本身就是`List Word`的类型别名)。

首先我们需要一个可以将`Word`转换成`String` :
的东西

```
wordToString : Word -> String
wordToString word =
    case word of
        SentenceWrd wordString ->
            wordString

        HiddenWrd hiddenWord ->
            case hiddenWord.answer of
                Answer answerString ->
                    answerString 
```

Enter fullscreen mode Exit fullscreen mode

如果`word`是`SenteceWrd`，我们就取它的`String`部分。如果是`HiddenWrd`，我们将从中提取`Answer`，然后模式匹配得到它的`String`部分。

让我们添加一个`viewOriginalSentence`函数来渲染句子:

```
viewOriginalSentence : Words -> Html Msg
viewOriginalSentence words =
    p
        [ class "has-text-centered" ]
        (List.map
            (\word ->
                wordToString word |> text
            )
            words
        ) 
```

Enter fullscreen mode Exit fullscreen mode

我们将每个`Word`映射到一个`String`，并将`String`传递给`Html.text`。

让我们更新`view`函数以使用`viewOriginalSentence` :

```
view : Model -> Html Msg
view model =
    main_ [ class "section" ]
        [ div [ class "container" ]
            [ viewTitle
            , div [ class "box" ]
                [ p
                    [ class "has-text-centered" ]
                    [ viewOriginalSentence model.sentence ]

                --                , viewSentence model.chosenSentence model.chosenWords
                ]
                --                , viewChosenWords model.chosenWords model.chosenSentence
            ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

应用程序现在可以编译了！让我们看看它的样子:

[![Original sentence created from our model](img/8d3715987da62f225b43a59de3454a38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xR5UBY0E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/13uiwawe2ktk4qm4136a.png)

良好的...所有的单词都写得中间没有空格。我们需要添加一些空格，一种方法是在每个单词后添加一个空格:

```
viewOriginalSentence : Words -> Html Msg
viewOriginalSentence words =
    p
        [ class "has-text-centered" ]
        (List.map
            (\word ->
                wordToString word ++ " " |> text
            )
            words
        ) 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以使用 [`List.intersperse`](https://package.elm-lang.org/packages/elm/core/latest/List#intersperse) ，*将给定值放在给定列表的所有成员之间。*:

```
viewOriginalSentence : Words -> Html Msg
viewOriginalSentence words =
    p
        [ class "has-text-centered" ]
        (words
            |> List.map wordToString
            |> List.intersperse " "
            |> List.map text
        ) 
```

Enter fullscreen mode Exit fullscreen mode

或者事件更简单的有 [`String.join`](https://package.elm-lang.org/packages/elm/core/latest/String#join) :

```
viewOriginalSentence : Words -> Html Msg
viewOriginalSentence words =
    p
        [ class "has-text-centered" ]
        [ words
            |> List.map wordToString
            |> String.join " "
            |> text
        ] 
```

Enter fullscreen mode Exit fullscreen mode

现在游戏看起来更好了:

[![Original sentence created from our model with spaces between words](img/ab96452d9155d877315d11b111504149.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tg4d9kBL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r8a55sjxru5tc7qtb4gx.png)

在继续之前，让我们做一个小的重构。让我们添加`answerToString`助手函数，将`Answer`转换成`String`。我们可以从`wordToString`开始使用它，以后在比较玩家的选择和答案时可能会派上用场:

```
answerToString : Answer -> String
answerToString (Answer wordString) =
    wordString

wordToString : Word -> String
wordToString word =
    case word of
        HiddenWrd hiddenWord ->
            answerToString hiddenWord.answer

        SentenceWrd wordString ->
            wordString 
```

Enter fullscreen mode Exit fullscreen mode

太好了。下一行是所选单词的视图。让我们取消注释并检查一下:

```
viewChosenWords : Words -> Words -> Html msg
viewChosenWords chosenWords sentenceWords =
    let
        viewChosenWord : Word -> Html msg
        viewChosenWord chosenWord =
            case chosenWord of
                HiddenWord ( _, wordString ) ->
                    let
                        isCorrectGuess : Bool
                        isCorrectGuess =
                            List.member chosenWord sentenceWords

                        className : String
                        className =
                            if isCorrectGuess then
                                "has-text-success"

                            else
                                "has-text-grey-light"
                    in
                    li []
                        [ span [ class className ]
                            [ text wordString
                            , text " "
                            , span [ class "icon is-small" ]
                                [ i [ class "far fa-check-circle" ] [] ]
                            ]
                        ]

                SentenceWord _ ->
                    text ""
    in
    ul [] (List.map viewChosenWord chosenWords) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以改进的第一件事是签名。现在它将`chosenWords`作为`Words`，但它应该只处理`List HiddenWord`。

以前，我们需要`sentenceWords`来提示玩家的选择是否正确。现在我们有了`HiddenWord`中的所有信息，包括`playerChoice`和`answer`！

这意味着可以简化签名，使其更好地表达输入——我们在这里只能传递`List HiddenWord`:

```
viewChosenWords : List HiddenWord -> Html msg
viewChosenWords chosenWords = 
```

Enter fullscreen mode Exit fullscreen mode

我们也把`viewChosenWords`改名为`viewHiddenWords`吧，因为`hidden`和`chosen`都用了，而且很混乱:

```
viewHiddenWords : List HiddenWord -> Html msg
viewHiddenWords hiddenWordList = 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要一种从模型中获取`List HiddenWord`的方法。让我们加上这个能力。我们需要过滤掉`HiddenWrd`并从中提取`HiddenWord`(注意“Word”与“Wrd”)。Elm 用[列表覆盖了我们。filterMap](https://package.elm-lang.org/packages/elm/core/latest/List#filterMap) :

```
hiddenWords : Words -> List HiddenWord
hiddenWords sentence =
    List.filterMap
        (\word ->
            case word of
                HiddenWrd hiddenWord ->
                    Just hiddenWord

                _ ->
                    Nothing
        )
        sentence 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以更新`view`来调用它:

```
view : Model -> Html Msg
view model =
    main_ [ class "section" ]
        [ div [ class "container" ]
            [ viewTitle
            , div [ class "box" ]
                [ p
                    [ class "has-text-centered" ]
                    [ viewOriginalSentence model.sentence

                    --                , viewSentence model.chosenSentence model.chosenWords
                    ]
                , viewHiddenWords (hiddenWords model.sentence)
                ]
            ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

回到`viewHiddenWords`。它有一个内部的`viewChosenWord`功能。让我们将其重命名为`viewHiddenWord`并更改签名，因为我们现在只有`HiddenWord` :

```
 viewHiddenWord : HiddenWord -> Html msg
        viewHiddenWord hiddenWord = 
```

Enter fullscreen mode Exit fullscreen mode

出于同样的原因，我们不再需要`case`——我们现在只有`HiddenWord`,所以我们可以删除它:

```
viewHiddenWords : List HiddenWord -> Html msg
viewHiddenWords hiddenWordList =
    let
        viewHiddenWord : HiddenWord -> Html msg
        viewHiddenWord hiddenWord =
            let
                isCorrectGuess : Bool
                isCorrectGuess =
                    List.member hiddenWord sentenceWords

                className : String
                className =
                    if isCorrectGuess then
                        "has-text-success"

                    else
                        "has-text-grey-light"
            in
            li []
                [ span [ class className ]
                    [ text wordString
                    , text " "
                    , span [ class "icon is-small" ]
                        [ i [ class "far fa-check-circle" ] [] ]
                    ]
                ]
    in
    ul [] (List.map viewHiddenWord hiddenWordList) 
```

Enter fullscreen mode Exit fullscreen mode

我们怎么知道玩家的选择是正确的选择？正如我之前提到的，我们在`HiddenWord`类型中已经拥有了我们所需要的一切:

```
type alias HiddenWord =
    { sortKey : Int
    , playerChoice : PlayerChoice
    , answer : Answer
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们所需要的就是比较`payerChoice`和`answer`。它们都是集装箱类型:

```
type PlayerChoice
    = PlayerChoice String

type Answer
    = Answer String 
```

Enter fullscreen mode Exit fullscreen mode

所以我们需要从它们中提取出`String`值。让我们在之前声明的`answerToString` :
之外再加上`playerChoiceToString`

```
playerChoiceToString : PlayerChoice -> String
playerChoiceToString (PlayerChoice stringValue) =
    stringValue

answerToString : Answer -> String
answerToString (Answer stringValue) =
    stringValue 
```

Enter fullscreen mode Exit fullscreen mode

使用析构非常简单。

现在我们可以在`isCorrectGuess` :
中使用它们

```
viewHiddenWord : HiddenWord -> Html msg
        viewHiddenWord hiddenWord =
            let
                isCorrectGuess : Bool
                isCorrectGuess =
                    answerToString hiddenWord.answer == playerChoiceToString hiddenWord.playerChoice 
```

Enter fullscreen mode Exit fullscreen mode

`className`不需要改变。但是`li`声明又需要一个`String`，而`wordString`原来是

```
 li []
                [ span [ class className ]
                    [ text wordString
                    , text " "
                    , span [ class "icon is-small" ]
                        [ i [ class "far fa-check-circle" ] [] ]
                    ]
                ] 
```

Enter fullscreen mode Exit fullscreen mode

那里应该有什么？当然是来自`hiddenWord.answer`的`String`！

```
 li []
                [ span [ class className ]
                    [ text <| answerToString hiddenWord.answer
                    , text " "
                    , span [ class "icon is-small" ]
                        [ i [ class "far fa-check-circle" ] [] ]
                    ]
                ] 
```

Enter fullscreen mode Exit fullscreen mode

我们可以稍微重构一下，通过添加两个声明来重用`answerToString hiddenWord.answer`的值:

```
viewHiddenWord : HiddenWord -> Html msg
        viewHiddenWord hiddenWord =
            let
                answerString : String
                answerString =
                    answerToString hiddenWord.answer

                playerChoiceString : String
                playerChoiceString =
                    playerChoiceToString hiddenWord.playerChoice 
```

Enter fullscreen mode Exit fullscreen mode

还有:

```
 isCorrectGuess : Bool
                isCorrectGuess =
                    answerString == playerChoiceString

-- omitted code

            li []
                [ span [ class className ]
                    [ text answerString
                    , text " "
                    , span [ class "icon is-small" ]
                        [ i [ class "far fa-check-circle" ] [] ]
                    ]
                ] 
```

Enter fullscreen mode Exit fullscreen mode

而现在我们的`viewHiddenWords`长这样:

```
viewHiddenWords : List HiddenWord -> Html msg
viewHiddenWords hiddenWordList =
    let
        viewHiddenWord : HiddenWord -> Html msg
        viewHiddenWord hiddenWord =
            let
                answerString : String
                answerString =
                    answerToString hiddenWord.answer

                playerChoiceString : String
                playerChoiceString =
                    playerChoiceToString hiddenWord.playerChoice

                isCorrectGuess : Bool
                isCorrectGuess =
                    answerString == playerChoiceString

                className : String
                className =
                    if isCorrectGuess then
                        "has-text-success"

                    else
                        "has-text-grey-light"
            in
            li []
                [ span [ class className ]
                    [ text answerString
                    , text " "
                    , span [ class "icon is-small" ]
                        [ i [ class "far fa-check-circle" ] [] ]
                    ]
                ]
    in
    ul [] (List.map viewHiddenWord hiddenWordList) 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看我们的游戏:

[![Game with hidden words](img/346b7865ad95faff944ad5ba8f2a0c58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SGl4MTd9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/abve6fjs8ohnkonfww5y.png)

看起来不错，但是我们遗漏了一个细节——隐藏的单词以它们在句子中出现的顺序出现...如果你记得我们在我们的`HiddenWord`中添加了`sortKey`以便能够随机显示它们:

```
type alias HiddenWord =
    { sortKey : Int
    , playerChoice : PlayerChoice
    , answer : Answer
    } 
```

Enter fullscreen mode Exit fullscreen mode

目前，我们将随机订单硬编码在`initialModel` :
中

```
initialModel : Model
initialModel =
    { sentence =
        [ SentenceWrd "The"
        , HiddenWrd
            { sortKey = 3
            , answer = Answer "pen"
            , playerChoice = PlayerChoice ""
            }
        , SentenceWrd "is"
        , HiddenWrd
            { sortKey = 1
            , answer = Answer "mightier"
            , playerChoice = PlayerChoice ""
            }
        , SentenceWrd "than"
        , SentenceWrd "the"
        , HiddenWrd
            { sortKey = 2
            , answer = Answer "sword"
            , playerChoice = PlayerChoice ""
            }
        ]
    } 
```

Enter fullscreen mode Exit fullscreen mode

所以剩下要做的就是在显示之前按照`sortKey`对隐藏的单词进行排序。我们将添加`hiddenWordsSorted`并使用它:

```
viewHiddenWords : List HiddenWord -> Html msg
viewHiddenWords hiddenWordList =
    let
        hiddenWordsSorted : List HiddenWord
        hiddenWordsSorted =
            List.sortBy .sortKey hiddenWordList

        viewHiddenWord : HiddenWord -> Html msg
        viewHiddenWord hiddenWord =

-- omitted

    in
    ul [] <| List.map viewHiddenWord hiddenWordsSorted 
```

Enter fullscreen mode Exit fullscreen mode

[![Randomly sorted hidden words](img/109dc81bd71bf859fc534526b918c3f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QqQkVat0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6gpie62v25s3arb3p8mw.png)

我想做的最后一个小的重构是`wordToString` :

```
wordToString : Word -> String
wordToString word =
    case word of
        SentenceWrd wordString ->
            wordString

        HiddenWrd hiddenWord ->
            case hiddenWord.answer of
                Answer answerString ->
                    answerString 
```

Enter fullscreen mode Exit fullscreen mode

我们有`answerToString`，所以我们可以在`case`的第二个分支中使用它:

```
wordToString : Word -> String
wordToString word =
    case word of
        SentenceWrd wordString ->
            wordString

        HiddenWrd hiddenWord ->
            answerToString hiddenWord.answer 
```

Enter fullscreen mode Exit fullscreen mode

或者更短:

```
wordToString : Word -> String
wordToString word =
    case word of
        SentenceWrd wordString ->
            wordString

        HiddenWrd { answer } ->
            answerToString answer 
```

Enter fullscreen mode Exit fullscreen mode

最后一部分- `viewSentence`。让我们在`view`函数中取消它的注释。

```
 viewSentence model.chosenSentence model.chosenWords 
```

Enter fullscreen mode Exit fullscreen mode

自从我们取代了`sentence`之后，就再也没有`chosenSentence`了，我们也去掉了额外的`chosenWords`。我们确实需要将`List HiddenWord`发送给`viewSentence`，这样它就知道作为`<select>`元素呈现什么。我们已经为它准备了一个助手函数——`hiddenWords`——在调用`viewHiddenWords`时使用。所以`viewSentence`可能是这样的:

```
view : Model -> Html Msg
view model =

-- omitted
                    [ viewOriginalSentence model.sentence
                    , viewSentence model.sentence (hiddenWords model.sentence)
                    ] 
```

Enter fullscreen mode Exit fullscreen mode

看起来我们正在发送`model.sentence`到`viewSentence`并使用它来获得隐藏的单词...我们不需要发送`(hiddenWords model.sentence)`，因为`viewSentence`可以自己计算它们！

```
view : Model -> Html Msg
view model =
    main_ [ class "section" ]
        [ div [ class "container" ]
            [ viewTitle
            , div [ class "box" ]
                [ p
                    [ class "has-text-centered" ]
                    [ viewOriginalSentence model.sentence
                    , viewSentence model.sentence
                    ]
                , viewHiddenWords (hiddenWords model.sentence)
                ]
            ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

让我们取消对`viewSentence`的注释，并将它的签名从:

```
viewSentence : Words -> Words -> Html Msg
viewSentence sentence chosenWords = 
```

Enter fullscreen mode Exit fullscreen mode

至

```
viewSentence : Words -> Html Msg
viewSentence sentence = 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将获得隐藏单词:

```
viewSentence : Words -> Html Msg
viewSentence sentence =
    let
        hiddenWords_ =
            hiddenWords sentence
    in 
```

Enter fullscreen mode Exit fullscreen mode

并将其余的`viewSentence`更新为:

```
viewSentence : Words -> Html Msg
viewSentence sentence =
    let
        hiddenWords_ : List HiddenWord
        hiddenWords_ =
            hiddenWords sentence
    in
    div [ class "has-text-centered" ]
        (List.map
            (\sentenceWord ->
                case sentenceWord of
                    SentenceWrd word ->
                        span [ class "sentence-word" ] [ text word ]

                    HiddenWrd hiddenWord ->
                        span [ class "sentence-word" ] [ text (answerToString hiddenWord.answer) ]
             --                        viewHiddenWord sentenceWord hiddenWords_
            )
            sentence
        ) 
```

Enter fullscreen mode Exit fullscreen mode

我已经注释掉了对`viewHiddenWord`的调用，因此我们可以看到中间结果:

[![Intermediate sentence render](img/54d117a6fb053352b93eca9c15290462.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BWETpqy6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zcvmgquvgygtp88vm3c4.png)

现在让我们取消对`viewHiddenWord`的注释，并返回`viewSentence` :
中的行

```
viewSentence : Words -> Html Msg
viewSentence sentence =
    let
        hiddenWords_ : List HiddenWord
        hiddenWords_ =
            hiddenWords sentence
    in
    div [ class "has-text-centered" ]
        (List.map
            (\sentenceWord ->
                case sentenceWord of
                    SentenceWrd word ->
                        span [ class "sentence-word" ] [ text word ]

                    HiddenWrd hiddenWord ->
                         viewHiddenWord sentenceWord hiddenWords_
            )
            sentence
        ) 
```

Enter fullscreen mode Exit fullscreen mode

我们需要把`sentenceWord`改成`hiddenWord` :

```
viewSentence : Words -> Html Msg
viewSentence sentence =
    let
        hiddenWords_ : List HiddenWord
        hiddenWords_ =
            hiddenWords sentence
    in
    div [ class "has-text-centered" ]
        (List.map
            (\sentenceWord ->
                case sentenceWord of
                    SentenceWrd word ->
                        span [ class "sentence-word" ] [ text word ]

                    HiddenWrd hiddenWord ->
                         viewHiddenWord hiddenWord hiddenWords_
            )
            sentence
        ) 
```

Enter fullscreen mode Exit fullscreen mode

很好。现在到`viewHiddenWord`。让我们从
更新它的类型签名

```
viewHiddenWord : Word -> List Word -> Html Msg
viewHiddenWord hiddenWord chosenWords = 
```

Enter fullscreen mode Exit fullscreen mode

至

```
viewHiddenWord : HiddenWord -> List HiddenWord -> Html Msg
viewHiddenWord hiddenWord hiddenWords = 
```

Enter fullscreen mode Exit fullscreen mode

`viewHiddenWord`现在唯一能接受的类型就是`HiddenWord`。我们还把所有的`chosenXYZ`改名为`hiddenXYZ`。

同样，我们不再需要`case`表达式了！

```
viewHiddenWord : HiddenWord -> List HiddenWord -> Html Msg
viewHiddenWord hiddenWord hiddenWords =
    let
        viewOption : String -> Html Msg
        viewOption wordString =
            option
                [ value wordString, selected (wordString == hiddenWordText) ]
                [ text <| String.toLower wordString ]

        wordElement : Word -> Html Msg
        wordElement word =
            case word of
                HiddenWord ( _, wordString ) ->
                    viewOption wordString

                SentenceWord ( _, wordString ) ->
                    viewOption wordString
    in
    div [ class "select" ]
        [ select [ class "hidden-word", onInput (WordChanged hiddenIndex) ] <|
            option []
                [ text "" ]
                :: List.map wordElement hiddenWords
        ] 
```

Enter fullscreen mode Exit fullscreen mode

让我们也将两个实例`hiddenWords`重命名为`hiddenWords_`，因为编译器会报错名称冲突(代码片段中的第二行和最后一行的前一行)。

```
viewHiddenWord : HiddenWord -> List HiddenWord -> Html Msg
viewHiddenWord hiddenWord hiddenWords_ =
    let
        viewOption : String -> Html Msg
        viewOption wordString =
            option
                [ value wordString, selected (wordString == hiddenWordText) ]
                [ text <| String.toLower wordString ]

        wordElement : Word -> Html Msg
        wordElement word =
            case word of
                HiddenWord ( _, wordString ) ->
                    viewOption wordString

                SentenceWord ( _, wordString ) ->
                    viewOption wordString
    in
    div [ class "select" ]
        [ select [ class "hidden-word", onInput (WordChanged hiddenIndex) ] <|
            option []
                [ text "" ]
                :: List.map wordElement hiddenWords_
        ] 
```

Enter fullscreen mode Exit fullscreen mode

`wordElement`也变得更简单，因为它应该只得到`HiddenWord`，所以看起来奇怪的`case`不见了:

```
viewHiddenWord : HiddenWord -> List HiddenWord -> Html Msg
viewHiddenWord hiddenWord hiddenWords_ =
    let
        viewOption : String -> Html Msg
        viewOption wordString =
            option
                [ value wordString, selected (wordString == hiddenWordText) ]
                [ text <| String.toLower wordString ]

        wordElement : HiddenWord -> Html Msg
        wordElement hiddenWord_ =
            viewOption (answerToString hiddenWord_.answer)
    in
    div [ class "select" ]
        [ select [ class "hidden-word", onInput (WordChanged hiddenIndex) ] <|
            option []
                [ text "" ]
                :: List.map wordElement hiddenWords_
        ] 
```

Enter fullscreen mode Exit fullscreen mode

变化是非常简单的。我们需要选择`<option>`，其中来自当前`HiddenWord`的`answer`等于来自传入
的`HiddenWord`的`playerChoice`

```
viewHiddenWord : HiddenWord -> List HiddenWord -> Html Msg
viewHiddenWord hiddenWord hiddenWords_ =
    let
        playerChoiceString : String
        playerChoiceString =
            playerChoiceToString hiddenWord.playerChoice

        viewOption : String -> Html Msg
        viewOption answerString =
            option
                [ value answerString, selected (answerString == playerChoiceString) ]
                [ text <| String.toLower answerString ]

        wordElement : HiddenWord -> Html Msg
        wordElement hiddenWord_ =
            viewOption
                (answerToString hiddenWord_.answer)
    in
    div [ class "select" ]
        [ select [ class "hidden-word", onInput (WordChanged hiddenIndex) ] <|
            option []
                [ text "" ]
                :: List.map wordElement hiddenWords_
        ] 
```

Enter fullscreen mode Exit fullscreen mode

编译器现在抱怨这个`viewHiddenWord`和`viewHiddenWords`内部的内部`viewHiddenWord`之间的名字冲突...让我们把`_`加到内部的`viewHiddenWord` :

```
viewHiddenWords : List HiddenWord -> Html msg
viewHiddenWords hiddenWordList =
    let
        hiddenWordsSorted : List HiddenWord
        hiddenWordsSorted =
            List.sortBy .sortKey hiddenWordList

        viewHiddenWord_ : HiddenWord -> Html msg
        viewHiddenWord_ hiddenWord =

-- omitted

    in
    ul [] <| List.map viewHiddenWord_ hiddenWordsSorted 
```

Enter fullscreen mode Exit fullscreen mode

查看代码，我们可以看到`wordElement`几乎什么都不做，只是调用`viewOption`，所以我们可以删除它，直接调用`viewOption`:

```
viewHiddenWord : HiddenWord -> List HiddenWord -> Html Msg
viewHiddenWord hiddenWord hiddenWords_ =
    let
        playerChoiceString : String
        playerChoiceString =
            playerChoiceToString hiddenWord.playerChoice

        viewOption : HiddenWord -> Html Msg
        viewOption hiddenWord_ =
            let
                answerString =
                    answerToString hiddenWord_.answer
            in
            option
                [ value answerString, selected (answerString == playerChoiceString) ]
                [ text <| String.toLower answerString ]
    in
    div [ class "select" ]
        [ select [ class "hidden-word", onInput (WordChanged hiddenIndex) ] <|
            option []
                [ text "" ]
                :: List.map viewOption hiddenWords_
        ] 
```

Enter fullscreen mode Exit fullscreen mode

我们现在将`HiddenWord`传递给`viewOption`，并在其中计算`answerString`。我们也可以在`viewOption`声明中使用析构来立即得到【T4:】T5

```
 viewOption : HiddenWord -> Html Msg
        viewOption { answer } =
            let
                answerString : String
                answerString =
                    answerToString answer
            in
            option
                [ value answerString, selected (answerString == playerChoiceString) ]
                [ text <| String.toLower answerString ] 
```

Enter fullscreen mode Exit fullscreen mode

我们的`viewHiddenWord`中还有`hiddenIndex`没有处理。这应该是该词在句子中的索引，这样我们就可以知道随着玩家的选择更新什么词了。正如我们在重构开始时所讨论的，我们可以通过使用`List.indexedMap`而不是`viewSentence`中的`List.map`来获得这个索引，并使用这个索引调用`viewHiddenWord`。

让我们更新`viewHiddenWord`以使用`List.indexedMap`，在内部函数中获取索引并将其传递给`viewHiddenWord` :

```
viewSentence : Words -> Html Msg
viewSentence sentence =
    let
        hiddenWords_ : List HiddenWord
        hiddenWords_ =
            hiddenWords sentence
    in
    div [ class "has-text-centered" ]
        (List.indexedMap
            (\index sentenceWord ->
                case sentenceWord of
                    SentenceWrd word ->
                        span [ class "sentence-word" ] [ text word ]

                    HiddenWrd hiddenWord ->
                        viewHiddenWord index hiddenWord hiddenWords_
            )
            sentence
        ) 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们更新`viewHiddenWord`，将索引作为它的第一个参数，并使用它:

```
viewHiddenWord : Int -> HiddenWord -> List HiddenWord -> Html Msg
viewHiddenWord index hiddenWord hiddenWords_ =

-- omitted 

    in
    div [ class "select" ]
        [ select [ class "hidden-word", onInput (WordChanged index) ] <|
            option []
                [ text "" ]
                :: List.map viewOption hiddenWords_
        ] 
```

Enter fullscreen mode Exit fullscreen mode

游戏编译运行！

但是下拉列表中的`<option>`仍然按照它们在句子中出现的顺序出现。我们仍然需要像在`viewHiddenWords`那样按照`sortKey`对它们进行分类。

我们用`hiddenWordsSorted`重复自己，所以让我们把它重构为自己的函数:

```
hiddenWordsSorted : List HiddenWord -> List HiddenWord
hiddenWordsSorted hiddenWordList =
    List.sortBy .sortKey hiddenWordList 
```

Enter fullscreen mode Exit fullscreen mode

并在`viewHiddenWords` :
内使用

```
viewHiddenWords : List HiddenWord -> Html msg
viewHiddenWords hiddenWordList =
    let
        hiddenWordsSorted_ : List HiddenWord
        hiddenWordsSorted_ =
            hiddenWordsSorted hiddenWordList

-------- omitted

    in
    ul [] <| List.map viewHiddenWord_ hiddenWordsSorted_ 
```

Enter fullscreen mode Exit fullscreen mode

并且在`viewHiddenWord` :

```
viewHiddenWord : Int -> HiddenWord -> List HiddenWord -> Html Msg
viewHiddenWord index hiddenWord hiddenWords_ =
    let
        hiddenWordsSorted_ : List HiddenWord
        hiddenWordsSorted_ =
            hiddenWordsSorted hiddenWords_

-- omitted

    in
    div [ class "select" ]
        [ select [ class "hidden-word", onInput (WordChanged index) ] <|
            option []
                [ text "" ]
                :: List.map viewOption hiddenWordsSorted_
        ] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们在下拉列表中有了正确随机排列的隐藏单词:

[![Hidden words in the correct order in the drop down](img/494e9a4d66249c490904884af6abb1a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JY1ZX7In--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8efg82y1pfcean99jmp1.png)

唷！太多了！而且我们收获了很多！更少的地方让我们的代码进入无效状态。

当前进度保存在 repo 中，标签为 v 2.0[https://github.com/mickeyvip/words-memory-game/tree/v2.0](https://github.com/mickeyvip/words-memory-game/tree/v2.0)。

在下一篇文章中，我们将添加随机性来为每个新游戏选择不同的单词。敬请期待！

## 鸣谢

特别感谢 jol Quenneville 审阅了代码，并在本文中提出了重构建议。
# 用 Elm 编写一个单词记忆游戏——第 4 部分:用随机性增加趣味性

> 原文：<https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-4-spicing-things-up-with-randomness-58ei>

这是“在 Elm 中编写单词记忆游戏”系列的第 4 部分，查找:

*   [第 1 部分:使用 Parcel 设置 Elm 应用程序](https://dev.to/mickeyvip/writing-a-word-memory-game-inelm---part-1-setting-up-an-elm-application-with-parcel-1ppk)
*   [第二部分:建模和构建一个基本的单词记忆游戏](https://dev.to/mickeyvip/writing-a-word-memory-game-inelm---part-2-modeling-and-building-a-basic-word-memory-game-583g)
*   [第三部分:重新思考模型](https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-3-rethinking-the-model-2ajp)
*   第 4 部分:用随机性增加趣味
*   [第 5 部分-更多的随机性和更多的游戏](https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-5-more-randomness-and-more-game-1i37)

* * *

每次都选择相同的单词是很无聊的。让我们的游戏每次随机选择单词。

随机性是一种副作用，不符合函数式编程的概念，因为生成随机数每次都会返回不同的结果。

为了让我们的应用程序处理副作用，我们需要将它从`Browser.sandbox`升级到`Browser.element`，这将增加*命令*和*订阅*，用于与外界交互。

让我们将我们的`main`定义改为使用`Browser.element` :

```
main : Program () Model Msg
main =
    Browser.element
        { view = view
        , init = init
        , update = update
        , subscriptions = subscriptions
        } 
```

Enter fullscreen mode Exit fullscreen mode

为了编译当前的应用程序，我们现在需要做一些更改:

*   `init`需要一个函数而不是`Model`，所以我们将添加`init`函数
*   `subscriptions`期望一个我们将添加的函数
*   `update`返回一个`Tuple` `(Model, Cmd Msg)`而不是一个`Model`

让我们跟随编译器并修复错误。

我们将从添加`subscriptions`函数开始。我们现在实际上不需要订阅，所以我们可以返回`Sub.none`，意思是*没有订阅* :

```
--------- SUBSCRIPTIONS ----

subscriptions : Model -> Sub Msg
subscriptions model =
    Sub.none 
```

Enter fullscreen mode Exit fullscreen mode

现在`update`——让我们把签名改成

```
update : Msg -> Model -> ( Model, Cmd Msg ) 
```

Enter fullscreen mode Exit fullscreen mode

并返回预期的元组(最后一行):

```
update : Msg -> Model -> ( Model, Cmd Msg )
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
            ( { model | sentence = newSentence }, Cmd.none ) 
```

Enter fullscreen mode Exit fullscreen mode

最后一个是`init`函数。它使用了我们不会用到的`flags`,我们可以通过将第一个参数声明为单元类型`()`来忽略它。它返回一个类似于`update`的`Tuple` `(Model, Cmd Msg)`。我们将使用我们的`initialModel`和`Cmd.none`来运行 :

```
init : () -> ( Model, Cmd Msg )
init _ =
    ( initialModel, Cmd.none ) 
```

Enter fullscreen mode Exit fullscreen mode

这应该使应用程序编译和游戏应该像以前一样工作。

当前进度保存在 repo 中，标签为 v 3.0:
[【https://github.com/mickeyvip/words-memory-game/tree/v3.0】](https://github.com/mickeyvip/words-memory-game/tree/v3.0)。

我们如何从单词列表中随机选择单词？Elm guide 有一个`Random`用法的例子:【https://guide.elm-lang.org/effects/random.html】， [`elm/random`](https://package.elm-lang.org/packages/elm/random/latest/Random) 包有一个很好看的生成随机数的 API。

我发现`Random.list`可能是有用的:我们可以从`0`到`words`的长度产生 3 个随机值，并在这些索引处取值。

```
indexes : Int -> Int -> Random.Generator (List Int)
indexes quantity listLength =
    Random.list quantity (Random.int 0 (listLength - 1)) 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，它可能会产生一个包含重复值的列表。例如，我们可以通过不断生成新的随机值来克服这个问题，直到我们得到所需数量的唯一值。但是在寻找更简单的或者“已经做好的”解决方案时，我发现了[elm-community/random-extra](https://package.elm-lang.org/packages/elm-community/random-extra/latest/)包，正如`elm/random`文档所说，它可能有一天会与`elm/random`合并。

`elm-community/random-extra`有`Random.List`模块，该模块公开了一个 [`shuffle`](https://package.elm-lang.org/packages/elm-community/random-extra/latest/Random-List#shuffle) 函数，正如其名字所述，该函数对列表进行洗牌。所以我们可以把它用在我们的单词列表上，取前 3 个。听起来很简单。

让我们安装它(停止应用程序，如果它正在运行):

```
$ elm install elm-community/random-extra 
```

Enter fullscreen mode Exit fullscreen mode

点击`y`(或“输入”)接受安装提示。

我们还需要将`elm/random`作为一个直接依赖项，尽管到目前为止它已经被安装为一个间接依赖项。当我们安装它时，Elm 安装程序会提示我们将它从间接依赖项转移到直接依赖项。选择‘y’:

```
$ elm install elm/random
I found it in your elm.json file, but in the "indirect" dependencies.
Should I move it into "direct" dependencies for more general use? [Y/n]:
Dependencies loaded from local cache.
Dependencies ready! 
```

Enter fullscreen mode Exit fullscreen mode

正如我们在文档中看到的，调用`shuffle`会产生一个`Generator (List a)`，这只是对我们想要做的事情的描述。为了实际执行`Generator`,我们需要调用`Random.generate`,它反过来产生一个`Command`,正如我们之前看到的 Elm 指南中的`Random`示例所描述的。

我们能做的是取一个索引列表`[0, 1, 2, 3...]`，打乱这个列表，取前 3 个索引。这些将是隐藏单词的索引；他们在新列表中的位置将是`sortKey`。

让我们添加一个将产生`Command` :
的函数

```
import Random
import Random.List

-------- COMMANDS ----

chooseWords : Words -> Cmd Msg
chooseWords chosenSentence =
    List.range 0 (List.length chosenSentence - 1)
        |> Random.List.shuffle
        |> Random.map (List.take 3)
        |> Random.map (List.indexedMap Tuple.pair)
        |> Random.generate WordsChosen 
```

Enter fullscreen mode Exit fullscreen mode

`List.range 0 (List.length chosenSentence - 1)`将给出`[0, 1, 2, 3, 4, 5, 6]`(因为我们的句子中有 7 个单词)。
`shuffle`将对列表进行重新排序。
`List.take 3`将从重新排序的列表中取出前 3 个项目。
`List.indexedMap Tuple.pair`将把单词索引列表映射到`Tuple` s 的`List`，其中第一个索引是`sortKey`，第二个索引是单词的索引。比如`[(0, 1), (1, 6), (2, 3)]`。
然后我们通过调用`Random.generate`并向其传递新消息`WordsChosen`来执行命令。

为了操作`Random`的结果，我们使用`Random.map`——类似于其他类型的`map`。

让我们添加一条新消息`WordsChosen`，它将获得所选索引的列表:

```
type Msg
    = WordChanged Int String
    | WordsChosen (List (Int, Int)) 
```

Enter fullscreen mode Exit fullscreen mode

现在编译器将引导我们在`update`函数中处理新消息:

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of

        -- omitted

        WordsChosen sortIndexes -> 
```

Enter fullscreen mode Exit fullscreen mode

我没有找到任何更简单的方法来更新句子，所以我做的如下:`List.indexMap`当前句子，所以我们有一个单词索引和一个单词。

```
 WordsChosen sortIndexes ->
            let
                sentence =
                    List.indexedMap
                        (\wordIndex word ->

-- omitted

                    model.sentence
            in
            ( { model | sentence = sentence }, Cmd.none ) 
```

Enter fullscreen mode Exit fullscreen mode

查看当前单词是否有一个`sortIndex`对(`wordIndex`等于`Tuple`的第二个成员):

```
-- omitted
                sentence =
                    List.indexedMap
                        (\wordIndex word ->
                            let
                                hiddenIndex =
                                    List.filter
                                        (\( _, originalWordIndex ) ->
                                            originalWordIndex == wordIndex
                                        )
                                        sortIndexes

-- omitted 
```

Enter fullscreen mode Exit fullscreen mode

如果`hiddenIndex`不为空，我们需要用`HiddenWord`替换当前单词，`sortKey`将成为`Tuple`的第一个成员。否则，我们需要保留当前的单词:

```
-- omitted

                                newWord =
                                    case List.head hiddenIndex of
                                        Just ( sortKey, _ ) ->
                                            HiddenWrd
                                                { sortKey = sortKey
                                                , answer = Answer (wordToString word)
                                                , playerChoice = PlayerChoice ""
                                                }

                                        Nothing ->
                                            word
-- omitted 
```

Enter fullscreen mode Exit fullscreen mode

下面是`WordsChosen`消息处理程序的完整代码:

```
 WordsChosen sortIndexes ->
            let
                sentence =
                    List.indexedMap
                        (\wordIndex word ->
                            let
                                hiddenIndex =
                                    List.filter
                                        (\( _, originalWordIndex ) ->
                                            originalWordIndex == wordIndex
                                        )
                                        sortIndexes

                                newWord =
                                    case List.head hiddenIndex of
                                        Just ( sortKey, _ ) ->
                                            HiddenWrd
                                                { sortKey = sortKey
                                                , answer = Answer (wordToString word)
                                                , playerChoice = PlayerChoice ""
                                                }

                                        Nothing ->
                                            word
                            in
                            newWord
                        )
                        model.sentence
            in
            ( { model | sentence = sentence }, Cmd.none ) 
```

Enter fullscreen mode Exit fullscreen mode

我们也把所有单词设为`SentenceWord` :

```
initialModel : Model
initialModel =
    { sentence =
        [ SentenceWrd "The"
        , SentenceWrd "pen"
        , SentenceWrd "is"
        , SentenceWrd "mightier"
        , SentenceWrd "than"
        , SentenceWrd "the"
        , SentenceWrd "sword"
        ]
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要更新我们的`init`函数来执行`chooseWords`命令:

```
init : () -> ( Model, Cmd Msg )
init _ =
    ( initialModel, chooseWords initialModel.sentence ) 
```

Enter fullscreen mode Exit fullscreen mode

刷新页面-我们应该看到我们的游戏正在工作，每次刷新都会选择不同的单词！

让我们添加一个漂亮的“新游戏”按钮，这样我们就不需要为新游戏刷新页面:

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
-- new code
                , div [ class "is-clearfix" ]
                    [ button
                        [ class "button is-info is-pulled-right"
                        , onClick NewGame
                        ]
                        [ text "New Game" ]
                    ]
                ]
            ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

让我们添加`NewGame`消息:

```
type Msg
    = WordChanged Int String
    | WordsChosen (List ( Int, Int ))
    | NewGame 
```

Enter fullscreen mode Exit fullscreen mode

并在`update`函数中处理:

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of

        -- omitted

        NewGame ->
            ( initialModel, chooseWords initialModel.sentence ) 
```

Enter fullscreen mode Exit fullscreen mode

并且页面刷新后，我们可以点击“新游戏”来重启游戏。

当前进度保存在 repo 中，标签为 v 4.0[https://github.com/mickeyvip/words-memory-game/tree/v4.0](https://github.com/mickeyvip/words-memory-game/tree/v4.0)。

# 表示获胜

这将是一个很好的时机向玩家证明这句话是正确的。

每次改变后，我们可以检查是否每个`HiddenWord`都有一个等于`answer`的`playerChoice`。如果是这样的话——我们可以呈现“你赢了”的观点。

让我们添加一个函数来检查是否有赢:

```
isWin : Words -> Bool
isWin sentence =
    List.all
        (\word ->
            case word of
                SentenceWrd _ ->
                    True

                HiddenWrd { answer, playerChoice } ->
                    answerToString answer == playerChoiceToString playerChoice
        )
        sentence 
```

Enter fullscreen mode Exit fullscreen mode

如果谓词函数为其所有成员返回`True`，则`List.all`将返回`True`。如果单词是`SentenceWrd`，那么没有什么可检查的，我们只需返回`True`。如果字是`HiddenWrd`，我们返回比较`answer`和`playerChoice`的结果。

我们可以缩短`isWin`:我们只对`HiddenWord` s 感兴趣，我们已经有了一个接受`Words`并返回`List HiddenWord`的函数，也就是`hiddenWords`。来用一下:

```
isWin : Words -> Bool
isWin sentence =
    List.all
        (\{ answer, playerChoice } ->
            answerToString answer == playerChoiceToString playerChoice
        )
        (hiddenWords sentence) 
```

Enter fullscreen mode Exit fullscreen mode

而如果`isWin`只处理`HiddenWord` s，我们根本不需要发送`Words`:

```
isWin : List HiddenWord -> Bool
isWin hiddenWordList =
    List.all
        (\{ answer, playerChoice } ->
            answerToString answer == playerChoiceToString playerChoice
        )
        hiddenWordList 
```

Enter fullscreen mode Exit fullscreen mode

那看起来好多了。

我们可以变得更好，完全去掉参数，使用 Elm 的 currying 功能，类似于 JSON 解码器的声明:

```
isWin : List HiddenWord -> Bool
isWin =
    List.all
        (\{ answer, playerChoice } ->
            answerToString answer == playerChoiceToString playerChoice
        ) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在返回一个等待`List HiddenWord`参数的函数。不过，这对新人(比如我自己)来说可能有点困惑。

当玩家赢了，让我们展示句子和“你赢了！”消息，并用下拉菜单和单词列表隐藏句子。为了更简单，我们可以重构主视图`view`，根据`win`是`True`还是`False` :
来显示`viewGame`或`viewWin`视图

```
view : Model -> Html Msg
view model =
    let
        win : Bool
        win =
            isWin <| hiddenWords model.sentence

        currentView : Html Msg
        currentView =
            if win then
                viewWin model.sentence

            else
                viewGame model.sentence
    in
    main_ [ class "section" ]
        [ div [ class "container" ]
            [ viewTitle
            , div [ class "box" ]
                [ currentView ]
            ]
        ]

viewGame : Words -> Html Msg
viewGame sentence =
    div []
        [ viewSentence sentence
        , viewHiddenWords (hiddenWords sentence)
        ]

viewWin : Words -> Html Msg
viewWin sentence =
    div []
        [ div [ class "notification is-primary" ]
            [ h3 [ class "title is-3 has-text-centered" ] [ text "You Win!" ]
            ]
        , h4
            [ class "title is-4 has-text-centered" ]
            [ viewOriginalSentence sentence ]
        , div [ class "is-clearfix" ]
            [ button
                [ class "button is-info is-pulled-right"
                , onClick NewGame
                ]
                [ text "New Game" ]
            ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

这应该非常简单，不需要解释。在`viewWin`视图中，我们有一个“你赢了！”消息、句子和“新游戏”按钮。我还在标题中添加了`is-big`类，使其更大。在`viewGame`视图中，我们像以前一样显示游戏。

[![Game in progress](img/619bdb63a5645d39d926650d78783220.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0-_ALbwK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mu094vyli9nvzcahabyh.png)

[![win](img/f709214564a89144189850f54c9ce763.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O5y5Ckrs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wuarnag7khdji308en44.png)

当前进度保存在 repo 中，标签为 v 5.0[https://github.com/mickeyvip/words-memory-game/tree/v5.0](https://github.com/mickeyvip/words-memory-game/tree/v5.0)。

# 自动生成`Sentence`

到目前为止，我们已经手动设置了`sentence`，但是在实际的游戏中，将会有许多句子作为`String`，我们希望能够从它们中生成`sentence`。

怎么才能做到呢？我们可以用 [`String.words`](https://package.elm-lang.org/packages/elm/core/latest/String#words) 。这将返回一个单词列表，我们可以把它变成我们需要的东西。

先把我们的`Model`改成有`currentSentence : String`，设置为*《笔杆子比剑强》*，设置`sentence`为空单:

```
type alias Model =
    { currentSentence : String
    , sentence : Words
    }

initialModel : Model
initialModel =
    { currentSentence = "The pen is mightier than the sword"
    , sentence = []
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将添加一个`generateSentence`函数，它将在`getWords` :
的帮助下生成`sentence`

```
getWords : String -> List String
getWords sentence =
    sentence
        |> String.words

generateSentence : String -> List Word
generateSentence sentenceString =
    sentenceString
        |> String.words
        |> List.map SentenceWrd 
```

Enter fullscreen mode Exit fullscreen mode

让我们更新我们的`init`函数来调用`generateSentence` :

```
init : () -> ( Model, Cmd Msg )
init _ =
    let
        sentence : Words
        sentence =
            generateSentence initialModel.currentSentence

        model : Model
        model =
            { initialModel | sentence = sentence }
    in
    ( model, chooseWords sentence ) 
```

Enter fullscreen mode Exit fullscreen mode

游戏应该像以前一样工作，只是现在`sentence`的生成是自动的。

然而，当赢得游戏并点击“新游戏”时，我们会看到一个空框。这是因为我们将模型重置为`initialModel`，而`sentence`被设置为`[]`。让我们将`update`函数改为调用`init`，它生成`sentence`并执行`chooseWords`命令:

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of

        -- omitted
        NewGame ->
            init () 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以开始新的游戏了！

# 将`Model`重构为游戏状态

我们又一次陷入了不同步的状态。`currentSentence`只在生成`sentence`时需要，我们必须在每场游戏开始时将`sentence`设置为`[]`，因为它在我们的`Model`上，但实际上第一步并不需要。

让我们重新定义我们的`Model`,使它有两种状态:

1.  `Started` -将只有`currentSentence : String`
2.  `Playing` -将只有`sentence : Words`

游戏将在`Started`状态开始，调用`generateSentence`。之后，该州将被改为`Playing`。

```
type alias StateStarted =
    { currentSentence : String }

type alias StatePlaying =
    { sentence : Words
    }

type GameState
    = Started StateStarted
    | Playing StatePlaying 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们将我们的`Model`更新为`GameState` :
类型

```
type alias Model =
    GameState 
```

Enter fullscreen mode Exit fullscreen mode

我们的`initialModel`可以设置为:

```
initialModel : Model
initialModel =
    Started (StateStarted "The pen is mightier than the sword") 
```

Enter fullscreen mode Exit fullscreen mode

初始状态没有多余的`sentence`！

`init`函数变得更加冗长:

```
init : () -> ( Model, Cmd Msg )
init _ =
    let
        ( model, cmd ) =
            case initialModel of
                Started { currentSentence } ->
                    let
                        sentence : Words
                        sentence =
                            generateSentence currentSentence
                    in
                    ( Playing (StatePlaying sentence), chooseWords sentence )

                Playing _ ->
                    ( initialModel, Cmd.none )
    in
    ( model, cmd ) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在需要模式匹配我们的`Model`来知道游戏现在处于哪个游戏状态。如果处于`Started`状态，我们将产生`sentence`并执行`chooseWords`。在另一种情况下(此时不应该发生)，我们将原样返回`initialModel`，并且不执行任何命令。

在`update`函数中，我们还需要考虑游戏状态:

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        WordChanged index wordString ->
            let
                model_ =
                    case model of
                        Playing { sentence } ->
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
                                    List.indexedMap updateWord sentence
                            in
                            Playing (StatePlaying newSentence)

                        Started _ ->
                            model
            in
            ( model_, Cmd.none )

-- omitted 
```

Enter fullscreen mode Exit fullscreen mode

这种改变应该非常简单:如果游戏处于`Playing`状态——从状态中提取`sentence`,更新它并用更新后的`newSentence`返回`Playing`状态，否则——保持`model`不变。

`WordsChosen`也是如此:

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of

-- omitted

        WordsChosen sortIndexes ->
            let
                model_ =
                    case model of
                        Playing { sentence } ->
                            let
                                sentence_ =
                                    List.indexedMap
                                        (\wordIndex word ->
                                            let
                                                hiddenIndex =
                                                    List.filter
                                                        (\( _, originalWordIndex ) ->
                                                            originalWordIndex == wordIndex
                                                        )
                                                        sortIndexes

                                                newWord =
                                                    case List.head hiddenIndex of
                                                        Just ( sortKey, _ ) ->
                                                            HiddenWrd
                                                                { sortKey = sortKey
                                                                , answer = Answer (wordToString word)
                                                                , playerChoice = PlayerChoice ""
                                                                }

                                                        Nothing ->
                                                            word
                                            in
                                            newWord
                                        )
                                        sentence
                            in
                            Playing (StatePlaying sentence_)

                        Started _ ->
                            model
            in
            ( model_, Cmd.none ) 
```

Enter fullscreen mode Exit fullscreen mode

`view`也必须更新:

```
view : Model -> Html Msg
view model =
    let
        currentView =
            case model of
                Playing { sentence } ->
                    let
                        win : Bool
                        win =
                            isWin <| hiddenWords sentence
                    in
                    if win then
                        viewWin sentence

                    else
                        viewGame sentence

                Started { currentSentence } ->
                    viewStarted currentSentence
    in
    main_ [ class "section" ]
        [ div [ class "container" ]
            [ viewTitle
            , div [ class "box" ]
                [ currentView ]
            ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

当游戏状态是`Started` -我们将渲染`viewStarted`视图，只显示当前句子:

```
viewStarted : String -> Html msg
viewStarted currentSentence =
    p []
        [ text currentSentence ] 
```

Enter fullscreen mode Exit fullscreen mode

虽然我们不会看到它，因为游戏在`init`功能后进入`Playing`状态，但我们会在未来使用它。

现在游戏可以像以前一样编译和运行，而`Model`的设计要好得多。

拥有另一个播放状态- `Win`实际上看起来是符合逻辑的。因此，获胜的逻辑将驻留在`update`函数中，而不是像现在这样驻留在`view`函数中，这感觉很奇怪。

```
type alias StateWin =
    { currentSentence : String
    }

type GameState
    = Started StateStarted
    | Playing StatePlaying
    | Win StateWin 
```

Enter fullscreen mode Exit fullscreen mode

`init` :

```
init : () -> ( Model, Cmd Msg )
init _ =
    let
        ( model, cmd ) =
            case initialModel of
                Started { currentSentence } ->
                    let
                        sentence : Words
                        sentence =
                            generateSentence currentSentence
                    in
                    ( Playing (StatePlaying sentence), chooseWords sentence )

                Playing _ ->
                    ( initialModel, Cmd.none )

                Win _ ->
                    ( initialModel, Cmd.none )
    in
    ( model, cmd ) 
```

Enter fullscreen mode Exit fullscreen mode

然后`update` :

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        WordChanged index wordString ->
            let
                model_ =
                    case model of
                        Playing { sentence } ->
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
                                    List.indexedMap updateWord sentence

                                isWin_ =
                                    isWin (hiddenWords newSentence)
                            in
                            if isWin_ then
                                Win (StateWin (sentenceToString newSentence))

                            else
                                Playing (StatePlaying newSentence)

                        Started _ ->
                            model

                        Win _ ->
                            model
            in
            ( model_, Cmd.none )

        WordsChosen sortIndexes ->
            let
                model_ =
                    case model of
                        Playing { sentence } ->

-- omitted

                        Win _ ->
                            model
            in
            ( model_, Cmd.none ) 
```

Enter fullscreen mode Exit fullscreen mode

我们把中奖支票加到了`Playing`箱子里。如果有中奖，我们就把状态改成`Win`。如果游戏已经处于`Win`状态——就像`Started`的情况一样返回当前的`model`。

我们添加了将`Words`转换为`String` :
的`sentenceToString`函数

```
sentenceToString : Words -> String
sentenceToString sentence =
    sentence
        |> List.map wordToString
        |> String.join " " 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在`viewOriginalSentence` :
中重用它

```
viewOriginalSentence : Words -> Html Msg
viewOriginalSentence words =
    p
        [ class "has-text-centered" ]
        [ text <| sentenceToString words ] 
```

Enter fullscreen mode Exit fullscreen mode

`view`现在简单多了:

```
view : Model -> Html Msg
view model =
    let
        currentView =
            case model of
                Playing { sentence } ->
                    viewGame sentence

                Started { currentSentence } ->
                    viewStarted currentSentence

                Win { currentSentence } ->
                    viewWin currentSentence
    in

-------- omitted 
```

Enter fullscreen mode Exit fullscreen mode

现在我们看到我们需要重构`viewWin`来接受`String`而不是`Words` :

```
viewWin : String -> Html Msg
viewWin sentence =
    div []
        [ div [ class "notification is-primary" ]
            [ h3 [ class "title is-3 has-text-centered" ] [ text "You Win!" ]
            ]
        , h4
            [ class "title is-4 has-text-centered" ]
            [ p
                [ class "has-text-centered" ]
                [ text sentence ]
            ]
        , div [ class "is-clearfix" ]
            [ button
                [ class "button is-info is-pulled-right"
                , onClick NewGame
                ]
                [ text "New Game" ]
            ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

而且也不需要`viewOriginalSentence`，因为我们只是使用了在` viewWin 里面生成 HTML 的相同代码，所以删除就好了。

游戏应该像以前一样编译和运行，但是现在我们有了 3 个特定的游戏状态和与每个状态相关的数据。

当前进度保存在 repo 中，标签为 v 6.0[https://github.com/mickeyvip/words-memory-game/tree/v6.0](https://github.com/mickeyvip/words-memory-game/tree/v6.0)。

请继续关注即将发布的帖子，在那里我们将为每个新游戏选择更多的句子！
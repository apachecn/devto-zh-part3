# 用 Elm 编写单词记忆游戏-第 5 部分:更多的随机性和更多的游戏性

> 原文：<https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-5-more-randomness-and-more-game-1i37>

这是“在 Elm 中编写单词记忆游戏”系列的第 5 部分，查找:

*   [第 1 部分:使用 Parcel 设置 Elm 应用程序](https://dev.to/mickeyvip/writing-a-word-memory-game-inelm---part-1-setting-up-an-elm-application-with-parcel-1ppk)
*   [第二部分:建模和构建一个基本的单词记忆游戏](https://dev.to/mickeyvip/writing-a-word-memory-game-inelm---part-2-modeling-and-building-a-basic-word-memory-game-583g)
*   [第三部分:重新思考模型](https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-3-rethinking-the-model-2ajp)
*   [第四部分:用随机性增加趣味](https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-4-spicing-things-up-with-randomness-58ei)
*   第 5 部分-更多的随机性和更多的游戏

* * *

老是玩同一个句子很无聊。实际游戏需要有很多句子可供选择。例如:

```
sentences : List String
sentences =
    [ "Two wrongs don't make a right."
    , "The pen is mightier than the sword."
    , "When in Rome, do as the Romans."
    , "Keep your friends close and your enemies closer."
    , "You can't make an omelet without breaking a few eggs."
    , "The grass is always greener on the other side of the hill."
    , "You can lead a horse to water, but you can't make him drink."
    , "If you want something done right, you have to do it yourself."
    ] 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过去掉所有的标点符号来简化句子

```
sentences : List String
sentences =
    [ "Two wrongs don't make a right"
    , "The pen is mightier than the sword"
    , "When in Rome do as the Romans"
    , "Keep your friends close and your enemies closer"
    , "You can't make an omelet without breaking a few eggs"
    , "The grass is always greener on the other side of the hill"
    , "You can lead a horse to water but you can't make him drink"
    , "If you want something done right you have to do it yourself"
    ] 
```

Enter fullscreen mode Exit fullscreen mode

要随机选择一个句子，我们可以使用 [`elm/Random`](https://package.elm-lang.org/packages/elm/random/latest/) 库生成一个从 0 到我们的`sentences`列表长度的随机数，并在这个索引处取一个句子(需要将`List`转换成`Array`才能使用索引)。

或者我们可以使用 [`elm-community/random-extra/Random-Extra#sample/`](https://package.elm-lang.org/packages/elm-community/random-extra/latest/Random-Extra#sample) 随机均匀地选择一个元素。我们已经有了`Random-Extra`所以让我们使用它:

```
import Random.Extra as Random

-- omitted

chooseSentence : List String -> Cmd Msg
chooseSentence sentences_ =
    Random.sample sentences_
        |> Random.map (Maybe.withDefault "")
        |> Random.generate SentenceChosen 
```

Enter fullscreen mode Exit fullscreen mode

在`SentenceChosen`处理程序中，我们可以调用`chooseWords`命令并像以前一样继续。

```
type Msg
    = WordChanged Int String
    | SentenceChosen String
    | WordsChosen (List ( Int, Int ))
    | NewGame 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要引入一个新的状态`Initialising`来模拟游戏开始:

```
type GameState
    = Initializing
    | Started StateStarted
    | Playing StatePlaying
    | Win StateWin 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们实现`update`函数的`SentenceChosen`分支:

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of

-- omitted

        SentenceChosen sentenceString ->
            case model of
                Initializing ->
                    let
                        sentence : Words
                        sentence =
                            generateSentence sentenceString
                    in
                    ( Started (StateStarted sentenceString), chooseWords sentence )

                Started _ ->
                    ( model, Cmd.none )

                Playing _ ->
                    ( model, Cmd.none )

                Win _ ->
                    ( model, Cmd.none ) 
```

Enter fullscreen mode Exit fullscreen mode

我们实际上是从`init`函数中复制了代码。而`init`本身就变得很短:

```
init : () -> ( Model, Cmd Msg )
init _ =
    ( Initializing, chooseSentence sentences ) 
```

Enter fullscreen mode Exit fullscreen mode

我们将游戏的当前状态设置为`Initializing`，并通过向其传递句子列表来调用`chooseSentence`命令。

编译器通知我们关于`update`函数中`GameState`的非穷举`case` s，所以让我们解决这个问题:

```
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
    case msg of
        WordChanged index wordString ->
            let
                model_ =
                    case model of
                        Started { sentence } ->

-- omitted

                        Initializing ->
                            model

                        Started _ ->
                            model

                        Win _ ->
                            model
            in
            ( model_, Cmd.none )

-- omitted

        WordsChosen sortIndexes ->
            let
                model_ =
                    case model of
                        Started { currentSentence } ->
                            let
                                sentence : Words
                                sentence =
                                    generateSentence currentSentence

-- omitted

                            Playing (StatePlaying sentence_)

                        Initializing ->
                            model

                        Playing _ ->
                            model

                        Win _ ->
                            model
            in
            ( model_, Cmd.none ) 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做了一些改变:

1.  第一个分支是`Playing`，现在是`Started`，我们在`let`块中添加了一个`sentence`的计算。
2.  第三支是`Started`，现在是`Playing`。

因此，流程更加正确:

1.  初始化-选择随机的句子
2.  开始-随机选择单词
3.  (演奏等的)表现，风格；(乐曲)演奏
4.  胜利

现在是`view`功能。再来加一个`viewInitilizing`视角:

```
viewInitializing : Html msg
viewInitializing =
    p []
        [ text "Initializing..." ] 
```

Enter fullscreen mode Exit fullscreen mode

并从`view`函数中调用:

```
view : Model -> Html Msg
view model =
    let
        currentView =
            case model of
                Initializing ->
                    viewInitializing

                Playing { sentence } ->
                    viewGame sentence

                Started { currentSentence } ->
                    viewStarted currentSentence

                Win { currentSentence } ->
                    viewWin currentSentence
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

游戏编译，现在每当一个新的游戏开始-它选择一个随机的句子！

当前进度保存在 repo 中，标签为 v 7.0[https://github.com/mickeyvip/words-memory-game/tree/v7.0](https://github.com/mickeyvip/words-memory-game/tree/v7.0)。

请继续关注第六部分，我们将增加句子预览的难度和倒计时。
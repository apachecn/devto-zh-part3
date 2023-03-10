# 用 Elm 编写单词记忆游戏——第 2 部分:建模和构建一个基本的单词记忆游戏

> 原文：<https://dev.to/mickeyvip/writing-a-word-memory-game-inelm---part-2-modeling-and-building-a-basic-word-memory-game-583g>

这是“在 Elm 中编写单词记忆游戏”系列的第 2 部分，查找:

*   [第 1 部分:使用 Parcel 设置 Elm 应用程序](https://dev.to/mickeyvip/writing-a-word-memory-game-inelm---part-1-setting-up-an-elm-application-with-parcel-1ppk)
*   第 2 部分:建模和构建一个基本的单词记忆游戏
*   [第三部分:重新思考模型](https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-3-rethinking-the-model-2ajp)
*   [第四部分:用随机性增加趣味](https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-4-spicing-things-up-with-randomness-58ei)
*   [第 5 部分-更多的随机性和更多的游戏](https://dev.to/mickeyvip/writing-a-word-memory-game-in-elm-part-5-more-randomness-and-more-game-1i37)

* * *

游戏视图应该显示缺少单词的句子，并在下面列出缺少的单词。玩家将可以选择一个单词，如果这个单词是正确的，它将在列表中以绿色标记。对于“简单”级别，视图将显示可用单词的下拉列表。

[![What we want the game to look like](img/c9301316701206ff54b86a633f0bdc2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FAk2gFqo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6nvhvvkuo2diqac2m2fu.png)

我们来想想游戏的`Model`应该是什么样子。为了对一个单词建模，让我们声明一个定制类型`Word`，它有变量`SentenceWord`和`HiddenWord`，每个变量保存一个`String`值:

```
type Word
    = SentenceWord String
    | HiddenWord String 
```

Enter fullscreen mode Exit fullscreen mode

这个句子是`Word`的一个`List`，我们可以把它别名为`Words` :

```
type alias Words =
    List Word 
```

Enter fullscreen mode Exit fullscreen mode

为了在玩家做出选择时更新句子中的正确单词，我们可以使用单词在句子中的索引。这意味着我们应该通过使用`Tuple` :
来更新`Word`类型以保存具有`String`值的索引

```
type Word
    = SentenceWord ( Int, String )
    | HiddenWord ( Int, String ) 
```

Enter fullscreen mode Exit fullscreen mode

`Model`现在看起来像这样:

```
type alias Model =
    { sentence : String
    , chosenWords : Words
    , chosenSentence : Words
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以硬核化`initialModel` :

```
initialModel : Model
initialModel =
    { sentence = "The pen is mightier than the sword"
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

在`chosenSentence`中，我们首先将每个`HiddenWord`的`String`值设置为空。然后这些将被玩家选择的单词更新，并通过索引与`chosenWords`条目进行比较。如果`chosenSentence`中的这个`HiddenWord`等于`chosenWords`中的`HiddenWord`之一，则选择正确。例如:

```
chosenWords =
    [ HiddenWord ( 1, "pen" )
    , HiddenWord ( 6, "sword" )
    , HiddenWord ( 3, "mightier" )
    ]

chosenSentence =
    [ SentenceWord ( 0, "The" )
    , HiddenWord ( 1, "pen" )        -- this choice is correct
    , SentenceWord ( 2, "is" )
    , HiddenWord ( 3, "sword" )      -- this choice is incorrect, wrong index
    , SentenceWord ( 4, "than" )
    , SentenceWord ( 5, "the" )
    , HiddenWord ( 6, "" )
    ] 
```

Enter fullscreen mode Exit fullscreen mode

让我们添加一个新的`viewSentence`函数来呈现句子:

```
viewSentence : Words -> Words -> Html msg
viewSentence sentence chosenWords =
    div [ class "has-text-centered" ]
        (List.map
            (\sentenceWord ->
                case sentenceWord of
                    SentenceWord ( _, word ) ->
                        span [ class "sentence-word" ] [ text word ]

                    HiddenWord ( index, word ) ->
                        span [ class "hidden-word" ] [ text "-----" ]
            )
            sentence
        ) 
```

Enter fullscreen mode Exit fullscreen mode

这非常简单:我们正在渲染的`Words`可以是显示常规单词的`SentenceWord`和显示被选中单词的`HiddenWord`。作为第一次迭代，我们只是渲染破折号。下一步我们将呈现一个下拉菜单`<select>`。

为了让单词之间有一个空格，让我们在`src`文件夹中添加一个`style.css`:

```
.hidden-word,
.sentence-word {
    display: inline-block;
    margin-left: 0.5em;
    margin-bottom: 1em;
    line-height: 2em;
}

.hidden-word:first-child,
.sentence-word:first-child {
    margin-left: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

并将其导入我们的`index.js` :

```
import { Elm } from './src/Main.elm';
import './src/style.css';

Elm.Main.init({
    node: document.getElementById('app')
}); 
```

Enter fullscreen mode Exit fullscreen mode

通过调用`viewSentence` :
更新主`view`函数

```
view : Model -> Html msg
view model =
    main_ [ class "section" ]
        [ div [ class "container" ]
            [ viewTitle
            , div [ class "box" ]
                [ p
                    [ class "has-text-centered" ]
                    [ text model.sentence ]
                , viewSentence model.chosenSentence model.chosenWords
                ]
            ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

[![Rendering a sentence with hidden words](img/024409883f354bdc55c9cc801e74898b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8vN1dK5C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/30o7o4j5f5cee6nb28r6.png)

艾莉:[https://ellie-app.com/5CQqN2ptGsha1](https://ellie-app.com/5CQqN2ptGsha1)

# 渲染下拉列表为隐藏文字

让我们添加新的`viewHiddenWord`函数，它将为`HiddenWord` :
渲染`<select>`

```
viewHiddenWord : Word -> List Word -> Html msg
viewHiddenWord hiddenWord chosenWords =
    case hiddenWord of
        HiddenWord ( _, hiddenWordText ) ->
            let
                viewOption : String -> Html msg
                viewOption wordString =
                    option
                        [ value wordString, selected (wordString == hiddenWordText) ]
                        [ text <| String.toLower wordString ]

                wordElement : Word -> Html msg                
                wordElement word =
                    case word of
                        HiddenWord ( _, wordString ) ->
                            viewOption wordString

                        SentenceWord ( _, wordString ) ->
                            viewOption wordString
            in
            div [ class "select" ]
                [ select [ class "hidden-word" ] <|
                    option []
                        [ text "" ]
                        :: List.map wordElement chosenWords
                ]

        _ ->
            text "" 
```

Enter fullscreen mode Exit fullscreen mode

`viewHiddenWord`接受当前隐藏的单词和所有缺失单词的列表，并呈现一个下拉列表供选择。

我们做的第一件事是模式匹配`Word`类型以获得我们的`HiddenWord`，因为只有对于`HiddenWord`我们才应该呈现`<select>`元素。`SentenceWord`渲染仍然在主`view`函数中处理。

`viewHiddenWord`函数呈现一个`<select>`元素并映射所有缺失单词的列表。对于其中的每一个，它调用`wordElement`函数。这里我们必须匹配两种情况，但是我们用 word 的文本呈现相同的`<option>`元素。

姑且从`viewSentence` :
说起吧

```
viewSentence : Words -> Words -> Html msg
viewSentence sentence chosenWords =
    div [ class "has-text-centered" ]
        (List.map
            (\sentenceWord ->
                case sentenceWord of
                    SentenceWord ( _, word ) ->
                        span [ class "sentence-word" ] [ text word ]

                    HiddenWord ( index, word ) ->
                        viewHiddenWord sentenceWord chosenWords
            )
            sentence
        ) 
```

Enter fullscreen mode Exit fullscreen mode

我们应该看到，我们现在可以选择任何缺失的单词:

[![Player can choose a word](img/dc5956a596d5bb6382596ddc6239bbc1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bw65IFLR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qesgkn6wg0di8y5q74ti.png)

艾莉:[https://ellie-app.com/5CQyR6WT3zca1](https://ellie-app.com/5CQyR6WT3zca1)

# 添加交互

玩家可以选择一个单词，但是我们的`Model`并没有随着这个变化而更新。我们可以使用`select`上的`onInput`事件，并用当前选择更新`model`。因为我们有一个`chosenSentence`作为有`index`的`Tuple`的`List`——我们知道我们需要更新哪个`Word`。

让我们删除`NoOp`消息类型，并添加一个新的消息类型，它将获得一个索引和一个新单词`String` :

```
type Msg
    = WordChanged Int String 
```

Enter fullscreen mode Exit fullscreen mode

更改`update`函数来处理新的`Msg`(不要忘记删除与`NoOp`消息相关的代码):

```
update : Msg -> Model -> Model
update msg model =
    case msg of
        WordChanged index wordString ->
            let
                updateWord : Word -> Word
                updateWord word =
                    case word of
                        (HiddenWord ( hiddenIndex, _ )) as hiddenWord ->
                            if hiddenIndex == index then
                                HiddenWord ( index, wordString )

                            else
                                hiddenWord

                        _ ->
                            word

                newSentence : Words
                newSentence =
                    List.map updateWord model.chosenSentence
            in
            { model | chosenSentence = newSentence } 
```

Enter fullscreen mode Exit fullscreen mode

我们映射`chosenSentence`中的所有单词，如果当前句子(`hiddenIndex`)单词的索引等于被改变的单词(`index`)的索引，我们用新的`wordString`替换`Tuple`的`String`部分。

我们使用`as`语法来引用整个匹配的单词，如果`index`不等于`hiddenIndex`，我们将返回这个单词。

如果当前单词不是一个`HiddenWord` -我们只返回当前单词。

让我们更新`viewHiddenWord`函数，从`select`元素:
发出消息

```
viewHiddenWord : Word -> List Word -> Html Msg
viewHiddenWord hiddenWord chosenWords =
    case hiddenWord of
        HiddenWord ( hiddenIndex, hiddenWordText ) ->
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
                        :: List.map wordElement chosenWords
                ]

        _ ->
            text "" 
```

Enter fullscreen mode Exit fullscreen mode

`onInput`需要一个接受`String`作为参数的函数。`WordChanged`消息接受一个`Int`和一个`String`。我们传递给`onInput` `(WordChanged hiddenIndex)`的正是它所需要的。在 Elm 中，每个函数都是自动执行的，因此，`(WordChanged hiddenIndex)`返回一个等待`String`的函数。

请注意，我们已经将`msg`(任何类型)更改为`Msg`(我们的特定消息类型)。我们也需要在`ViewSentence`函数中做同样的改变，因为它现在也传播我们的新消息:

```
viewSentence : Words -> Words -> Html Msg
   -- omitted 
```

Enter fullscreen mode Exit fullscreen mode

`Model`的初始缺失字为空`String`:

[![The `Model` has initial hidden words as an empty `String`](img/71a683082d6abd36da14d9848b683e8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--md9amlQT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pyjnafiwj7w5m92nwj3y.png)

当在下拉菜单中选择一个单词时,`Model`被更新:

[![The `Model` is updated when a word is chosen in the dropdown](img/55e7b707f4cffb9b40bad1a793832870.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FnVm2HRO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2a5bi0x8brf5he3m1o7r.png)

艾莉:[https://ellie-app.com/5CQDxjPhpwpa1](https://ellie-app.com/5CQDxjPhpwpa1)

# 提示正确性

我们已经有了一个很好的互动游戏，但是玩家不知道他们选择的单词是否正确。

我们可以呈现一个缺失单词的列表，并提示玩家他们选择的单词是否正确。

要呈现一个缺失单词列表并知道任何单词是否是正确的选择，我们需要一个缺失单词列表和一个句子单词列表。如果一个丢失的单词是句子单词的一个成员-具有相同的索引和单词串-该单词被正确地选择:

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

我们使用字体牛逼的图标和布尔玛的类来给单词涂上浅灰色或绿色。

将`viewChosenWords`添加到主`view`函数中，以呈现列表:

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
                , viewSentence model.chosenSentence model.chosenWords
                , viewChosenWords model.chosenWords model.chosenSentence
                ]
            ]
        ] 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当玩家选择正确的单词时——它会在选择的单词列表中显示为绿色:

[![Hint for a correct choice](img/c9301316701206ff54b86a633f0bdc2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FAk2gFqo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6nvhvvkuo2diqac2m2fu.png)

艾莉:[https://ellie-app.com/5CQKJZdYGMKa1](https://ellie-app.com/5CQKJZdYGMKa1)

游戏工作正常，但是`Model`还可以改进。在下一篇文章中，我们将重构它以消除无效状态。敬请期待！

当前进度保存在 repo 中，标签为 v 1.0[https://github.com/mickeyvip/words-memory-game/tree/v1.0](https://github.com/mickeyvip/words-memory-game/tree/v1.0)。
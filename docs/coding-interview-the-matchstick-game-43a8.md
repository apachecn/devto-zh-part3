# 编码面试:火柴杆游戏

> 原文：<https://dev.to/nestedsoftware/coding-interview-the-matchstick-game-43a8>

在这篇文章中，我想做一个采访式的问题解决和编码练习。我实际上还没有在面试中遇到过这个问题——我小时候在一台旧电脑上遇到过——但我确实认为这是一个很好的面试对象。它不依赖于复杂的数学或算法，所以这是一种评估基本问题解决和编程技能的好方法。实现将在 [elm](https://elm-lang.org/) 中，一个纯函数式语言/前端 web 框架。我决定使用 elm 主要是因为我以前没有尝试过，所以这篇文章是学习新东西的好借口。

## 规则

这个游戏的规则非常简单:游戏从两个玩家面前摆放的 21 根火柴杆开始。玩家轮流一次移除 1、2 或 3 根火柴杆。被迫拿最后一根火柴的玩家输了。

## 简化问题

这个游戏有没有最优的玩法？为了弄清楚这一点，让我们尽可能地简化问题:假设只剩下 1 根火柴杆，轮到我们了。我们必须接受它，因此我们输了。

然而，如果有两根火柴，我们可以拿一根，把最后一根留给另一个玩家拿。有三根火柴，我们可以拿两根，还剩下一根。如果有 4 根火柴，我们可以拿 3 根，另一个玩家再次被迫拿最后一根。

## 图样

我们可以看到一个归纳模式的出现:用一根火柴杆，我们输了。有了 2，3，4，我们就赢了。现在，有了 5 场比赛，我们又有麻烦了:无论我们做什么，我们的对手都会剩下 2、3 或 4 场比赛，我们已经知道这意味着他们会赢。有了 6、7 或 8 场比赛，我们再次处于胜利的位置。到了 9 点，我们又输了，以此类推。

我们可以将这种模式转化为一点简单的代数:如果有 m × 4 + 1 根火柴杆，其中 m 是 0 到 5 之间的整数，则当前玩家处于失败的位置。因此，这个游戏的目标就变成了试图让对手进入一个失败的位置——从那里开始，它就变成了一条单行道。我们可以看到，开始游戏的玩家可悲地注定要输！也就是说，除非对方球员失误！

## 朝着代码努力

我们知道`count`的以下值代表亏损头寸:

`count = m × 4 + 1`

`m`是 0 到 5 之间的整数，`4`是比玩家可以拿的火柴棍最大数量多 1。

让我们用整数除法求解 m:

`m = (count - 1) ÷ 4`

整数除法的余数是:

`r = (count - 1) mod 4`

如果余数是 0，我们就处于失败的境地，所以我们可以只拿一根火柴棒:要么这是最后一根，游戏结束，要么我们可以延长游戏，希望我们的对手会犯错。

另一方面，如果余数不是 0，那么我们就能赢。在那种情况下，我们应该带多少火柴杆？当轮到我们的对手进行计算时，我们希望他们得到余数 0。因此，我们需要做的就是去掉`r`对应的火柴棍数量——就是当前火柴棍数量和下一个输的位置之间的差值。

在伪代码中，我们可以将此描述为`if r is 0 take 1 else take r`。

## 单元测试

让我们编写一个单元测试来确保我们的计算正确:

```
test "matchsticksToTake gives correct values for 1 to 21" <|
    \_ ->
        let
            expectedMatchsticksToTake =
                [ ( 1, 1 ) -- lose
                , ( 2, 1 ) -- win
                , ( 3, 2 ) -- win
                , ( 4, 3 ) -- win
                , ( 5, 1 ) -- lose
                , ( 6, 1 ) -- win
                , ( 7, 2 ) -- win
                , ( 8, 3 ) -- win
                , ( 9, 1 ) -- lose
                , ( 10, 1 ) -- win
                , ( 11, 2 ) -- win
                , ( 12, 3 ) -- win
                , ( 13, 1 ) -- lose
                , ( 14, 1 ) -- win
                , ( 15, 2 ) -- win
                , ( 16, 3 ) -- win
                , ( 17, 1 ) -- lose
                , ( 18, 1 ) -- win
                , ( 19, 2 ) -- win
                , ( 20, 3 ) -- win
                , ( 21, 1 ) -- lose
                ]

            matchsticks =
                List.range 1 21

            actualMatchsticksToTake =
                List.map
                    (\count -> matchsticksToTake count)
                    matchsticks
        in
        Expect.equal
            expectedMatchsticksToTake
            actualMatchsticksToTake 
```

> `matchsticksToTake`还返回火柴棍的数量，使测试变得简单一些。

## 业务逻辑

我们已经解决了基本的逻辑，所以让我们展示实际的实现:

```
matchsticksToTake : Int -> ( Int, Int )
matchsticksToTake count =
    let
        remainder =
            remainderBy 4 (count - 1)

        take =
            if remainder == 0 then
                1

            else
                remainder
    in
    ( count, take ) 
```

就是这样！我们的测试通过了，游戏的核心逻辑也完成了！

## 用户界面和游戏玩法

编程的一个有趣方面是，业务逻辑可能是整个代码库中相对较小的一部分。在进行采访时，我想我可能只是要求这个游戏的一个简单的命令行实现。即使是我在 elm 中编写的使游戏运行的少量代码，对于一次面试编码会议来说也可能太多了。但是，如果您感兴趣，请继续阅读，我也将简要总结这一部分。

我们希望尽可能用最简单的用户界面来展示其功能。当然，我们需要显示当前的火柴棍数量。我们还需要知道轮到谁了。我决定也展示一下上一轮拿了多少根火柴。最后，我们需要一些按钮，让玩家选择他们想要的火柴棍的数量。下面是相应的 elm 代码:

```
view : Model -> Html Msg
view model =
    div []
        [ h1
            []
            [ playerTurnLabel model ]
        , h1 [] [ text (String.fromInt model.matchsticks) ]
        , button [ onClick (Take 1), disabled (disable model) ]
            [ text "take 1" ]
        , button [ onClick (Take 2), disabled (disable model) ]
            [ text "take 2" ]
        , button [ onClick (Take 3), disabled (disable model) ]
            [ text "take 3" ]
        , p [] [ text <| lastMoveString model.lastSelection ]
        ] 
```

我们可以看到，我们只需要一个非常简单的模型来跟踪这个用户界面的游戏状态:

```
type alias Model =
    { currentPlayer : Player
    , matchsticks : Int
    , lastSelection : Selection
    }

type Player
    = ComputerPlayer
    | HumanPlayer

type Selection
    = Selected Player Int
    | NoneSelected 
```

我们正在跟踪当前玩家、当前火柴棍的数量以及上一个玩家的选择。

在游戏的每一回合，elm 都会用一条消息来调用我们的`update`回调。该消息将包含当前玩家希望获得的火柴棍数量:

```
type Msg
    = ComputerTake Int
    | Take Int
    | DoNothing 
```

要么是电脑(`ComputerTake`)要么是人类玩家(`Take`)想玩一个回合。如果消息是`DoNothing`，那仅仅意味着我们返回当前的模型而不做任何改变。

当应用程序启动时，elm 运行时调用`init`函数:

```
init : () -> ( Model, Cmd Msg )
init _ =
    -- let the computer start the game
    wrapNextMsgWithCmd
        ( Model ComputerPlayer 21 NoneSelected, computerTakesNextTurn 21 ) 
```

我们希望计算机第一个回合人类玩家有机会获胜，所以我们初始化`currentPlayer`到`ComputerPlayer`。我们设置了`21`火柴棍，由于之前没有回合，所以`lastSelection`就是`NoneSelected`。

`computerTakesNextTurn 21`返回一条我们想要发送给 update 的消息，代表计算机的回合。这里的技巧是，我们想延迟这个消息一点，这样我们就不会在每个人类玩家的回合后立即更新屏幕。为此，我们使用了一个`Cmd`。通常命令是用来执行副作用的，比如发送一个 http 请求。这里我们使用一个命令来延迟计算机的运转。我们基本上是在说“请发出这个命令，然后向`update`发送以下消息。”

出于测试目的，让`computerTakesNextTurn`产生一个简单的`Msg`似乎更容易。`wrapNextMsgWithCmd`的目的是将所需的命令封装在消息中:

```
wrapNextMsgWithCmd : ( Model, Msg ) -> ( Model, Cmd Msg )
wrapNextMsgWithCmd ( nextModel, nextMsg ) =
    ( nextModel, wrapWithCmd nextMsg )

wrapWithCmd : Msg -> Cmd Msg
wrapWithCmd nextMsg =
    case nextMsg of
        DoNothing ->
            Cmd.none

        Take _ ->
            Cmd.none

        ComputerTake _ ->
            Cmd.batch
                [ Task.perform
                    (\_ -> nextMsg)
                    (Process.sleep 3000)
                ] 
```

这里我们看到，如果我们希望发送到`update`的下一条消息是一个计算机回合(`ComputerTake`，那么我们产生一个包含这条消息的`Cmd`。`Cmd`将运行`Process.sleep`任务 3 秒钟，然后用`nextMsg`调用 update。

`updateWithoutCmd`处理基本游戏:

```
updateWithoutCmd : Msg -> Model -> ( Model, Msg )
updateWithoutCmd msg model =
    case msg of
        Take selectedMatchsticks ->
            humanPlayerTakesTurn model selectedMatchsticks

        ComputerTake selectedMatchsticks ->
            computerPlayerTakesTurn model selectedMatchsticks

        DoNothing ->
            ( model, DoNothing )

humanPlayerTakesTurn : Model -> Int -> ( Model, Msg )
humanPlayerTakesTurn model selectedMatchsticks =
    case model.currentPlayer of
        HumanPlayer ->
            tryToPlayTurn
                model
                selectedMatchsticks
                (computerPlaysNextOrEndOfGame
                    model.matchsticks
                    selectedMatchsticks
                )

        ComputerPlayer ->
            rejectPlayerTurn model

computerPlayerTakesTurn : Model -> Int -> ( Model, Msg )
computerPlayerTakesTurn model selectedMatchsticks =
    case model.currentPlayer of
        ComputerPlayer ->
            tryToPlayTurn model selectedMatchsticks DoNothing

        HumanPlayer ->
            rejectPlayerTurn model 
```

这个代码确保如果一个玩家试图拿火柴杆，实际上是轮到他们了。它还检查火柴棍的数量是否有效。可能真的不需要，因为我们把输入按钮灰了，但是作为一个编写了很多服务器端代码的人，旧习惯很难改变！

我们可以看到，在人类玩家进行一轮游戏后，我们用`computerPlaysNextOrEndOfGame` :
为计算机玩家生成下一轮游戏的消息

```
computerPlaysNextOrEndOfGame : Int -> Int -> Msg
computerPlaysNextOrEndOfGame matchsticks selectedMatchsticks =
    if gameOver (matchsticks - selectedMatchsticks) then
        DoNothing

    else
        computerTakesNextTurn <|
            takeMatchsticks matchsticks selectedMatchsticks

computerTakesNextTurn : Int -> Msg
computerTakesNextTurn matchsticks =
    ComputerTake <| Tuple.second <| matchsticksToTake matchsticks 
```

我们可以看到，如果在人类玩家的当前回合之后游戏没有结束，那么我们生成适当的`ComputerTake`消息。该消息将包括基于当前玩家选择后剩余的火柴棍数量的电脑玩家的`matchsticksToTake`。

## 榆树建筑

基本的 [elm 架构](https://guide.elm-lang.org/architecture/)是一个相当简单的循环:当用户与 UI 交互时，这触发了适当的消息被发送到`update`函数。`update`返回一个新模型，并根据收到的消息进行修改。elm 运行时使用这个新模型更新它的内部状态，并使用它来刷新视图。

`update`也返回一个`Cmd`,它可以用来请求 elm 执行副作用。elm 是一门纯语言，所以 elm 中的函数都不直接执行 I/O，它们只是用指令返回`Cmd`对象。`elm`运行时使用这些命令来执行实际的 I/O，但是它是作为一个单独的步骤来完成的。

除了命令之外，elm 还支持[订阅](https://guide.elm-lang.org/effects/time.html)，它可以用来处理定期更新，但是在这个例子中我们没有使用订阅。

## 演示

你可以在 codepen 查看这个例子的演示:

[https://codepen.io/nestedsoftware/embed/LaNqRP?height=600&default-tab=result&embed-version=2](https://codepen.io/nestedsoftware/embed/LaNqRP?height=600&default-tab=result&embed-version=2)

## 源代码

github 提供了所有的源代码:

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [嵌套软件](https://github.com/nestedsoftware) / [火柴杆游戏](https://github.com/nestedsoftware/matchstick-game)

### 在 elm 中实现的 21 火柴杆游戏

<article class="markdown-body entry-content p-5" itemprop="text">

这个代码要求 [elm](https://elm-lang.org/) 被[安装](https://guide.elm-lang.org/install.html)。

*   要编译代码并生成 Main.js，运行`elm make src/Main.elm --output=Main.js`
*   要运行单元测试，运行`elm-test`。
*   [演示](https://codepen.io/nestedsoftware/pen/LaNqRP)

这两个命令都可以从项目的根目录运行。

游戏规则:有 21 根火柴杆。两名玩家轮流，一次移除 1、2 或 3 根火柴杆。被迫移除最后一根火柴杆的玩家输了。

</article>

[View on GitHub](https://github.com/nestedsoftware/matchstick-game)
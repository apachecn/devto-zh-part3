# 在 elm-ui 中使用菜单

> 原文：<https://dev.to/stevensonmt/-working-with-menus-in-elm-ui-292b>

最近我不得不弄清楚如何使用 Elm 0.19 中的`mdgriffith/elm-ui`包正确地打开和关闭一个模态排序的菜单。对于那些不熟悉的人来说，`elm-ui`包允许你创建前端界面，而不用求助于 CSS 或 HTML(偶尔有例外)。如果你在 Elm 工作，我强烈推荐。如果你还没有在 Elm 工作，我会把`elm-ui`作为它的一个卖点和一个尝试的理由。

这里是一个非常基本的概念在一个[艾莉](https://ellie-app.com/4jHGYY9PK6Ja1)。
我们简单的`model`有两个字段:计数和菜单。其定义如下:

```
type alias Model =
    { count : Int, menu : Bool }

initialModel : Model
initialModel =
    { count = 0, menu = False } 
```

以下是视图代码:

```
view : Model -> Html Msg
view model =
    Element.layout [ Element.Background.color (Element.rgb255 30 30 30), padding 10]
    <| column [spacing 10, width <| px 200, Element.Background.color (Element.rgb255 200 30 10)] 
           [ el [ width fill
                , padding 8
                , Element.Font.center
                , Element.Background.color <| 
                      Element.rgb255 120 120 180
                ] <| 
                     Element.text (String.fromInt model.count)
           , el [ centerX
                , Element.Events.onClick OpenMenu
                , Element.below <| myMenu model
                ] <| 
                    Element.text "I'm a menu!"
           ] 
```

最后，我们的`Msgs`和`update`函数为我们的演示应用程序填充了 Elm 架构:

```
type Msg
    = Increment
    | Decrement
    | OpenMenu
    | CloseMenu

update : Msg -> Model -> Model
update msg model =
    case msg of
        Increment ->
            { model | count = model.count + 1 }

        Decrement ->
            { model | count = model.count - 1 }

        OpenMenu -> 
            { model | menu = True }

        CloseMenu -> 
            { model | menu = False } 
```

如果您尝试 ellie 链接，您会发现菜单打开，显示增加和减少型号计数的按钮。很简单。但是菜单一直开着。解决这个问题的第一步是将菜单元素的`onClick`属性从`OpenMenu`动作改为`ToggleMenu`动作。
所以现在我们有了

```
type Msg
    = Increment
    ...
    | ToggleMenu

update : Msg -> Model -> Model
update msg model =
    case msg of
       ...
        ToggleMenu -> { model | menu = not model.menu }

view : Model -> Html Msg
view model =
    ...
    , el [ centerX
                , Element.Events.onClick ToggleMenu
                , Element.below <| myMenu model
                ] <| 
                    Element.text "I'm a menu!"
    ... 
```

太棒了。现在，当点击触发元素时，菜单打开和关闭。此处见更新版本[。但是许多用户可能会希望在单击视口的任何其他部分时关闭菜单。这是我想到的第一个设置方法:](https://ellie-app.com/4jHQRRKK4NXa1)

```
view : Model -> Html Msg
view model =
    Element.layout [ Element.Events.onClick CloseMenu
    ... 
```

这应该意味着点击任何地方都会发送`CloseMenu` `Msg`。看到这个方法的问题[这里](https://ellie-app.com/4jHVqgY4NZMa1)。尝试启动菜单，然后打开调试器。

你看到的是，点击菜单发送的`ToggleMenu` `Msg`正是我们想要的，但它紧接着是`CloseMenu` `Msg`。这导致菜单永远打不开。呃。

原因是`onClick`事件从子元素传播到父元素。我们不能只在`layout`上使用`ToggleMenu`修复，因为那会在你点击任何地方的任何时候打开菜单。

几乎可以肯定有很多方法可以解决这个问题，但是我想到的方法是使用巧妙命名的函数 [`Element.behindContent`](https://package.elm-lang.org/packages/mdgriffith/elm-ui/1.1.0/Element#behindContent) 在布局内容后面放置一个布局大小的空元素。赋予这个元素一个`onClick CloseMenu`属性是可行的，因为它与菜单元素没有父子关系。

这种方法工作正常，但仍有一些小故障。在这里看它的行动[。在关闭菜单的问题上，最明显的问题是显示计数器的元素在被点击时没有发送`CloseMenu` `Msg`，因为它在前面，而不是我们刚刚添加的元素的子元素。](https://ellie-app.com/4jJ5QMPPTD2a1)[我的解决方案](https://ellie-app.com/4jJgMcr8Gxfa1)是给那个元素添加`onClick CloseMenu`属性。不过，我很想听听更好的解决方案！

对我来说，第二个明显的问题是，每次增加或减少计数时，菜单都会关闭。我想大多数用户会希望菜单一直打开，直到他们想增加/减少多少次就增加/减少多少次。我将把这个问题的解决方案留给感兴趣的人作为练习。

感谢阅读。希望对你有帮助。如果你有关于榆树或`elm-ui`的问题，我强烈推荐[榆树话语](https://discourse.elm-lang.org)和榆树与`elm-ui` [休闲频道](https://elmlang.slack.com/messages)。
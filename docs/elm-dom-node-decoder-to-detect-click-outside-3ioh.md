# Elm DOM 节点解码器检测外部点击

> 原文：<https://dev.to/margaretkrutikova/elm-dom-node-decoder-to-detect-click-outside-3ioh>

前几天，我在`elm`的一个辅助项目中实现了一个可重用的下拉菜单，并希望当我点击它的外部时下拉菜单关闭。

我想出了一个特别的解决方案，感觉就像😕，所以我请求 slack 上的 elm 社区给我指出正确的方向。那里的人们提出了几个创造性的解决方案，并继续针对不同的边缘情况实施它们。作为一名 elm noob，我很高兴能从更有经验的 elm 开发者那里学到东西，并决定在几篇文章中分享我从那次讨论中学到的东西。

这一个是关于实现一个通用的方法来检测点击外部使用一些先进的解码机制从事件对象获得 DOM 节点。

多酷啊，**解码 elm 中的 DOM 节点**？🤯

## 方案大纲

我将使用一个简单的下拉菜单作为例子，但它可以是任何其他元素，从检测外部点击(自动完成，菜单，弹出等)中受益。

解决方案是确定被单击的 DOM 节点是否位于下拉列表中:

*   订阅文档上的全局`mousedown`事件，
*   获取调度事件的元素(事件目标)，
*   通过递归遍历 DOM 树来检查目标元素是否是 dropdown 的后代，
*   如果存在匹配(通过 id、类名或任何其他属性)，则单击发生在下拉列表中——保持它打开。

DOM API 有 [Node.contains](https://developer.mozilla.org/en-US/docs/Web/API/Node/contains) ，它检查一个节点是否是另一个节点的后代。这个功能在 Elm 中似乎不存在，但是不用担心，我们将推出自己的实现。

## 订阅全球`mousedown`

[浏览器。事件](https://package.elm-lang.org/packages/elm/browser/latest/Browser-Events)包允许将监听器附加到整个文档的事件上。它公开了`onMouseDown`监听器，该监听器接受一个 JSON 解码器来解码事件对象，如果解码成功，就发送一条消息和结果。

要使用 JSON 解码器，请安装 [Json-Decode 包](https://package.elm-lang.org/packages/elm/json/latest/Json-Decode) :

```
elm install elm/json 
```

Enter fullscreen mode Exit fullscreen mode

并在`subscriptions` :
中使用`onMouseDown`

```
import Browser.Events
import Json.Decode as Decode

subscriptions : Model -> Sub Msg
subscriptions _ =
    Browser.Events.onMouseDown (Decode.succeed MouseDown) 
```

Enter fullscreen mode Exit fullscreen mode

*注意:要使用`subscriptions`，elm 程序至少应该是`Browser.element`类型，`Browser.sandbox`不能与外界通话，不支持订阅，[参见 elm 指南效果](https://guide.elm-lang.org/effects/)。*

我们的订阅现在做的不多:`Decode.succeed`忽略 JSON 事件对象，只向更新函数发送`MouseDown`消息。

## 事件目标的递归解码

`mousedown`事件的事件目标是一个具有属性 [`parentNode`](https://developer.mozilla.org/en-US/docs/Web/API/Node/parentNode) 的 DOM 节点，我们可以使用该属性递归地遍历 DOM 树，直到找到下拉节点或到达树的顶部。为了确定节点是否匹配，我们将使用它们的 id。

我将首先解释解码器的一个更详细的实现，然后跳到最终的紧凑和优雅的解决方案，乍一看这似乎很神秘。

### 天真解码

下面是一个表示 DOM 节点的数据结构:

```
type DomNode
    = RootNode { id : String }
    | ChildNode { id : String, parentNode : DomNode } 
```

Enter fullscreen mode Exit fullscreen mode

这种联合类型的解码器将使用 [`Decode.lazy`](https://package.elm-lang.org/packages/elm-lang/core/5.1.1/Json-Decode#lazy) 来解码递归结构，使用 [`Decode.oneOf`](https://package.elm-lang.org/packages/elm-lang/core/5.1.1/Json-Decode#oneOf) 来解码联合的各个构造函数。

一般来说，`Decode.oneOf`对于解码可能是不同格式的数据很有用，它的工作原理是接受不同的解码器，并按顺序尝试它们，直到其中一个成功。

下面是实现:

```
domNode : Decode.Decoder DomNode
domNode =
    Decode.oneOf [ childNode, rootNode ]

rootNode : Decode.Decoder DomNode
rootNode =
    Decode.map (\x -> RootNode { id = x })
        (Decode.field "id" Decode.string)

childNode : Decode.Decoder DomNode
childNode =
    Decode.map2 (\id parentNode -> ChildNode { id = id, parentNode = parentNode })
        (Decode.field "id" Decode.string)
        (Decode.field "parentNode" (Decode.lazy (\_ -> domNode))) 
```

Enter fullscreen mode Exit fullscreen mode

这里的诀窍是相互递归引用的解码器:解码`ChildNode`引用`domNode`解码器并需要使用`Decode.lazy`，解码`DomNode`引用子节点解码器。

现在，我们可以从订阅中的事件中解码出`target`，并发送一条带有解码后的`DomNode`的消息。`update`函数将递归地遍历树中的节点，并检查与下拉列表匹配的节点。

但是令人兴奋的想法来了:**当递归遍历树时，如果我们让解码器确定被点击的节点是否在下拉菜单*中会怎么样？***🤔

解码器并没有将整个递归 DOM 结构交给更新函数，而是回答了如何处理下拉菜单的基本问题:**关闭还是不关闭？**

### 外部-下拉-解码器

一个更好的解码器将由一系列遍历 DOM 树并即时做出决定的解码器组成:

```
isOutsideDropdown : String -> Decode.Decoder Bool
isOutsideDropdown dropdownId =
    Decode.oneOf
        [ Decode.field "id" Decode.string
            |> Decode.andThen
                (\id ->
                    if dropdownId == id then
                        -- found match by id
                        Decode.succeed False

                    else
                        -- try next decoder
                        Decode.fail "continue"
                )
        , Decode.lazy (\_ -> isOutsideDropdown dropdownId |> Decode.field "parentNode")

        -- fallback if all previous decoders failed
        , Decode.succeed True
        ] 
```

Enter fullscreen mode Exit fullscreen mode

这些解码器的职责是:

*   第一个解码器将检查节点的 id，如果发现匹配，则通过`False`(下拉菜单中的*)成功，否则会导致其他解码器介入，*
*   第二个解码器将递归调用父解码器，如果`parentNode`为空(到达树的顶部),可能会失败，导致最后一个解码器运行，
*   最后一个解码器简单地以`True` ( *在下拉菜单*之外)成功。

顺便提一下，`Decode.fail`接受一个任意的字符串，这个字符串成为一个定制的错误消息。

我们还需要一个解码器来获取`target`，将其馈送给`isOutsideDropdown`解码器，并根据结果
发送消息`Close`

```
outsideTarget : String -> Decode.Decoder Msg
outsideTarget dropdownId =
    Decode.field "target" (isOutsideDropdown "dropdown")
        |> Decode.andThen
            (\isOutside ->
                if isOutside then
                    Decode.succeed Close

                else
                    Decode.fail "inside dropdown"
            ) 
```

Enter fullscreen mode Exit fullscreen mode

[`Decode.andThen`](https://package.elm-lang.org/packages/elm-lang/core/5.1.1/Json-Decode#andThen) 用于根据`isOutsideDropdown`解码器的结果创建新的解码器。

最后一步，如果下拉菜单是打开的(为了避免不必要的监听事件),我们订阅`mousedown`,并将下拉菜单的 id 传递给解码器。

```
subscriptions : Model -> Sub Msg
subscriptions model =
    if model.open then
        Browser.Events.onMouseDown (outsideTarget "dropdown")

    else
        Sub.none 
```

Enter fullscreen mode Exit fullscreen mode

## 解码器的其他应用

这种 DOM 节点解码技术非常强大，并且可以扩展到不仅仅是确定外部点击。

为了提高下拉菜单的键盘可访问性，我们需要在它失去焦点时关闭它(当“跳出”它时)。一个技巧是使用`focusout`事件，并将同一个解码器应用到 [`relatedTarget`](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent/relatedTarget) 属性，这给了将要接收焦点的元素。

在我的下一篇文章中，我将展示如何使下拉菜单更易访问，以及如何处理焦点和键盘事件。

* * *

这里是在 [github](https://github.com/MargaretKrutikova/elm-accessible-dropdown/tree/simple-dropdown) 上的源代码，这里是在 [ellie](https://ellie-app.com/5GKDGkdr8tBa1) 中稍微修整过的版本供你玩。

免责声明:如果没有令人惊叹的 elm 社区，这一切都不会发生，如果没有你，这一切都不会有意义，读者！😍*未完待续...*

另外，如果你需要在 ReasonML 中实现检测外部点击，你可以到我的另一篇文章中的[看看，在那里我解释了如何在 ReasonReact 应用程序中创建一个自定义的`useClickOutside`钩子。](https://dev.to/margaretkrutikova/reasonreact-use-dom-api-and-hooks-to-detect-click-outside-4f74)

我似乎有点击外部的困扰...无论如何，感谢阅读！😅
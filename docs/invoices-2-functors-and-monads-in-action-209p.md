# 发票 2:函子和单子在行动

> 原文：<https://dev.to/drbearhands/invoices-2-functors-and-monads-in-action-209p>

考虑到我在这个系列中的上一篇文章没有引起太多的关注，我决定把它写得简短一些。如果你对在 Elm 写发票感兴趣，我已经做了一个公共的[库](https://gitlab.com/DrBearhands/elm-invoice)。我自己也在使用它，但是在写的时候，它肯定还需要一些润色。

所以让我们转而关注每个人似乎最关心的东西，函子和单子。

```
type ColumnSize
  = [...]

columnSizeToString : ColumnSize -> String
columnSizeToString gs = [...]

columns : List (Html msg, ColumnSize) -> Html msg
columns cs =
  let
    columnSizes : String
    columnSizes =
      List.map ( columnSizeToString << Tuple.second ) cs
      |> List.intersperse " "
      |> String.concat

    inDiv : Html msg -> Html msg
    inDiv element = div [] [element]
  in
    div
      [ style "display" "grid"
      , style "grid-template-columns" columnSizes
      ]
      ( List.map ( inDiv << Tuple.first ) cs ) 
```

Enter fullscreen mode Exit fullscreen mode

这是怎么回事？

函数`columns`使用`display: grid;`将多个元素放入列中。

我决定让每一列都有自己的宽度。因此，我们不是传递一个`Html msg`列表，而是传递一个`(Html msg, ColumnSize)`列表。通过将元素和它们的宽度耦合成元组，我们确保了我们的宽度正好和列一样多。

`ColumnSize`是我之前声明的一种类型，表示列应该有多宽(例如`1fr`或`50px`)。我使用的是`ColumnSize`而不是`String`，所以参数的正确性会在编译时检查。如果我使用了`String`，我就可以生成一个`"7 kg"`长的列，这将导致运行时出现一个无声的错误，而不是在编译时出现一个令人讨厌的大错误。

* * *

现在我们来看看`columnSizes`。它使用给定的元组列表作为输入，构造一个表示网格的列模板的字符串。

这就是函子发挥作用的地方。正如我们在前面的中看到的[，函子是任何一个具有映射函数的*一元类型构造函数*。列表就是这种情况:列表本身不是一种类型，但字符串列表是，它有一个映射函数，将函数应用于列表的所有元素，返回一个新的列表。](https://dev.to/drbearhands/functors-monads-and-better-functions-26f3)

这正是这里发生的事情:

```
List.map ( columnSizeToString << Tuple.second ) cs 
```

Enter fullscreen mode Exit fullscreen mode

我们通过在`Tuple.second`之后组合`columnSizeToString`来创建一个函数，这个函数被传递给`List.map`，并且产生的函数被应用到 cs 上，cs 是父函数的输入的元组列表。

所以，对于 cs 的每个元素(一个 tuple `(Html msg, ColumnSize)`)，我们首先应用`Tuple.second`，即复合的第一部分。这将返回元组的第二个元素(一个`ColumnSize`)。我们将它提供给组合的第二个元素，`columnSizeToString`，返回一个字符串。因此，整个表达式的结果是一个`List String`。

在下一行:

```
|> List.intersperse " " 
```

Enter fullscreen mode Exit fullscreen mode

我们*通过管道*将前面表达式的结果(字符串的输出列表)传输到`List.intersperse " "`，它将空间元素分散到我们的列表中。

最后:

```
|> String.concat 
```

Enter fullscreen mode Exit fullscreen mode

我们通过管道进入`String.concat`，它将一系列字符串转换成一个单独的字符串。

* * *

现在，让我们来看一些使用列表的*一元*属性的代码。这是来自`keyValueLayout`内部的片段。

```
entryLayout : List (String, String) -> Html msg
    entryLayout entry =
      case entry of
        [] -> div [] []
        (headerKey, headerValue) :: entryRemainder ->
          entryHeaderLayout headerKey headerValue
            :: List.map entryBodyLayout entryRemainder
            |> List.map (\(a, b) -> [a, b])
            |> List.concat
            |> div
              [ style "display" "grid"
              , style "grid-template-columns" "2fr 5fr"
              , style "grid-column-gap" "10px"
              ]

entryHeaderLayout : String -> String -> (Html msg, Html msg)
entryHeaderLayout k v = [...]
entryBodyLayout : ( String, String ) -> (Html msg, Html msg)
entryBodyLayout (k, v) = [...] 
```

Enter fullscreen mode Exit fullscreen mode

这段代码将成对的字符串转换成一个网格元素，其中包含两个整齐对齐的列。

以下几行:

```
entryHeaderLayout headerKey headerValue
  :: List.map entryBodyLayout entryRemainder 
```

Enter fullscreen mode Exit fullscreen mode

创建一个类型为`List (Html msg, Html msg)`的列表。它们不是特别有趣，所以我不会深入研究它们。

我们需要一个类似于`[key1, value1, key2, value2,...]`的列表，而不是元组列表，因为这是网格元素作为子元素所需要的。我们可以使用`\(a, b) -> [a, b]`将一个元组转换成一个列表，并且我们可以使用`map`函数，但是那将留给我们一个元素列表的列表，这不是我们想要的。

因为列表是一个单子，我们*知道*我们可以“绑定”一个函数。
所以一个`type1 -> List type2`类型的函数，也就是`\(a, b) -> [a, b]`的类型，可以被‘绑定’，这样一个`List type1`就可以变成一个`List type2`。本质上，一个平面地图。这就是我们想要的！在我们的例子中，`type1`是`(Html msg, Html msg)`，而`type2`是`Html msg`。

List 用它的`concat`函数给了我们一元属性，它将一个`List (List a)`展平为`List a`。

因为我们已经有了`map`，映射后可以简单的展平，这里就是这么做的:

```
|> List.map (\(a, b) -> [a, b])
|> List.concat 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！成为单子所需要的:一张地图和一个平面地图函数。

<small><sub>好吧，从技术上来说，你也需要一个单独的`a -> F a`函数，但是这个函数通常是微不足道的。</sub></small>
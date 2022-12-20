# 保持解码器简单

> 原文：<https://dev.to/jwoudenberg/keeping-decoders-simple-360e>

理解 JSON 解码器是如何工作的可能需要一点时间。尤其是像`Json.Decode.andThen`这样的函数需要一点练习才能使用。好的一面是，您几乎总是可以使用这些函数，这通常会产生更好的代码。

为了举例说明，我想出了一些描述人和他们的宠物的 JSON。我们将尝试为它编写一个简单的 Elm 解码器。为了让事情变得更有挑战性，我故意让 JSON 结构变得不太好用。让我们来看看吧！

```
[  {  "id":  1,  "species":  "Human",  "name":  "Jasper"  },  {  "id":  2,  "species":  "Dog",  "name":  "Otis",  "owner":  1  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

上面的 JSON 包含一个人和一个宠物，混合在一起形成一个列表。一个更长的样本可以包含多个主人和宠物，仍然在一个列表中，并以任何顺序排列😨。幸运的是，我们可以在 Elm 应用程序中自由地对这些数据进行不同的建模。下面的`Model`类型可以存储相同的数据，但是确保所有的宠物都有一个主人。

```
type alias Model =
    List Human

type alias Human =
    { id : Id
    , name : String
    , pets : List Pet
    }

type alias Pet =
    { id : Id
    , name : String
    , animal : Animal
    }

type Animal
    = Dog
    | Cat
    | Fish

type Id
    = Id Int 
```

Enter fullscreen mode Exit fullscreen mode

那看起来不是更好吗？一旦我们将数据放入我们的 Elm 应用程序，它将变得更加易于使用！要将 JSON 数据加载到 Elm 的应用程序中，我们需要编写一个 JSON 解码器。但是在这种特殊情况下，这不是一项简单的任务，因为 JSON 和 Elm 类型的形状是如此不同。

我们可以编写一个解码器，将 JSON 数据解码成我们的`Model`类型，但是要这样做，我们需要解码器工具箱中的所有工具。幸运的是，有一种更简单的方法，我们总是可以使用，它只使用基本的`Json.Decode`原语。要使用它，我们需要一个额外的 Elm 类型，一个看起来像我们试图读取的 JSON 的类型。姑且称这种类型为`BackendData`。

```
type alias BackendData =
    List Individual

type alias Individual =
    { id : Int
    , species : String
    , name : String
    , owner : Maybe Int
    } 
```

Enter fullscreen mode Exit fullscreen mode

这种类型与我们试图解码的 JSON 几乎具有完全相同的形状！正因为如此，我们为`BackendData`写一个解码器要比为`Model`写一个容易得多。让我们现在写那个解码器。

```
import Json.Decode as Decode exposing (Decoder)

dataDecoder : Decoder BackendData
dataDecoder =
    Decode.list individualDecoder

individualDecoder : Decoder Individual
individualDecoder =
    Decode.map4
        (\id species name owner -> Individual id species name owner)
        Decode.int
        Decode.string
        Decode.string
        (Decode.maybe Decode.int) 
```

Enter fullscreen mode Exit fullscreen mode

不错吧？如果我们选择了正确的 Elm 类型，我们总是可以只使用少量技巧来解码 JSON 结构:

*   我们可以用一个`List`在 Elm 中表示一个 JSON 数组。我们用`Decode.list`解码。
*   一个 JSON 对象，我们可以用一条记录来表示 Elm。我们使用其中一个`Decode.map`函数对其进行解码(取决于记录的字段数量)。
*   JSON 值(数字和字符串)在 Elm 中可以用`Int` s、`Float` s 和`String` s 来表示
*   一个可能不存在的字段可以在 Elm 中用一个`Maybe`来表示。我们用`Decode.maybe`解码。
*   一个可能为空的字段在 Elm 中可以用一个`Maybe`来表示。我们可以用`Decode.nullable`解码。
*   如果 JSON 中的一个字段可以包含两种不同的类型，偶尔你会需要`oneOf`。

这是一个不错的结果，但我们还没有完成。我们已经成功地将我们的 JSON 解码成一个`BackendData`类型，但是它还不是一个`Model`。我们开始使用的 JSON 并不令人愉快，因为它的结构与我们希望在 Elm 应用程序中使用的`Model`类型非常不同。我们的`BackendData`类型与我们的 JSON 具有相同的结构，因此也继承了它的问题。

我们需要一个函数将我们的`BackendData`转换成`Model`，姑且称之为`fromBackendData`。它会有这样一个类型。

```
fromBackendData :: BackendData -> Result String Model 
```

Enter fullscreen mode Exit fullscreen mode

该函数返回一个`Result`,因为在这个转换过程中有一些事情可能会出错。举个例子:我们的数据可以包含一个`whale`，它既不是人也不是宠物。在这种情况下,`Result`允许我们返回一个错误来表明出错了。

写起来需要一些努力，这就是为什么我们不再需要担心解码器是件好事。编写 JSON 解码器很难，编写转换函数也很难。我们总是不得不处理这两个问题，但是引入`BackendData`允许我们一次面对一个问题，而不是一个综合的大问题。

还有其他好处。Elm 编译器将对我们编写`fromBackendData`有很大帮助。它不会为编写直接解码`Model`的解码器提供同样多的帮助。

我们还可以编写测试来帮助我们获得正确的转换逻辑。我们可以为直接解码`Model`的解码器编写测试，但是我们需要将 JSON 字符串作为输入传递给它。我们的转换函数接受并返回常规的 Elm 类型，因此它的测试将更容易编写和维护。

因为它很长，我将在这篇博文中省略掉`fromBackendData`的实现。如果你感兴趣，可以查看一下[Ellie](https://ellie-app.com/7WW3gGhHYtBa1)中的代码(非常感谢 [Tony Gu](https://dev.to/digitalsatori) 对代码的大量修正和改进！).

就是这样！每当我们面临编写复杂的 JSON 解码器时，我们可以选择编写一个中间类型、简单的解码器和转换函数。
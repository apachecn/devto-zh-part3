# 转换函数，五星

> 原文：<https://dev.to/jwoudenberg/conversion-functions-five-stars-2l87>

我最近在一个有很多更新功能的应用程序中发现了一个我认为可以简化更新功能的巧妙方法。诀窍是创建一种与模型类型相似但不同的新类型，这是我非常喜欢的策略，我曾与人合著了两篇关于它的博文，分别是[视图类型](https://dev.to/noredink/a-type-for-views-5g04)和[保持解码器简单](https://dev.to/jwoudenberg/keeping-decoders-simple-360e)。因此，在复杂的视图和解码逻辑之后，这次我们将尝试复杂的更新逻辑。

之后，我想分享一下我创作相似类型的热情来自哪里。先睹为快:这是转换功能。原来我喜欢转换函数。

## 一个应用实例

假设我们正在构建一个应用程序来支持人类努力填充太阳系 <sup id="fnref1">[1](#fn1)</sup> 。它应该记录太阳系所有行星的人口，以及轨道上的任何船只。这里有一个应用程序的模型:

```
type alias Model =
    Body

type Body
    = Body
        { name : Name
        , population : Int
        , moons : List Body
        }

type alias Name =
    String

solarSystem : Model
solarSystem =
    Body
        { name = "Sun"
        , population = 0
        , moons =
            [ Body
                { name = "Earth"
                , population = 7700000000
                , moons =
                    [ Body
                        { name = "Moon"
                        , population = 0
                        , moons = []
                        }
                    , Body
                        { name = "Worf's starship"
                        , population = 1
                        , moons = []
                        }
                    ]
                }
            , Body
                { name = "Mars"
                , population = 0
                , moons = []
                }
            ]
        } 
```

Enter fullscreen mode Exit fullscreen mode

正如你看到的，我们这里没有一个完整的太阳能系统，但它足以说明应用程序需要支持的两个操作:

*   我们想把飞船从一个星球转移到另一个星球。
*   我们想调整这些星球的人口。

这里有一个`Msg`类型:

```
type Msg
    = SetPopulation Name Int
    | Move { body : Name, destinationOrbit : Name } 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利。现在我们开始实现更新功能，这就是事情变得不愉快的地方。为了处理`SetPopulation`消息，我们需要搜索整个太阳系来找到我们需要更新的行星。处理`Move`将会更加困难:考虑一下我们需要采取什么步骤将 Worf 转移到火星；

1.  首先，我们需要在太阳系的某个地方找到 Worf，并将他存储在一个变量中。
2.  然后我们需要找到 Worf 运行的天体，并把 Worf 作为该天体的卫星移除。
3.  最后，我们需要找到 Worf 要移动到的身体，并插入我们存储的 Worf 的副本，作为该身体的新月。

哦，最后一件事，如果结果是目的体不存在，我们应该取消整个行动。否则 Worf 将会从太阳系中完全消失。

## 一个简单的`update`功能

当我们这样分解时，听起来这个逻辑会变得相当复杂。因此，在采用这种方法之前，让我们试着考虑不同的太阳系类型。有没有一种方法可以使处理上述消息变得简单？有，有！下面是一种可能的方法:

```
type alias FlatModel =
    Dict Name FlatBody

type alias FlatBody =
    { orbiting : Maybe Name
    , population : Int
    } 
```

Enter fullscreen mode Exit fullscreen mode

为了证明更新这种类型确实更容易，下面是一个`setPopulationOf`和`moveBodyTo`函数的实现:

```
setPopulationOf : Name -> Int -> FlatModel -> FlatModel
setPopulationOf name newPopulation flatModel =
    Dict.update
        name
        (Maybe.map (setPopulation newPopulation))
        flatModel

setPopulation : Int -> FlatBody -> FlatBody
setPopulation newPopulation flatBody =
    { flatBody | population = newPopulation }

moveBodyTo : Name -> Name -> FlatModel -> FlatModel
moveBodyTo movingBody destinationOrbit flatModel =
    Dict.update
        movingBody
        (Maybe.map (moveBody destinationOrbit))
        flatModel

moveBody : Name -> FlatBody -> FlatBody
moveBody destinationOrbit flatBody =
    { flatBody | orbiting = Just destinationOrbit } 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们不需要三步计划来将 Worf 转移到另一个星球。`setPopulationOf`和`moveBodyTo`都更新字典中的单个值——就是这样！考虑到`FlatModel`对`Model`的三驾马车，我们是不是应该完全抛弃`Model`，只和`FlatModel`合作？也许对于这个玩具例子来说，这不是一个坏主意，但这样做肯定有缺点。

其中一个缺点是`FlatModel`类型不能强制每个物体都围绕另一个存在的物体运行。我们可以把 Worf 定义为`{ population = 1, orbiting = Just "Teapot" }`，而太阳系中没有`Teapot`。换句话说，`FlatBody`比`Body`允许更多的“不可能状态”，作为一个规则，当我们可以的时候，最好避免这些。

另一个缺点是选择模型类型来简化我们的更新逻辑会使我们的视图逻辑变得复杂。不难想象最初的`Model`的树形结构将会被证明是方便绘图的:我们将能够首先绘制太阳，然后是围绕它的地球，然后是围绕地球的月亮，等等。`FlatModel`没有按照有用的顺序给我们提供行星体。因此，更改`Model`类型将简化更新逻辑，但代价可能是使`view`逻辑变得复杂。

理想情况下，我们希望两种类型都能发挥各自的优势。我们保留我们的`Model`类型，所以我们防止了一堆不可能的状态，但是对于更新，我们使用我们的`FlatModel`类型，它更容易操作。我们可以这样做，但是我们需要在两种类型之间转换函数。现在我只给出这些转换函数的类型:

```
toFlatModel : Model -> FlatModel
toFlatModel = Debug.todo "We'll need to implement this function."

fromFlatModel : FlatModel -> Maybe Model
fromFlatModel = Debug.todo "And this function as well." 
```

Enter fullscreen mode Exit fullscreen mode

您可能会注意到`fromFlatModel`返回一个`Maybe`。这是因为，正如我们以前看到的，`FlatModel`允许某些不可能的状态。我们不能将这样的状态转换成不允许这些状态的`Model`，所以在这种情况下我们将返回`Nothing`。

一旦我们实现了这些转换函数，我们就可以用它们来实现我们的`update`函数。

```
update : Msg -> Model -> Model
update msg model =
  case msg of
      SetPopulation body newPopulation ->
        toFlatModel model
          |> setPopulationOf body newPopulation
          |> fromFlatModel
          |> Maybe.withDefault model
      Move { body, destinationOrbit }
        toFlatModel model
          |> moveBodyTo body destinationOrbit
          |> fromFlatModel
          |> Maybe.withDefault model 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们差不多完成了。剩下要做的一件事就是实现那些转换函数。由于篇幅原因，我不打算在这里列出它们，但你可以在本帖的[完整代码](https://ellie-app.com/4j2g3JjhPVva1)中查看它们。

## 值得吗？

我们实现了我们设定的目标:我们有一个简单的更新函数，而没有损害我们的`Model`类型的强度。为此，我们需要创建一个额外的`FlatModel`类型和两个转换函数。这样的代价值得吗？

也许使用两种不同类型的警告声音来表达相同的概念。这是一种重复，为了实现最大程度的代码重用，我们经常被告知要避免这种情况。您必须自己做出决定，但是我会随时用代码复杂度的降低来换取额外的类型。

但是我们降低代码复杂度了吗？我们最终编写了两个新的转换函数，它们本身并不简单。也许更诚实地说，我们将应用程序的复杂性从更新功能转移到了转换功能。我仍然称之为胜利，因为我相信转换函数是你能拥有的最复杂的代码。为了支持这一观点，我想用这篇文章的最后一部分来看看转换函数的好处。

## 俯仰转换功能

让我们来看看转换函数的一些好的性质。

转换函数不会泄露它们的问题。转换函数可能是混乱的，但用桑迪·梅斯创造的术语来说，最坏的情况是它们是 T2 奥米加混乱。无论转换函数有多混乱，它总是被限制在它所转换的这两种类型之间。这种混乱不会蔓延到应用程序的其他部分，从而导致代码混乱。我觉得这是个令人欣慰的想法。

转换函数很容易写。当你编写转换函数时，Elm 编译器是最有用的。它对太阳系或任何其他领域一无所知，所以在这方面帮不了你什么。如果我们不小心克隆了 Worf 而不是移动了他，编译器不会发现这种错误。但是接受一种特定类型并返回另一种类型的转换函数才是 Elm 编译器真正需要的。在我们的示例应用程序中，我们有大量的转换代码，只有少量的域操作代码，因此我们优化了我们的应用程序，以发挥编译器的优势。

**转换函数很容易测试。**如果你想知道什么是使用`elm-test`的`Fuzz`模块中功能的好地方:就是这里。转换函数改变数据的表示方式，而不改变数据的含义。例如:太阳系例子中的转换函数不会改变存在的行星或它们的数量。我们可以测试这些“规则”是否适用于我们生成的任何随机太阳系。

太阳系的例子允许一个更好的测试，因为我们每个方向都有一个转换函数:我们可以检查如果我们从一个方向转换一个随机的太阳系，然后返回，我们总是以我们开始的那个太阳系结束。一个这样的“往返测试”将很容易涵盖更多的失败场景，然后你自己会想出。

编写转换函数是一项可训练的技能。本文示例中的转换函数并不比它们要取代的复杂更新逻辑更简单。但是当我们写更多的转换函数时，我们会开始意识到我们在一遍又一遍地应用同样的技巧。一个 app 和下一个 app 的业务逻辑会不一样，但是转换函数的编写是一个跨领域的技能，是可以训练的。

## 这就结束了

我们已经看到了一个将代码复杂性引入转换函数的例子，我也试图向你推销这种策略。感谢您的阅读！很想听听大家自己写转换函数的经历，好的坏的。

* * *

1.  Elm 0.19 为星际 Elm 程序员引入了一些很好的支持:它支持本地 Elm 包缓存！ [↩](#fnref1)
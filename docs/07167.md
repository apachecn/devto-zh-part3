# 用 Kotlin、KVision 和 Redux 构建 Pokedex 应用程序(第 2 部分)

> 原文：<https://dev.to/rjaros/building-a-pokedex-app-with-kotlin-kvision-and-redux-part-2-59fi>

又见面了:)

在本文的第一部分中，我们已经为我们的应用程序设计了 Redux 模型。一切准备就绪，神奇宝贝正在下载，但屏幕上什么也没有。我们需要创建一个 GUI。

请记住，您可以在 [kvision-examples](https://github.com/rjaros/kvision-examples) GitHub 资源库中找到[完整的应用程序](https://rjaros.github.io/kvision-examples/pokedex/)和所有源代码。

# GUI

我们的应用程序的用户界面由四部分组成:

*   搜索输入字段。
*   信息文本(在加载过程中和出错时可见)。
*   为选定的神奇宝贝制作的反应盒网格。
*   分页按钮。

我们可以使用下面的`start`方法的框架，它是我们应用程序的入口点，负责 GUI 的创建。`stateBinding`方法是 KVision 中 Redux 架构的核心部分。它使得给定容器中的所有组件都成为状态的函数，当状态改变时自动刷新。

```
 override fun start(state: Map<String, Any>) {
        root = Root("kvapp") {
            vPanel(alignItems = FlexAlignItems.STRETCH) {
                searchField()
                vPanel(alignItems = FlexAlignItems.STRETCH) {
                    maxWidth = 1200.px
                    textAlign = TextAlign.CENTER
                    marginLeft = auto
                    marginRight = auto
                }.stateBinding(store) { state -> // !!! Redux binding is here !!!
                    informationText(state)
                    pokemonGrid(state)
                    pagination(state)
                }
            }
        }
        store.dispatch(downloadPokemons())
    } 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们可以将大部分工作委托给一些更小的方法(它们都是`Container`类的扩展方法)。

# 搜索输入字段

`searchField`方法负责实例化`Text`组件。当用户在字段中输入一些文本时，事件监听器会调度`SetSearchString` Redux 动作。

```
 private fun Container.searchField() {
        text {
            placeholder = "Enter pokemon name ..."
            width = 300.px
            marginLeft = auto
            marginRight = auto
            autofocus = true
            setEventListener<Text> {
                input = {
                    store.dispatch(PokeAction.SetSearchString(self.value))
                }
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

# 信息文本

`informationText`方法负责显示“正在加载...”标签或错误消息。

```
 private fun Container.informationText(state: Pokedex) {
        if (state.downloading) {
            div("Loading ...")
        } else if (state.errorMessage != null) {
            div(state.errorMessage)
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

# 网格

首先让我们设计一个盒子，它将显示单个神奇宝贝的名称和图片。我们将声明一个继承自一个`DockPanel`容器和一个带有一些 CSS 属性的`Style`对象的新类。`PokeBox`类相当简单——它只有两个子组件——一个图像和一个 Div。我们通过使用`addCssClass`方法来应用样式。

```
val pokeBoxStyle = Style {
    border = Border(1.px, BorderStyle.SOLID, Col.GRAY)
    width = 200.px
    height = 200.px
    margin = 10.px

    style("img") {
        marginTop = 30.px
    }

    style("div.caption") {
        textAlign = TextAlign.CENTER
        background = Background(Col.SILVER)
        width = 100.perc
    }
}

class PokeBox(pokemon: Pokemon) : DockPanel() {
    init {
        addCssClass(pokeBoxStyle)
        image(
            "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokemon.url.substring(34,pokemon.url.length - 1)}.png",
            centered = true
        )
        add(Div(pokemon.name.capitalize(), classes = setOf("caption")), Side.DOWN)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`pokemonGrid`方法负责呈现 CSS 网格，在每个单元格中有一个`PokeBox`对象。它使用带有适当的`templateColumns`属性值的`GridPanel`组件来使其具有响应性。

```
 private fun Container.pokemonGrid(state: Pokedex) {
        if (!state.downloading && state.errorMessage == null) {
            gridPanel(
                templateColumns = "repeat(auto-fill, minmax(250px, 1fr))",
                justifyItems = GridJustify.CENTER
            ) {
                state.visiblePokemons.forEach {
                    add(PokeBox(it))
                }
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

# 分页

使用`ButtonGroup`和`Button` KVision 组件，通过以下方法创建分页。“下一个”和“上一个”按钮在必要时都被禁用，并且都分派适当的 Redux 动作。

```
 private fun Container.pagination(state: Pokedex) {
        if (!state.downloading && state.errorMessage == null) {
            hPanel(justify = FlexJustify.CENTER) {
                margin = 30.px
                buttonGroup {
                    button("<<") {
                        disabled = state.pageNumber == 0
                        onClick {
                            store.dispatch(PokeAction.PrevPage)
                        }
                    }
                    button(" ${state.pageNumber + 1} / ${state.numberOfPages} ", disabled = true)
                    button(">>") {
                        disabled = state.pageNumber == (state.numberOfPages - 1)
                        onClick {
                            store.dispatch(PokeAction.NextPage)
                        }
                    }
                }
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

# 最后的话

我们的申请准备好了。我们已经用大约 100 行代码创建了整个 GUI(是最初的 JavaScript 项目的一半)。KVision 真正为您提供了“两全其美”的东西——现代的、富有表现力的语言，具有许多可重用的组件，可以与众所周知的模式和库(如 Redux)一起使用。

[GitHub 资源库](https://github.com/rjaros/kvision-examples/tree/master/pokedex)中的[完整应用](https://rjaros.github.io/kvision-examples/pokedex/)有一些本文没有提到的小补充(触摸手势，I18n)，也是完全兼容的[渐进式网络应用(PWA)](https://developers.google.com/web/progressive-web-apps/) 。

感谢你阅读这篇文章，希望我能在下一篇文章中看到你！

干杯:)
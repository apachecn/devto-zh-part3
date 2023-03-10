# 用 Kotlin、KVision 和 Redux 构建 Pokedex 应用程序(第 1 部分)

> 原文：<https://dev.to/rjaros/building-a-pokedex-app-with-kotlin-kvision-and-redux-part-1-51h5>

大家好:)

在这篇文章中，我将向你展示如何用[科特林](https://kotlinlang.org/)、[视觉](https://github.com/rjaros/kvision)和 [Redux](https://redux.js.org/) 构建一个 web 应用程序。您可能非常了解 Redux，但是您肯定不知道在用 Kotlin 创建的 web 应用程序中使用它有多容易。

KVision 是为 Kotlin/JS 创建的开源 web 框架。它允许开发人员用 Kotlin 语言构建现代 web 应用程序。你可以在本文的[中找到我对 KVision 的简短介绍。](https://dev.to/rjaros/an-introduction-to-kvision-39i1)

Redux 是一个流行的 JavaScript 应用程序的可预测状态容器。一周前，KVision 以`kvision-redux`模块的形式加入了对 Redux 的支持。

在本文的第一部分，我们将为我们的应用程序设计和实现 Redux 模型。我们将实现 state 类、actions 类和 reducer 函数。在第二部分，我们将创建用户界面。

您可以在 [kvision-examples](https://github.com/rjaros/kvision-examples) GitHub 资源库中找到[完整的应用程序](https://rjaros.github.io/kvision-examples/pokedex/)和所有源代码。

# 规范

我们希望创建一个具有以下特性的简单应用程序:

*   它显示了一个带有名称和图片的神奇宝贝列表。
*   它使用 [PokéAPI](https://pokeapi.co/) RESTful 服务。
*   它允许通过名字搜索神奇宝贝。
*   它在带有分页的响应网格中显示结果。

这个应用程序松散地基于来自官方 React 示例的这个项目。

# 模板

首先，我们将从 [kvision-examples](https://github.com/rjaros/kvision-examples) GitHub 库克隆`template`项目。要使用 Redux，我们需要添加`kvision-redux`模块。这些是我们的`build.gradle`文件中的依赖项:

```
dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib-js:${kotlinVersion}"
    compile "pl.treksoft:kvision:${kvisionVersion}"
    compile "pl.treksoft:kvision-bootstrap:${kvisionVersion}"
    compile "pl.treksoft:kvision-i18n:${kvisionVersion}"
    compile "pl.treksoft:kvision-redux:${kvisionVersion}"
} 
```

Enter fullscreen mode Exit fullscreen mode

# 状态

应用程序的状态将存储在`Pokedex`类中。它包含有关下载过程的信息(可能会以错误结束)、所有已下载神奇宝贝对象的列表、当前页面上可见的神奇宝贝列表、当前搜索字符串、当前页码和可用页数。KVision 要求状态对象可以用 [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization) 库序列化，所以我们需要在`Pokedex`和`Pokemon`类上添加一个`@Serializable`注释。

```
@Serializable
data class Pokemon(val name: String, val url: String)

@Serializable
data class Pokedex(
    val downloading: Boolean,
    val errorMessage: String?,
    val pokemons: List<Pokemon>,
    val visiblePokemons: List<Pokemon>,
    val searchString: String?,
    val pageNumber: Int,
    val numberOfPages: Int
) 
```

Enter fullscreen mode Exit fullscreen mode

# 动作

Redux 中的动作对应于可以改变应用程序状态的事件。我们将把动作实现为从`redux.RAction`类继承的密封类层次结构。

```
sealed class PokeAction : RAction {
    object StartDownload : PokeAction()
    object DownloadOk : PokeAction()
    data class DownloadError(val errorMessage: String) : PokeAction()
    data class SetPokemonList(val pokemons: List<Pokemon>) : PokeAction()
    data class SetSearchString(val searchString: String?) : PokeAction()
    object NextPage : PokeAction()
    object PrevPage : PokeAction()
} 
```

Enter fullscreen mode Exit fullscreen mode

带参数的动作被实现为数据类，其他的只是简单的对象。

# 减速器功能

reducer 函数描述了一个动作如何将当前状态转换为新状态。这是应用程序逻辑的主要位置。

为了重用一些代码，我们将创建两个助手扩展函数。第一个函数使用给定的搜索字符串过滤出`Pokemon`对象的列表，如果字符串是`null`，则返回原始列表。

```
fun List<Pokemon>.filterBySearchString(searchString: String?): List<Pokemon> {
    return searchString?.let { search ->
        this.filter {
            it.name.toLowerCase().contains(search.toLowerCase())
        }
    } ?: this
} 
```

Enter fullscreen mode Exit fullscreen mode

第二个函数根据给定的页码从原始列表中剪切出一个子列表。它使用被定义为 12 的`MAX_ON_PAGE`常量，并做一些非常简单的数学运算。

```
fun List<Pokemon>.subListByPageNumber(pageNumber: Int): List<Pokemon> {
    return this.subList((pageNumber) * MAX_ON_PAGE, (pageNumber + 1) * MAX_ON_PAGE)
} 
```

Enter fullscreen mode Exit fullscreen mode

编写 reducer 函数现在非常容易。前四个动作只是改变状态属性。其他动作稍微复杂一些——它们计算新的可见魔法列表，我们使用上面定义的辅助函数来实现这个目的。我们可以如下实现整个`pokedexReducer`功能:

```
fun pokedexReducer(state: Pokedex, action: PokeAction): Pokedex = when (action) {
    is PokeAction.StartDownload -> state.copy(downloading = true)
    is PokeAction.DownloadOk -> state.copy(downloading = false)
    is PokeAction.DownloadError -> state.copy(downloading = false, errorMessage = action.errorMessage)
    is PokeAction.SetPokemonList -> state.copy(pokemons = action.pokemons)
    is PokeAction.SetSearchString -> {
        val filteredPokemon = state.pokemons.filterBySearchString(action.searchString)
        val visiblePokemons = filteredPokemon.take(MAX_ON_PAGE)
        state.copy(
            visiblePokemons = visiblePokemons,
            searchString = action.searchString,
            pageNumber = 0,
            numberOfPages = ((filteredPokemon.size - 1) / MAX_ON_PAGE) + 1
        )
    }
    is PokeAction.NextPage -> if (state.pageNumber < state.numberOfPages - 1) {
        val newPageNumber = state.pageNumber + 1
        val visiblePokemons = state.pokemons.filterBySearchString(state.searchString).subListByPageNumber(newPageNumber)
        state.copy(visiblePokemons = visiblePokemons, pageNumber = newPageNumber)
    } else {
        state
    }
    is PokeAction.PrevPage -> if (state.pageNumber > 0) {
        val newPageNumber = state.pageNumber - 1
        val visiblePokemons = state.pokemons.filterBySearchString(state.searchString).subListByPageNumber(newPageNumber)
        state.copy(visiblePokemons = visiblePokemons, pageNumber = newPageNumber)
    } else {
        state
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 商店

我们的数据模型现在几乎完成了。剩下的工作就是用一些初始状态创建 Redux 存储。为此我们使用了`createReduxStore`函数。

```
val store = createReduxStore(::pokedexReducer, Pokedex(false, null, listOf(), listOf(), null, 0, 1)) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以向商店分派操作(或操作创建者功能)。当应用程序启动时，我们必须从 PokéAPI 服务器下载所有 Pokémons 数据。我们用下面的方式调用`dispatch`。

```
 store.dispatch(downloadPokemons())

    // ...

    private fun downloadPokemons(): ActionCreator<dynamic, Pokedex> {
        return { dispatch, _ ->
            val restClient = RestClient()
            dispatch(PokeAction.StartDownload)
            restClient.remoteCall(
                "https://pokeapi.co/api/v2/pokemon/", obj { limit = 800 },
                deserializer = Pokemon.serializer().list
            ) {
                it.results
            }.then { list ->
                dispatch(PokeAction.DownloadOk)
                dispatch(PokeAction.SetPokemonList(list))
                dispatch(PokeAction.SetSearchString(null))
            }.catch { e ->
                val info = if (!e.message.isNullOrBlank()) { " (${e.message})" } else { "" }
                dispatch(PokeAction.DownloadError("Service error!$info"))
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

`downloadPokemons`方法返回 ActionCreator 函数，该函数使用`RestClient`实例调用 PokéAPI 端点，接收数据并最终调度`SetPokemonList`和`SetSearchString`动作。它还处理下载过程中可能出现的错误。

其他操作从用户界面元素中分派，但是我们将在本文的下一部分关注 GUI。一旦它准备好了，我将邮寄它。

现在，任何反馈都是高度赞赏的。

干杯:)

[第二部分](https://dev.to/rjaros/building-a-pokedex-app-with-kotlin-kvision-and-redux-part-2-59fi)
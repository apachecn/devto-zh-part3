# 存储库模式:正确组织数据层

> 原文：<https://dev.to/adammc331/the-repository-pattern-properly-organizing-your-data-layer-10bg>

原载于[安卓精华](https://androidessence.com/repository-pattern)。

如何正确地设计您的应用程序是我们作为开发人员经常面临的问题。不幸的是，没有一个放之四海而皆准的答案，有时我们甚至不知道从哪里开始。在我的 Android 之旅中，我了解到，答案也可能因你试图组织的应用程序部分而异。当然，你可能会说，[视情况而定](https://handstandsam.com/2019/03/10/it-depends-is-the-answer-to-your-android-question/)。

但是，当涉及到数据层时，有一些关于如何编写干净、可维护的代码的很好的技巧。其中之一是存储库模式，我想快速介绍一下它是什么以及它为什么重要。

# TL；速度三角形定位法(dead reckoning)

存储库模式是一种组织代码的方式，这样您的 ViewModel 或 Presenter 类就不需要关心您的数据来自哪里。它只关心如何请求数据和得到什么。

# 坏榜样

让我们首先看看当我们*没有*实现存储库模式时，我们的代码会发生什么。假设我有一个口袋妖怪的详细页面(是的，我喜欢使用口袋妖怪的例子)，我想从翻新服务中获取数据。我可能会得到一个类似这样的视图模型:

```
class DetailActivityViewModel(
    private val pokemonAPI: PokemonAPI
) : BaseObservableViewModel() {
    ...

    init {
        job = CoroutineScope(dispatcherProvider.IO).launch {
            ...

            val pokemon = pokemonAPI.getPokemonDetailAsync(pokemonName).await()

            ...
        }
    }

    ...
} 
```

虽然这看起来并不可怕，但它实际上提出了一个有趣的限制。如果要求您从 GraphQL API 中取数据会怎么样？或者甚至是本地数据库？如果你想要一个组合，或者 A/B 测试多种方法呢？

取决于你选择了哪一个，这变得很难看。首先，您必须将相关的属性添加到您的 ViewModel 中，然后您的 ViewModel 类最终会因数据层工作而变得臃肿，而这些工作实际上已经不属于那里了。

如果您曾经发现自己处于这种情况，即使您还没有遇到这种限制(有些人只使用改进的 API，这很好)，您可能想要考虑用存储库模式来帮助未来的自己。

# 知识库界面

回到 TL；博士，你的视图模型不应该关心信息来自哪里。在许多编程问题中，我们不关心某些东西的实现，我们可以将我们关心的东西放在一个接口中。

我们可以从定义数据获取行为的接口开始:

```
interface PokemonRepository {
    suspend fun getPokemon(): PokemonResponse
    suspend fun getPokemonDetail(pokemonName: String): Pokemon
} 
```

一旦我们定义了它，我们应该更新我们的视图模型来使用这个接口:

```
class DetailActivityViewModel(
    private val repository: PokemonRepository
) : BaseObservableViewModel() {
    ...

    init {
        job = CoroutineScope(dispatcherProvider.IO).launch {
            ...

            val pokemon = repository.getPokemonDetail(pokemonName)

            ...
        }
    }

    ...
} 
```

现在，我们可以很好地控制数据的来源，并根据需要更新数据，而不必担心视图模型的更新。

# 实现

如果你只使用一个数据源，比如一个改进的 API，你只需要担心创建一个实现，这可以通过大量的复制和粘贴来完成。在 Pokedex 示例中，我们将实现转换为:

```
open class PokemonRetrofitService(
    private val api: PokemonAPI
): PokemonRepository {
    override suspend fun getPokemon(): PokemonResponse {
        return api.getPokemonAsync().await()
    }

    override suspend fun getPokemonDetail(pokemonName: String): Pokemon {
        return api.getPokemonDetailAsync(pokemonName).await()
    }
} 
```

现在，我们可以更新创建视图模型的调用站点，以使用这个实现:

```
private val viewModelFactory = object : ViewModelProvider.Factory {
    override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        val pokemonAPI = ...
        val repository = PokemonRetrofitService(pokemonAPI)

        return DetailActivityViewModel(repository) as T
    }
} 
```

# 多个实现示例

如果您想考虑获取数据的多种实现，这种模式尤其重要。一个常见的例子是，除了网络服务之外，您可能还想从本地数据库获取数据。

让我们考虑一个例子，我们有一个显式的离线模式，从数据库中获取数据。如果您的代码已经在使用 repository 模式，我们就不需要担心 ViewModel 的更新。

我们需要做的就是创建我们的`DatabasePokemonService`，然后我们可以有条件地将它传递给视图模型:

```
private val viewModelFactory = object : ViewModelProvider.Factory {
    override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        val repository = getPokemonRepository()

        return DetailActivityViewModel(repository) as T
    }
}

private fun getPokemonRepository(): PokemonRepository {
    if (offlineMode()) {
        return DatabasePokemonService()
    } else {
        return RetrofitPokemonService()
    }
} 
```

# 测试好处

当您将数据获取代码移动到接口中时，您也使单元测试变得更加容易！我们对 ViewModel 的单元测试也不必担心数据实现，我们可以使用 Mockito 来模拟接口，并以这种方式存根测试数据:

```
class DetailActivityViewModelTest {
    ...

    private val mockRepository = mock(PokemonRepository::class.java)

    @Test
    fun loadData() {
        val testPokemon = Pokemon(name = "Adam", types = listOf(TypeSlot(type = Type("grass"))))

        whenever(mockRepository.getPokemonDetail(anyString())).thenReturn(testPokemon)

        ...
    }

    ...
} 
```

现在，我们不必担心在单元测试中创建房间数据库，或者更新服务。处理一个界面可以帮助避免所有这些问题。

# 资源

我希望这有助于理解如何正确组织数据获取代码。如果你想看看存储库模式的运行情况(虽然我不做这个 A/B 测试场景)，你应该在 GitHub 上查看这个 [Pokedex 项目](https://github.com/AdamMc331/PokeDex)。

如果你喜欢直接分析代码，你可以看到在[这个拉请求](https://github.com/AdamMc331/PokeDex/pull/20)中实现的存储库模式。
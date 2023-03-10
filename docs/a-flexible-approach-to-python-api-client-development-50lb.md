# Python API 客户端开发的灵活方法

> 原文：<https://dev.to/easyaspython/a-flexible-approach-to-python-api-client-development-50lb>

[![Ditto Pokémon](img/21042b5f8b855f608b2875e57778d010.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1H6Ga8EM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0mlhix21sasx9lzqlfdf.png)

你从事的是面向微服务的架构吗？有没有你想用的没有 Python SDK 的 API？虽然像`requests`这样的高级 HTTP 库通过减少启动和运行所需的工作来帮助开发，但是它们并没有提供太多专门围绕 API 的结构。这可能导致几段相似的调用代码散布在整个系统中，在最糟糕的情况下会导致不同的方法。

让我们看看使用`requests`时一组 API 调用会是什么样子。我将使用 PokéAPI，一个神奇的(而且免费的！)用于查询神奇宝贝元数据的 API。最有用的端点是`/pokemon/:pokemon`，它是获取特定神奇宝贝信息的主要入口点。您可以使用`requests` :
调用这个端点

```
>>> import requests
>>> ditto = requests.get('https://pokeapi.co/api/v2/pokemon/ditto') 
```

Enter fullscreen mode Exit fullscreen mode

这将返回大量信息，其中大部分链接到其他端点。为了更容易地检查数据，您将希望以 Python 字典的形式获得返回的 JSON:

```
>>> ditto = requests.get('https://pokeapi.co/api/v2/pokemon/ditto').json() 
```

Enter fullscreen mode Exit fullscreen mode

你可以通过检查它的名字来确认你已经收到了关于 Ditto 的回复:

```
>>> ditto['name']
'ditto' 
```

Enter fullscreen mode Exit fullscreen mode

你还可以看到，Ditto 有一步棋，名为“变换”:

```
>>> ditto['moves'][0]['move']['name']
'transform' 
```

Enter fullscreen mode Exit fullscreen mode

您还可以获得了解更多关于转换移动的端点:

```
>>> ditto['moves'][0]['move']['url']
'https://pokeapi.co/api/v2/move/144/' 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以使用`requests`调用`/move/:move_id`端点来获取它的信息。这将导致链接到更多端点的另一个响应。**有这么多的端点！**它们都在同一个 PokéAPI 保护伞下，但代码中没有多少结构反映这一事实。如果其中一些端点返回单个字符串值而不是 JSON，那么与数据交互的代码也需要更改。有没有更好的方法来保持这一切都是直的？

在大范围内，理解您的代码调用的所有 API 和端点是很重要的，部分原因是为了避免您花费精力创建重复现有功能的代码。这也使得调查使用情况以了解您是否已经完全从废弃的 API 或端点中迁移出来变得更加容易。要做到这一点，你需要一个 API 及其端点的单一真实来源，并且理想情况下需要一种与它们交互的同质方式。

[`apiron`](https://github.com/ithaka/apiron) 是一个 Python 包，它通过提供一种声明性的方法来产生类似 SDK 的交互供您使用，从而满足了这些需求。通过对您声明的配置进行自省，`apiron`允许您快速开始与 API 对话，并引导您对所有 API 依赖项进行集中配置。让我们看看如何使用`apiron`来设置 PokéAPI。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [伊萨卡](https://github.com/ithaka) / [阿皮龙](https://github.com/ithaka/apiron)

### 🍳apiron 是一个 Python 包，它帮助您为 RESTful APIs 编写一个美味的客户机。只是不要用肥皂洗。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 停机坪

[![PyPI version](img/6d46e53a9219901acb7370a43a791f4d.png)](https://pypi.org/project/apiron/#history)[![Supported Python versions](img/29c035781e9d420b7b0a86b4eeba5589.png)](https://pypi.org/project/apiron/)[![Build status](img/06101c0bccdeccb7e6743b7ee2f8462a.png)](https://github.com/ithaka/apiron/actions)[![Documentation Status](img/bdfe3a942d9c4ff0fcb902401d03307f.png)](https://apiron.readthedocs.io)[![Contributor Covenant](img/a5da9251464ed9dc3aa9eb14ee1afa9e.png)](https://github.com/ithaka/apironcode-of-conduct.md)

`apiron`帮助您为 RESTful APIs 设计一个美味的客户端。只是不要用肥皂洗。

[![Pie in a cast iron skillet](img/f3938924303c2a305297b4b143937342.png)](https://github.com/ithaka/apiron/raw/dev/docs/_static/cast-iron-skillet.png)

对于 web 应用程序开发人员来说，从多个服务收集数据已经成为一项无处不在的任务。调用具有多个参数集的 API 端点，调用多个 API 端点，调用多个 API 中的多个端点，这种复杂性会迅速增加。虽然业务逻辑可能会变得复杂，但是与这些 API 交互的代码却不会。

`apiron`为服务和端点提供声明性的结构化配置，并提供统一的接口与它们进行交互。

## 定义服务

服务定义需要一个域和一个或多个与之交互的端点:

```
from apiron import JsonEndpoint, Service

class GitHub(Service):
    domain = 'https://api.github.com'
    user = JsonEndpoint(path='/users/{username}')
    repo = JsonEndpoint(path='/repos/{org}/{repo}')
```

Enter fullscreen mode Exit fullscreen mode

## 与服务交互

一旦您的服务定义就绪，您就可以进行交互…

</article>

[View on GitHub](https://github.com/ithaka/apiron)

使用`apiron`你可以定义一个`Service`，它有一个`domain`和一个`Endpoint`的集合。神奇宝贝和移动端点恰好返回 JSON，所以使用一个`JsonEndpoint`将默认返回作为字典的响应。每个端点都有占位符，可以动态填充:

```
from apiron import Service, JsonEndpoint

class PokeAPI(Service):
    domain = 'https://pokeapi.co'

    pokemon = JsonEndpoint(path='/api/v2/pokemon/{pokemon}')
    move = JsonEndpoint(path='/api/v2/move/{move_id}') 
```

Enter fullscreen mode Exit fullscreen mode

这一切都很好，但你如何与之互动？`apiron`为调用您配置的服务提供类似 SDK 的交互。也就是说，`apiron`试图感觉像是为您需要使用的 API 专门构建的东西，同时又足够灵活，可以为任意数量的 API 这样做。下面是如何进行调用以获得 Ditto 的信息和关于转换移动的信息:

```
ditto = PokeAPI.pokemon(path_kwargs={'pokemon': 'ditto'})
transform = PokeAPI.move(path_kwargs={'move_id': 144}) 
```

Enter fullscreen mode Exit fullscreen mode

从这段代码中，可以更容易地看出您正在调用 PokéAPI 并使用 Pokémon 和 move 端点。也更容易看到哪些数据被插入到呼叫中。如果您使用的是 IDE，您还可以跳转到这些端点定义，查看它们是否返回 JSON，这样您就知道在使用响应时会发生什么。当您浏览`PokeAPI`类时，您还可以一目了然地看到哪些端点已经实现，如果您知道正确的路径和返回的数据类型，还可以快速添加任何不可用的端点。

如果 PokéAPI 移动到一个新的域，或者如果一个端点的路径发生变化，那么有一个明确的地方可以对这种变化做出反应。您还可以使用不同的头、cookies 等来调用端点，而不必复制太多的样板文件。这种方法的可读性和可检查性改善了我处理具有多个端点的多个服务的体验。除了帮助用户开发他们想要使用的 API 的客户端，这个包还可以减少 API 提供者为他们的消费者提供 Python SDK 的工作量。我真的希望你能尝试一下，并告诉我你能想到的所有打破它的方法！
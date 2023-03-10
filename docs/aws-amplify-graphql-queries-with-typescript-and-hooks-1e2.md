# AWS 用 TypeScript 和钩子扩展了 GraphQL 操作——第 1 部分[查询]

> 原文：<https://dev.to/mwarger/aws-amplify-graphql-queries-with-typescript-and-hooks-1e2>

我是 Amplify 的超级粉丝。我也是 TypeScript 的忠实粉丝。Amplify 不是用 TypeScript 构建的，要有效地使用它，有时候你需要给它一点帮助。对于 GraphQL 来说尤其如此。随着钩子的出现，我们可以为自己创建一些很好的实用程序，让我们在 GraphQL 查询中利用 TypeScript 的强大功能。让我们看看那是什么样子。

##### >我假设熟悉 React 和 TypeScript，包括这篇文章中 Amplify 和 GraphQL 的用法。网上有大量的资源。这里有一套[设置类似项目的伟大步骤](https://read.acloud.guru/8-steps-to-building-your-own-serverless-graphql-api-using-aws-amplify-42c21770424d)。或者，您可以克隆[示例应用程序](https://github.com/mwarger/gamer-fi-typescript)并在项目的根目录下运行`amplify init`来查看最终结果。

## 简单查询

从我们的示例应用程序中，我们有一个想要检索的流行游戏列表。查询如下:

```
popularGames  {  id  name  popularity  url  summary  # ommitted for brevity  } 
```

开始，这是我们追求的一个很好的开始。我们想使用`useEffect`来处理获取数据。我们使用 Amplify 的`graphql`方法，并将上面的 GraphQL 查询传递给`graphqlOperation`方法。

```
React.useEffect(() => {
  const fetchGames = async () => {
    try {
      const response = await API.graphql(graphqlOperation(popularGames));
    } catch (error) {
      console.log(error);
    }
  };

  fetchGames();
}, []); 
```

响应对象有一个数据属性，包含我们的游戏列表。这是列表中的几个。

```
{  "data":  {  "popularGames":  [  {  "id":  "76882",  "name":  "Sekiro: Shadows Die Twice",  "popularity":  3954.25  },  {  "id":  "114455",  "name":  "Pacify",  "popularity":  1472.0  }  ]  }  } 
```

我们希望在页面上显示这些内容，因此需要将它们加载到 state 中。使用钩子，您可以通过创建一个`useState`声明，然后使用为您创建的方法将它们加载到 state 中来完成这个任务。

```
const [gameData, setGameData] = React.useState(undefined); 

React.useEffect(() => {
  const fetchGames = async () => {
    try {
      const response: any = await API.graphql(graphqlOperation(popularGames));
      setGameData(response.data);
    } catch (error) {
      console.log(error);
    }
  };

  fetchGames();
}, []); 
```

此时，您可以使用`gameData`对象在页面上显示您的游戏。

但是在打字稿领域，我们实际上有更多的问题。通过将 state 对象初始化为 undefined，TypeScript 只能推断出允许用于`gameData`对象的值是未定义的，如果我们试图使用`setGameData`方法设置查询响应，将会给出一个错误。此外，我们使用了一个关键字来解决这个问题，这个问题让许多第一次使用 TypeScript 的开发人员头疼不已。

`any`

这个词会让所有见证它的人感到恐惧。至少，如果你想让未来的自己在某个时候感谢过去的自己，这是应该的(感谢 [Paul](https://twitter.com/paulweveritt?lang=en) ，这个伟大的洞察力)。

我们不希望在任何地方出现。

> 我们真的想尽我们最大的努力给自己和我们的工具尽可能多的力量。

我们现在无法预测我们的数据会是什么，这是一个问题...但是`Amplify`可以帮助我们。

## 救援类型

在我们的项目中，如果还没有配置，我们需要运行`amplify configure codegen`。这将设置代码生成，并引导我们完成基于查询生成类型的过程。这非常有帮助(就我而言，如果项目的根目录下有一个`.tsconfig`文件，这应该是默认设置...).

这为我们的数据提供了一种类型。通常，我们可以在变量之后抛出这个，然后就完成了。

```
const response: { data: PopularGamesQuery } = await API.graphql(
  graphqlOperation(popularGames)
); 
```

我们知道，如果操作不失败，响应将会是这种类型。然而，`graphql`函数返回的类型是`GraphQLResult`和`Observable`的混搭，因为这两者使用的是同一个函数。在这种情况改变之前，我们要让它知道我们期望得到什么。我们可以通过使用`as`关键字告诉 TypeScript 我们知道我们的类型是什么来做到这一点。

```
const response = (await API.graphql(graphqlOperation(popularGames))) as {
  data: PopularGamesQuery;
}; 
```

现在我们得到了我们想要的类型帮助。将鼠标悬停在`response`对象上可以确认 TypeScript 将响应识别为具有类型`PopularGamesQuery`的数据属性的对象。

## 游戏，设置状态

我们将使用相同的类型来告诉我们的`useState`函数我们想要如何利用我们的`gameData`对象。

`useState`接受一个[泛型类型参数](https://www.typescriptlang.org/docs/handbook/generics.html)，它允许我们通知函数我们想要使用的类型。

```
const [gameData, setGameData] = React.useState<PopularGamesQuery | undefined>(
  undefined
); 
```

这里，我们传递了相同的`PopularGamesQuery`类型(以及我们用来初始化它的可能未定义的值)。这控制了 TypeScript 编译器，我们可以继续显示我们的游戏了。

尽管这很简单，但有一些样板文件摆在我们面前。我们可以将这些东西提取出来，并创建一些可重用的代码，这将使其他组件中的未来查询更容易设置。定制钩子来拯救！

请继续关注下一篇文章，在那里我们会这样做！

> 这个帖子最初发布在[我的网站](https://mw.codes/posts/amplify-ts-query-hook)上。你可以看到这篇文章和其他类似的文章，以及我的一切。请在 [@twitter](https://twitter.com/mwarger) 上关注我，如果您有任何问题，请联系我！
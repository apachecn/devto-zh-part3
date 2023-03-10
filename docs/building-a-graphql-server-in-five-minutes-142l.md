# 在五分钟内构建一个 GraphQL 服务器

> 原文：<https://dev.to/eveporcello/building-a-graphql-server-in-five-minutes-142l>

*本帖最初发表于[月球公路](https://moonhighway.com/building-a-graphql-server-in-five-minutes)。*

现在是周五晚上 5 点 55 分。今晚该你做饭了，而你却没有食谱。如果你没有吹嘘你周五晚上要做的新玉米卷食谱，这不会是什么大不了的事。但是你没有食谱。你撒谎了。

你真的不想被视为吹牛大王和骗子。一个或者另一个，很好，但是两个都要？这座桥太远了。你需要找到一个配方，你现在就要找到。

你打开电脑，去谷歌上找一份美味的新玉米卷食谱。但是谷歌倒闭了。WTF？你伸手拿起手机，看到一条通知:*“突发事件:全球所有搜索引擎关闭”*。没有谷歌。没有冰。没有 AskJeeves。那和这个无关，但是 AskJeeves 还是不可用。

您能够处理这种情况的唯一方法是向您已经记住的[Taco fancy Taco Randomizer API route](https://github.com/evz/tacofancy-api)发送一个查询，并且您能够做到这一点的唯一方法是构建一个 GraphQL API。你必须在下午 6 点前完成。您有五分钟的时间来构建一个 GraphQL 服务器。时间现在开始。

首先初始化一个节点项目并安装 npm 的依赖项:

```
mkdir taco-emergency
cd taco-emergency
npm init -y
npm install graphql apollo-server 
```

然后创建一个名为`index.js`的文件，并添加以下内容:

```
const { ApolloServer } = require("apollo-server");

const server = new ApolloServer({});

server.listen().then(({ url }) => 
    console.log(`Server running on port ${url}`)); 
```

您将需要从`apollo-server`包中选择的 GraphQL 服务器库`ApolloServer`。然后创建一个`ApolloServer`的新实例。然后您将运行它，它将在端口 4000 上侦听。

Apollo Server 的任何新实例都必须接受`typeDefs`(您的 GraphQL 模式)和`resolvers`(用于获取数据以满足模式要求的函数)。

```
const server = new ApolloServer({
  typeDefs,
  resolvers
}); 
```

您将从模式开始。你需要获取一个玉米卷食谱的直接网址。GraphQL API 上所有可用的查询都可以在`Query`类型中找到:

```
const typeDefs = `
  type Query {
    recipeURL: String
  }
`; 
```

当您发送对`recipeURL`的查询时，它应该返回一个类型为`String`的值。

接下来，为这个查询创建一个解析器。解析器将通过使用一个简单的`fetch`调用从 API 获取您需要的数据。从安装带有 npm 的`node-fetch`开始:

```
npm install node-fetch 
```

将此添加到文件顶部的 require 语句列表中:

```
const fetch = require("node-fetch"); 
```

然后创建解析器对象。这些函数将返回`recipeURL` :
所需的数据

```
const resolvers = {
  Query: {
    recipeURL: () => {}
    }
  }
}; 
```

想想你需要`recipeURL`解析器做什么。**想！**它需要对 Taco API 进行一个`fetch`调用来获得一个随机的食谱。然后，它需要转换数据响应，只从响应中获取 URL，而不是所有额外的字段。

解析器可以是异步的，所以对`recipeURL`使用异步函数。在这个函数中，您从 API 获取数据并将结果记录到控制台:

```
const resolvers = {
  Query: {
    recipeURL: async () => {
      let taco = await fetch(
        "http://taco-randomizer.herokuapp.com/random/"
      ).then(res => res.json());
      console.log(taco);
    }
  }
}; 
```

现在您在终端中运行`node index`来运行这个文件。当你打开`localhost:4000`时，你会看到 GraphQL Playground，它允许你直接在浏览器中发送 GraphQL 查询。在左侧，您将编写查询:

```
query  {  recipeURL  } 
```

然后我们就点`Play`。右侧返回的数据如下:

```
{  "data":  {  "recipeURL":  null  }  } 
```

这是可以的，因为您实际上是在尝试查看记录到控制台或终端的完整对象。如果你打开终端，你会看到一团数据。请记住，这是一个随机的玉米卷，所以您的结果会略有不同:

```
{  base_layer:  {  url:  "https://raw.github.com/sinker/tacofancy/master/base_layers/spaghetti_squash.md",  ...  }  } 
```

现在您已经知道了响应对象的样子，您可以深入了解它，只获取 URL。

**滴答滴答**

您更改解析器，只返回 URL 字符串:

```
const resolvers = {
  Query: {
    recipeURL: async () => {
      let taco = await fetch(
        "http://taco-randomizer.herokuapp.com/random/"
      ).then(res => res.json());
      return taco.base_layer.url;
    }
  }
}; 
```

并且您停止并重新启动索引文件:`node index`。您单击 Play 再次运行查询:

```
query  {  recipeURL  } 
```

**5...四...**

你得到的回应:

```
{  "data":  {  "recipeURL":  "https://raw.github.com/sinker/tacofancy/master/base_layers/bellpepper.md"  }  } 
```

**3...2...**

将网址复制并粘贴到浏览器中，你就有了你的食谱。你做到了。晚饭省了。你的正直得救了。这一切都归功于一个简单的 GraphQL 服务器。

*要更详细地了解如何设置 GraphQL 服务器，请查看 egghead.io 上的播放列表:[使用 GraphQL 和 Apollo](https://egghead.io/playlists/create-fullstack-applications-with-graphql-and-apollo-794dc9c7) 创建 Fullstack 应用程序 T3。*
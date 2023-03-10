# 保持冷静，继续用 Cosmos 和 Node.js 编码

> 原文：<https://dev.to/john_papa/keep-calm-and-keep-coding-with-cosmos-and-nodejs-34ao>

用 Cosmos DB SDK 编码感觉很好。是的，我说感觉很好。是啊，是啊…安装、学习和执行都很快。但最重要的是，感觉很好。

想想当你编码的时候，所有的东西都在为你流动。处于一种节奏中感觉很棒，把所有事情都从你的头脑中拿出来，进入代码，流畅地工作！这其中的一个关键因素是当我们正在编码的 API 是直观的和易于遵循的。这是 Cosmos DB SDK 最闪亮的地方。我们来看看为什么。

## 我现在想玩玩代码

Cosmos DB SDK 棒极了，感觉棒极了，如果你想在不阅读这篇文章的情况下也感觉棒极了，并亲自尝试一下——下面是让你开始的链接。如果你跳过这篇文章，我可能会哭，但我会克服的。

*   [我在 GitHub 上的样本库](https://github.com/johnpapa/heroes-node-api?wt.mc_id=devto-blog-jopapa)
*   [Node 和 Cosmos DB 快速入门文档](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-nodejs-get-started?wt.mc_id=devto-blog-jopapa)
*   VS Cosmos DB 的代码扩展
*   免费试用 Azure Cosmos DB

## 沉着、冷静、收集编码

当编码世界一切正常时，编码对我们许多人来说是一种治疗。其他时候，就像在生日聚会上，十几个尖叫的孩子拿着彩枪在我们的房子里跑来跑去！当我们写代码时，我们知道我们想要它做什么。例如，最近我想从我的数据库中得到一个英雄的名单——只要把他们给我就行了，不要让我这么辛苦！用 Cosmos DB SDK 把它们交给我的代码行很简单:

```
container.items.readAll().toArray() 
```

我的英雄有一个容器，里面有物品(我的英雄)。所以请读取它们并以数组的形式返回。这并不难！

## 带快递路线的宇宙 DB

好的，那么这在 HTTP 请求的节点和快速路由的上下文中是如何工作的呢？让我们好好想想。

我们首先需要建立数据库。我们将在另一个模块中这样做，这样我们就可以快速地将其剔除并导入。

然后我们得到了英雄。好了，我们已经有了上面那行代码。这段代码是异步的，所以我们将考虑使用 promises 或 async/await。这是你的电话，所以明智地选择。使用 async/await 给我带来了快乐，所以这就是我们在这里要做的。这是我将结果取出并重命名为 heroes 时的样子。

```
const { result: heroes } = await  container
      .items
      .readAll()
      .toArray(); 
```

最后，如果成功的话，我们需要在响应中给英雄填充一个状态代码。如果失败，我们返回一个失败状态代码和消息。

这是我为此写的代码。你可以随意借(用完之后请还回来，这样别人也可以借)。

```
//hero.service.js
const { heroes: container } = require('./config').containers;
async function getHeroes(req, res) {
  try {
    const { result: heroes } = await  container
      .items
      .readAll()
      .toArray();
    res.status(200).json(heroes);
  } catch (error) {
    res.status(500).send(error);
  }
} 
```

那很容易。像疯了一样简单！我们只是编写了数据库访问代码，并用几行代码将它连接到一个快捷路线。

好的，这是我们为配置插入的一行。我们需要这样写，但是好消息是配置只需要做一次。固定成本是一次性的，您需要多少功能就可以重用多少功能。换句话说，我们可以编写函数来获取、上传、发布、删除英雄、恶棍以及任何我们想要的东西，并且只需要在上面的一行代码就可以配置它！

## 隔离配置

这里有一个模块可以帮助你开始。我喜欢将设置我的编码环境以连接到数据库的功能隔离在一个节点模块中。这里我们有一个可以称为 config.js 或 giraffe.js 的文件，我认为前者更有意义，但是你做你自己。

评论解释了这里正在发生的事情，但是让我们回顾一下。首先，我们引用@azure/cosmos npm 包中的 Cosmos 模块。然后我们设置密钥、URL 和数据库名称。这将我们所有的配置整合在一个地方。最后，我们实例化 Cosmos 客户机对象并公开容器对象。这些容器对象是我们将在其他模块中导入的对象，所以我们只需请求容器，它就能工作。是的，就这么简单。

```
// config.js
// Get the npm module for Azure's Cosmos
const cosmos = require('@azure/cosmos');
// Identify your database keys and name
const endpoint = process.env.CORE_API_URL;
const masterKey = process.env.CORE_API_KEY;
const dbDefName = 'vikings-db';
// Instantiate the Cosmos client object,
// which is the focal point of interacting with Cosmos
const { CosmosClient } = cosmos;
const client = new CosmosClient({ endpoint, auth: { masterKey } });
// Make it easy to acces the containers
const containers = {
  heroes: client.database(dbDefName).container('heroes'),
  villains: client.database(ddDefName).container('villains')
};
module.exports = { containers }; 
```

## 试试看

您可以在自己的应用程序中亲自尝试，或者在这里克隆我的存储库，或者在文档中快速浏览一遍。

以下是一些帮助您入门的链接

*   [我在 GitHub 上的样本库](https://github.com/johnpapa/heroes-node-api?wt.mc_id=devto-blog-jopapa)
*   [Node 和 Cosmos DB 快速入门文档](https://docs.microsoft.com/en-us/azure/cosmos-db/sql-api-nodejs-get-started?wt.mc_id=devto-blog-jopapa)
*   VS Cosmos DB 的代码扩展
*   免费试用 Azure Cosmos DB

*[本文交叉发布到我的博客](https://johnpapa.net/keep-calm-and-keep-coding-with-cosmos/)*
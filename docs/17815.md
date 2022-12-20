# 玩 GraphQL 瑜伽和猫鼬

> 原文：<https://dev.to/aurelkurtula/playing-with-graphql-yoga-and-mongoose-f4f>

这将是一个使用 GraphQL yoga 的介绍，这是一个基于 Express 的 GraphQL 服务器。

如果你不知道 GraphQL 是什么，你可能想看看我的[之前的教程](https://dev.to/aurelkurtula/exploring-graphql-api-serve-3abf)，在那里我使用了 Express 和 GraphQL。这应该能让你跟上进度。然后你可以学习如何[用 GraphQL 创建一个电影网站，并作出反应](https://dev.to/aurelkurtula/creating-a-movie-website-with-graphql-and-react-25d4)，但这并不是你真正需要理解的。我们只是用不同的工具重新创建了第一个教程。

## 开始使用

让我们设置基本项目。在空目录中运行以下命令行

```
npm init -y
npm i -S graphql-yoga mongoose
touch index.js 
```

第一行设置了 npm 项目，第二行安装了我们需要的两个包，为了简单起见，整个代码都放在`index.js`中。让我们创建一个非常基本的结构:

```
const { GraphQLServer } = require('graphql-yoga');

const typeDefs = `
type Query {
  Greeting: String
}
`
const resolvers = {
  Query: {
    Greeting: () => `Hello World`
  }
}
const server = new GraphQLServer({
  typeDefs,
  resolvers
})

server.start({port: 7777}, () => console.log(`The server is running on port 7777`)) 
```

我们要求`graphql-yoga`在顶部，服务器在底部运行。GraphQL 服务器需要我们定义它将运行的查询类型，以及每个查询将响应的数据类型。

在我们的简单版本中，我们有一个返回字符串的查询`Greeting`。我们指定当调用`Greeting`时，将返回`Hello World`。

现在，我们剩下要做的就是在终端`node index.js`中运行它。我强烈建议你安装`nodemon`，但这已经离题了。

现在你需要在 [graphql 操场](https://github.com/prisma/graphql-playground)或者使用[在线 graphql 操场](https://www.graphqlbin.com/)跑`http://localhost:7777`。然后只需运行以下查询:

```
{
  Greeting
} 
```

你会得到回应`Hello World`。

## 稍深一点

让我们添加一个对象数组

```
const typeDefs = `
type Query {
    People: [PeopleObject]!
    Greeting: String
}
type PeopleObject {
    id: ID
    first: String!
    last: String!
}
`
const resolvers = {
  Query: {
    Greeting: () => `Hello World`,
    People: () => [{first: 'Aurel', last: 'Kurtula'}]
  }
} 
```

我们已经添加了`People`作为可能的查询。它必须返回一个对象数组，对象的属性在`PeopleObject`中定义。注意,`!`的使用使得那些属性成为必需的——意味着我们必须返回那些内容。

然后我们硬编码了一个将被返回的数组。

在操场上，我们可以跑这个

```
{
  Greeting
  People{
    first
    last
  }
} 
```

## 连接到 mongo 数据库

在之前的教程中，我从 mlab 创建了一个 mongo 数据库。因此，如果您还没有在本地安装 mongodb，或者如果您不知道如何设置 mongo 数据库，请遵循下面的步骤。然后回来，仍然在`index.js`顶部添加下面的代码。

```
const mongoose = require('mongoose');
const db = mongoose.connect('mongodb://localhost:27017/population');
const Schema = mongoose.Schema;
const peopleSchema = new Schema({
    first: { type: String  },
    last: { type: String}
})
const People = mongoose.model('people', peopleSchema) 
```

如果您已经安装了 mongodb 并在本地运行，上面的内容将会正常工作，如果没有，您需要更改 mongoose 应该连接到的 url。`population`将被自动创建。然后我们创建一个模式。类似于我们对 Graphql 所做的，我们需要告诉 mongoose 什么类型的内容将被添加到模型中，然后我们指定模型。

从这一点开始，正如您将看到的，我们只与`People`模型交互，而`population`数据库中的`people`模型将受到影响。

### Mongo 和 GraphQL

当我们在 GraphQL playground 中查询`People`时，我们希望数据来自 mongo 数据库，而不是来自我们硬编码的数组。所以让我们改变一下`resolvers`

```
const resolvers = {
  Query: {
    Greeting: () => `Hello World`,
    People: () => People.find({}),
  }
} 
```

在那里，我们返回数据库中模型的所有条目。

显然，如果我们在操场上重新运行查询，我们将得到一个空数组用于查询`People`,因为数据库中什么也没有。

现在我们需要通过 GraphQL 添加到数据库中。为此，我们需要使用突变:

```
const typeDefs = `
type Query {
  people: [People!]!
  person(id: ID!): People
}
type People {
    id: ID
    first: String
    last: String
}
type Mutation {
    createPerson(first: String!, last: String!): People
}
` 
```

首先，我们指定类型；一个名为`createPerson`的变异需要两个属性，并将返回`People`对象。像往常一样，我们需要在`resolvers`中指定运行该突变的结果

```
const resolvers = {
  Query: {....},
  Mutation: {
    createPerson: async (parent, args) =>{
        const newPerson = new People({
            first: args.first,
            last: args.last
        })
        const error = await newPerson.save()

        if(error) return error 
        return newPerson
    }
  }
} 
```

类似于其他解析器，我们希望在`createPerson`运行时做一些事情。这次我们需要访问用户传递的变量，因此是`args` ( `parent`超出了本教程的范围)。

首先我们创建想要添加到数据库中的`newPerson`对象，然后我们创建一个新的用`newPerson`对象填充的`People`模块，最后，我们将对象保存到数据库中，然后我们简单地将新创建的对象返回给 graphQL 服务器。

回到 GraphQL 游乐场

```
query people {
  Greeting
  People{
    first
    last
  }
}
mutation addingPerson{
  createPerson(first: "Gregor", last: "Samsa"){
    first
    last

  }
} 
```

在那里，如果你想运行变异，你可以运行`addingPerson`(通过下拉菜单)。当然，在添加 Gregor 之后，当您运行`people`查询时，您可以看到这个 bug:)。

最后，让我们遵循同样的逻辑，添加从数据库中删除一个人的功能。

首先，指定类型`deletePerson`:

```
type Mutation {
    createPerson(first: String!, last: String!): PeopleObject
    deletePerson(id: ID!): PeopleObject
} 
```

那么让我们把它当作另一种变异来解决:

```
const resolvers = {
  ...
  Mutation: {
    createPerson: (parent, args) =>{...},
    deletePerson: (parent, args) => {
        return new Promise( (resolve, reject) => {
            People.findOneAndDelete(args.id, function(err, result){
                if (err) return err;
                resolve(result)
            })
        })
    }
  }

} 
```

这是完全相同的逻辑，mongoose 给了我们通过 id 删除对象的能力——使用`findOneAndDelete`。您可以按如下方式测试`deletePerson`:

```
mutation deleting{
  deletePerson(id: "5c1ccc3de652317c2c79d4ee"){
    first
    last
  }
} 
```

这就是本教程的全部内容。

你可以在 [github](https://github.com/aurelkurtula/Playing-with-GraphQL-yoga-and-mongoose) 获取代码。代码被分成不同的文件，就像在一个充实的项目中一样
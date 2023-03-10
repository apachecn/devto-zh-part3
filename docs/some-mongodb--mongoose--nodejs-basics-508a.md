# 一些 mongoDB+mongose+NodeJS 基础

> 原文：<https://dev.to/tttaaannnggg/some-mongodb--mongoose--nodejs-basics-508a>

### 警告

请注意，这完全是一个 hello-world 类型的练习。在真实环境中，您的数据库可能会有更多的安全配置。这将创建一个完全不受保护的数据库，任何连接到该数据库的人都可以读取或修改该数据库。也就是说，这里有一个关于如何从基本理解 NodeJS 到读写 mongoDB 数据库的指南。

### 运行 MongoDB 的进程

下面是 MongoDB 的大图分解。MongoDB 由**数据库**组成，其中有**集合**，集合中包含多个**文档**。在`mongoose`中，**文档**本质上是遵循我们定义的**模式**的 javascript 对象。

为了在我们的数据库中完成任何事情，我们需要运行一个允许用户访问它的进程。为了做到这一点，您需要安装 MongoDB，然后运行`mongod`。关于/data/db 中的权限，您可能有错误。这是因为/data/db 是一个受保护的文件夹，您可能需要以超级用户的身份运行 mongod(这可能不是最好的——我不清楚这里的安全含义)。看起来是这样的:`sudo mongod`。(让此窗口保持打开，以便进程可以继续运行，除非您想关闭数据库，否则不要触摸它。)

当您键入`sudo mongod`时，您的控制台上显示的部分内容应该是这样的:`MongoDB starting : pid=2265 port=27017 dbpath=/data/db`。默认情况下，mongoDB 实例的端口是 27017，但是当您启动 mongod 时，它总是会被打印出来。就找**港**。这一点以后会很重要。

现在，您的数据库将存储在/data/db 中，尽管不是任何我们可读的格式。我们可以通过 mongo shell 访问我们的数据库，这一点我们稍后会讲到。

### 创建和配置我们的 NodeJS 服务器

现在，我们终于可以开始创建和配置我们的服务器了。

首先，我们必须`npm init`在我们想要的目录中创建我们的服务器，然后我们将`npm install mongodb`和`npm install mongoose`作为概念验证/ hello world 类型服务器的最低要求。

我们将在项目文件夹中创建一个名为`server.js`的文件，我们将使用`node server.js`运行该文件。这个`server.js`将包含我们的服务器遵循的所有逻辑。

我们的目标是创建一个数据库，从我们的服务器写入和读取。下面是我们将如何做的快速路线图:

1.  进口猫鼬
2.  连接到我们的数据库
3.  使用 mongoose 创建一个**模式**对象。Schema()构造函数和一个为数据定义标准(键、数据类型等)的对象
4.  使用 mongoose.connect()创建一个**模型**，使用我们的**集合**的名称，以及我们刚刚创建的**模式**对象
5.  使用**模型**作为构造函数来生成一个 mongoDB **文档**对象
6.  我们返回的 mongoDB **文档**对象将有一个名为 save()的方法，这将允许我们将其保存到数据库中。
7.  如果我们想在之后找到我们的数据，我们的**模型**(不是文档)，有一个叫做‘find()’的方法。注意，我们的数据库方法是异步的。

### 第一步:在我们的服务器文件中导入 mongoose。

```
//in server.js
const mongoose = require('mongoose') 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们连接到数据库。同样，如果您已经在 CLI 中运行了`mongod`，它应该会打印出一些信息，包括一个端口号(默认为 27017)。如果您要连接到远程主机，他们会提供登录所需的信息。

### 第二步:连接数据库

您将在`mongodb://localhost:<portnumber>/<databasename>`连接，它看起来像这样:

```
//in server.js
const mongoose = require('mongoose')
mongoose.connect('mongodb://localhost:27017/test', {useNewUrlParser: true}); 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经注意到，我们在`mongodb://localhost:27017/test`的末尾添加了“测试”。`test`是我们选择用来命名数据库的名称。我们还没有创建这个数据库，但这并不重要，因为在 MongoDB 中，当我们开始在上面存储文档时，它就会被创建。

既然这个连接已经建立，我们就可以开始创建模式、模型和文档了。这从我们的`mongoose.schema`方法开始，它是一个位于`mongoose`上的构造函数

让我们将我们的`mongoose.schema`存储在一个 const 上，这样我们可以更容易地访问它。

### 步骤 2.5:保存我们的 schema 方法

```
//in server.js
const mongoose = require('mongoose')
mongoose.connect('mongodb://localhost:27017/test', {useNewUrlParser: true});
const Schema = mongoose.Schema 
```

Enter fullscreen mode Exit fullscreen mode

猫鼬。Schema 是一个接受对象的构造函数。

我们给构造函数的对象是一个“模式”,它用键/值对来表示我们的数据结构。

### 第三步:创建模式

在我们的示例代码中，我们的模式将被称为 exampleSchema。

```
//in server.js
const mongoose = require('mongoose')
mongoose.connect('mongodb://localhost:27017/test', {useNewUrlParser: true});
const Schema = mongoose.Schema
const exampleSchema = new Schema({
    someKeyName: {type: String, required: true, unique:true},
    someOtherKeyName: {type: Number, required: false, unique:false}
}) 
```

Enter fullscreen mode Exit fullscreen mode

这意味着集合中的每个对象都有两个键:someKeyName 和 someOtherKeyName。存储在 someKeyName 上的值将具有类型`String`，将是必需的，并且将是唯一的。someOtherKeyName 上存储的值需要是一个`Number`，既不是必需的，也不是唯一的(我不确定什么是唯一的)。

### 第四步:创建模型

接下来，我们将创建一个叫做“模型”的东西，它将我们的模式链接到我们的集合。

我们调用`mongoose.model()`，它有两个参数，一个是我们集合的名称，另一个是我们创建的新对象。再说一次，我们还没有创建一个集合并不重要。一旦我们尝试在其中存储数据，MongoDB 就会为我们创建它。

```
//in server.js
const mongoose = require('mongoose')
mongoose.connect('mongodb://localhost:27017/test', {useNewUrlParser: true});
const Schema = mongoose.Schema
const exampleSchema = new Schema({
    someKeyName: {type: String, required: true, unique: true} ,
    someOtherKeyName: {type: Number, required: false, unique:false}
})
const newModel = mongoose.model('collectionName', exampleSchema) 
```

Enter fullscreen mode Exit fullscreen mode

该步骤将模式与您的集合的名称链接起来(这产生了一个“模型”)。这个模型实际上是一个构造函数，它创建“文档”对象。

### 第五步:创建文档

为了做到这一点，我们采用我们的模型(`newModel`)，并给它一个对象，该对象保存我们想要放入单个文档中的所有数据。

```
//in server.js
const mongoose = require('mongoose')
mongoose.connect('mongodb://localhost:27017/test', {useNewUrlParser: true});
const Schema = mongoose.Schema
const exampleSchema = new Schema({
    someKeyName: {type: String, required: true, unique: true} ,
    someOtherKeyName: {type: Number, required: false, unique:false}
})
const newModel = mongoose.model('collectionName', exampleSchema)
const newDocument = newModel({someKeyName: 'hi', someOtherKeyName: 12345}) 
```

Enter fullscreen mode Exit fullscreen mode

### 第六步:保存文档

现在`newDocument`对象存在了，我们可以访问它上面的方法。其中之一是一种`save`方法。它做的和你想的完全一样。

```
//in server.js
const mongoose = require('mongoose')
mongoose.connect('mongodb://localhost:27017/test', {useNewUrlParser: true});
const Schema = mongoose.Schema
const exampleSchema = new Schema({
    someKeyName: {type: String, required: true, unique: true} ,
    someOtherKeyName: {type: Number, required: false, unique:false}
})
const newModel = mongoose.model('collectionName', exampleSchema)
const newDocument = newModel({someKeyName: 'hi', someOtherKeyName: 12345})
newDocument.save() 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你。您刚刚使用 mongoose 创建并写入了一个数据库。

让我们继续验证文档是否存在于我们的集合中。您可以通过下一个命令序列在 node 之外完成此操作:

将启动 mongo 命令行。

在 mongo 命令行界面中，您可以键入以下内容:

将向您显示一个数据库列表。你应该在列表上看到`test`。

`use test`将让您登录到您的`test`数据库。

`show collections`将显示您的`test`数据库中的收藏。您应该会看到一个`collectionName`集合被打印出来。

`db.collectionName.find()`将对`collectionName`数据库进行空查询，该查询将返回该集合中的每个文档。

你应该看到这样的东西:`{ "_id" : ObjectId("5c78204590796a1c74a20b11"), "someKeyName" : "hi", "someOtherKeyName" : 1234, "__v" : 0 }`

这是你的数据库条目！

当然，这对我们来说没那么有用。假设我们试图从 nodeJS 内部使用我们的数据库，我们需要能够从我们的服务器文件中读取我们的数据库。

回到服务器内部，让我们在模型上调用“find ”,以便从数据库中读取数据。

```
//in server.js
const mongoose = require('mongoose')
mongoose.connect('mongodb://localhost:27017/test', {useNewUrlParser: true});
const Schema = mongoose.Schema
const exampleSchema = new Schema({
    someKeyName: {type: String, required: true, unique: true} ,
    someOtherKeyName: {type: Boolean, required: false, unique:false}
})
const newModel = mongoose.model('collectionName', exampleSchema)
newModel.find({somekeyname:'hi'}, (err,results)=>{
    console.log(results)
}) 
```

Enter fullscreen mode Exit fullscreen mode

这可能需要一点解释。第一个论点相当清楚。这是一个我们正在寻找的具有键/值对的对象。第二个参数(称为回调函数)可能不太明显。

基本上，我们期望`newModel.find`查询我们的数据库，然后对数据做一些事情。它是做什么的？这是在回调函数中定义的。如果搜索由于某种原因失败了，它将传入一个参数作为`err`，如果成功了，我们将在`results`中得到一些东西。

`{someKeyName: "hi"}`应该与我们的条目`{ "_id" : ObjectId("5c78204590796a1c74a20b11"), "someKeyName" : "hi", "someOtherKeyName" : 1234, "__v" : 0 }`相匹配，而我们的数据库中没有其他任何东西，所以我们的`results`变量将计算为:`[{ "_id" : ObjectId("5c78204590796a1c74a20b11"), "someKeyName" : "hi", "someOtherKeyName" : 1234, "__v" : 0 }]`。`console.log`将简单地把这个值打印到我们的终端上。

注意，尽管只是一个项目，但对`find`的回调返回了一个数组。如果它没有找到任何东西，它只会给出一个空数组`[]`。

现在您已经获得了匹配数组，您可以开始用它做一些事情，无论是查找和显示信息、检查认证还是其他类似的操作！
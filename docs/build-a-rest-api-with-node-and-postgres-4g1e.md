# 用节点和 Postgres 构建一个 REST API

> 原文：<https://dev.to/oktadev/build-a-rest-api-with-node-and-postgres-4g1e>

如果你没有听说过 PostgreSQL(通常称为 Postgres)，今天是你的幸运日。这是一个健壮的开源关系数据库，支持世界上一些最大的应用程序。在本文中，我将向您展示如何在 Node 中创建一个 REST API，该 API 使用 Postgres 作为数据存储。我会带你完成所有的设置，所以如果你不熟悉 Postgres，*不要担心*。

具体来说，我将带您构建一个 API，它将跟踪您通过各种来源(如 DVD 和网飞)拥有(或访问)的电影。然后，您将能够查询这个 API 来查看什么应用程序(或橱柜？)您需要打开才能访问您想看的电影。在本文结束时，您将了解如何使用 Node 和 Postgres 构建这个 API，以及如何使用 OAuth 2 保护您的 API。

## 设置你的节点+ Postgres 的依赖关系

对于本教程，我使用的是节点 10.5.3。如果你使用的是更高的版本，甚至是 Node 8，你可能会没事，但是如果你使用的是 0.12 版本，你可能会遇到一些问题。如果你还没有安装 Node，最简单的方法就是使用 [NVM](https://github.com/creationix/nvm) ，它可以让你同时安装多个版本，让升级到最新版本像`nvm install node`一样简单。如果您没有安装节点或 NVM，请使用以下命令安装 NVM:

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash 
```

如果由于某种原因无法工作，根据您的系统，您可以在此处找到更多故障排除信息[。](https://github.com/creationix/nvm#install--update-script)

### 安装 PostgreSQL

我用的是 PostgreSQL 版。在本教程中，我不会使用任何复杂的查询，所以如果您安装了不同的版本，这应该不是问题。Postgres 预装在一些系统中。要检查是否已经安装了 Postgres，请运行以下命令:

```
postgres --version 
```

你应该得到类似于`postgres (PostgreSQL) 10.4`的东西。如果您得到类似于`bash: postgres: command not found`的错误，那么您还没有安装它。要安装 Postgres，您最好使用一个包管理器。如果你使用的是基于 Debian 的 Linux 发行版，你可以简单地运行:

```
sudo apt-get install postgresql-10 
```

如果你运行的是 MacOS 并且安装了 Homebrew，你可以简单地运行:

```
brew install postgresql 
```

对于任何其他操作系统，请访问 [PostgreSQL 的下载页面](https://www.postgresql.org/download/)以获得启动和运行的帮助。

### 创建 Postgres 数据库

安装 Postgres 后，您需要为该应用创建一个数据库来连接。键入`psql`连接到您的 Postgres 服务器，然后键入`create database movie_catalog`。

## 搭建基础节点 App

首先，创建一个新目录来存储您的应用程序(例如`movie-catalog`)。然后从命令行(`cd movie-catalog`)进入目录，键入`npm init`初始化您的节点项目(并创建一个`package.json`文件):

```
$ npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (movie-catalog)
version: (1.0.0) 0.1.0
description: Movie Catalog API
entry point: (index.js) src/index.js
test command:
git repository:
keywords:
author:
license: (ISC) Apache-2.0
About to write to /Users/bmk/code/okta/apps/movie-catalog/package.json:

{
  "name": "movie-catalog",
  "version": "0.1.0",
  "description": "Movie Catalog API",
  "main": "src/index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "Apache-2.0"
}

Is this OK? (yes) 
```

通常，应用程序的配置文件位于项目文件夹的根目录下，源代码位于单独的文件夹中。创建一个新的文件夹`src`来保存你的代码:

```
mkdir src 
```

如果您计划使用`git`进行源代码控制，您还应该创建一个`.gitignore`文件，以确保您不会提交*巨大的* `node_modules`目录。稍后您还将使用一个不想提交的`.env`文件。继续在你的项目文件夹的根目录下创建一个名为`.gitignore`的文件，并复制以下内容:

```
node_modules
.env 
```

接下来您需要做的是对数据库模式进行排序。一种方法是使用一个叫做 [Sequelize](http://docs.sequelizejs.com/) 的 ORM。您需要安装 Sequelize 所需的依赖项以及它连接到 Postgres 所需的库。

```
npm install sequelize@4.43.0 pg@7.8.1 
```

创建一个新文件`src/database.js`。在这里，您将设置运行电影目录所需的 Sequelize 数据库和模型。您将从第三方接收标题信息，您可以将这些信息存储在 JSON 中(使用 Postgres JSONB 类型)并直接访问或查询。您还将创建一个表来存储用户拥有哪些服务的信息(例如，网飞、Hulu、DVD 等。).然后你需要一个表格来连接两者；如果是 DVD 或蓝光影片，您可以使用此表格来提供额外的信息，如位置。

```
const Sequelize = require('sequelize')

const database = new Sequelize({
  database: 'movie_catalog',
  dialect: 'postgres',
  operatorsAliases: Sequelize.Op
})

const Title = database.define('title', {
  id: { type: Sequelize.STRING, primaryKey: true },
  title: { type: Sequelize.JSONB, allowNull: false }
})

const Service = database.define('service', {
  userId: { type: Sequelize.STRING, unique: 'user-name', allowNull: false },
  name: { type: Sequelize.STRING, unique: 'user-name', allowNull: false }
})

const TitleService = database.define('title_service', {
  location: Sequelize.STRING
})

TitleService.belongsTo(Title, {
  foreignKey: { allowNull: false, unique: 'title-service' },
  onDelete: 'cascade'
})

TitleService.belongsTo(Service, {
  foreignKey: { allowNull: false, unique: 'title-service' },
  onDelete: 'cascade'
})

module.exports = {
  Title,
  Service,
  TitleService,
  database
} 
```

接下来，设置 Express 服务器，它将提供内容。您需要安装更多的依赖项:

```
npm install express@4.16.4 cors@2.8.5 body-parser@1.18.3 
```

现在编辑`src/index.js` :

```
const express = require('express')
const cors = require('cors')
const bodyParser = require('body-parser')

const { database } = require('./database')

const port = process.env.SERVER_PORT || 3000

const app = express()
app.use(cors())
app.use(bodyParser.json())

// TODO: Remove this function and actually implement authentication
app.use('/', (req, res, next) => {
  req.userId = 'TODO'
  next()
})

// TODO: Add routes

database.sync().then(() => {
  app.listen(port, () => {
    console.log(`Listening on port ${port}`)
  })
}) 
```

到目前为止，这并没有真正做什么。您可以通过运行`node .`来启动它，但是它实际上只是托管了一个没有路由的服务器。接下来您需要添加这些内容。这段代码还嵌入了`TODO`的一个`userId`，稍后您将修复它。在此之前，您的 API 将只假设一个用户。

## 用节点取电影片头

为了能够获得电影或电视节目的元数据，您需要使用第三方 API。一个简单的开始是[开放电影数据库](https://www.omdbapi.com/)。你需要[注册一个免费的 API 密匙](https://www.omdbapi.com/apikey.aspx)，并确认你的电子邮件地址。一旦有了 API 密匙，在根目录下创建一个名为`.env`的新文件，并添加 API 密匙:

```
OMDB_API_KEY=abcd1234 
```

您还需要添加几个新的依赖项。为了读取`.env`文件并将值添加为可以在代码中访问的环境变量，您需要安装`dotenv`。要获取电影，您将需要`node-fetch`，它提供了浏览器默认拥有的相同 API，并且比 Node 的内置 API 更容易使用:

```
npm install dotenv@6.2.0 node-fetch@2.3.0 
```

为了让`dotenv`发挥它的魔力，你需要在`src/index.js`的顶部添加下面一行。它应该是运行的第一段代码:

```
require('dotenv').config() 
```

为了找到电影标题，您将在 OMDb 的 API 上编写一个简单的包装器。这样你就可以把所有需要的东西都放在一个地方。为了让事情变得更简单，让我们在`src/omdb.js`创建一个实用程序文件，让您的代码可以方便地查找标题:

```
const fetch = require('node-fetch')

const { OMDB_API_KEY } = process.env
const API_URL = 'https://www.omdbapi.com'

const search = async query => {
  const url = new URL(API_URL)
  url.searchParams.set('apikey', OMDB_API_KEY)
  url.searchParams.set('v', 1)
  url.searchParams.set('s', query)

  const response = await fetch(url)
  const {
    Response: success,
    Search: searchResults
  } = await response.json()

  return success === 'True' ? searchResults : []
}

const getTitle = async id => {
  const url = new URL(API_URL)
  url.searchParams.set('apikey', OMDB_API_KEY)
  url.searchParams.set('v', 1)
  url.searchParams.set('i', id)

  const response = await fetch(url)
  const {
    Response: success,
    Error: error,
    ...title
  } = await response.json()

  if (success === 'True') {
    return title
  }

  throw new Error(error)
}

module.exports = { search, getTitle } 
```

现在有两个函数可以让您按名称搜索标题，或者按特定 ID 选择有关标题的更多详细信息。现在创建一个新文件`src/titles.js`，它将处理所有与标题相关的活动:

```
const express = require('express')
const omdb = require('./omdb')

const router = express.Router()

router.get('/', async (req, res, next) => {
  try {
    if (!req.query.s) throw new Error('Search param (`s`) required')

    res.json(await omdb.search(req.query.s))
  } catch (error) {
    res.json({ error: error.message })
  }
})

router.get('/:id', async (req, res, next) => {
  try {
    res.json(await omdb.getTitle(req.params.id))
  } catch (error) {
    res.json({ error: error.message })
  }
})

module.exports = router 
```

在 Express 中使用路由器时，您的路径是相对于您使用路由器时给出的路径。在`src/index.js`中，在您之前留下的`TODO`评论后添加以下内容:

```
app.use('/titles', require('./titles')) 
```

在 Express 中使用路由器时，路由是相对于添加位置的。在`src/titles.js`中，您指定了`/`路线应该允许您搜索标题。但是在`src/index.js`中，你指定路线应该是相对于`/titles`的。因此，如果你去`/titles`你将能够搜索，你可以使用`/titles/:id`获得特定标题的信息。

重启你的服务器(你可以点击`ctrl-c`来停止它，然后点击`node .`来再次启动它)。现在要测试它，你可以在命令行中使用`curl`。

```
curl -s http://localhost:3000/titles?s=Fight+Club 
```

你应该会得到一大块 JSON。为了便于阅读，可以安装一个有用的命令行工具:

```
npm install --global json@9.0.6 
```

通过将结果传送到`json`重试。没有任何选项，它将以一种更容易阅读的格式显示所有内容。这里有几个例子，带有一些提取相关信息的选项:

```
$ curl -s http://localhost:3000/titles?s=Harry+Potter | json -a Year Title imdbID | sort
2001 Harry Potter and the Sorcerer's Stone tt0241527
2002 Harry Potter and the Chamber of Secrets tt0295297
2002 Harry Potter and the Chamber of Secrets tt0304140
2004 Harry Potter and the Prisoner of Azkaban tt0304141
2005 Harry Potter and the Goblet of Fire tt0330373
2007 Harry Potter and the Order of the Phoenix tt0373889
2009 Harry Potter and the Half-Blood Prince tt0417741
2010 Harry Potter and the Deathly Hallows: Part 1 tt0926084
2010 Harry Potter and the Forbidden Journey tt1756545
2011 Harry Potter and the Deathly Hallows: Part 2 tt1201607

$ curl -s http://localhost:3000/titles/tt0137523 | json Title Year Director Writer Actors
Fight Club
1999
David Fincher
Chuck Palahniuk (novel), Jim Uhls (screenplay)
Edward Norton, Brad Pitt, Meat Loaf, Zach Grenier 
```

## 使用节点与 Postgres 交互

此时，您应该有了一个简单的 API，可以获取关于电影标题的信息。现在是时候将 Postgres 集成到您的应用程序中，以便您可以跟踪这些电影。

### 创建节点服务路线

在你可以询问 API 你拥有(或者可以访问)哪些标题之前，你需要创建一个服务来指定*你如何*可以观看一部给定的电影。创建一个新文件`src/services.js`，并复制以下代码:

```
const express = require('express')

const { Service } = require('./database')

const router = express.Router()

router.get('/', async (req, res, next) => {
  const { userId } = req
  res.json(await Service.findAll({
    attributes: ['id', 'name'],
    where: { userId }
  }))
})

router.post('/', async (req, res, next) => {
  try {
    const { userId } = req
    const { name } = req.body
    const { id } = await Service.create({ userId, name })
    res.json({ success: true, id })
  } catch (error) {
    res.json({ success: false, error: error.message })
  }
})

router.delete('/:id', async (req, res, next) => {
  try {
    const { userId } = req
    const { id } = req.params
    if (await Service.destroy({ where: { userId, id } })) {
      res.json({ success: true })
    }
  } catch (error) { }

  res.json({ success: false, error: 'Invalid ID' })
})

module.exports = router 
```

您还需要在`src/index.js`中添加一条路线，就在您上面添加的`/titles`路线之后:

```
app.use('/services', require('./services')) 
```

这给了你三种不同的路线。你可以发送一个`GET`请求来查看你所有的服务，一个`POST`请求来添加一个新的，或者一个`DELETE`请求来删除一个。试一试:

```
$ curl -sH 'Content-Type: application/json' http://localhost:3000/services
[]

$ curl -sH 'Content-Type: application/json' http://localhost:3000/services -XPOST -d '{"name":"Netflix"}'
{"success":true,"id":1}

$ curl -sH 'Content-Type: application/json' http://localhost:3000/services -XPOST -d '{"name":"asdf"}'
{"success":true,"id":2}

$ curl -sH 'Content-Type: application/json' http://localhost:3000/services -XPOST -d '{"name":"Blu-ray"}'
{"success":true,"id":3}

$ curl -sH 'Content-Type: application/json' http://localhost:3000/services
[{"id":3,"name":"Blu-ray"},{"id":2,"name":"asdf"},{"id":1,"name":"Netflix"}]

$ curl -sH 'Content-Type: application/json' http://localhost:3000/services/2 -XDELETE
{"success":true}

$ curl -sH 'Content-Type: application/json' http://localhost:3000/services
[{"id":3,"name":"Blu-ray"},{"id":1,"name":"Netflix"}] 
```

### 创建节点标题路线

现在，您需要一种将服务与标题相关联的方法。在`src/my-titles.js`创建一个新路由器。这个会稍微长一点，因为它将一个`Title`和一个`Service`组合在一起，并允许您用一个`PUT`请求来更新一个位置:

```
const express = require('express')
const { getTitle } = require('./omdb')
const { Title, TitleService, Service } = require('./database')

const router = express.Router()

router.get('/', async (req, res, next) => {
  try {
    const full = 'full' in req.query
    const { userId } = req

    const data = await TitleService.findAll({
      attributes: ['id', 'location'],
      where: { '$service.userId$': userId },
      include: [{
        model: Title,
        attributes: ['title']
      }, {
        model: Service,
        attributes: ['id', 'name']
      }]
    })

    res.json(
      data.map(({ id, location, title: { title }, service }) => ({
        id,
        location,
        title: full
          ? title
          : { id: title.imdbID, name: `${title.Title} (${title.Year})` },
        service: { id: service.id, name: service.name }
      }))
    )
  } catch (error) {
    res.json({ error: error.message })
  }
})

router.post('/', async (req, res, next) => {
  try {
    const { titleId, serviceId, location } = req.body

    await Title.upsert({ id: titleId, title: await getTitle(titleId) })

    const { userId } = await Service.findByPk(serviceId)
    if (userId === req.userId) {
      const { id } = await TitleService.create({ titleId, serviceId, location })

      return res.json({ id })
    }
  } catch (error) {
    console.log(error)
  }

  res.json({ error: 'Error adding title' })
})

router.put('/:id', async (req, res, next) => {
  try {
    const { location } = req.body
    const { id } = req.params
    const { userId } = req

    const titleService = await TitleService.findByPk(id, { include: [{ model: Service }] })
    if (titleService && titleService.service.userId === userId) {
      await titleService.update({ location })
      return res.json({ id })
    }
  } catch (error) {
    console.log(error)
  }

  res.json({ error: 'Invalid ID' })
})

router.delete('/:id', async (req, res, next) => {
  try {
    const { id } = req.params
    const { userId } = req

    const titleService = await TitleService.findByPk(id, { include: [{ model: Service }] })
    if (titleService && titleService.service.userId === userId) {
      await titleService.destroy()
      res.json({ success: true })
    }
  } catch (error) {
    console.log(error)
  }

  res.json({ error: 'Invalid ID' })
})

module.exports = router 
```

同样，您需要将路由器添加到`src/index.js`，在您之前添加的其他路由之后:

```
app.use('/my-titles', require('./my-titles')) 
```

`DELETE`和`POST`请求最终与`/services`路由非常相似。与`POST`请求的主要区别在于，它还将验证标题是否存在于 OMDb 上，并将值插入到`titles`表中，以便以后更快地查找。`PUT`请求是新的，它允许您修改现有的“我的标题”。`GET`请求稍微长一点，因为它需要将所有的信息缝合在一起。它还允许您添加一个`full`参数来获取更多信息，但是只返回几个没有它的字段。测试一下:

```
$ curl -sH 'Content-Type: application/json' http://localhost:3000/my-titles
[]

$ curl -sH 'Content-Type: application/json' http://localhost:3000/my-titles -XPOST -d '{"serviceId":3,"titleId":"tt0241527","location":"Bookshelf"}'
{"id":1}

$ curl -sH 'Content-Type: application/json' http://localhost:3000/my-titles -XPOST -d '{"serviceId":1,"titleId":"tt4574334"}'
{"id":2}

$ curl -sH 'Content-Type: application/json' http://localhost:3000/my-titles | json -o inspect
[ { id: 1,
    location: 'Bookshelf',
    title:
     { id: 'tt0241527',
       name: 'Harry Potter and the Sorcerer\'s Stone (2001)' },
    service: { id: 3, name: 'Blu-ray' } },
  { id: 2,
    location: null,
    title: { id: 'tt4574334', name: 'Stranger Things (2016–)' },
    service: { id: 1, name: 'Netflix' } } ]

$ curl -sH 'Content-Type: application/json' http://localhost:3000/my-titles/2 -XPUT -d '{"location":"Internet"}'
{"id":"2"}

$ curl -sH 'Content-Type: application/json' http://localhost:3000/my-titles/1 -XDELETE
{"success":true}

$ curl -sH 'Content-Type: application/json' http://localhost:3000/my-titles | json -o inspect
[ { id: 2,
    location: 'Internet',
    title: { id: 'tt4574334', name: 'Stranger Things (2016–)' },
    service: { id: 1, name: 'Netflix' } } ] 
```

## 将用户认证添加到您的节点 API

您现在有了一个简单的 API，可以用来跟踪您的电影和电视节目。不幸的是，只有一个人可以使用它，除非您希望所有人共享同一个库。在这里，您可以使用 [Okta](https://developer.okta.com/) 添加身份验证，并轻松地为每个用户提供他们自己的电影目录。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。Okta API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看[产品文档](https://developer.okta.com/documentation/)

如果你还没有，那么[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)。登录你的开发者控制台，导航到**应用**，然后点击**添加应用**。选择**网**，然后点击**下一个**。

您需要将默认端口从`8080`更改为`3000`以匹配您的服务器。您的设置应该如下所示:

[![New Application Settings](img/f22f6c39bc4edd2a71da47b33884190a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yHQrCEa5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-postgres/okta-application-settings-fa8782d7bc6deb27edcb2ca4b6e12f3a60a21028baaf93af6e6606a6bdfafb44.png)

点击**完成**保存你的 app，然后复制你的**客户端 ID** 和**客户端秘密**并作为变量粘贴到你项目根目录下的`.env`文件中。您还需要添加您的组织 URL(不带`-admin`后缀)。将这三个变量添加到您现有的`.env`文件:

```
OKTA_ORG_URL=https://{yourOktaDomain}
OKTA_CLIENT_ID={yourClientId}
OKTA_CLIENT_SECRET={yourClientSecret} 
```

还需要一个 app 秘笈。获得随机`APP_SECRET`的一种方法是使用下面的命令，这将生成一个随机值并将其添加到您的`.env`文件中。

```
npm install -g uuid-cli
echo "APP_SECRET=`uuid`" >> .env 
```

现在您的环境变量已经准备好了，您需要安装一些新的依赖项来让 Okta 工作:

```
npm install @okta/jwt-verifier@0.0.14 @okta/oidc-middleware@2.0.0 express-session@1.15.6 
```

创建一个新文件`src/okta.js`。这里您将创建一个`initialize`函数，它需要 Express 应用程序和端口号来初始化。你将从`src/index.js`传入那个信息。您还将导出一个定制的`requireUser`中间件，该中间件将检查以确保用户已经过身份验证，并向请求添加适当的`userId`，而不是`TODO`。如果用户没有通过身份验证，他们会收到一条错误消息。

```
const session = require('express-session')
const { ExpressOIDC } = require('@okta/oidc-middleware')
const OktaJwtVerifier = require('@okta/jwt-verifier')

const issuer = `${process.env.OKTA_ORG_URL}/oauth2/default`

const initialize = (app, port) => {
  const oidc = new ExpressOIDC({
    issuer,
    client_id: process.env.OKTA_CLIENT_ID,
    client_secret: process.env.OKTA_CLIENT_SECRET,
    appBaseUrl: process.env.APP_BASE_URL || `http://localhost:${port}`,
    scope: 'openid profile'
  })

  app.use(session({
    secret: process.env.APP_SECRET,
    resave: true,
    saveUninitialized: false
  }))
  app.use(oidc.router)

  app.get('/', oidc.ensureAuthenticated(), (req, res) => {
    res.send(req.userContext.tokens.access_token)
  })

  return oidc
}

const oktaJwtVerifier = new OktaJwtVerifier({
  issuer,
  clientId: process.env.OKTA_CLIENT_ID
})

const requireUser = async (req, res, next) => {
  try {
    const { authorization } = req.headers
    if (!authorization) throw new Error('You must send an Authorization header')

    const [authType, token] = authorization.split('  ')
    if (authType !== 'Bearer') throw new Error('Expected a Bearer token')

    const { claims: { sub } } = await oktaJwtVerifier.verifyAccessToken(token)
    req.userId = sub
    next()
  } catch (error) {
    res.json({ error: error.message })
  }
}

module.exports = { initialize, requireUser } 
```

现在回到`src/index.js`并做一些改变。用以下内容替换假认证功能:

```
const okta = require('./okta')
okta.initialize(app, port) 
```

您还需要将`okta.requireUser`作为中间件添加到您的`/services`和`/my-titles`路线中。您可以决定是否需要一个用户来查询标题，但这并不是绝对必要的，因为您只是从这个途径查询 OMDb API。您的`src/index.js`文件现在应该看起来像这样:

```
require('dotenv').config()

const express = require('express')
const cors = require('cors')
const bodyParser = require('body-parser')

const { database } = require('./database')
const okta = require('./okta')

const port = process.env.SERVER_PORT || 3000

const app = express()
app.use(cors())
app.use(bodyParser.json())
okta.initialize(app, port)

app.use('/titles', require('./titles'))
app.use('/services', okta.requireUser, require('./services'))
app.use('/my-titles', okta.requireUser, require('./my-titles'))

database.sync().then(() => {
  app.listen(port, () => {
    console.log(`Listening on port ${port}`)
  })
}) 
```

是检验一切的时候了。为了使用这个 API 进行认证，您需要在 web 浏览器中访问 [http://localhost:3000](http://localhost:3000) 。这将提示您登录，并在通过身份验证后，打印出一个身份验证令牌。复制并将其作为标题添加到您的 curl 请求中。最简单的方法是用你的头文件创建一个新文件。它应该是这样的:

```
Content-Type: application/json
Authorization: Bearer eyJraW...NysQChA 
```

实际的令牌要长得多，但这就是我们的想法。在`curl`中，代替`-H 'Content-Type: application/json'`，你现在可以做`-H "$(cat headers.txt)"`，假设在同一个目录中文件名为`headers.txt`。登录后尝试一些东西(记住，你之前输入的任何东西都不可用，因为它在`TODO`的`userId`下)。

```
$ curl -sH "$(cat headers.txt)" http://localhost:3000/my-titles | json -o inspect
[]

$ curl -sH "$(cat headers.txt)" http://localhost:3000/services -XPOST -d '{"name":"HBO Go"}'
{"success":true,"id":4}

$ curl -sH "$(cat headers.txt)" http://localhost:3000/titles?s=game+of+thrones | json 0.Title 0.imdbID
Game of Thrones
tt0944947

$ curl -sH "$(cat headers.txt)" http://localhost:3000/my-titles -XPOST -d '{"titleId":"tt0944947","serviceId":4}'
{"id":3}

$ curl -sH "$(cat headers.txt)" http://localhost:3000/my-titles | json -o inspect
[ { id: 3,
    location: null,
    title: { id: 'tt0944947', name: 'Game of Thrones (2011–)' },
    service: { id: 4, name: 'HBO Go' } } ] 
```

## 节点+ Postgres API 外卖

就是这样！现在，您应该有一个经过完全认证的 API 来跟踪您的电影和电视节目，它使用 Postgres 来存储数据，并使用 Okta + OAuth2 进行 API 认证。我希望你喜欢和 Node 和 Postgres 一起做这个小项目。如果想看最终的代码样本做参考，可以在 GitHub 上找到[。](https://github.com/oktadeveloper/okta-node-postgres-example)

要了解更多使用 Okta with Node 的示例，请查看其他一些帖子，或者浏览 Okta 开发者博客。

*   [使用 TypeScript 构建一个带有 Express 的节点 API](https://dev.to/blog/2018/11/15/node-express-typescript)
*   【Express 节点中的现代令牌认证
*   [使用 Passport.js 和 OpenID Connect 建立安全节点认证](https://dev.to/blog/2018/05/18/node-authentication-with-passport-and-oidc)
*   [用 Node 和 OAuth 2.0 构建一个简单的 REST API](https://dev.to/blog/2018/08/21/build-secure-rest-api-with-node)

如果你对这篇文章有任何问题，请在下面添加评论。更多精彩内容，请在 Twitter 上关注 [@oktadev](https://twitter.com/oktadev) ，就像我们在脸书关注[，或者订阅](https://www.facebook.com/oktadevelopers/)[我们的 YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。
# 💯Node.js + Express 应用程序中的请求计数

> 原文：<https://dev.to/sochix/counting-requests-in-your-node-js-express-application-4g8g>

## 前言

对 web 应用程序的请求进行计数的目的是什么？

正如我在[上一篇文章](%7B%%20link%20https://dev.to/sochix/four-key-metrics-of-rest-api-every-developer-should-worry-about-12ie%20%%7D)中所写的，了解请求的数量有助于你回答接下来的重要业务问题:

*   有人在用我的 API 吗？(如果请求计数为零，则可能没有人)
*   我的 API 在工作吗？(如果请求计数为零，则它可能已损坏)
*   我的 API 受到了 DDoS 攻击吗？(如果最后一个小时的请求数远远高于平均水平)

在我的例子中，这是一种业务需求——每个状态代码为“200”的对 REST API 的特定方法的请求都会给我们公司的银行账户增加几分钱。这就是我们决定调查此事的原因。

首先，我们探索了所有现有的付费和免费监控工具，长话短说，没有一个是完美的。

其次，我搜索了计算请求的 npm 库。我发现在 90%的情况下，开发人员出于限制请求速率的目的而统计请求。在这种情况下，速率限制是另一个与我的任务无关的主题。

粗略地说，我的任务是统计按方法和状态代码分组的所有请求。

## 编写中间件

我的 web app 是一个写在 Node.js + Express 上的 REST API。为了简化事情，这里是样板:

```
const app = require('express')()

app.get('/api/', (req, res) => {
    res.sendStatus(200)
})

app.listen(3000, () => {
    console.log('Server started')
}) 
```

在 Express framework 中捕获所有请求的唯一合法方法是实现一个中间件功能，并在任何其他处理程序之前加载它。

引用自[官方 Express.js 文档](https://expressjs.com/en/guide/writing-middleware.html):

> 中间件功能是可以访问请求对象(req)、响应对象(res)和应用程序请求-响应周期中的下一个功能的功能。下一个功能是快速路由器中的功能，当被调用时，执行当前中间件之后的中间件。中间件功能可以执行以下任务:执行任何代码。对请求和响应对象进行更改。结束请求-响应循环。调用堆栈中的下一个中间件。如果当前中间件函数没有结束请求-响应循环，它必须调用 next()将控制传递给下一个中间件函数。否则，请求将被搁置。

为了理解我的应用程序中发生了什么，我编写了这个中间件函数(见下文)并提出了几个请求。

```
app.use((req, res, next) => {
   console.log(`${req.method}  ${req.originalUrl}`) 
   next()
}) 
```

结果是

```
> curl http://localhost:3000/api
GET /api

> curl http://localhost:3000/api/
GET /api/

> curl http://localhost:3000/api?q=test GET /api?q=test 
```

好了，起作用了。让我们添加一个捕获响应状态代码的功能。Node.js 有一个默认事件，在发送响应时触发该事件。更具体地说，当响应头和响应体的最后一段被传递给操作系统以便通过网络传输时，就会发出此事件。这个钩子是`res.on("finish")`。

我应该注意到，并不是每个请求都进入“完成”状态，在现实生活中，客户端可以在响应发送之前关闭连接。在这种情况下，Node.js 只发出`res.on("close")`事件。为了让这篇文章尽可能简单，我决定忽略这些类型的请求。

我修改了我的中间件，添加了关于响应状态代码
的信息

```
app.use((req, res, next) => {
   res.on("finish", () => {
       console.log(`${req.method}  ${req.originalUrl}  ${res.statusCode}`) 
   })
   next()
}) 
```

结果是

```
> curl http://localhost:3000/api
GET /api 200

> curl http://localhost:3000/api/
GET /api/ 200

> curl http://localhost:3000/api/?q=test GET /api?q=test 200 
```

我们捕获了 http 动词、状态代码和原始 url。正如您所看到的，每个请求的`originalUrl`是不同的，但是处理程序路径总是相同的，它是`api.get("/api")`。让我们捕获处理程序路径，而不是`originalUrl`。有点棘手。

Express 将关于处理程序路径的数据存储在`req.route`对象中。只有在处理程序处理了请求之后，对象才会填充数据。如上所述，钩子`res.on("finish")`在所有的处理程序被执行并且响应被发送之后被调用。所以我们应该在`res.on("finish")`中注入一个捕获代码。我们还应该记住，有些请求可能没有处理程序，我们也应该以某种方式处理它们。

我写了一个小的助手函数来获得正确的处理程序路径

```
getRoute(req) {
   const route = req.route ? req.route.path : '' // check if the handler exist
   const baseUrl = req.baseUrl ? req.baseUrl : '' // adding the base url if the handler is a child of another handler

   return route ? `${baseUrl === '/' ? '' : baseUrl}${route}` : 'unknown route'
 } 
```

并修改了中间件

```
app.use((req, res, next) => {
   res.on(‘finish’, () => {
       console.log(`${req.method}  ${getRoute(req)}  ${res.statusCode}`) 
   })
   next()
}) 
```

现在结果是一致的

```
> curl http://localhost:3000/api
GET /api 200

> curl http://localhost:3000/api/
GET /api 200

> curl http://localhost:3000/api?q=test GET /api 200

> curl http://localhost:3000/
GET unknown route 404

> curl -X POST http://localhost:3000/
POST unknown route 404 
```

## 数据持久性

最后但并非最不重要的一步是存储捕获的数据。我决定用下一种格式存储数据:

```
{
    "GET /stats/ 200": 11, // "route name": "number of requests"
    "GET /api/ 200": 7,
    "GET unknown route 404": 2,
    "POST unknown route 404": 1
} 
```

出于演示目的，我们将把统计数据存储在一个 JSON 文件中。让我们添加两个助手方法来读取和转储数据。

```
const fs = require('fs')
const FILE_PATH = 'stats.json'

// read json object from file
const readStats = () => {
    let result = {}
    try {
        result = JSON.parse(fs.readFileSync(FILE_PATH))
    } catch (err) {
        console.error(err)
    }
    return result
}

// dump json object to file
const dumpStats = (stats) => {
    try {
        fs.writeFileSync(FILE_PATH, JSON.stringify(stats), { flag: 'w+' })
    } catch (err) {
        console.error(err)
    }
} 
```

另外，我修改了中间件，将持久性添加到统计数据中

```
app.use((req, res, next) => {
    res.on('finish', () => {
        const stats = readStats()
        const event = `${req.method}  ${getRoute(req)}  ${res.statusCode}`
        stats[event] = stats[event] ? stats[event] + 1 : 1
        dumpStats(stats)
    })
    next()
}) 
```

并创建了返回统计数据的`/stats`方法。

```
app.get('/stats/', (req, res) => {
    res.json(readStats())
}) 
```

我们完成了，让我们提出一些请求并检查统计数据。

```
> curl -X GET  http://localhost:3000/api/
> curl -X POST http://localhost:3000/api/
> curl -X PUT http://localhost:3000/api/
> curl http://localhost:3000/stats/
{
    "GET /api/ 200": 1,
    "POST unknown route 404": 1,
    "PUT unknown route 404": 1
} 
```

如你所见，我们的应用程序中每条路线都有很多请求。这个样本 app 的完整代码可以在 [GitHub](https://gist.github.com/sochix/475c9d6b32780c56db909c72bde0f150) 上找到。

## 结论和下一步

在这篇文章中，我描述了请求计数的基础。将所有数据保存在一个文件中可能无法在生产中正常工作，您应该将它保存在一个不太不稳定的地方，比如数据库。可以是 Redis，InfluxDB，ElasticSearch，MongoDB 等。就个人而言，我们的 Node.js + Express 监控服务 SLAO 使用的是 InfluxDB 集群。

此外，在现实世界中，您希望获得更详细的统计数据，比如每天/每分钟/每小时的请求数，并且能够以比 API 返回的 JSON 更方便的方式查看数据。此外，一个简单的带有统计数据的仪表板是没有用的，除非你给它附加了一个警报。我们稍后将讨论所有这些主题。

* * *

我在建造📊 [SLAO: Node.js +快递监控](https://slao.io)。注册免费试用！
还不确定？只要按🧡为这个职位。

*原贴[此处](https://slao.io/blog/posts/counting-requests/)T3】*
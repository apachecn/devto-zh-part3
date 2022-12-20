# 使用 Node、Express 和 Cube.js 构建分析仪表板

> 原文：<https://dev.to/cubejs/building-an-analytics-dashboard-with-node-express-and-cubejs-1oje>

在下面的教程中，我将向您展示如何使用 Node、Express 和 [Cube.js](https://github.com/cube-js/cube.js) 创建一个基本的分析仪表板。作为数据库，我们将使用 MongoDB 和 MongoDB BI 连接器。如果你不熟悉它，我强烈推荐你阅读[构建 MongoDB 仪表板教程](https://statsbot.co/blog/building-mongodb-dashboard-using-node.js)。它涵盖了设置 Mongo 及其 BI 连接器的基础知识。

这是最终仪表板的外观。你可以在这里查看 Heroku 的现场演示。完整的源代码在 Github 上的[可以找到。](https://github.com/cube-js/cube.js/tree/master/examples/express-analytics-dashboard)

[![](img/fef54116d333877a878455b8ad2a5d04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xuv0hlND--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/hSWmTK9hQgyFh2eISUdL)

## 获取样本数据集

*如果您的仪表板已经有一些数据，您可以跳过这一步*

如果您没有本地 MongoDB 实例，请在这里下载它[。BI 连接器可以在这里](https://www.mongodb.com/download-center/community)下载[。](https://www.mongodb.com/download-center/bi-connector)

Github 上有一个很好的[资源库，里面有一个来自网络的 JSON / BSON 数据集的精选列表，以便在 MongoDB 中练习。我们将为我们的仪表板选择一个](https://github.com/ozlerhakan/mongodb-json-files)[推特数据集](https://github.com/ozlerhakan/mongodb-json-files/blob/master/datasets/tweets.zip)。

[下载测试数据](https://github.com/ozlerhakan/mongodb-json-files/blob/master/datasets/tweets.zip)并使用下面的命令将其导入到您的 MongoDB 目录中。

```
$ bin/mongorestore Your-Downloads-Folder/dump/twitter/tweets.bson 
```

Enter fullscreen mode Exit fullscreen mode

现在确保 MongoDB 和 MongoDB BI 连接器进程都在运行。

```
# Run from MongoDB directory
$ bin/mongod

# Run from MongoDB BI Connector directory
$ bin/mongosqld 
```

Enter fullscreen mode Exit fullscreen mode

## 设置后端

我们将使用一个 [express 应用生成器](https://expressjs.com/en/starter/generator.html)来创建一个应用框架。

```
# Install it if you don’t have it already
$ npm install express-generator -g 
```

Enter fullscreen mode Exit fullscreen mode

接下来，创建一个新的 express 应用程序，将视图引擎设置为 Handlebars (hbs)。

```
$ express --view=hbs express-analytics-dashboard 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用[开源框架 Cube.js](https://cube.dev) 作为我们的分析后端。它生成和执行 SQL 查询，并提供缓存、数据预聚合、安全性和 API 来查询结果和构建可视化。[你可以在这里了解更多。](https://cube.dev)

Cube.js 可以很容易地嵌入到 Express 应用程序中。让我们将它添加到我们的项目依赖项中。

```
$ npm install --save @cubejs-backend/server-core @cubejs-backend/mongobi-driver dotenv 
```

Enter fullscreen mode Exit fullscreen mode

我们为 Cube.js 和 Cube.js MongoBI 驱动程序添加了一个核心服务器包。我们还添加了一个`dotenv`包来管理我们的凭证。让我们用以下凭证创建一个`.env`文件；我们需要他们告诉 Cube.js 如何连接 Mongo。

```
CUBEJS_DB_HOST=localhost
CUBEJS_DB_NAME=twitter
CUBEJS_DB_PORT=3307
CUBEJS_DB_TYPE=mongobi
CUBEJS_API_SECRET=SECRET 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们将 Cube.js 服务器安装到我们的 express 应用程序中。在您的`app.js`中的 routes 声明之后添加以下代码。

```
var CubejsServerCore = require('@cubejs-backend/server-core');
// ...
app.use('/', indexRouter);

require('dotenv').config();
CubejsServerCore.create().initApp(app);
// ... 
```

Enter fullscreen mode Exit fullscreen mode

通过上面两行代码，我们已经从`.env`文件加载了所有需要的配置，并将 Cube.js 装载到我们的 Express 应用程序中。默认情况下，它被挂载到`/cubejs-api/v1/`路径名称空间中。但是您可以通过将[配置对象](https://cube.dev/docs/@cubejs-backend-server-core#api-reference-cubejs-server-core-create-options)传递给`CubejsServerCore.create()`方法来修改它和许多其他东西。我们将保留教程的默认设置。

现在，让我们为 tweets 表创建一个 Cube.js 模式。Cube.js 使用数据模式生成并执行 SQL 你可以在这里了解更多。

创建一个文件夹`schema`，里面有一个`Tweets.js`文件，内容如下。

```
cube(`Tweets`, {
  sql: `select * from tweets`,

  measures: {
    count: {
      type: `count`
    },

    favoriteCount: {
      type: `sum`,
      sql: `favorite_count`
    },

    retweetCount: {
      type: `sum`,
      sql: `retweet_count`
    }
  },

  dimensions: {
    location: {
      type: `string`,
      sql: `\`user.location\``
    },

    lang: {
      type: `string`,
      sql: `lang`
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

在 Cube.js 中，您可以用 Javascript 描述您的查询，然后它们将被编译成 SQL 并在您的数据库中执行。它使用`measures`和`dimensions`作为基本单位来描述各种分析查询。[本教程是开始使用 Cube.js 模式的好地方。](https://cube.dev/docs/getting-started-cubejs-schema)

现在让我们继续在前端构建一个仪表板。

## 备选设置:在无服务器模式下运行 Cube.js

如果你想把它作为一个微服务或者一个无服务器的功能来运行——使用 Cube.js CLI。以下代码显示了如何使用 Cube.js CLI 生成新的 Cube.js 应用程序:

```
$ npm install -g cubejs-cli
$ cubejs create -d mongobi -t serverless 
```

Enter fullscreen mode Exit fullscreen mode

它将创建一个预配置的新项目，使用无服务器框架部署到 AWS Lambda。你可以在这里了解更多关于 [Cube.js 无服务器部署的信息。](https://cube.dev/docs/deployment)

## 构建分析仪表板

我们将使用 Bootstrap 进行造型，使用 Cube.js 客户端加载数据，使用 Chart.js 显示数据。
将`views/index.hbs`的内容替换为以下内容。

```
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
      integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.2/Chart.bundle.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/numeral.js/2.0.6/numeral.min.js"></script>
<script src="https://unpkg.com/@cubejs-client/core@0.6.0/dist/cubejs-client-core.js"></script>

<div class="container" id="app">
  <div class="row">
    <div class="col-md-4">
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">Total Tweets</h5>
                <div class="card-text">
                    <h3 id="total-tweets"></h3>
                </div>
            </div>
        </div>
    </div>
    <div class="col-md-4">
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">Total Retweets</h5>
                <div class="card-text">
                  <h3 id="total-retweets"></h3>
                </div>
            </div>
        </div>
    </div>
    <div class="col-md-4">
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">Total Favorites</h5>
                <div class="card-text">
                  <h3 id="total-favorites"></h3>
                </div>
            </div>
        </div>
    </div>
  </div>
  <br />
  <br />
  <div class="row">
      <div class="col-md-6">
          <div class="card">
              <div class="card-body">
                  <h5 class="card-title">Top Tweets Locations</h5>
                  <div class="card-text">
                    <canvas id="pie-chart"></canvas>
                  </div>
              </div>
          </div>
      </div>
      <div class="col-md-6">
          <div class="card">
              <div class="card-body">
                  <h5 class="card-title">Most Popular Languages</h5>
                  <div class="card-text">
                    <canvas id="bar-chart"></canvas>
                  </div>
              </div>
          </div>
      </div>
  </div>
</div>

<script>
  var cubejsApi = cubejs(
    'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE1NTIzOTk5MjcsImV4cCI6MTU1MjQ4NjMyN30.SOO-A6GfGH7ar3EoeBb0cjj10BVxO3ffjvmqQziXIZA',
    { apiUrl: 'http://localhost:3000/cubejs-api/v1' }
  );

  var kpis = [
    { measure: "Tweets.count", element: "total-tweets" },
    { measure: "Tweets.retweetCount", element: "total-retweets" },
    { measure: "Tweets.favoriteCount", element: "total-favorites" }
  ];

  kpis.forEach(kpi => {
    cubejsApi.load({
      measures: [kpi.measure]
    }).then(resultSet => {
      document.getElementById(kpi.element).textContent =
        numeral(resultSet.totalRow()[kpi.measure]).format('0,0');
    })
  });

  // A helper method to format data for Chart.js
  // and add some nice colors
  var chartJsData = function(resultSet) {
    return {
      datasets: [{
        data: resultSet.series()[0].series.map(function(r) { return r.value }),
        backgroundColor: [
          'rgb(255, 99, 132)',
          'rgb(255, 159, 64)',
          'rgb(255, 205, 86)',
          'rgb(75, 192, 192)',
          'rgb(54, 162, 235)'
        ]
      }],
      labels: resultSet.categories().map(function(c) { return c.category })
    }
  }

  cubejsApi.load({
    measures: ["Tweets.count"],
    dimensions: ["Tweets.location"],
    filters: [
      {
        dimension: "Tweets.location",
        operator: "notEquals",
        values: [""]
      }
    ],
    limit: 5
  }).then(resultSet => {
    new Chart(document.getElementById("pie-chart"), {
      type: 'pie',
      data: chartJsData(resultSet)
    })
  });

  cubejsApi.load({
    measures: ["Tweets.count"],
    dimensions: ["Tweets.lang"],
    limit: 5
  }).then(resultSet => {
    new Chart(document.getElementById("bar-chart"), {
      type: 'bar',
      data: chartJsData(resultSet),
      options: { legend: { display: false } }
    })
  });
</script> 
```

Enter fullscreen mode Exit fullscreen mode

让我们把这个分成几部分。首先，我们正在加载我们需要的库。Cube.js 客户端可以用不同的方式安装，这里我们只是从 CDN 加载一个 UMD 版本。我们还加载了 Bootstrap、Chart.js 和 numeral.js 来格式化来自 CDN 的数字。

```
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
      integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.2/Chart.bundle.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/numeral.js/2.0.6/numeral.min.js"></script>
<script src="https://unpkg.com/@cubejs-client/core@0.6.0/dist/cubejs-client-core.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

下一部分只是带有引导网格的普通 HTML 标记。

最后一部分是我们在仪表板小部件中加载和显示数据的地方。出于本教程的考虑，我们不使用任何前端库。但是，如果你愿意，Cube.js 可以绑定所有流行的前端框架，比如 React。

首先，我们初始化 Cube.js 客户机，并传递 API 令牌和 API URL。您的 API 令牌应该在服务器启动时打印到终端。URL 应该是相同的。

```
var cubejsApi = cubejs(
  'YOUR-API-TOKEN',
  { apiUrl: 'http://localhost:3000/cubejs-api/v1' }
); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将为仪表板的上一行 KPI 部分加载和显示数据。这里我们只显示普通的数字，并由 digital . js .
进行一些格式化

```
var kpis = [
  { measure: "Tweets.count", element: "total-tweets" },
  { measure: "Tweets.retweetCount", element: "total-retweets" },
  { measure: "Tweets.favoriteCount", element: "total-favorites" }
];

kpis.forEach(kpi => {
  cubejsApi
    .load({
      measures: [kpi.measure]
    })
    .then(resultSet => {
      document.getElementById(kpi.element).textContent = numeral(
        resultSet.totalRow()[kpi.measure]
      ).format("0,0");
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

该行有一个饼图和一个条形图，用 Chart.js 绘制。为了显示条形图，我们请求了`Tweets.count`度量并按`Tweets.location`维度对其进行分组。我们还应用了一个过滤器来排除位置为空的推文。最后，我们将限制设置为 5，以便只获取前 5 个位置。

您可以在这里了解有关 Cube.js 查询格式的更多信息。

对于条形图，我们做了类似的分组，但是不是位置，而是按`Tweets.lang`维度分组。

```
// A helper method to format data for Chart.js
// and add some nice colors
var chartJsData = function(resultSet) {
  return {
    datasets: [
      {
        data: resultSet.series()[0].series.map(function(r) {
          return r.value;
        }),
        backgroundColor: [
          "rgb(255, 99, 132)",
          "rgb(255, 159, 64)",
          "rgb(255, 205, 86)",
          "rgb(75, 192, 192)",
          "rgb(54, 162, 235)"
        ]
      }
    ],
    labels: resultSet.categories().map(function(c) {
      return c.category;
    })
  };
};

cubejsApi
  .load({
    measures: ["Tweets.count"],
    dimensions: ["Tweets.location"],
    filters: [
      {
        dimension: "Tweets.location",
        operator: "notEquals",
        values: [""]
      }
    ],
    limit: 5
  })
  .then(resultSet => {
    new Chart(document.getElementById("pie-chart"), {
      type: "pie",
      data: chartJsData(resultSet)
    });
  });

cubejsApi
  .load({
    measures: ["Tweets.count"],
    dimensions: ["Tweets.lang"],
    limit: 5
  })
  .then(resultSet => {
    new Chart(document.getElementById("bar-chart"), {
      type: "bar",
      data: chartJsData(resultSet),
      options: { legend: { display: false } }
    });
  }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，要查看运行中的仪表板，请启动您的服务器。

```
$ npm start 
```

Enter fullscreen mode Exit fullscreen mode

并访问 [http://localhost:3000](http://localhost:3000) 查看您的分析仪表板的运行情况。此外，我们在 Heroku 上有一个应用程序[的现场演示。](https://express-analytics-dashboard.herokuapp.com/) [完整的源代码可以在 Github 上找到。](https://github.com/cube-js/cube.js/tree/master/examples/express-analytics-dashboard)

## 为什么是 Cube.js

为什么用 Cube.js 比直接用 SQL 查询打 MongoDB 好？Cube.js 解决了每个生产就绪的分析应用程序需要解决的大量不同问题:分析 SQL 生成、查询结果缓存和执行编排、数据预聚合、安全性、用于查询结果提取的 API 和可视化。

[![](img/3052955d876b327b1637a4c74fdeefa3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Eiz6sxHm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/statsbotco/cube.js/master/docs/old-was-vs-cubejs-way.png)

这些特性允许您构建能够处理数千个并发用户和数十亿个数据点的生产级分析应用程序。它们还允许您在生产 MongoDB 读取副本甚至 MongoDB 主节点上进行分析，因为它们能够减少向 MongoDB 实例发出的实际查询数量。
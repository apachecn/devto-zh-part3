# 使用 Node.js 构建 MongoDB 仪表板

> 原文：<https://dev.to/cubejs/building-mongodb-dashboard-using-nodejs-409l>

与现代 SQL RDBMS 和与聚合管道和 map-reduce 实践相关的数据仓库相比，使用 MongoDB 进行分析需要额外的开销。虽然这种方法允许设计高级定制聚合算法，但它需要额外的知识来构建和维护它。

为了填补这个空白，MongoDB 发布了 MongoDB connector for BI，它在 MongoDB 数据之上充当 MySQL 服务器。在底层，它将现有的聚合机制连接到 MySQL 协议，允许标准的 MySQL 客户端连接并发出 SQL 查询。

在这个 30 分钟的简短教程中，我们将为您的本地 MongoDB 实例设置 MongoDB connector，并启动 Node.js 应用程序，该应用程序使用 Cube.js 分析框架提供分析 API、查询缓存和编排。Cube.js 可以用作独立的服务器，也可以作为现有 Node.js web 应用程序的一部分嵌入。你可以在这里了解更多信息。

## 为 BI 设置 MongoDB 连接器

要在本地安装 MongoDB connector for BI，您可以使用[快速入门指南](https://docs.mongodb.com/bi-connector/master/local-quickstart/)或其中一个[平台相关安装指南](https://docs.mongodb.com/bi-connector/master/installation/)。

请确保您使用的 MongoDB 版本支持 MongoDB BI 连接器。在本教程中，我们使用 4.0.5。

如果您没有本地 MongoDB 实例，请在此下载它[。
BI 连接器可以在这里](https://www.mongodb.com/download-center/community)下载[。](https://www.mongodb.com/download-center/community)

安装 BI 连接器后，请先启动`mongod`实例。如果你使用下载安装，它可以从主目录启动，比如:

```
$ bin/mongod 
```

Enter fullscreen mode Exit fullscreen mode

BI 连接器本身也可以同样的方式启动:

```
$ bin/mongosqld 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`mongosqld`位于另一个`bin`目录中。
如果一切正常，您应该会在您的 shell 中看到`mongosqld`进程:
的成功日志消息

```
[initandlisten] waiting for connections at 127.0.0.1:3307 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在使用 MongoDB Atlas，你可以使用本指南来启用 BI 连接器。

## 导入测试数据集

如果数据库中已经有可以分析的数据，可以跳过这一步。否则，您可以使用 MongoDB 中的邮政编码测试数据集，用一些测试数据填充您的 DB。

从 MongoDB 主目录
下载 [zips.json](http://media.mongodb.org/zips.json) 并运行`mongoimport`

```
$ bin/mongoimport --db test --collection zips --file <path/to/zips.json> 
```

Enter fullscreen mode Exit fullscreen mode

请确保重新启动 MongoDB BI 连接器实例，以便从刚刚添加的集合中生成最新的 MySQL 模式。

## 旋转起来的 Cube.js 应用

我们将使用 [Cube.js](https://github.com/statsbotco/cube.js) 对我们的 MongoDB 实例进行分析查询。要安装其 CLI 运行:

```
$ npm install -g cubejs-cli 
```

Enter fullscreen mode Exit fullscreen mode

要使用 MySQL 驱动程序创建新的 Cube.js 应用程序，请运行:

```
$ cubejs create mongo-tutorial -d mysql 
```

Enter fullscreen mode Exit fullscreen mode

转到刚刚创建的`mongo-tutorial`目录并编辑刚刚创建的`.env`文件:用您的 MongoDB BI 连接器凭证替换占位符。

默认情况下，它应该类似于:

```
CUBEJS_DB_HOST=localhost
CUBEJS_DB_NAME=test
CUBEJS_DB_PORT=3307
CUBEJS_DB_TYPE=mysql
CUBEJS_API_SECRET=941ed7ad8a49bec1b3f87a9f48bff2a5e549e946fc22fcc5f18c3a17bf62c64ed3398e99b271cd66d4521a6fd7caa4bfd268dfccea59ffd9c67de660f3967338 
```

Enter fullscreen mode Exit fullscreen mode

现在为来自测试数据集的`zips`集合或者您自己的集合:
生成 Cube.js 模式文件

```
$ cd mongo-tutorial
$ cubejs generate -t zips 
```

Enter fullscreen mode Exit fullscreen mode

为了启动 Cube.js dev 服务器，您还需要本地运行 Redis 实例，该实例用于缓存和查询队列编排。你可以下载它并使用这些指令运行。

如果一切顺利，你就可以运行 Cube.js 开发服务器:

```
$ npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

如果服务器启动成功，您现在可以打开`http://localhost:4000`并导航到 Cube.js 开发环境。在这里，您应该可以看到饼图的工作示例。

## 构建仪表盘

Cube.js 开发环境示例包含构建分析仪表板的所有基本客户端部分。让我们稍微修改一下，让它看起来更像一个仪表板，并使用 zips 集合。

替换开发环境代码沙箱中`index.js`的内容:

```
import React from "react";
import ReactDOM from "react-dom";

import "antd/dist/antd.css";
import { Row, Col, Card, Layout, Spin } from "antd";

import cubejs from "@cubejs-client/core";
import { QueryRenderer } from "@cubejs-client/react";
import { Chart, Axis, Tooltip, Geom, Coord, Legend } from "bizcharts";

const renderChart = resultSet => (
  <Chart height={400} data={resultSet.chartPivot()} forceFit>
    <Coord type="theta" radius={0.75} />
    <Axis name="Zips.count" />
    <Legend position="bottom" name="category" />
    <Tooltip showTitle={false} />
    <Geom type="intervalStack" position="Zips.count" color="x" />
  </Chart> );

const cubejsApi = cubejs(
  "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE1NDkzMDk3NzMsImV4cCI6MTU0OTM5NjE3M30.eXEdfUa_ek2V9MlGTpBMOd_AFfs8laaZj8ZsuM1wqqo",
  { apiUrl: "http://localhost:4000/cubejs-api/v1" }
);

const { Header, Footer, Sider, Content } = Layout;

const App = () => (
  <Layout>
    <Header>
      <h2 style={{ color: "#fff" }}>MongoDB Dashboard</h2>
    </Header>
    <Content style={{ padding: "25px", margin: "25px" }}>
      <Row type="flex" justify="space-around" align="top" gutter={24}>
        <Col span={24} lg={12}>
          <Card title="Zip count by state" style={{ marginBottom: "24px" }}>
            <QueryRenderer
              query={{ measures: ["Zips.count"], dimensions: ["Zips.state"] }}
              cubejsApi={cubejsApi}
              render={({ resultSet, error }) =>
               (resultSet && renderChart(resultSet)) ||
(error && error.toString()) || <Spin />
              }
              />
          </Card>
        </Col>
      </Row>
    </Content>
  </Layout> );

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

请确保用您自己的 Cube.js 临时令牌替换 auth 令牌，该令牌在服务器启动时打印在控制台中。点击阅读更多关于认证令牌[的信息。](https://github.com/statsbotco/cube.js#security)

如果一切正常，您应该会看到以下仪表板:

[![](img/8483cf536ea66f02aa9bbb53669c4ce3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--57ja1ZO8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/oORb3acEQ7ahiKaQ4onH)

## 为什么是 Cube.js？

那么为什么使用 Cube.js 比直接对 MongoDB 打 SQL 查询更好呢？Cube.js 解决了每个生产就绪分析应用程序需要解决的大量不同问题:分析 SQL 生成、查询结果缓存和执行编排、数据预聚合、安全性、用于查询结果提取和可视化的 API。

这些特性允许构建能够处理数千个并发用户和数十亿个数据点的生产级分析应用程序。由于能够减少向 MongoDB 实例发出的实际查询量，它还允许您在生产 MongoDB 读取副本甚至 MongoDB 主节点上进行分析。Cube.js 模式还允许对从简单计数到漏斗和群组保持分析的一切进行建模。你可以在这里了解更多信息。

## 性能考虑

为了能够处理大量的数据，Cube.js 严重依赖预聚合。到目前为止，MongoDB BI 连接器还不支持 Create Table as 语句，该语句是在数据库中物化查询结果和创建预聚合所必需的。如果您需要在 MongoDB 中分析超过 100 兆的数据点，请考虑将 [Presto](https://prestodb.github.io/) 与 [MongoDB 连接器](https://prestodb.github.io/docs/current/connector/mongodb.html)一起使用，后者也受 Cube.js 支持
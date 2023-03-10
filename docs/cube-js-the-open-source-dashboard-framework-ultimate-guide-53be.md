# Cube.js,《开源仪表板框架:终极指南》

> 原文：<https://dev.to/cubejs/cube-js-the-open-source-dashboard-framework-ultimate-guide-53be>

[Cube.js](https://cube.dev) 是一个用于构建分析 web 应用程序的开源框架。它主要用于构建内部商业智能工具或向现有应用程序添加面向客户的分析。在大多数情况下，构建这种应用程序的第一步是分析仪表板。它通常以“让我们在管理面板中添加一个分析仪表板”开始然后，正如软件开发中经常发生的那样，事情变得越来越复杂。

当我们开始开发 Cube.js 时，我们希望构建一个工具，它开始简单，但在功能、复杂性和数据量方面很容易扩展。Cube.js 为您未来的分析系统打下了坚实的基础，无论它是独立的应用程序还是嵌入到现有的应用程序中。

你可以把这个教程想象成**“cube . js 101。”**我将向您介绍从数据库到可视化设计第一个仪表板的基本步骤。

*[最终仪表板的现场演示可在此处获得。](https://statsbotco.github.io/cubejs/react-dashboard/) [完整的源代码在 Github 上。](https://github.com/cube-js/cube.js/tree/master/examples/react-dashboard)*

## 建筑

大多数现代 web 应用程序都是作为[单页面应用程序](https://en.wikipedia.org/wiki/Single-page_application)构建的，前端与后端是分离的。后端通常也分成多个服务，遵循一个[微服务架构](https://en.wikipedia.org/wiki/Microservices)。

Cube.js 采用了这种方法。按照惯例，您将 Cube.js 后端作为服务运行。它管理到数据库的连接，包括查询队列、缓存、预聚合等等。它还为您的前端应用程序提供了一个 API 来构建仪表板和其他分析功能。

[![](img/d9217e82e03091bb807beb327fbce34c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2S8v9vcf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/WWFpTcUsTJaeOwTdH2iR)

## 后端

分析从数据开始，数据驻留在数据库中。这是我们首先需要做的事情。您很可能已经为您的应用程序建立了一个数据库，并且通常，它可以很好地用于分析。Postgres 或 MySQL 等现代流行数据库非常适合简单的分析工作负载。简单来说，我指的是少于 10 亿行的数据量。

MongoDB 也很好，您唯一需要添加的是 MongoDB BI 连接器。它允许在 MongoDB 数据上执行 SQL 代码。它是免费的，可以很容易地从 MongoDB 网站下载。还要记住的一点是复制。对生产数据库运行分析查询被认为是一种不好的做法，主要是因为性能问题。Cube.js 可以极大地减少数据库的工作量，但是我仍然建议连接到副本。

总结一下—
如果你使用 Postgres 或 MySQL，只需创建一个副本，我们就可以开始了。如果您使用 MongoDB—下载 MongoDB Connector for BI 并创建一个副本。

如果您没有仪表板的任何数据，您可以加载我们的示例电子商务 Postgres 数据集。

```
$ curl http://cube.dev/downloads/ecom-dump.sql > ecom-dump.sql
$ createdb ecom
$ psql --dbname ecom -f ecom-dump.sql 
```

Enter fullscreen mode Exit fullscreen mode

现在，数据库中有了数据，我们准备创建 Cube.js 后端服务。在您的终端中运行以下命令:

```
$ npm install -g cubejs-cli
$ cubejs create dashboard-backend -d postgres 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令安装 Cube.js CLI 并创建一个新服务，配置为使用 Postgres 数据库。

Cube.js 使用环境变量进行配置。它使用以`CUBEJS_`开始的环境变量。要配置到数据库的连接，我们需要指定数据库类型和名称。在 Cube.js 项目文件夹中，用以下内容替换`.env`的内容:

```
CUBEJS_API_SECRET=SECRET
CUBEJS_DB_TYPE=postgres
CUBEJS_DB_NAME=ecom 
```

Enter fullscreen mode Exit fullscreen mode

## Cube.js 数据模式

下一步是创建一个 [Cube.js 数据模式](https://cube.dev/docs/getting-started-cubejs-schema)。Cube.js 使用数据模式生成 SQL 代码，该代码将在您的数据库中执行。数据模式不能替代 SQL。它旨在使 SQL 可重用，并在保留其所有功能的同时为其提供一个结构。数据模式的基本元素是`measures`和`dimensions`。

**Measure** 指的是量化数据，比如售出的单元数、独立访问量、利润等等。

**维度**是指分类数据，如州、性别、产品名称或时间单位(如日、周、月)。

通常，模式文件位于`schema`文件夹中。下面是一个模式的例子，它可以用来描述用户的数据。

```
cube(`Users`, {
  sql: `SELECT * FROM users`,

  measures: {
    count: {
      sql: `id`,
      type: `count`
    }
  },

  dimensions: {
    city: {
      sql: `city`,
      type: `string`
    },

    signedUp: {
      sql: `created_at`,
      type: `time`
    },

    companyName: {
      sql: `company_name`,
      type: `string`
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，有了上面的模式，我们可以向 Cube.js 后端发送关于用户数据的查询。Cube.js 查询是普通的 javascript 对象。通常它有一个或多个`measures`、`dimensions`、`timeDimensions`。

如果我们想回答“我们的用户来自哪里？”我们可以向 Cube.js 发送以下查询:

```
{
   measures: ['Users.count'],
   dimensions: ['Users.city']
} 
```

Enter fullscreen mode Exit fullscreen mode

Cube.js 将根据模式生成所需的 SQL，执行它，并将结果发送回来。

让我们创建一个稍微复杂一点的查询。我们可以添加一个`timeDimensions`来查看去年每个月不同城市的比例是如何变化的。为此，我们将添加一个`signedUp`时间维度，按月分组，并只过滤去年的注册。

```
{
   measures: ['Users.count'],
   dimensions: ['Users.city'],
   timeDimensions: [{
     dimension: 'Users.signedUp',
     granularity: 'month',
     dateRange: ['2018-01-31', '2018-12-31']
   }]
} 
```

Enter fullscreen mode Exit fullscreen mode

Cube.js 可以基于数据库的表生成简单的模式。让我们生成仪表板所需的模式，然后启动一个开发服务器。

```
$ cubejs generate -t users,orders
$ npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过打开位于 [http://localhost:4000](http://localhost:4000) 的开发平台来检查生成的模式并发送测试查询。

## 前端

我们将使用 Cube.js React 客户端，用 React 构建我们的前端和仪表板。但是你可以使用任何框架或者只是普通的 javascript 来用 Cube.js 构建一个前端。
我们将使用由 React 团队官方支持的 Create React App 来设置一切。它打包了 React app 的所有依赖项，并使新项目的开始变得容易。在您的终端中运行以下命令:

```
$ npx create-react-app dashboard-frontend
$ cd cubejs-dashboard
$ npm start 
```

Enter fullscreen mode Exit fullscreen mode

最后一行启动端口 3000 上的服务器，并在 [http://localhost:3000](http://localhost:3000) 打开您的 web 浏览器。

我们将使用 Reactstrap 构建我们的 UI，React strap 是 Bootstrap 4 的 React 包装器。安装来自 NPM 的电抗器和自举装置。Reactstrap 不包含 Bootstrap CSS，所以这个需要单独安装:

```
$ npm install reactstrap bootstrap --save 
```

Enter fullscreen mode Exit fullscreen mode

在导入`./index.css` :
之前，先导入`src/index.js`文件中的引导 CSS

```
import 'bootstrap/dist/css/bootstrap.min.css'; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经准备好使用反应阱组件了。

下一步是安装 Cube.js 客户端，从服务器和我们的可视化库获取数据并显示出来。对于本教程，我们将使用 Recharts。Cube.js 是可视化不可知的，这意味着您可以使用任何想要的库。我们还将使用矩和数字来很好地格式化日期和数字。

```
$ npm install --save @cubejs-client/core @cubejs-client/react recharts moment numeral 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们完成了依赖项，所以让我们继续创建我们的第一个图表。
将`src/App.js`的内容替换为以下内容:

```
import React, { Component } from "react";
import {
 BarChart,
 Bar,
 XAxis,
 YAxis,
 Tooltip,
 ResponsiveContainer
} from "recharts";
import cubejs from "@cubejs-client/core";
import moment from "moment";
import { QueryRenderer } from "@cubejs-client/react";

const cubejsApi = cubejs(process.env.REACT_APP_CUBEJS_TOKEN, {
 apiUrl: process.env.REACT_APP_API_URL
});

const dateFormatter = item => moment(item).format("MMM YY");

class App extends Component {
 render() {
   return (
     <QueryRenderer
       query={{
         measures: ["Orders.count"],
         timeDimensions: [
           {
             dimension: "Orders.createdAt",
             dateRange: ["2017-01-01", "2018-12-31"],
             granularity: "month"
           }
         ]
       }}
       cubejsApi={cubejsApi}
       render={({ resultSet }) => {
         if (!resultSet) {
           return "Loading...";
         }

         return (
           <ResponsiveContainer width="100%" height={300}>
             <BarChart data={resultSet.chartPivot()}>
               <XAxis dataKey="x" tickFormatter={dateFormatter} />
               <YAxis />
               <Tooltip labelFormatter={dateFormatter} />
               <Bar dataKey="Orders.count" fill="rgba(106, 110, 229)" />
             </BarChart>
           </ResponsiveContainer>
         );
       }}
     />
   );
 }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

你可以在下面的 CodeSandbox 中查看这个例子。

[https://codesandbox.io/embed/xv0v19l1pp](https://codesandbox.io/embed/xv0v19l1pp)

让我们更深入地看看如何加载数据和绘制图表。

首先，我们正在初始化 Cube.js API 客户端:

```
const cubejsApi = cubejs(process.env.REACT_APP_CUBEJS_TOKEN, {
 apiUrl: process.env.REACT_APP_API_URL
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用了`REACT_APP_CUBEJS_TOKEN`和`REACT_APP_API_URL`环境变量。如果环境变量以`REACT_APP_`开头，Create React App 会自动从`.env`文件中加载它们。Cube.js 后端将在启动时打印开发 API 令牌。

[![](img/20d7fd9c102ca3d9331996156b7ec19f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nxPENh3F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/e3BEqwLRzqfodnUkgWzw)

用正确的凭证创建一个`.env`文件。

```
REACT_APP_CUBEJS_TOKEN=COPY-API-TOKEN-FROM-TERMINAL-OUTPUT
REACT_APP_API_URL=http://localhost:4000/cubejs-api/v1 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将使用 QueryRenderer Cube.js React 组件加载订单数据。

```
<QueryRenderer
  query={{
    measures: ["Orders.count"],
    timeDimensions: [
      {
        dimension: "Orders.createdAt",
        dateRange: ["2017-01-01", "2018-12-31"],
        granularity: "month"
      }
    ]
  }}
  cubejsApi={cubejsApi}
  render={({ resultSet }) => {
    // Render result
  }}
/> 
```

Enter fullscreen mode Exit fullscreen mode

QueryRenderer 对 Cube.js 后端执行一个 API 请求，并使用 [render props](https://reactjs.org/docs/render-props.html) 技术让您以您想要的方式呈现结果。我们已经讨论了上面的查询格式，但是如果你想更新，这里是[查询格式的完整参考。](https://cube.dev/docs/query-format)

QueryRenderer 的`render`参数是类型`({error, resultSet, isLoading}) => React.Node`的函数。该函数的输出将由 QueryRenderer 呈现。`resultSet`是一个包含从查询中获得的数据的对象。如果未定义该对象，则意味着仍在获取数据。

`resultSet`提供了多种数据操作方法，但是在我们的例子中，我们只需要`chartPivot`方法，它以 Recharts 期望的格式返回数据。

我们将把订单数据绘制成一个响应容器中的条形图。

```
if (!resultSet) {
  return "Loading...";
}

return (
  <ResponsiveContainer width="100%" height={300}>
    <BarChart data={resultSet.chartPivot()}>
      <XAxis dataKey="x" tickFormatter={dateFormatter} />
      <YAxis />
      <Tooltip labelFormatter={dateFormatter} />
      <Bar dataKey="Orders.count" fill="rgba(106, 110, 229)" />
    </BarChart>
  </ResponsiveContainer> ); 
```

Enter fullscreen mode Exit fullscreen mode

## 构建仪表板

我们学习了如何用 Cube.js 和 Recharts 构建单个图表，现在我们准备开始构建整个仪表板。有一些关于设计仪表板布局的最佳实践。通常的做法是将最重要和最高级的指标作为单值图表放在顶部，有时称为[KPI](https://en.wikipedia.org/wiki/Performance_indicator)，然后列出这些指标的相关细分。

这是我们最终控制面板的屏幕截图，顶部是 KPI，后面是条形图和折线图。

[![](img/0c01ba90c203590153942d5ac17b384f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VWl3ua3f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/RwQuJwkpSm23UqhxbixV)

首先，让我们重构图表，并将公共代码提取到一个可重用的`<Chart />`组件中。创建一个如下内容的`src/Chart.js`文件:

```
import React from "react";
import { Card, CardTitle, CardBody, CardText } from "reactstrap";
import { QueryRenderer } from "@cubejs-client/react";

const Chart = ({ cubejsApi, title, query, render }) => (
 <Card>
   <CardBody>
     <CardTitle tag="h5">{title}</CardTitle>
     <CardText>
       <QueryRenderer
         query={query}
         cubejsApi={cubejsApi}
         render={({ resultSet }) => {
           if (!resultSet) {
             return <div className="loader" />;
           }

           return render(resultSet);
         }}
       />
     </CardText>
   </CardBody>
 </Card> );

export default Chart; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们使用这个组件来创建仪表板。将`src/App.js`的内容替换为:

```
import React, { Component } from "react";
import { Container, Row, Col } from "reactstrap";
import {
 AreaChart,
 Area,
 XAxis,
 YAxis,
 Tooltip,
 ResponsiveContainer,
 Legend,
 BarChart,
 Bar
} from "recharts";
import moment from "moment";
import numeral from "numeral";
import cubejs from "@cubejs-client/core";
import Chart from "./Chart.js";

const cubejsApi = cubejs(process.env.REACT_APP_CUBEJS_TOKEN, {
 apiUrl: process.env.REACT_APP_API_URL
});
const numberFormatter = item => numeral(item).format("0,0");
const dateFormatter = item => moment(item).format("MMM YY");

const renderSingleValue = (resultSet, key) => (
 <h1 height={300}>{numberFormatter(resultSet.chartPivot()[0][key])}</h1> );

class App extends Component {
 render() {
   return (
     <Container fluid>
       <Row>
         <Col sm="4">
           <Chart
             cubejsApi={cubejsApi}
             title="Total Users"
             query={{ measures: ["Users.count"] }}
             render={resultSet => renderSingleValue(resultSet, "Users.count")}
           />
         </Col>
         <Col sm="4">
           <Chart
             cubejsApi={cubejsApi}
             title="Total Orders"
             query={{ measures: ["Orders.count"] }}
             render={resultSet => renderSingleValue(resultSet, "Orders.count")}
           />
         </Col>
         <Col sm="4">
           <Chart
             cubejsApi={cubejsApi}
             title="Shipped Orders"
             query={{
               measures: ["Orders.count"],
               filters: [
                 {
                   dimension: "Orders.status",
                   operator: "equals",
                   values: ["shipped"]
                 }
               ]
             }}
             render={resultSet => renderSingleValue(resultSet, "Orders.count")}
           />
         </Col>
       </Row>
       <br />
       <br />
       <Row>
         <Col sm="6">
           <Chart
             cubejsApi={cubejsApi}
             title="New Users Over Time"
             query={{
               measures: ["Users.count"],
               timeDimensions: [
                 {
                   dimension: "Users.createdAt",
                   dateRange: ["2017-01-01", "2018-12-31"],
                   granularity: "month"
                 }
               ]
             }}
             render={resultSet => (
               <ResponsiveContainer width="100%" height={300}>
                 <AreaChart data={resultSet.chartPivot()}>
                   <XAxis dataKey="category" tickFormatter={dateFormatter} />
                   <YAxis tickFormatter={numberFormatter} />
                   <Tooltip labelFormatter={dateFormatter} />
                   <Area
                     type="monotone"
                     dataKey="Users.count"
                     name="Users"
                     stroke="rgb(106, 110, 229)"
                     fill="rgba(106, 110, 229, .16)"
                   />
                 </AreaChart>
               </ResponsiveContainer>
             )}
           />
         </Col>
         <Col sm="6">
           <Chart
             cubejsApi={cubejsApi}
             title="Orders by Status Over time"
             query={{
               measures: ["Orders.count"],
               dimensions: ["Orders.status"],
               timeDimensions: [
                 {
                   dimension: "Orders.createdAt",
                   dateRange: ["2017-01-01", "2018-12-31"],
                   granularity: "month"
                 }
               ]
             }}
             render={resultSet => {
               return (
                 <ResponsiveContainer width="100%" height={300}>
                   <BarChart data={resultSet.chartPivot()}>
                     <XAxis tickFormatter={dateFormatter} dataKey="x" />
                     <YAxis tickFormatter={numberFormatter} />
                     <Bar
                       stackId="a"
                       dataKey="shipped, Orders.count"
                       name="Shipped"
                       fill="#7DB3FF"
                     />
                     <Bar
                       stackId="a"
                       dataKey="processing, Orders.count"
                       name="Processing"
                       fill="#49457B"
                     />
                     <Bar
                       stackId="a"
                       dataKey="completed, Orders.count"
                       name="Completed"
                       fill="#FF7C78"
                     />
                     <Legend />
                     <Tooltip />
                   </BarChart>
                 </ResponsiveContainer>
               );
             }}
           />
         </Col>
       </Row>
     </Container>
   );
 }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

这足以构建我们的第一个仪表板。在下面的 CodeSanbox 中尝试一下。

[https://codesandbox.io/embed/71nz8w20qj](https://codesandbox.io/embed/71nz8w20qj)

## 下一步

我们已经用 Cube.js 建立了一个简单的概念验证仪表板。你可以在这里查看的现场演示。《T2》的完整源代码可以在 Github 上获得。

要了解更多关于 Cube.js 后端部署的信息，可以参考[部署文档。](https://cube.dev/docs/deployment)此外，在这里你可以找到关于各种主题的[更多教程](https://github.com/cube-js/cube.js#tutorials)。

并加入我们的 [Slack 社区](https://publicslack.com/slacks/cubejs/invites/new)！这是一个获得帮助和了解最新版本的好地方。
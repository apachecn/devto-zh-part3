# 从头开始构建无服务器 Google Analytics

> 原文：<https://dev.to/cubejs/building-serverless-google-analytics-from-scratch-j8m>

从工程的角度来看，谷歌分析背后的技术在创建时是相当复杂的。为事件收集、采样、聚合和存储输出实施了定制的算法，以用于报告目的。当时，运送这样一个软件需要数年的工程时间。从那时起，大数据格局发生了巨大变化。在本教程中，我们将重建一个完整的谷歌分析管道。我们将从数据收集和报告开始。通过使用最新的大数据技术，我们将看到如今复制这样的软件是多么简单。

## TL；速度三角形定位法(dead reckoning)

[这是一个嵌入了跟踪代码的分析仪表板](http://aws-web-analytics-dashboard.s3-website-us-east-1.amazonaws.com/)，它收集访问者的数据，同时将其可视化。

在 GitHub 上查看[源代码。喜欢就给它一颗星吧！](https://github.com/statsbotco/cubejs-client/tree/master/examples/aws-web-analytics)

## 谷歌分析的工作原理

如果你熟悉 Google Analytics，你可能已经知道 GA 跟踪的每个网页都包含一个 [GA 跟踪代码](https://support.google.com/analytics/answer/1008080?hl=en#snippet)。它会加载一个异步脚本，如果用户还没有设置跟踪 cookie，该脚本会为用户分配一个跟踪 cookie。它还为每个用户交互发送一个 XHR，比如页面加载。这些 XHR 请求随后被处理，原始事件数据被存储并被安排用于聚集处理。根据传入请求的总量，还将对数据进行采样。

尽管这是 Google Analytics essentials 的高级概述，但足以重现大部分功能。让我告诉你怎么做。

## 您自己的 GA 架构概述

实现后端有很多方法。我们将选择无服务器路线，因为网络分析最重要的是可扩展性。在这种情况下，您的事件处理管道与负载成比例扩展。就像谷歌分析一样。

在本教程中，我们将坚持使用 Amazon Web Services。谷歌云平台也可以使用，因为他们有非常相似的产品。这是我们将要构建的 web 分析后端的一个示例架构。

[![](img/793622aa8744c5a0f56a3cac832d9e49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fqh210Cl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/759QfYqGS9KM09J6I8Ru)

为了简单起见，我们只收集页面视图事件。页面视图事件的旅程始于访问者的浏览器，在那里向 API 网关发起 XHR 请求。请求事件然后被传递到 Lambda，在那里事件数据被处理并被写入 Kinesis 数据流。Kinesis Firehose 使用 Kinesis 数据流作为输入，并将处理后的拼花文件写入 S3。Athena 用于直接从 S3 查询拼花地板文件。Cube.js 将生成 SQL 分析查询，并提供用于在浏览器中查看分析的 API。

这乍一看似乎非常复杂，但是组件分解是关键。它允许我们建立可扩展的和可靠的系统。让我们开始实现数据收集。

## 使用 AWS Lambda 构建事件集合

为了部署数据收集后端，我们将使用[无服务器应用框架](https://serverless.com/)。它让你开发无服务器的应用程序，对云提供商的代码依赖性最小。在我们开始之前，请确保 Node.js 已安装在您的计算机上。此外，如果你还没有 AWS 账户，你需要[免费注册](https://aws.amazon.com/)和[安装和配置 AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) 。

要安装无服务器框架 CLI，让我们运行:

```
# Step 1\. Install serverless globally
$ npm install serverless -g
# Step 2\. Login to your serverless account
$ serverless login 
```

Enter fullscreen mode Exit fullscreen mode

现在从 Node.js 模板创建事件收集服务:

```
$ serverless create -t aws-nodejs -n event-collection 
```

Enter fullscreen mode Exit fullscreen mode

这将搭建整个目录结构。让我们将`cd`添加到创建的目录中，并添加`aws-sdk`依赖项:

```
$ yarn add aws-sdk 
```

Enter fullscreen mode Exit fullscreen mode

没有的话安装纱包管理器:

```
$ npm i -g yarn 
```

Enter fullscreen mode Exit fullscreen mode

我们需要用下面的代码片段更新`handler.js`:

```
const AWS = require('aws-sdk');
const { promisify } = require('util');

const kinesis = new AWS.Kinesis();

const putRecord = promisify(kinesis.putRecord.bind(kinesis));

const response = (body, status) => {
  return {
    statusCode: status || 200,
    body: body && JSON.stringify(body),
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Credentials': true,
      'Content-Type': 'application/json'
    }
  }
}

module.exports.collect = async (event, context) => {
  const body = JSON.parse(event.body);
  if (!body.anonymousId || !body.url || !body.eventType) {
    return response({
      error: 'anonymousId, url and eventType required'
    }, 400);
  }

  await putRecord({
    Data: JSON.stringify({
      anonymous_id: body.anonymousId,
      url: body.url,
      event_type: body.eventType,
      referrer: body.referrer,
      timestamp: (new Date()).toISOString(),
      source_ip: event.requestContext.identity.sourceIp,
      user_agent: event.requestContext.identity.userAgent
    }) + '\n',
    PartitionKey: body.anonymousId,
    StreamName: 'event-collection'
  });

  return response();
}; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这个简单函数唯一做的事情就是将记录写入名为`event-collection`的 Kinesis 数据流。请注意，我们正在以新行分隔的 JSON 格式编写数据，以便 Athena 和 Kinesis Firehose 能够理解它。

此外，我们需要修改`serverless.yml`来部署一切。将此粘贴到您的`serverless.yml`文件中。

```
service: event-collection

provider:
  name: aws
  runtime: nodejs8.10

  iamRoleStatements:
    - Effect: "Allow"
      Action:
        - "kinesis:PutRecord"
      Resource:
        - "*"

functions:
  collect:
    handler: handler.collect
    events:
      - http:
          path: collect
          method: post
          cors: true 
```

Enter fullscreen mode Exit fullscreen mode

该配置将部署`collect`函数，并为其分配一个 API 网关事件触发器。它还会将 AWS Kinesis 数据流权限分配给该函数。

至此，我们已经完成了自制 GA 所需的所有后端代码的编写。它将能够每秒处理数以千计的输入事件。2018 年太多了吧？:)

让我们将它部署到 AWS:

```
$ serverless deploy -v 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，您将获得一个 URL 端点。我们用 CURL 来测试一下:

```
curl -d '{}' https://<your_endpoint_url_here>/dev/collect 
```

Enter fullscreen mode Exit fullscreen mode

它应该返回一个`400`状态代码和一个类似于
的错误消息

```
{"error":"anonymousId, url and eventType required"} 
```

Enter fullscreen mode Exit fullscreen mode

如果是这种情况，让我们继续进行 Kinesis 设置。

## AWS Kinesis 设置

首先，我们需要创建一个名为`event-collection`的 Kinesis 数据流。首先，在 console.aws.amazon.com[登录你的 AWS 账户，从菜单中选择 Kinesis 服务。默认情况下，无服务器框架将资源部署到`us-east-1`区域，所以我们假设 AWS Lambda 函数是在那里创建的，如果有必要，在创建流之前切换区域。](https://console.aws.amazon.com/)

为了创建数据流，我们需要将名称设置为`event-collection`并设置碎片的数量。现在可以设置为 1。碎片的数量定义了您的事件收集吞吐量。你可以在这里找到更多关于它的信息。

一旦你完成了数据流，创建一个 Kinesis 消防水带传输流。

### 第一步

您应该选择`event-collection` Kinesis 流作为源。

[![](img/d8fd8499e516b3a7f3288fa57bc66fee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fa4xglAl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/LYroWWAmThmJKMA3a813)

### 第二步

现在，为了使本教程简单，我们不需要处理任何数据。在生产中，您需要将其转换为半兽人或拼花地板，以确保最佳性能。您还可以将此步骤用于事件数据填充，如 IP 到位置。

[![](img/39b5111be232b48e95ef4988c5b38fb0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LqatQT1A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/D2BvUB9S8ym01aTKp4eA)

### 第三步

我们将把 S3 作为目的地。您需要创建一个新的 S3 存储桶。请选择您喜欢的名称，但要加上一个`events`后缀，因为它将包含事件。

[![](img/f9d82862c14f1b4a27967c6c9599e66e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TcPvX5Bl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/R6eFTTs3Ss6j238xHuMm)

### 第四步

这里你可以选择 Gzip 压缩来节省一些账单。还会提示您为此交付流创建 IAM 角色。请按照说明操作。

[![](img/95aa4a5e774f8f2c2195939c0e3c327d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kxxSQfbJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/q3qoXSY9RLy2mqui4CFs)

就是这样。如果你做的一切都是正确的，试着用一个真正的负载再次运行你的 AWS Lambda 函数。

```
curl -d '{"anonymousId": "123", "url": "-", "eventType": "pageView"}' https://<your_endpoint_url_here>/dev/collect 
```

Enter fullscreen mode Exit fullscreen mode

事件应该在五分钟内开始流向你的 S3 桶。至此，事件收集完成。让我们设置分析查询。

## 为查询分析设置 AWS Athena

随着数据开始流向 S3，我们需要用元数据来支持它。Athena 使用它来了解在哪里可以找到数据以及数据的结构。这是一个繁琐的过程，但使用 AWS Glue 可以轻松完成。Glue 是 AWS 的元数据管理器和 ETL。它还有一个 crawler 概念，作为一个 cron 作业来分析 S3 数据，以便从中提取元数据。

从服务菜单中导航到 Glue，然后选择数据库。添加一个新数据库，并将其命名为`aws_web_analytics`。然后转到爬网程序并选择“添加爬网程序”。

将其命名为`events-crawler`，并选择刚刚创建的 S3 存储桶作为数据存储:

[![](img/c5765ebd0a9fe395851bc1fe3153c100.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mP-UXP5Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/C2s8qeBxRPOlKiV3BtkN)

根据说明创建一个 IAM 角色，并设置为每小时运行一次:

[![](img/10b7c9dd14d29d80c40d759afa7408a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eseO7fox--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/xkAqxJYsRqSu1NzajGbH)

作为输出，选择以前创建的数据库:

[![](img/2662878bcd7188702df3a8c833533dd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nB9ORW9_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/Cc8L7OSaneJ1j3EuJFgw)

创建完成后，让我们手动运行它。如果成功，您应该会在`aws_web_analytics`数据库中看到一个表。我们来试着查询一下。

从服务菜单转到 Athena。选择`aws_web_analytics`数据库，编写一些简单的查询，比如`select * from aws_web_analytics_event_collection`。您应该会得到这样的结果:

[![](img/7348f408a03cf76569e33a41674ddf67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T6fOBM_E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/K3ofNBKfTH7gxxJOJe8R)

如果一切正常，我们可以继续构建分析 UI。

## 设置 Cube.js 为最终用户提供分析

AWS Athena 是一个非常好的分析后端，适合查询数 Pb 的数据，但与任何大数据后端一样，它不适合最终用户直接查询。为了提供可接受的性能与成本平衡，您应该在其上使用缓存和预聚合层，以及用于查询分析的 API。这正是 Cube.js 所做的！

首先，我们需要创建一个 IAM 用户来从 Cube.js 访问 Athena。选择用户，然后单击添加用户按钮。将用户名设置为`cubejs`并启用编程访问:

[![](img/63500c200b1aa6eeae86a2af35d0e56c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XWkOz0Pt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/jUnVI38T8yP24QpF2SDr)

在第 2 步中，选择直接附加现有策略，然后选择 AmazonAthenaFullAccess 和 AmazonS3FullAccess:

[![](img/b9c88e05c29baa87c54fb01767423132.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v6eLXK14--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/4MDShP7RR0mbH1casivp)

继续并创建一个用户。创建之后，复制访问密钥 ID 和秘密访问密钥并保存它。请注意，你只会看到这个秘密一次，所以不要忘记把它保存在某个地方。

如果你还没有 Cube.js 帐户，现在就让我们[免费注册](https://statsbot.co/sign-up?cubejs=true)吧。注册后，连接 Athena 作为数据源:

[![](img/a3505e09759da9418a0976c60f806837.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lN4zQgWj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/8XNHka1uTIeBRwxXoKoG)

您还应该创建一个新的或者找到一个现有的 S3 存储桶来存储 us-east-1 区域内的 Athena 结果输出。设置应该如下所示:

[![](img/66534396235685f5a1e5448de27014c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bFQC0AzS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/pV0WayJXTO6YwDPc5now)

如果 Athena 连接成功，您将被转发到 Cube.js 模式。让我们创建一个新的`PageViews`文件并将它粘贴到:

```
cube(`PageViews`, {
 sql: `select * from aws_web_analytics.aws_web_analytics_event_collection`,

 measures: {
   count: {
     type: `count`
   },

   userCount: {
     sql: `anonymous_id`,
     type: `countDistinct`,
   }
 },

 dimensions: {
   url: {
     sql: `url`,
     type: `string`
   },

   anonymousid: {
     sql: `anonymous_id`,
     type: `string`
   },

   eventType: {
     sql: `event_type`,
     type: `string`
   },

   referrer: {
     sql: `referrer`,
     type: `string`
   },

   timestamp: {
     sql: `from_iso8601_timestamp(timestamp)`,
     type: `time`
   }
 }
}); 
```

Enter fullscreen mode Exit fullscreen mode

请用您自己的名称替换事件表名称。保存文件并进入浏览器。您可以在文档[中了解更多关于 Cube.js 模式的信息。如果一切正常，你将会看到本周的页面浏览量](https://statsbot.co/docs/getting-started-cubejs)

[![](img/b70495bdc858f08732988c1e768ae5b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rqe9rLpt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/qeiyhe7CTXOh7hONfwJh)

一旦成功，我们就可以启用 Cube.js API 访问了。为此，请转到数据源并编辑 Athena 数据源。在 Cube.js API 选项卡中，启用 Cube.js API 访问并复制全局令牌:

[![](img/74540fd10c82b2d274c5e31a8e85cc06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P0kA2HYx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/s7uBkLUASMKSv2vbybE8)

我们现在准备创建一个 React 应用程序来可视化我们的分析。

## 构建 React 分析仪表板

让我们使用 [create-react-app](https://github.com/facebook/create-react-app) 脚手架来为我们的应用程序创建目录结构:

```
$ yarn create react-app analytics-dashboard 
```

Enter fullscreen mode Exit fullscreen mode

然后将`cd`放入创建的目录中，并添加所需的依赖项:

```
$ yarn add @cubejs-client/core @cubejs-client/react antd bizcharts component-cookie uuid whatwg-fetch moment 
```

Enter fullscreen mode Exit fullscreen mode

`@cubejs-client/core`和`@cubejs-client/react`模块用于以方便的方式访问 Cube.js API。而`antd`和`bizcharts`用于创建布局和可视化结果。最后三个，`component-cookie`、`uuid`和`whatwg-fetch`用于实现跟踪页面功能，收集用户的事件数据。

先说追踪功能。在`analytics-dashboard`目录下创建一个`track.js`文件并粘贴到:

```
import { fetch } from 'whatwg-fetch';
import cookie from 'component-cookie';
import uuidv4 from 'uuid/v4';

export const trackPageView = () => {
  if (!cookie('aws_web_uid')) {
    cookie('aws_web_uid', uuidv4());
  }
  fetch(
    'https://<your_endpoint_url>/dev/collect',
    {
      method: 'POST',
      body: JSON.stringify({
        url: window.location.href,
        referrer: document.referrer,
        anonymousId: cookie('aws_web_uid'),
        eventType: 'pageView'
      }),
      headers: {
        'Content-Type': 'application/json'
      }
    }
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

请用您自己的收集函数端点替换该 URL。这是我们在客户端跟踪用户页面视图所需的所有代码。加载页面时应调用此代码。

让我们用两个简单的图表创建主应用程序页面。为此，用下面的代码片段替换 App.js 的内容:

```
import React, { Component } from 'react';
import "antd/dist/antd.css";
import "./index.css";
import { Row, Col, Card, Layout } from "antd";
import cubejs from '@cubejs-client/core';
import { QueryRenderer } from '@cubejs-client/react';
import { Spin } from 'antd';
import { Chart, Axis, Tooltip, Geom, Coord, Legend } from 'bizcharts';
import moment from 'moment';
import { trackPageView } from './track';

const dateRange = [
  moment().subtract(14,'d').format('YYYY-MM-DD'),
  moment().format('YYYY-MM-DD'),
];

const { Header, Footer, Sider, Content } = Layout;

const renderChart = (resultSet) => (
  <Chart scale={{ category: { tickCount: 8 } }} height={400} data={resultSet.chartPivot()} forceFit>
    <Axis name="category" label={{ formatter: val => moment(val).format("MMM DD") }} />
    {resultSet.seriesNames().map(s => (<Axis name={s.key} />))}
    <Tooltip crosshairs={{type : 'y'}} />
    {resultSet.seriesNames().map(s => (<Geom type="line" position={`category*${s.key}`} size={2} />))}
  </Chart>
);

const API_KEY = 'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpIjo0MDY3OH0.Vd-Qu4dZ95rVy9pKkyzy6Uxc5D-VOdTidCWYUVhKpYU';

class App extends Component {
  componentDidMount() {
    trackPageView();
  }

  render() {
    return (
      <Layout>
          <Header>
            <h2 style={{ color: '#fff' }}>AWS Web Analytics Dashboard</h2>
          </Header>
          <Content style={{ padding: '25px', margin: '25px' }}>
            <Row type="flex" justify="space-around" align="middle" gutter={24}>
              <Col lg={12} md={24}>
                <Card title="Page Views" style={{ marginBottom: '24px' }}>
                  <QueryRenderer
                    query={{
                      "measures": [
                        "PageViews.count"
                      ],
                      "timeDimensions": [
                        {
                          "dimension": "PageViews.timestamp",
                          "dateRange": dateRange,
                          "granularity": "day"
                        }
                      ]
                    }}
                    cubejsApi={cubejs(API_KEY)}
                    render={({ resultSet }) => (
                      resultSet && renderChart(resultSet) || (<Spin />)
                    )}
                  />
                </Card>
              </Col>
              <Col lg={12} md={24}>
                <Card title="Unique Visitors" style={{ marginBottom: '24px' }}>
                  <QueryRenderer
                    query={{
                      "measures": [
                        "PageViews.userCount"
                      ],
                      "timeDimensions": [
                        {
                          "dimension": "PageViews.timestamp",
                          "dateRange": dateRange,
                          "granularity": "day"
                        }
                      ]
                    }}
                    cubejsApi={cubejs(API_KEY)}
                    render={({ resultSet }) => (
                      resultSet && renderChart(resultSet) || (<Spin />)
                    )}
                  />
                </Card>
              </Col>
            </Row>
          </Content>
        </Layout>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

确保用您自己的 Cube.js 全局标记替换`API_KEY`常量。您应该能够看到带有两个图表的仪表板:

[![](img/2971fff3214478a1dd6e6c1b7f4d5a09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YOJBEkc6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.graphcms.com/VGHw26wtQyOfgqL0vzEb)

再一次，[这是](http://aws-web-analytics-dashboard.s3-website-us-east-1.amazonaws.com)仪表盘的部署版本，如果你想看看的话。

要部署您自己的应用程序，请创建一个启用静态站点服务的公共 S3 桶，构建应用程序，并将其同步到桶:

```
$ yarn build
$ aws s3 sync build/ s3://<your_public_s3_bucket_name> 
```

Enter fullscreen mode Exit fullscreen mode

你也可以使用像 [Netlify](https://www.netlify.com/) 这样的服务来托管你的网站。它们让部署和托管网站变得异常简单。

## 结论

本教程向您展示了如何构建一个与 Google Analytics 特性集相比的概念验证。该架构的可扩展性足以每秒处理数千个事件，并且可以毫不费力地分析数万亿个数据点。Cube.js 适合实现你期望在 GA 中看到的所有指标，比如跳出率、会话花费时间等。你可以在这里阅读更多相关信息[。没有数据的预聚合，就无法构建大规模分析。GA 经常这样做，Cube.js 有一个内置的](https://statsbot.co/docs/event-analytics)[解决方案](https://statsbot.co/docs/pre-aggregations)。
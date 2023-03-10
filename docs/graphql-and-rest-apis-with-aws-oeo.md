# 带 AWS 的 GraphQL 和 REST APIs

> 原文：<https://dev.to/metamn/graphql-and-rest-apis-with-aws-oeo>

最近，我被委托寻找在 AWS 上创建 API 的方法，以供 web 和本地移动客户端使用。

* * *

风景很简单。对于经典的 REST API，可以使用 Amazon API 网关服务。对于 GraphQL，AWS AppSync。

乍一看，一切都很简单:选择一个服务。深入研究之后——为了休息，一切都变得复杂，而 GraphQL 保持干净。

## 原因

[**AppSync / GraphQL**](https://aws.amazon.com/api-gateway/) 自动化程度更好。API 所需的所有附加服务(功能、数据、存储、授权)都被集成，变得几乎不可见，或者只需一次点击即可配置。一个人必须用一个官方的工具链来管理一个单一的伞式服务。仅此而已。

[**REST API 网关**](https://aws.amazon.com/appsync/) 原来只是拼图的一部分。与 AppSync 不同，仅提供一个 API 是不够的。它至少需要手动连接 AWS Lambda 和 dynamo db——功能和数据的独立服务。

不是一次服务休息，而是三次。三倍的工作量？在管理方面，是的，在编码方面。由于 REST 没有像 GraphQL 那样的代码生成器，所有代码都必须手工编写。这导致**的工作量增加了**。

有第三方生成器、工具和框架可以提供帮助。事实上，有一个完整的行业是围绕着让 REST APIs 在 Amazon 上工作而建立的——事情就是这么复杂。

虽然在几天内就可以用 React 设置 AppSync，并仔细考虑优化和体验(Relay vs. Apollo)，但花在 REST API 上的时间仅够创建和管理模式。使用生态系统提供的生成器和工具添加必要的 Lamdba 函数和数据后端是不成功的。

## 休息

[![The REST API on AWS](img/2733c3026150c8f9648b6c6b3e147562.png) ](///react/static/dcc205f362f6e5b460a7dff5b75234cd/96788/rest-api-aws.jpeg) *图片来源:【https://iotdemos.wordpress.com】T4*

要了解 AWS 复杂性的其余部分，应该看看这些官方视频。第一个解决了创建 API 的困难，第二个解决了确保最佳实践的困难。

根据视频中的建议，人们应该使用推荐的工具来更快地获得更好的结果: [Swagger](https://swagger.io/) ， [Serverless](https://serverless.com/) ，[Claudia . js](https://www.claudiajs.com/)——或者甚至尝试亚马逊自己的 [Amplify](https://aws.amazon.com/amplify/) 。

我的个人经历:没有成功。bug、过时的代码和文档、未记录的特性、[令人恐惧的](https://serverless.com/blog/node-rest-api-with-serverless-lambda-and-dynamodb) [大量的代码](https://serverless.com/blog/serverless-express-rest-api)需要编写。

剩下的结论是 AWS REST APIs 过时、复杂且昂贵。

## GraphQL

GraphQL 是 REST 的继任者。它具有以下优点:

#### 1。灵活的 API

GraphQL 不需要版本控制。REST APIs 的一个痛点是迭代版本，但对 API 客户端和最终用户保持透明。

GraphQL 消除了这一点，缩短了开发和维护时间。

#### 2。具有实时和离线功能的数据驱动型应用

REST APIs 在移动时代之前是一个人工制品。他们只是为每个原子请求提供原子数据。比如一个包含所有文章的列表。仅此而已。

随着移动设备的出现，需求发生了变化:需要更智能、低带宽的查询(相对于通常的 REST CRUD 操作而言是动态的),甚至可以在没有请求的情况下(实时)推送，并且在网络服务不可用时具有弹性(离线功能)。

GraphQL 应用程序可以在一个查询中请求所有文章以及作者、评论和喜欢。这些数据被自动缓存在本地以供离线访问，当其中一部分被更新时(比如添加了新文章)，新数据被推送到设备并自动可视化。

GraphQL 堆栈的一个完整例子是[脸书 2019 重新设计和重写](https://developers.facebook.com/videos/2019/building-the-new-facebookcom-with-react-graphql-and-relay/)。

## AppSync

[![The AWS AppSync Management Console](img/25c718647ff2345a8f6a23adc0e22461.png)](///react/static/830f175816ee1d90dd8c659a3b6b76e3/2fba0/aws-appsync-management-console.png)

[AppSync](https://aws.amazon.com/appsync/) 是亚马逊的 GraphQL 服务。管理 API 模式、模式查询、附加到模式的数据源，以及跨模式/应用程序重用的功能/业务逻辑。

AppSync 的神奇之处在于，人们只需处理模式，而忘记其他的。

定义模式后，只需点击一下鼠标，所有 GraphQL 查询、变异和订阅都会自动生成。和一个带有现成可用 API 的公共 URL。

接下来，通过使用[AWS Amplify Framework](https://aws.amazon.com/amplify/)——一个创建、管理和实现亚马逊网络服务的命令行工具——用几个简单的命令就可以重新设置所有其他东西，比如数据、认证、存储绑定；托管甚至各种 web 和移动客户端来消费 API。

使用 AppSync 和 Amplify，您可以在…分钟内拥有一个移动友好的 API 服务器和客户端。一旦模式完成，剩下的工作就由 AWS 完成了。

# 总结归纳

|  | GraphQL | 休息 |
| --- | --- | --- |
| 要管理的服务 | AppSync | API 网关，AWS Lambda，DynamoDB |
| 官方工具链 | AWS 放大器 | 没有人 |
| 代码生成器 | 功能、数据、存储、创作、托管 | 没有人 |
| 客户端 SDK | iOS、Android、Web、React Native | Java、JavaScript、Android 的 Java、iOS 的 Objective-C 或 Swift 以及 Ruby |
| 建议的第三方服务 | 没有人 | 为模式招摇；工装用 Serverless.com |
| 开发任务 | 创建 GraphQL 片段 | 编写一个完整的本地服务器，稍后将部署到不同的 AWS 服务 |
| 发展环境 | 反应 | Node.js，快递 |
| 发展技能 | 前端 | 全栈，AWS 工程 |

## 资源

*   [亚马逊 API 网关](https://aws.amazon.com/api-gateway/)
*   [AWS AppSync](https://aws.amazon.com/appsync/)
*   [AWS Amplify 框架](https://aws.amazon.com/amplify/)
*   [用 React、GraphQL 和 Relay 构建新 facebook.com](https://developers.facebook.com/videos/2019/building-the-new-facebookcom-with-react-graphql-and-relay/)
*   [使用 Serverless、Express 和 Node.js 部署 REST API](https://serverless.com/blog/serverless-express-rest-api)
*   [用 Lambda、API Gateway、DynamoDB 和无服务器框架在 Node.js 中构建 REST API](https://serverless.com/blog/node-rest-api-with-serverless-lambda-and-dynamodb/)
*   [用亚马逊 API Gateway 构建 API 驱动的微服务——AWS 在线技术讲座](https://www.youtube.com/watch?v=xkDcBssNd1g)
*   [使用 Amazon API Gateway 构建企业级 API 的最佳实践——AWS 在线技术讲座](https://www.youtube.com/watch?v=9ElpSPXk-g8)
*   [大摇大摆](https://swagger.io/)
*   [无服务器](https://serverless.com/)
*   [Claudia.js](https://www.claudiajs.com/)
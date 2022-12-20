# Web APIs 的简史

> 原文：<https://dev.to/mikeralphson/a-brief-history-of-web-apis-47k4>

在 20 世纪 90 年代末和 21 世纪初，分布式 API 在 HTTP 协议上的主要使用包括以相对简单的远程过程调用(RPC)方式交换可扩展标记语言(XML)格式的文档。

如果你在受影响的地区生活或工作，基于 XML 的 RPC APIs 的一个不利方面是它们关于适用专利的状态不清楚(参见 [US7028312](https://patents.google.com/patent/US7028312B1/en) )。

诸如 XML-RPC 之类的协议演变成了简单对象访问协议(SOAP ),从而产生了广为人知的 Web 服务方法。这些主要是机器对机器的交互，尽管它们使用了万维网的技术，但它们很少在网络服务器和网络客户端(浏览器)之间使用。

HTTP 上的 SOAP 调用涉及对 HTTP 端点的`GET`或`POST`请求，将`SOAPAction`指定为 HTTP 头或 URL 查询参数。尽管 SOAP 也支持不太常见的传输方式，如电子邮件。XML 名称空间用于区分 SOAP 信封元素和由 web 服务定义的消息体元素，但是这对于提高 SOAP 消息的可读性毫无帮助。

SOAP 请求的例子，摘自[https://www.w3.org/2001/03/14-annotated-WSDL-examples:](https://www.w3.org/2001/03/14-annotated-WSDL-examples:)

```
<SOAP-ENV:Envelope
  xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/"
  SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/">
  <SOAP-ENV:Body>
    <m:GetEndorsingBoarder xmlns:m="http://namespaces.snowboard-info.com">
      <manufacturer>K2</manufacturer>
      <model>Fatbob</model>
    </m:GetEndorsingBoarder>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope> 
```

SOAP 响应的示例，来自同一来源:

```
<SOAP-ENV:Envelope
  xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/"
  SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/">
  <SOAP-ENV:Body>
    <m:GetEndorsingBoarderResponse xmlns:m="http://namespaces.snowboard-info.com">
      <endorsingBoarder>Chris Englesmann</endorsingBoarder>
    </m:GetEndorsingBoarderResponse>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope> 
```

因为 SOAP over HTTP 仅限于`GET`和`POST` HTTP 方法，所以它无法从更广泛的可用 HTTP 方法(如`PUT`、`DELETE`、`HEAD`和`OPTIONS`)以及与之相关的完整 HTTP 状态代码中获益。这可能会对可缓存性和性能产生负面影响。

#### AJAX

2005 年 2 月，Jesse James Garrett 发表了一篇名为[Ajax:Web 应用程序的新方法](https://adaptivepath.org/ideas/ajax-new-approach-web-applications/)的文章，详细描述了 Google 开发的一种相对轻量级的方法，其中 Web 客户端可以异步(即在后台)从服务器加载内容。AJAX 代表异步 JavaScript 和 XML，很快成为使网站动态化的流行方法，催生了“Web 2.0”的概念。

尽管最初关注的是加载 HTML 和 XHTML 文档片段，但开发人员很快就开始使用 AJAX 与 web 服务器交换数据，几个因素(如性能和跨浏览器兼容性)导致了从 XML 到 JavaScript 对象表示法(JSON)的数据格式逐渐转变。然而，这个首字母缩略词一直沿用至今。

曾经常见的是，将来自多个“Web 2.0”来源的内容或数据组合在一起的网站被称为“混搭”。虽然这个术语已经基本上不再使用了，但是这个术语的残余仍然可以在组织名称中看到，例如 Mashery(现在是 Tibco 的一部分)和 Mashape(现在被称为 Kong)。

AJAX 的简单性和 jQuery 等 JavaScript 库的日益流行导致了服务器和浏览器之间 web APIs 使用的激增。随着 2007 年以来移动应用的兴起，web API 的使用只会进一步增长，这种方法现在已经与 JavaScript、XML 和浏览器本身脱钩，已经有效地取代了 SOAP 等其他 web API 技术。

#### REST 和 RESTful APIs

Roy T. Fielding 在他 2000 年发表的博士论文中创造了 REST 这个术语——代表代表性状态转移；架构风格和基于网络的软件架构的设计。它旨在描述像万维网这样的网络以及像 Web 应用程序这样的软件应该如何工作。它拒绝 RPC 风格的 API 方法，提倡使用我们在万维网上熟悉的特性，例如:

*   有意义的资源标识符，如 URIs
*   资源表示的传递，与消息或函数调用相反
*   标准媒体类型和 HTTP 状态代码的使用
*   适当时明确支持缓存

来传输超媒体文档(包含数据的包含文本的链接，就像超文本仅仅指包含链接的文本一样)。

REST 被描述为一种架构风格，这就是应该如何看待它的原则和建议。从 REST 中可以学到很多东西，当面临复杂的设计决策时，它通常是一个很好的参考。REST 不是“新的 SOAP ”,它们处理完全不同风格的 API，并且以完全不同的方式来处理。REST 风格在应用于 HTTP 时，也最大限度地利用了这一底层协议，因此如果实现得好，性能会很高，尽管这不是它最初的关注点。HTTP/2 改进带来的好处只会进一步提高这种性能。然而，休息并不是一个你必须遵守的标准，也不应该被视为一条真正的道路，就像福音一样。知道什么时候打破“规则”和知道什么时候坚持它们一样重要，重要的是不要把严格遵守 REST 本身视为一个目标，特别是当它与您或您的 API 消费者的业务需求冲突时。

REST 确实有特定的约束，它应用于 web 的设计来定义它的架构风格，就像建筑风格如帕拉第亚风格或新古典风格被用来划分和描述建筑架构的范围一样。如果一个 API 没有展示出所有的强制 REST 约束，那么它就不能真正被称为 RESTful API。这导致了类似 REST，甚至 RESTish 这样的术语的产生，用来描述显示 REST 风格的某些方面的 API，或者与 REST 比类似 RPC 的 API 有更多的共同点。REST 本身早于 web APIs 的出现，而且在将 web / web 应用程序的 REST 风格映射到我们现在所知道和描述的 API 时会有一些困难。例如，美国白宫既有帕拉第亚风格的建筑，也有新古典主义的建筑，但这并没有降低它的宏伟建筑，也没有降低它在短期内倒塌的可能性。

> 与大多数现实世界的系统一样，并非部署的 Web 体系结构的所有组件都遵守其体系结构设计中的所有约束

*菲尔丁博士论文，第 6.2.5 节*

通常 we b-API 被称为 RESTful APIs，意思是那些符合 REST 架构风格强加的大多数或所有约束的 API。

#### GraphQL

2015 年 1 月，脸书宣布其内部客户端 API 建立在一种名为 GraphQL 的新技术之上(尽管它自 2011 年以来一直在以某种形式进行开发)。这是一个 API 框架，它有点依赖于 RPC 模型，有点依赖于 REST 和它对 web 协议的使用，但也有自己的方向。

GraphQL 基于类型模式，其中数据类型和它们之间的关系用接口描述语言(IDL，也称为模式描述语言，或 SDL)建模，该信息可由客户端使用所谓的自省查询来动态查询。

与 RPC 等固定函数或 REST 等可通过 URL 访问的固定资源表示不同，GraphQL 是一种概念上类似于结构化查询语言(SQL)的查询语言，它允许客户端仅请求执行任务所需的数据，有时还可以避免多次 API 调用来获取所需的所有数据。GraphQL 还可以批处理和组合查询。GraphQL 将查询分为仅仅读取数据的查询和可以改变数据的查询，即所谓的“突变”。

下面是一个 GraphQL 查询的例子，它使用的语法有点类似于 JSON:

```
{ allPeople { people { name, homeworld { name }}}} 
```

以及来自[http://graphql.org/swapi-graphql](http://graphql.org/swapi-graphql)T2【的 JSON 回复摘录

```
{  "data":  {  "allPeople":  {  "people":  [  {  "name":  "Luke Skywalker",  "homeworld":  {  "name":  "Tatooine"  }  },  {  "name":  "C-3PO",  "homeworld":  {  "name":  "Tatooine"  }  },  {  "name":  "R2-D2",  "homeworld":  {  "name":  "Naboo"  }  }  ]  }  } 
```

这种方法有一些潜在的性能缺点，因为在 HTTP 级别缓存 GraphQL 查询很困难，而且客户端可能会发出以前从未见过或没有经过充分测试的查询。

还有一种观点认为 GraphQL 的类型系统鼓励底层数据模型(比如 SQL 数据库模式)和呈现给客户端的数据模型之间更紧密的耦合(尽管情况并不总是如此)。

脸书 GraphQL 堆栈提供了一个交互式控制台，称为 graphic QL(发音为“graphical”)。其他组织提供 GraphQL 的实现(例如开源的 Apollo 框架), GraphQL 被 GitHub、Shopify 和 Yelp 等组织公开使用。

#### gRPC

最近几年，API 的 RPC 风格以 Google 的 gRPC 的形式复兴了(g 代表 gRPC，是递归的缩写，不是 Google)。

gRPC 关注的是性能，从 Google 系统交换的数据量就可以想象得到，并且要求使用 HTTP/2。消息由协议缓冲区(protobuf)接口描述语言定义，它具有文本/人类可读和二进制序列化。生成数据并将其序列化为协议缓冲区格式的代码通常是使用`protoc`编译器从 protobuf IDL 生成的，这有助于实现紧凑和高性能的表示，尽管这种表示不像 HTTP/1.x 这样的文本传输协议那样容易检查。

Google 的一个开源项目， [gnostic](https://github.com/googleapis/gnostic) 旨在允许使用 OpenAPI 规范来定义使用 protobuf 格式的 API。

感谢 Marco Palladino 在这篇文章中捕捉到一个想法。
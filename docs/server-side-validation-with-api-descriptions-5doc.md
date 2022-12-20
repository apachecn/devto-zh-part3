# 带有 API 描述的服务器端验证

> 原文：<https://dev.to/philsturgeon/server-side-validation-with-api-descriptions-5doc>

验证可能意味着很多事情，但在 API 领域，它通常意味着弄清楚设置给 API 的数据是好是坏。验证可以发生在很多不同的地方，可以发生在服务器上，也可以发生在客户端。传统上，客户端和服务器端验证都扮演着角色，涵盖了不同的用例。

客户端验证通常用于非常快速地向用户提供反馈，例如用红色轮廓突出显示失败的输入框，解释电子邮件地址看起来无效的工具提示，解释“偿还信用卡的金额”应该大于 0，等等。如今，浏览器处理了大量的视觉反馈，所以客户端验证不像以前那么频繁了，但它仍然是必需的，因为“应该设置字段 A 或 B，但不是两个都设置，如果 B 设置了，我们 C 也应该设置。”诸如此类的事情。

[![](img/1b8cd38e293da2e6026c5154e47fff92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R_Cybk-L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/0%2AVLT_tCuqQX4svep4.png)

服务器端验证总是需要的，对于 API 来说，这是两者中最重要的。完全依赖于客户端的 API 最终会出问题。来自客户端的数据永远不可信，因为服务器不可能知道客户端上发生了什么。即使你只为两个已知的客户端开发一个私有 API，这些客户端的验证也总有失败的可能；或者有人会用 curl 或者 Postman 打那些 API，发一些无效的东西。即使数据库捕捉到无效数据，[错误也不会有用](https://www.apisyouwonthate.com/blog/creating-good-api-errors-in-rest-graphql-and-grpc)。

在过去的 15 年里，编写验证规则一直是我…头疼的主要原因，所以我一直在想一种同步这两者的方法。

### API 描述文件

服务器端验证通常完成最普通的任务。

*   该属性是必需的吗
*   该属性是电子邮件地址吗
*   这个属性是信用卡号吗
*   如果存在其他属性，是否需要此属性

一些框架将这种逻辑硬塞到控制器中，当您需要在两种不同的用例中验证相同的有效负载时，这是一种痛苦。其他人把它塞进数据模型，比如 Ruby on Rails:

这一切听起来是不是非常熟悉？这正是 API 描述文档[(也称为规范)](https://www.apisyouwonthate.com/blog/resolving-overloaded-terms-for-api-specifications-descriptions-contract)所谈论的内容、需求、类型、格式等。所有这些都已经完全由我们用来[生成我们的模拟服务器](http://github.com/stoplightio/prism/)用于试验集成、我们用来获取[漂亮的参考文档](https://www.apisyouwonthate.com/blog/turning-contracts-into-beautiful-documentation)以及我们用来管理我们的 API 网关等的相同 API 描述来处理。

你们中的一些人可能已经读过我们不久前关于使用 JSON Schema 进行客户端验证的文章，现在我们想向您展示如何利用您现有的事实资源来大幅减少您也需要编写的服务器端验证代码的数量。

### 哪种描述格式

OpenAPI 和 JSON Schema 是两种最大的 API 描述格式，所有编程语言都有很多选择。

OpenAPI 工具列在 [OpenAPI 上。工具](https://openapi.tools/)和 JSON 模式在 [JSON 模式:实现](https://json-schema.org/implementations.html)上有一个很大的列表。

### JSON 模式示例

JSON 模式不知道像 URL 或 HTTP 方法这样的元数据，因为它被设计为与任何 JSON 数据实例一起工作。在 API land 中，我们最有可能使用的 JSON 数据实例是 HTTP 请求体或响应体。

出于示例目的，我们将在 Node.js 中进行一些 JSON 模式验证，但是您可以使用任何具有 [JSON 模式验证器](https://json-schema.org/implementations.html#validators)的语言(这是大多数语言)。

要使用 JSON Schema 进行服务器端验证，通常只需获取一个验证器，将其放入控制器或“routing”或您选择的语言/框架所称的任何东西。使用 [Express.js](https://expressjs.com/) ，我们可以把这个逻辑放到我们的路由中。

对于节点用户来说，这是一个相当熟悉的 app.js，我们只需要一些代码，并加载 userSchema。

该 userSchema 文件来自 schemas/user.json，您可以在本地创建该文件，其内容如下:

当您检查该结构时，您可以推断出我们的 JSON 对象具有以下属性列表:name、email 和 date_of_birth。前两个是字符串，第三个是日期。此外，根据需要标记名称。

现在我们可以用 node app.js 运行这个节点应用程序，并向它发出 HTTP 请求:

```
$ http -v PUT http://localhost:3000/123 name=Frank

HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 14
Content-Type: text/html; charset=utf-8
Date: Sat, 25 May 2019 08:09:10 GMT
ETag: W/"e-4o7E1rWH1O+7xJOCXIMFqIbMSxE"
X-Powered-By: Express

that was great 
```

好吧，这很好，因为名字是固定的，但电子邮件和出生日期是可选的。让我们试着发送它们，但是不好。

```
$ http -v PUT http://localhost:3000/123 name=Frank email=notanemail

HTTP/1.1 400 Bad Request
Connection: keep-alive
Content-Length: 164
Content-Type: application/json; charset=utf-8
X-Powered-By: Express

{
 "errors": [
 {
 "dataPath": ".email",
 "keyword": "format",
 "message": "should match format \"email\"",
 "params": {
 "format": "email"
 },
 "schemaPath": "#/properties/email/format"
 }
 ]
} 
```

哦不！发生了一些错误。不好意思！

这些错误不是最好的格式，因为我们只是出于演示的目的将它们丢弃，但这可以用一个简单的助手来整理。

如果你已经在控制器中做了验证，那么你的控制器应该更干净，如果你在你的模型中做了广泛的验证，那么这将会去除很多瑕疵。如果您以前没有验证，那么使用这种方法意味着您不需要开始编写它。赢赢赢！

此外，虽然这在任何语言中都可以工作，但是使用 Rack 的 Ruby 用户(因此任何使用 Rails 的人也可以)可以使用 [committee](https://github.com/interagent/committee) ，这是一个非常棒的中间件，可以使这变得稍微容易一些。

JSON Schema 非常擅长处理请求体验证，但是必须在每个控制器中都这样做可能有点烦人。OpenAPI 可以帮助我们解决这个问题。

### OpenAPI 中间件示例

OpenAPI 在这里可能更容易实现，因为它也涵盖了服务模型，而不仅仅是数据模型。

```
openapi: "3.0.0"
# ... snip ...
paths:
 /pets:
 post:
 description: Creates a new pet in the store
 operationId: addPet
 requestBody:
 description: Pet to add to the store
 required: true
 content:
 application/json:
 schema:
 $ref: '#/components/schemas/NewPet'
 responses:
 '200':
 description: pet response
 content:
 application/json:
 schema:
 $ref: '#/components/schemas/Pet' 
```

因为 OpenAPI 会说“这个模式应该用于这个组合或者 HTTP 方法和路径”,所以你不需要提供胶水。相反，许多语言提供的工具让你只需注册一个中间件，告诉中间件使用哪个 OpenAPI 文件，然后工作就完成了。

继续使用 Node/Express 作为示例，让我们看看如何使用 OpenAPI 和注册中间件:

Tadaaa！您不必在所有的路由中进行验证检查，因为中间件可以为您处理这些，如果传入的请求无效，您的路由/控制器代码甚至不会被调用。框架中间件能够查看请求，将其与 API 描述进行比较，并在您的代码需要唤醒之前以错误格式(希望类似于 [RFC 7807](https://tools.ietf.org/html/rfc7807) )拒绝它。

有相当多的选择，但应该更多:

对于 OpenAPI v3.0 版:

*   **PHP:**[open API-PSR 7-validator](https://github.com/lezhnev74/openapi-psr7-validator)
*   **node . js:**[express-swagger-ajv-validator](https://github.com/Zooz/express-ajv-swagger-validation)/[express-open API-validate](https://github.com/Hilzu/express-openapi-validate)
*   **Ruby/Rails:** [委员会](https://github.com/interagent/committee)
*   **Perl:** [【磨粉机::插件::open API】](https://metacpan.org/pod/Mojolicious::Plugin::OpenAPI)

对于 OpenAPI v2.0:

*   **Rails:**[swagger _ shield](https://github.com/amcaplan/swagger_shield)

Rails 工具 swagger_shield 非常棒。它因在失败时使用 RFC 7807 而赢得最大的“惯恨点数:

```
{
 "errors": [
 {
 "status": "422",
 "detail": "The property '#/widget/price' of type string did not match the following type: integer",
 "source": {
 "pointer": "#/widget/price"
 }
 }
 ]
} 
```

### 仍然需要一些验证

这只处理不需要在数据存储中查找的验证规则，或者需要运行一些其他类型的程序。您可以使用 JSON Schema 或 OpenAPI 做很多事情，但是它不能告诉您电子邮件地址是否有效，或者这个资源是否处于正确的状态，可以做您正在尝试做的事情。

没问题。验证完成后，您仍然可以执行自己的检查，因为所有的东西都是使用 RFC 7807 完成的，所以您的代码、中间件和其他东西都将匹配。可爱！

### 测试好处

除了不必编写大量验证代码而节省的时间和金钱之外，还有两个巨大的好处我们还没有得到。

***试图*** [***保持文档与代码同步的整体思路***](https://www.apisyouwonthate.com/blog/keeping-documentation-honest) ***当你的描述文档是字面上的代码时，这种想法就消失了。*T13】**

由于您使用与文档、模拟服务器等相同的描述文档来处理请求验证，因此不需要额外的逻辑来确保您的请求被正确描述(或记录)。您可以对请求进行常规的集成测试，一切都准备好了。

```
expect(goodApple).to.be.jsonSchema(fruitSchema); 
```

您处理 bog 标准单元和集成测试的测试套件现在证明了您记录的请求是正确的，如果有人在没有更新描述文档的情况下改变了请求的工作方式，他们已经被当场抓获，他们的拉请求将会失败。如果他们更新了 pull 请求中的描述来修复测试，嘣，我们现在可以聊一聊为什么他们只是试图提交一个突破性的更改…！🧐

使用描述文档进行验证只覆盖请求，所以[使用 API 描述文档来支持契约测试](https://www.apisyouwonthate.com/blog/tricking-colleagues-into-writing-documentation-via-contract-testing)以确保响应也是良好的。

[通过合同测试欺骗同事编写文档](https://blog.apisyouwonthate.com/tricking-colleagues-into-writing-documentation-via-contract-testing-206308b47e05)

### 服务器端验证的未来

这是一个非常古老的概念，最近随着越来越多的开发人员开始关注 API 设计优先的工作流，这个概念变得越来越流行。

编写 HTTP APIs 的好处之一是，您通常不会被限制在一个单一的实现中，并且不必尝试和强制使用随之而来的“一刀切”的工具。遗憾的是，这意味着某些语言的某些工具不如其他语言优秀，但由于我们是开源开发者社区，我们可以解决这个问题。

我听到开发人员说“这不是高性能的”，但是没有理由相信这一点。一个特定的工具可能不是以最有效的方式编写的，但是可以用 PRs 来解决。只要该工具不是动态地解析整个文档，并且在启动时在内存中构建某种工件，这可能比运行您已经加载的任何大型“验证库”来手动完成所有这些要容易得多。

另一种方法是跳过在服务器端做这件事，把它移到更高的层次:API 网关。我们很快会写更多关于这方面的内容，但是大多数 API 网关开始变得更加聪明，知道如何接受 API 描述作为输入，以及如何使用输入。

一个例子是 [Express Gateway](https://www.express-gateway.io/) ，他[增加了 JSON 模式验证](https://www.express-gateway.io/docs/policies/customization/conditions/#json-schema)，一个由我的朋友和 [Stoplight.io](https://stoplight.io/) 同事[文森佐·基亚内塞](https://twitter.com/D3DVincent)维护的项目。

### 总结

将描述视为获取文档后必须做的烦人事情的日子已经一去不复返了。API 描述现在排在第一位，用于[合同测试](https://www.apisyouwonthate.com/blog/tricking-colleagues-into-writing-documentation-via-contract-testing)，获得实现的反馈，以及一大堆其他的东西。

用这个作为另一个胡萝卜来说服你落后的队友或老板，这是一条路要走。

[![](img/7ac8e2322a5261628991f1b9df9e2899.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lzK3vb3S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgJBqIwe1FYx3iO1DONgJIQ.jpeg)

* * *
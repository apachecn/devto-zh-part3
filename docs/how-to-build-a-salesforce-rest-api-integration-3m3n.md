# 如何构建 Salesforce REST API 集成？

> 原文：<https://dev.to/vikastwittes/how-to-build-a-salesforce-rest-api-integration-3m3n>

基于 REST 的 API 使用 HTTP 请求数据。REST 使用很少的带宽，因此更适合互联网使用。API 是最合适的代码，通过它两个不同的软件程序可以相互通信。

**REST API 的使用**

一个在销售和营销领域拥有产品的公司，构建一个 Salesforce API 可以带来巨大的好处，因为 Salesforce 的范围很广。在 Salesforce 生态系统中列出您的产品或应用程序是销售方法中最适合的社区。当一家公司在实力和规模方面达到一定水平时，它会发现适合求助于 Salesforce 来做大销售和营销。Salesforce 有一个优秀的开发人员社区，并有一个针对您可能面临的几乎任何障碍的精心编写的解决方案。此外，他们还有一个完善的电子学习平台，提供了大量的学科选择，以获得对各种术语和原则的见解。

**构建 REST API 集成**

REST API 主要集中在根据您的产品或应用程序发送的数据查找来自 Salesforce 的数据。这些集成通常不涉及任何成本，但是您应该记住，通过 REST 的集成仅对企业计划中的 Salesforce 用户可用。整合需要 3 个重要步骤:

```
Authorization
Data push by User
Data retrieval by User 
```

* *第一步:创建开发者账户**

他们真的很有用，并提供了一个 Salesforce 组织，在那里您可以亲自操作界面。您还可以自由添加和删除用户。

**第二步:连接应用**

一旦开发人员帐户就位，你将不得不建立一个连接的应用程序，它具有巨大的功能，因此很难理解。为了使事情更简单，将您的连接应用程序作为一个小应用程序，它基于需要集成的 Salesforce 基础架构。这个连接的应用程序将满足身份验证的管理，并将请求引导到适当的客户端服务。因此，现在您必须单击导航栏右上角的设置图标。在快速查找框中，您需要选择应用程序管理器，然后单击“新连接的应用程序”。这将带您进入基本信息部分，您必须输入以下三项内容:

```
Connect App Name
API Name
Email address 
```

接下来，在 API 部分，在您检查了 [Enable OAuth settings](https://help.salesforce.com/articleView?id=connected_app_create.htm&type=5) 之后，您必须输入回调 URL。后者是 URL，一旦用户允许您的应用程序访问他/她的数据，Salesforce 将使用它来发布。因此，这包括访问和请求令牌。你应该有一个适当定义的角色，让某人来处理。此外，在选定的 OAuth 范围下，选择访问并管理您的数据，最后单击 Add。现在，除了为您的应用程序生成消费者密码之外，还将生成消费者密钥。

设置完成后，用户将被带到 Salesforce 授权的端点，他们需要登录并批准应用程序访问数据。授权完成后，Salesforce 会发送两个令牌，即

```
**Access Token:** This needs to be put in the header of every API request for data. It has an expiry date and by default, it expires after 15 minutes in Connected Apps.
**Refresh Token:** This is used to fetch a valid access token. Expiry settings can be changed on this. 
```

您必须从访问令牌响应构建 API URL，并将访问令牌的值用作报头中的承载令牌。您必须小心访问令牌中必须转义的字符。

**第三步:从 Salesforce 检索应用程序数据**

在进行检索之前，您应该了解 Salesforce 对象的含义。Salesforce 的对象是包含组织数据的数据库表。这些是客户、潜在客户、任务、联系人等。也可以创建自定义对象。Salesforce 记录描述了对象的特定事件。

说到检索，这可以通过向联系端点发出请求来完成，联系端点将返回关于所讨论的对象的信息。此外，您可以使用对象“describe”端点的组合，这将给出用户可以填充的所有字段。因此，对所有字段的访问也可以用于构建自定义查询，该查询可以给出关于对象的所有相关属性。

**结论**

Salesforce REST API 易于使用，具有简单的集成和简单的 HTTP 请求。Salesforce 在企业客户层面取得了不可比拟的成功。这是一个完美的技术选择，可以用于移动应用程序和 Web 2.0 项目。 [Salesforce 编码技能](https://www.janbasktraining.com/blog/how-to-improve-coding-skills-in-the-salesforce/)和 REST API 也很重要，对于各种 web 或移动应用程序非常有用，这些应用程序对访问大量数据没有任何基本需求。这是一个开创性的平台，其采用率非常高。这也增加了那些接受过适当的 Salesforce 在线培训的人的职业机会。
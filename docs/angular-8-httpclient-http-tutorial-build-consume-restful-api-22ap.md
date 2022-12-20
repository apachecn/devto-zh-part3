# Angular 8 HttpClient & Http 教程——构建、使用 RESTful API

> 原文：<https://dev.to/singhdigamber/angular-8-httpclient-http-tutorial-build-consume-restful-api-22ap>

[![Angular 8 HttpClient Tutorial](img/0277fcf338be5cafe813e2eeb91369e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rq5eA_4u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.positronx.io/wp-content/uploads/2019/05/angular-8-httpclient-4439-01.jpg)

**Angular 8 HttpClient & Http 教程**今天要在这个博客里讨论。我们将在 Angular 8 项目中学习创建和使用 RESTful APIs。为了管理远程服务器上的数据，我们使用 HttpClient API 进行 GET、POST、PUT 和 Delete 操作。我们需要在 Angular 8 项目中导入并设置 HttpClient 服务来使用 REST APIs。

要在 Angular 中使用 HttpClient 服务，您需要在`app.module.ts`文件中导入`HttpClientModule`。然后在构造函数方法中注入 HttpClient 服务，之后就可以通过 HTTP 的 POST、GET、PUT 和 DELETE 方法访问远程服务器。

## HttpClient 在 Angular 8

HttpClient API 服务用于在前端 web 应用程序和后端服务之间进行通信。这种通信是通过 HTTP 协议完成的。Angular 8 HttpClient 服务使得与远程服务器的通信变得非常容易。需要通过@angular/common/http 包导入。

### http client 服务的好处

*   易于测试
*   类型化的请求和响应对象
*   请求拦截
*   响应拦截
*   API 可观察支持
*   简单的错误处理

#### [angular . io](https://angular.io/api/common/http/HttpClient#description)关于 HttpClient 说了什么？

HttpClient 是一个可注入的类，具有执行 HTTP 请求的方法。每个请求方法都有多个签名，返回类型根据被调用的签名(主要是 observe 和 responseType 的值)而有所不同。”

## 角形 8 HttpClient 方法

*   请求()
*   删除()
*   获取()
*   补丁()
*   帖子()
*   放()
*   头部()
*   jsonp()
*   选项()

[点击此处阅读更多信息](https://www.positronx.io/angular-8-httpclient-http-tutorial-build-consume-restful-api/)
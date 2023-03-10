# 无服务器常见问题

> 原文：<https://dev.to/3sanket3/serverless-faqs-akl>

## 没有服务器？真的吗？😲

哈哈，不要跟名字的字面意思走。有服务器🤦‍，只是你不需要像往常一样管理。云提供商将动态管理资源的分配。

您只需编写功能并部署到云中。云提供商将把你的功能存储在存储器中。当您要求运行该功能时，他们将从存储中提取，运行它并完成。

## 我将如何被收费？💸

它将只对你的函数的计算时间收费。所以，当你的代码不运行时，不收费🤑

## 它支持我的编程语言吗？👨‍💻

这取决于云提供商。以下是云提供商支持的语言列表。

*   [**AWS:**](https://aws.amazon.com/lambda/) Java、Go、PowerShell、Node.js、C#、Python、Ruby
*   [**谷歌云:**](https://cloud.google.com/functions/) Go，Node.js，Python
*   [**Azure:**](https://azure.microsoft.com/en-in/services/functions/)PowerShell、Node.js、C#、Python、F#、Java

还有许多其他云提供商支持运行无服务器功能，如 [Cloudflare](https://www.cloudflare.com/en-in/products/cloudflare-workers/) 、 [IBM Openwhisks](https://www.ibm.com/in-en/cloud/functions) 、 [Kubeless](https://kubeless.io/) 等。

## 我可以灵活地使用任何版本的运行时环境吗？👩‍🔧

不，您的代码将在它们支持的版本上运行。您可以从相应提供商的文档中找到支持的版本。比如，AWS 你可以在这里找到。

## 有什么框架指导我写无服务器功能或者项目？👨‍🏫

*   https://serverless.com 是供应商不可知的，支持大多数语言。
*   用于 JavaScript 的 https://claudiajs.com/
*   [https://www.zappa.io/](https://www.zappa.io/)为 Python
*   [AWS 生态系统的无服务器应用模型](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html)
*   https://serverlesslibrary.net:Azure 无服务器社区的图书馆

## 如果服务器根据请求运行，而不是一直准备就绪，那么响应我的请求不是需要时间吗？😴

是的，当你触发一个函数时，你可能会经历延迟。

当您第一次调用该函数时，需要花费时间来设置容器和引导应用程序。这被称为“冷启动”。但是对于后续请求，云提供商会尝试重用容器。它取决于云提供商和其他基础设施因素，它将保持容器存活多长时间。因此，他们总是建议以不假设容器将被重用的方式编写代码。

“冷启动”时间取决于各种因素，如:

*   编程语言——静态类型语言需要更多的时间
*   当您更新功能和部署时，它会破坏容器
*   函数被调用的频率，并保持容器“温暖”。
*   代码大小等。

在大多数情况下，这不是一个大问题。但是，如果你想避免冷启动，你可以安排一个函数，每隔一段时间就调用你想保持温暖的函数。

## 如何触发已部署的功能？⚡️

它们可以通过多种方式触发:

*   部署后，您将获得一个专用的 HTTP URL，使用它我们可以调用
*   您可以配置 HTTP URLs 作为 REST Apis 使用
*   该功能可以计划在特定时间运行
*   在其他云基础架构事件上，如存储的数据更改事件等。

## 有没有享受无服务器的例子？😎

你可以找到由框架和云提供商管理的案例研究，比如无服务器框架的[案例研究](https://serverless.com/learn/case-studies/)和 AWS Lambda 的[案例研究](https://aws.amazon.com/lambda/resources/customer-case-studies/)

如果您还没有尝试过无服务器，我建议您开始尝试。最简单的方法是，让你的端项目的后端功能没有服务器。如果你还没有实现你的第一个想法，无服务器将真正有助于消除服务器管理的头痛和船舶你的想法很快🚀
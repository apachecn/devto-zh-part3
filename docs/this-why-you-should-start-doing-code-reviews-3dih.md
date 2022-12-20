# 这就是你应该开始做代码审查的原因

> 原文：<https://dev.to/ogfris/this-why-you-should-start-doing-code-reviews-3dih>

#### ***大家今天应该开始做代码评审在我看来主要有三个原因:***

## **1)提高代码的效率和性能。⚡**

首先，当你写代码时，你总是倾向于选择最简单和最快的方式。例如，我目前正在为网站 [Anime Twist](https://twist.moe) 编写一个新的搜索引擎，因为我们现在使用的这个搜索引擎是在浏览器端运行的，性能非常差，这会导致设备不好的用户死机。我选择用 Elasticsearch 来完成这项任务，所以当我在使用迁移功能时，我通过向数据库发送一个新的插入请求来添加每个条目，直到我的团队成员给了我一个代码审查，并告诉我应该使用 bulk，我知道这一点，但我没有应用它，直到有人告诉我。

## **2)提高代码质量。👌**

一般来说，当我们独自编写代码时，我们不太关心代码的质量，只要它能工作，没有测试和文档。这是我经常做的事情，非常糟糕，尤其是当你是像我一样的后端工程师时，如果没有测试，你不知道你的代码是否工作正常。但是，当我要求进行代码评审时，他们称赞我的代码，这给了我很大的动力，让我想写出我所写过的最好的代码。

## **3)安全。🛡**

通常，当你写代码时，你会考虑它应该如何工作，但从来不会考虑它不应该如何工作，这会导致更多的漏洞。对于这一点，我的建议是，从将要使用你的工作的相反的开发领域获取代码评审。例如，后端开发人员可能不知道他们的 API 将如何在前端使用，所以他们大部分时间都会因为缺乏经验而犯下巨大的错误。

### ***总之，不知道你有没有看到，但是 Github 有一个给代码添加注释的功能。***
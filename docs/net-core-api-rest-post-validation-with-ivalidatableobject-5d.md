# 。使用 IValidatableObject 进行 NET Core API REST 后验证

> 原文：<https://dev.to/cesarcodes/net-core-api-rest-post-validation-with-ivalidatableobject-5d>

我最近开始在我的。NET 核心 API 来封装验证逻辑。这有助于我分离关注点，并让我的控制器保持精简。例如，假设我们需要编写一个端点，返回给定日期的所有项目…

一种选择是编写一个 GET 方法，接受一个`date`和`resourceGroup`输入参数。这是可行的，但问题是参数的验证逻辑会导致控制器中的额外代码，理想情况下，我们希望我们的控制器尽可能精简。

更好的选择是使用接收 JSON 对象的 POST 端点，该对象对应于扩展了`IValidatableObject`接口的 C#模型。假设您的 API 接收以下数据:
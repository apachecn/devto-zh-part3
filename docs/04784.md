# WebSockets 有什么 API 规范格式吗？

> 原文：<https://dev.to/idanarye/are-there-any-api-specification-formats-for-websockets-2mkk>

WebSocket 协议允许在服务器和客户端之间来回发送消息，但是它没有定义这些消息的结构。它们可以是字符串或二进制大对象，但是如何构造这些字符串/二进制大对象由用户决定。

这并不是一件坏事——你希望这个抽象层简单，这样就容易实现和构建——但是对于 HTTP 来说——在这方面是类似的(你只能发送带有一些头的明文),有许多建立在它之上的协议增加了这个结构: [OpenAPI](https://www.openapis.org/) ， [OData](https://www.odata.org/) ， [XML-RPC](https://en.wikipedia.org/wiki/XML-RPC) 等等。

其中一些协议提供了模式生成器——您只需以协议最喜欢的格式编写一次协议规范，它就会生成类型和/或函数，以便在许多不同的语言中使用您的协议。

我试图为 WebSocket 寻找类似的东西，但我能找到的只有 [AsyncAPI](https://www.asyncapi.com/) 。它有一个[生成器](https://github.com/asyncapi/generator)，可以使用模板输出——但它只有 Java(实际上是 Spring)、HTML 和 Markdown(最后两个用于生成文档)的模板，谷歌搜索第三方模板没有任何结果(除了他们的问题跟踪器的结果，该跟踪器请求添加更多语言的模板)。

还有比 AsyncAPI 更完整的吗？如果这是我们最好的，我会使用它——良好的文档生成已经是一个额外的奖励，我可能会为我使用的语言贡献自己的模板——但是如果有更好的，我宁愿只使用它。
# RFC: JSONx 框架

> 原文：<https://dev.to/sevasafris/rfc-jsonx-framework-4jmc>

嗨，开发者社区，我想分享我两天前发布的一个开源项目，并请求评论。该项目名为 **JSONx Framework** ，它的创建是为了帮助开发者在 Java 中使用 JSON。我在 2015 年开始这个项目，并投入了数百小时。简而言之， **JSONx 框架**提供了:

1.  例如，JSON 模式定义语言(JSD)，用于消费者驱动的契约。
2.  一个用于绑定到 jsd 的 Java API，用注解实现以实现轻耦合。
3.  一个 JSD->Java 绑定生成器，一个 Java->JSD 逆向生成器。
4.  一个`jsonx-maven-plugin`，例如用于在`generate-sources`阶段生成绑定类。
5.  一个用于`MessageBodyReader`和`MessageBodyWriter`的 JAX-RS 提供程序，用于插入 JAX-RS 运行时。
6.  一个名为“消费者驱动契约”的示例项目。

该项目通过一个定制的测试框架进行了彻底的测试，该框架生成随机的 JSON 文档(每次运行 392000 个),符合一个表达 JSD 语言的完整声明范围的模式，包括所有成功和失败的案例。
项目可在此获得: [JSONx](https://github.com/jsonx-org/) 。
我将非常感激一瞥、一颗星或尝试一下。对于我来说，发布这个已经是很久很久的事情了，我很高兴能为开源社区贡献这份工作。
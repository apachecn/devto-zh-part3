# AWS 峰会回顾:Lambda、Cloudformation 等的新特性

> 原文：<https://dev.to/raoulmeyer/aws-summit-recap-what-s-new-for-lambda-cloudformation-and-more-4la>

AWS 定期在世界各地举办峰会，本周我参观了阿姆斯特丹的峰会。加入这些真的很好，首先因为它们是免费的，其次因为它们非常专注于教你你不知道你可以用 AWS 做的事情。

在这篇文章中，我将简要回顾我学到的一些东西。当然，我只能参加几个讲座，其中大部分讨论的是 Lambda 和 DynamoDB 等无服务器 AWS 服务。

## λ性能

在其中一个讲座中，分享了一个关注 lambda 性能的博客。我没有看完所有的帖子，但是我点的第一个真的很有用。在 Node.js lambdas 中，不需要整个 AWS SDK，您可以只需要您需要的部分，这样可以大大改善 lambda 的冷启动时间。这里可以阅读更多[。](https://theburningmonk.com/2019/03/just-how-expensive-is-the-full-aws-sdk/)

另一个改进 lambda 包大小和冷启动时间的有趣方法是使用 webpack 来编译您的 lambda。例如，您可以应用[树摇动](https://webpack.js.org/guides/tree-shaking/)来从依赖关系中丢失未使用的代码。

AWS 表示，他们正在寻找解决在 VPC 运行的 lambdas 冷启动时间长的方法。目前，在 VPC 中运行时，lambda 可能需要几秒钟才能启动。如果你使用 lambda 向客户提供某种内容，几秒钟是相当痛苦的。不幸的是，他们没有分享修复的时间表，但很高兴知道他们正在努力。

## λ层

[Lambda layers](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html) 已经有一段时间了，但我还是想强调一下，因为它相当强大。它允许你打包一个由一些文件组成的层，例如你的 lambda 所依赖的库或二进制文件。然后，您的 lambda 可以在该层之上构建，使用该层中可能需要的任何东西。这种设置与 Docker 容器的构建方式非常相似，每个命令都会创建一个新层。

类似于 Docker，这允许你创建基本的 Lambda 图像。如果你愿意，可以使用第三方提供的图层[。有了](https://aws.amazon.com/blogs/apn/apn-partners-offer-lambda-layers-and-custom-runtimes/)[定制运行时支持](https://aws.amazon.com/about-aws/whats-new/2018/11/aws-lambda-now-supports-custom-runtimes-and-layers/)，这意味着你将能够找到一个预建层，它将为几乎任何流行语言添加运行时。

## 云形成好东西

使用 [Cloudformation 宏的](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-macros.html)，您可以大大减少需要编写的样板 Cloudformation 的数量。本质上，这就是 [SAM(无服务器应用模型)](https://github.com/awslabs/serverless-application-model)已经为 Lambda/DynamoDB/ApiGateway 所做的事情。我注意到我写的大多数 Cloudformation 都非常相似，有时会调整一两个属性。重用已知符合最佳实践和安全指南的基本资源配置似乎也是一个不错的主意。

作为 yaml/json Cloudformation 的替代方案， [AWS CDK](https://github.com/awslabs/aws-cdk) 允许将您的基础设施指定为 javascript/typescript、Java 或. NET。这样，您可以指定自己的构造(如无服务器功能、负载平衡器等)。)并在您的堆栈中轻松使用它们。对我来说，这听起来像类固醇上的云形成。这种灵活性是 yaml 或 json 难以实现的。如果你有兴趣，一定要看看[的例子](https://github.com/aws-samples/aws-cdk-examples)。

## 示例应用

AWS 有一个很棒的回购示例目录。除了像[架构良好的框架](https://aws.amazon.com/architecture/well-architected/)这样的一般建议之外，这些示例应用程序对于了解什么样的解决方案是可能的和通用的非常有用。演示了一个有趣的[示例应用](https://github.com/aws-samples/aws-bookstore-demo-app)，它包含一个相当大的基础设施，将几个数据存储连接在一起。这个具体的例子将几个服务链接在一起，包括 DynamoDB、Elasticsearch、Elasticache 和 Neptune，以创建一个基本的电子商务平台。

## 结论

AWS 一直在发展，这些峰会是了解最新进展的好方法。为了强调这一点，我学到的大部分东西都是在过去的 6 个月里添加的。如果你正在使用或打算以任何方式使用 AWS，我强烈推荐你在有机会的时候去参观你附近的峰会。
# 掌握模型部署的神秘艺术，第 2 部分:使用

> 原文：<https://dev.to/aws/mastering-the-mystical-art-of-model-deployment-part-2-deploying-amazon-sagemaker-endpoints-with-323n>

### 掌握模型部署的神秘艺术，第 2 部分:使用 AWS CloudFormation 部署 Amazon SageMaker 端点

在之前的[文章](https://dev.to/juliensimon/mastering-the-mystical-art-of-model-deployment-2afi)中，我讨论了机器学习模型的几种部署策略，并向您展示了如何使用 [SageMaker](http://aws.amazon.com/sagemaker) [SDK](https://github.com/aws/sagemaker-python-sdk) 在同一个预测端点上部署多个模型。

这一切都很好，但在生产环境中，我敢打赌，你们大多数人更喜欢用[云形成](http://aws.amazon.com/cloudformation)来正确地自动化。一键式部署、自动清理等等:有什么不喜欢的？

在这篇文章中，我将向你展示如何:

*   **部署由单一模型支持的 SageMaker 端点**，
*   **通过添加更多实例来缩放端点，**
*   **向端点**添加一个额外的生产变量，即向端点添加第二个模型并设置加权循环。
*   用漂亮的 YAML [模板](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/gettingstarted.templatebasics.html)和[变化集](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets.html)以正确的方式做事。

> 像往常一样，所有代码都可以在 [Gitlab](https://gitlab.com/juliensimon/sagemaker-automation/tree/master/cloudformation) 上获得。

好吧，魔法时间！

<figure>[![](img/b744ac4936252dafa7ca0ef2b99208bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yKONE7vT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/540/0%2Abbaw5i7z11hQ9YZS.gif) 

<figcaption>而这，我的朋友，就是你如何部署两个生产变种。</figcaption>

</figure>

### 部署 SageMaker 端点

首先，让我们编写一个允许我们部署单一模型的模板。我们需要为[模型](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-sagemaker-model.html)、[端点配置](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-sagemaker-endpointconfig.html)和[端点](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-sagemaker-endpoint.html)本身创建云形成资源。我尽量让模板简单明了，它应该适用于任何单容器模型

> 对于[推理管道](https://docs.aws.amazon.com/sagemaker/latest/dg/inference-pipelines.html)，您必须列出模型资源中的所有容器。
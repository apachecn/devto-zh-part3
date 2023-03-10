# 多云，有可能是含咖啡因的查询编排 AWS Athena SDK for Java 的新 rJava 包装器

> 原文：<https://dev.to/hrbrmstr/cloudy-with-a-chance-of-caffeinated-query-orchestration--new-rjava-wrappers-for-aws-athena-sdk-for-java-4fp2>

有两个基于 rJava 的 R 包可以与 AWS SDK for Athena 协同工作:

*   [`awsathena`](https://git.sr.ht/~hrbrmstr/awsathena)|[GL](https://gitlab.com/hrbrmstr/awsathena)|[GH](https://github.com/hrbrmstr/awsathena)
*   [`awsathenajars`](https://git.sr.ht/~hrbrmstr/awsathenajars)|[GL](https://gitlab.com/hrbrmstr/awsathenajars)|[GH](https://github.com/hrbrmstr/awsathenajars)

它们都需要符合 CRAN 的方式，比如提交的基于 rJava 的包也有很大的 JAR 依赖性。我们的目标是最终在 AWS Java SDK 菜单下为人们需要的任何东西提供包装器。

一旦每个包达到 90%的 API 覆盖率，通过 CRAN 检查并通过 Travis 检查，所有包对最终将在 [Cloudy R 项目](https://github.com/cloudyr)下共存。

我一开始做的一件事就是[异步`dplyr`链式查询执行`collect_async()`](https://github.com/hrbrmstr/awsathena/blob/master/R/collect-async.R) ，所以如果你需要它并且不想使用网状包装器，现在你的机会来了。

你可以正确地假设这是最近更新`metis` 的[工作的一个分支。我这样做的主要动机是从我们的 Dockerized 生产设置中移除对`reticulate`的依赖，但是我也发现我更喜欢 Java 库而不是基于`boto3`的库(如果你知道我对 Python 的看法，这并不奇怪)。因此，我应该能够快速包装您可能需要的几乎任何库(见下文)。](https://dev.to/hrbrmstr/conquering-caffeinated-amazon-athena-with-the-metis-trio-of-packages-1mgb)

### 鳍

下一个主要的包装器是 S3(现在在`awsathena`中已经实现了一部分，但那是暂时的),现在，你可以在这里发表评论，或者在任何你喜欢的社交编码网站上提出问题，优先包装其他 AWS Java SDK 库。此外，如果你想在一个自由判断的区域中体验使用 rJava 包，请在这些或任何新的基于 AWS rJava 的包 repos 中留言，我会很乐意带你完成你的第一个 PR。
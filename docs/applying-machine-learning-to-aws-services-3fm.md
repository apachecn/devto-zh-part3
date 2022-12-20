# 将机器学习应用于 AWS 服务

> 原文：<https://dev.to/juliensimon/applying-machine-learning-to-aws-services-3fm>

### 在 AWS 服务中应用机器学习

当使用机器学习时，人们可能会很快被技术细节淹没，并忘记最初的目的:我们如何使用过去的数据来回答未来的业务问题？

这就引出了一个问题:**这真的是我们想要玩的游戏吗？或者机器学习可以变得透明并自动完成它的任务吗？**

[![](img/75e907b26dc872a24a55086ed6cc57ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VWSoE8E7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AD_j_iz4DcYRNiO58MbWJzg.jpeg)

事实证明，许多 AWS 服务提供了内置的机器学习功能，使用起来完全不需要任何工作。让我们回顾一下所有这些，看看我们能享受多少免费搭车。

### 计算—预测自动缩放

在构建平台时，确定计算基础架构的规模可能是最困难的事情。容量规划是一门黑暗的艺术，尤其是在像初创公司这样快速发展、不可预测的环境中。当然，云计算给我们带来了[弹性的、随需应变的虚拟机](https://aws.amazon.com/ec2/)，几乎完全解决了这个问题。

但是，基础设施太多了，成本也需要管理。多年来， [Amazon Auto Scaling](https://aws.amazon.com/autoscaling/) 已经帮助开发人员调整了他们的计算平台。不过，公平地说，调整阈值、警报和扩展策略已经成为一门独立的黑暗艺术，正如由全能的网飞主持的 re:Invent 分组会议所展示的那样。

[https://www.youtube.com/embed/PideBMIcwBQ](https://www.youtube.com/embed/PideBMIcwBQ)

本着化繁为简的精神，我们在 re:Invent 2018 上推出了 [**预测自动缩放**](https://aws.amazon.com/blogs/aws/new-predictive-scaling-for-ec2-powered-by-machine-learning/) 。Jeff Barr 说:“*使用从您的实际 EC2 使用中收集的数据，并进一步了解从我们自己的观察中提取的数十亿个数据点，我们使用训练有素的机器学习模型来预测您的预期流量(和 EC2 使用)，包括每天和每周的模式*”。

这一新功能将节省你大量的实验和猜测…以及对键盘，桌子和咖啡机的无意识的随机暴力行为。最酷的是，该模型每 24 小时重新评估一次，以适应不断变化的交通模式。在本次主题为“创新”的分组会议中，您将了解到更多信息，本次会议由 Genesys PureCloud 主讲。

[https://www.youtube.com/embed/EHH5LuVbJBw](https://www.youtube.com/embed/EHH5LuVbJBw)

### 存储—智能分层

管理存储是您在构建平台时很快面临的另一个问题:更多的用户、更多的合作伙伴、更多的数据要记录等等。流动从未停止。与计算类似，云存储需要既有弹性又有成本效益，这正是[亚马逊 S3](http://aws.amazon.com/s3) 自推出以来一直努力的目标。

多年来，已经引入了更多的[存储类别](https://aws.amazon.com/s3/storage-classes/):

*   [亚马逊冰川](https://aws.amazon.com/blogs/aws/archive-s3-to-glacier/) (2012)，一种低成本的数据存档存储服务，
*   [不经常访问](https://aws.amazon.com/blogs/aws/aws-storage-update-new-lower-cost-s3-storage-option-glacier-price-reduction/) (2015)对于不经常访问的对象。
*   [单区域非频繁访问](https://aws.amazon.com/blogs/aws/amazon-s3-update-new-storage-class-general-availability-of-s3-select/) (2018)，以减少冗余为代价额外节省 20%。

按照真正的 AWS 方式，开发人员现在可以通过编写[生命周期配置](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lifecycle-mgmt.html)来自动将他们的 S3 对象从一个类迁移到下一个类。

不可避免的事情很快发生了，在 re:Invent 2018 上推出了 [**智能分层**](https://aws.amazon.com/blogs/aws/new-automatic-cost-optimization-for-amazon-s3-via-intelligent-tiering/) 。Jeff Barr 说:“*这种存储类别包含两个访问层:频繁访问和非频繁访问。两个访问层都提供了与标准存储类相同的低延迟。S3 智能分层可以监控访问模式，并将连续 30 天未被访问的对象移动到不频繁访问的层，只需支付少量的监控和自动化费用。如果数据稍后被访问，它会自动移回频繁访问层。底线:即使在不断变化的访问模式下，您也可以节省资金，没有性能影响，没有运营开销，也没有检索费用*。

在本次主题为“重新发明”的分组讨论中，您可以了解有关智能分层的更多信息，主题为 Pinterest。

[https://www.youtube.com/embed/FEaFuN03g-o](https://www.youtube.com/embed/FEaFuN03g-o)

### 存储—数据保护

扩展存储还不算成功:如何确保您的数据安全？当然，S3 提供了像桶策略、ACL 或加密这样的功能来管理和保护您的桶和对象。以下是最近对这些话题的深入探讨。

[https://www.youtube.com/embed/vPgXx_zlEx0](https://www.youtube.com/embed/vPgXx_zlEx0)

尽管如此，错误还是会发生:不正确的配置、将敏感文件放在错误的位置、公开存储桶等。面对现实吧，这是个‘何时’的问题，不是‘如果’的问题。当这些问题出现时(最近确实出现了)，唯一重要的是你能多快发现并修复它们:每一秒都很重要！

为了帮助组织避免这些问题，我们在 re:Invent 2017 上推出了 [**亚马逊 Macie**](https://aws.amazon.com/blogs/aws/launch-amazon-macie-securing-your-s3-buckets/) 。Macie 特别使用了一个基于 [**支持向量机的分类器**](http://This%20classifier,%20managed%20by%20Macie,%20was%20trained%20against%20a%20large%20corpus%20of%20training%20data%20of%20various%20types%20and%20has%20been%20optimized%20to%20support%20accurate%20detection%20of%20various%20content%20types,%20including%20source%20code,%20application%20logs,%20regulatory%20documents,%20and%20database%20backups.%20The%20classifier%20can%20also%20generalize%20its%20detections.%20For%20example,%20if%20it%20detected%20a%20new%20kind%20of%20source%20code%20that%20doesn't%20match%20any%20of%20the%20types%20of%20source%20code%20that%20it%20is%20trained%20to%20recognize,%20it%20can%20generalize%20the%20detection%20as%20being%20%22source%20code.%22) 来对存储在你的 S3 桶中的对象进行分类。

该文件称:“*这个由 Macie 管理的分类器是根据各种类型的大量训练数据进行训练的，并已进行优化，以支持对各种内容类型的准确检测，包括源代码、应用程序日志、监管文档和数据库备份。分类器也可以概括它的检测。例如，如果它检测到一种新的源代码，它不匹配它被训练识别的任何类型的源代码，它可以将检测概括为源代码*。

Macie 还使用机器学习来分析 AWS CloudTrail 日志，以便检测未经授权的访问和数据泄漏。你可以在 Edmunds.com 主持的分组会议中了解更多信息。

[https://www.youtube.com/embed/LCjX2rsQ2wA](https://www.youtube.com/embed/LCjX2rsQ2wA)

### 网络—安全监控

网络安全也面临类似的挑战。尽最大努力阻止野蛮人是不够的。无论是他们造成的还是您自己造成的，违规行为迟早都会发生，您最好做好检测、记录、修复并运行详细的取证分析的准备，以确保这种情况不会再次发生。

不幸的是，构建一个可靠的安全监控系统是相当复杂的，即使是在合理的规模上:记录、分析、适应新的威胁、补救等等。这是一个全新的平台，需要构建和管理…

为此，我们在 re:Invent 2017 推出了 [**亚马逊守卫**](https://aws.amazon.com/blogs/aws/amazon-guardduty-continuous-security-monitoring-threat-detection/) 。杰夫·巴尔(Jeff Barr)说:“*guard duty 通过大量公共和 AWS 生成的数据信息，并由机器学习提供支持，分析了数十亿次事件，以寻求趋势、模式和异常，这些都是一些问题的可识别迹象。您只需点击一下就可以启用它，并在几分钟内看到第一批结果*。

一旦发现结果可用，就可以通过您自己的代码或第三方解决方案自动进行处理以进行补救。您可以在本次 re:Invent 2018 分组讨论中了解更多信息，该会议将向您展示如何使用 Splunk 等流行的解决方案来扩展 GuardDuty。

[https://www.youtube.com/embed/DygpSx1e3Dg](https://www.youtube.com/embed/DygpSx1e3Dg)

### 分析—清理数据

一旦您对您的存储基础架构相当满意，您就可以开始接收数据、对其进行编目、清理并为分析做准备。你当然知道如果你在处理数据，这一步可能会占用你 80%的时间…每次我问问题的时候，你都会举手示意。

最初，客户通过组装亚马逊 S3、亚马逊 EMR、AWS Glue 等服务构建了自己的解决方案。为了让事情更简单，我们在 re:Invent 2018 上预演了 [AWS 湖形成](https://aws.amazon.com/lake-formation/)。

湖泊形成的一个很酷的特征是 **ML 变换**。这些转换帮助您自动清理数据。以下是来自[常见问题解答](https://aws.amazon.com/lake-formation/faqs/)的一些例子:

> *   By using FindMatches on an independent database containing public fields such as name, birthday, home address, telephone number, etc., patient records are linked between hospitals so that doctors can have more background information and better treat patients.
>     
>     
> *   Deduct the data of the movie database containing columns such as title, synopsis, release year, running time and actors. For example, the same movie may be differently identified as "Star Wars", "Star Wars: New Hope" and "Star Wars: Episode IV-New Hope (Special Edition)".
>     
>     
> *   By identifying the equivalent products in the clothing catalogue, all related products in the storefront will be automatically combined together. In the clothing catalogue, you should define "equivalent" to indicate that they are the same, ignoring the differences in size and color. Therefore, "Levi 501 blue jeans, size 34x34" is defined as the same as "Levi 501 jeans-black, size 32x31".

在这个专题讨论中，您可以了解更多关于 AWS 湖形成的信息。

[https://www.youtube.com/embed/nsiLMqg654s](https://www.youtube.com/embed/nsiLMqg654s)

### 分析—流数据

当处理实时流数据时，提取见解的任何延迟都应保持在最低限度。由于亚马逊 Kinesis 是摄取流媒体数据的首选方式，我们在亚马逊 Kinesis 数据分析中添加了[机器学习功能](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/examples-machine.html)，这是 Kinesis 的一个扩展，可以让你对流媒体消息运行 SQL 查询。此时， [**热点检测**](https://aws.amazon.com/blogs/aws/real-time-hotspot-detection-in-amazon-kinesis-analytics/) 和 [**异常检测**](https://aws.amazon.com/blogs/big-data/real-time-clickstream-anomaly-detection-with-amazon-kinesis-analytics/) 可用。

您可以在这个以 Autodesk 为特色的 re:Invent 分组讨论中了解有关该服务的更多信息。

[https://www.youtube.com/embed/jhYIxM2KiLs](https://www.youtube.com/embed/jhYIxM2KiLs)

### 汇报— ML 洞察

正如本文开头所提到的，大多数机器学习实际上只涉及一件事:使用过去的数据来回答未来的业务问题。因此，如果我们可以在可视化数据时简单地从数据中提取见解，而不是经历构建模型、预测等通常的循环，这不是很好吗？？

这正是[亚马逊 QuickSight](https://aws.amazon.com/quicksight/) 现在所提供的，这要归功于 **ML Insights** ，这是[在 re:Invent 2018 上预演的新功能](https://pages.awscloud.com/QuickSight-ML-Insights-Preview.html)。现在，您可以检测异常，预测未来趋势，并构建描述您的仪表板的自然语言叙述。

我认为这是一种在没有任何专业知识的情况下使用机器学习的令人兴奋的新方法:你可以在这个 re:Invent breakout 中了解更多信息。

[https://www.youtube.com/embed/GATR05jEvMo](https://www.youtube.com/embed/GATR05jEvMo)

### 结论

正如你所看到的，在 AWS 的引擎盖下，正在发生越来越多的机器学习。这不仅使服务更加智能，还节省了大量的时间和复杂性:你能想象自己必须构建这些吗？

我不怎么盯着水晶球，但我希望在未来几年里看到这个方向的重大进展。没有理由为什么机器学习不能像其他任何东西一样普及，而且它应该普及。

几年前，在 re:Invent，沃纳·威格尔博士介绍了一种叫做 AWS Lambda 的奇怪的新服务，有一张幻灯片说:“没有服务器比没有服务器更容易管理”。我们中的许多人花了一段时间才理解他的意思，现在我们看到无服务器的愿景正在成为现实。

因此，本着同样的精神，让我们在此宣布:

> 没有机器学习比没有机器学习更容易管理。

这将是一条漫长的道路，但我们会到达那里。同意吗？不同意？很高兴在这里或在 [Twitter](http://twitter.com/julsimon) 上讨论功能想法并回答问题。

一如既往，感谢阅读。
# 使用机器学习可靠地检测日志中的异常

> 原文：<https://dev.to/gdcohen/how-structuring-logs-makes-anomaly-detection-work-2dc1>

拉里·兰卡斯特@ ZebriumLearns(经许可后发布)

在 Zebrium，我们有一句话:“结构第一”。我们经常谈论结构化，因为它允许我们用日志数据做令人惊奇的事情。但大多数人不知道我们说“结构”这个词是什么意思，也不知道为什么它允许像异常检测这样的惊人事情。这是对我们所指的“结构”的一个温和而直观的介绍。

几年前，我有一个愿景:如果软件可以像人一样将日志文件组织到数据库中，如果它可以与日志生成保持一致并保持模式更新，您将拥有一个基于日志构建的惊人平台。

Zebrium 的目标很简单:通过日志快速、自动地了解正在发生的事情。让我们来解释一下我们的无人监管的在线机器学习引擎是如何构建数据的。

**一个简单的例子:**一个日志片段。

[![image](img/2203ba93625677457c1975e7c3c1051e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SI1I7c86--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb01.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb01.jpg)

头脑结构:这里似乎有 4 种不同的“东西”。

[![image](img/53eadd694e999fab12753d8b86b93832.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jf3Io4aI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb02.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb02.jpg)

**重新整理:**这样更清楚。

[![image](img/52a13f0b70e1b19a40e713a30427605a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IbHA0nzb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb03.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb03.jpg)

更好的是:我们称这 4 个组块为 etypes(“事件类型”的简称)。

[![image](img/14844d5e3720e408b0f901328ee43d9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ktBA6m7m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb04.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb04.jpg)

日志管理软件通常会保留一个众所周知的前缀格式列表，并要求您为其他人创建解析表达式。我们不这样做。相反，我们自己学习与日志文件或流相关联的前缀。我们将尝试猜测好的名称，但您可以随时覆盖它们。

接下来是更令人兴奋的部分:确定由名称、结构和 T2 组成的 etype(事件类型)。我们从事件体和前缀中学习 evar 名称和类型。

让我们一次一个 etype 地浏览上面的四个日志文本块:

**第一个 etype:** 看看最右边两列的那些 yummy stats。异常检测的一种方式是在这样的时间序列中寻找特征。我们把这些栏目，比如“fun”和“wait_ms”， **evars** 。

[![image](img/b9930e7adfa5845188e6fd0865dc05e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9MEtJ2v2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb08.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb08.jpg)

有了这种层次的结构，你就可以确定时间序列中的每个数字都是同一个 evar，都与同一个 etype 相关联。在以后的文章中，我们将展示如何自动提取时间序列度量的维度并检测其中的异常。

**第二个 etype:** 注意，我们键入每个 evar。

[![image](img/6f18e4fadbe8f7349bd0492ccd3fbcb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tL8PKcxb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb07.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb07.jpg)

我们有简单类型的概念，如“float”和“str ”,也有复杂类型的概念，如文件路径和 MAC 地址。我们计划允许用户添加更多。

第三个 etype: 有时给 evar 命名有很好的提示，比如本例中的“coff”。

[![image](img/bb27436bcd7264ea5615485018631706.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YJmTnADR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb06.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb06.jpg)

**最后一个:**注意“raid_id”和“coff”也出现在前面的 etype 中。如果愿意，我们可以对这些列进行有效的内部和外部连接，将 etypes 视为表。

[![image](img/f8e5f2814e742157505dbcc52c0f2f05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oe30tJyq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb05.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb05.jpg)

**全部完成:**概念上，etype 是一个表，evar 是一个列。

[![image](img/9acaf290b78c56b7d2be5d85133714db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UzEfcpft--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb09.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb09.jpg)

另一种异常检测与 etype 出现的顺序有关；未来的文章将更详细地介绍我们的异常检测分析。作为一个超级简单的例子，每个 etype 第一次只会出现一次——这显然是某种异常，对吗？让我们找出答案。

我们运行一个亚特兰蒂斯服务器。作为测试的一部分，我们的一名工程师决定登录并关闭 Atlassian 应用程序背后的数据库。然后，我们收集了之前 19 个小时左右的所有日志来测试自动分类。

在测试期间，系统日志生成了 113 个事件:

**我们的 API 客户端:**从 CLI 查询和上传数据:CSV、JSON 或 pretty-print。

[![image](img/ca1ae23be0c0c74eedc3cb0edd507ecf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8JA_S9u9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb11.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb11.jpg)

这些事件中只有两个事件的 etypes 是以前从未见过的:

**微小异常检测:**如果某件事第一次发生，它可能是诊断性的。

[![image](img/81047860f4b83ff038c83308c9c941e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ptQRiSVf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb10.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb10.jpg)

到目前为止，一切顺利:我们的异常检测发现了相关的两行，仅仅是因为 etypes 是新的。现在，假设我们想查看一个异常词类的所有数据，以便更好地理解它。我们提供了两种方法来从 CLI 查询给定 etype 的数据:

ze API 客户端:简单点说吧。
T3![image](img/ec8ac6ce5dc61dd05be043436f32f6d1.png)T5】

**zsql SQL 客户端:**让我们添加第一次出现的时间戳。

[![image](img/a8511ca7acb85c0b2f30f1fb319b99d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u3MALq9x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/larry_blog_anomaly_cb13.jpg%3Fwidth%3D2880%26name%3Dlarry_blog_anomaly_cb13.jpg)

现在我们已经看到 etypes 和 evars 如何为您提供开始从日志数据进行异常检测或任何类型的分析所需的所有基本构件。如果您对我们的测试计划感兴趣，或者只是想联系我们，[请告诉我们](https://www.zebrium.com/preregister-for-beta)！
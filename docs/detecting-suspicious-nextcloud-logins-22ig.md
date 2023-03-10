# 检测可疑的 Nextcloud 登录

> 原文：<https://dev.to/christophwurst/detecting-suspicious-nextcloud-logins-22ig>

随着 [Nextcloud 16](https://nextcloud.com/blog/nextcloud-16-introduces-machine-learning-based-security-and-usability-features-acl-permissions-and-cross-app-projects/) 我们发布了一个新的安全插件: *[可疑登录](https://apps.nextcloud.com/apps/suspicious_login)* 。这款应用程序可以使用人工神经网络的监督学习来检测登录用户 Nextcloud 帐户的 IP 地址的异常。

## 概述

每当用户登录 Nextcloud 时，就会加载一个经过训练的神经网络模型，并用于将 IP 和用户 ID (UID)的组合分类为可疑或不可疑。在前一种情况下，用户会立即收到一个发送到桌面和移动客户端的(推送)通知。

[![Nextcloud Android app showing a suspicious login into the user's account](img/1f39e7d4193f7baad028434bd4c792c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w75MGM_N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.wuc.me/assets/20190424_nextcloud_suspiciou_login_detection/notification_android.png)

然后，受影响的用户可以更改他们的密码或取消任何他们看起来不熟悉的设备令牌。

## 您的云，您的数据

为了训练和使用神经网络对登录进行分类，我们需要一些好的训练数据。虽然我们可以发布一个预先训练好的模型，它已经学会了 ip 地址的结构，并且实际使用的时间更短，但我们知道 Nextcloud 管理员和用户重视隐私。因此，应用程序只使用实例中的数据。

*可疑登录*应用将从每次登录和每次客户端连接中收集(IP，UID)元组。经过几周的收集，这些数据可以用于训练。训练数据会像训练模型一样不断更新。

由于训练数据只从实例中收集，分类器应该不是一个黑盒，而是在重视用户数据隐私的同时提供高度定制的结果。

## 采集*好的*训练数据

为了获得可能的最佳分类器，为神经网络提供一组良好的训练数据非常重要。成功登录形成正数据集。但我们也需要阴性样本。由于我们在理想情况下没有这样的数据，我们必须伪造它。

### 随机抽样

获取随机数据样本的最简单方法是使用随机数生成器来生成 IP 地址。结合现有用户，它们模拟从与用户以前的登录源非常不同的 IP 地址登录。

### 混洗样品

神经网络不仅应该警告它以前没有见过的 IP，而且应该在用户试图登录另一个用户的帐户时发出警告。这对于同一个实例的许多用户从同一个网络使用 Nextcloud 的环境不太相关，但对于共享实例(例如托管提供商或远程公司)最有意义。

因此，该应用程序从积极的训练集中随机挑选数据，并将 uid 与特定用户以前没有登录过的 IP 混合。

## 分离训练和验证数据集

分类器的性能可以通过用它以前没有见过的数据集测试来确定。在许多机器学习任务中，这是通过将数据行随机分成训练集和验证集来完成的。由于用户的登录历史不是随机的，而是遵循一定的模式，因此只有最近的数据用于验证，而历史数据用于训练。

## 建筑输入层

设计机器学习应用的另一个重要考虑是选择好的输入表示。原始数据是 UID 字符串和 IPv4 字符串。这些必须被转换成数值，同时既保持输入值之间的平衡，又使神经网络能够学习 IP 地址的结构和层次。

这是投入最多精力的部分，也是分类器性能改进最大的部分。我没有向您详细介绍我为获得一个好的输入表示所做的失败尝试，但是如果您感兴趣，可以在 git 历史中找到它😉。

到目前为止，我发现的最好的表示方法是将 UID 散列为一个 16 位二进制向量。这降低了值的范围，同时仍然具有相对较低的冲突概率。IP 地址也表示为其 32 位二进制向量。结合起来，这构成了 48 位输入层。

## 明智的训练参数

现在我们有了训练数据和正确的表示，我们可以仔细看看训练的参数。 [php-ml](https://php-ml.org/) 的多层感知器有[这些自由参数](https://php-ml.readthedocs.io/en/latest/machine-learning/neural-network/multilayer-perceptron-classifier/#constructor-parameters):

*   层
*   迭代次数(时期)
*   学习率
*   激活功能

我已经用不同的参数值进行了多次训练。我们的目标是找到一种方法，它对分类为可疑的登录有很高但平衡的精确度和召回率，同时不会消耗太多的计算时间。对于小型、中型和大型安装，默认的参数集也应该工作得比较好。在用参数值的许多不同组合进行了几百次训练之后，我发现 150 个时期、14 层和 7%的学习率可以满足这些要求。我还实现了一个启发式*模拟退火*算法，该算法从随机参数开始，并在一定范围内用递减修改的随机值迭代测试这些参数。这些运行需要很长时间，因为每个参数集都要多次使用，以补偿训练神经网络的随机性。最终找到的最佳解决方案通常也是在我手工制作的领域。

除了训练参数，该应用程序还可以配置为可用的积极数据生成多少随机和洗牌数据的比率。作为一个明智的默认设置，该应用程序将 150%的重组阴性样本和 100%的随机样本作为阳性样本。在这种配置下，精确度和召回率是最高的，也是最平衡的。

## 保持模型最新

当用户旅行或简单地通过他们的 ISP 获得新的 IP 时，训练模型一次并不奏效。但管理员实际上甚至不必触发任何培训，因为培训是在每天运行一次的 Nextcloud 后台作业中自动进行的。这确保了所使用的模型能够区分最近使用的 IP 的登录模式和新 IP 模式的登录模式。根据实例的大小，训练可能需要几分钟才能完成，但在任何功能相当强大的服务器上应该不会消耗太多资源。

## 结果

上面已经提到了精确度和召回率。这些二进制度量可以告诉训练模型对新登录数据的反应有多好。在这个应用程序中，我们只关心负面数据集，例如，检测到多少可疑登录，以及检测到多少确实来自可疑来源。

**precision(n)** 是分类为可疑的(UID，IP)元组集合中真正可疑登录的比率。因此，它告诉我们可疑登录警报应该向我们发出警报的可能性有多大(与假警报相反)。

**recall(n)** 是所有可疑登录集合中检测到的可疑登录的比率。因此，它可以指示有多少来自未知/不常见来源的登录将被捕获。

[![Historic precision and recall of suspicious login detection on a small Nextcloud instance](img/f298a542f9f4f7adad9175a45d69a1c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LOTZYeD0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.wuc.me/assets/20190424_nextcloud_suspiciou_login_detection/admin_settings.png)

我们已经在三个实例上连续测试了该应用程序，并监控了训练结果。Nextcloud 的用户越稳定(没有国际旅行，由 ISP 和手机运营商分配的类似 IP ),结果就越好。但是对于有旅行用户的大型实例，结果总是高于 80%的准确率(包括精确度和召回率)。

## 结论

这是我第一次将我关于神经网络的知识应用到实际的用例中。所以我很高兴到目前为止这个应用程序运行得非常好，我希望这个应用程序可以用来保护许多 Nextcloud 实例的数据。

和往常一样，代码是 100%开源的，在 GNU APGL 许可下使用。你可以在 [GitHub](https://github.com/nextcloud/suspicious_login) 上找到源代码，我希望在那里看到你的反馈、bug 报告和问题。

如果您是 Nextcloud 的新手，请查看我们的[网站](https://nextcloud.com/)！

## 回执

这个应用程序的许多想法来自于[亚马逊](https://aws.amazon.com/blogs/machine-learning/detect-suspicious-ip-addresses-with-the-amazon-sagemaker-ip-insights-algorithm/)和[这篇论文，这篇论文也对主题](https://arxiv.org/pdf/1701.02145.pdf)做了很好的概述。

* * *

这篇文章最初发表在我的个人博客上。dev.to 社区对这些开源特性博客感兴趣吗？请在评论中告诉我☺️
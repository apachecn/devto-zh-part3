# 微软奥尔良——我的第一次播客采访！

> 原文：<https://dev.to/kritner/microsoft-orleansmy-first-podcast-interview-4mld>

### 微软奥尔良——我的第一次播客采访！

最近，我接受了盖博节目主持人的采访，他的节目是《T2》。网芯播客“!

第 21 集——拉塞尔·哈米特的奥尔良

最近，我试着写更多的博客。过去，我断断续续地写博客，但是最近我写得更成功了；我希望我能保持下去！我的雇主[亨廷顿英格尔斯工业](https://www.huntingtoningalls.com/)(前身为 [G2 公司](https://www.g2-inc.com/))在过去的两年里让我参与了一个非常棒的项目——在这个项目中，我每天都需要学习更多的东西，巩固我已经掌握的知识。基本上，我想说的是我很幸运能在一家重视增长的公司工作！

GaProgMan 接近我(至少我觉得是他接近我？)关于对奥尔良做播客采访的可能性；他知道，由于我写的博客帖子，我至少有一些粗略的经验。在征得主管(我的经理和 NIST)同意后，我们安排了面试！

我不确定我们会在采访中谈论多少这个项目，但我希望无论如何都能把它说清楚。这一集现在已经发布，供您欣赏！

做播客对我来说很可怕，因为我是一个社交焦虑的人。最近，我一直在努力让自己更多地参与其中——通过博客帖子、游戏流和少量的指导。我想做得更多，也许开始教学和/或转换代码流，让我更多地走出我的舒适区，并在“肌肉”上努力；如果这种东西存在的话。

因为杰米和我谈到了我正在进行的项目，但不是特别深入，所以我想我应该在这里多谈一点。我与 NIST 合作了一个名为自动加密验证协议(ACVP)的项目:

美国国家安全局/ACVP

在这个项目中，我们致力于创建一个 API 来测试你的加密算法实现。在政府内部使用您的硬件/软件/固件需要这种验证(以及其他)。

从项目一开始，我们就知道最终需要将我们的加密调用“分发”到一个计算集群；因为我们预计必须为每个经过验证的算法运行成百上千个加密调用。这一点已经研究了一段时间，在围绕实现和易于管理等方面的概念证明之后。我们决定给微软奥尔良一个机会。Orleans 并不完全适合我们的用例，因为在文档中声明 Microsoft Orleans 旨在用于异步代码。通过研究，在我自己的时间里的博客帖子，试验和错误，我们似乎已经成功地将奥尔良应用到我们的项目中。

因此，你显然可以通过我写的关于这个主题的一系列帖子(在所有其他资源中)找到更多关于奥尔良的信息，但只是详细说明一下我们正在对奥尔良做些什么(因为我没有专门写过这个):

*   我们测试的每个算法都可能有数百到数千个潜在的不同加密调用。这些调用中有许多是在紧循环中发生的。
*   每个算法都是使用同步代码创建的，但 Orleans 期望异步代码。
*   我们将每个算法实现为它自己的粒度，使用观察者模式来订阅/取消订阅粒度，以发送和接收加密操作的“答案”。
*   由于 Orleans 期望一切都是异步的，我们不得不通过利用异步“进入”我们的粒度来解决这个问题，然后它立即将实际的 CPU 密集型工作分派到它自己的任务调度器上，并立即返回给调用者。
*   所使用的任务调度器只允许有限的并发集，其目的是永远不要让 CPU 完全“忙碌”,以便为异步 Orleans 任务运行程序留出一些空间，使其始终有“空间”来确认传入的工作。
*   通过 Orleans，我们可以在需要时增加或减少集群中的额外节点，以获得额外的计算能力。

这是我们如何使用 Orleans 的最基础的东西，我们试图用单独的调度程序实现的(非常)早期的概念证明可以在这里找到:

[问题/示例请求:通过 Orleans Issue # 4826 dot net/Orleans 执行长时间运行的函数](https://github.com/dotnet/orleans/issues/4826)

无论如何，请你们自己听这一集吧！我们以一种非常特殊的、非标准的方式使用奥尔良，所以我真的希望我没有把事情搞得太糟！杰米和我聊了一会儿我与 HII 和 NIST 合作的项目，还聊了很多关于奥尔良和茶的事情。

剧透警告:我的声音听起来比我想象的要书呆子气得多

相关:

*   上的播客插曲。杰米·泰勒的网络核心播客
*   亨廷顿英格尔斯工业公司
*   [微软奥尔良入门](https://medium.com/@kritner/getting-started-with-microsoft-orleans-882cdac4307f?source=friends_link&sk=1fc3451d71a19dcb49f2c8bbeb6b079e)
*   [微软奥尔良——再利用谷物和谷物状态](https://medium.com/@kritner/microsoft-orleans-reusing-grains-and-grain-state-136977facd42?source=friends_link&sk=f19cfa3f17665c3d700bfe0df56e27a9)
*   [微软奥尔良—报告仪表板](https://medium.com/@kritner/microsoft-orleans-reporting-dashboard-16465d255199)
*   使用多态性更新新奥尔良项目，为新奥尔良范例做好准备！
*   [微软奥尔良—依赖注入](https://medium.com/@kritner/microsoft-orleans-dependency-injection-6379d52a7169?source=friends_link&sk=6c3883a5213d65eb251b56c717e0e4f2)
*   [微软奥尔良——在“开发”和“生产”配置之间轻松切换。](https://medium.com/@kritner/microsoft-orleans-easily-switching-between-development-and-production-configurations-20e109be6458?source=friends_link&sk=1e8fc6aa072a5b293d029c00012165b3)
*   [。网络核心控制台应用程序选项配置](https://medium.com/@kritner/net-core-console-application-ioptions-t-configuration-ae74bfafe1c5?source=friends_link&sk=c5bcab4f7f10c97175ad68fc12cb9cc6)
*   [微软奥尔良—观察家](https://medium.com/@kritner/microsoft-orleans-observables-5e0040c949cd?source=friends_link&sk=bcb921fdf593bdc97b9c5909b2730f2d)
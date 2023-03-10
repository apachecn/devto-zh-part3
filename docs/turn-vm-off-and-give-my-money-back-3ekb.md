# 关掉虚拟机，把钱还给我

> 原文：<https://dev.to/rafalpienkowski/turn-vm-off-and-give-my-money-back-3ekb>

# 简介

在前一集，我熟悉了 Azure 中可用的支付方式和云模型。现在，我将进一步了解虚拟机(VM)的成本优化，在我看来，这是 IaaS 的基础。

### 虚拟机

首先，你需要知道 Azure 中的“典型”虚拟机由几个组件组成:

*   存储帐户—用于虚拟机的备份/快照和磁盘
*   磁盘—可以连接到虚拟机的可选磁盘
*   虚拟计算机
*   网络接口-使虚拟机能够与虚拟网络通信
*   网络安全组-用于允许或拒绝虚拟机的网络流量

[![VM](img/dd7fe4a965a32872bc53f7a630863fe8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1v5rS1R8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rafalpienkowski/resources/master/azure-cost-opt/vm.png)

您必须记住,“典型的”虚拟机实际上是几个元素的组合。为什么它如此重要？只有在虚拟机本身的情况下，费用是按它被使用的每一秒计算的。当虚拟机关闭时，不会产生任何费用。这给了我们什么？答案很简单。当你不需要的时候关闭你的虚拟机。例如，如果我们有一个专用于开发团队的虚拟机。它可以在下班时间被取下。我不是说一天去一两次 Azure 门户网站，手动操作。哦不，这不是我的本意。我们应该尽可能使我们的工作自动化。 [Azure Automation](https://azure.microsoft.com/en-us/services/automation/) 自带帮助。你不一定要有专业知识才能做到这一点。你可以通过点击 Azure 门户来实现。文章:[Azure Automation 中的非工作时间启动/停止虚拟机解决方案](https://azure.microsoft.com/en-us/services/automation/)是一个很好的起点。

[![Azure Scheduler](img/fc9436dc72df488377858724b4b3ef1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MzG4yi38--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rafalpienkowski/resources/master/azure-cost-opt/azure-scheduler.jpg)

Azure 门户中可用的调度程序并不完美。可用的计划选项有一次性、每月、每周、每天、每小时。在周末或国庆节关闭我们的虚拟机是不够的。幸运的是，我们可以编写自己的运行手册，定制调度程序的行为。[这里的](https://blog.kloud.com.au/2016/03/16/add-workdays-and-public-holidays-to-your-azure-automation-runbooks/)就是一个例子。好的一面是，我们还可以自动创建时间表。为此，我们需要使用 Azure 资源管理器(ARM)模板。ARM 是一个单独的帖子的主题，所以，现在，我只给感兴趣的人一个 Azure Automation PowerShell run book 文章中的[部署 Azure 资源管理器模板的链接。](https://docs.microsoft.com/en-us/azure/automation/automation-deploy-template-runbook)

因此，让我们来计算切换虚拟机可能带来的节约。为了进行计算，我采用了 DS3 V2 虚拟机(操作系统 Windows)，并假设该虚拟机将在每个工作日运行 13 个小时。平均一个月有 23 个工作日，所以我们有 299 个小时。下表比较了虚拟机全天候运行和仅在工作时间运行的成本。简而言之，我们每月可以节省 200 多美元。

[![VM pricing](img/b652577ad67a14225fcc1220784ec5e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZWH2FaXh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rafalpienkowski/resources/master/azure-cost-opt/vm_pricing.png)

但是万一我们的虚拟机必须全天候工作呢？毕竟，这不是一个不寻常的案例。在这种情况下， [Azure 保留虚拟机实例(RIs)](https://azure.microsoft.com/en-us/pricing/reserved-vm-instances/) 开始发挥作用。

[![Reserved](img/2e393468911b42a28cdc5cfdf26b214a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E54ewLYx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rafalpienkowski/resources/master/azure-cost-opt/reserved.jpg)

国际扶轮如何运作？我们正在申请一个特定时间(一年或三年)的虚拟机，并提前支付费用。对于前面提到的 DS3 v2 (Windows)虚拟机，将其保留一年可以节省大约 35%(保留三年可以节省大约 45%)。令人兴奋的是，使用 VM 和 Linux 操作系统给了我们更多的好处(分别约 40%和 59%)。

国际扶轮的其他好处:

*   在 RI 购买的虚拟机不必全天候工作
*   我们可以在 RI 中交换虚拟机类型
*   作为保留实例订购的虚拟机的工作时间可能比 RI 持续时间更长
*   我们可以随时取消国际订单，但我们必须记住，微软将收取未使用资金的 12%的费用

对于那些已经拥有 MS Windows Server 或 MS SQL Server 软件保障许可证的人来说， [Azure Hybrid benefit](https://azure.microsoft.com/en-us/pricing/hybrid-benefit/) 可能是一个令人兴奋的选择。它基于*自带执照(BYOL)* 模式。简而言之，您已经拥有了可以在本地环境中使用的许可证。这个许可证可以在 Azure 云环境中使用，因此我们不必为同样的东西支付两次费用。azure 混合优势可以节省高达 40%的虚拟机成本。

### 总结

现在就这些了。在下一集，我将重点介绍平台即服务模型的组件(应用服务和 Azure SQL 数据库)。

下集见。

附注:本文中使用的所有照片均来自[unsplash.com](https://unsplash.com/)。
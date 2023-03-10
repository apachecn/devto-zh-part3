# Azure Sentinel:将安全性提升到新的水平

> 原文：<https://dev.to/foppenma/azure-sentinel-taking-security-to-the-next-level-2ji7>

[![Azure Sentinel: Taking Security To The Next Level](img/24507a222e3e41dde36f3065c4c93c9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LvUgiI-1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/07/philipp-katzenberger-iIJrUoeRoCQ-unsplash.jpg)

就在 RSA 2019 大会之前，微软宣布了一个新的云原生 SIEM 解决方案，名为 Azure Sentinel。Sentinel 是额外的一双眼睛，让您的企业比以前更加安全。随着越来越多的公司转向云计算，威胁比以往任何时候都更加突出。因此，攻击者有更多的方法来破坏您的云环境。为了应对这种情况，您可以使用 Sentinel，它使您能够在本地和云中“收集”所有用户、应用程序和资源的数据。通过使用 Sentinel，您可以像任何其他 SIEM 一样使用预定义的用例或通过使用 AI 中的内置来“检测”威胁。“调查”并手动或自动快速响应威胁。

[![Azure Sentinel: Taking Security To The Next Level](img/8e39939f5eb59acb17d5f67bf7e595bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yxRcodD7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/07/Blog-Azure-Sentinel-1-1024x723---Copy-1.jpg)

## 为什么这是下一步？

目前，微软云中有许多不同的安全提供商和产品。想想 Azure 安全中心或 Windows Defender 高级威胁防护。当其中一个安全解决方案检测到可能的恶意活动时，SecOps 员工必须查看发生了什么。然后，员工将确定警报的类型，并登录特定的解决方案门户。对于 Windows Defender ATP，它看起来会像下面这样，在这种情况下，mimikatz 工具是在特定计算机上的 Windows Defender 可以干预之前下载和提取的。

[![Azure Sentinel: Taking Security To The Next Level](img/1c1e4c1e80f436b7d657bd1d53067d3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--biHHyVPL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/07/Blog-Sentinel-2.png)

我们在这里错过的是其他安全解决方案可能捕捉到的关于相同活动的所有信息。比如它最初是怎么被下载的(零号病人)？为了解决这个问题，应该有一个集中的地方来获得整个恶意活动的概况。这就是御天敌的用武之地。

有了 Sentinel，你对活动有了一个更高级的概述(在撰写本文时还没有公开预览)。这将减少花在找出发生了什么的时间，而是花在解决问题和使受影响的客户更安全。

[![Azure Sentinel: Taking Security To The Next Level](img/9cb1fcb00d7439a2444d565c205b9fa0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RGe5WgCy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/07/Blog-Sentinel-3.png)

## Azure Sentinel 怎么帮？

运行 SOC(安全运营中心)来监控其内部 IT 的大多数 CISO 对迁移到云犹豫不决。过去，缺乏监控云 IT 的控制措施，我们遇到的大多数 CISO 都有这种想法。历史上，SOC 的核心是它的 SIEM(安全信息和事件管理)工具。SIEM 很难构建，需要技术和安全人员花费大量的维护和时间。在我看来，内部 SIEM 的一些关键问题是:

*   很难建立，有一个陡峭的学习曲线和大量不同的数据要收集和理解。
*   对探测器的需求，为了获得更大的图片，您需要安装探测器和代理。正确配置这些需要很长的时间和大量的技巧；
*   通常局限于内部部署的 IT，虽然 IT 往往会迁移到云，但缺乏“云日志数据”的集成；
*   没有机器学习，所有逻辑必须来自 SIEM 厂商和 SecOps 团队的数据分析技能；
*   更新缓慢，传统 SIEM 的 AI 嵌入在供应商的软件中，因此在新攻击的情况下，SIEM 只会在供应商更新后学习检测算法；
*   不易扩展，硬件和软件需求需要提前规划，不容易根据业务需求进行更改和发展；
*   拥有和操作 SIEM 的成本很高，建立和维护 SIEM 需要时间和高技能的 SecOps 人员，这两者都必须具备。

微软将 SIEM 工具重新设想为一个名为 Microsoft Azure Sentinel 的新的云原生解决方案。Azure Sentinel 的一些关键功能包括:

*   易于收集安全数据
*   混合 IT 组织
*   设备
*   用户
*   应用程序
*   任何云上的服务器

Sentinel 集成了所有微软(安全)警报和日志记录，并与微软智能安全协会的许多合作伙伴合作。

消除了在以下方面花费时间的需要:

*   安装
*   维持
*   扩展基础设施

Sentinel 已经集成到 Azure 门户中，并带有内置连接器。所有扩展和维护(修补)都是自动完成的。

## 天蓝色的哨兵

[![Azure Sentinel: Taking Security To The Next Level](img/0b4e7123efb44a670fea58ac157a4dfc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_TV4zV6P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/07/Blog-Sentinel-4-1024x550.png)

*   云规模和速度的智能安全分析，当一个 Azure 租户受到新攻击时，微软的 SecOps 为 Sentinel 调查并构建新的 AI，一旦微软将其发布到云，Sentinel 的 AI 就会更新。
*   通过 ML 识别威胁，Azure Sentinel 使用最先进、可扩展的机器学习算法来关联数百万个低级异常，以得出一些高级别的安全事件
*   在开源检测查询的支持下，除了 ML 和 IA 之外，您还可以在 Sentinel 中设置自己的触发器和警报。这可以通过运行定制的 KQL 查询来完成。微软有这个 GitHub 让你开始使用并分享你自己的。

有了 Azure Sentinel，就没有前期成本，你只需为你使用的东西付费。通过这种方式，您可以以更少的工作量和成本更快地将 SOC 部署到云中。微软将允许您免费连接您的 Office365、Azure AD、Syslog、Azure Security Center 和第三方数据，因此当组织通过 Sentinel 迁移到云时，您仍然可以依靠您的 SOC 和 SIEM，并获得云能力的额外好处。

## 更上一层楼

除了“普通”SIEM 所具备的一切功能，Sentinel 还可以支持“Sentinel 融合”。Fusion 正在向您的警报检测添加具有高级机器学习模型的人工智能。好极了。这是什么意思，有什么作用？

来自不同 Microsoft 安全解决方案和提供商的大量警报会使您面临低或中严重性检测。通过 Sentinel Fusion 的机器学习模型，您可以自动检测这些“不太重要”的检测是否值得研究。AI 也将试图通过使用实体来关联来自不同提供者的事件。这些可以在 sentinel 中通过所谓的“实体类型”进行配置。目前，这些实体是帐户、主机或 IP 地址。正如您在下面看到的，您在 Sentinel 中创建的用例能够将任何列链接到适当的实体类型。

[![Azure Sentinel: Taking Security To The Next Level](img/9ae116c4a4e0334ca7b899164e9ef6c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5oWa9tsp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.re-mark-able.net/conteimg/2019/07/Blog-Sentinel-5.png)
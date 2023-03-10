# 如何逐步在 SharePoint 场中安装 SharePoint 累积更新

> 原文：<https://dev.to/mlisidoro/how-to-install-sharepoint-cumulative-updates-in-a-sharepoint-farm-step-by-step-f50>

帖子[如何在 SharePoint 场中逐步安装 SharePoint 累积更新](https://blogit.create.pt/miguelisidoro/2019/04/08/how-to-install-sharepoint-cumulative-updates-in-a-sharepoint-farm-step-by-step/)最先出现在[博客 IT](https://blogit.create.pt) 上。

这篇文章将解释如何一步一步地在你的 SharePoint 场中安装累积更新。本文中描述的步骤适用于 SharePoint 2010、SharePoint 2013、SharePoint 2016 和 SharePoint 2019。

本文假设只有一台 SharePoint 服务器，但是如果您有多台 SharePoint 服务器，您必须在服务器场中的所有 SharePoint 服务器上执行这些步骤。

## 简介

随着 SharePoint Online 和 Office 365 的出现，SharePoint 世界正在迅速向云迁移，但一些组织仍未准备好完全迁移到 Office 365 和 SharePoint Online。

升级到支持混合场景的 SharePoint 2019 可能是允许逐步迁移到云的有效解决方案。

使用 PowerShell 和 [AutoSPInstaller 可以自动安装 SharePoint 2019 服务器场。](https://autospinstaller.com/)此安装过程的一个额外优势是确保 SharePoint 场安装了最新的更新，使 SharePoint 场保持更新并正常工作(如果您想了解如何以自动化方式安装 SharePoint 2019 场，请单击[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2019-farm-using-powershell-and-autospinstaller-part-1-5bjp)和[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2019-farm-using-powershell-and-autospinstaller-part-2-1b9k) [)](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-2-5e36) 。

为了确保安装后 SharePoint 服务器始终保持更新，我们必须确保 Windows 更新和 SharePoint 累积更新都已正确安装。

## 安装 SharePoint 更新

若要在您的服务器场中安装最新的 SharePoint 累积更新，请按照下列步骤操作:

*   下载 SharePoint 累积更新
*   备份 SharePoint 场和配置
*   安装前步骤
*   在 SharePoint 场中安装累积更新
*   运行 SharePoint 产品和配置向导
*   安装后步骤

### 下载 SharePoint 累积更新

一切从下载最新的 SharePoint 累积更新开始。为此，您必须下载:

*   最新的独立于语言的 SharePoint 更新
*   最新的依赖于语言的 SharePoint 更新(如果您的服务器场中安装了任何语言包)

要保持最新状态并下载最新更新，请点击[此处](https://buildnumbers.wordpress.com/sharepoint/)。

### 备份 SharePoint 场和配置

在应用更新之前，您应该执行以下备份:

*   通过运行以下命令备份 SharePoint 场:***Backup-sp farm-Directory-Backup method Full-Verbose***
*   备份所有 web 应用程序的 web.config 文件
*   如果您有使用服务器端代码定制开发的解决方案，请备份这些解决方案。为了使这更容易，您可以从我的 GitHub 资源库下载一个 [PowerShell 脚本](https://github.com/miguelisidoro/SharePoint/blob/master/PowerShell/SharePoint2019/Export-WSP.ps1)

### 预安装步骤

为了确保累积更新安装期间不会启动或运行爬网，暂停搜索服务非常重要。如果在安装过程中触发了搜索爬网，搜索应用程序可能会损坏，您可能会被迫重建整个搜索服务应用程序，重新创建任何自定义托管属性，并再次执行完全爬网，以确保您拥有搜索结果以及所有基于搜索的解决方案正常工作。

要避免这些问题并确保安装期间没有搜索爬网运行，请执行以下 PowerShell 命令:

***Suspend-spenterprisearchserviceapplication–Identity】***

### 在 SharePoint 场中安装累积更新

下载最新更新并采取必要的预防措施后，您就可以在 SharePoint 场中安装它们了。

首先，安装独立于语言的更新:

[![](img/6450f14d220f2326e2afa1a3297863ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tBLy7xKO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blogit.create.pt/wp-content/uploads/2019/04/SP2019_Install_Update.png%3Fw%3D696%26ssl%3D1) 

<figcaption>安装最新语言独立更新</figcaption>

更新将需要几分钟时间，具体取决于您的服务器功能。

[![](img/5af7b9989cfb0aa1a39bdde3f89fc21b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GTQbQPGA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blogit.create.pt/wp-content/uploads/2019/04/SP2019_Install_Update_Progress.png%3Fw%3D696%26ssl%3D1) 

<figcaption>*安装最新语言独立更新*(进行中)</figcaption>

几分钟后，更新完成安装，需要重新启动服务器。

[![](img/49ce3e6e45e2cb04401bdcd6f4f5b201.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hIUjoibV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blogit.create.pt/wp-content/uploads/2019/04/SP2019_Install_Update_Reboot.png%3Fw%3D696%26ssl%3D1) 

<figcaption>*安装最新语言独立更新(已完成* )</figcaption>

如果您有与语言相关的更新，请在安装与语言无关的更新后安装它:

[![](img/7d8bee1fc38b253d28d58d5967071157.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2ILLd-EW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blogit.create.pt/wp-content/uploads/2019/04/SP2019_Install_Update_Language_Pack.png%3Fw%3D696%26ssl%3D1) 

<figcaption>*安装最新语言依赖更新*</figcaption>

更新将需要几分钟时间，具体取决于您的服务器功能。在我的例子中，我安装了葡萄牙语(葡萄牙)语言包累积更新。

[![](img/124188e9e3fa37cd316bf4d7b25b6651.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LjWC39oX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blogit.create.pt/wp-content/uploads/2019/04/SP2019_Install_Update_Language_Pack_Progress.png%3Fw%3D696%26ssl%3D1) 

<figcaption>
*安装最新的语言依赖更新(进行中)*</figcaption>

几分钟后，更新完成安装。

[![](img/bfb4eb5183609fca488311d96b61121c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AGeAKBpT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blogit.create.pt/wp-content/uploads/2019/04/SP2019_Install_Update_Language_Pack_Finished.png%3Fw%3D696%26ssl%3D1) 

<figcaption>*安装最新语言依赖更新(已完成)*</figcaption>

要了解有关如何更新您的 SharePoint 场的更多信息，请单击[此处](https://blogit.create.pt/miguelisidoro/2019/04/08/how-to-install-sharepoint-cumulative-updates-in-a-sharepoint-farm-step-by-step/)。

分享快乐！

帖子[如何在 SharePoint 场中逐步安装 SharePoint 累积更新](https://blogit.create.pt/miguelisidoro/2019/04/08/how-to-install-sharepoint-cumulative-updates-in-a-sharepoint-farm-step-by-step/)最先出现在[博客 IT](https://blogit.create.pt) 上。
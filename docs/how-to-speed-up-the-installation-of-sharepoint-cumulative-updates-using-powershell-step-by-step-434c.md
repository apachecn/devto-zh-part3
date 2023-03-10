# 如何使用 PowerShell 逐步加快 SharePoint 累积更新的安装

> 原文：<https://dev.to/mlisidoro/how-to-speed-up-the-installation-of-sharepoint-cumulative-updates-using-powershell-step-by-step-434c>

帖子[如何使用 PowerShell 一步一步加快安装 SharePoint 累积更新](https://blogit.create.pt/miguelisidoro/2019/05/02/how-to-speed-up-the-installation-of-sharepoint-cumulative-updates-using-powershell-step-by-step/)最先出现在[博客 IT](https://blogit.create.pt) 上。

这篇文章将解释如何使用 PowerShell 逐步加快 SharePoint 场中累积更新的安装。使用 PowerShell，您可以大大减少更新 SharePoint 场所需的时间，如果使用安装累积更新的传统方法，更新 SharePoint 场可能需要几个小时才能完成。本文中描述的步骤适用于 SharePoint 2013、SharePoint 2016 和 SharePoint 2019。

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
*   在 SharePoint 场中安装累积更新(使用 PowerShell)
*   运行 SharePoint 产品和配置向导

### 下载 SharePoint 累积更新

一切从下载最新的 SharePoint 累积更新开始。为此，您必须下载:

*   SharePoint 2016 和 SharePoint 2019:
    *   最新的独立于语言的 SharePoint 更新
    *   最新的依赖于语言的 SharePoint 更新(如果您的服务器场中安装了任何语言包)
*   SharePoint 2013:
    *   最新的 SharePoint Server 更新
    *   最新的 SharePoint foundation 更新

要保持最新状态并下载最新更新，请点击[此处](https://buildnumbers.wordpress.com/sharepoint/)。

### 备份 SharePoint 场和配置

在应用更新之前，您应该执行以下备份:

*   通过运行以下命令备份 SharePoint 场:***Backup-sp farm-Directory-Backup method Full-Verbose***

备份文件夹应该是网络上的共享文件夹，SharePoint 和数据库服务器都可以访问。

确保 SharePoint Server 2013 VSS 编写器服务已在 SharePoint Server 上启动(不会自动启动)。

请确保数据库服务器上启动了 SQL Server VSS 编写器服务。

有关 SharePoint 场备份的更多详细信息，请点击[此处](https://docs.microsoft.com/en-us/SharePoint/administration/prepare-to-back-up-and-restore)，点击[此处](https://docs.microsoft.com/en-us/sharepoint/administration/back-up-a-farm)，点击[此处](https://docs.microsoft.com/en-us/sharepoint/administration/best-practices-for-backup-and-restore)

*   使用 SQL Server 企业工具备份支持 SharePoint 场的数据库服务器中的所有 SharePoint 数据库
*   备份所有 web 应用程序的 web.config 文件
*   如果您有使用服务器端代码定制开发的解决方案，请备份这些解决方案。为了使这更容易，您可以从我的 GitHub 资源库下载一个 [PowerShell 脚本](https://github.com/miguelisidoro/SharePoint/blob/master/PowerShell/SharePoint2019/Export-WSP.ps1)

### 在 SharePoint 场中安装累积更新(使用 PowerShell)

下载最新更新并采取必要的预防措施后，您就可以在 SharePoint 场中安装它们了。

如果你想用传统方法更新你的农场，点击[这里](https://dev.to/mlisidoro/how-to-install-sharepoint-cumulative-updates-in-a-sharepoint-farm-step-by-step-f50)。如果您想了解 PowerShell 的方式并加快 SharePoint 场中累积更新的安装速度，请继续阅读！

要下载 SharePoint 补丁 PowerShell 脚本，请单击此处的。该脚本是由 Russ Maxwell 为 SharePoint 2013 创建的 [PowerShell 脚本](https://blog.russmax.com/why-sharepoint-2013-cumulative-update-takes-5-hours-to-install/)的改进版本。

此 PowerShell 脚本确保 SharePoint 修补程序处理时间从 4 到 5 小时减少到不到 1 小时的方法是通过执行以下操作:

*   如果选择暂停(使用-Pause 开关参数)，将暂停 Search Service 应用程序。这一点非常重要，因为如果在安装累积更新的过程中触发了搜索爬网，搜索应用程序可能会损坏，您可能会被迫重建整个搜索服务应用程序，重新创建任何自定义的托管属性，并再次执行完全爬网，以确保您有搜索结果，并且所有基于搜索的解决方案都能正常工作
*   对于 SharePoint 2013(SharePoint 2016 和 SharePoint 2019 不需要)，将禁用并停止以下服务:
    *   搜索服务被禁用
    *   搜索服务已停止
    *   IISAdmin 和 SPTimerV4 服务被禁用
    *   IISAdmin 和 SPTimerV4 服务已停止
*   如果您选择这样做，累积更新将以被动模式安装(没有任何必要的用户交互)，但您仍然可以在 UI 中看到安装过程
*   累积更新安装完成后:
    *   对于 SharePoint 2013(SharePoint 2016 和 SharePoint 2019 不需要)，以下服务将设置为自动:
    *   搜索服务
    *   IISAdmin 和 SPTimerV4 服务
    *   如果选择使用-Pause 开关参数，将会恢复搜索服务应用程序

要开始安装过程，请打开 SharePoint PowerShell 窗口并执行以下命令:

*   导入模块。\SharePointPatchScript.psm1
*   安装-sp patch-路径-暂停
    *   示例:Install-sp patch-Path D:\ SharePoint \ Updates-Pause

这将启动 SharePoint 场更新过程(SharePoint 2013 和 SharePoint 2019 的示例)。

对于 SharePoint 2013，将自动检测并安装 SharePoint Server 和 SharePoint Foundation 包。

[![](img/e84a612f35c56bfe9313ec8e142b3437.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lkWkCGjk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blogit.create.pt/wp-content/uploads/2019/05/SP2013_ApplyUpdate.png%3Fresize%3D680%252C544%26ssl%3D1)

<figcaption>*SharePoint 2013 农场更新使用 PowerShell*
</figcaption>

对于 SharePoint 2016 和 SharePoint 2019，将自动检测和安装语言无关和语言相关的软件包。

[![](img/9e790b728badadd78f9215cd61ce964a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wpTBjYEd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blogit.create.pt/wp-content/uploads/2019/05/SP2019_ApplyUpdate.png%3Fw%3D696%26ssl%3D1)

[![](img/5af7b9989cfb0aa1a39bdde3f89fc21b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GTQbQPGA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blogit.create.pt/wp-content/uploads/2019/04/SP2019_Install_Update_Progress.png%3Fw%3D696%26ssl%3D1)

<figcaption>*SharePoint 2019 农场更新使用 PowerShell*
</figcaption>

更新应该在不到一个小时的时间内完成，而不是花费几个小时。

[![](img/201743a5021e3a86156fa0c896da3236.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rNN1fRu1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blogit.create.pt/wp-content/uploads/2019/05/SP2019_ApplyUpdate_Finish.png%3Fw%3D696%26ssl%3D1)

<figcaption>*SharePoint 2019 服务器场更新使用 PowerShell* (安装完成)</figcaption>

可能需要重新启动服务器。

[![](img/49ce3e6e45e2cb04401bdcd6f4f5b201.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hIUjoibV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/blogit.create.pt/wp-content/uploads/2019/04/SP2019_Install_Update_Reboot.png%3Fw%3D696%26ssl%3D1) 

<figcaption>安装后重启到服务器的累积更新</figcaption>

要阅读整篇文章并了解如何更新您的 SharePoint 场的更多信息，请单击[此处](https://blogit.create.pt/miguelisidoro/2019/05/02/how-to-speed-up-the-installation-of-sharepoint-cumulative-updates-using-powershell-step-by-step/)。

分享快乐！

帖子[如何使用 PowerShell 一步一步加快安装 SharePoint 累积更新](https://blogit.create.pt/miguelisidoro/2019/05/02/how-to-speed-up-the-installation-of-sharepoint-cumulative-updates-using-powershell-step-by-step/)最先出现在[博客 IT](https://blogit.create.pt) 上。
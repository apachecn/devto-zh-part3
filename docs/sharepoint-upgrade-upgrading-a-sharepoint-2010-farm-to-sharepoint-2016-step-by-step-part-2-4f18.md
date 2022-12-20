# SharePoint 升级:逐步将 SharePoint 2010 服务器场升级到 SharePoint 2016(第 2 部分)

> 原文：<https://dev.to/mlisidoro/sharepoint-upgrade-upgrading-a-sharepoint-2010-farm-to-sharepoint-2016-step-by-step-part-2-4f18>

帖子 [SharePoint 升级:将 SharePoint 2010 服务器场逐步升级到 SharePoint 2016(第二部分)](https://blogit.create.pt/miguelisidoro/2019/02/04/sharepoint-upgrade-upgrading-a-sharepoint-2010-farm-to-sharepoint-2016-step-by-step-part-2/)最先出现在[博客 IT](https://blogit.create.pt) 上。

本文是关于如何将 SharePoint 2010 服务器场升级到 SharePoint 2016 的两部分分步教程的第二部分。

要阅读第一部分，请点击[她的](https://dev.to/mlisidoro/sharepoint-upgrade-upgrading-a-sharepoint-2010-farm-to-sharepoint-2016-step-by-step-part-1-252p-temp-slug-7631689)T2 的【e】的。

## 简介

随着 SharePoint Online 和 Office 365 的出现，SharePoint 世界正在迅速向云迁移，但一些组织仍未准备好完全迁移到 Office 365 和 SharePoint Online。

升级到支持混合方案的 SharePoint 2016 或 SharePoint 2019 可能是一个有效的解决方案。

升级 SharePoint 场可能是一项复杂的任务，升级过程由一系列步骤组成:

*   将定制开发的解决方案从 SharePoint 2010 迁移到 SharePoint 2016(不在本文讨论范围之内)
*   升级到临时 SharePoint 2013 服务器场
*   升级到目标 SharePoint 2016 服务器场

* *重要说明:** 在典型的升级过程中，如果有定制开发的解决方案，以下步骤应执行两次:

*   第一次，测试迁移过程，并允许最终用户正确迁移和测试定制开发的解决方案
*   第二次是在最终用户迁移和测试了所有定制开发的解决方案之后，执行最终迁移

这篇文章基于将 SharePoint 2010 服务器场升级到 SharePoint 2016，但如果您想升级到 SharePoint 2019，也应该有效。本文假设我们将仅将内容数据库从源 SharePoint 2010 迁移到 SharePoint 2016，所有其他 SharePoint 数据库都是在目标 SharePoint 2016 服务器场的安装过程中创建的。

如果您想了解有关如何使用 PowerShell 以自动化方式安装 SharePoint 2016 服务器场的更多信息，请点击[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-1-54m5)和[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-2-5e36)。

这篇文章将涵盖:

*   升级到目标 SharePoint 2016 服务器场

## 升级到目标 SharePoint 2016 服务器场

**重要说明:**不支持从 SharePoint 2010 直接迁移到 SharePoint 2016。要从 SharePoint 2010 迁移到 SharePoint 2016，我们必须先迁移到临时 SharePoint 2013 场，然后再迁移到 SharePoint 2016。

使用数据库连接升级方法(从 SharePoint 2010 升级到 SharePoint 2016 的唯一支持方法)执行迁移。

在升级过程中，对于临时 SharePoint 2013 和目标 SharePoint 2016 服务器场，您应该创建一个空的 web 应用程序，以便创建有效的 SharePoint 内容数据库。要了解有关如何安装 SharePoint 2016 服务器场的更多信息(该过程也适用于 SharePoint 2013)，请点击[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-1-54m5)和[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2016-farm-using-powershell-and-autospinstaller-part-2-5e36)。

要了解有关将 SharePoint 2010 服务器场升级到 SharePoint 2016 的所有信息，请单击此处的

分享快乐！

帖子 [SharePoint 升级:将 SharePoint 2010 服务器场逐步升级到 SharePoint 2016(第二部分)](https://blogit.create.pt/miguelisidoro/2019/02/04/sharepoint-upgrade-upgrading-a-sharepoint-2010-farm-to-sharepoint-2016-step-by-step-part-2/)最先出现在[博客 IT](https://blogit.create.pt) 上。
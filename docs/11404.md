# 如何逐步从 SharePoint 2013 升级到 SharePoint 2019(第 1 部分)

> 原文：<https://dev.to/mlisidoro/how-to-upgrade-from-sharepoint-2013-to-sharepoint-2019-step-by-step-part-1-2jkn>

帖子[如何一步步从 SharePoint 2013 升级到 SharePoint 2019(上)](https://blogit.create.pt/miguelisidoro/2019/03/06/how-to-upgrade-from-sharepoint-2013-to-sharepoint-2019-step-by-step-part-1/)最早出现在[博客 IT](https://blogit.create.pt) 上。

本文是关于如何将 SharePoint 2013 服务器场迁移到 SharePoint 2019 的两部分分步教程的第一部分。

阅读第二部分，点击[这里](https://blogit.create.pt/miguelisidoro/2019/03/06/how-to-upgrade-from-sharepoint-2013-to-sharepoint-2019-step-by-step-part-2/)。

## 简介

随着 SharePoint Online 和 Office 365 的出现，SharePoint 世界正在迅速向云迁移，但一些组织仍未准备好完全迁移到 Office 365 和 SharePoint Online。

升级到支持混合场景的 SharePoint 2019 可能是允许逐步迁移到云的有效解决方案。

这篇文章是基于使用数据库连接方法将 SharePoint 2013 服务器场升级到 SharePoint 2019，但如果您想要升级到 SharePoint 2016 也是有效的(要了解更多关于升级到 SharePoint 2016 的信息，请单击[此处](https://dev.to/mlisidoro/sharepoint-upgrade-upgrading-a-sharepoint-2010-farm-to-sharepoint-2016-step-by-step-part-1-55n7)和[此处](https://dev.to/mlisidoro/sharepoint-upgrade-upgrading-a-sharepoint-2010-farm-to-sharepoint-2016-step-by-step-part-2-4f18))。本文假设我们将仅将内容数据库从源 SharePoint 2013 迁移到 SharePoint 2019，所有其他 SharePoint 数据库都是在目标 SharePoint 2019 服务器场的安装过程中创建的。

如果您想了解有关如何使用 PowerShell 以自动化方式安装 SharePoint 2019 场的更多信息，请点击[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2019-farm-using-powershell-and-autospinstaller-part-1-5bjp)和[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2019-farm-using-powershell-and-autospinstaller-part-2-1b9k)。

另一种有效的迁移方法是使用内容迁移从 SharePoint 2013 迁移到 SharePoint 2019。在这种方法中，升级不是基于数据库升级过程，而是基于内容迁移，通常需要第三方解决方案来完成内容迁移。一些能够完成这项工作的第三方解决方案是 [ShareGate](https://sharegate.com/products/sharegate-desktop) 、 [AvePoint](https://www.avepoint.com/products/hybrid/office-365-migration/) 和 [Metalogix](https://www.quest.com/metalogix/) 。

使用数据库连接方法升级 SharePoint 场可能是一项复杂的任务，升级过程由一系列步骤组成:

*   将定制开发的解决方案从 SharePoint 2013 迁移到 SharePoint 2019(不在本文讨论范围之内)
*   升级到临时 SharePoint 2016 服务器场
*   升级到目标 SharePoint 2019 服务器场

* *重要说明:** 在典型的升级过程中，如果有定制开发的解决方案，以下步骤应执行两次:

*   第一次，测试迁移过程，并允许最终用户正确迁移和测试定制开发的解决方案
*   第二次是在最终用户迁移和测试了所有定制开发的解决方案之后，执行最终迁移

这篇文章将涵盖:

*   将定制开发的解决方案从 SharePoint 2013 迁移到 SharePoint 2019(关于定制解决方案迁移的细节不在本文讨论范围之内)
*   升级到临时 SharePoint 2016 服务器场

## 将定制开发的解决方案从 SharePoint 2013 迁移到 SharePoint 2019

这一步超出了本文的范围，但是如果您有自定义开发的解决方案，并且希望从 SharePoint 2013 迁移到 SharePoint 2019，则需要注意以下几点:

*   首先，您应该分析源 SharePoint 2013 服务器场，并寻找定制开发的解决方案，尤其是服务器场解决方案和/或沙盒解决方案
*   如果找到了定制开发的解决方案，并且您希望采用最简单的迁移途径，您只需将定制开发的解决方案升级到 SharePoint 2019，而无需更改解决方案架构。这通常涉及将服务器端代码从 SharePoint 2013 迁移到 SharePoint 2019，可能需要迁移母版页并调整 CSS 和 JS 文件
*   尽管难度更大，但如果可能，您应该考虑将现有解决方案迁移到以下开发模型之一:
    *   SharePoint Framework:现代 SharePoint 解决方案的最新和推荐开发模型，支持更轻松地迁移到 SharePoint Online 和 Office 365
    *   SharePoint 外接程序:这种开发模型仍然受支持，根据您的情况，它可能是一种有效的解决方案。然而，如果可能的话。您应该考虑将解决方案迁移到 SharePoint 框架开发模型

要了解有关将 SharePoint 2013 服务器场升级到 SharePoint 2019 的所有信息，请单击此处的。

分享快乐！

帖子[如何一步步从 SharePoint 2013 升级到 SharePoint 2019(上)](https://blogit.create.pt/miguelisidoro/2019/03/06/how-to-upgrade-from-sharepoint-2013-to-sharepoint-2019-step-by-step-part-1/)最早出现在[博客 IT](https://blogit.create.pt) 上。
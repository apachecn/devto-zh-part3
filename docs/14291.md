# SharePoint 升级:逐步将 SharePoint 2010 服务器场升级到 SharePoint 2016(第 1 部分)

> 原文：<https://dev.to/mlisidoro/sharepoint-upgrade-upgrading-a-sharepoint-2010-farm-to-sharepoint-2016-step-by-step-part-1-55n7>

帖子 [SharePoint 升级:将 SharePoint 2010 服务器场逐步升级到 SharePoint 2016(第一部分)](https://blogit.create.pt/miguelisidoro/2019/02/04/sharepoint-upgrade-upgrading-a-sharepoint-2010-farm-to-sharepoint-2016-step-by-step-part-1/)最先出现在[博客 IT](https://blogit.create.pt) 上。

本文是如何将 SharePoint 2010 服务器场升级到 SharePoint 2016 的两部分分步教程的第一部分。

阅读第二部分，点击[这里](https://blogit.create.pt/miguelisidoro/2019/02/04/sharepoint-upgrade-upgrading-a-sharepoint-2010-farm-to-sharepoint-2016-step-by-step-part-2/)。

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

*   将定制开发的解决方案从 SharePoint 2010 迁移到 SharePoint 2016(不在本文讨论范围之内)
*   升级到临时 SharePoint 2013 服务器场

## 将定制开发的解决方案从 SharePoint 2010 迁移到 SharePoint 2016

这一步超出了本文的范围，但是如果您有自定义开发的解决方案，并且希望从 SharePoint 2010 迁移到 SharePoint 2016，那么需要注意以下几点:

*   首先，您应该分析源 SharePoint 2010 服务器场，并寻找定制开发的解决方案，尤其是服务器场解决方案和/或沙盒解决方案
*   如果找到了定制开发的解决方案，并且您希望采用最简单的迁移途径，您只需将定制开发的解决方案升级到 SharePoint 2016，而无需更改架构。这通常涉及将服务器端代码从 SharePoint 2010 迁移到 SharePoint 2016，可能需要将母版页和调整迁移到 CSS 和 JS 文件
*   尽管难度更大，但如果可能，您应该考虑将现有解决方案迁移到以下开发模型之一:
    *   SharePoint Framework:现代 SharePoint 解决方案的最新和推荐开发模型，支持更轻松地迁移到 SharePoint Online 和 Office 365
    *   SharePoint 外接程序:这种开发模型仍然受支持，根据您的情况，它可能是一种有效的解决方案。然而，如果可能的话。您应该考虑将解决方案迁移到 SharePoint 框架开发模型

要了解有关将 SharePoint 2010 服务器场升级到 SharePoint 2016 的所有信息，请单击此处的。

分享快乐！

帖子 [SharePoint 升级:将 SharePoint 2010 服务器场逐步升级到 SharePoint 2016(第一部分)](https://blogit.create.pt/miguelisidoro/2019/02/04/sharepoint-upgrade-upgrading-a-sharepoint-2010-farm-to-sharepoint-2016-step-by-step-part-1/)最先出现在[博客 IT](https://blogit.create.pt) 上。
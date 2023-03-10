# 如何使用 PowerShell 和 AutoSPInstaller 安装 SharePoint 2019 服务器场(第 2 部分)

> 原文：<https://dev.to/mlisidoro/how-to-install-a-sharepoint-2019-farm-using-powershell-and-autospinstaller-part-2-1b9k>

帖子[如何使用 PowerShell 和 AutoSPInstaller 安装 SharePoint 2019 服务器场(第二部分)](https://blogit.create.pt/miguelisidoro/2018/12/09/how-to-install-a-sharepoint-2019-farm-using-powershell-and-autospinstaller-part-2/)首先出现在[博客 IT](https://blogit.create.pt) 上。

随着 SharePoint Online 和 Office 365 的推出，SharePoint 世界正在迅速向云转变，但微软最近发布了新的 SharePoint Server 版本，改进了混合支持。要了解 SharePoint 2019 中所有新功能的更多信息，请单击[此处。](https://dev.to/mlisidoro/meet-the-new-modern-sharepoint-server-sharepoint-2019-rtm-is-here-2oak)

这篇文章是两部分系列的第二部分，在这里我将谈论一种使用 PowerShell 以完全自动化的方式安装 SharePoint 2019 的好方法。

阅读第一部分，点击[这里](https://blogit.create.pt/miguelisidoro/2018/12/09/how-to-install-a-sharepoint-2019-farm-using-powershell-and-autospinstaller-part-1/)。

# 简介

安装 SharePoint 场可能是一项复杂的任务，安装过程由一系列步骤组成:

*   下载和安装 SharePoint 必备组件
*   下载并安装 SharePoint 二进制文件
*   下载并安装最新的 SharePoint 更新
*   下载并安装所需的语言包(可选，如果您希望 SharePoint 界面以英语以外的语言显示，这是必需的)
*   使用 SharePoint 产品和配置向导配置 SharePoint 场

所有上述步骤都很耗时，而且很容易出错，例如，如果您没有下载任何先决条件。

幸运的是，通过使用名为 [AutoSPInstaller](https://autospinstaller.com/) 的脚本，使用 PowerShell 完全自动化 SharePoint 场安装是可能的。安装过程可以分为两个主要步骤:

*   下载 SharePoint 安装包，点击[此处](https://dev.to/mlisidoro/how-to-install-a-sharepoint-2019-farm-using-powershell-and-autospinstaller-part-1-40af-temp-slug-9890448)了解更多详情
*   安装 SharePoint 并配置 SharePoint 场

在这篇文章中，我将讨论这个过程的第二部分:安装 SharePoint 和配置 SharePoint 场。

[AutoSPInstaller](https://autospinstaller.com/) 可用于在以下版本的 SharePoint 中安装 SharePoint 场:

*   SharePoint 2010
*   SharePoint 2013
*   SharePoint 2016
*   SharePoint 2019(要了解 SharePoint 2019 中所有新功能的更多信息，请单击[此处)](https://dev.to/mlisidoro/meet-the-new-modern-sharepoint-server-sharepoint-2019-rtm-is-here-2oak)

本文中的示例基于 SharePoint 2019。

要了解安装过程第二部分(安装 SharePoint 和配置 SharePoint 场)的所有详细信息，请单击此处的。

分享快乐！

帖子[如何使用 PowerShell 和 AutoSPInstaller 安装 SharePoint 2019 服务器场(第二部分)](https://blogit.create.pt/miguelisidoro/2018/12/09/how-to-install-a-sharepoint-2019-farm-using-powershell-and-autospinstaller-part-2/)首先出现在[博客 IT](https://blogit.create.pt) 上。
# Office 365 中单租户与多租户的利弊

> 原文：<https://dev.to/mlisidoro/pros-and-cons-of-single-tenant-vs-multiple-tenants-in-office-365-2hd9>

Office 365 中的帖子[单租户与多租户的利弊首先出现在](https://blogit.create.pt/miguelisidoro/2019/01/07/pros-and-cons-of-single-tenant-vs-multiple-tenants-in-office-365/)[博客 IT](https://blogit.create.pt) 上。

我最近遇到了一个案例，客户必须在拥有多个 Office 365 租户或只有一个租户之间做出选择。这篇文章将讨论一些技术上的考虑，如果你正在考虑选择单租户还是多租户方法，你将不得不问自己，以及这个决定对最终用户体验的影响。

## 简介

不要轻易决定为您的组织选择多个租户，因为**它对最终用户体验有很多影响**。我将在下面更详细地讨论最终用户体验的影响，但让我说清楚:**如果可以，您应该选择为您的组织使用单个 Office 365 租户。**

## 多个租户的常见场景

在一些情况下，组织可能认为最好甚至是唯一的选择是选择多个租户。以下是一些最常见的情况:

*   我们的组织由世界各地的几个部门组成，每个部门必须将其数据存储在不同的地理位置
*   我们必须为组织内的每个部门提供完全自主的管理控制
*   我们希望避免 Office 365 工作负载(例如:Exchange Online、SharePoint Online、团队)的网络延迟问题
*   您的一个部门有一天可能会离开组织，我们希望确保如果发生这种情况，数据得到适当的隔离
*   该组织拥有来自不同地理位置的多个 Office 365 许可提供商

采用多租户是解决上述问题的一种可能方式，但几乎总是有替代解决方案。最近，发布了多地理位置功能，允许组织跨不同地理位置分割其数据驻留，而无需拥有多个租户。要了解 Office 365 中多地理位置功能的更多信息，请单击[此处](https://products.office.com/en/business/multi-geo-capabilities)。

## 要考虑的重要技术问题

### 域

许多组织可能犯的一个相当常见的错误是认为因为他们有多个 DNS 域，所以他们需要有多个 Office 365 租户。这是不正确的，您可以在单个租户中拥有多个域(在单个 Office 365 租户中您可以拥有多达 900 个域，更多详细信息[请点击此处](https://docs.microsoft.com/en-us/office365/servicedescriptions/office-365-platform-service-description/domains))。

### 身份

在 Office 365 中，用户将使用他们的身份对每个工作负载进行身份验证，通常在以下情况下:

*   与您的本地 Active Directory 环境同步的 Azure Active Directory 帐户(大多数组织的推荐方案)
*   仅云帐户(如果您的组织没有本地活动目录，或者一些用户只需要访问 Office 365 和云资源)

### Azure 活动目录

如果您需要将您的本地 Azure Active Directory 与 Azure Active Directory 同步，您必须考虑以下几点:

*   Azure Active Directory 租户与单个 Office 365 租户相关联
*   Azure Active Directory 中的每个用户都是唯一的，您不能将同一个用户同步到多个租户中。这有一个非常重要含义:**每个用户都是单个租户的成员，并被视为任何其他租户的访客用户**
*   如果您有多个租户，每个 DNS 域只能在一个租户中注册
*   如果您有几个本地 Active Directory 林，并且您希望将所有林同步到单个租户，则您只能有一个 Azure AD Connect 实例，该实例必须能够访问所有本地 AD 林。要了解有关 Azure AD Connect 支持的拓扑的更多信息，请单击此处的

### 租户迁移

如果您的组织中已经有多个租户，并且您希望将用户和 Office 365 工作负载迁移到一个租户，那么评估哪些工作负载已经在使用非常重要。最简单的迁移途径是，如果唯一使用的工作负载是电子邮件，并且使用的工作负载越多，迁移就越困难。

如果您仍在评估是选择单租户还是多租户，请继续阅读。

## 用户体验

在决定选择单个租户还是多个租户之前，要考虑的最重要的事情之一是终端用户体验。接下来，我将更详细地讨论这两种场景中的最终用户体验。

要了解 Office 365 中单租户与多租户的利弊，请点击[此处](https://blogit.create.pt/miguelisidoro/2019/01/07/pros-and-cons-of-single-tenant-vs-multiple-tenants-in-office-365/)。

分享快乐！

Office 365 中的帖子[单租户与多租户的利弊首先出现在](https://blogit.create.pt/miguelisidoro/2019/01/07/pros-and-cons-of-single-tenant-vs-multiple-tenants-in-office-365/)[博客 IT](https://blogit.create.pt) 上。
# 实现平台安全性

> 原文：<https://dev.to/cheahengsoon/implement-platform-security-4nid>

**为平台即服务配置定制域**

*   Azure AD 中的每个域名要么是初始域名，要么是自定义域名
*   每个 Azure 广告实例都有一个 company.onmicrosoft.com 格式的初始域名
*   使用自定义域有助于确保您的内部和外部 URL 是相同的，这提供了以下好处:
    *   你的用户将会有一个更容易的体验，这建立了用户的信心
    *   应用程序中包含的链接无需额外配置即可工作
    *   某些配置只有在您拥有自定义域的情况下才有效

**配置更新域**

*   微软不会自动更新您的 IaaS 虚拟机
*   更新域管理有意关闭一台(或多台)服务器以提供关键更新的活动
*   为了向您的应用程序提供冗余，我们建议您将两个或更多虚拟机分组到一个可用性集中
*   底层 Azure 平台为可用性集中的每个虚拟机分配一个更新域和一个容错域![Alt text of image](img/67b51fe2aa9fb45df66e945da40fe420.png)

**为无服务器计算实现 Azure 功能更新**

*   Azure Functions 是一个无服务器应用的例子，它可以支持单页面应用
*   最常见的更新方法之一是使用 PUT 语句
*   在特定 Azure UDF 资源上执行 PUT 操作会替换整个 UDF 资源
*   部署和更新您的功能的另一种方法是使用应用服务持续集成
*   目前支持以下部署源:
    *   Bitbucket
    *   Dropbox
    *   外部存储库(Git 或 Mercurial)
    *   Git 本地存储库和 GitHub
    *   微软 OneDrive
    *   Azure DevOps

**为无服务器计算配置安全性**

*   无服务器计算将服务器管理的责任从应用程序所有者转移到平台提供商
*   这有助于消除安全问题，例如具有已知安全可变性但尚未更新的服务器
*   但是，在无服务器计算中存在一些安全问题和挑战，因为您仍然要负责:
    *   您的应用程序代码
    *   数据管理
    *   数据加密
    *   身份管理
    *   认证/授权
    *   服务配置和基于角色的访问控制(RBAC)

**配置集装箱安全**

*   容器部署中的网络是您必须在安全场景中解决的一个特殊领域
*   容器映像是一个轻量级的、独立的、可执行的包，包括运行应用程序所需的一切
*   当应用程序被容器化时，应用程序和运行应用程序所需的组件被组合在单个映像中
*   容器本身并不脆弱
*   内核由所有容器和主机共享
*   获得容器访问权限的攻击者应该不能获得其他容器或主机的访问权限
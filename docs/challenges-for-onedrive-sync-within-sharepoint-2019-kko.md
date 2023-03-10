# SharePoint 2019 中 OneDrive 同步面临的挑战

> 原文：<https://dev.to/venithk/challenges-for-onedrive-sync-within-sharepoint-2019-kko>

工作场所的灵活性是未来工作的关键组成部分之一。为了实现这一点，人们必须能够在任何地方、任何设备上以安全的方式轻松访问组织内外并进行协作。SharePoint 和 OneDrive 为此提供了解决方案。OneDrive for Businesses】微软 Office 365 云服务，允许用户在云端存储、共享文件，而 SharePoint 允许多个团队同时处理同一份文档。通过[本地 SharePoint Server](https://www.microexcel.com/microsoft-business-solutions-consulting-services/microsoft-productivity-collaboration-and-cloud-solutions/sharepoint-business-solutions-and-consulting-services/) 实例，它为 OneDrive 站点提供了与 OneDrive 云类似的体验。允许您存储和保护您的文件，与他人共享这些文件，并从您的所有设备上的任何位置获取这些文件，这些设备可以托管在您公司的服务器上。

#### **OneDrive 同步客户端:**

OneDrive 同步客户端(也称为下一代同步客户端–NGSC)提供在您的设备和 OneDrive for Business 网站之间同步文件的功能。这一新的同步客户端现已在 SharePoint 2019 中提供。以前版本的 SharePoint 使用不同的同步客户端“groove ”,该客户端在跨设备提供无缝一致的体验方面有严重的局限性。

## **挑战:与 SharePoint 2019 的同步问题**

我们一直在测试 SharePoint 2019 和不同拓扑结构(内部网和外部网)的新同步客户端。在此期间，我们遇到了一些配置方面的挑战，例如清理安装在台式机上的旧版本客户端，并准备好必要的注册表项，以便在 NTLM 身份验证中与内部网同步。这些更改将允许 OneDrive Sync 客户端根据内部部署的 SharePoint 2019 进行身份验证，并支持与云和内部部署的 OneDrive for Business 进行同步。

点击阅读更多关于[微软列出的配置。](https://docs.microsoft.com/en-us/sharepoint/install/new-onedrive-sync-client)

HKLM:\软件\策略\ Microsoft \ one drive \ SharePoint onpremization

HKLM:\ Software \ Policies \ Microsoft \ one drive \ sharepointonpremfrontdoourl

HKLM:\软件\策略\ Microsoft \ one drive \ SharePoint onpremtenant name

但是，如果我们将 SharePoint 站点(OneDrive 或团队站点)配置为使用基于 SAML 的(声明)身份验证，则同步客户端会失败。SharePoint 2019 支持使用 SAML 1.1，但 NGSC(下一代同步客户端)不支持。

在我们与卓越支持的通话中，我们验证了 NGSC 甚至不会传递 cookie，即使我们使用的是永久 cookie。令人惊讶的是，WebDAV 客户端使用持久 cookies，我们能够成功地将网络驱动器映射到 OneDrive 共享，但这并不是我们想要的用户体验。

目前，在 SharePoint 2019 上利用最新 OneDrive 同步体验的唯一方式是使用 Windows 身份验证。这严重限制了将 SharePoint intranets 扩展到 Extranets，从而促进了使用基于声明的身份验证的 SSO。该产品组正在研究添加该功能的可能性，但目前还没有 ETA。
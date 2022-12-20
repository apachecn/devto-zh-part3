# 使用 Azure 认知服务丰富 SharePoint 图像库

> 原文：<https://dev.to/simonwaight/enriching-sharepoint-image-libraries-with-azure-cognitive-services-27d0>

这些年来，通过搜索在 SharePoint 中定位内容的能力一直在不断提高，随着 OneDrive for Business 中的 [Delve](https://support.office.com/en-ie/article/what-is-office-delve-1315665a-c6af-4409-a28d-49f8916878ca) 和 [OCR 等功能的发布，这种能力的影响力大幅提升。](https://techcommunity.microsoft.com/t5/Microsoft-Search-Blog/Search-for-words-in-your-images-in-Office-365/ba-p/135703)

从文档库中为文件内容编制索引已经取得了无止境的改进，但是我认为还有一个不足之处，那就是从 SharePoint 中存储的图像中提取推断出的元数据。

在这篇文章中，以及相关的截屏，我将看看我们如何利用 [Azure 认知服务](https://azure.microsoft.com/en-au/services/cognitive-services/)和 [Azure 逻辑应用](https://docs.microsoft.com/en-au/azure/logic-apps/)的几个功能来快速增加你上传到 SharePoint 的图像数据的价值。

### 基线服务

为了使用此解决方案，您需要:

*   一个 SharePoint Online(或 SharePoint on-premises)文档库，您可以在其中验证一个[Logic App SharePoint Connector](https://docs.microsoft.com/en-us/azure/connectors/connectors-create-api-sharepoint)。
*   您可以针对这些图像[训练和测试自定义视觉分类器。](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)[开放图像数据集](https://storage.googleapis.com/openimages/web/index.html)是一个很好的起点，如果你想按类别获取源图像的话。
*   Azure 订阅(试用或付费)——在[http://azure.com/free](http://azure.com/free)注册免费试用。这个演示将在一个试用帐户中愉快地运行。
*   一个训练有素的 Azure 认知服务定制视觉分类器。出于演示的目的，我只使用了认知服务的免费层，这对您来说应该同样适用。

### 构建逻辑 App

Logic 应用程序最棒的地方不仅在于它们是无服务器的，而且几乎完全是“无代码”的。我没有详细描述这些步骤，而是包括了以下项目来帮助您开始。

#### 1。逻辑应用程序的设计者视图

点击图片查看放大版本。

[![Logic App](img/af43c65133be013cabe32a09c8736b89.png "Logic App")](https://siliconvalve.files.wordpress.com/2019/03/vision-logic-app.png)

#### 2。导出到 GitHub 的 Logic 应用程序

从这里抢代码:[https://github.com/sjwaight/SharePointCognitiveServices](https://github.com/sjwaight/SharePointCognitiveServices)。

注意:这不包括连接，您需要使用自己的 SharePoint 和认知服务 API 详细信息手动重新创建连接。

在下图中，您可以看到如何在 SharePoint Online 中检索列表的 GUID–通过从 Office 365 标题中您的用户名旁边的“设置”菜单(cog)中单击“库设置”来访问此页面。

[![SharePoint List GUID](img/b9a3cfea2eb059fc1e3b688717edb71d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FOjAZRsJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://siliconvalve.files.wordpress.com/2019/03/sharepoint-list-guid.png%3Fw%3D1100%26h%3D370)

#### 3。展示这一切是如何结合在一起的截屏

请注意，在截屏中，与我上面发布的逻辑应用程序有一点不同…看看你是否能找到它！😉

好了，我们知道了如何(相当)容易地为存储在 SharePoint 中的图像数据增加更多价值。请注意，您可以将这种方法应用于存储在 Google Drive 或 Dropbox 中的文档，以及使用 Azure 认知服务的许多其他类别来丰富您已经拥有的数据！

在我结束之前的最后一个项目是——如果你想了解更多关于 Azure 认知服务的信息，你可以免费做，不需要 Azure 订阅。Microsoft Learn 有一系列涵盖这一领域的模块可供您完成。[使用浏览器在线访问它们](https://docs.microsoft.com/en-us/learn/browse/?roles=developer&products=azure-cognitive-services)。

快乐的日子！😎
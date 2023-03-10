# Azure Storage Easy Web 文件托管

> 原文：<https://dev.to/sebnilsson/azure-storage-easy-web-file-hosting-536g>

为了稍微改进一下我的博客，我想在帖子中包含更多的图片，但觉得缺乏一个好的网络文件托管解决方案。为了找到最合适的，我写下了一张特征和标准的清单。我寻找的解决方案应该或必须检查以下内容:

*   **文件的所有权:**如果明天使用的解决方案消失，您仍然可以访问我的文件。
*   **可预测的 URL:**资源的 URL 应该永远不会改变。你不想更新你所有的博客文章或其他在互联网上传播的外部链接。
*   **良好的工具:**在上传多个和/或大型文件时，避免缓慢的网络上传，而且还能轻松获得文件的概览。
*   ***(奖金)*漂亮的网址:**“漂亮”的网址更容易检查复制粘贴错误，但也可能有利于 [SEO](https://en.wikipedia.org/wiki/Search_engine_optimization) 。
*   ***【奖金】*低成本或免费:**既然有免费服务，为文件托管付费肯定是值得的。

[![Images & Tech](img/4c3c8e45412c0edd0955bccbc1b760f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vUHfHJ0p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.sebnilsson.com/wimg/azure-storage-file-hosting/lead.jpg)

## 不合适的替代品

不久前，当我开始评估替代品时， **Flickr** 还是一个东西。问题是，我无法直接链接到我上传的图片。感觉到所有的障碍后，这表明使用 Flickr 进行图像托管被积极阻止，我明白这是太多的黑客。

从历史上看，我一直通过 Blogger 使用 **Google 的图片托管，这也是我的博客开始的地方。问题是，这也让我感觉像是在黑，我总是担心网址会改变，我不得不浏览我发表的每一篇博文，更新所有的图片。**

像 **Dropbox** 和 **Google Drive** 这样的服务似乎积极地试图阻止使用他们的服务，即使他们可以通过网络访问。

## Azure 存储，轻松托管网络文件

进入 [Azure 存储](https://azure.microsoft.com/services/storage/)，其广泛采用、熟悉的界面和[价格极其实惠的 Blob 存储](https://azure.microsoft.com/en-us/pricing/details/storage/blobs/)。它检查了我在清单上的所有要点和更多。有了它的支持，可以合理地假设更多的功能将会不断增加。

Azure Blob 存储可用于在 Azure 云中存储几乎任何大小的数据 Blob。通过提供路径/密钥，您可以在该“路径”上读取或写入该“文件”。Azure Storage 的整体[性能非常好，也是该服务的一个重要特性，但 Azure Blob 存储的简单机制使它非常快。](https://docs.microsoft.com/en-us/azure/storage/common/storage-scalability-targets)

### 向互联网公开 blob 内容

因此，你可以构建一个 web 应用程序来访问 Azure Blob 存储上的文件，并通过你的 API 中的 URL 来公开它们，但你也可以通过激活对你的 Blob 的[匿名读取访问](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources)，让 Azure 为你处理这些。

你可以在 blob 级别上这样做，所以**你可以有一个专用于公共文件**的 blob，与同一个存储帐户中的其他 blob 分开。当您使用选项`Blob (anonymous read access for blobs only)`时，这些文件将是只读的，该选项位于所选 blob 的*访问策略*部分下。

### 上传文件

然后你可以使用 Azure 门户网站，以编程方式使用 [Azure Blob 存储 API](https://docs.microsoft.com/azure/storage/) 上传文件，或者你可以使用应用程序 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ，获得友好的 GUI 体验。

[![Azure Storage Explorer](img/3e6c93bb3088121bd4e66ed26c6e1caa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SeF6b8jP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://files.sebnilsson.com/wimg/azure-storage-file-hosting/storage-explorer.png)

现在，你可以通过 Azure 提供的 URL 或更漂亮的 URL，使用自定义域在任何地方共享文件。

### 添加自定义域

为了满足漂亮网址的标准，你可以[为 Azure 存储账户](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-custom-domain-name)设置你自己的自定义域名。如果不这样做，Azure Blob 存储的默认 URL 是`https://{storage-account-name}.blob.core.windows.net/{file-path}`。

### 激活 Azure CDN

对于你的小项目来说，这是一个很好的开始，但你可以在未来轻松过渡到在现有的 Azure 存储帐户上使用 [Azure 内容交付网络(Azure CDN)](https://azure.microsoft.com/services/cdn/) 的全部功能，只需通过[从你的存储帐户中的*Azure CDN*-部分](https://docs.microsoft.com/azure/cdn/cdn-overview)激活它。
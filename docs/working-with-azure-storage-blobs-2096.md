# 使用存储 Blobs

> 原文：<https://dev.to/azure/working-with-azure-storage-blobs-2096>

*我们选择了由[迈克尔·克伦普](https://twitter.com/mbcrump)创造的我们最喜欢的技巧和诀窍，并在整个四月在 Azure 上提供新的技术内容！错过一天(或更多)？[赶上系列](https://dev.to/t/azureapril)。*

**没有天蓝色？** [抢免费订阅](https://azure.microsoft.com/en-us/free?&WT.mc_id=azureapril_devto-blog-cxa)。

* * *

Azure 存储被描述为一种服务，它提供可用、安全、持久、可扩展和冗余的存储。

Azure 存储是一个总括术语。它包括:

*   [斑点存储](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-overview?&WT.mc_id=azureapril_devto-blog-cxa)
*   [数据湖存储](https://docs.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-introduction?&WT.mc_id=azureapril_devto-blog-cxa)
*   [文件存储](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-introduction?&WT.mc_id=azureapril_devto-blog-cxa)
*   [队列存储](https://docs.microsoft.com/en-us/azure/storage/queues/storage-queues-introduction?&WT.mc_id=azureapril_devto-blog-cxa)
*   [表格存储](https://docs.microsoft.com/en-us/azure/storage/tables/table-storage-overview?&WT.mc_id=azureapril_devto-blog-cxa)

我们现在将重点放在 Blob 存储上。

# 在 Azure 门户中创建 Blob 存储📂

打开 [Azure 门户](https://azure.microsoft.com/en-us/features/azure-portal/?&WT.mc_id=azureapril_devto-blog-cxa)，点击`Create a Resource`。选择`Azure Storage`。我们将保持如下所示的简单来开始。

[![create](img/611a50edfdb29bb8ece421938de9167f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nyotgVi7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/b7645052be9c63c3640e848daba977ddf4a46e9f/058ec/files/storageacct1.png)

一旦完成—门户会通知您—进入资源并在`Services`下查看。

[![find it](img/d5c6539994837d8579de0ac17c9dee6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZNKbgV57--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/9284e1b624ffe72451a8b7ae5eedc0417b02b396/5b3be/files/storageacct2.png)

继续点击`Blobs`，创建一个`Container`，并将其命名为`Images`。

> 记住！容器很像文件夹。`https://myblob/container/image1.jpg`

现在您需要点击`Upload`并选择物理磁盘上的一个文件。

[![upload](img/aecb9e090d8f450dd824f3879c722ca1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hwmJK939--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/6169cbd610f715c39319c0f24281f3c530711a68/53696/files/storageacct4.png)

现在您的文件已经上传，选择它。您可以点击省略号并选择`Blob properties`查看更多详细信息。

[![done](img/c9e1a7721864538985b6eaf8466f44cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WwZ7d0ku--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/b386bbc78a81a72061126042d3deda8cf4942d62/c11bc/files/storageacct5.png)

耶！您可以下载它或通过提供的 URL 访问它。很简单，对吧？

让我们做同样的事情...不一样。

# 在 C# 🗄️中提供 Azure 存储 Blob 容器

是的，我们使用的代码是 C#，但是如果你不知道 C#(我不知道！)Michael Crump 写了这段代码，我认为不管你选择哪种语言，发生的事情都很清楚。

在门户中导航到您的 Azure 存储帐户。

在`Settings`下查找，然后`Access Keys`并复制连接字符串。

[![connection string](img/a4786ebeb27760500fdbf0496d8a3ff5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mbRZ0jcG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/748934007efa227bfcb9fbb6e8fe148be9211a3c/4d7d8/files/storagethroughcsharp1.png)

创建一个 C#控制台应用程序，并使用 NuGet 获取对以下内容的引用:

*   WindowsAzure。储存；储备
*   微软。windows azure . configuration manager

[![console application](img/e319efaee2cba94d0f1b7d226cad3bbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dBACQCNF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/5f3c5e84c8a898d7633004c069b634042bba6c87/125ce/files/storagethroughcsharp3.png)

在您的控制台应用程序中，您会看到`App.config`，现在添加以下部分:

```
<appSettings>
  <add key="StorageConnection" value="YOUR-CONNECTION-STRING-COPIED-FROM-EARLIER"/>
</appSettings> 
```

将下面的代码复制到您的 Main 方法中:

```
static void Main(string[] args)
{
    var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnection"));
    var myClient = storageAccount.CreateCloudBlobClient();
    var container = myClient.GetContainerReference("images-backup");

container.CreateIfNotExists(BlobContainerPublicAccessType.Blob);
    Console.ReadLine();
} 
```

这段代码将从`App.config`获取我们的连接字符串，创建我们的客户端和一个名为`images-backup`的容器(如果它不存在的话)。我们可以回到门户内部，看看执行是否正确。

成功！我们现在已经通过 Azure 门户和 C#完成了同样的任务。

**想要更多 Azure 存储空间？**查看我们的[快速入门和教程](//%7B%7B%7BLINK%7D%7D%7D?WT.mc_id=azureapril_devto-blog-cxa)！

* * *

我们将在 4 月份每天发布文章，所以请继续关注或提前查看更多提示和技巧[现在](http://azuredev.tips)。
# 修复 Azure 函数和 Azure 存储模拟器 5.8 问题

> 原文：<https://dev.to/maximrouiller/fixing-azure-functions-and-azure-storage-emulator-58-issue-5o4>

所以你已经把 Azure 函数更新到了最新版本(2。写这篇文章的时候是 x)，然后就什么都没开始了。

现在，当您启动函数主机时，您会得到这个奇怪的错误。

```
[TIMESTAMP] A host error has occurred
[TIMESTAMP] Microsoft.WindowsAzure.Storage: Server encountered an internal error. Please try again after some time. 
```

GitHub 上恰好有一个关于持久功能和 Azure 存储模拟器的问题。

问题是，它与 Azure 持久功能没有直接关系。在我看来，这与 Azure Storage Emulator 5.8 ways 响应方式的突破性变化有关。

如果您想解决这个问题，请在您`local.settings.json`文件中合并以下设置。

```
{  "Values":  {  "AzureWebJobsSecretStorageType":  "files"  }  } 
```

这仅适用于`"AzureWebJobsStorage": "UseDevelopmentStorage=true"`时。

那么我们为什么要设置它呢？早在 2018 年 9 月 Azure Functions V2 发布时就有所改变。Azure 函数在 2.0 之前是把你的秘密存储在磁盘上的。Azure 函数，当[插槽交换环境](https://docs.microsoft.com/azure/app-service/deploy-staging-slots?WT.mc_id=devto-blog-marouill#swap-two-slots)时，交换包括秘密在内的磁盘内容。

这个设置的作用是确保函数在默认情况下将机密存储在文件系统中。这是使用本地开发环境时的预期行为。

如果你想了解更多，这里有一整篇关于这种行为的文章。

## 结论

要解决这个问题，您可以使用变通办法或者[将 Azure 存储模拟器更新到 5.9](https://docs.microsoft.com/azure/storage/common/storage-use-emulator?WT.mc_id=devto-blog-marouill) 。
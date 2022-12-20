# 通过 ARM 部署带有应用洞察的 Azure 功能

> 原文：<https://dev.to/aloneguid/deploying-azure-functions-with-application-insights-via-arm-1o9k>

最近我已经开始真正地、端到端地玩 Azure Function，把它推到极限，但那是另一个话题了。我掌握它们的最后一英里是通过 ARM 模板在 Microsoft Azure 中自动创建必要的资源。功能本质上需要 4 种资源:

*   Azure Functions 资源本身，您将在其中部署您的函数应用程序。
*   一个托管计划，无论是虚拟主机还是消费。网站托管计划是网站使用的一个经典计划——你预先分配一定的资源来使用并支付费用，不管你是否在使用它们。消费计划是真正的交易，因为它将按执行时间收费，所以如果你不使用任何东西，你也不用支付任何东西。我一般会推荐所有功能应用的消费计划。
*   一个存储帐户，azure 函数需要它来存储内部状态。
*   Azure Application Insights 用于监控，除非您正在使用另一个监控解决方案。

###### 应用见解

创建人工智能资源很简单:

```
{  "apiVersion":  "2014-04-01",  "name":  "[variables('appInsightsName')]",  "type":  "Microsoft.Insights/components",  "location":  "[resourceGroup().location]",  "properties":  {  "applicationId":  "[variables('appInsightsName')]"  }  } 
```

###### 消耗计划

以及在 ARM 中创建消耗计划:

```
{  "comments":  "consumption plan for functions",  "type":  "Microsoft.Web/serverfarms",  "sku":  {  "name":  "Y1",  "tier":  "Dynamic",  "size":  "Y1",  "family":  "Y",  "capacity":  0  },  "kind":  "functionapp",  "name":  "[variables('functionsWebFarmName')]",  "apiVersion":  "2016-09-01",  "location":  "[resourceGroup().location]",  "scale":  null,  "properties":  {  "name":  "[variables('functionsWebFarmName')]",  "perSiteScaling":  false,  "reserved":  false,  "targetWorkerCount":  0,  "targetWorkerSizeId":  0  },  "dependsOn":  []  } 
```

###### 存储账户

存储帐户也没有意外:

```
{  "type":  "Microsoft.Storage/storageAccounts",  "sku":  {  "name":  "Standard_LRS",  "tier":  "Standard"  },  "kind":  "Storage",  "name":  "[variables('storageAccountName')]",  "apiVersion":  "2018-07-01",  "location":  "[resourceGroup().location]",  "properties":  {  "networkAcls":  {  "bypass":  "AzureServices",  "virtualNetworkRules":  [],  "ipRules":  [],  "defaultAction":  "Allow"  },  "supportsHttpsTrafficOnly":  false  }  } 
```

###### 把这一切联系在一起

最后一部分，function app 本身，就有点棘手了，尤其是如果你想自动将 azure 功能与 application insights 集成的话。完整的模板在这里，我也会解释一下。

```
{  "type":  "Microsoft.Web/sites",  "kind":  "functionapp",  "name":  "[variables('functionsAppName')]",  "apiVersion":  "2016-08-01",  "location":  "[resourceGroup().location]",  "properties":  {  "serverFarmId":  "[resourceId('Microsoft.Web/serverfarms', variables('functionsWebFarmName'))]",  "siteConfig":  {  "appSettings":  [  {  "name":  "AzureWebJobsStorage",  "value":  "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]"  },  {  "name":  "AzureWebJobsDashboard",  "value":  "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]"  },  {  "name":  "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",  "value":  "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]"  },  {  "name":  "WEBSITE_CONTENTSHARE",  "value":  "[toLower(variables('functionsAppName'))]"  },  {  "name":  "FUNCTIONS_EXTENSION_VERSION",  "value":  "~2"  },  {  "name":  "APPINSIGHTS_INSTRUMENTATIONKEY",  "value":  "[reference(concat('microsoft.insights/components/', variables('appInsightsName'))).InstrumentationKey]"  }  ]  }  },  "dependsOn":  [  "[resourceId('Microsoft.Web/serverfarms', variables('functionsWebFarmName'))]",  "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"  ]  } 
```

你会注意到，function app ARM resource 毕竟不是一个特殊的资源类型，它只是一个普通的 web app ( `"type": "Microsoft.Web/sites"`)，那么你怎么把 AI 资源，和存储绑定到它呢？奇怪的是，这是通过应用程序属性完成的！

*   AzureWebJobsDashboard 是某种我不确定的东西，与日志记录相关的东西，它是由 Azure Portal 设置的，因此为了一致性，我把它留在这里。
*   AzureWebJobsStorage 将存储帐户绑定到函数 app。该值必须是一个连接字符串，就像前面的参数一样，因此我在这里生成它。如你所见，我使用了内置的字符串连接函数，并通过 ARM 函数动态获取存储键，因为它们是同一个模板的一部分
*   我也不太确定 WEBSITE _ CONTENTAZUREFILECONNECTIONSTRING，但它也存在于 Azure Portal 生成的资源中，所以我也在这里添加了它以保持一致性。如果你知道它是做什么的，请在这里评论。
*   WEBSITE_CONTENTSHARE 也是如此——它被设置为应用程序名，这也是我正在生成的。
*   FUNCTIONS_EXTENSION_VERSION 负责设置运行时使用的函数。~1 是老函数 v1 的运行时，但我用的是新的 shiny。net core v2，因此它被设置为~2。
*   APPINSIGHTS_INSTRUMENTATIONKEY 是启动与应用洞察集成的重要键。这意味着每一个函数的执行都会被记录下来，你也会在 Azure Portal 中很好地看到它。通过反复试验，我发现这个参数名必须大写，否则无法工作。我在同一个 ARM 文件中声明了 AI 资源，因此我可以通过引用它来获得 AI 键。*

一旦部署，通过门户手动创建的功能 app 和通过 arm 模板创建的功能 app 没有区别。

*本文原载于[isolineltd.com](https://www.isolineltd.com/blog/deploying-azure-functions-with-application-insights-via-arm.html)2019 年 2 月 15 日*
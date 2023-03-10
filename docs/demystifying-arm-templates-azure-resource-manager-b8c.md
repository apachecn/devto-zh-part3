# 揭开 ARM 模板的神秘面纱(Azure 资源管理器)

> 原文：<https://dev.to/azure/demystifying-arm-templates-azure-resource-manager-b8c>

*我们选择了由[迈克尔·克伦普](https://twitter.com/mbcrump)创造的我们最喜欢的技巧和诀窍，并在整个四月在 Azure 上提供新的技术内容！错过一天(或更多)？[赶上系列](https://dev.to/t/azureapril)。*

**没有天蓝色？** [抢免费订阅](https://azure.microsoft.com/en-us/free?&WT.mc_id=azureapril_devto-blog-cxa)。

* * *

如果你在技术领域呆得够久，你可能会把 ARM 和 CPU 架构联系在一起。这里 ARM 代表 [Azure 资源管理器](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview?WT.mc_id=azureapril_devto-blog-cxa)。ARM 是你在 Azure 上组织所有东西的方式——虚拟机(VM)、数据库、存储账户、容器、web 应用、机器人等等。

> 在 Azure，我们把你所有的东西都叫做资源。

[![portal](img/914dba39af0b0e3eecf7d414ce8f6ee4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c6vehvtg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://microsoft.github.io/AzureTipsAndTricks/files/resources_page.PNG)

大多数人倾向于在 Azure 中一起创建多个相关的项目。一个 web 应用程序和一个数据库可能包含在一个资源组中。但是如果你不得不一直这样做呢？还是针对很多不同的客户？

如果有一种简单的方法来跟踪您为人们创建的所有可重复的资源组，这不是很好吗？好消息，这正是 [ARM 模板](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview?WT.mc_id=azureapril_devto-blog-cxa)所做的！

**想要更多 ARM 模板？**查看我们的[概述](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview?WT.mc_id=azureapril_devto-blog-cxa)！

# 真实场景为 ARM 模板

当你需要一种简单的方法来重复 Azure 上的基础设施部署时，ARM 模板将为你节省大量时间。

如果你需要和其他人分享你的基础设施——比如开源项目或者博客、教程和文档——ARM 模板将会是一个救命稻草，让你的读者和用户复制你所做的。

最后，如果你只是需要一种方法来跟踪你在 Azure 上部署了什么，ARM 模板是一种帮助你记忆的好方法。

# 这只是一个 JSON 文件

这就是 ARM 模板的用武之地。ARM 模板是 JSON 文件，类似于您想要一起部署的相关资源的蓝图。你还会听到这个叫做“基础设施作为代码”，或者 IaC，这是极客的说法，如果你愿意，可以把你的基础设施笔记上传到 GitHub。

这是一种结构化的格式，用于跟踪你的 Azure 基础设施和一些超级能力。ARM 模板最大的优势是，你可以使用模板来自动化你的基础设施部署，因为 Azure 知道如何阅读它们。毕竟它真的只是一个 JSON 文件。

在下面的例子中，我们正在创建一个创建通知中心的 ARM 模板。您将看到它包含部署所需的东西，例如`Name`、`Location`等。

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "namespaceName": {
            "type": "string",
            "metadata": {
                "description": "The name of the Notification Hubs namespace."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location in which the Notification Hubs resources should be deployed."
            }
        }
    },
    "variables": {
        "hubName": "MyHub"
    },
    "resources": [
        {
            "apiVersion": "2014-09-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.NotificationHubs/namespaces",
            "location": "[parameters('location')]",
            "kind": "NotificationHub",
            "resources": [
                {
                    "name": "[concat(parameters('namespaceName'), '/', variables('hubName'))]",
                    "apiVersion": "2014-09-01",
                    "type": "Microsoft.NotificationHubs/namespaces/notificationHubs",
                    "location": "[parameters('location')]",
                    "dependsOn": [
                        "[parameters('namespaceName')]"
                    ]
                }
            ]
        }
    ]
} 
```

# 入门

你可以在 [Visual Studio](https://docs.microsoft.com/en-us/azure/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy?&WT.mc_id=azureapril_devto-blog-cxa) ，在 [VSCode](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI?&WT.mc_id=azureapril_devto-blog-cxa) ，或者在 [Azure portal](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal?&WT.mc_id=azureapril_devto-blog-cxa) 做一个 ARM 模板。

最后一种方法可能是最简单的，因为它会引导您完成整个过程。通过点击 Azure 门户仪表板上的`Create Resource`,开始像平常一样通过门户创建资源。

现在选择您想要创建的内容。我要创建一个 Web 应用程序。看看这一页的底部，你会看到自动化选项。

[![options](img/68d5028e45d8d4e99e65b076a96304f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N628Vldy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://microsoft.github.io/AzureTipsAndTricks/files/new_webapp.png)

但是旁边那个不起眼的标签为`Automation options`的链接是做什么的呢？您可能以前从未注意到它，或者害怕弄乱它，但是如果您单击该链接，您将踏上为您的资源创建 ARM 模板的道路。

# 更进一步

继续点击 Azure 门户内的`Create a resource`并选择`Web App`。

为您的 web 应用程序输入一个`Name`和一个`Resource Group`，点击底部的`Automation options`，然后点击`Create`，开始创建您的 ARM 模板。

[![ARM template](img/68d5028e45d8d4e99e65b076a96304f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N628Vldy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://microsoft.github.io/AzureTipsAndTricks/files/new_webapp.png)

点击`Automation options`后，你会看到这样的内容:

[![automation options](img/cdad5bf60726bc0da5d559e9ffc58c3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--srBvWCeS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://microsoft.github.io/AzureTipsAndTricks/files/arm_template.png)

创建 web 应用程序(或任何其他 Azure 资源)的模板只是一个 JSON 文件，其中包含多个描述如何部署 web 应用程序的值。一旦你克服了花哨的行话，这是非常简单的。而且我们让你不用说流利的 JSON 也能轻松入门。

# 为您的 [Azure 应用服务创建静态应用设置](https://azure.microsoft.com/en-us/services/app-service/)

为了使事情尽可能简单，我们现在假设您想要在每次部署 web 应用程序模板时添加完全相同的设置。

转到`Deploy`然后`Edit Template`，粘贴下面的设置片段来覆盖你的模板的资源部分。(当然，您可以根据 web 应用程序的需要添加任意数量的键，或者根据需要进行更改。)

> 我们为`MyFirstName`、`MyLastName`和`MySSN`添加了三个名字和三个值。

```
 "resources": [
  {
    "apiVersion": "2016-03-01",
    "name": "[parameters('name')]",
    "type": "Microsoft.Web/sites",
    "properties": {
        "name": "[parameters('name')]",
        "siteConfig": {
            "appSettings": [
            {
              "name": "MyFirstName",
              "value": "Michael"
            },
            {
              "name": "MyLastName",
              "value": "Crump"
            },
            {
              "name": "MySSN",
              "value": "355-643-3356"
            }
          ]
        },
        "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]"
    },
    "location": "[parameters('location')]"
  }], 
```

按下`Save.`确保设置已填写完毕。同意条款并勾选`Purchase`选项。

[![template settings](img/ff985235b324aea737790f036220f1a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ltHkL4iQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://microsoft.github.io/AzureTipsAndTricks/files/customdeployment.png)

> 如果显示部署失败，那么再试一次。有时异步返回是唯一的问题。

您的设置(针对`MyFirstName`、`MyLastName`和`MySSN`)现在将被部署。

部署后，导航到您的`App Service`并转到`Application Settings`。您将看到您的站点已部署，并带有我们之前指定的设置。

[![application settings](img/f05eb4d5d4426f1048d4e0a804e2a18e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AH3sVm1Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://microsoft.github.io/AzureTipsAndTricks/files/create_resource1.png)

现在让我们看看添加参数。

# 部署前输入数值

您已经看到，您可以使用 ARM 模板自动部署静态配置信息，如应用程序设置。但是，提供允许最终用户在部署之前输入值的参数怎么样呢？

继续在 Azure 门户中搜索`templates`，然后点击`Add`创建一个新的。

在 ARM 模板上输入名称和描述。

[![enter name](img/1c3287f22dc2607fa1601b122950d875.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZzlHCXRu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://microsoft.github.io/AzureTipsAndTricks/files/customdeploy5.png)

# 选择自己的冒险

我们希望每次部署 web 应用程序时都可以自定义动态设置，而不是每次都相同，您只需将所需的参数值添加到 ARM 模板中。

下面是我们之前硬编码的三个值(`FirstNameValue`、`LastNameValue`和`SSNValue`)的示例:

```
"FirstNameValue": {
    "type": "string"
},
"LastNameValue": {
    "type": "string"
},
"SSNValue": {
    "type": "string"
}, 
```

我们将把名为`FirstNameValue`、`LastNameValue`和`SSNValue`的相同参数添加到模板的参数集合中。从现在开始，每次部署这个模板时，系统都会提示您为每个模板输入一个值。

```
"siteConfig": {
    "appSettings": [
        {
            "name": "MyFirstName",
            "value": "[parameters('FirstNameValue')]"
        },
        {
            "name": "MyLastName",
            "value": "[parameters('LastNameValue')]"
        },
        {
            "name": "MySSN",
            "value": "[parameters('SSNValue')]"
        }
    ]
} 
```

# 把一切串在一起

我们完整的模板文件如下所示:

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appServiceName": {
            "type": "string",
            "minLength": 1,
            "maxLength": 10
        },
        "appServicePlanName": {
            "type": "string",
            "minLength": 1
        },
        "FirstNameValue": {
                "type": "string"
            },
            "LastNameValue": {
                "type": "string"
            },
            "SSNValue": {
                "type": "string"
            },
        "appServicePlanSkuName": {
            "type": "string",
            "defaultValue": "S1",
            "allowedValues": [
                "F1",
                "D1",
                "B1",
                "B2",
                "B3",
                "S1",
                "S2",
                "S3",
                "P1",
                "P2",
                "P3",
                "P4"
            ],
            "metadata": {
                "description": "Describes plan's pricing tier and capacity. Check details at https://azure.microsoft.com/en-us/pricing/details/app-service/"
            }
        }
    },
    "variables": {
        "appHostingPlanNameVar": "[concat(parameters('appServicePlanName'),'-apps')]"
    },
    "resources": [
        {
            "name": "[variables('appHostingPlanNameVar')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[resourceGroup().location]",
            "apiVersion": "2015-08-01",
            "sku": {
                "name": "[parameters('appServicePlanSkuName')]"
            },
            "dependsOn": [],
            "tags": {
                "displayName": "appServicePlan"
            },
            "properties": {
                "name": "[variables('appHostingPlanNameVar')]",
                "numberOfWorkers": 1
            }
        },
        {
            "name": "[parameters('appServiceName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "apiVersion": "2015-08-01",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', variables('appHostingPlanNameVar'))]"
            ],
            "tags": {
                "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('appHostingPlanNameVar')))]": "Resource",
                "displayName": "webApp"
            },
            "properties": {
                "name": "[parameters('appServiceName')]",
                "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('appHostingPlanNameVar'))]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "MyFirstName",
                            "value": "[parameters('FirstNameValue')]"
                        },
                        {
                            "name": "MyLastName",
                            "value": "[parameters('LastNameValue')]"
                        },
                        {
                            "name": "MySSN",
                            "value": "[parameters('SSNValue')]"
                        }
                    ]
                }
            }
        }
    ],
    "outputs": {}
} 
```

如果保存该模板，那么下次使用该 ARM 模板部署资源时，您将需要为名、姓和 SSN 输入一个新值，该值将在应用程序设置中使用。

[![customized template](img/14aa8aceb76224adce068a4d86532c70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--18YYDb_Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://microsoft.github.io/AzureTipsAndTricks/files/customdeploy3.png)

部署后，去检查你的应用程序设置。

[![check settings](img/ba4ac1a50da625c7d8a3477e5c46c51f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3AUD1QDb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://microsoft.github.io/AzureTipsAndTricks/files/customdeploy4.png)

很酷吧。一旦我克服了什么是 ARM 模板以及我为什么需要它们的困惑，我的大脑就像打开了一样。ARM 模板通过自动化资源管理和按需定制值，让您的生活更加轻松。

**想了解更多关于 ARM 模板的信息？**查看我们的[概述](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview?WT.mc_id=azureapril_devto-blog-cxa)！

* * *

我们将在 4 月份每天发布文章，所以请继续关注或提前查看更多提示和技巧[现在](http://azuredev.tips)。
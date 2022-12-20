# 如何让您的部署每次都成功

> 原文：<https://dev.to/fboucheros/how-to-make-your-deployment-successful-every-time-3k8b>

您已经完成了代码，并准备好在 Azure 中部署它。您执行 PowerShell 或 Bash 脚本，然后嘣！显示该名称已被占用的错误消息。在这篇文章中，我将向你展示一个简单的方法，让你看起来像一个老板，并让你的部署一直有效。

法文版[此处](http://www.cloudenfrancais.com/posts/Comment%20faire%20en%20sorte%20que%20vos%20d%C3%A9ploiement%20fonctionne%20%C3%A0%20tout%20coup.html)

> 具有给定名称 _ _ _ _ _ 的 _ _ _ _ _ 已经存在。

## 其他使用的招数

您可以尝试在资源名称的末尾添加一个数字(例如:demo-app1、demo-app2、demo-app123...)，但那不是真正的专业。你可以创建一个随机的字符串并把它附加到名字后面。是的，那会起作用的，一次。如果你试图重新部署你的资源，价值将会改变，因此它将永远不会相同。

解决方案是拥有一个在我们的环境中不变的唯一字符串。

## 解

解决方案是使用 Azure 资源管理器(ARM)模板的函数`UniqueString()`部分。如果我们查看一下[文档](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-functions-string#uniquestring?WT.mc_id=cloud5mins-youtube-frbouche)，uniquesting*会基于作为参数提供的值创建一个确定性的散列字符串。*让我们来看一个部署名为`demo-app`的网站的 ARM 模板的快速示例。

```
{  "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  "contentVersion":  "1.0.0.0",  "parameters":  {},  "variables":  {  "webAppName":  "demo-app"  },  "resources":  [  {  "type":  "Microsoft.Web/sites",  "apiVersion":  "2015-08-01",  "name":  "[variables('webAppName')]",  "location":  "[resourceGroup().location]",  "tags":  {  "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/frankdemo-plan')]":  "Resource",  "displayName":  "[variables('webAppName')]"  },  "dependsOn":  [  "Microsoft.Web/serverfarms/frankdemo-plan"  ],  "properties":  {  "name":  "[variables('webAppName')]",  "serverFarmId":  "[resourceId('Microsoft.Web/serverfarms/', 'frankdemo-plan')]"  }  },  {  "type":  "Microsoft.Web/serverfarms",  "apiVersion":  "2016-09-01",  "name":  "frankdemo-plan",  "location":  "[resourceGroup().location]",  "sku":  {  "name":  "F1",  "capacity":  1  },  "tags":  {  "displayName":  "frankdemo-plan"  },  "properties":  {  "name":  "frankdemo-plan"  }  }  ],  "outputs":  {}  } 
```

Enter fullscreen mode Exit fullscreen mode

如果您尝试部署此模板，将会出现错误，因为名称`demo-app`已经被占用...这并不奇怪。

让我们创建一个新变量`suffix`，我们将使用**资源组 Id** 和**位置**作为值。然后我们只需要使用函数`concat()`将这个值添加到我们的名字中。

```
 "variables":  {  "suffix":  "[uniqueString(resourceGroup().id, resourceGroup().location)]",  "webAppName":  "[concat('demo-app', variables('suffix'))]"  } 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单！现在，每次您将部署一个独特的字符串将被添加到您的资源名称。对于资源组位置部署，该字符串将始终相同。

因为有些资源类型比其他资源类型更严格，所以您可能需要修改您的新名称。也许您的资源名称加上这十三个字符的散列会太长...没问题，你可以通过使用`substring()`和`toLower()`很容易地使它变得更短和全部小写。

```
 "parameters":  {},  "variables":  {  "suffix":  "[substring(toLower(uniqueString(resourceGroup().id, resourceGroup().location)),0,5)]",  "webAppName":  "[concat('demo-app', variables('suffix'))]"  } 
```

Enter fullscreen mode Exit fullscreen mode

瞧，现在通过使用 ARM 模板，您可以毫无问题地部署和重新部署您构建的相同解决方案。要了解有关 ARM 模板的 move，您可以跳转到[文档](https://docs.microsoft.com/en-us/azure/azure-resource-manager/?WT.mc_id=cloud5mins-youtube-frbouche)，在那里您可以找到示例、分步教程等。

如果你有一个关于 ARM 模板的具体问题，或者如果你想看到更多类似的提示，请不要犹豫，在评论区提问或在社交媒体上联系！

## 请上一段视频！

如果你喜欢，我也有这个帖子的视频。

[https://www.youtube.com/embed/dnb-f4C052w](https://www.youtube.com/embed/dnb-f4C052w)
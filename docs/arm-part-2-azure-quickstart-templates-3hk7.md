# ARM–第 2 部分:Azure 快速入门模板

> 原文：<https://dev.to/chris_l_ayers/arm-part-2-azure-quickstart-templates-3hk7>

## 潜进去的时间

我是那种喜欢边做边学的人。阅读文档很棒，我经常这样做。但对我来说，要真正找到某样东西，我需要玩它，运行它，可能还要把它放大。

如果你错过了第一部分，请继续阅读并回来。我需要为我的示例项目安装一个 WebApp。我意识到我有几种方法可以做到。一些方法是非常手工的，一些是可重复的，但是有一个让我印象深刻。

*   在 Azure 门户中创建资源
*   当我右击发布时，在 Visual Studio 中创建资源
*   通过 Powershell 脚本创建资源
*   通过 Azure CLI 创建资源
*   **创建 ARM 模板，在部署时或通过 CLI 创建资源**

## 我们来写一个 ARM 模板

我知道我想做什么，写一个 ARM 模板。我是否知道我在做什么，或者在这一点上从哪里开始？对我来说幸运的是，微软提供了一系列**资源来帮助你起步。有一个带有示例的[完整可搜索快速入门页面](https://azure.microsoft.com/en-us/resources/templates/)(由 [github repo](https://www.github.com/Azure/azure-quickstart-templates) 支持)以及[ARM 模板上的微软文档](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates)。**

嗯，我在快速入门里找到了一个:[https://azure . Microsoft . com/en-us/resources/templates/101-web app-basic-windows/](https://azure.microsoft.com/en-us/resources/templates/101-webapp-basic-windows/)。从这个快速入门，我可以点击部署到 Azure，填写一些信息，我就有了一个应用服务计划和一个 Web 应用。**这太酷了。**现在把这个东西拆开，弄清楚它是什么。

## 好了，我们来玩一个手臂模板

在 [github repo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) 中，看起来有 2 或 3 个文件组成了 ARM 模板。

### 解剖

让我们来剖析一下 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-webapp-basic-windows/azuredeploy.json) 。模式、版本等等…

#### 参数

我注意到了参数部分。我在部署到 Azure 时就认识其中的一些。感受一下参数，我发现你可以用元数据来描述它们，类型，长度和默认值。这给了我很多想法和选择。

#### 变量

这不是你妈妈的 json。对我来说那看起来像一个函数！看来有人在我的 json 里得到了他们的代码。我喜欢。稍后快速搜索谷歌，我在微软的文档中看到一个巨大的[模板函数](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-functions)列表。我会回来的。同时，这些变量看起来很像 webapp 和 AppServicePlan 的名称。我可以接受这个。

#### 资源

肉和土豆。头奖。这看起来像是定义资源的地方。每一个只有一些属性，一个驱动程序，类型，名称，位置，sku，和一些其他的。我还注意到有一个“依赖”属性，我打赌我可以建立依赖于什么的东西，它会以正确的顺序做所有的事情。

## 下一步

我现在有了一个可以工作的 ARM 模板。我已经研究过一点了。是时候让这个东西飞起来了。让我们在 DevOps 上设置一个 Azure Repo，看看如何尝试部署这个东西，并在发布时创建我的 webapp。我希望能够签入更改，并让它在 azure 中创建/更新我的资源。下一次，我们开始反馈循环。
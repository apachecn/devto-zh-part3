# Blazor 早期预览版 Telerik 用户界面介绍

> 原文：<https://dev.to/progresstelerik/introducing-the-telerik-ui-for-blazor-early-preview-2f4c>

Progress Software 和 Telerik 品牌在支持方面有着悠久的历史。NET 社区，产品有 ASP.NET 的 Telerik UI、ASP.NET 的 UI 和 ASP.NET 的 UI。这就是为什么我们自豪地宣布 Blazor 的 Telerik UI 的早期预览版！

1.  布拉索是什么
2.  什么是剃须刀组件
3.  布拉佐推荐阅读
4.  从头开始建造- 100%原生。网
5.  和我们一起实验
6.  Blazor 早期预览版的 Telerik UI

## 什么是 Blazor

Blazor 是微软[ASP.NET](http://ASP.NET)团队的一个新框架，它引入了下一代组件模型，允许开发者不用 JavaScript 就能编写单页应用程序(SPA)。如果 Blazor 对你来说听起来很陌生，那是因为它的开发还不到一年，而且还是一个实验项目。最初，Blazor 框架的目标是 **Mono WebAssembly 运行时**。NET 运行时编译为`wasm`)，从而允许。NET 在客户端的浏览器上运行，由此产生了“Blazor”(浏览器+ Razor)这个名字。

[![](img/337bb6225ed6b65de2f5e9652ee04e5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DNwxS1Cx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/dhrE75J.jpg)

在这种配置中，包括`.dll`文件在内的应用资源被交付给客户端，并由 **Mono WebAssembly 运行时**执行。虽然 Blazor 的 WebAssembly 部署仍在积极开发中，但引入了一个名为 Razor Components 的服务器端部署选项。

### Blazor 组件模型

Blazor 组件模型的设计非常简单。组件可以在一个 Razor (cshtml)文件中包含标记(HTML)和逻辑(C#)。该组件能够在没有 JavaScript 的情况下处理数据绑定、事件和依赖注入。

下面的计数器组件演示了 Blazor 组件的基本组成。

[![](img/3cdb35d4bb9555d76f19e0e94f65ba3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--10sAXkXq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/MOXoKaQ.jpg)

计数器组件使用一个基本的 HTML 按钮来增加显示在段落标记中的计数器字段。因为 Blazor 作为单页面应用程序运行，所以组件中的所有交互都发生在客户端。对浏览器文档对象模型(DOM)的更新由 Blazor 框架通过数据绑定来处理。

## 什么是剃须刀组件

最近，微软宣布了在服务器端以一种叫做 Razor 组件的模式运行时支持该框架的计划。甚至在最近，一项声明宣布，同样的组件模型将向后兼容[ASP.NET](http://ASP.NET)核心(MVC)和 Razor 页面。所有这些关于 Blazor 的前沿新闻和点滴让我们在 Telerik 的团队对 ASP.NET[发展的未来感到兴奋。与运行在 WebAssembly 上的 Blazor 非常相似，Razor Components 允许开发人员编写没有 JavaScript 的单页应用程序(SPA)。](http://ASP.NET)

[![](img/5e9ef9b7ba73d6a5b126c29d32519f86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yWfS47ug--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/i1smLgn.jpg)

Blazor 和 Razor 组件的关键区别在于应用程序的部署方式。Blazor 框架在服务器上作为可执行文件运行，而不是通过 WebAssembly 在客户端运行。在这种模式下，[ASP.NET](http://ASP.NET)内核托管应用，并使用 SignalR 技术提供通信通道。使用 SignalR，应用程序通过 web socket 连接发送 UI 更新，并以二进制数据包的形式接收更改和事件。因为只有更改通过连接发送，所以有效负载很小而且很有效。

因为 Razor 组件利用了 Blazor 框架，所以组件可以在两种部署类型中使用。

## Blazor 推荐阅读

如果所有这些对你来说都是新的，而且很有可能是新的，那么我们有一些博客文章让你了解 Blazor 的所有事情。下面的文章应该能让你跟上速度，或者如果你正咬着指甲想看看下面有什么，我们也包括了 TLDR。

*   Blazor——基于浏览器的新框架。网络应用(DevReach 2018)
*   什么来了。网芯 3.0
*   [布拉索问&微软的丹尼尔·罗斯](https://dev.to/progresstelerik/blazor-qa-with-microsofts-daniel-roth-3d48)
*   [再见 JavaScript，你好 WebAssembly](https://dev.to/progresstelerik/goodbye-javascript-hello-webassembly-6oc-temp-slug-9525238)
*   [Blazor 项目类型的细分](https://dev.to/progresstelerik/a-breakdown-of-blazor-project-types-2b4g-temp-slug-848040)
*   [2019 年无 JavaScript 前端的 Razor 组件](https://dev.to/progresstelerik/razor-components-for-a-javascript-free-frontend-in-2019-1f6o)

### TLDR

**Razor 组件**，是最初于 2018 年初开始的 **Blazor 服务器端**。Blazor 是一个. NET (SPA)框架，通常与。NET 运行在 Web 程序集上。然而，Blazor 能够在多种场景下运行，包括作为 Razor 组件的服务器端。

*   Razor 是一种流行的模板标记语法。网
*   (浏览器+ Razor) **Blazor** 是一个基于. NET 的 web 框架，它可以**使用 WebAssembly** 在客户端上运行，或者作为:
    *   **Razor 组件**:Blazor 框架**通过 SignalR 运行在服务器上**
    *   通过“**电路**的剃须刀组件——剃须刀组件运行在【ASP.NET】T2 核心/剃须刀页面
*   所有 Blazor 托管模型，包括客户端和服务器端，都使用 C # API**而不是 JavaScript**
*   剃刀组件预计**在[ASP.NET](http://ASP.NET)核心 3.0** 出货

## 从头打造——100%原生。网

用于 Blazor 的 Telerik UI 将**而不是**用 C#包装现有的 jQuery/JavaScript 产品，并假装它是新的东西。使用 Telerik UI for Blazor，我们可以从头开始使用编写组件。尽可能使用网络。或者，我们可以决定包装现有的 jQuery 组件，作为短期途径，但是我们觉得包装器编程模型是一个有漏洞的抽象，会“回流”到。NET API 层。另外，我们觉得一个本地人。净方法是一项长期投资，从长远来看将获得更大的收益。这是一个现代的龟兔大战的故事。

我们无法表达 JavaScript 和。网络生态系统对我们来说。我们为所有技能类型的开发人员提供了 UI 库，但是。NET 社区表达了对不需要 JavaScript 的前端 web 工具的需求，我们很高兴有机会通过 Blazor 为这个社区服务。

## 和我们一起实验

布拉索是一个令人兴奋的前景。NET 开发人员，因为它允许我们创建全栈。NET web 应用程序。我们收到了无数的反馈，要求我们支持这个下一代平台。

最初我们为 Blazor 提供 Telerik UI 作为早期预览版。这种开发模式非常类似于微软与 Blazor 所做的努力，因为我们的目标是发布小功能集，希望获得关于我们的客户如何使用该产品的反馈和知识。在试验阶段，面向 Blazor 的 Telerik UI 将对所有人免费试用,我们希望您能继续与我们分享您的用例、经验、障碍和错误。

下载 Blazor 的 Telerik UI 后，您将会收到一封电子邮件，说明如何与我们分享您的反馈、愿望和 Blazor 实验。

## 用于 Blazor 早期预览的 Telerik UI

最初的产品将很小，只有几个流行的 UI 组件，包括**数据网格**、**标签集**和**按钮**。通过客户的反馈，我们计划扩大组件的数量和 API 的范围。我们相信，根据客户的需求和建议进行构建是成功之路。

### 先决条件

按照 Blazor 网站上概述的[步骤安装 Blazor SDK](https://blazor.net/docs/get-started.html)

直接下载 Blazor NuGet 包的 [Telerik UI](https://www.telerik.com/download-trial-file/v2-b/ui-for-blazor)

(可选)[使用 Telerik NuGet feed 作为包源](https://docs.telerik.com/aspnet-mvc/getting-started/nuget-install#the-telerik-private-nuget-feed)

### 你的第一个 Blazor 项目

当使用新项目对话框时，我们建议从“Blazor(服务器端在[ASP.NET](http://ASP.NET)核心中)”项目类型开始。该项目类型也被称为**“剃刀组件”，最终将与[ASP.NET](http://ASP.NET)Core 3.0**一起发布。然而，用于 Blazor 的 Telerik UI 将在所有 Blazor 项目类型上工作。

[![](img/b289e97e7d554523deb2ad76e55eb29f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z2ITpeDG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/7l0z7qr.jpg)

随着新项目的创建，我们需要为 Blazor NuGet 依赖项安装 Telerik UI。在添加包之前，请确保导航到包含应用程序 UI 的项目。这将是`<project>.App`或`<project>.Client`，取决于您选择的模板。

安装`Telerik.UI.for.Blazor` NuGet 包。这将组件库添加到您的应用程序中。

在应用程序中注册组件。在应用程序的根目录中，找到`_ViewImports.cshtml`文件并添加以下代码。

```
@using Kendo.Blazor

@addTagHelper *,Kendo.Blazor 
```

1.  我们还需要引用组件所需的样式表。在`/wwwroot`文件夹中找到`index.html`文件，并添加下面一行。

```
<link href="//kendo.cdn.telerik.com/2019.1.115/styles/kendo.material-v2.min.css" rel="stylesheet"> 
```

注意，我们没有引用任何 JavaScript 文件，这是因为没有任何 JavaScript 依赖关系。您现在已经准备好开始测试 Blazor 的 Telerik UI 了。

### Blazor 数据网格

在这个预览版中，用于 Blazor 数据网格的 Telerik UI 有相当多的特性。该版本的数据网格具有:数据绑定、排序、分页、[主题](http://themebuilder.telerik.com)、模板、单元格内编辑，仅支持`int`、`string`和`DateTime`字段。

通过用 Telerik 数据网格替换**获取数据**示例中的手工编码表，让我们来看看这些功能的运行情况。首先花点时间运行并探索在`localhost/fetchdata`获取数据的例子。

在`/Pages`文件夹下找到获取数据的代码。用一个`KendoGrid`部件替换整个`table`元件。*用于 Blazor 组件的 Telerik UI 使用**剑道**名称空间，因为它是我们现有前端库的熟悉引用，并与这些库共享 CSS 代码。*

```
else
{
  <KendoGrid Data=@forecasts Pageable=true PageSize=5 Sortable=true>
    <KendoGridColumn Field=@nameof(WeatherForecast.Date)>
      <Template>
        @($"{(context as WeatherForecast).Date:d}")
      </Template>
    </KendoGridColumn>
    <KendoGridColumn Field=@nameof(WeatherForecast.TemperatureC) />
    <KendoGridColumn Field=@nameof(WeatherForecast.TemperatureF) />
    <KendoGridColumn Field=@nameof(WeatherForecast.Summary) />
  </KendoGrid>
} 
```

`KendoGrid`组件将`Data`属性绑定到`forecasts`，后者是`WeatherForecast`对象的数组。网格还启用了`Pageable`、`PageSize`和`Sortable`属性。在`KendoGrid`组件内部，我们为希望在网格中显示的每个字段定义了子组件。因为这都是 C#代码，我们可以用 C#的`nameof`操作符设置字段属性，给我们类型安全。此外，模板可以用来显示自定义格式、图像，甚至其他 UI 组件。这里使用一个模板来格式化`Date`字段。

[![](img/8378312483b31ed096f2ffc8be439009.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--To-rxaQZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/JnF5seI.gif)

### Blazor 标签集

这个版本中包含的另一个主要组件是`KendoTabSet`。`KendoTabSet`支持多个标签位置:顶部(默认)、底部、左侧和右侧。我们可以使用 Blazor 的`bind`属性来演示运行时的标签位置。在`/pages`文件夹下找到`index.cshtml`页面。用下面的代码替换页面的内容。

```
@using Kendo.Blazor.Components.TabStrip

<h1>Hello, world!</h1>
<select bind=@tabPosition>
  <option value=@KendoTabPosition.Top>Top</option>
  <option value=@KendoTabPosition.Left>Left</option>
  <option value=@KendoTabPosition.Right>Right</option>
  <option value=@KendoTabPosition.Bottom>Bottom</option>
</select>

<KendoTabStrip TabPosition=@tabPosition>
  <KendoTab Title="Sofia">
    <h2>22<span>ºC</span></h2>
    <p>Sunny weather in Sofia</p>
  </KendoTab>
  <KendoTab Title="New York">
    <h2>24<span>ºC</span></h2>
    <p>Partly Cloudy weather in New York</p>
  </KendoTab>
  <KendoTab Title="Paris">
    <h2>21<span>ºC</span></h2>
    <p>Rainy weather in Paris</p>
  </KendoTab>
</KendoTabStrip>

@functions {
  KendoTabPosition tabPosition = KendoTabPosition.Top;
} 
```

在这个例子中，我们首先创建一个简单的`select`列表元素，包含所有的`KendoTabStrip`位置值、`KendoTabPosition.<value>`。最重要的是，`KendoTabPosition`是一个标准的 C#枚举类型，所以我们在这里得到强类型值和智能感知。接下来，用几个显示天气预报数据的`KendoTab`组件创建一个`KendoTabStrip`组件。`TapPosition`属性通过在`@function`中声明的简单支持字段`tabPosition`绑定到`select`元素和`KendoTabStrip`。

因为`select`正在使用`bind`属性，当选项改变时，它自动更新`tabPosition`值，这允许我们在运行时修改位置。

[![](img/5752e419399a14105a461d47f68f4017.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zY0ijr1T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/kVkwnss.gif)

## 总结

我们很高兴通过支持 Blazor 成为下一代[ASP.NET](http://ASP.NET)应用的一部分。[Blazor 早期预览版的 Telerik UI 今天免费提供](https://www.telerik.com/download-trial-file/v2-b/ui-for-blazor)。该包目前包括从头开始编写的数据网格、选项卡集和按钮组件，以利用 Blazor 而不依赖 JavaScript(这次没有 jQuery)。今年，微软将致力于 Blazor(又名剃刀组件)的开发，作为 ASP.NET 核心 3.0[的一部分。我们计划随着项目的进展保持 Telerik UI for Blazor 的更新，并期待听到您的反馈，看到您已经构建了什么，并知道您的下一个应用程序需要什么组件。](http://ASP.NET)

## 报名参加发布会

眼见为实，请注册查看所有新功能——网络研讨会即将推出！如果您[在此](https://www.telerik.com/support/whats-new)下载最新版本，将有助于您轻松跟上进度。

### 电视

日期/时间:1 月 18 日星期五美国东部时间上午 11:00-下午 12:00[立即注册](https://www.telerik.com/campaigns/wb-jan-2019-release-r1)

### 剑道 UI

日期/时间:1 月 22 日星期二上午 11:00 东部时间-下午 12:00 东部时间[立即注册](https://www.telerik.com/campaigns/kendo-ui/wb-jan-2019-release-r1)
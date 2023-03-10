# Telerik ASP.NET 数据网格快速介绍——AJAX、MVC 和 Core

> 原文：<https://dev.to/progresstelerik/a-quick-intro-to-the-telerik-aspnet-datagrids--ajax-mvc-and-core-2f0f>

每个 Telerik ASP.NET 数据网格都有 100 多种内置功能，您将获得一个强大的组件，它为如何呈现和执行底层数据操作提供了多种选项。

如你所知，ASP.NET 有三种不同的味道。一个并不比另一个更好，但是您使用哪一个将主要取决于您的最终应用目标。如果你计划构建一个可以在 Windows、macOS 或 Linux 上运行的基于云的应用，你会选择**ASP.NET 核心**。如果你对轻量级和高度可测试的东西感兴趣，你可能会选择 ASP.NET 的 MVC**。或者，如果你来自桌面背景，喜欢声明式和基于控件的编程方法，【ASP.NET】Web Forms**可能是你的首选框架。

对于任何 web 应用程序，您可能会发现自己需要组件或控件来提供现代化的 UI。因为我们构建组件，所以我们对开发人员在其应用程序中使用最多的组件有一些了解。毫无疑问，在所有框架中，最流行的是 **DataGrid** 。

有道理。数据网格是一种普遍存在的表示和操作数据的方式。

## 基本数据网格元素

任何试图构建网格的开发人员都会告诉你，网格不是静态的。功能网格是交互式的；它是有结构的，通常需要比你第一眼看到的更多的东西。

下面描述的数据网格是相当常见的。它包括一些最常见的功能，如分页、过滤、分组、编辑、嵌套表等。

[![ASP.NET DataGrid Essential Elements](img/9f4a21002c960fab0b847b9f07f41eaf.png "ASP.NET DataGrid Essential Elements")](https://res.cloudinary.com/practicaldev/image/fetch/s--PhjqPDUW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/asp-net-datagrid-elementsd3734a240ab4469f8a989bc7d6febc7e.png%3Fsfvrsn%3D2a6f61cf_1)

网格是 ASP.NET AJAX 的 [Telerik UI，ASP.NET MVC](https://www.telerik.com/products/aspnet-ajax.aspx)的 [Telerik UI，以及 ASP.NET Core](https://www.telerik.com/aspnet-mvc)工具集的 [Telerik UI 的一部分。用于 ASP.NET AJAX 的 Telerik UI 可以作为独立产品购买，也可以作为](https://www.telerik.com/aspnet-core-ui) [Telerik DevCraft 捆绑包](https://www.telerik.com/devcraft)的一部分购买。其他的可以作为[剑道 UI](https://www.telerik.com/kendo-ui) 的服务器端附件或者在 Telerik DevCraft 中购买。

每个 Telerik ASP.NET 数据网格都有 100 多种内置功能，您将获得一个强大的组件，它为如何呈现和执行底层数据操作提供了多种选项。除了排序、过滤、分组、编辑和导出等基本和预期功能外，还支持更多高级功能，如明细行、行重新排序、滚动模式、多行或单元格选择、响应式设计等。就数据绑定而言，您选择的数据源和实现方式可能因框架而异，但如果您使用 ASP.NET 泰利瑞克数据网格控件，您通常会有多个数据绑定选项。

因为每个框架都有些不同，所以让我们探索一下由 Telerik 团队构建的 ASP.NET AJAX、MVC 和核心数据网格。

## ASP.NET 网络表单(又名 AJAX)数据网格

[ASP.NET AJAX DataGrid](https://www.telerik.com/products/aspnet-ajax/grid.aspx)是[Telerik ASP.NET AJAX 套件](https://www.telerik.com/products/aspnet-ajax.aspx)中最成熟、最先进的控件之一，具有丰富的功能库，如[数据绑定到各种数据源](https://demos.telerik.com/aspnet-ajax/grid/examples/data-binding/client-side/client-data-source-binding/defaultcs.aspx)、[编辑](https://demos.telerik.com/aspnet-ajax/grid/examples/data-editing/automatic-crud-operations/defaultcs.aspx)、[过滤](https://demos.telerik.com/aspnet-ajax/grid/examples/functionality/filtering/basic-filtering/defaultcs.aspx)、[排序](https://demos.telerik.com/aspnet-ajax/grid/examples/functionality/sorting/basic-sorting/defaultcs.aspx)、 [CRUD 操作](https://demos.telerik.com/aspnet-ajax/grid/examples/data-editing/manual-crud-operations/defaultcs.aspx)、[移动支持](https://demos.telerik.com/aspnet-ajax/grid/examples/functionality/mobile-and-touch-support/adaptive-behavior/defaultcs.aspx)、[分组](https://demos.telerik.com/aspnet-ajax/grid/examples/functionality/grouping/grouping/defaultcs.aspx)等等。这些特征中的每一个都比一个单词所暗示的更加丰富。

例如，通过 Telerik UI for ASP.NET AJAX 数据网格，您可以使用任何声明性数据源控件(AccessDataSource、SqlDataSource、LinqDataSource、EntityDataSource、ObjectDataSource、XmlDataSource、OpenAccessDataSource)或将网格绑定到您的自定义对象。我们还支持服务器事件，只有当网格需要从服务器获取新数据时，才会执行这些事件。还支持到 ASP.NET Web 服务、ADO.NET WCF、OData 服务和页面方法的声明式和编程式客户端绑定。

[![Telerik ASP.NET AJAX GridView - Data Binding](img/a3e49be2be7b2b16dff1f66ff6f6221e.png "Telerik ASP.NET AJAX GridView - Data Binding")](https://res.cloudinary.com/practicaldev/image/fetch/s--kbrKUtYz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/telerik-asp-net-ajax-gridview---data-binding.png%3Fsfvrsn%3D153871ef_1)

当绑定到数据源控件时，ASP.NET AJAX 网格的编辑特性允许您启用自动数据编辑。您还可以使用扩展的 API 以定制的方式处理 CRUD 操作。您可以以内联方式显示编辑表单，作为常规表单或在弹出窗口中显示，并在内置编辑表单、您自己的模板或用户控件之间进行选择。您还可以启用批量编辑，以允许用户编辑多个项目，并通过点击一次保存按钮来保存它们。

[![Telerik ASP.NET AJAX GridView - Editing](img/7d4fd0e6be0187d43a70215884d9a03d.png "Telerik ASP.NET AJAX GridView - Editing")](https://res.cloudinary.com/practicaldev/image/fetch/s--r1s8QlZf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/telerik-asp-net-ajax-gridview---editing.png%3Fsfvrsn%3D4d398b71_1)

过滤是另一项功能，为您提供了大量显示数据的选项。使用 Telerik ASP.NET 数据网格，您可以过滤任何类型的数据——字符串、小数、布尔、日期和时间。根据过滤的列，用户可以从上下文菜单中选择任何过滤功能，包括“等于”、“包含”、“大于”和“小于”您可以使用不同的网格列来提供合适的过滤器输入，如日期选择器、数字文本框和下拉列表。客户端和服务器端 API 允许您定义自己的过滤器模板，同时仍然利用内置的过滤机制。

[![Telerik ASP.NET AJAX GridView - Filtering](img/96d176ff9868d3d2bb26ddd8d859b2ec.png "Telerik ASP.NET AJAX GridView - Filtering")](https://res.cloudinary.com/practicaldev/image/fetch/s--tw-yL-7h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/ajax_grid_filtering.png%3Fsfvrsn%3D2f67bc0d_1)

这些例子仅仅是开始。要了解更多关于 Telerik ASP.NET AJAX 数据网格的功能，请务必浏览我们关于带有模板的[网格层次结构](https://demos.telerik.com/aspnet-ajax/grid/examples/hierarchy/hierarchy-with-templates/defaultcs.aspx)、[网格虚拟化](https://demos.telerik.com/aspnet-ajax/grid/examples/performance/virtualization/defaultvb.aspx)、[类似 excel 的过滤](https://www.telerik.com/blogs/excel-like-filtering-using-telerik-ui-for-asp.net-ajax-grid)、[移动友好网格](https://www.telerik.com/blogs/the-most-mobile-friendly-grid-for-your-responsive-asp.net-web-forms-apps)，以及[ASP.NET AJAX Telerik UI 入门技巧](https://dev.to/progresstelerik/did-you-know-10-useful-telerik-ui-for-aspnet-ajax-tips-2cf6-temp-slug-5747128)的资源。

当您开始看到数据网格的深度和每个特性的丰富性时，您将意识到使用正确的控件可以对数据网格做多少事情。你也不会惊讶地发现，主要的组织和机构，如美国运通，美国银行和北卡罗来纳大学都使用了 ASP.NET AJAX 控件的 Telerik 用户界面。

**[试用](https://www.telerik.com/download-trial-file/v2-b/ui-for-asp.net-ajax) |** [**网页邮件示例 App**](https://demos.telerik.com/aspnet-ajax/webmail/) **|** [**演示**](https://demos.telerik.com/aspnet-ajax/grid/examples/overview/defaultcs.aspx) **|** [**文档**](https://docs.telerik.com/devtools/aspnet-ajax/controls/grid/overview)

## ASP.NET MVC 数据网格

用于 ASP.NET MVC 网格的[Telerik UI](https://www.telerik.com/aspnet-mvc/grid)支持与您的数据进行高度复杂的交互。

例如，世界上最大的闪存产品制造商之一 SanDisk 在一个应用程序中使用了 ASP.NET MVC 控件的 [Telerik UI，该应用程序旨在帮助其员工计划和预测他们所服务的所有不同市场的生产和销售数据，而无需维护电子表格。分析师可以维护他们预测的各种版本，并将它们合并到由计划经理或副总裁开发的最终发布版本中。这些报告在每个季度末使用 Tableau 等 BI 工具发布，并发布给更多的受众，包括公司的首席执行官和其他决策者。](https://www.telerik.com/aspnet-mvc)

最终应用以网格为特色，使用基于 HTML5 和 jQuery 的剑道 UI 控件、面向 ASP.NET MVC 的 Telerik UI、一个 web API 和一个使用专门为 SAP HANA 开发的自定义数据访问层的服务层构建。您可以从我们的[案例研究](https://www.progress.com/customers/sandisk)中了解更多关于应用和项目的信息。

与 ASP.NET 网络表单/AJAX 网格非常相似，ASP.NET MVC 数据网格的 Telerik UI 提供了基本的网格特性，如使用非常直观的界面对数据进行分页、排序、过滤和分组。你可以[调整](https://demos.telerik.com/aspnet-mvc/grid/column-resizing)的大小、[重新排序](https://demos.telerik.com/aspnet-mvc/grid/column-reordering)你的列，甚至可以轻松地单独隐藏或显示它们。您可以[冻结单个属性的列](https://demos.telerik.com/aspnet-mvc/grid/frozen-columns)，或者允许用户交互地锁定和解锁列，或者甚至轻松地[为您的父记录和子记录](https://demos.telerik.com/aspnet-mvc/grid/hierarchy)创建层次结构。使用网格组件的模板特性，您可以完全控制复杂的格式。您可以选择[工具栏模板](https://demos.telerik.com/aspnet-mvc/grid/toolbar-template)、[列标题模板](https://demos.telerik.com/aspnet-mvc/grid/headertemplate)、[行模板](https://demos.telerik.com/aspnet-mvc/grid/rowtemplate)、[明细模板](https://demos.telerik.com/aspnet-mvc/grid/detailtemplate)等。

[![Telerik ASP.NET MVC DataGrid - Essential Features](img/e58050ba0d51d46556281ba004bb141e.png "Telerik ASP.NET MVC DataGrid - Essential Features")](https://res.cloudinary.com/practicaldev/image/fetch/s--jc4NALd7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/telerik-asp-net-mvc-datagrid---essential-features.gif%3Fsfvrsn%3D9571a26f_1)

而且，正如我们在关于 Web 表单的部分中提到的，每个特性都有深度和丰富性，使您能够创建出色的用户体验。

以剪辑为例。编辑可以很容易地连接到 RESTful API，或者处理本地数据。编辑表单可以在[内嵌](https://demos.telerik.com/aspnet-mvc/grid/editing-inline)，或者在[弹出](https://demos.telerik.com/aspnet-mvc/grid/editing-popup)中呈现。您甚至可以进行[批量更新](https://demos.telerik.com/aspnet-mvc/grid/editing)，以便在将更改保存到数据中之前允许进行许多更改。这允许您的用户创建、更新甚至删除底层数据源中的数据。

[![Telerik ASP.NET MVC DataGrid - Editing](img/964b909e799519e3db9b6f5f40fc145f.png "Telerik ASP.NET MVC DataGrid - Editing")](https://res.cloudinary.com/practicaldev/image/fetch/s--YtGqUVq5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/telerik-asp-net-mvc-datagrid---editing.gif%3Fsfvrsn%3D871f240e_1)

由于我们已经提到了数据源，使用 MVC DataGrid，您可以使用 Kendo UI DataSource 实用程序轻松地配置[远程数据绑定](https://demos.telerik.com/aspnet-mvc/grid/remote-data-binding)，它可以与任何 RESTful API 一起工作。您可以连接到任何[本地数据源](https://demos.telerik.com/aspnet-mvc/grid/local-data-binding)，或者通过 JSON/JSONP、OData 或 XML 格式进行远程通信。

[![Telerik ASP.NET MVC Grid - Data Sources](img/dc44d642f90042565f1b8c38d69943a4.png "Telerik ASP.NET MVC Grid - Data Sources")](https://res.cloudinary.com/practicaldev/image/fetch/s--jFPYBjdx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/telerik-asp-net-mvc-datagrid---data-binding.png%3Fsfvrsn%3D68556050_1)

同样，我们只是用这些例子探索了冰山一角，所以请务必查看我们的演示和[。NET DevChat:使用 ASP.NET MVC 和 Bootstrap 4 的响应式 Web 应用](https://www.telerik.com/blogs/net-devchat-responsive-web-apps-asp-net-mvc-bootstrap)和使用 C#扩展方法重构数据网格的[文章。](https://developer.telerik.com/topics/net/refactoring-data-grids-c-extension-methods/)

[**试炼**](https://www.telerik.com/download-trial-file/v2-b/ui-for-asp.net-mvc#register) **|** [**剑道 UI ♥自举示例 app**](http://demos.telerik.com/aspnet-mvc/bootstrap)**|**[**演示**](https://demos.telerik.com/aspnet-mvc/grid)|**[文档](https://docs.telerik.com/aspnet-mvc/helpers/grid/overview)**

## ASP.NET 核心数据网格

ASP.NET 核心是一个开源的跨平台框架，由微软于 2016 年 6 月首次发布。那年秋天，Progress 发布了用于 ASP.NET 核心工具集的 Telerik UI。

在产品上市的短短两年内，[ASP.NET 核心数据网格](https://www.telerik.com/aspnet-core-ui/grid)已经迅速成为工具集中最受欢迎的组件。与其他 ASP.NET 数据网格一样，它是一个响应迅速、适应性强的 HTML5 网格，提供了 100 多种功能，从[过滤](https://demos.telerik.com/aspnet-core/grid/filter-row)到[数据排序](https://demos.telerik.com/aspnet-core/grid/sorting)，再到更高级的功能，如[分页](https://demos.telerik.com/aspnet-core/grid/paging)和[分层数据分组](https://demos.telerik.com/aspnet-core/grid/hierarchy)。

除了传统的 HTMLHelpers(其中一个是 [Grid HtmlHelper](https://docs.telerik.com/aspnet-core/html-helpers/data-management/grid/overview) )之外，Telerik UI for ASP.NET Core 还提供了业内第一个完整的标签助手包。网格标签助手帮助你[在你的 ASP.NET 核心应用](https://www.telerik.com/blogs/migrating-to-asp-dotnet-core-tag-helpers)中配置剑道 UI 网格组件。

除了 ASP.NET 核心独有的标签助手之外，ASP.NET 核心和 ASP.NET MVC 数据网格共享相同的特性和功能。我们已经谈了一些关于编辑和数据源的内容，但是您是否知道 ASP.NET 核心网格的 Telerik UI 提供了[虚拟化](https://demos.telerik.com/aspnet-core/grid/virtualization-remote-data)和[无限滚动](https://demos.telerik.com/aspnet-core/grid/endless-scrolling-remote)功能，允许用户平滑地滚动记录？这为您提供了获得卓越性能的机会，无论数据库的大小或网格配置如何。

[![Telerik ASP.NET Core DataGrid Performance](img/808daa090fe4995bb972f202a5203861.png "Telerik ASP.NET Core DataGrid Performance")](https://res.cloudinary.com/practicaldev/image/fetch/s--m_H0-82L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/telerik-asp-net-core-datagrid-performance.png%3Fsfvrsn%3D9fa36cbd_1)

从可用性的角度来看，我们的数据网格满足可访问性需求以及多种交互机会。用于 ASP.NET 核心数据网格组件的 Telerik UI 符合 508 条款、WAI-ARIA 和 WCAG 2.1 标准，确保残障人士可以与之交互。它还提供键盘导航，使您能够创建一个允许用户使用鼠标点击、键盘敲击或两者兼有的应用程序。

[![Telerik ASP.NET Core DataGrid - Keyboard Navigation](img/989b5839b49bb52ab00f1832cc0afc28.png "Telerik ASP.NET Core DataGrid - Keyboard Navigation")](https://res.cloudinary.com/practicaldev/image/fetch/s--6Oiyhyt5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/telerik-asp-net-core-datagrid---keyboard-navigation.gif%3Fsfvrsn%3D9ab8d178_1)

仅网格就有 100 多种特性，因此我们的一些文章绝对值得一读，特别是[使用 alone 核心和数据网格](https://developer.telerik.com/topics/net/asp-net-core-and-data-grids/)、[制作 alone 核心数据网格原型](https://www.telerik.com/blogs/prototyping-aspnet-core-data-grids)和[开始使用 alone 核心和引导程序 4 的 Telerik UI](https://www.telerik.com/blogs/getting-started-telerik-ui-for-asp-net-core-and-bootstrap-4)。

如你所料，我们有一个演示页面库，包括从数据源插入、更新和删除数据的演示、导出到 PDF 的演示、冻结网格列的演示、分层网格的演示和列交互的演示。

****|[剑道 UI ♥自举示例 app](http://demos.telerik.com/aspnet-core/bootstrap)|**[**demos **]([https://demos.telerik.com/aspnet-core/grid](https://demos.telerik.com/aspnet-core/grid))**|**[* * html 助手文档**](https://docs.telerik.com/aspnet-core/html-helpers/data-management/grid/overview) **|** [**标签助手文档**](https://docs.telerik.com/aspnet-core/tag-helpers/data-management/grid/overview)**

 **## ASP.NET AJAX、MVC 和核心数据网格的附加资源

我们的 ASP.NET 工具的数据网格功能丰富、高性能、设计美观。但是不要只相信我们的话。一定要下载一个[免费 30 天试用版](https://www.telerik.com/download)，带他们去兜风。

并查看我们工具集中可用的其他一些网格资源和信息。

*   **文章:**[Web 应用中有效网格的 17 大技巧](https://www.telerik.com/blogs/top-17-tips-for-effective-grids-in-web-apps)
*   **主题:**所有网格都有三个基于 Sass 的主题——默认、引导和材料。我们有没有提到还有一些不那么基础的主题？还提供了[主题构建器](https://themebuilder.telerik.com/)应用程序，可以让你为每个框架创建新的或者定制现有的主题。
*   **无限的产品支持:**我们很自豪能够提供我们客户喜爱的[支持服务](https://www.telerik.com/support)。Progress 提供由产品工程师提供的无限支持，保证 24 小时响应时间，周一至周五。
*   StackOverflow: 我们是 Progress 的忠实粉丝。如果你想从更大的 Telerik 用户社区中学习，我们鼓励你访问 stackoverflow.com 的[并使用标签“telerik-grid”](https://stackoverflow.com/)

编码快乐！**
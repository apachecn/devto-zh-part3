# 剑道 UI JavaScript 网格的快速介绍——jQuery、Angular、React 和 Vue

> 原文：<https://dev.to/progresstelerik/a-quick-intro-to-the-kendo-ui-javascript-grids---jquery-angular-react-and-vue-8lb>

现代 web 应用程序为用户提供了许多不同的功能，但最常见的功能涉及数据的显示和操作。这可能是库存控制，或股票历史，或房价。大多数应用程序的共同点是数据。无论您的应用程序基于何种技术，您都可能需要让您的用户能够处理大量数据。值得花些时间来理解可视化数据最流行的组件:数据****网格**(也称为表)，并探索它可能具有的功能范围。网格是强大的组件，使您能够可视化、组织和编辑表格数据。**

 **下面的摘要简要概述了所有主流 JavaScript 框架的[剑道 UI](https://www.telerik.com/kendo-ui/) 网格组件的众多可用特性( [jQuery](https://www.telerik.com/kendo-ui/grid) 、 [Angular](https://www.telerik.com/kendo-angular-ui/components/grid/) 、 [React](https://www.telerik.com/kendo-react-ui/components/grid/) 和 [Vue](https://www.telerik.com/kendo-vue-ui/components/grid-native/) )。它们还会让您很好地了解网格可以拥有的特性的广度。对于这些框架中的每一个，我们都使用该框架技术从头开始开发网格，以实现最佳的集成和性能。

但是为什么首先要使用商业数据网格呢？这难道不是你可以自己开发的东西吗？也许吧，但这是你真正想花时间去做的吗？使用第三方 UI 组件可以帮助您分担一些 UI 开发工作，从而让您有更多时间关注应用程序的竞争优势。结果是，你大大减少了开发时间和成本，同时创建了一个一致和专业的应用程序。开发一个复杂的网格是一项坚实的事业，所以实现一个专业开发的、有文档记录的和受支持的网格的好处是相当大的。

在我们深入网格之前，请注意每个组件都是一个更大的应用程序的一部分，可能有其独特的设计要求。这不应该成为你使用组件库的障碍。这就是为什么我们创建了我们的[主题构建器](https://themebuilder.telerik.com/)工具，用它你可以快速设置我们所有组件的外观和感觉来匹配你现有的环境和主题需求。

## 剑道 UI jQuery 网格

用于 jQuery 的 Kendo UI 是一个流行的商业 UI 库，包括数据网格、图表、调度器和许多其他组件。剑道 UI 网格是[市场上最先进的 jQuery 网格](https://www.telerik.com/kendo-ui/grid)，拥有 100 多种内置功能。它拥有满足最严格的业务需求所需的一切。

为了用数据填充网格，您可以提供[本地](https://demos.telerik.com/kendo-ui/grid/local-data-binding)或[远程](https://demos.telerik.com/kendo-ui/grid/remote-data-binding)数据。有了剑道 UI [数据源](https://demos.telerik.com/kendo-ui/datasource/index)组件作为中介，你可以很容易地做到这一点。如果你需要创建带有动态数据的剑道 UI 网格，遵循这些步骤，展示如何用一行代码初始化一个动态的可编辑网格。在表示和执行底层数据操作时，该组件提供了多种选项。基本的有[分页](https://demos.telerik.com/kendo-ui/grid/index)、[排序](https://demos.telerik.com/kendo-ui/grid/sorting)、[过滤](https://demos.telerik.com/kendo-ui/grid/filter-row)和[选择](https://demos.telerik.com/kendo-ui/grid/selection)。然而，网格可以变得非常复杂，并且包括高级功能，例如[编辑](https://demos.telerik.com/kendo-ui/grid/editing)、[分组聚合](https://demos.telerik.com/kendo-ui/grid/aggregates)、[虚拟化](https://demos.telerik.com/kendo-ui/grid/virtualization-local-data)、[冻结列](https://demos.telerik.com/kendo-ui/grid/frozen-columns)、[导出](https://demos.telerik.com/kendo-ui/grid/excel-export)和[层次结构](https://demos.telerik.com/kendo-ui/grid/hierarchy)。

[![Kendo UI jQuery Grid - Editing Inline](img/47006f54592801524a5181fe68c07e33.png "jQuery-grid-editing-inline")](https://res.cloudinary.com/practicaldev/image/fetch/s--bg8iEL0m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/jquery-grid-editing-inlinea777f1c732264b9287729eb54e49f1bc.gif%3Fsfvrsn%3D45ed90ba_1)

应用速度通常是良好用户体验的首要因素，[我们已经写了优化 Kendo UI jQuery 网格性能的最佳实践](https://www.telerik.com/blogs/how-to-get-the-best-grid-performance)，包括你应该避免犯的常见错误。

在许多情况下，仅仅在表结构中可视化数据是不够的——您还需要让用户能够操作这些数据并保存他们的更改。Kendo UI 网格组件通过简单的数据源配置支持数据编辑操作(创建、更新、销毁)。它提供了几个编辑选项——[批处理](https://demos.telerik.com/kendo-ui/grid/editing)、[内嵌](https://demos.telerik.com/kendo-ui/grid/editing-inline)、[弹出](https://demos.telerik.com/kendo-ui/grid/editing-popup)和[自定义编辑器](https://demos.telerik.com/kendo-ui/grid/editing-custom)模板。

通常，您需要在现有的应用程序中实现网格。使用网格组件很容易做到这一点，因为它支持可重用的主题，包括 SASS 和 LESS。如果你需要改变网格的外观来匹配你公司的配色方案，定制一些现有的主题或者用[剑道 UI 主题生成器](https://themebuilder.telerik.com/kendo-ui)创建一个新的主题是你正在寻找的。网格本身提供了一组丰富的模板([行模板](https://demos.telerik.com/kendo-ui/grid/rowtemplate)、[工具栏模板](https://demos.telerik.com/kendo-ui/grid/toolbar-template)和[细节模板](https://demos.telerik.com/kendo-ui/grid/detailtemplate))，开发人员可以使用这些模板来配置组件的视觉和功能布局，以满足特定的项目需求。

在构建业务应用程序时，可以利用剑道 UI 的网格的其他基本特性是[键盘导航](https://demos.telerik.com/kendo-ui/grid/keyboard-navigation)、[本地化](https://demos.telerik.com/kendo-ui/grid/localization)(全球化)和 [RTL 支持](https://demos.telerik.com/kendo-ui/grid/right-to-left-support)。键盘支持提供了对可用网格特性的快速访问，而无需与鼠标交互。

一个好的网络应用不仅有用、快捷、美观，而且所有人都可以使用。从法律的角度来看，根据你和什么国家做生意，有不同的可及性标准。Kendo UI 通过提供额外的功能来帮助提高用户的可访问性，使您能够做正确的事情，并使遵从变得容易。我们的网格组件符合[第 508 节和 WCAG 2.1](https://docs.telerik.com/kendo-ui/accessibility/section-508-wcag) 指南，并包括 [WAI-ARIA 支持](https://docs.telerik.com/kendo-ui/accessibility/wai-aria-support-in-kendo)，确保残疾人能够使用它。

此外，剑道 UI 网格公开了丰富的 [API](https://demos.telerik.com/kendo-ui/grid/api) 和[事件](https://demos.telerik.com/kendo-ui/grid/events)，它们为内置特性之上的定制功能提供了简单的配置或扩展点。

由于它的优势，加上我们提供的高质量技术支持，剑道 UI 网格组件被众多大大小小的企业和组织使用。如果您需要尚未添加的功能，您可以随时使用剑道 UI [反馈门户](https://feedback.telerik.com/kendo-jquery-ui)告诉我们您的需求。

听起来很复杂？一切都被一丝不苟地记录下来。如果您没有找到您需要的学习资源，您可以随时提交一张票，免费 30 天试用或购买许可证。

**[试用](https://www.telerik.com/kendo-jquery-ui) **** | [演示](https://demos.telerik.com/kendo-ui/grid/index) | [文档](https://docs.telerik.com/kendo-ui/controls/data-management/grid/overview) **** | [仪表盘应用](https://demos.telerik.com/kendo-ui/html5-dashboard-sample-app/products-orders.html) | [三部分博客系列](https://dev.to/progresstelerik/how-to-use-a-jquery-grid-ui-component-in-your-web-app-1lm1-temp-slug-8528131) **

## 剑道 UI 角度网格

[Angular 的剑道 UI](https://www.telerik.com/kendo-angular-ui)包括 60 多个原生 Angular UI 组件，从头开始构建，[不断优化性能](https://dev.to/progresstelerik/introducing-the-60fps-grid-in-kendo-ui-for-angular-4p8-temp-slug-8058310)。它们专为 Angular 设计，使开发人员能够充分利用框架的本机性能，如提前编译(AOT)、Angular Universal 和 Tree Shaking。简而言之，使用这个工具集，您可以快速构建速度惊人的角度应用程序。

除了[分页](https://www.telerik.com/kendo-angular-ui/components/grid/paging/)、[编辑](https://www.telerik.com/kendo-angular-ui/components/grid/editing/)、[排序](https://www.telerik.com/kendo-angular-ui/components/grid/sorting/)、[过滤](https://www.telerik.com/kendo-angular-ui/components/grid/filtering/)、[分组](https://www.telerik.com/kendo-angular-ui/components/grid/grouping/)、不同的[滚动模式](https://www.telerik.com/kendo-angular-ui/components/grid/scroll-modes/)和导出( [PDF](https://www.telerik.com/kendo-angular-ui/components/grid/export/pdf-export/) 和 [Excel](https://www.telerik.com/kendo-angular-ui/components/grid/export/excel-export/) )、[角度网格](https://www.telerik.com/kendo-angular-ui/components/grid/)提供了[主从网格](https://www.telerik.com/kendo-angular-ui/components/grid/advanced-features/hierarchy/)、[明细行模板](https://www.telerik.com/kendo-angular-ui/components/grid/advanced-features/detail-template/)、[等高级功能](https://www.telerik.com/kendo-angular-ui/components/grid/advanced-features/toolbar-template/)

[![Kendo UI Angular Grid - Grouping Basics](img/e814413752d4f50d5597f44f884c40e1.png "Angular-grid-grouping-basics")](https://res.cloudinary.com/practicaldev/image/fetch/s--r4BMmDX8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/angular-grid-grouping-basics435827f5d7dd4f1989a684cbf948d33f.gif%3Fsfvrsn%3D75391a75_1)

Kendo UI [数据绑定指令](https://www.telerik.com/kendo-angular-ui-develop/components/grid/data-binding/automatic-operations/)是网格的一个强大特性，如果您需要操作网格中的数据，无论是通过过滤、排序还是分组，您都可以使用它。它通过减少重复的样板代码简化了数据操作的处理。

Angular Grid 的 Kendo UI 是一个完全意义上的企业级网格组件，包括[性能提示](https://www.telerik.com/kendo-angular-ui/components/grid/browser-performance/)、[全球化](https://www.telerik.com/kendo-angular-ui/components/grid/globalization/)、[键盘](https://www.telerik.com/kendo-angular-ui/components/grid/keyboard-navigation/)支持和[可访问性兼容](https://www.telerik.com/kendo-angular-ui/components/grid/accessibility/)、[行重新排序](https://www.telerik.com/kendo-angular-ui/components/grid/how-to/row-reordering/)和[上下文菜单](https://www.telerik.com/kendo-angular-ui/components/grid/how-to/context-menu/)。网格还公开了丰富的 [API](https://www.telerik.com/kendo-angular-ui/components/grid/api/) ，为更具体的场景提供了简单的配置。

对组件进行样式化是一个必要的步骤，您可以通过两种方式来处理:为 Angular 安装一个 Kendo UI 主题(默认、引导或材料),或者使用我们的[主题构建器](https://themebuilder.telerik.com/kendo-angular-ui) web 应用程序来使网格匹配您的样式表。关于如何添加样式的更多信息，请参考关于[样式](https://www.telerik.com/kendo-angular-ui/components/styling/)的文档。

**[试用](https://www.telerik.com/download-trial-file/v2/kendo-angular-ui) **** | [演示](https://www.telerik.com/kendo-angular-ui/components/grid/)* * * |[仪表盘渐进式 Web 应用](https://demos.telerik.com/kendo-ui/html5-dashboard-sample-app/products-orders.html) | [入门视频教程](https://www.telerik.com/campaigns/kendo-ui/using-kendo-ui-with-angular-video-tutorial) | [深潜博文](https://dev.to/progresstelerik/diving-into-the-kendo-ui-grid-with-angular-45lh-temp-slug-7158107) **

## KendoReact 网格

如果您的业务需求是在短时间内构建一个复杂的 React 应用程序，那么 [KendoReact](https://www.telerik.com/kendo-react-ui/) 本地组件库将帮助您应对这一挑战，并创建一个健壮且用户友好的应用程序。这个组件库是从头开始构建的，是专门为使用 React 进行响应式 web 开发而设计的。

这些组件完全基于 React 框架实现的方法，并且每个控件都为高级定制提供了各种选项。KendoReact 套件的灵活性使组件能够与 React 框架相关的几乎所有库进行整洁的集成，包括: [GraphQL](https://www.telerik.com/kendo-react-ui/components/integration/graphql/) 、 [Material UI](https://www.telerik.com/kendo-react-ui/components/integration/material-ui/) 、 [React Final Form](https://www.telerik.com/kendo-react-ui/components/integration/react-final-form/) 、 [Redux Form](https://www.telerik.com/kendo-react-ui/components/integration/redux-form/) 、 [Redux Store](https://www.telerik.com/kendo-react-ui/components/integration/redux-store/) 、 [Redux Undo](https://www.telerik.com/kendo-react-ui/components/integration/redux-undo/) 和[主题构建器样本](https://themebuilder.telerik.com/kendo-react-ui)。

当开始构建数据定期更新的大型 web 应用程序时， [KendoReact Grid](https://www.telerik.com/kendo-react-ui/components/grid/) 是合适的工具。它提供了全方位的配置选项，从标准操作([页面](https://www.telerik.com/kendo-react-ui/components/grid/paging/)、[编辑](https://www.telerik.com/kendo-react-ui/components/grid/editing/)、[过滤](https://www.telerik.com/kendo-react-ui/components/grid/filtering/)、[分组](https://www.telerik.com/kendo-react-ui/components/grid/grouping/)、[排序](https://www.telerik.com/kendo-react-ui/components/grid/sorting/)、[选择](https://www.telerik.com/kendo-react-ui/components/grid/selection/)，以及导出到 [PDF](https://www.telerik.com/kendo-react-ui/components/grid/pdf-export/) 和 [Excel](https://www.telerik.com/kendo-react-ui/components/grid/excel-export/) ，到更复杂的功能，如[层次结构](https://www.telerik.com/kendo-react-ui/components/grid/advanced-features/hierarchy/)、[冻结列](https://www.telerik.com/kendo-react-ui/components/grid/columns/locked/)、[列菜单](https://www.telerik.com/kendo-react-ui/components/grid/columns/column-menu/)

[![Kendo UI React Grid - Row Reordering](img/4dfcda225142269a25fc05500473d104.png "React-grid-row-reordering")](https://res.cloudinary.com/practicaldev/image/fetch/s--hfybioR5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/react-grid-row-reorderingcd2eb04f73eb437cb5d3714df73b25c8.gif%3Fsfvrsn%3Dff66bccd_1)

对于需要扩展现有功能的开发人员，有 Grid [API](https://www.telerik.com/kendo-react-ui/components/grid/api/) 。

KendoReact 数据网格允许您通过添加一个 CSS 引用来使用像 Twitter Bootstrap 和 Google Material Design 这样的设计范例。关于可用主题的更多信息在相应的文章中提供:[引导主题](https://www.telerik.com/kendo-react-ui/components/styling/theme-bootstrap/)、[默认主题](https://www.telerik.com/kendo-react-ui/components/styling/theme-default/)、[素材主题](https://www.telerik.com/kendo-react-ui/components/styling/theme-material/)。

**[试用](https://www.telerik.com/download-trial-file/v2-b/kendo-react-ui) **** | [演示](https://www.telerik.com/kendo-react-ui/components/grid/)* * * |[带有 Redux 的渐进式 Web 应用](https://telerik.github.io/react-dashboard/dashboard) | [入门视频教程](https://www.telerik.com/campaigns/kendo-ui/using-kendo-ui-with-angular-video-tutorial) | [概述博文](https://dev.to/progresstelerik/introduction-to-the-kendoreact-grid-2b3h) **

## 剑道 UI 视图网格

Vue 是一个流行的新兴框架，它在 web 应用程序中的采用正在快速增长。为了让这些应用更好更快，你可以利用 Vue 套件的[剑道 UI。这是一个完整的 UI 组件库，用于响应式 web 应用程序。为了给我们的用户提供最好的性能和特性集，我们的 Vue 库中的网格组件完全是用 Vue 原生构建的，没有其他库依赖。该库中剩余的剑道 UI 组件是我们 jQuery 库组件的 Vue 包装版本。](https://www.telerik.com/kendo-vue-ui)

Vue 组件的 Kendo UI 也有内置的功能来支持本地 Vue 反应，这是通过使用它们的 API 方法来实现的。这使得 Vuex 能够自动将更改传播到 Kendo UI 组件，并随着数据和状态的更改更新它们。这使得用于 Vue 套件的剑道 UI 可以完全集成到 Vuex 环境中。有关该主题的更多信息，请参考 [Vuex 集成文章](https://www.telerik.com/kendo-vue-ui/components/framework/vuex-integration/)。

[Vue 网格组件](https://www.telerik.com/kendo-vue-ui/components/grid-native/)让你能够[编辑](https://www.telerik.com/kendo-vue-ui/components/grid-native/editing/editing/)、[页面](https://www.telerik.com/kendo-vue-ui/components/grid-native/paging/)、[排序](https://www.telerik.com/kendo-vue-ui/components/grid-native/sorting/)，以及[过滤](https://www.telerik.com/kendo-vue-ui/components/grid-native/filtering/)你的数据。它还支持[分组](https://www.telerik.com/kendo-vue-ui/components/grid-native/grouping/)并在分组底部显示[聚合计算](https://www.telerik.com/kendo-vue-ui/components/grid-native/grouping/grouping/)。你可以从中受益的其他有用的特性有[虚拟化](https://www.telerik.com/kendo-vue-ui/components/grid-native/scroll-modes/#toc-virtual-scrolling)、[调整大小](https://www.telerik.com/kendo-vue-ui/components/grid-native/columns/resizing/)、[重新排序](https://www.telerik.com/kendo-vue-ui/components/grid-native/columns/reordering/)和[多列标题](https://www.telerik.com/kendo-vue-ui/components/grid-native/columns/multi-column/)等等。

[![Kendo UI Vue Grid - Column Menu](img/ba2be06087d49dd67ce6b46393885fd0.png "Vue-grid-column-menu")](https://res.cloudinary.com/practicaldev/image/fetch/s--YUC3g1cY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/vue-grid-column-menu0e30da70b61b4b6f979caccde1d953b2.gif%3Fsfvrsn%3D58b97683_1)

除此之外，你还拥有所有剑道 UI 所熟知的辅助功能，比如[键盘支持](https://www.telerik.com/kendo-vue-ui/components/grid/keyboard-navigation/)，508 区，WAI-ARIA 支持和 WCAG 2.1 兼容。

您可以轻松地将这个强大的网格组件集成到您的 Vue.js 项目中，并利用它所有的强大特性。它也非常适合创建高适应性的用户界面和单页面应用程序(spa)。网格有[默认](https://www.npmjs.com/package/@progress/kendo-theme-default)和[引导](https://www.npmjs.com/package/@progress/kendo-theme-default)基于 Sass 的主题，由于有了[主题生成器](https://themebuilder.telerik.com/kendo-vue-ui)应用，可以很容易地定制以适合你的颜色。

**[试用](https://www.telerik.com/download-trial-file/v2-b/kendo-vue-ui) **** | [演示](https://www.telerik.com/kendo-vue-ui/components/grid-native/)* * * |[仪表盘应用](https://telerik.github.io/vue-dashboard/#/) | [入门视频教程](https://www.telerik.com/campaigns/kendo-ui/using-kendo-ui-with-vue-video-tutorial) | [概述博文](https://dev.to/progresstelerik/introducing-the-new-native-kendo-ui-vuejs-grid-587-temp-slug-3272728) **

## 所有技术的共同特征

### 主题

所有网格都有三个基于 Sass 的主题——默认、引导和材料。您还可以利用[主题构建器](https://themebuilder.telerik.com/)应用程序，它使您能够创建新的主题或者定制每个框架的现有主题。

### 无限产品支持

我们很自豪能够提供客户喜爱的支持服务。Kendo UI 提供由产品工程师提供的无限支持，保证 24 小时响应时间(优先支持)，周一至周五。对于时间紧迫的开发项目，可以使用高级支持选项，包括更短的响应时间，甚至是实时电话支持。

### 综合工具集

我们为您提供全套工具箱。你可以用网格、下拉菜单、各种图表和许多其他组件来显示你的数据，这会让你的用户很高兴。

## 剑道 UI 网格:复杂 JavaScript 应用的最佳选择

Kendo UI 数据网格组件为复杂的 web 应用程序提供了最高级的数据选项。我们提供专门为每个主要 JavaScript 框架构建的组件，无论是基本的 jQuery、Angular、React 还是 Vue。这为您提供了每种技术的所有固有特性，以及高性能、优化的数据显示和操作解决方案。如果您需要改变技术环境，该怎么办？从一个框架中的剑道 UI 网格转移到另一个框架很容易。

Kendo UI 网格的好处不仅仅是更好的集成:我们提供了一个完全可定制的综合特性集。网格提供了 100 多个参数，可以快速轻松地设置这些参数来控制网格在应用程序中的外观和行为。您可以只公开对您的应用程序有意义的功能，而不必更改任何代码，只需通过一个简单的参数打开和关闭各个功能。这意味着您可以获得您想要的确切功能集，并根据您的特定需求快速定制。

虽然我们的网格是剑道 UI 库的旗舰，但它只是丰富的库中的一个组件，库[提供了开发人员在任何复杂的现代应用程序中可能需要的所有组件。从高级图表、调度器和日期选择器到下拉菜单、编辑器和开关，Kendo UI 提供了完整的 UI 组件。](https://www.telerik.com/kendo-ui)

剑道 UI 组件很容易与您的应用程序集成，并使编码变得简单。但是编码只是完整集成的一部分。您可以将时间集中在应用程序的核心功能上。[使用剑道 UI](https://www.telerik.com/purchase/kendo-ui) 更快地构建更好的网络应用。**
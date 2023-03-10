# 如何在 Web 应用程序中创建一个简单、响应迅速、动态的仪表板

> 原文：<https://dev.to/syncfusion/how-to-create-a-simple-responsive-and-dynamic-dashboard-in-web-application-4nbc>

仪表板对于跟踪来自多个数据源的战略数据、关键性能指标和重要指标非常有用，并且可以直观地呈现它们。但是，当您需要允许用户放置组件、拖放、调整大小和重新排序的功能时，设计仪表板是一项具有挑战性的工作。您还需要以响应性和适应性的布局呈现仪表板，以便可以在任何设备上以任何分辨率查看它，而不会丢失数据。

考虑到所有这些挑战，Syncfusion [仪表盘布局组件](https://www.syncfusion.com/javascript-ui-controls/js-dashboard-layout)得以实现。它将快速创建一个具有预期布局的动态仪表板，并允许您只考虑数据集成和投影。

您可以将组件放置在网格结构布局中的所需位置，并通过借助重新排序、拖放、调整大小、添加新功能和删除功能实时编辑面板来设计布局。

让我们尝试使用仪表板布局组件制作一个仪表板，如下图所示。

[![A simple dashboard with Grid, Chart and Map components.](img/ae5fed35848ec2658e55f168d841f162.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--FFX1Mo2p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/DSL-2.png) *一个简单的仪表盘，有网格、图表和地图组件。*

您可以从 GitHub 资源库获得演示示例:[如何创建动态响应的仪表板](https://github.com/SyncfusionExamples/How-to-create-a-dynamic-dashboard-with-responsive-and-adaptive-layout)

## 初始化仪表板布局组件

呈现仪表板布局组件所需的依赖项列表如下。

|–@ sync fusion/ej2-布局

|–@ sync fusion/ej2-base

要使用仪表板布局组件，我们需要从 npm 的[**@ sync fusion/ej2-layouts**](https://www.npmjs.com/package/@syncfusion/ej2-layouts)包中导入它，并将其附加到一个元素中，如下面的代码示例所示。

[index.ts]

```
//importing DashboardLayout from ej2-layouts 
import { DashboardLayout } from '@syncfusion/ej2-layouts';

let dashboard: DashboardLayout = new DashboardLayout({}); 

dashboard.appendTo('#editLayout'); 
```

【index.html】

```
<!-- dashboard layout element --> 

<div id="editLayout"></div> 
```

## 创建面板

面板是仪表板布局组件的构建块。它保存要可视化或呈现的数据(组件)。它提供了以所需方式调整组件大小、重新排序或定位组件的选项。如下面的代码示例所示，配置面板以放置在仪表板内。

[index.ts]

```
let dashboard: DashboardLayout = new DashboardLayout({ 
//splits the entire width of the component into 6 equal columns. 
columns: 6, 
//defines the width/height ratio of each panel, here panel height will be 10/8 of its width value. 
cellAspectRatio: 100 / 80, 
//adding panel properties. 
panels: [
{'sizeX': 2, 'sizeY': 1, 'row': 0, 'col': 0 }, 
{'sizeX':2, 'sizeY': 1, 'row': 0, 'col': 2 }, 
{'sizeX': 2, 'sizeY': 1, 'row': 0, 'col': 4 }, 
{'sizeX': 3, 'sizeY': 2, 'row': 1, 'col': 0 }, 
{'sizeX': 3, 'sizeY': 2, 'row': 1, 'col': 3 }, 
{'sizeX': 3, 'sizeY': 2, 'row': 4, 'col': 0 }, 
{'sizeX': 3, 'sizeY': 2, 'row': 4, 'col': 3 },

]}); 

dashboard.appendTo('#editLayout'); 
```

在上面的代码块中，**列**和**cellaspectatio**属性定义了面板尺寸(每个面板的高度和宽度)来表示每行中放置的列数和面板数。此外，您可以打开面板调整大小选项来进一步增强实时设计。

[![Dashboard layout with empty panels](img/136276b426a0ed3c0f6954b109f3e875.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--rYXONLCt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/empty_panels-with-expected-size-300x214.jpg) *带空面板的仪表盘布局*

## 将带有表头信息的组件添加到面板

在仪表板布局组件中，Panels 具有定义标题和内容的选项，并具有完整的定制功能。Header 保存补充数据以及任何操作项，如按钮或菜单。面板的内容可以采用任何数据，甚至是数据可视化组件，如图表、仪表或地图。请参考下面的代码示例向面板添加标题和内容。

[index.ts]

```
import { enableRipple } from '@syncfusion/ej2-base'; 

import { DashboardLayout } from '@syncfusion/ej2-layouts'; 

import { CircularGauge } from '@syncfusion/ej2-circulargauge'; 

import { gauge1, gauge2, gauge3 } from './gauge-component'; 

import { Grid } from '@syncfusion/ej2-grids'; 

// importing grid component. 
import { gridComponent } from './grid-component'; 

import { Maps} from '@syncfusion/ej2-maps'; 
// importing map component. 
import { mapsComponent } from './map-component'; 

import { Chart, Legend, Tooltip, ColumnSeries, Category, DataLabel } from '@syncfusion/ej2-charts'; 
// importing column chart. 
import { columnChartComponent } from './chart-component'; 

Chart.Inject(ColumnSeries, DataLabel, Category, Legend, Tooltip); 

enableRipple(true); 

// initializing DashboardLayout component. 

let dashboard: DashboardLayout = new DashboardLayout({

 columns: 5, 
cellSpacing: [25, 25], 
cellAspectRatio: 100 / 80, 

panels: [
{ 
'sizeX': 2, 'sizeY': 1, 'row': 0, 'col': 0, cssClass: 'card-template', content: '#card1' 
}, 
{ 
'sizeX': 2, 'sizeY': 1, 'row': 0, 'col': 2, cssClass: 'card-template', content: '#card2' 
}, 
{ 
'sizeX': 2, 'sizeY': 1, 'row': 0, 'col': 4, cssClass: 'card-template', content: '#card3' 
}, 
{ 
'sizeX': 3, 'sizeY': 2, 'row': 1, 'col': 0, header: '<div> Customer details</div>', content: '<div id="Grid"></div>' 
}, 
{ 
'sizeX': 3, 'sizeY': 2, 'row': 1, 'col': 3, header: '<div>Regional Map</div>', content: '<div id="map"></div>' 
}, 
{ 
'sizeX': 3, 'sizeY': 2, 'row': 4, 'col': 0, header: '<div>Sales in 2018 </div>', content: '<div id="colChart"></div>' 
}, 
{ 
'sizeX': 3, 'sizeY': 2, 'row': 4, 'col': 3, header: '<div> Sales Comparison </div>', content: '#card4' 
}
]}); 

dashboard.appendTo('#editLayout'); 

// initializing gauge components. 

let germany: CircularGauge = new CircularGauge(gauge1(), '#container1'); 

let usa: CircularGauge = new CircularGauge(gauge2(), '#container2'); 

let uk: CircularGauge = new CircularGauge(gauge3(), '#container3'); 

// initializing chart component. 

let chart: Chart = new Chart (columnChartComponent(),'#colChart'); 

// initializing grid component. 

let grid: Grid = new Grid(gridComponent(),'#Grid'); 

// intitalizing map component. 
let map: Maps = new Maps(mapsComponent(), "#map"); 
```

这里，面板的第一行包含 HTML 模板卡。然后，通过从 [grid-component.ts](https://github.com/SyncfusionExamples/How-to-create-a-dynamic-dashboard-with-responsive-and-adaptive-layout/blob/master/src/app/grid-component.ts) 文件中获取模型值来初始化和呈现网格组件。

[![Grid contains customer details as records](img/95deae9655ef7b50a4babab473166d5d.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--d7Y8x01b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/Grid.png) *网格面板内*

类似地，其他组件、图表、地图和轨距也分别从[图表组件. ts](https://github.com/SyncfusionExamples/How-to-create-a-dynamic-dashboard-with-responsive-and-adaptive-layout/blob/master/src/app/chart-component.ts) 、[地图组件. ts](https://github.com/SyncfusionExamples/How-to-create-a-dynamic-dashboard-with-responsive-and-adaptive-layout/blob/master/src/app/map-component.ts) 和[轨距组件. ts](https://github.com/SyncfusionExamples/How-to-create-a-dynamic-dashboard-with-responsive-and-adaptive-layout/blob/master/src/app/gauge-component.ts) 文件导入。

[![Shows the chart, map and gauge inside the panels](img/5f8f674a0fb76134c385e0f642ba9d9d.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--wE7STBrm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/ChartMapGuage.png) *图表、地图和圆形仪表盘内的仪表*

最后，您将获得一个简单的仪表板，带有卡片、网格、图表、仪表和地图，如下图所示，具有可编辑的布局。

[![A simple dashboard with DataGrid, Chart and Map components.](img/ae5fed35848ec2658e55f168d841f162.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--FFX1Mo2p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/DSL-2.png) *一个带有网格、图表和地图组件的简单仪表盘*

通过实时编辑仪表板，将布局重新设计为不同的结构，如下所示。

[![sales dashboard with live editing](img/4b7333849c4f6c1c578383e746ba87fc.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--yYJybVPV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/04/dashboardlayout.gif) *带现场编辑的仪表盘*

## 结论

我们希望这篇博客和分享的示例能够帮助您使用仪表板布局组件设计一个包含网格、图表和仪表的动态仪表板。仪表盘布局也有 [Angular](https://ej2.syncfusion.com/home/angular.html) 、 [React](https://ej2.syncfusion.com/home/react.html) 、 [Vue](https://ej2.syncfusion.com/home/vue.html) 、[ASP.NET MVC](https://ej2.syncfusion.com/home/aspnetmvc.html)和[ASP.NET Core](https://ej2.syncfusion.com/home/aspnetcore.html)框架可供选择，您可以根据自己喜欢的框架来试用样品。

要了解有关仪表板布局组件的更多信息，请参考 Syncfusion [在线样本](https://ej2.syncfusion.com/demos/#/material/dashboard-layout/analytics-dashboard.html)和[文档](https://ej2.syncfusion.com/documentation/dashboard-layout/getting-started)。我们期待您尝试这一组件，并通过这个[反馈门户](https://www.syncfusion.com/feedback/)提供您的反馈。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums)或 [Direct-Trac](https://www.syncfusion.com/support/directtrac/) 联系我们。我们很乐意为您提供帮助！

帖子[如何创建一个简单、响应迅速、动态的仪表板](https://blog.syncfusion.com/post/how-to-create-a-simple-responsive-and-dynamic-dashboard.aspx)首先出现在[的 Syncfusion 博客](https://blog.syncfusion.com)上。
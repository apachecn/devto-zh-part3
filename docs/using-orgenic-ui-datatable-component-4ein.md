# 使用有机 ui 数据表组件

> 原文：<https://dev.to/tikri/using-orgenic-ui-datatable-component-4ein>

ORGENIC UI 是一个不断发展的 web 组件集，专注于 UX 和设计。本教程演示如何使用 datatable 组件。

datatable 组件可用于在高性能的灵活表格中显示大型数据集。该组件基于[制表器](https://github.com/olifolkerd/tabulator)和一些易于使用的接口来隐藏库的复杂性。

在本教程中，我们使用没有任何框架的普通 JS，但你也可以使用任何框架，如 Angular，React 或 Vue，以及其舒适的数据绑定。

## 通用

数据表本身只是一行代码。像配置和数据提供者这样的其他东西都是通过 JavaScript 设置的，或者可以使用您选择的框架通过数据绑定来设置。

```
<og-datatable id="datatable"></og-datatable> 
```

在 JavaScript 中，使用查询选择器解析数据表元素:

```
const datatable = document.querySelector('#datatable'); 
```

## 配置

datatable 只有一个带有列定义的配置对象和一个数据服务，该服务可以提供客户端数据或来自后端的(惰性加载的)数据。此外，还可以定义在没有可用数据时显示的可选消息。

```
datatable.config =  {
    noDataMessage: 'No items available',
    dataService: paginatedDataService,
    columns: columnConfiguration
}; 
```

## 列

基本的列声明非常简单。它只是数据属性和列标题的定义。

```
const columnConfiguration = [
    //...
    {
        property: 'name',
        title: 'Label'
    },
    //...
]; 
```

### 自定义列格式化程序

自定义列格式化程序可以指定为字符串或函数，以添加自定义代码。此示例显示了如何根据 activity 属性的值绘制彩色圆圈。

```
const columnConfiguration = [
    //...
    {
        property: 'activity',
        title: 'Activity',
        formatter: (cell, formatterParams, onRendered) => {
            let colored = '';
            for (let i = 0; i < cell.getValue(); i++) {
                const hue = 150 - (37.5 * i);
                colored += '<span style="color: hsl(' + hue + ',100%,40%)">&#10687;</span>';
            }
            return '<strong>' + colored + '</strong>';
        }
    }
    //...
] 
```

您可以在[制表器文档](http://tabulator.info/docs/4.0/format)中阅读更多关于格式化器规范的信息。

### 自定义列排序器

如果您需要一个列排序器，例如包含格式化日期的列，您可以添加一个排序功能。

这个函数有两个参数，代表要比较的单元格值。如果`a`小于`b`，结果应为负值；如果两个值相等，结果应为`0`，如果`a`大于`b`，结果应为正值。

```
const columnConfiguration = [
    //...
    {
        property: 'date',
        title: 'Date',
        sorter: (a, b) => {
            // this example requires momentjs
            return moment(a, 'DD.MM.YYYY').valueOf() - moment(b, 'DD.MM.YYYY').valueOf();
        }
    }
    //...
] 
```

## 数据服务

datatable 组件可以在两种不同的表示模式下使用:您可以拥有无限滚动的表或分页的表。这两种模式都可以用于内存数据或来自远程资源的延迟加载数据。

如果您在客户端有大量数据，您还可以实现一个延迟加载数据服务。这个数据服务按需提供较小的数据块，以提高表的性能。

数据服务接口分为以下几个部分:

*   **类型**(滚动/分页)
*   **选项**(可选)
*   **供应商**
    *   **类型**(默认/懒惰)
    *   **getData** (异步函数返回 LazyDataResponse 类型的数据或对象的数组)

### 滚动

第一个场景是无限滚动数据服务，所有数据都在客户端可用。你只需要在一个异步函数中返回数组。

```
const scrollDataService = {
    type: 'scrolled',
    provider: {
        type: 'default',
        getData: async () => tableData
    }
}; 
```

### 滚动+懒加载

如果你需要有大量的数据，你应该惰性地加载它。此外，如果您在客户端拥有所有可用的数据，那么使用惰性数据提供者来提高表初始化性能可能是个好主意。

options 对象中的属性`requestLimit`定义了每个块将请求多少项。

异步函数`getData`的返回值必须包含总行数(`totalRows`)和数据数组。

```
const scrollLazyDataService = {
    type: 'scrolled',
    options: {
        requestLimit: 10
    },
    provider: {
        type: 'lazy',
        getData: async (page, size, sorters) => {
            // request data from server and return:
            return {
                totalRows: 0, // total number of available rows
                data: [ ] // data array
            };
        }
    }
}; 
```

### 分页

如果您想使用分页表，您可以配置此服务类型，并在选项对象中设置页面大小。默认数据提供程序有一个异步函数`getData`返回普通数据数组。

```
const paginatedDataService = {
    type: 'paginated',
    options: {
        pageSize: 5
    },
    provider: {
        type: 'default',
        getData: async () => tableData
    }
}; 
```

### 分页+惰性加载

对分页表使用惰性数据提供程序不需要任何额外的配置。每个请求的块大小由`pageSize`定义。

异步函数`getData`的返回值必须包含总行数(`totalRows`)和数据数组。

```
const paginatedLazyDataService = {
    type: 'paginated',
    options: {
        pageSize: 5
    },
    provider: {
        type: 'lazy',
        getData: async (page, size, sorters) => {
            // request data from server and return:
            return {
                totalRows: 0, // total number of available rows
                data: [ ] // data array
            };
        }
    }
}; 
```

## 处理选择事件

选择项目将发出一个事件，您可以使用事件侦听器来处理该事件。在使用 angular、react 或 vue 等框架时，您可以在由查询选择器选择的 HTML 对象上添加此事件侦听器，或者通过绑定事件侦听器来添加。

```
const datatable = document.querySelector('#datatable');
datatable.addEventListener('itemSelected', e => {
    // e.detail contains selected object
}); 
```

## 注释

对于使用异步函数或返回承诺的函数，如果你需要增强浏览器支持，你应该使用 babel 将你的代码转换到 ES5。

要查看运行中的数据表，请看下面的代码笔:

[https://codepen.io/orgenic-team/embed/EzxqQz?height=600&default-tab=result&embed-version=2](https://codepen.io/orgenic-team/embed/EzxqQz?height=600&default-tab=result&embed-version=2)

请访问我们的网站:

*   [ORGENIC.org](https://orgenic.org/?utm_source=devto&utm_medium=article&utm_campaign=datatable)
*   [文档](https://docs.orgenic.org/components/og-datatable)
*   [Github](https://github.com/orgenic/orgenic-ui)
*   [推特](https://twitter.com/orgenicUI)
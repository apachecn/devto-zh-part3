# 如何在您的 Web 应用程序中使用 Vue 网格 UI 组件(第 2 部分)

> 原文：<https://dev.to/progresstelerik/how-to-use-a-vue-grid-ui-component-in-your-web-app-part-2-3e8k>

在本指南的第二部分中，学习如何在 Vue 的剑道 UI 中编辑您用新的原生网格创建的网格。

在[的上一篇文章](https://dev.to/progresstelerik/how-to-use-a-vue-grid-ui-component-in-your-web-app-part-1-3fcc-temp-slug-500595)中，我们看到了如何使用本地和远程数据，用[剑道 UI](https://www.telerik.com/kendo-vue-ui) 初始化一个 [Vue 原生网格](https://www.telerik.com/kendo-vue-ui/components/grid-native/)。在这篇文章中，我们将学习如何实现 Vue 原生网格的一些编辑特性。使用网格包装器，您可以通过配置`editable`和`toolbar`属性来启用编辑，组件会处理所有事情。但是在这种情况下，我们仅限于默认行为。使用原生网格，我们必须自己实现功能以支持编辑。这让我们对组件的行为有了更多的控制，并允许我们处理页面上所有可能的交互。接下来，我们将看到如何添加内联编辑记录和创建新记录的功能。

## 入门

首先，我们将使用 [Vue webpack-simple 模板](https://github.com/vuejs-templates/webpack-simple)初始化我们的项目。在项目目录中，我们将使用以下命令安装一个主题、网格、全球化包和 vue-class-component:

```
npm install --save @progress/kendo-theme-default
npm install --save @progress/kendo-vue-grid
npm install --save @progress/kendo-vue-intl 
npm install --save vue-class-component 
```

接下来，我们将主题、`Grid`和`GridToolbar`导入到我们的`main.js`文件中。我们需要添加新记录功能的`GridToolbar`组件。最后，我们在全球注册了`Grid`和`GridToolbar`。以下是更新后的`main.js`文件。

```
import Vue from 'vue'
import App from './App.vue'
import '@progress/kendo-theme-default/dist/all.css'
import { Grid, GridToolbar } from '@progress/kendo-vue-grid'

Vue.component('Grid', Grid)
Vue.component('grid-toolbar', GridToolbar)

new Vue({
  el: '#app',
  render: h => h(App)
}) 
```

在`App.vue`文件中，我们首先将网格组件添加到模板中。在组件中，我们将设置`data-items`、`columns`和`edit-field`属性，并添加`rowclick`和`itemchange`事件。这是模板:

```
<template>
  <div id="app">
    <Grid :style="{height: '240px'}"
          :data-items="getData"
          :columns="columns"
          :edit-field="'inEdit'"
          @rowclick="rowClick"
          @itemchange="itemChange">
    </Grid>
  </div>
</template> 
```

我们需要`data-items`和`columns`属性来构建网格。`edit-field`用于确定当前记录是否处于编辑模式。它是我们的数据项中的一个字段的名称，值可以是 true 或 false。我们选择了名称`inEdit`，但是我们的数据项没有名为`inEdit`的字段，所以我们必须给它一个。我们将创建一个名为`getData`的计算方法，将属性`inEdit`分配给所有的数据项。根据`editID`是否等于`personId`，值`inEdit`为真。`editID`是处于编辑模式的项目的 id。这是目前为止的脚本:

```
<script>
import Vue from 'vue';
var people = [
  {
    "personId": 0,
    "name": "Ruxanda",
    "surname": "Corbea",
    "gender": "female",
    "region": "Romania"
  },
  {
    "personId": 1,
    "name": "Paula",
    "surname": "Acevedo",
    "gender": "female",
    "region": "Mexico"
  },
  {
    "personId": 2,
    "name": "David",
    "surname": "Dediu",
    "gender": "male",
    "region": "Romania"
  },
  {
    "personId": 3,
    "name": "Urmila",
    "surname": "Belbase",
    "gender": "female",
    "region": "Nepal"
  }
];

export default {
  name: 'app',
  data () {
    return {
      gridData: people,
      editID: null,
      columns: [
        { field: 'personId', title: 'ID'},
        { field: 'name'},
        { field: 'surname'},
        { field: 'gender'},
        { field: 'region' }
      ],
    };
  },
  computed: {
    getData () { 
      return this.gridData.map((item) => Object.assign({inEdit: item.personId === this.editID}, item))
    }
  }
}
</script> 
```

## T2】

编辑记录

为了启用编辑，我们需要处理`rowclick`和`itemchange`事件。当用户点击一行时，触发`rowclick`事件。当该事件被触发时，我们希望将记录置于编辑模式。在我们的处理程序中，我们将设置`editID`等于`personId`，这样`inEdit`就变成真。当用户改变一个项目的值时，触发`itemchange`事件。我们将使用此事件来保存我们的更改。在处理程序中，首先，我们复制数据项。然后我们找到正在编辑的项目的索引。我们用新值替换该索引处的项目。最后，我们将本地数据源设置为新数据。这是`rowclick`和`itemchange`处理程序:

```
export default {
  ...
  methods: {
    rowClick (e) {
      this.editID = e.dataItem.personId;
    },
    itemChange (e) {
      const data = this.gridData.slice();
      const index = data.findIndex(d => d.personId === e.dataItem.personId);
      data[index] = { ...data[index], [e.field]: e.value };
      this.gridData = data;
    }
  }
} 
```

## T2】

添加记录

接下来，我们将讨论如何添加新记录。我们将在工具栏上添加一个按钮。单击时，在编辑模式下，一个新行将被添加到网格的开头。单击工具栏时，记录将退出编辑模式。首先，我们将把`grid-toolbar`模板添加到我们现有的模板中。这是更新后的`Grid`组件:

```
<Grid :style="{height: '240px'}"
      :data-items="getData"
      :columns="columns"
      :edit-field="'inEdit'"
      @rowclick="rowClick"
      @itemchange="itemChange">
  <grid-toolbar>
    <div @click="closeEdit">
      <button title="Add new" class="k-button k-primary" @click='addRecord' >
        Add new
      </button>
    </div>
  </grid-toolbar>
</Grid> 
```

接下来，我们将实现`addRecord`和`closeEdit`点击事件，并将它们添加到我们的方法列表中。对于`addRecord`方法，我们将首先创建一个新记录，并用一个`personId`字段初始化它。然后我们将复制当前数据。接下来，我们将新记录添加到复制数据的开头。然后，我们将本地数据源设置为更新后的数据。最后，我们通过设置`editID`等于`personId`将记录置于编辑模式。对于`closeEdit`方法，我们让`editID`等于`null`，这样记录就不再处于编辑模式。这是添加到我们脚本中的两个方法。

```
export default {
  ...
  methods: {
    ...
    addRecord () {
      const newRecord = {personId: this.gridData.length}
      const data = this.gridData.slice();
      data.unshift(newRecord);
      this.gridData = data;
      this.editID = newRecord.personId;
    },
    closeEdit (e) {
      if (e.target === e.currentTarget) {
        this.editID = null;
      }
    }
  }
} 
```

[![Grid](img/39e5eea3425515f300701f4580fed6c7.png "55Grid03")](https://res.cloudinary.com/practicaldev/image/fetch/s--qRecjbmL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d585tldpucybw.cloudfront.net/sfimages/default-source/default-album/55grid03.png%3Fsfvrsn%3D9f0b85b0_1)

看这里最后的项目回购:[https://github.com/albertaw/kendoui-vue-native-grid-editing](https://github.com/albertaw/kendoui-vue-native-grid-editing)

## 总结

我们看到了如何在网格中编辑数据项和创建新数据项。还有许多其他的方法可以定制我们的网格。可以内嵌或在单元格中编辑项目。除了添加和编辑记录，我们还可以添加删除记录的功能。我们可以添加按钮来保存和取消更改，而不是单击工具栏来关闭编辑。我们还可以创建一个编辑按钮来激活编辑模式。您也可以使用模板来创建自定义编辑字段。这些只是您可以添加的一些功能。

## 资源

*   [项目回购](https://github.com/albertaw/kendoui-vue-native-grid-editing)
*   [Vue 网格原生概述](https://www.telerik.com/kendo-vue-ui/components/grid-native/)
*   [Vue 网格本地编辑基础知识](https://www.telerik.com/kendo-vue-ui/components/grid-native/editing/editing/)
*   [Vue 网格原生 API](https://www.telerik.com/kendo-vue-ui/components/grid-native/api/)

## 亲自尝试剑道 UI

想开始利用 70 多个现成的剑道 UI 组件，比如网格或调度器吗？您可以立即开始免费试用剑道 UI，并开始更快地开发您的应用程序。

[开始我的剑道 UI 试用](https://www.telerik.com/download-login-v2-kendoui?ReturnUrl=https://www.telerik.com/download-login-v2-kendoui/thank-you-for-downloading)

## Angular、React 和 jQuery 版本

寻找支持特定框架的 UI 组件？查看角度的[剑道 UI，反应](https://www.telerik.com/kendo-angular-ui/components/)的[剑道 UI，或者 jQuery](https://www.telerik.com/kendo-react-ui/components/) 的[剑道 UI。](https://www.telerik.com/kendo-jquery-ui/)

## 更多资源

*   [剑道 UI Vue 文档和演示](https://www.telerik.com/kendo-vue-ui/components/)
*   [vista . js web pack-简单模板](https://github.com/vuejs-templates/webpack-simple)
*   [剑道 UI 在线编辑](https://dojo.telerik.com/)
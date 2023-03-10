# 如何在 Web 应用程序中使用 Vue 网格 UI 组件(第 1 部分)

> 原文：<https://dev.to/progresstelerik/how-to-use-a-vue-grid-ui-component-in-your-web-app-part-1-2okd>

让我们学习如何在 Vue 的剑道 UI 中使用新的原生网格创建网格。新的原生网格是用 Vue 从头开始构建的。

在这个两部分系列的第一篇文章中，我们将学习如何为 Vue 创建一个剑道 UI 原生网格。新的本地电网是用 Vue 从头开始建造的。[原生网格](https://www.telerik.com/kendo-vue-ui/components/grid-native/)与网格包装器的一个不同之处在于，原生网格不依赖于 jQuery。另一个区别是不再需要`DataSource`组件来加载数据。数据可以直接在组件内部定义。接下来，我们将看到如何使用本地和远程数据创建本地网格。然后，我们将在网格中添加一个分页器，将数据分成页面。

## 创建网格

首先，我们将使用 [Vue webpack-simple 模板](https://github.com/vuejs-templates/webpack-simple)初始化我们的项目。在项目目录中，我们将使用以下命令安装一个主题、网格、全球化包和 vue-class-component:

```
npm install --save @progress/kendo-theme-default
npm install --save @progress/kendo-vue-grid
npm install --save @progress/kendo-vue-intl 
npm install --save vue-class-component 
```

接下来，我们将主题和网格导入到我们的`main.js`文件中，并全局注册网格组件。

```
import Vue from 'vue'
import App from './App.vue'
import '@progress/kendo-theme-default/dist/all.css'
import { Grid } from '@progress/kendo-vue-grid'

Vue.component('Grid', Grid);

new Vue({
  el: '#app',
  render: h => h(App)
}) 
```

在第一个例子中，我们将在本地定义数据。数据由 uinames.com 提供。我们将把网格组件添加到模板的`App.vue`文件中，并设置`data-items`和`columns`属性。`data-items`属性设置网格的数据。属性`columns`设置网格的字段。这是更新后的`App.vue`文件:

```
<template>
  <div id="app">
    <Grid :style="{height: '200px'}"
          :data-items="dataItems"
          :columns="columns">
    </Grid>
  </div>
</template>

<script>

export default {
  name: 'app',
  data () {
    return {
      dataItems: [
      {
        "name": "Ruxanda",
        "surname": "Corbea",
        "gender": "female",
        "region": "Romania"
      },
      {
        "name": "Paula",
        "surname": "Acevedo",
        "gender": "female",
        "region": "Mexico"
      },
      {
        "name": "David",
        "surname": "Dediu",
        "gender": "male",
        "region": "Romania"
      },
      {
        "name": "Urmila",
        "surname": "Belbase",
        "gender": "female",
        "region": "Nepal"
      }],
      columns: [
        { field: 'name'},
        { field: 'surname'},
        { field: 'gender'},
        { field: 'region' }
      ],
    };
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  color: #2c3e50;
  margin-top: 60px;
}
</style> 
```

使用相同的数据，我们将从远程数据源构建一个网格。我们将从一个空的`dataItems`数组开始。这一次我们将使用 axios 从一个 API 获取数据。然后我们将用结果填充`dataItems`数组。API 调用将发生在`mounted`生命周期事件中。首先，我们将使用以下命令安装 axios:

```
npm install --save axios 
```

然后，我们在我们的`App.vue`文件的脚本中导入 axios，并从`mounted`生命周期事件中加载数据。这是更新后的剧本:

```
<script>
import axios from 'axios'

export default {
  name: 'app',
  data () {
    return {
      dataItems: [],
      columns: [
        { field: 'name'},
        { field: 'surname'},
        { field: 'gender'},
        { field: 'region' }
      ],
    }
  },
  mounted () {
    axios
      .get('https://uinames.com/api/?amount=25')
      .then(response => {
        this.dataItems = response.data
      })
  }
}
</script> 
```

## 添加分页

默认情况下，网格中的所有记录都显示在一页上。为了实现分页，我们需要配置几个属性。我们必须设置网格的`pageable`、`skip`、`take`和`total`属性以及`pagechange`事件。属性为网格配置分页器。`skip`属性是分页器将跳过的记录数。属性是每页显示的记录数。`total`是数据项的总数。而`pagechange`是页面改变时触发的事件。这是更新后的模板:

```
<template>
  <div id="app">
    <Grid :style="{height: '200px'}"
          :data-items="result"
          :columns="columns"
          :pageable="true"
          :skip="skip"
          :take="take"
          :total="total"
          @pagechange="pageChange">
    </Grid>
  </div>
</template> 
```

`data-items`属性应该设置为当前页面的数据。为了实现这一点，我们创建了一个名为`result`的计算方法，它计算从数据项列表中获取哪些记录。`skip`属性被初始化为 0，`take`被初始化为 10。我们为`total`创建一个计算方法，返回数据项的数量。最后，我们添加一个`pageChange`方法来更新`skip`和`take`属性。这是更新后的脚本:

```
<script>
import axios from 'axios'

export default {
  name: 'app',
  data () {
    return {
      dataItems: [],
      columns: [
        { field: 'name'},
        { field: 'surname'},
        { field: 'gender'},
        { field: 'region' }
      ],
      skip: 0,
      take: 10,
    }
  },
  computed: {
    result () {
        return this.dataItems.slice(this.skip, this.take + this.skip)
    },
    total () {
      return this.dataItems.length
    }
  },
  mounted () {
    axios
      .get('https://uinames.com/api/?amount=25')
      .then(response => {
        this.dataItems = response.data
      })
  },
  methods: {
    pageChange(event) {
        this.skip = event.page.skip;
        this.take = event.page.take;
    }
  }
}
</script> 
```

看这里最后的项目回购:[https://github.com/albertaw/kendoui-vue-native-grid](https://github.com/albertaw/kendoui-vue-native-grid)

## 总结

首先，我们看到了如何使用本地数据创建网格。这需要将`data-items`属性设置为数据中定义的列表，并设置`columns`属性。然后，我们通过使用 axios 在 Vue 的`mounted`生命周期事件中进行 API 调用，从远程数据源创建了网格。最后，我们在网格中添加了分页。我们必须设置`pageable`、`skip`、`take`和`total`属性，并处理`pagechange`事件。

在下一篇文章中，我们将看到如何编辑网格中的记录。

## 资源

*   [项目回购](https://github.com/albertaw/kendoui-vue-native-grid)
*   [Vue 网格原生概述](https://www.telerik.com/kendo-vue-ui/components/grid-native/)
*   [Vue 网格本地分页](https://www.telerik.com/kendo-vue-ui/components/grid-native/paging/)
*   [Vue 网格原生 API](https://www.telerik.com/kendo-vue-ui/components/grid-native/api/)

## 亲自尝试剑道 UI

想开始利用 70 多个现成的剑道 UI 组件，比如网格或调度器吗？您可以立即开始免费试用剑道 UI，并开始更快地开发您的应用程序。

[开始我的剑道 UI 试用](https://www.telerik.com/download-login-v2-kendoui?ReturnUrl=https://www.telerik.com/download-login-v2-kendoui/thank-you-for-downloading)

## Angular、React 和 jQuery 版本

寻找支持特定框架的 UI 组件？查看[剑道 UI 的角度](https://www.telerik.com/kendo-angular-ui/components/)、[剑道行为](https://www.telerik.com/kendo-react-ui/components/)，或[剑道 UI 的 jQuery](https://www.telerik.com/kendo-jquery-ui/) 。

## 资源

*   [剑道 UI Vue 文档和演示](https://www.telerik.com/kendo-vue-ui/components/)
*   [vista . js web pack-简单模板](https://github.com/vuejs-templates/webpack-simple)
*   [剑道 UI 在线编辑](https://dojo.telerik.com/)
# 使用 Cube.js 的 Vue 仪表板教程

> 原文：<https://dev.to/cubejs/vue-dashboard-tutorial-using-cube-js-6h5>

现在有几个流行的前端框架。我们最近讨论了如何使用[反应](https://cube.dev/blog/cubejs-open-source-dashboard-framework-ultimate-guide/)和[角度](https://cube.dev/blog/angular-dashboard-tutorial-with-cubejs/)构建分析仪表板。这两个指南的共同点是[cube . js](https://cube.dev)——一个开源分析框架，它为这些仪表板提供高性能数据。由于是开源的，Cube.js 对贡献开放，你可以在 Github 的资源库中找到一个[贡献指南](https://github.com/statsbotco/cube.js/blob/master/CONTRIBUTING.md)。
社区的最新贡献之一是 Vue 的 Cube.js 客户端。它是由 Qualibrate 的 Ricardo Tapia 创建的，他有一篇关于这个过程的很棒的[博客文章](https://cube.dev/blog/how-we-build-analytics-at-qualibrate-using-cubejs-and-vuejs/)。在本教程中，我将涉及一些细节，并提供一个分步指南，介绍如何使用 [Vue](https://vuejs.org/) 构建您自己的分析仪表板。

我们将使用 [Vue](https://vuejs.org/) 、 [Cube.js](https://cube.dev) 和 [Laue](https://laue.js.org/) 。你可以在这里找到最终的仪表板[和代码沙箱，下面是源代码。](https://statsbotco.github.io/cubejs/vue-dashboard/)

[https://codesandbox.io/embed/cubejs-vue-demo-9r5xl](https://codesandbox.io/embed/cubejs-vue-demo-9r5xl)

## 设置 Cube.js 后端

我们在其他教程中讨论过这个主题，所以如果您已经设置并运行了 Cube.js 后端，可以跳过这一节。

您可以通过 NPM 或 Yarn 安装用于各种 Cube.js 工作流的 Cube.js CLI。

```
npm install -g cubejs-cli 
```

Enter fullscreen mode Exit fullscreen mode

让我们准备一个 Cube.js 后端来为我们正在构建的仪表板提供数据。Cube.js 支持许多数据库和部署选项。你可以在[文档](https://cube.dev/docs/deployment)中了解更多。对于本教程，我们将使用 Postgres 数据库并将 Cube.js 部署到 Heroku。让我们使用刚刚安装的 CLI 创建一个新的 Cube.js 应用程序。

```
cubejs new vue-demo -d postgres
cd vue-demo 
```

Enter fullscreen mode Exit fullscreen mode

如果您还没有用于仪表板的数据库，您可以下载我们的 Postgres 演示电子商务数据集。

```
curl http://cube.dev/downloads/ecom-dump.sql > ecom-dump.sql
createdb ecom
psql --dbname ecom -f ecom-dump.sql 
```

Enter fullscreen mode Exit fullscreen mode

下一步是定义数据模型。在生产应用程序中，您很可能会有多个模式文件，但是对于我们的演示应用程序，我们将只有一个多维数据集。如果你不熟悉 Cube.js 数据模式，这里有一个深入的教程。

```
cube(`Users`, {
  sql: `SELECT * FROM users`,

  measures: {
    count: {
      sql: `id`,
      type: `count`
    }
  },

  dimensions: {
    city: {
      sql: `city`,
      type: `string`
    },

    signedUp: {
      sql: `created_at`,
      type: `time`
    },

    companyName: {
      sql: `company_name`,
      type: `string`
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

Cube.js 使用数据模式在连接的数据库中生成和执行 SQL。我们可以通过向 Cube.js REST API 端点发送一个示例请求来测试它。

```
curl \
 -H "Authorization: EXAMPLE-API-TOKEN" \
 -G \
 --data-urlencode 'query={"measures":["Users.count"]}' \
 http://localhost:4000/cubejs-api/v1/load 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里了解更多关于 Cube.js 查询格式[的信息。](https://cube.dev/docs/query-format)

最后，让我们将后端部署到 Heroku:

```
git init
git add -A
git commit -am "Initial commit"
heroku create cubejs-vue-demo
git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

您可以在[文档](https://cube.dev/docs/deployment#heroku)中找到完整的部署指南。

## 创建 Vue App

当后端启动并运行时，就该构建仪表板了。由于我们使用的是 Vue，创建新应用的最佳方式是使用`vue-cli`。

首先，安装 vue-cli，如果你还没有的话:

```
npm install -g @vue/cli
# or using yarn
yarn global add @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

要创建一个应用程序，你可以使用你的终端或者启动一个叫做 [Vue UI](https://cli.vuejs.org/guide/creating-a-project.html#using-the-gui) :
的工具

```
vue ui 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的电脑上运行一个网站，允许您创建、运行和监控应用程序。它还包含文档和其他社区资源的所有链接。

要使用终端创建应用程序，您只需要一个名称:

```
vue create YOUR-APP-NAME
cd YOUR-APP-NAME 
```

Enter fullscreen mode Exit fullscreen mode

您可以为您的应用程序配置插件，但对于演示，我们将使用默认设置。

如果您使用 Vue UI 创建了该应用程序，您可以直接在那里启动它。如果您正在使用控制台，运行`serve`任务:

```
npm run serve
# or using yarn
yarn serve 
```

Enter fullscreen mode Exit fullscreen mode

现在，您的应用程序正在您的计算机上运行，并且可以通过浏览器访问。

## 设置仪表板

首先，我们将使用[引导程序](https://getbootstrap.com/)添加一些基本样式。我们将从 CDN 安装它，但是您可以使用 npm 或 yarn 添加它。打开您的`public/index.html`文件并添加引导资源:

```
<head>
    ...
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T"
     crossorigin="anonymous">
</head> 
```

Enter fullscreen mode Exit fullscreen mode

现在，当 bootstrap 安装完成后，我们可以为应用程序创建一些基本结构:

```
<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-sm-4">
      </div>
      <div class="col-sm-4">
      </div>
      <div class="col-sm-4">
      </div>
    </div>
    <div class="row">
      <div class="col-sm-6">
      </div>
      <div class="col-sm-6">
      </div>
    </div>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要一些数据来展示。Cube.js Vue 客户端有一个完整的[文档](https://cube.dev/docs/@cubejs-client-vue),您可以在其中找到其他选项。首先，我们需要用我们的后端 URL 和 API 令牌设置一个 Cube.js 实例:

```
// App.vue
import cubejs from "@cubejs-client/core";
import { QueryBuilder } from "@cubejs-client/vue";

const cubejsApi = cubejs(
  "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e30.K9PiJkjegbhnw4Ca5pPlkTmZihoOm42w8bja9Qs2qJg",
  { apiUrl: "https://react-query-builder.herokuapp.com/cubejs-api/v1" }
); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们设置我们的应用程序来使用一个 [QueryBuilder 组件](https://cube.dev/docs/@cubejs-client-vue#query-builder)并创建一个对所有用户进行计数的查询:

```
// App.vue

export default {
  name: "App",
  components: {
    Chart,
    QueryBuilder
  },
  data() {
    return {
      cubejsApi,
      usersQuery: { measures: ["Users.count"] }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

Cube.js Vue 客户端允许子组件接收给定查询的 [resultSet](https://cube.dev/docs/@cubejs-client-core#result-set) 对象。让我们创建一个名为`Chart` :
的新组件

```
# components/Chart.vue
<template>
  <div class="card">
    <div class="card-body">
      <h5 class="card-title">{{ title }}</h5>
      <div class="card-text">
        <div class="d-flex justify-content-center text-dark">
          <div class="spinner-border" role="status" v-if="loading">
            <span class="sr-only">Loading...</span>
          </div>
        </div>
        <h1
          v-if="!loading && type === 'number'"
          height="300"
        >{{ values[0][metrics[0]] }}</h1>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: "Chart",
  props: {
    resultSet: Object,
    loading: Boolean,
    title: String,
  },
  computed: {
    values: function() {
      if (this.loading) return [];
      return this.resultSet.chartPivot();
    },
    metrics: function() {
      if (this.loading) return [""];
      return this.resultSet.seriesNames().map(x => x.key);
    }
  };
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这里我们需要的是在数据加载时显示一个加载元素，然后显示一个数字。让我们回到我们的`App.vue`组件，创建第一个图块:

```
<query-builder :cubejs-api="cubejsApi" :query="usersQuery">
  <template v-slot="{ loading, resultSet }">
    <Chart title="Total Users" :loading="loading" :result-set="resultSet"/>
  </template>
</query-builder> 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里使用了一个 [QueryBuilder 组件](https://cube.dev/docs/@cubejs-client-vue#query-builder)，它使用 [Vue 作用域插槽道具](https://vuejs.org/v2/guide/components-slots.html#Named-Slots)将数据传递给图表组件。现在我们的仪表板上有一个计数器显示用户总数。我们来补充一些图表吧！

图表

为了创建一个图表，我们将使用 [Laue](https://laue.js.org/) 库。这很简单，网上有很多例子。首先，我们将在我们的应用程序中安装 Laue:

```
// main.js

import { Laue } from 'laue';

Vue.use(Laue); 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们在任何组件中使用所有 Laue 项。在[文档](https://laue.js.org/guide)中还有其他安装方法。现在让我们创建一个折线图组件:

```
<template>
  <la-cartesian autoresize :data="values" :padding="[0, 0, 5, 0]">
    <la-line curve :width="2" color="#7DB3FF" :prop="metrics[0]"/>
    <la-y-axis :nbTicks="4"></la-y-axis>
    <la-x-axis prop="x" :format="dateFormatter" :interval="6"></la-x-axis>
    <la-tooltip></la-tooltip>
  </la-cartesian>
</template>

<script>
import moment from "moment";
export default {
  name: "LineChart",
  props: {
    values: Array,
    metrics: Array
  },
  methods: {
    dateFormatter: function(value) {
      return moment(value).format("MMM YY");
    }
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

为了呈现图表，我们将在图表组件上使用类型属性。让我们在那里添加一个条件渲染:

```
<line-chart v-if="!loading && type === 'line'" :values="values" :metrics="metrics"/> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的折线图组件完成了！我们现在需要的是在 App.vue 组件中添加一个对折线图的查询:

```
<query-builder :cubejs-api="cubejsApi" :query="lineQuery">
  <template v-slot="{ loading, resultSet }">
    <Chart
      title="New Users Over Time"
      type="line"
      :loading="loading"
      :result-set="resultSet"
    />
  </template>
</query-builder>

<script>
...
data() {
  return {
    cubejsApi,
    usersQuery: { measures: ["Users.count"] },
    lineQuery: {
      measures: ["Users.count"],
      timeDimensions: [
        {
          dimension: "Users.createdAt",
          dateRange: ["2017-01-01", "2018-12-31"],
          granularity: "month"
        }
      ]
    }
  }
...
</script> 
```

Enter fullscreen mode Exit fullscreen mode

折线图到此为止。条形图的设置非常相似。

仪表板就完成了！你可以在这里找到 dashboard live [和这个](https://statsbotco.github.io/cubejs/vue-dashboard/) [CodeSandbox](https://codesandbox.io/s/cubejs-vue-demo-9r5xl) 中的源代码。我们希望本指南对您有用。
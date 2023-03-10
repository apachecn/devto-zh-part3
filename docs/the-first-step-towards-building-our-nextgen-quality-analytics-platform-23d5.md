# 构建下一代质量分析平台的第一步

> 原文：<https://dev.to/cubejs/the-first-step-towards-building-our-nextgen-quality-analytics-platform-23d5>

在 qualibrate，我们一直在尝试管理最佳方式，为用户提供高级报告的灵活性和生成出色仪表板的简单性，虽然 Kibana、Grafana 等是很好的产品，但我们希望在用户使用我们的平台时为他们提供无缝体验。在讨论了可供选择的各种方案与构建我们自己的方案之后，我们找到了来自 Statsbot.co[的](https://statsbot.co) [Cube.js](https://cube.dev) 。我们认为他们的产品非常适合我们的用例，但有一个很大的警告——我们当前的平台使用 Vue.js，但没有实现。这时候火花点燃了；为什么我们要通过复制一个不可思议的产品来重新发明轮子，相反，我们可以利用一个伟大的社区给我们的东西，并努力一起工作。当我们发现有机会对 MongoDB 连接器进行改进时，我们开始在工作流中使用 Cube.js。那一刻，我们与社区的关系开始了。一旦我们能够映射我们的数据库模式并管理我们的关系，我们的工作就开始了。我们试图在 Vue.js 中尽可能匹配 React 组件。

这就是结果。

## 设置一个演示后端

如果您已经启动并运行了 Cube.js 后端，您可以跳过这一步。

因为我们在产品中使用 MongoDB，所以我们也将在教程中使用它。请按照本指南设置 Cube.js 后端。

## 使用 Vue.js 库

让我们使用@vue/cli 创建一个新的 Vue CLI 项目，并导航到其中。

```
$ vue create cubejs-mongo-example && cd cubejs-mongo-example 
```

Enter fullscreen mode Exit fullscreen mode

接下来，安装`@cubejs-client/vue`依赖项。

```
$ npm install --save @cubejs-client/core @cubejs-client/vue 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们将为这个示例
添加`vue-multiselect`、`vue-chartkick`和`Chart.js`

```
$ npm install --save vue-multiselect vue-chartkick chart.js 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以启动开发服务器

```
$ npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。您的应用程序将被启动，并可在 [http://localhost:8080](http://localhost:8080) 上获得，应该如下所示:
[![](img/7f1d58efa02e4655f5b670acdb1bb24e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sZCxi6ES--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o3egr2d80hsaxkcz22ea.png)

## 环境变量

Vue 应用程序配置变量可以通过. env 文件加载，如果使用`VUE_APP_`约定，
将可用([更多信息请点击](https://cli.vuejs.org/guide/mode-and-env.html#environment-variables) )

```
VUE_APP_CUBEJS_API_TOKEN=<YOUR-API-TOKEN>
VUE_APP_CUBEJS_API_URL=<YOUR-API-URL> 
```

Enter fullscreen mode Exit fullscreen mode

无论您的应用程序何时运行，都可以通过`process.env`对象访问该配置。

## 配置依赖关系

`src/main.js`是任何生成的`@vue/cli`项目的默认入口点；这是安装 Vue 应用程序的地方。在这种情况下，我们将放置全局组件，以便它们在任何地方都可用。

```
import Vue from 'vue';
import VueChartkick from 'vue-chartkick';
import Chart from 'chart.js';
import App from './App.vue';

Vue.config.productionTip = false;
// Add ChartKick components with Chart.js Adapter
Vue.use(VueChartkick, { adapter: Chart });

new Vue({
  render: h => h(App),
}).$mount('#app'); 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 QueryRenderer

`<query-renderer/>`组件接受一个 Cube.js 对象和一个公式查询，以便获取数据并返回一个`resultSet`。它异步处理请求，并在问题解决后呈现插槽。

```
<template>
  <div class="hello">
    // Query Renderer component
    <query-renderer :cubejs-api="cubejsApi" :query="query">
      <template v-slot="{ loading, resultSet }">
          <chart-renderer
            v-if="!loading"
            :result-set="resultSet" />
      </template>
    </query-renderer>
  </div> </template> 
<script>
import cubejs from '@cubejs-client/core';
import { QueryRenderer } from '@cubejs-client/vue';
import ChartRenderer from './ChartRenderer.vue';

const cubejsApi = cubejs(
  process.env.VUE_APP_CUBEJS_API_TOKEN,
  { apiUrl: process.env.VUE_APP_CUBEJS_API_URL },
);

export default {
  name: 'HelloWorld',
  components: {
    QueryRenderer,
    ChartRenderer,
  },
  data() {
    const query = {
      measures: ['Orders.count'],
      timeDimensions: [
        {
          dimension: 'LineItems.createdAt',
          granularity: 'month',
        },
      ],
    };

    return {
      cubejsApi,
      selected: undefined,
      query,
    };
  },
  methods: {
    customLabel(a) {
      return a.title;
    },
  },
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个简单的`<chart-renderer/>`组件示例，用于显示来自`resultSet`的信息，将结果映射到与`vue-chartkick` `line-chart`数据系列兼容的对象:

```
<template>
  <div class="chart-renderer">
    <line-chart :data="series"></line-chart>
  </div> </template> 
<script>
export default {
  name: 'ChartRenderer',
  props: {
    resultSet: {
      type: Object,
      required: true,
    },
  },
  computed: {
    series() {
      const seriesNames = this.resultSet.seriesNames();
      const pivot = this.resultSet.chartPivot();
      const series = [];
      seriesNames.forEach((e) => {
        const data = pivot.map(p => [p.x, p[e.key]]);
        series.push({ name: e.key, data });
      });
      return series;
    },
  },
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，它应该是这样的:
[![](img/f377ca4c713e872e34f29cce002ee68d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VfRYGSMX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xtfxpj2fr9gdbowl3azj.png)

## 使用 QueryBuilder

`<query-builder/>`组件是一个从 API 获取模式信息
的实用程序，它允许您使用一些助手方法生成查询。
其中一些包括`setMeasures`和`addMeasures`，它们改变底层查询并在后台更新`resultSet`。每个可用的方法都是[作用域插槽](https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots)对象的一部分。唯一需要的道具是`cubejsApi`。它需要一个由 Cube.js 方法返回的 Cube.js API 客户端实例。

[在这里你可以找到`QueryBuilder`组件](https://cube.dev/docs/@cubejs-client-vue)的更详细参考

```
<template>
  <div class="hello">
    <query-builder :cubejs-api="cubejsApi">
      <template v-slot="{ measures, setMeasures, availableMeasures, loading, resultSet }">
        // Render whatever content
      </template>
    </query-builder>
  </div> </template> 
<script>
import cubejs from '@cubejs-client/core';
import { QueryBuilder } from '@cubejs-client/vue';

const cubejsApi = cubejs(
  process.env.VUE_APP_CUBEJS_API_TOKEN,
  { apiUrl: process.env.VUE_APP_CUBEJS_API_URL },
);

export default {
  name: 'HelloWorld',
  components: {
    QueryBuilder,
  },
  data() {
    return {
      cubejsApi,
    };
  },
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将添加选择一些维度和度量的功能。
它将更新查询并相应地呈现内容。`setMeasures`和
`setDimensions`方法将集合直接设置为查询，因为这些多选
控件使它变得更容易一些。`availableMeasures`和`availableDimensions`从模式中获取信息
以显示允许的输入。

```
<template>
  <div class="hello">
    <query-builder :cubejs-api="cubejsApi" :query="query">
      <template v-slot="{ measures, setMeasures, availableMeasures, dimensions, setDimensions, availableDimensions, loading, resultSet }">
        <div class="selects">
          <multiselect
            :multiple="true"
            :customLabel="customLabel"
            @input="setMeasures"
            :value="measures"
            :options="availableMeasures"
            placeholder="Please Select"
            label="Title"
            track-by="name"/>
          <multiselect
            :multiple="true"
            :customLabel="customLabel"
            @input="setDimensions"
            :value="dimensions"
            :options="availableDimensions"
            placeholder="Please Select"
            label="Title"
            track-by="name"/>
        </div>
          <chart-renderer
            v-if="!loading && measures.length > 0"
            :result-set="resultSet" />
      </template>
    </query-builder>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

这是一个完整的工作示例，应该是这样的。
[![](img/190dcfde46b9ad94d4e60c41f0156f75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JEUdU_Xy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/phyungp77v22pc7yqsfa.png)T4】

```
<template>
  <div class="hello">
    <query-builder :cubejs-api="cubejsApi" :query="query">
      <template v-slot="{ measures, setMeasures, availableMeasures, dimensions, setDimensions, availableDimensions, loading, resultSet }">
        <div class="selects">
          <multiselect
            :multiple="true"
            :customLabel="customLabel"
            @input="setMeasures"
            :value="measures"
            :options="availableMeasures"
            placeholder="Please Select"
            label="Title"
            track-by="name"/>
          <multiselect
            :multiple="true"
            :customLabel="customLabel"
            @input="setDimensions"
            :value="dimensions"
            :options="availableDimensions"
            placeholder="Please Select"
            label="Title"
            track-by="name"/>
        </div>
          <chart-renderer
            v-if="!loading && measures.length > 0"
            :result-set="resultSet" />
      </template>
    </query-builder>
  </div> </template> 
<script>
import cubejs from '@cubejs-client/core';
import Multiselect from 'vue-multiselect';
import { QueryBuilder } from '@cubejs-client/vue';
import ChartRenderer from './ChartRenderer.vue';

const cubejsApi = cubejs(
  process.env.VUE_APP_CUBEJS_API_TOKEN,
  { apiUrl: process.env.VUE_APP_CUBEJS_API_URL },
);

export default {
  name: 'HelloWorld',
  components: {
    Multiselect,
    QueryBuilder,
    ChartRenderer,
  },
  data() {
    const query = {
      measures: [],
      timeDimensions: [
      ],
    };

    return {
      cubejsApi,
      selected: undefined,
      query,
    };
  },
  methods: {
    customLabel(a) {
      return a.title;
    },
  },
};
</script> <style src="vue-multiselect/dist/vue-multiselect.min.css"></style> 
<style scoped>
.hello {
  padding: 0 10rem;
}

.selects {
  display: flex;
}

.selects .multiselect {
  margin: 0.5rem;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

与 measures、`availableMeasures`和`updateMeasures`类似，有要呈现的属性和要管理的维度、段、时间、过滤器和图表类型。

您可以在文档中找到属性[的完整列表。](https://cube.dev/docs/@cubejs-client-vue#query-builder)
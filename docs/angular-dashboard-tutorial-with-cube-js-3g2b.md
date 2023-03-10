# 使用 Cube.js 的角度仪表板教程

> 原文：<https://dev.to/cubejs/angular-dashboard-tutorial-with-cube-js-3g2b>

几乎任何网站都有一些嵌入式分析。今天，你可以找到每个公共 Github 库或任何社交网络的使用图表。 [Cube.js](https://github.com/statsbotco/cubejs-client) 旨在帮助开发者构建这样的分析应用。它解决了每个生产就绪型分析应用程序需要解决的大量不同问题:分析 SQL 生成、查询结果缓存和执行编排、数据预聚合、安全性和用于查询结果提取的 API。

我们最近报道了[如何用 Cube.js 和 React 构建](https://cube.dev/blog/cubejs-open-source-dashboard-framework-ultimate-guide/)一个分析仪表板，但是 Angular 呢？从版本 0.8.4 开始，Cube.js 客户端附带了一个 Angular 模块，以便于集成。
今天我将向你展示如何使用 [Angular](https://angular.io/) 、 [Cube.js](https://cube.dev) 和 [ng2-charts](https://valor-software.com/ng2-charts/) 构建一个分析仪表板。

你可以在这里找到[一个最终仪表板](https://statsbotco.github.io/cubejs/angular-dashboard/)和一个 CodeSandbox，下面是源代码。

[https://codesandbox.io/embed/cs1kg](https://codesandbox.io/embed/cs1kg)

## 设置 Cube.js 后端

我们在其他教程中讨论过这个主题，所以如果您已经设置并运行了 Cube.js 后端，可以跳过这一节。

您可以通过 NPM 或 Yarn 安装用于各种 Cube.js 工作流的 Cube.js CLI。

```
npm install -g cubejs-cli 
```

Enter fullscreen mode Exit fullscreen mode

让我们准备一个 Cube.js 后端来为我们正在构建的仪表板提供数据。Cube.js 支持许多数据库和部署选项。你可以在文档中了解更多信息[。对于本教程，我们将使用 Postgres 数据库并将 Cube.js 部署到 Heroku。让我们使用刚刚安装的 CLI 创建一个新的 Cube.js 应用程序。](https://cube.dev/docs) 

```
cubejs new ng-demo -d postgres
cd ng-demo 
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
heroku create cubejs-ngx-demo
git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

您可以在[文档](https://cube.dev/docs/deployment#heroku)中找到完整的部署指南。

## 仪表盘

现在，当我们运行一个功能性的后端时，我们可以进入下一部分——构建一个仪表板。Cube.js 有一个角度绑定，它本身不提供任何可视化，但设计用于任何图表库。通过这种方式，它为开发人员构建独特的自定义用户体验提供了极大的灵活性。

首先，如果您还没有安装 ng-cli，请安装它:

```
npm install -g angular/cli 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用 SCSS 模板创建一个新的角度应用:

```
ng new ng-demo-dashboard -s scss 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用 ng2-charts 库来绘制图表，它是 Chart.js 的一个有角度的包装器。Cube.js Angular 客户端将用于从后端加载数据，最后 [Bootstrap](https://getbootstrap.com/) 将为我们提供一些漂亮的样式。让我们添加这些依赖:

```
npm install -s ng2-charts @cubejs-client/core @cubejs-client/ngx moment
# or
yarn add ng2-charts @cubejs-client/core @cubejs-client/ngx moment 
```

Enter fullscreen mode Exit fullscreen mode

接下来，将所需的模块添加到 app.module.ts 文件:

```
const cubejsOptions = {
  token:
    "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.e30.K9PiJkjegbhnw4Ca5pPlkTmZihoOm42w8bja9Qs2qJg",
  options: {
    apiUrl: "https://react-query-builder.herokuapp.com/cubejs-api/v1"
  }
};

@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
    ChartsModule,
    CubejsClientModule.forRoot(cubejsOptions)
  ],
  providers: [],
  bootstrap: [AppComponent]
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经完成了应用程序设置。让我们创建一个图表组件:

```
ng generate component chart 
```

Enter fullscreen mode Exit fullscreen mode

为 ng2 图表添加一些样式和元素:

```
<div class="card">
  <div class="card-body">
    <h5 class="card-title">{{ title }}</h5>
    <div class="card-text">
      <div *ngIf="ready === false" class="d-flex justify-content-center text-dark">
        <div class="spinner-border" role="status">
      <span class="sr-only">Loading...</span>
    </div>
      </div>
      <canvas *ngIf="ready && showChart" baseChart height="300" [datasets]="chartData" [labels]="chartLabels" [options]="chartOptions"
             [colors]="chartColors" [chartType]="chartType"></canvas>
      <h1 *ngIf="ready && !showChart" height="300">{{ chartData }}</h1>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

让我们为图表获取数据。我们需要定义输入，我们将把它传递给 ngx-chart 组件以允许定制:

```
@Input() chartType;
@Input() query;
@Input() title;

public chartData;
public chartLabels;
public chartOptions: any = {
  responsive: true
};
public chartColors; 
```

Enter fullscreen mode Exit fullscreen mode

为了收集数据，我们将为查询添加一个输入，并使用 cube . js[Angular watch API](https://cube.dev/docs/@cubejs-client-ngx#api):

```
constructor(private cubejs: CubejsClient) {}

ngOnInit() {
  this.querySubject = new Subject();
  this.resultChanged = this.resultChanged.bind(this);
  this.cubejs
    .watch(this.querySubject)
    .subscribe(this.resultChanged, err => console.log("HTTP Error", err));

  this.querySubject.next(this.query);
} 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我们在每次查询改变时获取和显示新的数据。现在让我们在应用程序中创建一个简单的仪表板

```
<div class="container-fluid">
  <div class="row">
    <div class="col-sm-4">
      <app-chart chartType="singleValue" [query]="usersQuery" title="Total Users"></app-chart>
    </div>
    <div class="col-sm-4">
      <app-chart chartType="singleValue" [query]="ordersQuery" title="Total Orders"></app-chart>
    </div>
    <div class="col-sm-4">
      <app-chart chartType="singleValue" [query]="shippedOrdersQuery" title="Shipped Orders"></app-chart>
    </div>
  </div>
  <div class="row">
    <div class="col-sm-6">
      <app-chart chartType="line" [query]="lineChartQuery" title="New Users Over Time"></app-chart>
    </div>
    <div class="col-sm-6">
      <app-chart chartType="stackedBar" [query]="stackedBarChartQuery" title="Orders by Status Over time"></app-chart>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

完成了！你可以在这里找到[生成的仪表板，在这里](https://statsbotco.github.io/cubejs/angular-dashboard/)找到一个 codesandbox 演示[。](https://codesandbox.io/s/cubejs-client-ngx-demo-cs1kg?fontsize=14)
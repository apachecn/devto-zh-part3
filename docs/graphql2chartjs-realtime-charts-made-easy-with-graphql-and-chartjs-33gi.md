# graphql2chartjs:使用 graphql 和 chartjs 可以轻松制作实时图表

> 原文：<https://dev.to/hasurahq/graphql2chartjs-realtime-charts-made-easy-with-graphql-and-chartjs-33gi>

[![realtime chart with live data](img/1414670c24b02e0d15b375156c1f6161.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---ZGljCiq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://storage.googleapis.com/graphql-engine-cdn.hasura.io/assets/graphql2chartjs/live-chart.gif)

[我们](https://hasura.io)开始在 GraphQL 中使用 [ChartJS](https://www.chartjs.org) ，这样我们就可以利用 GraphQL 的实时订阅来构建实时图表。很快我们意识到，我们可以自动将 GraphQL 数据重组为 ChartJS 期望的形式。

> graphql2chartjs 是一个开源工具，可以根据 ChartJS API 重塑 graphql 数据。这使得构建图表就像简单地进行 GraphQL 查询一样简单。

该工具背后的思想是通过简单地在 GraphQL 查询中添加几个别名，从 GraphQL 响应中生成 ChartJS 兼容的数据对象。

[*点击这里查看现场演示*](https://graphql2chartjs-examples.herokuapp.com/)

## 用法同 Hasura

Hasura 给你一个即时的实时 GraphQL API，基于现有的 Postgres 数据库。您可以创建视图来捕获数据库中的分析和聚合，并立即将它们转换成图表。
如果你使用 Postgres 和 Hasura，这就是使用 graphql2chartjs 的样子:

[![graphql2chartjs](img/5bc6dc26a75dd185b2ba159b0313c894.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GZKUPTC1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/graphql-engine-cdn.hasura.io/img/graphql2chartjs-explained.png)

观看下面的视频，了解如何在现有的 Postgres 数据库上使用 Hasura，创建视图和构建图表。

 [![youtube video demo](img/c85088783e60310c923eafe44d7c8d3c.png)
T4】](https://www.youtube.com/watch?v=153iv1-qFuc&feature=youtu.be)

继续阅读:[https://blog . hasura . io/graphql 2 chart js-real time-charts-made-easy-with graphql-and-chart js](https://blog.hasura.io/graphql2chartjs-realtime-charts-made-easy-with-graphql-and-chartjs/)
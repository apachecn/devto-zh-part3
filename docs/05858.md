# 当查询缓慢时记录 MongoDB 查询计划。

> 原文：<https://dev.to/kelp404/log-query-plans-to-console-when-the-query-is-slow-502o>

## 如何调优关于 MongoDB 的性能问题？

我们可以使用`.explain()`，然后 [MongoDB](https://www.mongodb.com/) 将返回[解释结果](https://docs.mongodb.com/manual/reference/explain-results/)。解释的结果是这样的。

```
"executionStats" : {
   "executionSuccess" : <boolean>,
   "nReturned" : <int>,
   "executionTimeMillis" : <int>,
   "totalKeysExamined" : <int>,
   "totalDocsExamined" : <int>,
   "executionStages" : {
      "stage" : <STAGE1>
      "nReturned" : <int>,
      "executionTimeMillisEstimate" : <int>,
      "works" : <int>,
      "advanced" : <int>,
      "needTime" : <int>,
      "needYield" : <int>,
      "saveState" : <int>,
      "restoreState" : <int>,
      "isEOF" : <boolean>,
      ...
      "inputStage" : {
         "stage" : <STAGE2>,
         "nReturned" : <int>,
         "executionTimeMillisEstimate" : <int>,
         ...
         "inputStage" : {
            ...
         }
      }
   },
   "allPlansExecution" : [
      {
         "nReturned" : <int>,
         "executionTimeMillisEstimate" : <int>,
         "totalKeysExamined" : <int>,
         "totalDocsExamined" :<int>,
         "executionStages" : {
            "stage" : <STAGEA>,
            "nReturned" : <int>,
            "executionTimeMillisEstimate" : <int>,
            ...
            "inputStage" : {
               "stage" : <STAGEB>,
               ...
               "inputStage" : {
                 ...
               }
            }
         }
      },
      ...
   ]
} 
```

*   太大意味着你的查询需要扫描很多文档。
*   当`stage`为`COLLSCAN`时，意味着 MongoDB 从集合而不是索引中扫描文档。从索引中扫描比从集合中扫描更快。

当我们在控制台上记录所有解释结果时。有太多的信息，以至于我们找不到我们想要的。

## 一个[猫鼬](https://mongoosejs.com/)插件，在查询缓慢时显示查询计划。

您可以自己设置何时显示解释结果。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ kelp404 ](https://github.com/kelp404) / [猫鼬-侧写师](https://github.com/kelp404/mongoose-profiler)

### Mongoose 的性能调优工具。查询速度慢时显示解释结果。

<article class="markdown-body entry-content container-lg" itemprop="text">

# mongoose-profiler

[![npm version](img/568a48b067b7c43e49e719d1aff45c46.png)](https://www.npmjs.com/package/mongoose-profiler)[![Language grade: JavaScript](img/1aeede26ddea119645aefa462e4ab14f.png)](https://lgtm.com/projects/g/kelp404/mongoose-profiler/context:javascript)[![Coverage Status](img/8193a3c84af9213c6b0bb943bc8ac351.png)](https://coveralls.io/github/kelp404/mongoose-profiler?branch=master)[![Actions Status](img/1473e97d2d36b78b61da7a3a9f73c5b4.png)T11】](https://github.com/kelp404/mongoose-profiler/actions)

这是一个用于调优性能的[mongose](https://mongoosejs.com)插件。
查询慢的时候会在控制台上显示[解释结果](https://docs.mongodb.com/manual/reference/explain-results/)。比如 mongodb 扫描集合中所有没有索引的文档。
**不要用在生产上。**

## 装置

```
$ npm install mongoose-profiler --save-dev
```

## 快速启动

```
const mongooseProfiler = require('mongoose-profiler');
schema.plugin(mongooseProfiler());
```

当您在没有索引的情况下执行这个查询时，您将在控制台上看到一些消息。

```
ProductModel
  .where({state: 'active'})
  .where({owner: {$in: ['5c9d9428e7462d3d989cb69b', '5c9d95acea5c9b4036d97c88']}})
  .limit(100);
```

```
Mongoose:      64ms Products.find({ state: 'active', owner: { '$in': [ ObjectId("5c9d9428e7462d3d989cb69b"), ObjectId("5c9d95acea5c9b4036d97c88") ] } }, { skip: 0, limit: 100 })
[ { queryPlanner
     { plannerVersion: 1
       namespace: 'database.Products'
       indexFilterSet: false,
       parsedQuery:
        { '$and':
           [ { state:
```

…</article>

[View on GitHub](https://github.com/kelp404/mongoose-profiler)

```
const mongooseProfiler = require('mongoose-profiler');
schema.plugin(mongooseProfiler({
  isAlwaysShowQuery: true,
  duration: 1000,          // Show query plans when it took more than this time (ms).
  totalDocsExamined: 1000, // Show query plans when "totalDocsExamined" more than this value.
  level: 'COLLSCAN'        // Show query plans when the stage is "COLLSCAN".
})); 
```
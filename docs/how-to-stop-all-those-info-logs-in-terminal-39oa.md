# 如何停止所有这些信息:在终端日志

> 原文：<https://dev.to/oathkeeper/how-to-stop-all-those-info-logs-in-terminal-39oa>

我们的工作项目是使用 Node.js/Express.js next . js 和 React-Redux 应用程序来管理产品的仪表板。

现在这个代码库是巨大的，它让我很困惑 Express router 如何定义一个路由，然后 redux action creator 对其进行 api 调用，并获取、上传、发布、删除数据。所以我写这篇文章的问题是关于我如何停止 nextJS 在每个页面构建时给我信息。它持续记录如下消息

```
info: GET /json 302 0ms statusCode=302, url=/json, host=[::1]:4002, method=GET, httpVersion=1.1, originalUrl=/json, , responseTime=0
  morgan log request +6ms
GET /json 302 1.093 ms - 46 
```

Enter fullscreen mode Exit fullscreen mode

我猜是每一秒或每一毫秒。我想删除所有这些，因为我无法计算我在访问每个 API 端点时放置的控制台日志，这使得我的终端非常混乱，并且很难找到所需的控制台日志。
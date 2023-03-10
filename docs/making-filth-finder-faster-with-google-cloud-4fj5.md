# 使用谷歌云加快污物查找速度

> 原文：<https://dev.to/ketoaustin/making-filth-finder-faster-with-google-cloud-4fj5>

我写了一些关于污物查找器的文章，这是一个显示你附近纽约餐馆卫生检查的应用程序。最近，我[写了](https://codehannah.nyc/nyc_geo_client_api)关于为餐厅提供前端服务的 API。虽然这个实现工作正常，但加载速度相当慢。为了加快加载速度，我[重写了后端](https://github.com/hcarnes/filth_finder/commit/40ea0759ae863bd5b6b020c28b671b92275cdcbf)，以便从谷歌云存储中提取索引餐馆的信息。看看它[加载](https://filth-finder.codehannah.nyc/)的速度有多快。

> 如果你感兴趣，这里有另一个关于用 React 钩子重写 Filth Finder 的[帖子](https://codehannah.nyc/filth_finder_react_hooks)。

## 为什么慢了？

以前，这款应用程序作为两个不同的应用程序托管在 Heroku 上，每个应用程序都需要 5-10 秒才能启动，因为它们属于免费计划。前端提供 React 应用程序，后端提供餐馆列表 API。最后，这些应用程序不会同时启动，这意味着后端只会在前端完成后才开始旋转。最终结果是，如果一段时间内没有人访问该应用程序，用户需要等待近 20 秒才能开始使用它。

## 移向云端

在[Google Cloud Storage:node . js Client](https://www.npmjs.com/package/@google-cloud/storage)的帮助下，我现在有了一个[脚本](https://github.com/hcarnes/filth_finder/blob/40ea0759ae863bd5b6b020c28b671b92275cdcbf/scripts/build-geocode-index.js)，它从纽约市开放数据 API 中获取所有的餐馆，并将它们放入 Google Cloud Storage bucket 中。该市每天都会更新健康检查信息，因此这个脚本需要定期运行，以便从新开业的餐馆中提取数据。加载餐厅大约需要 45 分钟。

## 更新编制模型

> 根据维基百科，哈弗辛公式确定了给定经度和纬度的球体上两点之间的大圆距离。

我还更新了[建立模型](https://github.com/hcarnes/filth_finder/blob/40ea0759ae863bd5b6b020c28b671b92275cdcbf/src/models/Establishment.js#L13)，以便从新创建的 Google 云存储桶而不是旧的后端获取数据。[哈弗辛图书馆](https://www.npmjs.com/package/haversine)允许机构模型计算用户(纬度和经度通过参数传递)与近 27000 家餐馆之间的距离。然后根据用户和餐馆之间的距离以升序返回餐馆，然后限制为 20 个结果以保持前端灵活。JSON 文件非常简单，看起来像这样:

```
[
   {
      "latitude" : 40.7178921762742,
      "camis" : "41631962",
      "longitude" : -74.0008766031009,
      "dba" : "POPEYES LOUISIANA KITCHEN"
   },
   {
      "latitude" : 40.7560475658859,
      "camis" : "50072138",
      "dba" : "HUI'S GARDEN",
      "longitude" : -73.8335953523179
   },
   {
      "longitude" : -73.8279669765882,
      "dba" : "JIN DAL LAE 8",
      "latitude" : 40.7648883680589,
      "camis" : "50087614"
   },
   // 26k+ more restaurants...
] 
```

Enter fullscreen mode Exit fullscreen mode

## 火基

为了让应用程序加载速度更快，我按照[这个指南](https://medium.freecodecamp.org/react-and-firebase-are-all-you-need-to-host-your-web-apps-f7ab55919f53)利用谷歌云 Firebase 的免费静态托管。这完全消除了对 Heroku 的需求，Firebase 静态托管不需要任何启动。

## 期末笔记

现在应用程序运行得更快了，因为 React 前端立即从 Firebase 加载，并快速从 Google 云存储中获取餐厅列表。

Google Cloud+dirty Finder =永远的朋友(或者至少在更好的东西出现之前！).

 [![drawing of cat and shiba hugging](img/013bdd39de501a6f8a1284de7fc2b2dc.png)
T4】](https://codehannah.nyc/img/friends.svg)
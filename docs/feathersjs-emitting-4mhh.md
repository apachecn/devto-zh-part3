# 羽毛散发

> 原文：<https://dev.to/mattchewone/feathersjs-emitting-4mhh>

<figure>[![](img/b23dd11848763d62721070b674b9faf5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ikTAj5iN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGcntRQ8wvI-AUugDj8kRUw.jpeg) 

<figcaption>照片由 [rawpixel](https://unsplash.com/photos/faUzKz3TvpM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Unsplash](https://unsplash.com/search/photos/publish?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

FeathersJS 使得编写实时 web API 变得非常容易。我想谈谈如何从钩子中发出数据。我有一个应用程序，它有两个服务组和 group-access，如果用户在 group-access 集合中有记录，他们只能从组中获取项目。

但这意味着当用户通过组访问服务被授予访问权限时，他们必须重新加载页面，以便重新获取组来反映新的访问权限。
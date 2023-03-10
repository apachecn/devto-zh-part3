# 我为什么离开 App Engine 转而使用云运行

> 原文：<https://dev.to/weezer924/why-i-left-app-engine-for-cloud-run-mem>

谷歌应用引擎是我最喜欢的 GCP 服务之一。它使我的应用程序易于部署，并且不需要担心服务器负载。它与我的 Angular/Vue.js SPA 应用程序配合良好。

然后我的膝盖犯了一个错误...

抱歉，我是说我的应用引擎服务出错了。

```
Error: EROFS: read-only file system, mkdir '/srv/sessions' at Object.mkdirSync (fs.js:752:3) at Object.mkdirsSync 
```

只读文件系统？！？！我只想使用节点持久化。对于一些小的缓存文件...你为什么要这样对我！

[![](img/ca5ba66dfba57ed5f2b9e11e856426a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aRS6_XbL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tj35fcpg6hldgpt1glh3.gif)

下面是来自 App Engine 文档的原因:

> 在 App Engine 中，应用程序部署到的本地文件系统是不可写的。这种行为确保了应用程序的安全性和可伸缩性。
> 
> 但是，如果应用程序需要在运行时读写文件，App Engine 提供了内置的 Google 云存储流包装器，允许您使用许多标准的 PHP 文件系统函数在 App Engine PHP 应用程序中读写文件。

1.我不是 PHPer，用 Google 云存储只是为了一些缓存文件？圆盘烤饼...

2.我的程序运行良好，我不想改变它。(比如使用云存储)

3.从零开始使用谷歌电脑引擎和安装服务器？谁愿意关心服务器负载？

[![](img/4aacc1149d89eff7c614cb4d38d5c3c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3zhO4h_s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2n04wmsg456fbeyet24v.jpg)

然后我发现了一个值得一试的方法！

[![](img/2cd4ecab485286e932faa2f754dea8ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tAMr1QNO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rd9ytckmzcuaowynkbas.jpg)

> Cloud Run 是一个托管计算平台，使您能够运行可通过 HTTP 请求调用的无状态容器。云运行是无服务器的:它将所有基础设施管理抽象化，因此您可以专注于最重要的事情—构建优秀的应用程序。

嗯，这就是我想要的。把我的申请放进一个容器里。然后上传到云跑。
它将为我的容器提供工作负载的灵活性和可移植性。

好吧！我们黑吧。

1.创建一个 docker 文件。我用的是 nuxt.js

```
FROM node:10

WORKDIR /usr/src/app

ENV PORT 8080
ENV HOST 0.0.0.0

COPY package*.json ./

RUN npm install --only=production

COPY . .

RUN npm run build
CMD npm start 
```

2 .从 dockerfile 构建 docker 映像。

```
 docker build ./  -t  gcr.io/project-name/image-name:1.0 
```

3.将映像推送到您的 GCP 集装箱注册处

```
docker push gcr.io/project-name/image-name:1.0 
```

4.如果成功的话，它会是这个样子。
[![](img/8464810e093eb77dcb20361f69bc7089.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---pG_OT15--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pdtwu3cg6gdpmyzoxpw3.png) (对不起日本人)

5.点按它，选择「在云端执行时建立服务」。几分钟后，你的集装箱将在 GCP 运行。

好了，我的缓存文件起作用了！我所有的程序运行得和我的本地程序一样好。
而使用容器构建微服务是一个很好的特性。

那么，我要和 GAE 说再见了？

嗯，GAE 仍然是一个非常好的服务。和 SPA 一样，你可以在你的前端多加注意。

云润只是给了我另一种选择。
了解自己需要什么。然后你就可以决定你需要用哪个了。
# 可编程边沿

> 原文：<https://dev.to/sanemat/programmable-edge-2o8h>

10 年前，我们说“数据库访问太慢了。”我们使用了缓存层 memcached。

5 年前，我们说“访问网络服务器太慢。”我们用了 CDN，应用缓存。

两年前，我们说过“appCache 不够灵活”我们使用了 ServiceWorker。

现在，我们说“Edge(包括 CDN)不灵活。”

## 苍蝇简介

这是来自[飞自述](https://github.com/superfly/fly/blob/f524f833cf83f87d6f5926cf247ee93b2241a1c2/README.md)，

[![Edge](img/99f34fc400c4630ae23c6cbd606ad74f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TkiVP4j9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g5uxx50pbebps4nt9lfo.png) ，这是 edge，我想通过更可编程的方式来创建这个，并获得更大的灵活性。

## 例 1

[https://vanilla-terrier-972.edgeapp.net/](https://vanilla-terrier-972.edgeapp.net/)

这只是对[https://www . money forward . VN](https://www.moneyforward.vn)的代理。

这是从[https://github.com/superfly/cdn#try-the-starter-app](https://github.com/superfly/cdn#try-the-starter-app)T2【入门】

```
import { backends, proxy, middleware, pipeline } from "@fly/cdn";

const mw = pipeline(
  middleware.httpsUpgrader,
  middleware.httpCache
)

const app = mw(
  proxy("https://www.moneyforward.vn")
);

fly.http.respondWith(app); 
```

Enter fullscreen mode Exit fullscreen mode

我写了这个，并部署飞行。我可以用简单的方法创造优势！

## 例 2

提供的示例

```
fly.http.respondWith(function(request){
   return new Response("Hello! We support whirled peas.", { status: 200})
 })
 // if you'd prefer to be service worker compatibility, this is also supported:
 // around addEventListener('fetch', function(event){}) 
```

Enter fullscreen mode Exit fullscreen mode

## 例 3

提供的示例

```
addEventListener('fetch', function (event) {
   event.respondWith(new Response('Redirecting',
     {
       headers: {
         'Location': 'https://fly.io/docs/apps/'
       },
       status: 302
     }
   ))
 }) 
```

Enter fullscreen mode Exit fullscreen mode

## 参考文献

*   [https://fly.io/docs/](https://fly.io/docs/)
*   [https://github.com/superfly/cdn#clone-and-run-tests](https://github.com/superfly/cdn#clone-and-run-tests)
*   [https://github.com/superfly/fly/tree/master/examples](https://github.com/superfly/fly/tree/master/examples)
*   [https://github.com/superfly/fly/blob/master/README.md](https://github.com/superfly/fly/blob/master/README.md)
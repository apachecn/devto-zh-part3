# 我做的第一个代码抓取

> 原文：<https://dev.to/khalisafkari/the-first-code-grab-i-made-40c5>

blogger 流链接是用 javascript 引擎 nodejs 语言创建的一个抓取工具

我的灵感来自于制作 grab，因为当我研究并发现 YouTube 视频频道 [Wesbos](https://www.youtube.com/user/wesbos/) 时，这是我希望制作 grab 的最初来源，获取 blogger.com
的视频网址

```
 // online example blogger 
  `https://khalis-khalis411.now.sh/?url=https://prikate09.blogspot.com/2017/10/all-ani-29-oktober-2017-413.html`

   //localhost mode blogger
   `http://localhost:3000/?url=https://prikate09.blogspot.com/2017/10/all-ani-29-oktober-2017-413.html`

   //online example cloudvideo.tv
  `https://khalis-khalis411.now.sh/cloudvideo?url=https://cloudvideo.tv/42xcdwiwamlh`

  //localhost mode cloudvideo.tv
   `http://localhost:3000/cloudvideo?url=https://cloudvideo.tv/42xcdwiwamlh`

   //online example photos.google.com
   `https://khalis-khalis411.now.sh/photos?url=https://photos.google.com/share/AF1QipMTEPAiVF8t0YqLukflnOSQjwfd8ARIoT2h37AXvYO1uaWodbeiFoBUDuD_19tEbg/photo/AF1QipPA2Bq0JlAR9LoGD3mogsxSb9OZWEG4XqBDD4Rv?key=cjhUT0xrZjM5NGN2SVRLOVptZU5SMUlKV0lQYWpB`

   //localhost mode photos.google.com
   `http://localhost:3000/photos?url=https://photos.google.com/share/AF1QipMTEPAiVF8t0YqLukflnOSQjwfd8ARIoT2h37AXvYO1uaWodbeiFoBUDuD_19tEbg/photo/AF1QipPA2Bq0JlAR9LoGD3mogsxSb9OZWEG4XqBDD4Rv?key=cjhUT0xrZjM5NGN2SVRLOVptZU5SMUlKV0lQYWpB` 
```

> 代理模式
> [![](img/8bc7acc40a67b55883a312647fc8731a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hZ25XkHr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/7TfqEUc.png)
> 
> json 模式
> [![](img/ca47cf1b245f8a9a9d36a9b4a7d93074.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y2NVwBA7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/xETf6I7.png)

回购:[哈利斯](https://github.com/khalisafkari/khalis/)
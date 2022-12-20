# 走向 PWA 的第一步

> 原文：<https://dev.to/owlfox/first-step-to-pwa-3gfh>

通过参加 [Safari Online](https://learning.oreilly.com/videos/progressive-web-apps/9781789135770?autoplay=false) 的[Maximilian Schwarzüller](https://www.linkedin.com/in/maximilian-schwarzm%C3%BCller-66b152a5)的课程，我正在学习如何构建一个 PWA(渐进式网络应用程序)。我认为它也可以在许多其他平台上使用，如 packt 或 udemy，有更多的功能，如论坛，但因为我订阅了 Safari...是的，就是它。

我从 packt [页面](https://www.packtpub.com/web-development/progressive-web-apps-pwa-complete-guide-video)下载了代码源文件。我会继续上传我的 PWA 进度到 [github](https://github.com/owlfox/pwa_practice) 和这里。

PWA 基于 WEB 技术(Html/CSS/JS ),此外还使用浏览器功能，使网页在没有互联网接入的情况下也能工作。让我们深入课程以了解更多信息:

# [T1】manifest . JSON](#manifestjson)

为了使我们的网页可以添加到我们的 android 手机的主屏幕上，第一件事就是将`manifest.json`文件添加到你网站的所有 html/视图中。这将让加载您的页面的浏览器知道他们正在处理 PWA。

一个`manifest.json`文件的细节如下所示:

```
{  "name":  "Hohoho you're on PWA",  "short_name":  "Hoho",  "start_url":  "/index.html",  "scope":  ".",  "display":  "standalone",  "background_color":  "#636363",  "theme_color":  "#636363",  "description":  "Just a practice of PWA",  "dir":  "ltr",  "lang":  "en-US",  "orientation":  "portrait-primary",  "icons":  [  {  "src":  "/simg/icons/app-icon-48x48.png",  "type":  "image/png",  "sizes":  "48x48"  },  {  "src":  "/simg/icons/app-icon-96x96.png",  "type":  "image/png",  "sizes":  "96x96"  },  {  "src":  "/simg/icons/app-icon-144x144.png",  "type":  "image/png",  "sizes":  "144x144"  },  {  "src":  "/simg/icons/app-icon-192x192.png",  "type":  "image/png",  "sizes":  "192x192"  },  {  "src":  "/simg/icons/app-icon-256x256.png",  "type":  "image/png",  "sizes":  "256x256"  },  {  "src":  "/simg/icons/app-icon-384x384.png",  "type":  "image/png",  "sizes":  "384x384"  },  {  "src":  "/simg/icons/app-icon-512x512.png",  "type":  "image/png",  "sizes":  "512x512"  }  ],  "related_applications":  [  {  "platform":  "Hohoho",  "rul":  "https://play.google.com/store/apps/details?id=com.gorro.nothing",  "id":  "com.gorro.nothing"  }  ]  } 
```

为了添加引用，在 head 元素中添加下面几行:

```
 <link rel="manifest" href="/manifest.json">

  <!-- For Safari pwa -->
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="grey">
  <meta name="apple-mobile-web-app-status-title" content="Hohoho">
  <link rel="apple-touch-icon" href="/simg/icons/apple-icon-57x57.png" sizes="57x57">
  <link rel="apple-touch-icon" href="/simg/icons/apple-icon-60x60.png" sizes="60x60">
  <link rel="apple-touch-icon" href="/simg/icons/apple-icon-72x72.png" sizes="72x72">
  <link rel="apple-touch-icon" href="/simg/icons/apple-icon-76x76.png" sizes="76x76">
  <link rel="apple-touch-icon" href="/simg/icons/apple-icon-114x114.png" sizes="114x114">
  <link rel="apple-touch-icon" href="/simg/icons/apple-icon-120x120.png" sizes="120x120">
  <link rel="apple-touch-icon" href="/simg/icons/apple-icon-144x144.png" sizes="144x144">
  <link rel="apple-touch-icon" href="/simg/icons/apple-icon-152x152.png" sizes="152x152">
  <link rel="apple-touch-icon" href="/simg/icons/apple-icon-180x180.png" sizes="180x180">

  <!-- For ms edge pwa -->
  <meta name="msapplication-TileImage" content="/simg/icons/app-icon-144x144.png">
  <meta name="msapplication-TileColor" content="#fff">
  <meta name="theme-color" content="#636363"> 
```

然后我们可以打开我们的手机浏览器，你会发现有一个“添加到主屏幕”选项出现。添加它，它将像一个独立的应用程序一样工作。

虽然在撰写本文时，Safari/Edge 还不完全支持 PWA，但我认为，如果...有用！

# 关于 PWA 的更多信息

1.  从 chrome 73 开始，你可以用 PWA 制作一个桌面应用程序，哦，多么令人兴奋。请查看此[链接](https://developers.google.com/web/progressive-web-apps/desktop)了解更多信息。
2.  我发现使用 USB 共享来允许我的手机访问我在 MACOSX 上开发的服务器很方便！
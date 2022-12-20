# Egghead 课程:用 RxJS 进行简单缓存

> 原文：<https://dev.to/angular/egghead-lesson-simple-caching-with-rxjs-3ob6>

*这是我最新的 Egghead 视频课程的附带文章，讲述了如何利用 RxjS 在 Angular 中缓存数据。*

缓存很难，这是软件开发中的一个难题，因为很多事情都可能出错。尽管有些实现非常容易实现，但问题通常在于何时使缓存无效，以便不向用户显示陈旧数据，以及调试问题(因为您可能会进入缓存，而看不到您实际期望的内容)。我们都经历过，但我们仍然经常需要缓存数据来提高应用程序的性能。我一直在考虑的事情有...

*   在最近的时刻延迟缓存。先尝试其他优化，以防以后添加缓存
*   考虑服务器端 API 级缓存，无论是响应头还是 Redis 这样的缓存服务器。请注意，API 是您最了解数据新鲜度的单一入口点。

有两分钟时间吗？前往我的网站上的 Egghead 视频课程和可运行的 Stackblitz。[阅读更多](https://juristr.com/blog/2018/11/videolesson-rxjs-caching-angular/?utm_source=devto&utm_medium=crosspost)
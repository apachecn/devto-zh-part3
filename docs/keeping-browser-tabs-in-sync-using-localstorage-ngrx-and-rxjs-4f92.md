# 使用 localStorage、NgRx 和 RxJS 保持浏览器标签同步

> 原文：<https://dev.to/angular/keeping-browser-tabs-in-sync-using-localstorage-ngrx-and-rxjs-4f92>

在这篇文章中，我们将看看当一个用户打开多个标签页时，如何保持应用程序状态的同步。我们将通过使用 Web 存储 API、NgRx(存储和效果)和 RxJS 来实现这一点。遵循示例需要 NgRx 的基本知识。

据我所知，有两种同步状态的方法。其中之一是将动作从一个选项卡发送到另一个选项卡，另一个是将(部分)状态从一个选项卡发送到另一个选项卡。虽然两者的实现非常相似，但它们都有自己的亮点。

作为起点，我们将从上一篇文章“让我们来谈谈 NgRx 中的动作和动作创建者”中停止的地方开始，在那里我们创建了一个简单的购物清单。

[阅读更多...](https://blog.angularindepth.com/keeping-browser-tabs-in-sync-using-localstorage-ngrx-and-rxjs-87de3bca4e2c)
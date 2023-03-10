# 服务人员注册了吗？

> 原文：<https://dev.to/negue/service-worker-registered-yet-354d>

## [TL；博士](#tldr)

有一个小应用程序，试图让 angular 的服务人员注册，花了比预期更长的时间。

使用普通的`ng add @angular/pwa --project *project-name*`添加所有需要的文件。然而，使用`--prod`构建/服务它不会注册任何东西。

## 好。让谷歌。

许多网站/页面/小时后，没有任何东西真正回答了这个问题，有的只是像
一样的“使用自定义注册码”

```
if ('serviceWorker' in navigator && environment.production) {
  navigator.serviceWorker.register('./ngsw-worker.js');
} 
```

这很有效，但是如果这是添加工人的唯一方法，那么 Angular 的`ServiceWorkerModule.register('./ngsw-worker.js')`为什么不起作用呢？

## 让我们调试。

一开始我以为是因为用了`nrwl`和`angular.json`出了什么问题。

在我的工作区创建了第二个应用程序，添加了 pwa，运行良好。

## 现在我明白了为什么我的应用程序不想在那里注册...

一部分一部分地添加到新的应用程序中，一切都正常，直到我添加了 NgRx EffectModule。*古怪。*Effects-Class 是空的，只是注入了一些服务。

几个小时的搜索后，为什么这可能是一个原因，它登记的正常方式...

*   错误的基础 Href？-已检查，无变化
*   默认路由器重定向' '--> '页面'-已删除，仍然不起作用
*   已更新至 8.0-rc3 -仍不工作

查看`ServiceWorkerModule`的源代码，我看到它使用`ApplicationRef.isStable()` -Observable 来等待注册。但是`isStable()`只是停留在`false`。

幸运的是，我还看到了`.register`-方法也有一个`options`参数。在里面，你可以设置`registrationStrategy`。(*在我写这篇文章的时候，哪些~~不是~~还没有在文档中列出。*)

可能的策略:

*   `registerWhenStable`(默认)
*   `registerWithDelay:*delay*`
*   `registerImmediately`

## 解。

```
 ServiceWorkerModule.register('./ngsw-worker.js', {
    enabled: environment.production,
    registrationStrategy: 'registerImmediately'
  }), 
```

使用`registerWithDelay`或`registerImmediately`至少可以在没有自定义注册码的情况下注册它。(所以它应该是未来的证明，只使用`ServiceWorkerModule.register`-调用)
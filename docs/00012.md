# 滚动到角度路由器导航的顶部

> 原文：<https://dev.to/angular/scroll-to-top-on-angular-router-navigation-1iha>

*最初发布于[https://samvloeberghs . be](https://samvloeberghs.be/posts/scroll-to-top-on-angular-router-navigation)2016 年 11 月 19 日*

**更新(2018 年 12 月):** *本文已更新为代表新可用的`ViewportScroller`类，可从 Angular v7+获得。这个类实现包装了窗口对象，只有当窗口对象可用时才执行。*

当我创建这个博客并为移动设备优化它时，我在用 Angular 从一条路线导航到另一条路线时，经历了一些默认但不那么用户友好的行为。

问题是移动设备上的内容可能会深入到初始视口高度以下。所以当你向下滚动并点击一个内部链接到另一个页面时，你会停留在那个高度。

这与普通 web 应用程序中页面间的标准导航有些不同，在普通 web 应用程序中，页面会重新加载，默认情况下您从顶部开始。在 S.P.A .中，这可以通过使用本机`window.scroll`函数
滚动到导航顶部来轻松解决

```
window.scroll(0,0) 
```

通过监听事件`$routeChangeSuccess`或`$stateChangeSuccess`，可以轻松检测 Angular 1 和 ngRoute 甚至 ui 路由器中的路由导航。所以结合这两个要素，我们就有了:

```
// ngRoute:
$rootScope.$on('$routeChangeSuccess', () => {
    $window.scroll(0,0);
});

// ui-router:
$rootScope.$on('$stateChangeSuccess', () => {
    $window.scroll(0,0);
}); 
```

我在 Angular 路由器的文档中没有找到任何类似的东西，所以我去挖掘。事实是，我正在用 [@luisfarzati](https://twitter.com/luisfarzati) 的[angular artics 2 插件](https://github.com/angulartics/angulartics2)来追踪你们的行为:)这也是在导航上完成的，所以在那个插件上肯定也有类似的事情发生。插件 BTW 很好用！

## 收听角状导航事件

看来 Angular v2+路由器有一个`events`可观察属性，你可以订阅。是的，就这么简单。这些事件可以是任何预定义的类型`NavigationStart`、`NavigationCancel`、`NavigationEnd`或`NavigationError`。在我的情况下，我只需要`NavigationEnd`。

在保存导航的组件中`router-outlet`你只需要设置监听器，就像这样:

```
import { Component } from '@angular/core';
import { Router, NavigationEnd } from '@angular/router';
import { ViewportScroller } from '@angular/common';

@Component({
  selector: 'sv-app',
  template: '<router-outlet></router-outlet>'
})
export class AppComponent {

  constructor(
    readonly router: Router,
    readonly viewportScroller: ViewportScroller
  ) {

    router.events
      .filter(event => event instanceof NavigationEnd)
      .subscribe((event: NavigationEnd) => {
        // Angular v2-v6
        window.scroll(0, 0);
        // Angular v7+
        this.viewportScroller.scrollToPosition([0, 0]);
      });

  }
} 
```

**更新(2018 年 12 月):** *本文已更新为代表新可用的`ViewportScroller`类，可从 Angular v7+获得。这个类实现包装了窗口对象，只有当窗口对象可用时才执行。*

就这样了！请注意，`window`对象可能不是在每个上下文中都可用，除了浏览器。[查看由](http://juristr.com/blog/2016/09/ng2-get-window-ref/)[@法学家](https://twitter.com/juristr)撰写的这篇很棒的文章，阅读更多关于为什么你可能想要包装你的`window`对象引用的信息！

另外，请注意不要使用这些事件来处理业务逻辑，例如检查您是否可以根据一些身份验证规则导航到特定的路线。对于这些情况，您可能希望实现保护！更多关于守卫的信息可以在 Thoughtram 的 [@PascalPrecht](https://twitter.com/PascalPrecht) 的[这篇精彩文章](http://blog.thoughtram.io/angular/2016/07/18/guards-in-angular-2.html)中找到。

*最初发布于[https://samvloeberghs . be](https://samvloeberghs.be/posts/scroll-to-top-on-angular-router-navigation)2016 年 11 月 19 日*
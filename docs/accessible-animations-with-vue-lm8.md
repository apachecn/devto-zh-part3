# 使用 Vue 的辅助动画

> 原文：<https://dev.to/marcush/accessible-animations-with-vue-lm8>

为了让用户体验愉快，更重要的是，为了让用户体验更像应用程序，web 应用程序作者不仅使用了异步加载数据的策略，还使用了动画。虽然您的应用程序的用户体验可能会从中受益(参见:[“功能动画如何帮助改善用户体验”](https://www.everyinteraction.com/articles/functional-animation-helps-improve-user-experience/))，但突然的移动可能会导致一些患有运动敏感症(如[前庭障碍](https://vestibular.org/understanding-vestibular-disorder))的用户感到头晕、眩晕或恶心:

> 想象一个你的内部陀螺仪工作不正常的世界。就像喝醉了一样，所有的东西似乎都在自动移动，你的脚似乎永远都不太稳定，你的感觉比你身体的其他部分移动得更快或更慢。

*资料来源:A11y 项目的[前庭障碍入门](https://a11yproject.com/posts/understanding-vestibular-disorders/)*

幸运的是，有了一个新的 CSS 媒体查询([，它在浏览器支持](https://caniuse.com/#search=prefers-reduced-motion)方面获得了关注:`prefers-reduced-motion`。例如，在苹果的 MacOS 和 iOS 系统中，用户可以在操作系统级别启用这个设置(*系统设置-可访问性-显示*)——这让 CSS 作者检测这个用户偏好:

```
@media (prefers-reduced-motion: reduce) {
    /* Disable animation on your selectors here */
} 
```

但是为了在你的应用程序中创建一个可访问的体验，即使用户使用的操作系统没有提供这个设置，web 应用程序的作者应该使用他们材料中的 pwoer:管理状态。您可以在您的应用程序中建立一个关于动画的设置，模仿`prefers-reduced-motion`并可以应用到我们应用程序的`<body>`元素。因此，如果设置为 true，那么在 body 元素上出现以下选择器将起作用:

```
body.user-prefers-reduced-motion-reduce {
    /* Disable animation on your selectors here */
} 
```

我已经在[https://vuejs.accessible-app.com](https://vuejs.accessible-app.com)实施了这两种描述的方法和手段。如果你使用的是 Mac，并且想要摆脱它，那么进入操作系统的系统设置，设置“减少运动”复选框。如果你碰巧使用的系统没有这样的设置，你可以进入应用程序内的设置页面:点击帐户按钮，然后进入“我的设置”。在那里你会发现——实际上不是很多——但是有一个标记为“禁用动画”的复选框。以代码方式检查它会将`.user-prefers-reduced-motion-reduce`添加到主体中。

现在我们知道了激活，让我们来看看实际禁用动画的方法，这很简单:

```
@media (prefers-reduced-motion: reduce) {

  * {
    animation: none !important;
    -webkit-animation: none !important;
  }
}

.user-prefers-reduced-motion-reduce {

  * {
    animation: none !important;
    -webkit-animation: none !important;
  }
} 
```

我的“Accessibook”示例应用程序相当轻松地使用了动画:

*   一旦模式窗口被打开
*   点击其中一个菜单按钮(购物车、账户)

但是随着`.user-prefers-reduced-motion-reduce`或`prefers-reduced-motion: reduce`的设置，你会看到这些动画消失。我承认，禁用这些动画并不是最有说服力的例子——但希望我能理解这个想法。一个想法是，作为一个 web 应用程序的作者应该注意到“用户查询”,如偏好-减少-运动，并且你应该提供一个关于你的动画的选择--独立于这个媒体查询的传播。

如果您想了解 Accessibooks 的 Vuejs 实现的确切代码:

*   [在这里找到禁用 CSS 的动画](https://github.com/accessible-app/vuejs/blob/master/src/scss/utils/_a11y.scss)
*   [我如何使用 Vue 的状态管理器 vuex 来同步应用视图中的动画偏好设置](https://github.com/accessible-app/vuejs/blob/master/src/store.js#L16)
*   [How or prefers-reduced-motion 的用户设置“even”禁用 Vue 的`<transition />`组件——因为它使用 CSS 动画](https://github.com/accessible-app/vuejs/blob/master/src/components/ShoppingCartMenu.vue#L4)
*   [...以及设置页面如何工作](https://github.com/accessible-app/vuejs/blob/master/src/views/Settings.vue)
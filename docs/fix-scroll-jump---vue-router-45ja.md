# 修复意外滚动跳转路由器

> 原文：<https://dev.to/uwutrinket/fix-scroll-jump---vue-router-45ja>

你可能已经注意到在使用 [Vue 路由器](https://router.vuejs.org/)时，现代浏览器中的滚动行为发生了一个突破性的变化，叫做[scroll restore](https://developer.mozilla.org/en-US/docs/Web/API/History)。

这是一个很棒的特性，它允许浏览器通过基于 JS 脚本的路由跳回到先前保存的滚动位置。

不幸的是，它与 Vue 路由器实现的方法 [scrollBehavior](https://router.vuejs.org/guide/advanced/scroll-behavior.html#scroll-behavior) 相冲突。

如果你的`<router-view/>`上没有`<transition/>`，你可能看不出与旧行为有什么不同。
但如果你正在使用它，尤其是离开过渡，当你导航到一个新的页面时，页面滚动会在过渡开始时跳回到顶部。

解决方案是将[scroll restore](https://developer.mozilla.org/en-US/docs/Web/API/History)设置为`manual`，这样浏览器就不会试图在你的位置处理这个行为。

```
if ('scrollRestoration' in window.history) {
  window.history.scrollRestoration = 'manual';
} 
```

来源:[历史 API:卷轴修复](https://developers.google.com/web/updates/2015/09/history-api-scroll-restoration)作者:保罗·刘易斯
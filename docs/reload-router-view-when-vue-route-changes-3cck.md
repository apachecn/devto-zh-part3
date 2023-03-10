# 当 Vue 路由改变时更新“路由器视图”

> 原文：<https://dev.to/brylie/reload-router-view-when-vue-route-changes-3cck>

有时候，你的 Vue 路线会改变，但不会触发你的`router-view`更新。

有一种简单的方法来告诉`router-view`或者任何组件进行更新。只需将组件`key`属性绑定到一个反应性数据源。在路由器变更的情况下，可以将`router-view`绑定到`$route.fullPath`，如下:

```
<router-view :key="$route.fullPath"></router-view>
<!-- more verbose with v-bind:key -->
<router-view v-bind:key="$route.fullPath"></router-view> 
```

Enter fullscreen mode Exit fullscreen mode

现在，只要完整路径发生变化，`router-view`就会更新。

[![Creative Commons License](img/e59cd93fa004ceb14ed402ec095ed201.png)](http://creativecommons.org/licenses/by/4.0/) 
本作品获得[知识共享署名 4.0 国际许可](http://creativecommons.org/licenses/by/4.0/)的许可。
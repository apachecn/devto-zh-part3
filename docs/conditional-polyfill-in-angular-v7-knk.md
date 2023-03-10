# 角度 v7 中的条件 Polyfill

> 原文：<https://dev.to/daudr/conditional-polyfill-in-angular-v7-knk>

这篇文章之前发表在我的主博客上。

如果你有一个基于 Angular v7 的项目，没有机会升级到即将到来的 v8，或者你想测试未来版本中最有用的小功能之一，这里你会找到如何做。

[![Dance of souls](img/da0dfbe6ca9f52ee98dfef67335cc501.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ArPYyuce--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1511715282680-fbf93a50e721%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D1950%26q%3D80)

> 由 [Ahmad Odeh](https://unsplash.com/photos/JhqhGfX_Wd8) 在 [Unsplash](https://unsplash.com) 上拍摄的照片

## 绝招

在 Angular 的开发中，我们使用了带有许多现代 JavaScript 特性的 TypeScript。用户的浏览器并不支持所有这些文件，因此，作为产品构建的一部分，我们提供了一个名为 polyfills.js 的文件。

在过去的几年里，浏览器开始实现很多这样的 API。例如，使用最新版本 Chrome 的用户可能不需要我们作为 polyfills.js 的一部分提供的 ES2015 polyfills。相反，他们的浏览器将原生支持它们。这使得 polyfills.js 的大部分内容变得过时和不必要。

这就是为什么从 Angular CLI 7.3.0 开始，引入了有条件多填充加载！作为默认构建过程的一部分，Angular CLI 将生成两个包 polyfills.js 和 es2015-polyfills . js。poly fills . js 仅包含浏览器所需的 poly fills，这些浏览器本机实现了 es 2015 的所有功能。相比之下，es2015-polyfills.js 包含 polyfills.js + ES2015 中的所有聚合填充(如地图、集合等)。

结果应该是你的`index.html`的身体看起来像这样:

```
<body>
  <app-root></app-root>
  <script type="text/javascript" src="runtime.***.js"></script>
  <script type="text/javascript" src="es2015-polyfills.***.js" nomodule></script>
  <script type="text/javascript" src="polyfills.***.js"></script>
  <script type="text/javascript" src="main.***.js"></script>
</body> 
```

在上面的代码片段中，您可以看到 Angular CLI 向 es2015 polyfills 标签添加了`nomodule`属性。只有当浏览器不能识别[模块脚本 web api](https://html.spec.whatwg.org/multipage/webappapis.html#module-script) 时，该属性才允许浏览器下载并执行脚本，这意味着浏览器是旧的👌

您可能会想:我的所有用户都使用现代浏览器，为什么我要等待 CLI 生成永远不会使用的 es2015-polyfills.js？如果你真的这么想，那么你是一个快乐的开发者。如果您想完全忽略 ES2015 polyfills，则构建您的应用程序运行:

`ng build --es5BrowserSupport=false`

通过将- es5BrowserSupport 标志设置为 false，Angular CLI 将仅生成 polyfills.js，其中包含启用 ES2015 的浏览器所需的 polyfills，但您的应用程序将无法在这些旧浏览器上正常工作。

如果您不想在每次运行构建命令时都设置该属性，您可以直接在您的`angular.json`文件:
中修改该属性

```
"projects":  {  "project-name":  {  ...  "architect":  {  "build":  {  ...  "options":  {  ...  "es5BrowserSupport":  false, 
```

这个技巧可以让您保存大约 60Kb 的 javascript 脚本😎
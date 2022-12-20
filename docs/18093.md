# 在不暴露源地图的情况下调试生产中的角度应用程序

> 原文：<https://dev.to/kreuzerk/debug-angular-apps-in-production-without-revealing-source-maps-56di>

#### 处理源地图的替代方法

[![](img/d28619b0cebf999935d23fe1440528f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xXz2Z4a6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_uv_vlGy0jc05noZ-MZTKA.jpeg)

当我们的应用程序被部署到生产环境中时，我们经常会遇到与我们在开发过程中编辑的代码不同的代码。我们的代码在构建过程中会被修改和优化。

打字稿被翻译、删节和篡改。生成的包尽可能小，并且能够在浏览器中运行。

所有这些概念都很棒，因为它们提高了我们应用程序的性能。但是我们看到的是难以阅读并且妨碍调试的 JavaScript。

但是有一个解决办法。来源图！

了解如何在不暴露源地图的情况下启用它们！
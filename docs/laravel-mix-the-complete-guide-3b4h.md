# laravel Mix–完整指南

> 原文：<https://dev.to/larashout/laravel-mix-the-complete-guide-3b4h>

Laravel 提供了一个流畅的工具来编译你所有的前端资产，比如样式表和脚本，叫做 Laravel Mix。在本帖中，我们将了解什么是 Laravel Mix 以及如何使用它。

## 什么是 Laravel Mix？

根据 laravel 的官方文件:

Laravel Mix 提供了一个流畅的 API，使用几个常见的 CSS 和 JavaScript 预处理程序为您的 Laravel 应用程序定义 Webpack 构建步骤。

如果您曾经尝试过使用 WebPack 进行资产编译，您会发现使用 Mix 来编译您的所有资产是多么容易。您可以用一行代码定义资产编译，它的预配置设置将为您完成这项工作。

在 webpack.mix.js 文件中，我们将管理所有需要编译的资产。您可以在 Laravel 应用程序的根目录下找到这个文件。

你可以在 [Laravel Mix](https://www.larashout.com/laravel-mix) 阅读全文
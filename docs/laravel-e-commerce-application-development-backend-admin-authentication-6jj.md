# Laravel 电子商务应用程序开发–后端管理认证

> 原文：<https://dev.to/larashout/laravel-e-commerce-application-development-backend-admin-authentication-6jj>

这是 Laravel 电子商务应用程序开发系列的第四部分，在这一部分中，将使用 Laravel guards 实现管理员身份验证。

我假设您的机器上应该有电子商务应用程序项目，或者您可以从 Laravel 电子商务应用程序库获取它，我们将从上一部分离开的地方开始。

在最后一部分，我们创建了管理模型，迁移和种子，在这篇文章中，我们将实现管理区的认证。Laravel 的默认身份验证适用于用户模型，但我们将为管理模型实现我们自己的登录功能，这意味着我们将为不同的模型添加多个身份验证。

在 https://www.larashout.com/backend-admin-authentication 阅读全文
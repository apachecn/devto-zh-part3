# ASP.NET 核心中的依赖注入过滤器

> 原文：<https://dev.to/xhantimda/filters-with-dependency-injection-in-aspnet-core-1l8c>

最近，我想在一个定制的授权过滤器中添加一个日志记录器(这不是一个好主意)，但是我不想在过滤器中创建一个新的实例，我想让 DI 为我处理所有的事情。

我做了一些调查，发现了 **TypeFilterAttribute** 、 **ServiceFilterAttribute** 和 **IFilterFactory** 。

## **IFilterFactory**

IFilterFactory 接口允许您通过在过滤器中实现 CreateInstance 方法来创建过滤器的实例。当 MVC 调用一个过滤器时，它首先尝试将它转换成 IFilterFactory。如果强制转换成功，它将调用 CreateInstance 方法。
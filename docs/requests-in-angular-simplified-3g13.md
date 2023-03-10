# 角度请求:简化

> 原文：<https://dev.to/burlak/requests-in-angular-simplified-3g13>

在开发 Angular 应用程序时，肯定会有需要访问在线资源的时候。 ***可观察的*** 提供了一种简单而强大的访问和编程延迟资源的方式，并且可以使用 ***RxJS 操作符*** 进一步操作。

本指南旨在教你如何在一个 *Angular* 项目中实现简单的请求，并使用 Observables 处理它们的响应。

### 最简单的要求

假设你想在一个 API 上执行一个简单的请求，你会怎么做呢？您可以使用`HttpClient`类来获取访问 API 的可观察对象。

首先，您需要一个服务来初始化可观察对象，并在需要时将其返回给您的组件。您可以使用命令:
创建服务

```
ng generate service SERVICE_NAME 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们创建一个使用`HttpClient`返回被请求资源的可观察对象的函数，以及一个订阅该可观察对象的组件。

下面的代码演示了一个简单的 GET 和 POST 请求的例子，其中我们`console.log()`了我们得到的所有输出:
# 永远不要错过爱奥尼亚的一个错误

> 原文：<https://dev.to/robertohuertasm/never-miss-an-error-in-ionic-2if5>

您可能知道 [Angular](https://angular.io) 为集中式异常处理提供了一个`hook`，您可以覆盖它。[默认实现](https://angular.io/api/core/ErrorHandler)向`console`打印错误消息。

开发人员通常会通过提供自己的`ErrorHandler`来覆盖默认实现。通过这样做，他们可以管理所有的异常，并对它们做一些事情(例如，将它们存储在某个数据库中...).

做到这一点的方法非常简单:

```
class CustomErrorHandler implements ErrorHandler {
  handleError(error: Error) {
    // do something with the exception
  }
}

@NgModule({
  providers: [{ provide: ErrorHandler, useClass: CustomErrorHandler }],
})
class AppModule {} 
```

Enter fullscreen mode Exit fullscreen mode

无论何时发生未处理的异常，您都希望它被记录到`console`中，不管您的定制实现是否明确地这样做。至少这是默认的 [Angular](https://angular.io) 实现所提供的，事实上，这就是它的工作方式。在这个 [StackBlitz 项目](https://stackblitz.com/edit/angular-swallowing-errors)现场检查一下。

[https://stackblitz.com/edit/angular-swallowing-errors?](https://stackblitz.com/edit/angular-swallowing-errors?)

## 但是...爱奥尼亚怎么样？

在 [Ionic](https://ionicframework.com/) 中，为了提供相同的行为，你需要扩展 [IonicErrorHandler](https://ionicframework.com/docs/api/util/IonicErrorHandler/) :

```
class CustomErrorHandler extends IonicErrorHandler {
  handleError(error: Error) {
    // do something with the exception
  }
}

@NgModule({
  providers: [{ provide: ErrorHandler, useClass: CustomErrorHandler }],
})
class AppModule {} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利，但不幸的是，如果你忘记显式调用`console.error`，你将看不到你的`console`中的任何内容。在这种情况下，默认的[角度](https://angular.io)实现不起作用，您将**完全错过错误**。

你可以看看这个 [StackBlitz 项目](https://stackblitz.com/edit/ionic-swallowing-errors)并检查一下`console`。您不会看到任何错误，屏幕将保持空白，因为在这种情况下，错误发生在`app.component`的一个依赖项的构造函数中。

## TL；博士；医生

如果你正在使用 [Ionic](https://ionicframework.com/) 并且你覆盖了默认的 [IonicErrorHandler](https://ionicframework.com/docs/api/util/IonicErrorHandler/) ，如果你想在`console`中有一些可视性的话，不要忘记在那个处理程序中显式地调用`console.error`，以防一些意外的错误发生😉。

-
原载于 2018 年 10 月 14 日[robertohuertas.com](https://robertohuertas.com/2018/09/30/angular-error-handler)。
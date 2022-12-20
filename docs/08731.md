# RxJS finalize 运算符在可观察到的终止时执行逻辑

> 原文：<https://dev.to/angular/rxjs-finalize-operator-to-execute-logic-on-observable-termination-a0c>

**在本文中，我们将看看 RxJS `finalize`操作符。为了有一个实际的用例，让我们看看在 HTTP 请求期间禁用/启用表单提交按钮。**

## 在角度 HTTP 请求期间禁用/启用按钮

让我们来看看一个 RxJS `Observable`订阅:

```
this.someService.fetchDataFromApi()
  .subscribe(
    result => {
      // success
    },
    err => {
      // some error happened
    }
  ) 
```

Enter fullscreen mode Exit fullscreen mode

假设这个调用是由我们表单上的一个按钮点击触发的。许多人仍然双击这些按钮，我们肯定要防止 2 个调用被发送到我们的后端 API。当然，有不同的方法可以避免这种情况，但是对于这个例子来说，让我们走一条一旦按钮被点击就禁用它的路线，并在 http 调用终止时重新启用它。

```
this.isLoading = true;
this.someService.fetchDataFromApi()
  .subscribe(
    result => {
      // success
      this.isLoading = false;
    },
    err => {
      // some error happened
      this.isLoading = false;
    }
  ) 
```

Enter fullscreen mode Exit fullscreen mode

每当设置了`isLoading`时，我们就禁用表单上的按钮。现在和前面的例子一样，`isLoading = false`指令是重复的，因为我们想在成功和错误的情况下重新启用按钮。

[阅读更多](https://juristr.com/blog/2019/04/rxjs-finalize-operator/?utm_source=devto&utm_medium=crosspost)

* * *

## TL；大卫:这是相应的理论家的教训

查看[我对应的书呆子视频](https://egghead.io/lessons/angular-execute-code-when-the-rxjs-observable-terminates-with-the-finalize-operator)
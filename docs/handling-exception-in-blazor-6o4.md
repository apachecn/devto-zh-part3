# 处理 Blazor 中的异常

> 原文：<https://dev.to/rembou1/handling-exception-in-blazor-6o4>

# 处理 Blazor 中的异常

就像系统的每个部分一样，在前端部分，你应该注意异常。就像它的名字所说的:它不应该发生。但是我们并不完美，它们的发生有很多原因。目前，Blazor 中发生的异常被发送到浏览器控制台。但是我们可能需要更多:将它显示在网页上，甚至发送到我们的后端，以提醒团队出现了问题。

## 布拉索现在是怎么做到这一点的

异常记录是在 ComponentBase 类[上完成的，这里是](https://github.com/aspnet/AspNetCore/blob/master/src/Components/src/Microsoft.AspNetCore.Components/ComponentBase.cs)。方法 HandleException line 209 完成了这项工作:

```
 private static void HandleException(Exception ex)
{
    if (ex is AggregateException && ex.InnerException != null)
    {
        ex = ex.InnerException; // It's more useful
    }

    // TODO: Need better global exception handling
    Console.Error.WriteLine($"[{ex.GetType().FullName}] {ex.Message}\n{ex.StackTrace}");
} 
```

然后是 C#控制台。Error.WriteLine 由 mono 运行时在 js“console . error”中更改，就像 Console 一样。WriteLine 是 js“console . log”中的更改。不要问我它发生在哪里，mono 源代码对我来说很难读懂。我们可以看到这里没有很多扩展点。有一种方法可以在运行时覆盖静态方法(这里的[是](https://stackoverflow.com/questions/7299097/dynamically-replace-the-contents-of-a-c-sharp-method)),但是这似乎有风险。

我能想到的唯一入口就是控制台。错误:有一个控制台。SetError(textWriter ),我可以用它来发送我自己的实现。

## 实现 TextWriter

TextWriter 的实现非常简单，如下:

```
 public class ExceptionNotificationService : TextWriter, IExceptionNotificationService
    {
        private TextWriter _decorated;

        public override Encoding Encoding => Encoding.UTF8;

        public ExceptionNotificationService()
        {
            _decorated = Console.Error;
            Console.SetError(this);
        }
        //THis is the method called by Blazor
        public override void WriteLine(string value)
        {
            //do something with the error

            _decorated.WriteLine(value);
        }
    } 
```

*   我使用装饰模式，异常消息仍然会被发送到控制台
*   TextWriter 不是一个接口，而是一个实现了大多数方法的抽象类:只需要实现“编码”。所以这里我重写了 Blazor 调用的方法“WriteLine(string)”。如果 Blazor 团队改变了他们的异常处理，这些代码将无法工作。

然后我像这样在 StartUp.cs :
中注入我的服务

```
services.Add(new ServiceDescriptor(
    typeof(IExceptionNotificationService),
    typeof(ExceptionNotificationService),
    ServiceLifetime.Singleton)); 
```

## 我对错误做了什么

正如接口所暗示的，我将使用 event 来传播异常，所以我对我的类做了一点修改，如下所示:

```
 public class ExceptionNotificationService : TextWriter, IExceptionNotificationService
{
    private TextWriter _decorated;

    public override Encoding Encoding => Encoding.UTF8;

    /// <summary>
    /// Raised is an exception occurs. The exception message will be send to the listeners
    /// </summary>
    public event EventHandler<string> OnException;

    public ExceptionNotificationService()
    {
        _decorated = Console.Error;
        Console.SetError(this);
    }
    //THis is the method called by Blazor
    public override void WriteLine(string value)
    {
        //notify the listenners
        OnException?.Invoke(this,value);

        _decorated.WriteLine(value);
    }
} 
```

*   当异常发生时，每个注册了异常事件监听器的类都会得到通知。

然后为了注册，我在我的布局中这样做:

```
 cs
protected override async Task OnInitAsync()
{
    this.exceptionNotificationService.OnException += HandleExceptions;
    base.OnInit();
}
private void HandleExceptions(object sender, string s)
{
    JsInterop.Toastr("error", s).ContinueWith((a) => { });
}
public void Dispose()
{
    this.exceptionNotificationService.OnException -= HandleExceptions;
}

- The JsInterop.Toastr method just display a toastr.
- Don't forget to unsubcribe or the instance of your component will stay around for ever
- I couldn't find how to make this code async/await. So for async calls I had to add an empty ContinueWith as Wait() does not work on mono.

## Conclusion
It took me a while to find this solution but I'm glad I found one (it was on my blog post to-write list). This solution has many drawbacks : it depends on implementation detail, it does handle loop and async well and it just send a text instead of the full exception object. But it works and helped me understand a bit better how Blazor and mono wasm work. 
```
# 如何在 Asp.net 核心中通过自定义绑定器从主体绑定模型值

> 原文：<https://dev.to/musmanrafiq/how-to-bind-model-value-frombody-by-custom-binder-in-asp-net-core-32ik>

几天前，我遇到了一个场景，要求我使用定制模型绑定器来转换我在 FromBody Json 请求中收到的值。当我实现模型绑定器时，它可以很好地处理 FromQuery 属性，但不能处理 FromBody，同样的问题还有:[https://github.com/aspnet/Mvc/issues/8110](https://github.com/aspnet/Mvc/issues/8110)。因此，在这个问题上花了几个小时之后，我知道了 FromBody 属性将使用 BodyModelBinder 类以及 uses Formatters 和 ReaderFactory 来处理。

公共类 MyModelBinder:IModelBinder
{
私有 BodyModelBinder defaultBinder
public mymodel binder(IList formatters，IHttpRequestStreamReaderFactory reader factory)
{
default binder = new body model binder(formatters，reader factory)；
}

```
 public async Task BindModelAsync(ModelBindingContext bindingContext)
  {
      // calling the default body binder
      await defaultBinder.BindModelAsync(bindingContext);
      if (bindingContext.Result.IsModelSet && bindingContext.Result.Model is ResetPasswordModel)
      {
          var inputModel = (ResetPasswordModel)bindingContext.Result.Model;

          // all of your property updates with be listed here
          //
          //
          //

          bindingContext.Result = ModelBindingResult.Success(inputModel);
      }
  } 
```

Enter fullscreen mode Exit fullscreen mode

}
对于这个模型绑定器，我们需要一个模型提供者，它将像:

公共类 MyModelBinderProvider:IModelBinderProvider
{
私有只读 IList _ formatters
私有只读 IHttpRequestStreamReaderFactory _ reader factory；
私有 BodyModelBinderProvider _ default provider；

```
 public MyModelBinderProvider(IList<IInputFormatter> formatters, IHttpRequestStreamReaderFactory readerFactory)
   {
       _formatters = formatters;
       _readerFactory = readerFactory;
       _defaultProvider = new BodyModelBinderProvider(formatters, readerFactory);
   }

   public IModelBinder GetBinder(ModelBinderProviderContext context)
   {
       IModelBinder modelBinder = _defaultProvider.GetBinder(context);

       // default provider returns null when there is error.So for not null setting our binder
       if (modelBinder != null)
       {
           modelBinder = new ProtectedModelBinder(_formatters, _readerFactory);
       }
       return modelBinder;
   } 
```

Enter fullscreen mode Exit fullscreen mode

}
我们需要在 ConfigureServices 方法内部的启动类中注册这个 provide，例如:

服务。add MVC(config = >
{
var reader factory = services。BuildServiceProvider()。GetRequiredService()；
配置。ModelBinderProviders.Insert(0，new MyModelBinderProvider(config。InputFormatters，reader factory))；

})。setcompatibility version(compatibility version。版本 _ 2 _ 2)；
最后用下面一行注释你想要定制绑定的模型:

[model binder(binder type = type of(MyModelBinder))]
公共类 ResetPasswordModel:user confirmation model
现在，如果您使用请求体中的模型属性对内容类型 application/json 进行 http post api 调用，那么您的自定义模型绑定将会工作。

你可以在我们的 github 仓库中找到所有有用的代码和示例:[https://github.com/Usman-uzi/techintalk](https://github.com/Usman-uzi/techintalk)

你可以访问我所有的博客文章:https:[www.techintalk.com](http://www.techintalk.com)
# 使用编辑表单验证您的 Blazor 表单

> 原文：<https://dev.to/rembou1/validate-your-blazor-form-using-the-editform-37pm>

# 使用编辑表单验证您的 Blazor 表单

Blazor 服务器端将与 ASPNET Core 3 一起发布，在这个版本中，ASPNET 团队致力于实现表单验证，因此任何人都可以实现自己的验证逻辑，框架将负责剩下的工作:阻止表单 subit、添加/删除 css 类、显示错误消息等...他们还为用 DataAnnotation 属性注释的类实现了第一个验证器。

这项工作的大部分是由这个在 6 周前被合并的 PR[https://github.com/aspnet/AspNetCore/pull/7614](https://github.com/aspnet/AspNetCore/pull/7614)执行的。这段代码一个月前在 Blazor 0.9 上发布。

我已经写了自己的表单验证逻辑,但是他们的解决方案更好，因为它需要更少的管道:你只需添加一次模型引用(在表单级别),然后所有的子组件都会通过 EditContext 知道它。

在这篇博文中，我们将探索他们的工作，如何使用它以及如何定制它。

## Blazor 表单验证组件

表单验证主要是在名称空间“Microsoft”上实现的。AspNetCore.Components.Forms "源代码位于[这里](https://github.com/aspnet/AspNetCore/tree/master/src/Components/Components/src/Forms)(组件在 3.0 版本之前会重新命名回 Blazor)。
我想，你应该了解的主要阶层有:

*   [微软。AspNetCore . components . forms . edit context](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContext.cs):这个类对一个模型实例的验证信息(验证器、消息、字段)进行分组。这是您的自定义验证的集成点，通过订阅它的事件，您可以执行您自己的验证逻辑并将您的错误发送到 GUI。
*   [微软。AspNetCore . components . forms . edit form](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditForm.cs):这个组件是一个 html 表单标签，它将为一个给定的模型实例实例化 EditContext，它还提供了仅当验证成功时提交表单或者当验证失败时处理的事件。
*   [微软。AspNetCore . components . forms . validationmessagestore](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/ValidationMessageStore.cs):这个类用于向 EditContext 添加关于字段的错误。

下面是验证的执行方式:

*   EditForm 用您给它的模型实例实例化 EditContext。
*   服务由您或某些框架组件创建，并侦听 EditContext 事件，它们必须创建 ValidationMessageStore 以使错误对 EditContext 可用。
*   提交表单时，EditForm 调用 EditContext 上的 Validate
*   EditContext 以自身作为参数触发 ValidationRequested 事件
*   正在侦听此实例事件的每个服务都将进行验证工作，并将错误推送到消息存储。
*   当您将错误推送到消息存储库时，它会在 EditContext 上创建一个字段引用，将自己链接到该字段(内部类 FieldState ),并将错误消息存储在映射上。
*   当每个侦听器完成其工作后，EditContext 浏览所有字段状态，并检查是否有任何错误。如果有一个错误，就不会调用提交回调。

我不知道我在这里是否足够清楚，我希望在这篇文章结束时会更清楚。

## 验证您的表单

这是通过数据注释属性
验证的登记表

```
 <EditForm  OnValidSubmit="CreateAccount" Model="@registerCommand">
            <DataAnnotationsValidator />
            <div asp-validation-summary="All" class="text-danger"></div>
            <div class="form-group row mb-1">
                <label class="col-sm-3 col-form-label" for="NewEmail">Email</label> 
                <div class="col-sm-9">
                    <InputText Class="form-control" bind-Value="@registerCommand.Email" />
                    <ValidationMessage For="@(() => registerCommand.Email)"/>
                </div>

            </div>
            <div class="form-group row mb-1">
                <label class="col-sm-3 col-form-label" for="NewName">Name</label> 
                <div class="col-sm-9">
                    <InputText Class="form-control" bind-Value="@registerCommand.Name" />
                     <ValidationMessage For="@(() => registerCommand.Name)" />
                </div>
            </div>
            <div class="form-group row mb-1">
                <label class="col-sm-3 col-form-label" for="NewPassword">Password</label> 
                <div class="col-sm-9">
                    <InputPassword Class="form-control" bind-Value="@registerCommand.Password" />
                     <ValidationMessage For="@(() => registerCommand.Password)" />
                </div>
            </div>
            <div class="form-group row mb-1">
                <label class="col-sm-3 col-form-label" for="NewConfirmPassword">Confirm</label> 
                <div class="col-sm-9">
                    <InputPassword Class="form-control" bind-Value="@registerCommand.ConfirmPassword" />
                    <ValidationMessage For="@(() => registerCommand.ConfirmPassword)" />
                </div>
            </div>
            <div class="form-group text-center mb-0">
                <button type="submit" ref="createButton" id="BtnRegister" class="btn btn-primary">Register</button> 
            </div>  

        </EditForm> 
```

*   我使用 EditorForm 而不是普通的 hmtl 表单，它将提供所有的验证逻辑和所需的服务
*   InputText 用于将输入绑定到验证逻辑，该逻辑将在编辑值时执行。如果字段无效，将添加“无效”css 类，如果字段无效，将添加“有效”CSS 类。
*   ValidationMessage 用类“validation-message”在 div 中显示给定字段的错误消息。如果你想在同一个地方显示你所有的信息，你还需要一个 ValidationSUmmary。
*   我在处理 CompareAttribute 的方式上发现了一个 bug，我将尝试修复这个 bug 并发送一个 PR。
*   InputPassword 是我自己的，因为 ASPNET 团队决定通过内置组件只提供一组有限的输入属性。这不是一个大问题，因为创建这个组件就像这样简单:

```
@inherits InputBase<string>
<input bind="@CurrentValue" type="password" id="@Id" class="@CssClass" /> 
@functions{
        protected override bool TryParseValueFromString(string value, out string result, out string validationErrorMessage)
        {
            result = value;
            validationErrorMessage = null;
            return true;
        }
} 
```

也许当类似 Angular decorator 的东西在 Blazor 中可用时，它会更简单，但迄今为止，这不是什么大问题。

我还添加了下面的 css 来对错误应用引导样式

```
 .form-control.invalid{
    border-color:#dc3545;
}
.form-control.valid{
    border-color:#28a745;
}
.validation-message {
    width: 100%;
    margin-top: .25rem;
    font-size: 80%;
    color: #dc3545;
} 
```

现在，当提交表单或更改输入值时，字段是红色的，错误消息显示如下

[![Form validation](img/37d5061c3e76cf8b5de99ee12225dc6a.png)](/assets/img/FormValidation.png)

## 显示来自服务器的验证错误

就我个人而言，我不喜欢用验证属性来处理关于全局状态的验证(比如电子邮件的唯一性)，我更喜欢在我的命令处理程序中显式地处理它。所以我的服务器可能会返回验证错误。为了从服务器返回这些错误，我简单地构建了一个字典<string>>，其中键是字段名，值是来自服务器的错误消息，并返回一个错误请求(400) Http 状态。你可以在这里查看我的项目，看看我是如何在服务器端完成的:[https://github.com/RemiBou/Toss.Blazor](https://github.com/RemiBou/Toss.Blazor)。</string>

在客户端，我首先必须将我的自定义 valdiator 插入到 EditContext 中。这是我的验证器:

```
public class ServerSideValidator : ComponentBase
{
    private ValidationMessageStore _messageStore;

    [CascadingParameter] EditContext CurrentEditContext { get; set; }

    /// <inheritdoc />
    protected override void OnInit()
    {
        if (CurrentEditContext == null)
        {
            throw new InvalidOperationException($"{nameof(ServerSideValidator)} requires a cascading " +
                $"parameter of type {nameof(EditContext)}. For example, you can use {nameof(ServerSideValidator)} " +
                $"inside an {nameof(EditForm)}.");
        }

        _messageStore = new ValidationMessageStore(CurrentEditContext);
        CurrentEditContext.OnValidationRequested += (s, e) => _messageStore.Clear();
        CurrentEditContext.OnFieldChanged += (s, e) => _messageStore.Clear(e.FieldIdentifier);
    }

    public void DisplayErrors(Dictionary<string, List<string>> errors)
    {
        foreach (var err in errors)
        {
            _messageStore.AddRange(CurrentEditContext.Field(err.Key), err.Value);
        }        
        CurrentEditContext.NotifyValidationStateChanged();
    }
} 
```

*   它深受 DataAnnotationValidator 的启发
*   它是一个组件，因为它必须被插入到组件层次结构中，以便从表单中获取级联 EditContext
*   如前所述，我必须创建一个 ValidationMessageStore 来将错误推送到上下文中
*   当编辑一个字段时，我会清除错误，这样用户就可以重试另一个值

为了使用它，我必须在我的表单下添加这个组件，就像这样

```
 <EditForm  OnValidSubmit="CreateAccount" Model="@registerCommand" ref="registerForm">
            <DataAnnotationsValidator />
            <ServerSideValidator ref="serverSideValidator"/>
            ....
        </EditForm>
        ...

    @functions{
        RegisterCommand registerCommand = new RegisterCommand();
        ServerSideValidator serverSideValidator;
        async Task CreateAccount(EditContext context)
        {
            await ClientFactory.Create("/api/account/register", createButton)
                .OnBadRequest<Dictionary<string, List<string>>>(errors => {

                    serverSideValidator.DisplayErrors(errors);
                })
                .OnOK(async () =>
                {
                    await JsInterop.Toastr("success", "Successfully registered, please confirm your account by clicking on the link in the email sent to " + registerCommand.Email);
                    registerCommand = new RegisterCommand();
                    StateHasChanged();
                })
                .Post(registerCommand);
        }
    } 
```

*   我使用“ref”关键字直接与验证器交互
*   服务器推送的字段名必须与命令的字段名匹配
*   这样，如果用户名或电子邮件不是唯一的，消息将显示在 good 字段下。

## 结论

我不知道这是不是做类似事情的最好方法，但它确实有效，而且并不复杂。ASPNET 团队采用的方法非常好，因为您不必实现适配器接口，只需监听您想要使用的事件。他们走的是模型验证的道路，而不是 angular 团队采取的被动形式，这在我看来要好得多。

与第一篇博文一样，这里的好处是我不必实现两次验证机制:我只需将我的数据注释属性添加到我的命令/查询类中，它们将在客户机和服务器上得到验证。

下一步可能是使用在双方都能工作的服务来实现定制验证器。
# 发送电子邮件的混乱方式。NET Core 和其他好东西

> 原文：<https://dev.to/coolgoose/the-confusing-way-of-sending-emails-in-net-core-and-other-goodies-3hdd>

自从我终于有时间玩 C#以来，我遇到了下一个挑战，当我想用确认邮件实现用户注册时。

尽管[官方文档](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/accconfirm?view=aspnetcore-2.2&tabs=visual-studio)清楚地说明了如何处理一次性代码生成，但我不想使用 Sendgrid，而是简单的老式 SMTP 认证。此外，我不同意在我的注册任务中将邮件正文作为一个字符串。

这就是事情变得相当复杂的地方。
教程说包含[系统。Net.Mail](https://docs.microsoft.com/en-us/dotnet/api/system.net.mail?view=netcore-2.2) ，它有一个 [SmtpClient](https://docs.microsoft.com/en-us/dotnet/api/system.net.mail.smtpclient?view=netcore-2.2) ，但是不幸的是这个 API 已经过时了。

并不是所有的东西都丢失了，因为它确实推荐使用 Mailkit，但是由于 MailKit 不处理任何类型的模板管理，我开始在网上搜索人们如何发送电子邮件。

我想大声喊出 [FluentEmail](https://github.com/lukencode/FluentEmail) ，因为它几乎是我想要的(除了 MailKit 处理程序)。

不用多说，你可以在这个 [github 提交](https://github.com/rheoli-io/craidd/tree/dc6c974fb398cd9d576e3c6acf6c9121b26794fc)中访问当前版本，或者直接检查[提交](https://github.com/rheoli-io/craidd/commit/dc6c974fb398cd9d576e3c6acf6c9121b26794fc)

我使用了[选项模式](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-2.2)来简化依赖注入处理， [RazorLight](https://github.com/toddams/RazorLight) 用于模板处理(也许微软的某个人可以将官方的东西提取为一个库)，并在我的`EmailsService`中实现了一个简单的`sendEmailFromTemplateAsync`方法。

我不得不绕开 RazorLight 的 CompileRenderAsync，因为我向`sendEmailFromTemplateAsync`发送了一个匿名对象，并在`CompileRenderAsync`方法中将其作为**模型**直接传递，不知何故破坏了变量解析。
解决方法是将 **messageData** 作为一个*字典*，并使用*expand object*
将其转换成一个对象

```
var localMessageData = messageData.Aggregate(
    new ExpandoObject() as IDictionary<string, Object>,
    (a, p) => { a.Add(p.Key, p.Value); return a; }
); 
```

Enter fullscreen mode Exit fullscreen mode

你们是如何在申请中处理电子邮件的？
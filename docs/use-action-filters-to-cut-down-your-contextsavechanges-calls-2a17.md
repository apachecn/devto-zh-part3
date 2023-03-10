# 使用动作过滤器来减少你的上下文。保存更改呼叫

> 原文：<https://dev.to/jonhilt/use-action-filters-to-cut-down-your-contextsavechanges-calls-2a17>

如果你使用实体框架核心，你可能会发现自己一遍又一遍地写这样的东西...

```
context.SaveChangesAsync(); 
```

其中上下文是指您的实体框架数据库上下文的实例。

这引发了一些问题。

首先，它太容易忘记，结果花了几分钟(甚至几个小时！)找出您的更改未被保存的原因。

其次，它无处不在，增加了代码库中“基础设施”代码的总量，而这实际上并不是关键业务逻辑的一部分。

第三(也许是最重要的)，它往往会以不一致的方式结束使用。

因此，与在一个地方逻辑地保存所有内容不同，您可以很容易地在不同时间和应用程序的不同层进行多次保存。

这可能会使您的应用程序有些不可预测，还会导致不一致的状态。

如果您的代码的一部分失败了，但是其他代码已经将它的状态保存到了数据库中，那么您现在就有了一项工作来确定哪些被保存了，哪些没有被保存。

## ASP.NET 核心行动过滤救援

这里有一个替代方案。

使用一个动作过滤器，当您的任何 MCV 动作方法执行完毕时，总是对`SaveChanges`进行一次调用。

通过这种方式，您可以确保操作要么成功(所有内容都将以可预测的方式持久化)要么失败(所有内容都不会持久化，让用户可以自由地再试一次，或者抱怨！)

```
public class DBSaveChangesFilter : IAsyncActionFilter
{
    private readonly MyContext _dbContext;

    public DBSaveChangesFilter(MyContext dbContext)
    {
        _dbContext = dbContext;
    }

    public async Task OnActionExecutionAsync(
        ActionExecutingContext context,
        ActionExecutionDelegate next) 
    {
        var result = await next();
        if (result.Exception == null || result.ExceptionHandled)
        {
            await _dbContext.SaveChangesAsync();
        }
    }
} 
```

您可以在`startup.cs`中注册它，并忘记在代码中的任何地方调用`SaveChanges()`(而是专注于您的特性！).

```
public void ConfigureServices(IServiceCollection services){

    // rest of code omitted

    services
        .AddMvc(opt => opt.Filters.Add(typeof(DBSaveChangesFilter)))
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
} 
```

这为您提供了一个方便的“每个动作一个事务”，有助于避免您的数据库进入不一致的状态。

[想先拿到这些文章？点击此处，让我通过电子邮件将它们发送给您:-)](https://jonhilton.net/devto/next-react-post/)
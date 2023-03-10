# 如何重命名 Asp？Net Core 2.2 标识表在 EF Core 中没有 AspNet 前缀

> 原文：<https://dev.to/coolgoose/how-to-rename-asp-net-core-22-identity-tables-to-not-have-aspnet-prefix-in-ef-core-33c9>

不确定下面的代码是否应该被认为是黑客，但是删除前缀`AspNet`最简单的方法是遍历模型。

```
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    foreach (var entityType in modelBuilder.Model.GetEntityTypes())
    {
        var table = entityType.Relational().TableName;
        if (table.StartsWith("AspNet")) {
            entityType.Relational().TableName = table.Substring(6);
        }
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

在一个[2014 ASP . net 问题](https://github.com/aspnet/Identity/issues/300)中找到初始代码，由于复制粘贴没有剪切它，所以做了一个方法/属性名称的快速更新。

这也允许您用您想要的任何东西替换它，或者添加一个通用的表前缀。
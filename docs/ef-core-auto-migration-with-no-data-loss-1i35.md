# EF 核心自动迁移，无数据丢失

> 原文：<https://dev.to/akashkava/ef-core-auto-migration-with-no-data-loss-1i35>

EF Core 已经放弃了对自动迁移的支持，这在原型应用中非常有用。EF 核心的迁移支持太费力了。

# EF-Core-Live-Migration 简介

## 特性

1.  创建缺失的表
2.  创建缺失的列
3.  如果列已经存在，并且如果数据类型不同，那么旧的列将被重命名，并且新的列将随着从旧的列传输数据而被创建，传输可能是有损失的，
4.  重命名旧索引并创建新索引
5.  基于外键创建索引

## 安装

```
PM>Install-Package NeroSpeech.EFCoreLiveMigration 
```

## 配置

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env){

    if(env.IsDevelopment()){
        app.UseDeveloperExceptionPage();

        using (var scope = app.ApplicationServices.GetService<IServiceScopeFactory>().CreateScope())
        {
            using (var db = scope.ServiceProvider.GetRequiredService<AppModelContext>())
            {
                MigrationHelper.ForSqlServer(db).Migrate();
            }
        }
    }

} 
```

## 局限性

目前仅适用于 SQL Server。

## 旧名称属性

如果您决定重命名一个列，您可以用`[OldName("name")]`标记该属性，这样在迁移时，如果没有数据丢失，它将重命名现有的列。如果数据丢失，现有列将被保存为其他列，数据将被转移到新列。

## 求援

该 API 是在没有后来在 EF Core 中引入的迁移 API 的情况下编写的。我需要用迁移 API 重写 API 的帮助，以便它可以用于任何后端。
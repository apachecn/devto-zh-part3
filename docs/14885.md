# 管理 CosmosDB 对象(存储过程、函数、触发器...)使用这个 Nuget 包

> 原文：<https://dev.to/rembou1/manage-cosmosdb-objects-stored-procedure-functions-triggers--with-this-nuget-package-5gpk>

# 管理 CosmosDB 对象(存储过程、函数、触发器...)使用这个 Nuget 包

在我的 Toss 项目中，我决定使用 CosmosDB 作为主要的数据存储。与关系型数据库相比，面向文档的数据库很有趣，因为你必须改变你看待数据和处理的方式。NoSQL 数据库经常被错误地框定为“无模式的”，[但是并不存在无模式的](https://martinfowler.com/articles/schemaless/)，模式只是在别处定义的:不是在数据库上，而是在你的应用程序代码上。

对于开发人员来说，这种方法有两个问题:

*   数据是持久的，所以当您更改模式(属性重命名)时，您必须考虑现有的数据
*   需要在某个地方定义一些特定的数据库对象，比如函数和触发器

这就是为什么我创建了这个包，它将帮助你管理你的数据库对象和模式演变，以及你的存储库上的应用程序代码，它将在你需要的时候应用(主要是在应用程序启动时)。

## 阅读他嵌入的资源

我选择在程序集中使用嵌入的 js 文件来定义对象定义:

*   因为它们是在 javascript 中定义的，所以最好使用。js 文件，IDE 将帮助开发人员
*   如果它是嵌入的，那么包用户就不必考虑保护包含定义的文件夹

在 C# / netstandard :
中读取嵌入式资源相当简单

```
//read all the migration embbeded in CosmosDB/Migrations
var ressources = migrationAssembly.GetManifestResourceNames()
    .Where(r => r.Contains(".CosmosDB.Migrations.") && r.EndsWith(".js"))
    .OrderBy(r => r)
    .ToList();
//for each migration
foreach (var migration in ressources)
{
    string migrationContent;
    using (var stream = migrationAssembly.GetManifestResourceStream(migration))
    {
        using (var reader = new StreamReader(stream))
        {
            migrationContent = await reader.ReadToEndAsync();
        }
    }
    // do something
} 
```

*   migrationAssembly 作为我的方法的参数发送，因此用户可以将其迁移添加到应用程序资源或库资源中。
*   我在这里并没有真正考虑性能，因为这段代码应该在每个应用程序生命周期中只运行一次，所以我更喜欢保持清晰。

## 应用迁移

我决定实现策略模式:对于每种类型的对象都有一个策略，所以用户将能够实现他们自己的策略。

这段代码延续了前面代码示例中的“//做点什么:

```
var parsedMigration = new ParsedMigrationName(migration);
var strategy = strategies.FirstOrDefault(s => s.Handle(parsedMigration));
if (strategy == null)
{
    throw new InvalidOperationException(string.Format("No strategy found for migration '{0}", migration));
}

await client.CreateDatabaseIfNotExistsAsync(parsedMigration.DataBase);
if (parsedMigration.Collection != null)
{
    await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri(parsedMigration.DataBase.Id), parsedMigration.Collection);
}

await strategy.ApplyMigrationAsync(client, parsedMigration, migrationContent); 
```

*   解析迁移是检查和读取资源名称的类。每个资源都必须遵守记录的约定:位于“cosmos db/Migrations/TestDataBase/stored procedure/”中的“Test.js”文件是位于数据库“Test database”和集合中的名为“Test”的存储过程的内容
*   在这里，我还创建了所需的数据库和集合

## 策略实施

对于我想要处理的每一种对象，我必须创建一个策略的实现，这里是触发器的实现:

```
internal class TriggerMigrationStrategy : IMigrationStrategy
{
    public async Task ApplyMigrationAsync(IDocumentClient client, ParsedMigrationName migration, string content)
    {
        var nameSplit = migration.Name.Split('-');

        Trigger trigger = new Trigger()
        {

            Body = content,
            Id = nameSplit[2],
            TriggerOperation = (TriggerOperation)Enum.Parse(typeof(TriggerOperation), nameSplit[1]),
            TriggerType = (TriggerType)Enum.Parse(typeof(TriggerType), nameSplit[0])
        };
        await client.UpsertTriggerAsync(
                        UriFactory.CreateDocumentCollectionUri(migration.DataBase.Id, migration.Collection.Id),
                        trigger);
    }

    public bool Handle(ParsedMigrationName migration)
    {
        return migration.Type == "Trigger";
    }
} 
```

*   对于触发器，除了名称之外，我还需要更多的信息:类型和操作，所以我创建了另一个约定来设置文件名:{Type}-{Operation}-{Name}

## 使用库

要使用该库，您需要:

*   安装软件包“Install-Package RemiBou。cosmos db . Migration-Version 0 . 0 . 0-CI-2019 01 26-145359 "(这仍然是一个预发布版本，我将在以后尝试更好的版本化策略)。
*   创建文件夹“CosmosDB”和“CosmosDB/Migrations”
*   将“”添加到您的项目 csproj
*   按照惯例添加您的迁移
*   当您想要运行迁移时(我猜是在应用程序启动时)添加这段代码。)

```
await new CosmosDBMigration(documentClient).MigrateAsync(this.GetType().Assembly);// add .Wait() if your are in a not in an async context like the Startup 
```

*   运行您的应用

我稍后会尝试在安装软件包时自动执行第 2 步和第 3 步。

## 结论

这个包的构建和设计很有趣，我想我得到了一些好东西。现在我有许多功能要做[阅读这里的待办事项列表](https://github.com/RemiBou/RemiBou.CosmosDB.Migration)，但在此之前，我必须设置测试套件，这样用户就可以放心，软件包是稳定的。
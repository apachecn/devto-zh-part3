# 让 DynamoDB 更易于开发

> 原文：<https://dev.to/hussein_zawawi/make-dynamodb-easier-for-dev-34mb>

### 开发中的 DynamoDb:

使用 DynamoDb API 涉及大量样板代码和定义表的代码复制。在开发周期的早期阶段，您会期望对表格设计进行大量破坏性的修改。因此，仅仅依赖 API 可能会开始变得痛苦。这可能会减缓开发周期，并围绕代码可维护性迅速增加技术债务。

介绍 *DynamoDbProvisioner* ，这是一个旨在开发时隐藏所有这些复杂性的工具。这将有助于开发人员将更多的精力放在表格设计上，而不必担心任何无关的细节，因为这些细节会耗费大量的开发时间。

*DynamoDbProvisioner* 将负责在应用程序启动时初始化所有已定义的表，并能够在需要时删除/重新创建表。

### dynamodb provisioner 是如何工作的？

provisioner 是针对一组程序集注册的，在运行时，provisioner 将遍历程序集中的所有类型，并查找由 *DynamoDBTableAttribute* 修饰的任何类。知道了这一点，只需针对主程序集注册 provisioner 就足够了，您不必担心为添加的任何新表添加任何配置。

provisioner 依赖于一个表示 DynamoDb 表的类，该类由一些属性修饰，这些属性将为 dynamo db API 提供创建该表所需的信息。

其中一些属性如下:

*   *DynamoDBTableAttribute* 这将映射到表名
*   这将指定以下哪个模型属性将代表表的散列值
*   *DynamoDBRangeKeyAttribute*这将指定以下哪个模型属性将代表表的 rangeKey
*   这将指定以下哪个模型属性将代表全局二级索引的散列值
*   *dynamodbglobalsecondaryindexrangekey attribute*这将指定以下哪个模型属性将代表全局二级索引的 rangeKey
*   这将指定以下哪个模型属性将代表本地二级索引的 rangeKey
*   *DynamoDBTimeToLiveAttribute*这将指定以下哪个模型属性将存储项目的到期时间

### dynamo db 表的例子

```
[DynamoDBTable(nameof(TableWithStringHashKeyAndNumberRangeKey))]
public class TableWithStringHashKeyAndNumberRangeKey
{
  [DynamoDBHashKey]
  public string Id { get; set; }

  [DynamoDBRangeKey]
  public int Timestamp { get; set; }
} 
```

```
[DynamoDBTable(nameof(TableWithGlobalSecondaryIndexHashKeyAndRangeKey))]
public class TableWithGlobalSecondaryIndexHashKeyAndRangeKey
{
  [DynamoIndex(ProjectionType = ProjectionType.All)] public const string ByLastNameAndFirstNameIndex = "ByLastNameAndFirstName-Index";

  [DynamoDBHashKey]
  public string Id { get; set; }

  [DynamoDBGlobalSecondaryIndexHashKey(ByLastNameAndFirstNameIndex)]
  public string LastName { get; set; }

  [DynamoDBGlobalSecondaryIndexRangeKey(ByLastNameAndFirstNameIndex)]
  public string FirstName { get; set; }
} 
```

### 集成测试中的 DynamoDbProvisioner

DynamoDBProvisioner 的一个很好的用途是在测试中，在测试中您需要执行一些 dynamoDb 操作，并且您不想在运行测试时过多地担心创建/清理表。
provisioner 使您能够在初始化期间传入一个表前缀。在测试中，你需要一些随机的前缀，比如 GUID。这将使您能够对同一个表的多个副本并行运行许多测试。

### 本地 DynamoDb，不在云端

除了置备程序之外，我还推荐使用下面列出的应用程序之一。这些将为您提供一个与 dynamodb 交互的完整环境，而不需要在云中提供任何表。这将削减一些成本，使开发/测试更快更容易，并且不依赖于任何外部网络。

*   [本地堆栈](https://github.com/localstack/localstack)
*   动模块

### Nuget 包

可以从 [Pushpay 下载并参考 nuget 包。电动发电机供应器](https://www.nuget.org/packages?q=Pushpay.DynamoDbProvisioner)

### 源代码

你可以在 [github](https://github.com/pushpay-labs/dynamodb-provisioner) 上找到上面的代码片段
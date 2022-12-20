# 实体框架中 nvarchar 和 varchar 的奇特情况

> 原文：<https://dev.to/kylegalbraith/the-curious-case-of-nvarchar-and-varchar-in-entity-framework-9nj>

我最近在做一个项目，该项目使用 SQL Server 作为主要数据库。这不是我第一次使用 SQL Server，事实上，我还有一个项目大量使用了。网络堆栈。但是，在这个项目中，我遇到了一个主要的性能问题，这个问题不是很清楚，也很容易被忽略。

所以本着在公众面前学习的精神，我想我应该写下我在这个问题上的经历。我们可以深入问题和解决方案，以便您可以在未来避免这种疤痕组织。

### 项目

首先，让我们通过了解项目的一些背景来为问题做准备。出于这篇博文的目的，我将使用一个反映实际项目的例子。

该项目是一个使用`dotnet core`构建的 web API。API 中的主端点接收一个 id 数组，并在数据库的一个表中检查这些 id。就 API 而言，这一个相当简单。

在开发这个 API 时，我们可以访问数据库，但不能访问原始模式。这不成问题，因为我们首先利用实体框架代码来表示我们想要查询的表。我们查看了真实数据库上的模式，并将这些列及其类型镜像到我们的实体框架模型中。

这是那个模型在初始阶段后的样子。

```
namespace my_api.Data
{
    public class FieldTable
    {
        public Int64 Id { get; set; }
        public string FieldId { get; set; }
        public string Description { get; set; }
        public DateTime DateAdded { get; set; }
        public bool Available { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`FieldId`是我们在 API 中查询的列，以查看传入的 id 数组是否匹配数据库中的任何`FieldId`值。匹配的返回给客户。下面是实现这一点的 API 逻辑代码。

```
public IEnumerable<MatchedField> GetMatches(IEnumerable<string> fieldIds)
{
    return _dataContext.FieldTable.Where(f => fieldIds.Contains(f.FieldId))
        .Select(f =>
            new MatchedField()
            {
                FirstSeen = f.DateAdded,
                FieldId = f.FieldId.ToLower(),
                Available = f.Known
            }
        ).ToList();
} 
```

Enter fullscreen mode Exit fullscreen mode

### 实体框架到原始 SQL

在高层次上，实体框架将把我们上面的代码映射到一个原始的 SQL 查询中，如下所示。

```
select DateAdded, FieldId, Known
from dbo.FieldData
where FieldId in (N'1', N'2', N'3', N'4', N'etc') 
```

Enter fullscreen mode Exit fullscreen mode

这个查询看起来很简单，对吗？它在表上做了一个相当简单的查找。但是它实际上做的不止这些，注意每个 id 值前面的`N`字符。

SQL 中的这个字符将该字符串的类型声明为`nvarchar`。如果您不熟悉`nvarchar`类型，它允许您在列中存储任何 Unicode 值。这不同于只允许你存储 ASCII 码的`varchar`数据类型。

这就是`nvarchar`和`varchar`之间的差异变得重要的地方。当我们在上面定义我们的`FieldTable`类时，我们没有指定表上列的 SQL 类型。这意味着什么呢？这意味着实体框架将使用它的默认 SQL 类型。NET 字符串默认类型是`nvarchar`。

这就是为什么我们会在原始 SQL 查询所查找的每个值前面看到`N`字符。

有问题吗？如果我们表中的列是类型`nvarchar`就不会。实体框架在查询中发送`nvarchar`id，我们的列有那个类型，所以一切都很好。

但是如果列的类型是`varchar`呢？

如果`FieldId`实际上是类型`varchar`，但是实体框架发送了一组`nvarchar`id，现在我们有一个类型不匹配。当这种情况发生时，现在必须在查询时进行转换。

这是一个微妙的细微差别，往往会被忽视。但是，如果我们的查询要查找数百个值，性能影响会非常大。

### 吸取教训

当查找一个`FieldId`值时，这种细微的差别并不那么明显。它看起来比应该慢了一点，但总体来说还不错。

但是，查询 500 个`FieldId`值根本没有性能，大约需要 45-60 秒。这导致了上述调查。查看框架生成的原始查询实体，我们看到 id 值以`N'1', N'2', N'3'`为前缀。我们假设这些列实际上是`nvarchar`，所以这不应该是性能瓶颈。

但是，随后我们运行了相同的查询，但是我们没有在 id 值前面加上前缀`N`，而是查找普通的`varchar`字符串。

```
select DateAdded, FieldId, Known
from dbo.FieldData
where FieldId in ('1', '2', '3', '4', 'etc') 
```

Enter fullscreen mode Exit fullscreen mode

结果不到 500 毫秒就出来了。

查看`FieldId`列，我们能够确认它实际上是类型`varchar`而不是`nvarchar`。✅.发现性能瓶颈

这不是实体框架的错，甚至不是数据库的错。这是我们创建的数据模型中的一个小错误，很容易被忽略。当我们在代码中定义表`FieldTable`时，我们指定`FieldId`的类型为 string。

```
namespace my_api.Data
{
    public class FieldTable
    {
        public Int64 Id { get; set; }
        public string FieldId { get; set; }
        public string Description { get; set; }
        public DateTime DateAdded { get; set; }
        public bool Available { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

到了 EF 查询那个表的时候，它做了它最擅长的事情，把你的代码翻译成 SQL 查询。但是，它为每个 id 在`WHERE IN`子句中添加了`N`前缀。它在假设`FieldId`列是类型`nvarchar`的情况下运行。这是因为`nvarchar`是。实体框架中的. NET 类型`string`。

```
select DateAdded, FieldId, Known
from dbo.FieldData
where FieldId in (N'1', N'2', N'3', N'4', N'etc') 
```

Enter fullscreen mode Exit fullscreen mode

巴达兵，重大性能问题。但这是为什么呢？因为现在 SQL Server 必须将查询中声明为`nvarchar`的每个 id 转换为`varchar`类型来查询列。将 500 多个 id 转换为 lookup 的成本非常高。

### 必要时予以明确

修复非常简单。我们需要明确为`FieldTable`定义的属性。这意味着向每个属性添加一个属性，告诉实体框架它所代表的确切 SQL 数据类型。

```
namespace my_api.Data
{
    public class FieldTable
    {
        public Int64 Id { get; set; }
        [Column(TypeName="varchar(50)")]
        public string FieldId { get; set; }
        [Column(TypeName="varchar(500)")]
        public string Description { get; set; }
        public DateTime DateAdded { get; set; }
        public bool Available { get; set; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

更改是将`[Column(TypeName="varchar(50)")]`添加到`FieldId`属性中。这告诉实体框架该列的确切 SQL 数据类型。通过这样做，实体框架现在生成适当的 SQL 查询，一个没有`nvarchar`字符串的查询。

```
select DateAdded, FieldId, Known
from dbo.FieldData
where FieldId in ('1', '2', '3', '4', 'etc') 
```

Enter fullscreen mode Exit fullscreen mode

结果呢？一次查找 500 多个 id 现在可以在 200-400 毫秒内完成，而不是 45-60 秒。

### 结论

构建解决方案时，如果您无法访问所有的部分，会很有挑战性。如我们所见，将模式手动复制到您自己的代码中是容易出错的。

像 Entity Framework 这样的 ORM 非常擅长隐藏数据库访问的复杂性。大多数时候这是我们想要的。然而，正如我们所看到的，有时隐藏会引入容易被忽略的细微差别。需要时使用 ORM，但要确保你在他们可能做出或不做出的隐含决策中有坚实的基础。

### 您是否渴望了解更多关于亚马逊网络服务的信息？

我的整个职业生涯几乎都在 AWS 生态系统中工作。但即使是我，有时也会发现学习新的 AWS 服务令人望而生畏。这就是为什么我创建了一个课程，教你如何像我一样通过实际使用来掌握 AWS。

如果你想开始你的 AWS 之旅，但却不知道从哪里开始，可以考虑查看我的课程。我们专注于在 AWS 上托管、保护和部署静态网站。让我们在使用时能够了解超过 6 种不同的 AWS 服务。在你掌握了基础知识之后，我们可以进入**的两个额外章节**来讨论更高级的主题，比如基础设施代码和持续部署。
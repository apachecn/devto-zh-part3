# 35 LINQ 面试问答 2019 刷新

> 原文：<https://dev.to/aershov24/35-linq-interview-questions-and-answers-to-refresh-for-2019-1dng>

谈到查询数据库，LINQ 在大多数情况下是一种比 SQL 更高效的查询语言。LINQ 与 C#(或 VB)集成，从而消除了编程语言和数据库之间的阻抗不匹配，并为大量数据源提供了单一的查询接口。

> 最初发表于 [FullStack。永远不要再错过你的技术面试](https://www.fullstack.cafe)

### Q1:LINQ 是什么？

> 题目:**linq**T2】难度:⭐

**LINQ** 代表语言综合查询。LINQ 允许我们编写对本地集合对象和远程数据源的查询，如 SQL、XML 文档等。我们可以在任何实现了`IEnumerable`接口的集合类上编写 LINQ 查询。

🔗**来源:**【stackoverflow.com】T2

### Q2:LINQ 有哪些类型？

> 题目:**linq**T2】难度:⭐

*   LINQ 对物体
*   LINQ 到 XML
*   LINQ 到数据集
*   LINQ 到 SQL
*   LINQ 至实体

🔗**来源:**【career.guru99.com】T2

### Q3:解释什么是 LINQ？为什么需要？

> 题目:**linq**T2】难度:⭐

**语言集成查询**或 **LINQ** 是标准查询操作符的集合，提供查询设施 into.NET 框架语言如 C#，VB.NET。LINQ 是必需的，因为它在数据世界和对象世界之间架起了一座桥梁。

🔗**来源:**【career.guru99.com】T2

### Q4:列出 LINQ 的三个主要成分？

> 题目:**linq**T2】难度:⭐⭐

LINQ 的三个主要组成部分是

*   标准查询运算符
*   语言扩展
*   LINQ 供应商

🔗**来源:**【career.guru99.com】T2

### Q5:什么是匿名功能？

> 题目:**linq**T2】难度:⭐⭐

匿名函数是一种没有名字的特殊函数。我们只需定义它们的参数，并将代码定义在花括号中。

考虑:

```
delegate int func(int a, int b);
static void Main(string[] args)
{
    func f1 = delegate(int a, int b)
    {
        return a + b;
    };

    Console.WriteLine(f1(1, 2));
} 
```

🔗**来源:**【stackoverflow.com】T2

### Q6:什么是匿名类型？

> 题目:**linq**T2】难度:⭐⭐

匿名类型是编译器在运行时生成的类型。当我们创建匿名类型时，我们不指定名称。我们只写属性名和它们的值。编译器在运行时创建这些属性并给它们赋值。

```
var k = new { FirstProperty = "value1", SecondProperty = "value2" };
Console.WriteLine(k.FirstProperty); 
```

匿名类在 LINQ 查询中很有用，可以保存我们的中间结果。

匿名类型也有一些限制:

*   匿名类型不能实现接口。
*   匿名类型不能指定任何方法。
*   我们不能定义静态成员。
*   所有已定义的属性都必须初始化。
*   我们只能定义公共字段。

🔗**来源:**【stackoverflow.com】T2

### Q7:什么是延伸法？

> 题目:**linq**T2】难度:⭐⭐

**扩展方法**是静态类的静态函数。这些方法可以像实例方法语法一样被调用。当我们不想修改类时，这些方法很有用。考虑:

```
public static class StringMethods
{
    public static bool IsStartWithLetterM(this string s)
    {
        return s.StartsWith("m");
    }
}
class Program
{
    static void Main(string[] args)
    {
        string value = "malslfds";
        Console.WriteLine(value.IsStartWithLetterM()); //print true;

        Console.ReadLine();
    }
} 
```

🔗**来源:**【stackoverflow.com】T2

### Q8:解释什么是“LINQ 至物”？

> 题目:**linq**T2】难度:⭐⭐

当 LINQ 直接查询任何`IEnumerable(<T>)`集合或 IEnumerable 而不使用中间 LINQ 提供者或 API(如 LINQ 到 SQL 或 LINQ 到 XML)时，被称为 **LINQ 到对象**。

🔗**来源:**【career.guru99.com】T2

### 问题 9:解释 LINQ 供应商在 LINQ 的目的是什么？

> 题目:**linq**T2】难度:⭐⭐

LINQ 提供程序是采用 LINQ 查询的一组类，该查询生成对特定数据源执行等效查询的方法。

🔗**来源:**【career.guru99.com】T2

### Q10:提到 DataContext 类在 LINQ 的作用是什么？

> 题目:**linq**T2】难度:⭐⭐

**DataContext** 类充当 SQL Server 数据库和 SQL LINQ 之间的桥梁。为了访问数据库和改变数据库中的数据，它包含连接字符串和函数。实际上，DataContext 类执行以下三项任务:

*   创建到数据库的连接。
*   它向数据库提交和检索对象。
*   将对象转换为 SQL 查询，反之亦然。

🔗**来源:**【career.guru99.com】T2

### Q11:在 LINQ 中，如何使用 where()和 Lambda 表达式找到元素的索引？

> 题目:**linq**T2】难度:⭐⭐

为了找到元素的索引，使用重载版本的`where()`和 lambda 表达式:

```
where(( i, ix ) => i == ix); 
```

🔗**来源:**【career.guru99.com】T2

### Q12:解释一下 LINQ 为什么比存储过程有用？

> 题目:**linq**T2】难度:⭐⭐

*   调试:调试存储过程很困难，但是由于 LINQ 是 of.NET 的一部分，所以可以使用 visual studio 调试器来调试查询
*   **部署:**对于存储过程，应该提供额外的脚本，但是有了 LINQ，一切都被编译成一个 DLL，因此部署变得容易
*   类型安全: LINQ 是类型安全的，所以在编译时会对查询错误进行类型检查

🔗**来源:**【career.guru99.com】T2

### Q13:解释为什么在 LINQ 中 SELECT 子句在 FROM 子句之后？

> 题目:**linq**T2】难度:⭐⭐

与其他编程语言和 C#一样，LINQ 被使用，它要求所有的变量被首先声明。LINQ 查询的“FROM”子句定义了选择记录的范围或条件。因此，在 LINQ，FROM 子句必须出现在 SELECT 之前。

🔗**来源:**【career.guru99.com】T2

### Q14:您能比较一下实体框架和 LINQ 与 SQL 和 ADO.NET 的存储过程吗？

> 题目:**linq**T2】难度:⭐⭐⭐

**存储过程:**

(++)

*   极大的灵活性
*   完全控制 SQL
*   现有的最高性能

( - )

*   需要 SQL 知识
*   存储过程不受源代码控制(更难测试)
*   在指定相同的表名和字段名时，大量的“重复工作”。在重命名一个数据库实体并在某处丢失了对它的一些引用后，破坏应用程序的可能性很大。
*   发展缓慢

**形式(EF、L2SQL、ADO。净:**

(++)

*   迅速发展
*   数据访问代码现在受源代码控制
*   你不会受到 DB 变化的影响。如果发生这种情况，您只需要在一个地方更新您的模型/映射。

( - )

*   性能可能会更差
*   对 ORM 产生的 SQL 没有控制或者控制很少(可能效率低下或者错误百出)。可能需要干预并用自定义存储过程替换它。这将使您的代码变得混乱(一些 LINQ 在代码中，一些 SQL 在代码中和/或在不受源代码控制的数据库中)。
*   因为任何抽象都可能导致“高级”开发人员不知道它是如何工作的

🔗**来源:**【stackoverflow.com】T2

### Q15:当试图在使用实体框架和 LINQ 到 SQL 作为 ORM 之间做出决定时，有什么区别？

> 题目:**linq**T2】难度:⭐⭐⭐

*   **LINQ 到 SQL** 仅支持 Microsoft SQL Server 中可用的数据库表、视图、存储过程和函数的一对一映射。对于设计相对较好的 SQL Server 数据库来说，这是一个用于快速数据访问构造的很好的 API。LINQ2SQL 最初是随 C# 3.0 和。Net 框架 3.5。

*   ADO.Net 实体框架是一个 ORM(对象关系映射器)API，它允许对对象域模型及其与许多不同 ADO.Net 数据提供者的关系进行广泛的定义。因此，您可以混合和匹配许多不同的数据库供应商、应用服务器或协议，以设计由各种表、源、服务等构建的对象的聚合混搭。ADO.Net 框架是随。Net 框架 3.5 SP1。

🔗**来源:**【stackoverflow.com】T2

### Q16:使用 LINQ 从列表中删除元素

> 题目:**linq**T2】难度:⭐⭐⭐

假设`authorsList`是类型`List<Author>`，我如何删除等于`Bob`的`Author`元素？

* * *

考虑:

```
authorsList.RemoveAll(x => x.FirstName == "Bob"); 
```

🔗**来源:**【stackoverflow.com】T2

### Q17:First()和 Take(1)有什么区别？

> 题目:**linq**T2】难度:⭐⭐⭐

考虑:

```
var result = List.Where(x => x == "foo").First();
var result = List.Where(x => x == "foo").Take(1); 
```

* * *

`First()`和`Take()`的区别在于`First()`返回的是元素本身，而`Take()`返回的是恰好包含一个元素的元素序列。(如果将 1 作为参数传递)。

🔗**来源:**【stackoverflow.com】T2

### Q18:LINQ 什么时候用 First()什么时候用 FirstOrDefault()？

> 题目:**linq**T2】难度:⭐⭐⭐

*   当您知道或期望序列至少有一个元素时，使用`First()`。换句话说，当序列为空是一种例外情况时。
*   当你知道你需要检查是否有一个元素时，使用`FirstOrDefault()`。换句话说，当序列为空合法时。您不应该依赖异常处理来进行检查。(这是不好的做法，可能会影响性能)。

如果没有要返回的行，`First()`将抛出一个异常，而`FirstOrDefault()`将返回默认值(对于所有引用类型为 NULL)。

🔗**来源:**【stackoverflow.com】T2

### Q19:解释标准查询操作符在 LINQ 如何有用？

> 题目:**linq**T2】难度:⭐⭐⭐

形成查询模式的一组扩展方法被称为 LINQ 标准查询操作符。作为 LINQ 查询表达式的构建块，这些运算符提供了一系列查询功能，如过滤、排序、投影、聚合等。

根据功能，LINQ 标准查询操作符可以分为以下几类。

*   过滤运算符(Where，OfType)
*   联接运算符(联接、组联接)
*   投影操作(选择，多选)
*   排序运算符(OrderBy、ThenBy、Reverse、...)
*   分组运算符(GroupBy、ToLookup)
*   转换(Cast、ToArray、ToList、...)
*   串联(Concat)
*   聚合(聚合、平均、计数、最大值、...)
*   量词运算(All、Any、Contains)
*   分区操作(Skip，SkipWhile，Take，...)
*   生成操作(DefaultIfEmpty、Empty、Range、Repeat)
*   集合操作(Distinct，Except，...)
*   相等(SequenceEqual)
*   元素运算符(ElementAt，First，Last，...)

🔗**来源:**【tutorialspoint.com】T2

### Q20:什么是表情树，它们在 LINQ 是如何使用的？

> 题目:**linq**T2】难度:⭐⭐⭐

一个**表达式树**是一个包含表达式的数据结构，基本上就是代码。所以它是一个树形结构，表示你可以用代码进行的计算。然后，可以通过在一组数据上“运行”表达式树来执行这些代码。

在 LINQ，表达式树被用来表示结构化查询，这些查询以实现`IQueryable<T>`的数据源为目标。例如，LINQ 提供者实现了用于查询关系数据存储的`IQueryable<T>`接口。C#编译器将针对此类数据源的查询编译成代码，在运行时构建表达式树。然后，查询提供程序可以遍历表达式树数据结构，并将其翻译成适合数据源的查询语言。

🔗**来源:**【docs.microsoft.com】T2

### Q21:你能解释一下 C#中延迟执行和延迟求值的确切区别吗？

> 题目:**linq**T2】难度:⭐⭐⭐

实际上，它们的意思基本相同。然而，最好使用延期这个术语。

*   懒惰意味着“不到万不得已不要做工作。”
*   延迟意味着“在调用者实际使用结果之前，不要计算结果。”

当调用者决定使用评估的结果时(即开始遍历一个`IEnumerable<T>`)，这正是需要完成“工作”的时候(比如向数据库发出一个查询)。

术语*延迟*更具体/描述了实际发生的事情。当我说我懒的时候，意思是我避免做不必要的工作；这到底意味着什么还不清楚。然而，当我说执行/评估被推迟时，它本质上意味着我根本没有给你真正的结果，而是一张你可以用来要求结果的票。我推迟了实际上走出去，得到那个结果，直到你要求它。

🔗**来源:**【stackoverflow.com】T2

### Q22:解释什么是 LINQ 编译查询？

> 题目:**linq**T2】难度:⭐⭐⭐

可能会出现我们需要多次重复执行特定查询的情况。LINQ 允许我们创建一个查询，并使它总是被编译，从而使这个任务变得非常简单。编译查询的好处:

*   每次都需要编译查询，所以查询的执行速度很快。
*   查询只编译一次，可以使用任意次。
*   即使查询的参数正在更改，查询也需要重新编译。

考虑:

```
static class MyCompliedQueries {
    public static Func <DataClasses1DataContext, IQueryable <Person>> CompliedQueryForPerson = 
        CompiledQuery.Compile((DataClasses1DataContext context) = >from c in context.Persons select c);
} 
```

🔗**来源:**【career.guru99.com】T2

### Q23:获取项目值为真的前 n 个项目的索引

> 题目:**linq**T2】难度:⭐⭐⭐

我有一个`List<bool>`。我需要得到前 n 个条目的索引，其中条目`value = true`。

```
10011001000

TopTrueIndexes(3) = The first 3 indexes where bits are true are 0, 3, 4 
TopTrueIndexes(4) = The first 4 indexes where bits are true are 0, 3, 4, 7 
```

* * *

假设你有一些容易识别的条件，你可以这样做，这将适用于任何`IEnumerable<T>` :

```
var query = source.Select((value, index) => new { value, index })
                  .Where(x => x.value => Condition(value))
                  .Select(x => x.index)
                  .Take(n); 
```

重要的一点是，您使用重载`Select`来获取 Where 之前的索引/值对，然后使用另一个 Select 来获取 Where 之后的索引...并使用 Take 只获得前 n 个结果。

🔗**来源:**【stackoverflow.com】T2

### Q24:定义什么是 Let 子句？

> 题目:**linq**T2】难度:⭐⭐⭐

在查询表达式中，存储子表达式的结果有时很有用，以便在后续子句中使用它。您可以使用`let`关键字来实现这一点，它创建了一个新的范围变量，并用您提供的表达式的结果来初始化它。

考虑:

```
var names = new string[] { "Dog", "Cat", "Giraffe", "Monkey", "Tortoise" };
var result =
    from animalName in names
    let nameLength = animalName.Length
    where nameLength > 3
    orderby nameLength
    select animalName; 
```

🔗**来源:**【career.guru99.com】T2

### Q25:解释 Skip()和 SkipWhile()扩展方法的区别？

> 题目:**linq**T2】难度:⭐⭐⭐

*   **Skip()** **:** 它将接受一个整数参数，并从给定的`IEnumerable`开始跳过顶部的`n`个数字
*   **SkipWhile ():** 它将继续跳过元素，直到输入条件为`true`。如果条件是`false`，它将返回所有剩余的元素。

🔗**来源:**【career.guru99.com】T2

### Q26:解释什么是 lambda 在 LINQ 的表达？

> 题目:**linq**T2】难度:⭐⭐⭐

**λ表达式**被认为是用于形成委托或表达式树类型的唯一函数，其中右侧是方法的输出，左侧是方法的输入。特别是为了编写 LINQ 查询，使用了 Lambda 表达式。

🔗**来源:**【career.guru99.com】T2

### Q27:说出 LINQ 优于存储过程的一些优点

> 题目:**linq**T2】难度:⭐⭐⭐⭐

LINQ 相对于体育运动的一些优势:

1.  **类型安全**:这个我想大家都懂。
2.  抽象:对于 LINQ 到实体来说尤其如此。这种抽象还允许框架添加额外的改进，您可以很容易地利用这些改进。PLINQ 是向 LINQ 添加多线程支持的一个例子。添加这种支持所需的代码更改很少。简单地调用存储过程的数据访问代码要困难得多。
3.  **调试支持**:我可以用任何。NET 调试器来调试查询。使用存储过程，您不能轻松地调试 SQL，这种体验很大程度上依赖于您的数据库供应商(MS SQL Server 提供了一个查询分析器，但通常这还不够)。
4.  **与供应商无关** : LINQ 与许多数据库协同工作，支持的数据库数量只会增加。存储过程并不总是可以在数据库之间移植，因为语法或特性支持不同(如果数据库支持存储过程的话)。
5.  **部署**:部署单个程序集比部署一组存储过程更容易。这也与第四点有关。
6.  **更简单**:不需要学习 T-SQL 做数据访问，也不需要学习调用存储过程所必需的数据访问 API(例如 ADO.NET)。这与#3 和#4 有关。

🔗**来源:**【stackoverflow.com】T2

### Q28:在 LINQ 延期执行死刑有什么好处？

> 题目:**linq**T2】难度:⭐⭐⭐⭐

**延迟执行**意味着表达式的求值被延迟，直到实际需要它的实现值。当您必须操作大型数据集合时，延迟执行可以极大地提高性能，尤其是在包含一系列链式查询或操作的程序中。在最好的情况下，延迟执行只能对源集合进行一次迭代。

在迭代器块中使用时，C#语言中的关键字`yield`直接支持延迟执行(以 yield-return 语句的形式)。

🔗**来源:**【stackoverflow.com】T2

### Q29:什么时候应该使用编译查询？

> 题目:**linq**T2】难度:⭐⭐⭐⭐

当以下所有情况都成立时，您应该使用`CompiledQuery`:

*   该查询将被多次执行，仅因参数值而异。
*   查询非常复杂，以至于表达式求值和视图生成的成本非常高(反复试验)
*   你没有使用像`IEnumerable<T>.Contains()`这样的 LINQ 功能，它不能与`CompiledQuery`一起工作。
*   您已经简化了查询，这在可能的情况下提供了更大的性能优势。
*   您不打算进一步组合查询结果(例如，restrict 或 project)，这会产生“反编译”的效果。

第一次执行查询时，它就开始工作。这对第一次执行没有任何好处。像任何性能调优一样，通常要避免它，直到您确定您正在修复一个实际的性能热点。注意 EF 5 会自动这样做。

🔗**来源:**【stackoverflow.com】T2

### Q30:链式 LINQ 扩展方法调用中的“let”关键字等价于什么？

> 题目:**linq**T2】难度:⭐⭐⭐⭐

基本上`let`创建了一个匿名元组。相当于:

```
var result = names.Select(animal => new { animal = animal, nameLength = animal.Length })
    .Where(x => x.nameLength > 3)
    .OrderBy(y => y.nameLength)
    .Select(z => z.animal); 
```

🔗**来源:**【stackoverflow.com】T2

### Q31:能否提供匿名方法和 lambda 表达式的简明区分？

> 题目:**linq**T2】难度:⭐⭐⭐⭐⭐

考虑以下查询:

```
Customers.Where(delegate(Customers c) { return c.City == "London";}); // delegate
Customers.Where(c => c.City == "London"); // lambda 
```

第一次生成:

```
SELECT [t0].[CustomerID], [t0].[CompanyName], [t0].[ContactName], [t0].[ContactTitle], [t0].[Address], [t0].[City], [t0].[Region], [t0].[PostalCode], [t0].[Country], [t0].[Phone], [t0].[Fax]
    FROM [Customers] AS [t0] 
```

而第二个生成:

```
SELECT [t0].[CustomerID], [t0].[CompanyName], [t0].[ContactName], [t0].[ContactTitle], [t0].[Address], [t0].[City], [t0].[Region], [t0].[PostalCode], [t0].[Country], [t0].[Phone], [t0].[Fax]
    FROM [Customers] AS [t0]
    WHERE [t0].[City] = @p0 
```

编译器能够确定 lambda 表达式是一个简单的单行表达式，可以保留为表达式树，而匿名委托不是 lambda 表达式，因此不能包装为`Expression<Func<T>>`。因此，在第一种情况下，Where 扩展方法的最佳匹配是扩展 IEnumerable 的方法，而不是需要一个`Expression<Func<T, bool>>`的 IQueryable 版本。

🔗**来源:**【stackoverflow.com】T2

### Q32:说出 LINQ 在体育运动上的一些缺点

> 题目:**linq**T2】难度:⭐⭐⭐⭐⭐

LINQ vs 存储过程的一些缺点:

1.  **网络流量**:当 LINQ 发送整个查询时，存储过程只需要通过网络序列化存储过程名称和参数数据。如果查询非常复杂，情况会变得非常糟糕。然而，LINQ 的抽象允许微软随着时间的推移来改进这一点。
2.  不太灵活:存储过程可以充分利用数据库的特性集。LINQ 倾向于提供更通用的支持。这在任何一种语言抽象中都很常见(比如 C#和汇编)。
3.  **重新编译**:如果你需要改变数据访问的方式，你需要重新编译、版本化和重新部署你的程序集。存储过程可以*有时*允许 DBA 调优数据访问例程，而不需要重新部署任何东西。

安全性和可管理性也是人们争论的话题。

1.  **安全性**:例如，您可以通过直接限制对表的访问来保护您的敏感数据，并在存储过程上放置 ACL。然而，使用 LINQ，您仍然可以限制对表的直接访问，而是将 ACL 放在可更新的表*视图*上，以达到类似的目的(假设您的数据库支持可更新的视图)。
2.  **可管理性**:使用视图还可以让您的应用程序不受模式变化的影响(比如表规范化)。您可以更新视图，而不需要更改数据访问代码。

🔗**来源:**【stackoverflow.com】T2

### Q33:Select 和 SelectMany 有什么区别？

> 题目:**linq**T2】难度:⭐⭐⭐⭐⭐

*   **Select** 是一个简单的从源元素到结果元素的一对一映射。
*   **SelectMany** 在查询表达式中有多个 from 子句时使用:原始序列中的每个元素都用来生成一个新序列。它还*简化返回列表列表的查询*。

考虑:

```
public class PhoneNumber
{
    public string Number { get; set; }
}

public class Person
{
    public IEnumerable<PhoneNumber> PhoneNumbers { get; set; }
    public string Name { get; set; }
}

IEnumerable<Person> people = new List<Person>();

// Select gets a list of lists of phone numbers
IEnumerable<IEnumerable<PhoneNumber>> phoneLists = people.Select(p => p.PhoneNumbers);

// SelectMany flattens it to just a list of phone numbers.
IEnumerable<PhoneNumber> phoneNumbers = people.SelectMany(p => p.PhoneNumbers);

// And to include data from the parent in the result: 
// pass an expression to the second parameter (resultSelector) in the overload:
var directory = people
   .SelectMany(p => p.PhoneNumbers,
               (parent, child) => new { parent.Name, child.Number }); 
```

🔗**来源:**【stackoverflow.com】T2

### Q34:返回 IQueryable 和 IEnumerable 有什么区别？

> 题目:**linq**T2】难度:⭐⭐⭐⭐⭐

返回`IQueryable<T>`和`IEnumerable<T>`有什么区别？

```
IQueryable < Customer > custs = from c in db.Customers
where c.City == "<City>"
select c;

IEnumerable < Customer > custs = from c in db.Customers
where c.City == "<City>"
select c; 
```

两者都要延期执行吗？何时应该优先执行哪一个？

* * *

不同的是，`IQueryable<T>`是允许 LINQ 到 SQL (LINQ)的接口。-到-任何真的)工作。因此，如果您进一步细化对一个`IQueryable<T>`的查询，如果可能的话，该查询将在数据库中执行。

考虑:

```
IQueryable<Customer> custs = ...;
// Later on...
var goldCustomers = custs.Where(c => c.IsGold); 
```

该代码将执行 SQL 以仅选择黄金客户。另一方面，会在数据库中执行原始查询，然后过滤掉内存中的非金牌客户:

```
IEnumerable<Customer> custs = ...;
// Later on...
var goldCustomers = custs.Where(c => c.IsGold); 
```

这是一个非常重要的区别，在很多情况下，使用`IQueryable<T>`可以避免从数据库中返回太多的行。另一个主要的例子是分页:如果您在`IQueryable`上使用`Take`和`Skip`，您将只能获得请求的行数；在一个`IEnumerable<T>`上这样做将导致所有的行都被加载到内存中。

🔗**来源:**【stackoverflow.com】T2

### Q35:为什么用。AsEnumerable()而不是转换为 IEnumerable？

> 题目:**linq**T2】难度:⭐⭐⭐⭐⭐

可读性是这里的主要问题。考虑一下

```
Table.AsEnumerable().Where(somePredicate) 
```

比
更具可读性

```
((IEnumerable<TableObject>)Table).Where(somePredicate). 
```

🔗**来源:**【stackoverflow.com】T2

> 谢谢🙌阅读，祝你面试好运！
> *如果你喜欢这篇文章，请分享给你的开发者伙伴！*
> *查看更多全栈面试问题&答案上👉[www . full stack . cafe](https://www.fullstack.cafe)T9】*
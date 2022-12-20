# c#中的数据库—我该怎么做？

> 原文：<https://dev.to/kritner/db-in-c-how-does-i-do-again-1ebh>

### c#中的数据库—我该怎么做来着？

[![](img/ea0889b4d67f239d5f6968f712b86c76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4c95fP3c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Ahp-e6QVsqJS_4tN5) 

<figcaption>照片由[凯文·Ku](https://unsplash.com/@ikukevk?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

我已经有相当长一段时间没有做数据库方面的事情了，因为在我的工作中，我严格地使用 c#-没有数据库，没有前端。随着政府关闭(再次！上好油的机器！)我现在没有工作，所以开始学习吧！我想用数据库做一些事情，但是我已经做了，并且忘记了如何使用它们。已经太久了！

我以前用过 EF，但这次我想用一点老方法。我拼凑了一个小小的抽象概念，它…似乎…有用…？也许我可以在这个过程中不断改进，或者如果没有其他方法可以帮助别人，或者我自己。感谢反馈！:)

使用数据库连接需要一些东西:

*   连接字符串—指定在哪里以及如何连接到数据库的东西
*   数据库连接—实际的数据库连接
*   DB 命令—针对数据库运行的命令(包括参数和返回)
*   可能还有更多，但对我来说应该足够了

#### 连接字符串

有相当多的[连接字符串](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/connection-strings-and-configuration-files)——将它们保存在代码中、配置文件中、环境变量中，可能更多。在我的例子中，我希望将它们存储在一个配置文件中，该文件可以根据环境进行交换。有关如何根据环境交换配置文件的更多信息，请参见:

[。网络核心控制台应用程序选项配置](https://dev.to/kritner/net-core-console-application-ioptionst-configuration-42od)

对于我们的连接字符串，我们需要一些东西:

*   要连接的服务器
*   要使用的数据库
*   身份验证方法(集成或用户 id/密码)

我们将把示例连接字符串放在 appsettings.json 中:

```
"ConnectionStrings":  {  "SomethingAwesomeDatabase":  "Server=MyLegitServer;Database=SomethingAwesome;User Id=MyDude;password=Totes$ecurePassw0rd;"  } 
```

#### IDB 连接

对于我们的数据库连接，理想情况下，我们会利用一个为我们创建连接的方法，以避免在我们的实际服务中出现一些新的类。我们创建 db 连接实际上唯一需要的是连接字符串。这使得我们的工厂方法的签名非常简单。注意我这里用的是工厂这个术语，我 ***很确定*** 这是一个工厂，但不是正面的，如果错了有人能纠正我吗？

```
public interface IDbConnectionFactory
{
 IDbConnection Get(string connectionString);
} 
```

请注意，在上面的内容中，`IDbConnection`是来自名称空间`System.Data`的内置类型。

和一个实现:

```
public class SqlDbConnectionFactory : IDbConnectionFactory
{
 public IDbConnection Get(string connectionString)
 {
  return new SqlConnection(connectionString);
 }
} 
```

很简单——new up 并使用作为参数给出的`connectionString`返回一个`SqlConnection`。

#### IDB 命令

在我们开始使用这个东西之前，这差不多就是所有的设置了。

怎么做呢？

```
public interface ISuperService
{
 void DoStuff();
}

public class MySuperService : ISuperService
{
 private readonly IDbConnectionFactory _dbConnectionFactory;

 public MySuperService(IDbConnectionFactory dbConnectionFactory)
 {
  _dbConnectionFactory = dbConnectionFactory;
 }

 public void DoStuff()
 {
  using (var conn = _connectionFactory.Get("mySecretConnectionString"))
  {
   conn.Open();

   using (var cmd = conn.CreateCommand())
   {
    cmd.CommandText = "mySuperAwesomeStoredProc";
    cmd.CommandType = CommandType.StoredProcedure;

    cmd.AddParameter("someParam", 42);

    using (var reader = cmd.ExecuteReader())
    {
     while (reader.Read())
     {
      Console.WriteLine("Some record was returned");
     }
    }
   }
  }
 }
} 
```

不幸的是，在使用 using 块时，代码变得如此“嵌套”,但是在使用实现了`IDisposable`的对象(必须在 using 块中使用)时，使用块被认为是最佳实践(至少我是这样检查的)。using 块确保代码被更有效地“处理”,而不是让使用者记住必须关闭连接和类似的东西。

我喜欢上面的一点是，我们正在处理一切事物的抽象，而不是具体——这也有助于(forces？)让我们编写更容易测试的代码。

最后，我们使用一个扩展方法`AddParameter`,因为接口没有提供一个非常“漂亮”的添加方法。

扩展方法如下所示:

```
public static class IDbCommandExtensions
{
 public static void AddParameter(this IDbCommand command, string parameterName, object value)
 {
  var param = command.CreateParameter();
  param.ParameterName = parameterName;
  param.Value = value ?? DBNull.Value;

  command.Parameters.Add(param);
 }
} 
```

这有助于我们避免为每个添加的参数添加“几行”——因为它们现在被压缩成一个单行调用。我不记得什么时候需要在`IDbDataParameter`(T2 返回的东西)上指定`DbType`——但是这应该可以，直到它不能。此时，您可以再添加一些扩展方法来处理需要在参数上设置的附加属性。

当把读者返回的值投射到一个对象中时，比你从 EF 中得到的要多一点，但是它是非常直接的。也许我会在某个时候把它放在这里。

代码要点:
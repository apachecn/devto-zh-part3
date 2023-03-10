# 生成一个假数据。网络核心 API

> 原文：<https://dev.to/praneetnadkar/generating-a-fake-data-in-net-core-api-3kba>

前几天，我在做一些东西，我需要一些假数据或者一些随机数据在我的。net 核心 web API。在搜索的时候，我看到了很多博客和链接，我相信有很多人都遇到过这种情况，因为这样或那样的原因，我们不得不生成一个假数据。

有一个简单易行的方法，添加一个循环，创建一个类的新对象，并将其添加到一个列表中。归还名单。

我们都知道这一点。然而，每次添加这个新的和循环是很麻烦的。我搜索并找到了生成测试数据的 [Genfu](http://genfu.io/) 。我喜欢哇！我的大部分工作现在已经完成了。如果你看到它，我将不得不从这个 GenFu 包中调用一个方法，这个方法就是

```
GenFu.GenFu.ListOf<T>() 
```

Enter fullscreen mode Exit fullscreen mode

但是，我又在想，我将不得不把它写在太多的地方。这里还能做什么？

我想为此编写一个服务，在启动时添加，并通过 DI 在每个控制器中使用它。我设法做到了这一点。这是我的接口和我用于 DI 的服务。

```
 public interface IDataGenerator<T> where T : class
    {
        /// <summary>
        /// Generates a collection of type T based on the properties in T
        /// </summary>
        /// <returns>List<T></returns>
        List<T> Collection();

        /// <summary>
        /// Generates the collection of type T of size = length 
        /// </summary>
        /// <param name="length">The size of the collection to be passed</param>
        /// <returns>A collection of type T based on the length passed</returns>
        List<T> Collection(int length);

        /// <summary>
        /// Generates an object of type T with data
        /// </summary>
        /// <returns>T with data based on the properties in T</returns>
        T Instance();
    } 
```

Enter fullscreen mode Exit fullscreen mode

这是我继承接口的类。

```
 public class DataGeneratorService<T> : IDataGenerator<T>
        where T : class, new()
    {
        /// <summary>
        /// Generates a collection of type T based on the properties in T
        /// </summary>
        /// <returns>List<T></returns>
        public List<T> Collection() => GenFu.GenFu.ListOf<T>();

        /// <summary>
        /// Generates the collection of type T of size = length 
        /// </summary>
        /// <param name="length">The size of the collection to be passed</param>
        /// <returns>A collection of type T based on the length passed</returns>
        public List<T> Collection(int length) => GenFu.GenFu.ListOf<T>(length);

        /// <summary>
        /// Generates an object of type T with data
        /// </summary>
        /// <returns>T with data based on the properties in T</returns>
        public T Instance() => GenFu.GenFu.New<T>();
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这里，可以有很多方法可以添加。根福让出了很多东西。我在这里展示了一些。你可以试着和他们一起玩。

方法集合会给我一个 T 类型的列表，集合(int length)会给我一个传递给方法的大小列表。

现在在这里，不要忘记添加包根福。您可以使用软件包管理器控制台添加它，如下所示:

```
install-package GenFu 
```

Enter fullscreen mode Exit fullscreen mode

就这样，现在唯一剩下的事情就是将它添加到启动中。因为这是一个通用的东西，所以我将它添加到启动中，如下所示:

在您的 ConfigureServices 方法中:

```
services.AddSingleton(typeof(IDataGenerator<>), typeof(DataGeneratorService<>)); 
```

Enter fullscreen mode Exit fullscreen mode

现在在去 DI 之前，考虑一个像 Contact 这样的类。这是将为其生成数据的模型类。

```
 public class Contact
    {
        public int Id { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public string EmailAdress { get; set; }
        public string PhoneNumber { get; set; }
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在，我有一个值控制器，其中有一个 HttpGet，它将为我提供这些联系人的假数据。

```
[Route("api/[controller]")]
public class ValuesController : Controller
{
    private readonly IDataGenerator<Contact> _contactsGeneratorService;

    public ValuesController(IDataGenerator<Contact> dataGeneratorService)
    {
        _contactsGeneratorService = dataGeneratorService;
    }

    // GET: api/<controller>
    [HttpGet]
    public IEnumerable<Contact> Get()
    {
        var data = _contactsGeneratorService.Collection(100);
        return data;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我已经准备好了服务。每当我需要任何假数据时，我就把它注入我的控制器并运行它。
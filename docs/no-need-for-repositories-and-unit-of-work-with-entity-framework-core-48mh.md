# 不需要存储库和具有实体框架核心的工作单元

> 原文：<https://dev.to/gpeipman/no-need-for-repositories-and-unit-of-work-with-entity-framework-core-48mh>

在通过存储库和工作单元模式使用实体框架核心的代码库上再次工作后，我决定为下一代(甚至可能是当前和上一代)写一篇令人大开眼界的帖子，讨论实体框架根据这些模式必须提供什么。在许多情况下，我们不必脱离数据库上下文方法，而是比我们最初计划的还要坚持下去。这里列出了产生了多少无用的存储库和工作单元，以及如何避免这些并使用实体框架核心提供的实现。

### 实体框架核心上典型的知识库实现

那是一个有遗留代码的项目出现在我的桌子上的日子，当然有大量的存储库接口和类。乍一看，代码看起来一切正常——组织良好，非常干净，没有一点混乱。但是仓库警告我——地毯下有地雷。

让我们从代码中取出一个存储库，并对其进行一点剖析。首先让我们看看东西是如何连接的。

通常存储库有通用接口和抽象基类。

```
public interface IRepository<T> where T : Entity
{
    T Get(Guid id);
    IList<T> List();
    IList<T> List(Expression<Func<T, bool>> expression);
    void Insert(T entity);
    void Update(T entity);
    void Delete(T entity);
}

public abstract class BaseRepository<T> : IRepository<T> where T : Entity
{
    private readonly MyDbContext _dataContext;

    public BaseRepository(MyDbContext dataContext)
    {
        _dataContext = dataContext;
    }

    public void Delete(T entity)
    {
        _dataContext.Set<T>().Remove(entity);
        _dataContext.SaveChanges();
    }

    public T Get(Guid id)
    {
        return _dataContext.Set<T>().Find(id);
    }

    public void Insert(T entity)
    {
        _dataContext.Set<T>().Add(entity);
        _dataContext.SaveChanges();
    }

    public IList<T> List()
    {
        return _dataContext.Set<T>().ToList();
    }

    public IList<T> List(Expression<Func<T, bool>> expression)
    {
        return _dataContext.Set<T>().Where(expression).ToList();
    }

    public void Update(T entity)
    {
        _dataContext.Entry<T>(entity).State = EntityState.Modified;
        _dataContext.SaveChanges();
    }
} 
```

每个存储库都有自己的接口。它与依赖注入一起使用，所以如果需要的话，更改实现更容易。

```
public interface IInvoiceRepository : IRepository<Invoice>
{
    List<Invoice> GetOverDueInvoice();
    List<Invoice> GetCreditInvoices();
}

public class InvoiceRepository : BaseRepository<Invoice>, IInvoiceRepository
{
    public List<Invoice> GetCreditInvoices()
    {
        // return list of credit invoices
    }

    public List<Invoice> GetOverDueInvoice()
    {
        // return list of over-due invoices
    }
} 
```

那些熟悉存储库模式的人会发现这可能没什么问题。漂亮干净的泛化、代码重用、依赖注入等等——就像一个好的代码架构应该有的那样。

### 在地毯下发现地雷

现在让我们好好看看存储库的操作是如何工作的。让我们采取更新和删除方法。

```
public void Delete(T entity)
{
    _dataContext.Set<T>().Remove(entity);
    _dataContext.SaveChanges();
}

public void Update(T entity)
{
    _dataContext.Entry<T>(entity).State = EntityState.Modified;
    _dataContext.SaveChanges();
} 
```

这里出了什么问题，或者为什么我要抱怨这段代码？良好的..SaveChanges()方法将所有更改发送到数据库。不仅保存与给定实体相关的更改，还保存对所有其他实体的更改。如果我们有使用这个存储库的服务类，并且它实现了一些更复杂的用例，其中更新和删除被多次调用，我们就完蛋了。如果一次更新或删除失败，那么就没有办法回滚之前对数据库所做的所有更改。

### 工作单位帮忙？

我们可以将 SaveChanges()方法移到工作单元容器中，这样我们就可以进行我们需要的更改，并在提交工作单元时保存它们。

```
public class UnitOfWork
{
    private readonly MyDbContext _dataContext;

    public UnitOfWork(MyDbContext dataContext)
    {
        _dataContext = dataContext;
    }

    public void Save()
    {
        _dataContext.SaveChanges();
    }
} 
```

这就是我们如何在假想的服务类中使用我们的工作单元。

```
public class InvoiceService
{
    private readonly IInvoiceRepository _invoiceRepository;
    private readonly UnitOfWork _unitOfWork;

    public InvoiceService(IInvoiceRepository invoiceRepository, UnitOfWork unitOfWork)
    {
        _invoiceRepository = invoiceRepository;
        _unitOfWork = unitOfWork;
    }

    public void CreditInvoice(Guid id)
    {
        var invoice = _invoiceRepository.Get(id);
        Invoice creditInvoice;

        // create credit invoice

        _invoiceRepository.Insert(creditInvoice);
        _unitOfWork.Save();
    }
} 
```

但是事情还是没有好转。我们现在有额外实例注入到服务类中，我们的存储库只是实体框架数据库上下文的虚拟包装器。

### 处置自定义仓库

在努力思考是否有任何理由将工作单元和存储库保留在代码中之后，我发现这不值得。DbContext 类为我们提供了工作单元和存储库的混合。我们不需要仅仅是 DbContext 的 DbSet <something>属性的容器的存储库类。我们也不需要新的类来包装对 SaveChanges()的调用；</something>

更好的是——自定义数据库上下文是我们编写的类。我们扩展了 EF 核心的 DbContext，这意味着我们可以自由地构建自定义上下文。它也可以作为上下文的单位。如果我们需要，它也可以作为一次性存储库。

```
public class LasteDbContext : DbContext
{
    public LasteDbContext(DbContextOptions<LasteDbContext> options)
        : base(options)
    {
    }

    public DbSet<Comment> Comments { get; set; }
    public DbSet<Invoice> Invoices { get; set; }
    public DbSet<InvoiceLine> InvoiceLines { get; set; }

    private IDbContextTransaction _transaction;

    public void BeginTransaction()
    { 
        _transaction = Database.BeginTransaction();
    }

    public void Commit()
    {
        try
        {
            SaveChanges();
            _transaction.Commit();
        }
        finally
        {
            _transaction.Dispose();
        }        
    }

    public void Rollback()
    { 
        _transaction.Rollback();
        _transaction.Dispose();
    }
} 
```

人们现在可以指出，我们正在对具体类型进行操作，这对于依赖注入来说是不好的。好了，让我们应用接口，称之为 IDataContext。

```
public interface IDataContext
{
    DbSet<Comment> Comments { get; set; }
    DbSet<Invoice> Invoices { get; set; }
    DbSet<InvoiceLine> InvoiceLines { get; set; }

    void BeginTransaction();
    void Commit();
    void Rollback();
} 
```

所以，不再有对技术设计的亵渎。我们可以将 IDataContext 的实例注入到我们的服务类、命令或控制器中，并且只使用这个简单接口的类。

> 将 IDataContext 与 NHibernate 等其他对象关系映射器一起使用并不难。我的博客文章[NHibernate on ASP.NET 核心](https://gunnarpeipman.com/aspnet-core-nhibernate/)演示了如何为 NHibernate 模仿类似 DbContext 的 mapper 接口。

### 但是自定义查询呢？

存储库类的一个好处是可以保存自定义的查询方法。这是一个理想的地方，因为这些方法是在靠近处理数据的地方实现的。如果有很多疑问，这不是一个完美的方法，但仍然有一些问题。

```
public class InvoiceRepository : BaseRepository<Invoice>, IInvoiceRepository
{
    public List<Invoice> GetCreditInvoices()
    {
        // return list of credit invoices
    }

    public List<Invoice> GetOverDueInvoice()
    {
        // return list of over-due invoices
    }
} 
```

将这样的方法添加到定制的数据库上下文中会使它变得更大。如此巨大，我们需要一些更好的解决方案。我们有一些选择可以考虑:

*   查询类–实现存储查询的类。缺点和存储库一样——查询方法越多，类就越大。在某一点上，你仍然需要一些更好的解决方案。当然，在 C#中，我们总是可以使用分部类和分组查询来分离文件。
*   [查询对象](https://www.martinfowler.com/eaaCatalog/queryObject.html)——Martin Fowler 在其著名的[企业应用架构模式一书](https://gunnarpeipman.com/patterns-of-enterprise-application-architecture/)中介绍的模式。查询对象是构建转换为 SQL(或任何后端数据存储)的查询的对象层次结构。如果我们没有太多条件要考虑的话是行得通的。
*   扩展方法——每个实体类型也可以有一个静态类，其中查询被定义为它们对应的 DbSet 的扩展方法。它也可以是一般的东西，比如我的[实体框架分页示例](https://gunnarpeipman.com/ef-core-paging/)。
*   应用规范模式——我已经看到了一些解决方案，其中定义了包含子对象和集合的规范以及 where 子句和 order-by 子句的规范。从长远来看，这不是一个好的解决方案，因为随着时间的推移，查询也会增加，我们很容易陷入规范地狱。

在实践中，我们经常在数据库复杂性、数据量和托管数据库费用不断增加的情况下运行 sprint。迟早我们会优化查询，只询问我们需要的数据。这意味着我们的 LINQ 查询会因为调用 Include()方法而变得更加复杂。有时候我们需要在 EF 核心查询上进行变更跟踪，有时候不需要。

我目睹了这一切的发生。在一个项目中，在对 LINQ 查询进行了所有小的优化和调整后，我们在数据库性能上取得了一些可观的胜利，但我们最终得到了冗长的查询。大多数查询又长又丑，不能把它们堆在一个类中。由于大多数查询是特定于模型的，所以我们在模型中添加了工厂方法。对于一些模型，我们创建了工厂类，以避免模型本身因构建细节而膨胀。在这两种情况下，查询都是直接在工厂中托管的，因为没有必要添加额外的层来托管所有查询。我们还将共享查询作为数据访问层的扩展方法来实现。但是我们没有单一的定制存储库或者工作类单元。尽管如此，我们还是设法获得了良好且易于理解的代码结构。

### 包装完毕

为实体框架核心创建工作单元、存储库接口和类是很诱人的，因为每个人都在这么做。尽管如此，DbContext 类仍然提供了这两者的特定于数据库的实现。

*   **工作单元—**使用数据库事务来避免查询在事务提交前意外修改数据，并小心使用数据库上下文的 SaveChanges()方法。
*   **CRUD**–使用数据库上下文的 DBSet < Something >属性。这些集合具有修改和查询数据库中的数据所需的所有方法。
*   **存储查询**–通用查询可以是数据库上下文的一部分，专用查询可以位于查询类、工厂或扩展方法中。

如果它们只是包装了 DbContext 功能而没有添加任何新的价值，那么实际上没有必要实现您自己的工作单元和存储库。简单的想法——利用你已经拥有的东西。

帖子[不需要存储库和具有实体框架核心的工作单元](https://gunnarpeipman.com/ef-core-repository-unit-of-work/)首先出现在 [Gunnar Peipman 编程博客](https://gunnarpeipman.com)上。
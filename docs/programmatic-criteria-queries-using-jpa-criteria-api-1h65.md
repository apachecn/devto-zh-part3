# 使用 JPA 标准 API 的编程标准查询

> 原文：<https://dev.to/irshsheik/programmatic-criteria-queries-using-jpa-criteria-api-1h65>

Java Persistence API (JPA)提供了在 Java 对象和数据库之间管理数据的规范，比如访问和持久化数据。

显然，JPA 中有许多方法可以用来与数据库进行交互，比如 JPQL(Java 持久性查询语言)、Criteria API 和特定于实体的方法，比如 persist、merge、remove、flush 等。

我最初发现 Criteria API 相当吓人。坦率地说，我不得不花相当多的时间去弄明白它。它最终被证明是一个相当流畅的 API。我想为什么不写下这些经历。所以这里是，**“使用 JPA Criteria API 创建编程标准查询”**。

条件查询是类型安全和可移植的。它们是使用 Java 编程语言 API 编写的。它们使用持久实体的抽象模式，通过调用 JPA 实体操作来查找、修改和删除持久实体。

在本教程中，我们将使用以下领域模型来构建标准查询。

[![Object Model- UML](img/2df523fa24c2e5ac2254ef38b09ea166.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--cx4P0abs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.initgrep.com/asseimg/Criteria-object-UML.png) *描述对象关系映射的 UML 图*

我们有三个对象(参考。上图)`Student`、`Course`和`Passport`。每个对象相互之间都有关系:

*   `Student`与`Passport`有`One-To-One`的关系。这意味着每个学生只能有一本护照，反之亦然。
*   `Student`与`Address`有`One-To-Many`关系，这意味着一个学生可以有一个或多个地址，并且一个地址总是分配给一个学生。
*   `Student`与`Course`有`Many-To-Many`关系，这意味着每个学生可以注册许多课程，一门课程可以有许多学生注册。

我们将从一个简单的标准查询开始，慢慢地尝试在此基础上进行构建。

```
public Long getStudentsCount() {

        / **CriteriaBuilder instance** / 
        CriteriaBuilder builder = entityManager.getCriteriaBuilder();

        / **create a criteriaQuery Object** /
        CriteriaQuery<Long> studentQuery = builder.createQuery(Long.class);

        / **create a Root Object -> references to the queried entity** /
        Root<Student> studentRoot = studentQuery.from(Student.class);

        / **Path Object to refer the attribute name of entity** /
        / **we are not using it for now** /
        Path<Object> namePath = studentRoot.get("name");

        / **Aggregate Expression for count operation** /
        Expression<Long> countExpression = builder.count(studentRoot);

        / **** /
        studentQuery.select(countExpression);

        /** instance of Typed Query */
        TypedQuery<Long> typedStudentQuery = 
                            entityManager.createQuery(studentQuery);

        / **return the result** /
        Long count = typedStudentQuery.getSingleResult();

        return count;
    } 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码示例检索数据库中的学生总数。

条件查询是一个对象图，其中图的每个部分代表查询的一个原子部分。构建对象图的各个步骤大致可转化为以下步骤。

*   一个`CriteriaBuilder`接口包含构建标准查询、表达式、排序和谓词所需的所有方法。
*   接口定义了构建顶级查询所需的功能。为标准查询指定的类型，即`criteriaQuery<Class<T> resultClass>`将是返回结果的类型。如果没有提供类型，结果的类型将是`Object`。Criteria Query 包含指定查询结果中要返回的项目、基于特定条件限制结果、对结果进行分组、指定结果的顺序以及[等等](https://docs.oracle.com/javaee/6/api/javax/persistence/criteria/CriteriaQuery.html)的方法。
*   `Root`界面代表查询中涉及的根实体。条件查询中可能定义了多个根。
*   `Path`接口表示`root`实体中属性的路径。`Path`接口也`extends`到包含返回谓词的方法的`Expression`接口。
*   `builder.count()`是一种聚合方法。它返回一个将用于结果的`Selection`的表达式。当聚合方法被用作`select`方法中的参数时，查询的类型应该与聚合方法的返回类型相匹配。
*   运行`CriteriaQuery`需要一个`TypedQuery`实例。

上例的最终输出如下:)

```
select
        count(student0_.id) as col_0_0_ 
    from
        student student0_ 
```

Enter fullscreen mode Exit fullscreen mode

这看起来太累了，不是吗？这么多行代码的输出只是一个普通的老式`SELECT`查询。但是创建标准 API 是为了不同的目的，即*可编程查询 API* 。它有助于动态构建查询。您可以只编写一个程序来为应用程序中的所有对象构建查询，或者根据您的业务逻辑构建查询。

比方说，我们希望获得学生、课程或应用程序中任何其他实体的数量。我们可以为它们分别编写不同的查询，也可以只使用 Criteria API。

```
public <T> Long getCountOfEntity(Class<T> claz) {

        CriteriaBuilder builder = em.getCriteriaBuilder();
        CriteriaQuery<Long> studentQuery = builder.createQuery(Long.class);
        Root<T> root = studentQuery.from(claz);
        Expression<Long> countExpression = builder.count(root);
        studentQuery.select(countExpression);
        TypedQuery<Long> typedStudentQuery = em.createQuery(studentQuery);

        return typedStudentQuery.getSingleResult();
    } 
```

Enter fullscreen mode Exit fullscreen mode

我只更新了前面的例子，以允许指定`Entity`的`Class`的通用参数。其余的代码保持不变。你可以在这里了解更多关于 Java 泛型[的知识。](https://docs.oracle.com/javase/tutorial/java/generics/index.html)

让我们详细看看上面的例子。

**条件查询**

```
CriteriaQuery<T> createQuery(Class<T> resultClass); 
```

Enter fullscreen mode Exit fullscreen mode

`CreateQuery`方法将结果类型的`Class`名称作为参数。

*   如果结果作为与实体相关的数据集返回，例如所有学生或其中一名学生。参数应该是`Student.class`。

```
builder.createQuery(Student.class); 
```

Enter fullscreen mode Exit fullscreen mode

*   如果查询返回任何其他结果，不管它处理哪个实体，它都应该有一个返回类型的参数。正如我们在上面看到的，当返回计数时，参数类型是`Long.class`

```
builder.createQuery(Long.class); 
```

Enter fullscreen mode Exit fullscreen mode

**根**

```
Root<T> root = studentQuery.from(Student.Class) 
```

Enter fullscreen mode Exit fullscreen mode

`Root`指的是将在其上运行查询的实体，比如上面例子中的`Student.class`。

**选择**

```
studentQuery.select(countExpression); 
```

Enter fullscreen mode Exit fullscreen mode

`select`方法指定查询返回的结果。如果实体的所有属性都应该被返回，而不仅仅是返回计数，我们可以像下面这样将`root`实体作为参数传递。

```
studentQuery.select(studentRoot); 
```

Enter fullscreen mode Exit fullscreen mode

如果您已经到了这一步，您可能还想了解一下 Criteria API 提供的各种方法，以实现 criteria join、Fetch Join、聚合函数和子查询。我已经发了一个详细的帖子。 **[点击这里](https://www.initgrep.com/posts/java/jpa/create-programmatic-queries-using-criteria-api)** 查看。
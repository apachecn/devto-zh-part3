# 用于 Microsoft SQL Server 的 Spring Data R2DBC

> 原文：<https://dev.to/lankydandev/spring-data-r2dbc-for-microsoft-sql-server-1084>

这篇文章是我写的另一篇博文的兄弟，这篇博文关注的是 [Spring 数据 R2DBC 和 Postgres](https://lankydan.dev/2019/02/16/asynchronous-rdbms-access-with-spring-data-r2dbc) 。希望你在进入这一页之前已经阅读了标题。所以，你应该知道我将要写的是 Spring Data R2DBC 与 Microsoft SQL Server 的集成。由于 Spring 提供了很多神奇的🧙‍♀️，我之前的 [Postgres 帖子](https://lankydan.dev/2019/02/16/asynchronous-rdbms-access-with-spring-data-r2dbc)中的大部分代码仍然可以工作，只需要做一些修改就可以使用 MSSQL 了。

要了解更多的背景信息，我推荐阅读这篇文章的 [Postgres 版本](https://lankydan.dev/2019/02/16/asynchronous-rdbms-access-with-spring-data-r2dbc)，因为我不想重复太多。也就是说，下面是它的介绍，应该为我们继续提供足够的背景。我将通过提供 MSSQL 实现的简要说明来保持这篇文章的简短。

> 不久前，发布了 JDBC 驱动程序的反应式变体。被称为 R2DBC。它允许数据异步传输到任何订阅它的端点。将 R2DBC 这样的反应式驱动程序与 Spring WebFlux 结合使用，可以让您编写一个完整的应用程序来异步处理数据的接收和发送。在这篇文章中，我们将关注数据库。从连接到数据库，然后最终保存和检索数据。为此，我们将使用 Spring 数据。与所有 Spring 数据模块一样，它为我们提供了开箱即用的配置。减少了我们为设置应用程序而需要编写的样板代码的数量。最重要的是，它在数据库驱动程序上提供了一个层，使简单的任务变得更容易，更困难的任务变得不那么痛苦。

## 依赖关系

```
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-r2dbc</artifactId>
    <version>1.0.0.M1</version>
  </dependency>
  <dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-mssql</artifactId>
    <version>1.0.0.M7</version>
  </dependency>
  <dependency>
    <groupId>io.projectreactor</groupId>
    <artifactId>reactor-core</artifactId>
  </dependency>
</dependencies>

<repositories>
  <repository>
    <id>repository.spring.milestone</id>
    <name>Spring Milestone Repository</name>
    <url>http://repo.spring.io/milestone</url>
  </repository>
</repositories> 
```

Enter fullscreen mode Exit fullscreen mode

这些依赖是这篇文章最起码的要求。由`spring-data-r2dbc`和`r2dbc-mssql`做所有的重活。根据您想要使用的数据库，`r2dbc-mssql`可以切换到另一个有 R2DBC 驱动程序的数据库(例如`r2dbc-postgresql`)。

## 连接数据库

春天在这里支持你，🦸‍♀️.它允许你通过扩展`AbstractR2dbcConfiguration`和实现一个`ConnectionFactory` bean:
来获得所有的基本设置

```
@Configuration
@EnableR2dbcRepositories
class DatabaseConfiguration(
  @Value("\${spring.data.mssql.host}") private val host: String,
  @Value("\${spring.data.mssql.port}") private val port: Int,
  @Value("\${spring.data.mssql.database}") private val database: String,
  @Value("\${spring.data.mssql.username}") private val username: String,
  @Value("\${spring.data.mssql.password}") private val password: String
) : AbstractR2dbcConfiguration() {

  override fun connectionFactory(): ConnectionFactory {
    return MssqlConnectionFactory(
      MssqlConnectionConfiguration.builder()
        .host(host)
        .port(port)
        .database(database)
        .username(username)
        .password(password).build()
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您需要实现的惟一 bean 是`ConnectionFactory`，它包含(顾名思义)关于数据库连接的细节。这个 bean 是`DatabaseClient`的一个组件。这是一个重要的类，因为它是在 Spring Data R2DBC 模块中执行 SQL 的核心。构建`DatabaseClient`的其余 beans 是在`AbstractR2dbcConfiguration`内部创建的，并提供给客户端。让春天来掌舵🙌🚗你会没事的。说真的，扩展`AbstractR2dbcConfiguration`并实现`connectionFactory`将会让你的应用程序快速启动并运行。

最后，`@EnableR2dbcRepositories`注释指示 Spring 寻找任何扩展 Spring 的存储库接口的存储库接口。这被用作检测 Spring 数据仓库的基本接口。在下一节中，我们将对此进行更深入的探讨。

## 创建 Spring 数据仓库

R2DBC 目前不支持查询的推断，这是我习惯于提供给我的数据的便利特性之一。虽然它丢失了，但生命可以继续，但这一次它将需要多一点点代码来完成它:

```
@Repository
interface PersonRepository : R2dbcRepository<Person, Int> {

  @Query("SELECT * FROM people WHERE name = @name")
  fun findAllByName(name: String): Flux<Person>

  @Query("SELECT * FROM people WHERE age = @age")
  fun findAllByAge(age: Int): Flux<Person>
} 
```

Enter fullscreen mode Exit fullscreen mode

通过扩展`R2DBCRepository`，Spring 将获取这些查询并对它们进行检测，同时为您提供一些典型的查询，比如`findByAllId`。这就是前面添加的`@EnableR2dbcRepositories`注释发挥作用的地方。如果没有这个注释，那么这个存储库将不会为您做任何事情，而是需要一个完全手工的实现。

`@Query`用于定义函数将提供的查询，Spring 本身提供了实现。传递给查询的输入使用`@<parameter_name>`的格式定义。

从这些函数返回的`Flux`对象基本上是一个`List`的反应版本。换句话说，它们返回多个值。一旦创建了`Flux`,只要您订阅了它，这些值就会在到达时被返回和处理。

## 实体

尽管 Spring Data R2DBC 的目标不是成为一个完全成熟的 ORM，但它仍然提供了实体映射。下面是一个其实不需要太多解释的实体类:

```
@Table("people")
data class Person(
  @Id val id: Int? = null,
  val name: String,
  val age: Int
) 
```

Enter fullscreen mode Exit fullscreen mode

我说这不需要太多的解释，所以让我说明这一点。`id`已被设置为可空，并具有默认值`null`，以允许 Postgres 自己生成下一个合适的值。如果这不可为空，并且提供了一个`id`值，那么 Spring 实际上会在保存时尝试运行更新，而不是插入。有其他方法可以解决这个问题，但我认为这已经足够好了。老实说，这是一个科特林特有的问题。如果你用的是 Java，那就放松点，你就不需要担心这个了。也就是说，来到光的一面，在科特林编程，如果你这样做，你所有的梦想都会实现(我不能保证那真的会发生🤦‍♂️).

该实体将映射到下面定义的`people`表:

```
CREATE TABLE people (
  id INT NOT NULL IDENTITY PRIMARY KEY,
  name VARCHAR(75) NOT NULL,
  age INTEGER NOT NULL
) 
```

Enter fullscreen mode Exit fullscreen mode

## 在行动中看到一切

所有的设置都已经完成，下面的`Application`类利用了之前编写的查询以及 Spring Data 提供的一些现成的查询。

```
@SpringBootApplication
class Application : CommandLineRunner {

  @Autowired
  private lateinit var personRepository: PersonRepository

  override fun run(vararg args: String?) {
    personRepository.saveAll(
      listOf(
        Person(name = "Dan Newton", age = 25),
        Person(name = "Laura So", age = 23)
      )
    ).log().subscribe()
    personRepository.findAll().subscribe { log.info("findAll - $it") }
    personRepository.findAllById(Mono.just(1)).subscribe { log.info("findAllById - $it") }
    personRepository.findAllByName("Laura So").subscribe { log.info("findAllByName - $it") }
    personRepository.findAllByAge(25).subscribe { log.info("findAllByAge - $it") }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码的实际实现中，添加了一些`sleep`来确保反应式代码有机会工作。反应式应用程序意味着异步做事，因此该应用程序将在不同的线程中处理函数调用。如果不阻塞主线程，这些异步进程可能永远不会完全执行。为了保持整洁，我已经从示例中删除了`sleep`。

运行上面代码的输出类似于下面的内容:

```
2019-05-06 18:05:04.766 INFO 23225 --- [main] reactor.Flux.ConcatMap.1 : onSubscribe(FluxConcatMap.ConcatMapImmediate)
2019-05-06 18:05:04.767 INFO 23225 --- [main] reactor.Flux.ConcatMap.1 : request(unbounded)
2019-05-06 18:05:15.451 INFO 23225 --- [actor-tcp-nio-1] reactor.Flux.ConcatMap.1 : onNext(Person(id=1, name=Dan Newton, age=25))
2019-05-06 18:05:20.533 INFO 23225 --- [actor-tcp-nio-1] reactor.Flux.ConcatMap.1 : onNext(Person(id=2, name=Laura So, age=23))
2019-05-06 18:05:20.533 INFO 23225 --- [actor-tcp-nio-1] reactor.Flux.ConcatMap.1 : onComplete()
2019-05-06 18:05:25.550 INFO 23225 --- [actor-tcp-nio-2] com.lankydanblog.tutorial.Application : findAll - Person(id=1, name=Dan Newton, age=25)
2019-05-06 18:05:25.550 INFO 23225 --- [actor-tcp-nio-2] com.lankydanblog.tutorial.Application : findAll - Person(id=2, name=Laura So, age=23)
2019-05-06 18:05:30.554 INFO 23225 --- [actor-tcp-nio-3] com.lankydanblog.tutorial.Application : findAllById - Person(id=1, name=Dan Newton, age=25)
2019-05-06 18:05:35.582 INFO 23225 --- [actor-tcp-nio-4] com.lankydanblog.tutorial.Application : findAllByName - Person(id=2, name=Laura So, age=23)
2019-05-06 18:05:40.587 INFO 23225 --- [actor-tcp-nio-5] com.lankydanblog.tutorial.Application : findAllByAge - Person(id=1, name=Dan Newton, age=25) 
```

Enter fullscreen mode Exit fullscreen mode

这里要带走一些东西:

*   `onSubscribe`和`request`出现在调用`Flux`的主线程上。只有`saveAll`输出这个，因为它包含了`log`功能。将它添加到其他调用中会导致记录到主线程的相同结果。
*   包含在`subscribe`函数中的执行和`Flux`的内部步骤在不同的线程上运行。

这与您将如何在生产应用程序中使用反应流的真实表现相去甚远，但希望演示如何使用它们，并提供一些关于它们如何执行的见解。

## doOnComplete()

是的，这是一个使用`Flux`函数名称的巧妙双关语🤣😒🤦‍♀️.

我们已经结束了这篇简短的文章。我没有解释太多，因为我的 [Postgres](https://lankydan.dev/2019/02/16/asynchronous-rdbms-access-with-spring-data-r2dbc) 版本做了所有这些，我懒得重写。但是，这也是因为 Spring Data 提供了一个通用接口，它将为您铺平大部分道路。留给你的只有你自己需要做的几个步骤。首先，决定使用哪个数据库，是 Postgres、H2、微软 SQL Server(如本文所示)还是将来有 R2DBC 驱动的其他数据库。最后，在代码方面，设置`ConnectionFactory`和存储库查询，它们在数据库之间会略有不同。我可能应该提到这样一个事实，R2DBC 使您能够转向更具反应性的应用程序。我的意思是，这个帖子毕竟是关于 R2DBC 的🤷‍♀️.利用带有 R2DBC 驱动程序和 Spring 模块的数据库将允许您构建一个完全反应式的应用程序。从应用程序的核心(数据库)到向外部客户端公开的边缘和端点。Spring 对反应式应用程序的推动以及即将到来的 R2DBC 驱动程序的改进，将使这种转换(如果适合您的业务)不那么痛苦，实现起来更快。

这篇文章中使用的代码可以在我的 [GitHub](https://github.com/lankydan/spring-data-r2dbc/tree/mssql_r2dbc) 上找到。

如果你觉得这篇文章很有帮助，你可以在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) 来了解我的新文章。
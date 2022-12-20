# 记录你的集成测试

> 原文：<https://dev.to/vga/dockerize-your-integration-tests--4edm>

我们的大多数应用程序都必须与数据库、HTTP API、消息代理、SMTP 服务器等进行通信，而用这些组件建立一个真正的测试环境是相当复杂的。

在某些情况下，我们可以简单地模仿这些组件，或者在测试执行期间在内存中创建一个组件。例如，ou[HSQLDB](http://hsqldb.org/)是众所周知的在集成测试中使用的内存数据库。然而，它们不是在生产环境中使用的，我们的测试似乎不具有代表性。
今天，多亏了 [Testcontainers](https://www.testcontainers.org/) ，使用 Docker 的所有功能并轻松建立一个连接的测试环境成为可能。

[![testcontainers logo](img/f6ab0f4c029e022110042953b3b2b789.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PIaUbmnf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rnorth.org/public/testcontainers/logo.png)

## Testcontainers

Testcontainers 允许我们在测试执行期间轻松操作 Docker 容器。它使用 docker 客户端 [docker-java](https://github.com/docker-java/docker-java) 与 Docker 守护进程通信。它适用于大多数操作系统和环境，尽管尽最大努力支持 Windows，我每天都用 Docker Toolbox 来使用它。你可以在这里找到矩阵兼容性。

当你创建一个容器时，Testcontainers 将试图通过使用`DOCKER_HOST`、`DOCKER_TLS_VERIFY`和`DOCKER_CERT_PATH`变量连接到 Docker 守护进程。例如，这些环境变量可以很容易地在 JVM 中被覆盖。

### 创建容器

容器用对象 *GenericContainer* 表示。可以从图像、docker 文件或动态创建的 docker 文件创建容器。此外，可以从一个 [Docker 合成文件](https://www.testcontainers.org/usage/docker_compose.html)中创建容器。
例如，这是一个根据图片[docker.elastic.co/elasticsearch/elasticsearch:6.1.1](https://hub.docker.com/_/elasticsearch/)创建的弹性搜索服务器。

```
GenericContainer container = new GenericContainer("docker.elastic.co/elasticsearch/elasticsearch:6.1.1")
   .withEnv("discovery.type", "single-node")
   .withExposedPorts(9200)
   .waitingFor(
      Wait
       .forHttp("/_cat/health?v&pretty")
       .forStatusCode(200)
   ); 
```

我们可以看到，用方法`withEnv`向容器提供环境变量相当容易。在本例中，它是变量 *discovery.type* ，值为*单节点*。
接下来，我们通过对`/_cat/health` API 进行 HTTP 调用并获得 200 代码响应来确保我们的容器启动。还有其他策略来断言容器正在运行:

*   Wait.forLogMessage 等待日志消息，
*   Wait.forListeningPort 等待监听端口
*   Wait.forHealthcheck 允许使用 docker 的[健康检查](https://docs.docker.com/engine/reference/builder/#healthcheck)功能。

为了完成容器配置，我们的容器公开了内部端口 9200，这是用方法`withExposedPorts`显式设置的。这意味着 Testcontainers 将把这个容器的端口映射到一个随机端口。可以用方法`getMappedPort`检索映射的端口，否则我们可以用方法`setPortBindings`定义端口绑定。这里，我们将端口 9200 从我们的容器暴露给端口 9200:

```
container.setPortBindings(Arrays.asList("9200:9200")); 
```

就是这样！我们的 Elasticsearch 服务器已经可以使用了。要启动它，我们只需执行 start 方法:

```
container.start(); 
```

在启动时，Testcontainers 将运行一系列检查，如 docker 版本或注册 Docker 注册表的连接。如果您在公司代理后面工作，这可能会被阻止，因此可以通过在测试资源目录中创建文件*test containers . properties*来禁用这些检查，该文件包含以下内容:

```
check.disable=true 
```

最后，我们可以用 stop 方法停止我们的容器。

```
container.stop(); 
```

这将停止容器并删除附加的卷。这很好，因为它可以防止出现悬空卷。

## 测试期间

Testcontainers 的一大优势是它与 JUnit 框架的集成。事实上，GenericContainer 对象是 [JUnit 规则](https://github.com/junit-team/junit4/wiki/rules)。这意味着它们的生命周期直接与测试生命周期绑定在一起。因此，通过使用`@Rule`或`@ClassRule` JUnit 注释，我们的容器将在测试启动前初始化，并在测试执行结束时停止。

```
@ClassRule
public static GenericContainer redis = new GenericContainer("redis:3.0.2")
               .withExposedPorts(6379); 
```

尽管如此，这意味着 Testcontainers 将带有 JUnit 4 依赖项，如果您的测试使用 [JUnit 5](https://junit.org/junit5/docs/current/user-guide/#overview-what-is-junit-5) 运行，这可能会很烦人。事实上，JUnit 已经用[扩展](https://junit.org/junit5/docs/current/user-guide/#extensions)取代了规则概念。自 2018 年 11 月发布的 1.10.0 版本以来，Testcontainers [现在支持 JUnit 5](https://www.testcontainers.org/usage.html#junit) ，并且可以在专用库 junit-jupiter 的`@Testcontainers`和`@Container`注释的帮助下使用扩展:

```
<dependency>
 <groupId>testcontainers</groupId>
 <artifactId>junit-jupiter</artifactId>
 <version>1.10.2</version>
</dependency> 
```

## 预配置的容器

和 Docker 一样，Testcontainers 生态系统非常丰富。可以找到像 MySql、PostgreSQL、Oracle database、Kafka、Neo4j、Elasticsearch 等预配置的容器。

```
@Rule
public KafkaContainer kafka = new KafkaContainer(); 
```

可以直接从 [maven 库](https://search.maven.org/search?q=g:org.testcontainers)浏览列表。

# 具体案例

让我们看一个在 [Spring PetClinic 应用程序](https://github.com/spring-projects/spring-petclinic)中使用 Testscontainers 的具体例子。这是一个基于几个 Spring 组件的演示项目，比如 Spring Boot、Spring MVC 和 Spring JPA。这个应用程序旨在管理一个有宠物，宠物主人和兽医的宠物诊所。

[![application petclinic](img/fddf8aaf7e018bcb1337c1959565319c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F-e4U47x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6dmhjw1jun0azij8d3zk.png)

控制器层公开 HTTP 端点来创建和读取实体。然后，持久层与关系数据库通信。该应用程序可以配置为与 HSQLDB 或 MySql 数据库进行通信。

持久层通过集成测试进行测试，集成测试使用内存中的 HSQL 数据库，而持久层本身使用 MySql 数据库。

## 要求

首先，我们必须在将要执行测试的机器上安装 Docker。然后，我们需要将 Testcontainers 依赖项添加到项目中。在这种情况下，我们只需将以下内容添加到 *pom.xml* 文件中:

```
<dependency>
  <groupId>org.testcontainers</groupId>
  <artifactId>testcontainers</artifactId>
  <version>1.10.2</version>
  <scope>test</scope>
</dependency> 
```

## 数据库配置

默认的数据库配置在 *application.properties* 文件中完成。

```
database=hsqldb
spring.datasource.schema=classpath*:db/${database}/schema.sql
spring.datasource.data=classpath*:db/${database}/data.sql 
```

正如我们所看到的，这是一个内存中的 HSQLDB 数据库，使用来自 *schema.sql* 文件的模式进行初始化。然后，用 *data.sql* 文件填充数据库。这是默认的项目配置。
我们需要创建*application-test . properties*文件来配置到 MySql 数据库的连接。

```
spring.datasource.url=jdbc:mysql://localhost/petclinic
spring.datasource.username=petclinic
spring.datasource.password=petclinic
spring.datasource.driver-class-name=com.mysql.jdbc.Driver 
```

接下来我们来上测试课`ClinicServiceTests.java`。该类包含持久层的所有集成测试。首先，我们需要更改 Spring 测试配置，以确保测试将使用我们的数据库连接。
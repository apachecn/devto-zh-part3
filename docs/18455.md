# 使用 PostgreSQL，用 Spring Boot 和 JPA 构建一个基本的应用程序

> 原文：<https://dev.to/oktadev/build-a-basic-app-with-spring-boot-and-jpa-using-postgresql-ke9>

每个重要的应用程序都需要一种保存和更新数据的方法:一个可以通过 HTTP 访问的资源服务器。通常，这些数据必须受到保护。Java 是一种优秀的语言，在专业、企业开发方面有几十年的历史，对于任何应用程序的服务器堆栈来说都是一个很好的选择。在 Java 生态系统中，Spring 使得为数据构建安全的资源服务器变得简单。当与 Okta 结合使用时，您可以使用 Spring Security 将专业维护的 OAuth 和 JWT 技术轻松集成到 Spring Boot。

在本文中，您将使用 Spring Boot 和 Spring Data JPA 构建一个资源服务器。在此基础上，您将使用 OAuth 2.0 实现基于组的身份验证和授权层。如果这听起来很复杂-不要担心！不是的。

在我们深入探讨之前，让我们先了解一些背景:

**资源服务器**是服务器功能和数据的编程访问点(基本上与 API 服务器和/或 REST 服务器相同)。

JPA 是 Java 持久性 API，一种使用 Java 管理关系数据库的规范。它描述了 Java 类和关系数据库之间的抽象层。

[**Spring Data JPA**](https://spring.io/projects/spring-data-jpa) 是 Hibernate 等 JPA 提供者的包装器。正如您将看到的，它使持久化 Java 类变得简单，只需添加一些注释并创建一个简单的存储库接口。不需要实际编写持久化或检索方法！另一个很大的好处是，您可以透明地更改底层数据库实现，而不必更改任何代码。例如，在本教程中，您将使用 Postgres，但是如果以后您决定使用 MySQL，您所要做的就是改变一些依赖关系。

## 为 JPA 持久化安装 PostgreSQL

对于本教程，您需要安装 PostgreSQL。如果你还没有安装它，去[他们的下载页面](https://www.postgresql.org/download/)安装它。

接下来，您需要为项目创建一个 Postgres 用户和数据库。为此，您可以使用 Postgres CLI，`psql`。

您应该能够运行下面的命令:`psql -V`并得到类似于:
的响应

```
psql (PostgreSQL) 11.12 
```

## 为你的 JPA 实体创建一个 PostgreSQL 数据库

在使用数据库之前，您需要做一些事情。您需要:

1.  为应用程序创建用户
2.  为该用户设置密码
3.  为应用程序创建数据库
4.  为用户授予数据库权限

本教程使用*jpatural*作为用户名，使用 *springbootjpa* 作为数据库名。如果您愿意，可以随意更改这些值，但是您必须记住在整个教程中使用您的自定义值。

在终端键入`psql`进入 Postgres shell。然后输入以下命令。

### 创建用户

```
create user jpatutorial; 
```

shell 应该用`CREATE ROLE`来响应。

**别忘了分号！**我永远不会，永远不会这么做。我绝对不是凭经验说话。但是，如果你不输入分号，`psql`不会处理命令，你可能会在沮丧的困惑中失去 20-30 分钟，不知道发生了什么，直到你*输入分号，*才尝试处理你的所有命令。

### 给用户一个密码

```
alter user jpatutorial with encrypted password '<your really secure password>'; 
```

shell 应该用`ALTER ROLE`来响应。

### 创建数据库

```
create database springbootjpa; 
```

shell 应该用`CREATE DATABASE`来响应。

### 授予特权

```
grant all privileges on database springbootjpa to jpatutorial; 
```

shell 应该用`GRANT`来响应。

最后，如果您愿意，键入`\q`退出 shell。

如果你想了解更多关于`psql`的信息，你可以看看[的研究生论文](https://www.postgresql.org/docs/9.2/app-psql.html)。

## 搭建 Spring Boot 资源服务器

从 GitHub 存储库中克隆 starter 项目，并检查 **start** 分支:

```
git clone -b start https://github.com/oktadeveloper/okta-spring-boot-jpa-example.git 
```

starter 项目是一个全新的 Spring Boot 项目，只需要一点 Postgres 特有的配置。如果查看`build.gradle`文件，您会看到一个 PostgreSQL JPA 连接器依赖项。您还会注意到文件`src/main/resources/hibernate.properties`,它的唯一目的是消除一个对我们来说无关紧要的恼人的警告/错误。`src/main/resources/application.yml`文件也为您预先填充了一些属性。

继续打开`application.yml`文件，填写您为数据库用户创建的密码。您还应该更新用户名、数据库名和端口(如果它们不同的话)。

```
spring:  
  jpa:  
    hibernate:  
      ddl-auto: create  
    database-platform: org.hibernate.dialect.PostgreSQLDialect  
  datasource:  
    url: "jdbc:postgresql://localhost:5432/springbootjpa"  
    username: jpatutorial  
    password: < your password > 
```

属性指定 hibernate 在加载时的行为。这些选项包括:

*   validate: *验证模式，但不做任何更改*
*   更新:*更新模式*
*   创建:*创建模式，销毁所有以前的数据*
*   create-drop: *类似于 create，但是在会话关闭时也会删除模式(对测试有用)*

你在用`create`。每次程序运行时，都会创建一个新的数据库，从新的表和数据开始。

`database-platform`其实没必要。Spring Data/Hibernate 可以自动检测平台。然而，如果没有这个属性，如果你在没有启动你的 Postgres 服务器的情况下运行这个应用程序，你将会得到一个没有添加这个 config 属性的错误，而不是被告知启动你的服务器。发生这种情况是因为 Hibernate 不能自动检测数据库平台，所以在抱怨实际上没有运行的服务器之前先抱怨这个问题。

用`./gradlew bootRun`运行 app。您应该会看到这样的内容:

```
2018-11-21 09:27:50.233 INFO 31888 --- [main] o.s.j.e.a.AnnotationMBeanExporter : Located MBean 'dataSource': registering with JMX server as MBean [com.zaxxer.hikari:name=dataSource,type=HikariDataSource]
2018-11-21 09:27:50.302 INFO 31888 --- [main] o.s.b.w.embedded.tomcat.TomcatWebServer : Tomcat started on port(s): 8080 (http) with context path ''
2018-11-21 09:27:50.308 INFO 31888 --- [main] c.o.s.SpringBootJpaApplication : Started SpringBootJpaApplication in 21.361 seconds (JVM running for 21.848)
<=========----> 75% EXECUTING [4m 26s]
> :bootRun 
```

不过，现在还没什么效果。没有域模型、资源库或控制器类。

## 用 Spring 数据和 JPA 添加一个域类

域或模型是您将要存储的数据的编程表示。Spring Data 和 JPA 的神奇之处在于，Spring 可以接受一个 Java 类，并将其转换为数据库表。它甚至会自动生成必要的加载和保存方法。最好的部分是，这(或多或少)是独立于数据库的。

在本教程中，您将使用 PostgreSQL，如果您愿意，您可以很容易地将其切换到 MySQL，只需更改`build.gradle`文件中的依赖关系。当然，还要创建一个 MySQL 数据库并更新`application.yml`文件中的必要属性。这对测试、开发和长期维护非常有用。

请继续阅读，了解如何开发一个简单的服务器来存储各种类型的 kayaks。

在`com.okta.springbootjpa`包中创建一个名为`Kayak.java`的 Java 文件。你的 kayak 模型将会有一个名字、一个所有者、一个值和一个品牌/型号。

```
package com.okta.springbootjpa;

import lombok.Data;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity // This tells Hibernate to make a table out of this class
@Data // Lombok: adds getters and setters
public class Kayak {

    public Kayak(String name, String owner, Number value, String makeModel) {
        this.name = name;
        this.owner = owner;
        this.value = value;
        this.makeModel = makeModel;
    }

    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Integer id;

    private final String name;

    private String owner;

    private Number value;

    private String makeModel;
} 
```

这个项目使用 **Lombok** 来避免编写一堆仪式 getters 和 setters 之类的代码。你可以查看[他们的文档](https://projectlombok.org/)，或者更具体地查看[你正在使用的`@Data`注释](https://projectlombok.org/features/Data)。

`@Entity`注释告诉 Spring 这个类是一个模型类，应该转换成一个数据库表。

大多数属性可以自动映射。然而,`id`属性是用几个注释修饰的，因为我们需要告诉 JPA 这是 ID 字段，它应该是自动生成的。

## 用 Spring 数据 JPA 实现一个 CRUD 库

定义了域类之后，Spring 知道了足够的信息来构建数据库表，但是它没有定义任何控制器方法。数据没有输出或输入。Spring 使得添加资源服务器变得很简单。其实这么琐碎，你大概不会相信。

在包`com.okta.springbootjpa`中，创建一个名为`KayakRepository.java`的接口。

```
package com.okta.springbootjpa;

import org.springframework.data.repository.CrudRepository;
import org.springframework.data.rest.core.annotation.RepositoryRestResource;

@RepositoryRestResource
public interface KayakRepository extends CrudRepository<Kayak, Integer> {
} 
```

就是这样！

您现在可以从资源服务器创建、读取、更新和删除 kayaks。一会儿你就要这么做了，但在此之前，再做一个改变。

将下面的`init()`方法添加到`SpringBootJpaApplication`类中:

```
package com.okta.springbootjpa;

import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

import java.text.NumberFormat;
import java.text.ParseException;
import java.util.stream.Stream;

@SpringBootApplication
public class SpringBootJpaApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootJpaApplication.class, args);
    }

  @Bean
  ApplicationRunner init(KayakRepository repository) {

    String[][] data = {
        {"sea", "Andrew", "300.12", "NDK"},
        {"creek", "Andrew", "100.75", "Piranha"},
        {"loaner", "Andrew", "75", "Necky"}
    };

    return args -> {
      Stream.of(data).forEach(array -> {
        try {
          Kayak kayak = new Kayak(
              array[0],
              array[1],
                  NumberFormat.getInstance().parse(array[2]),
              array[3]
          );
          repository.save(kayak);
        }
        catch (ParseException e) {
          e.printStackTrace();
        }
      });
      repository.findAll().forEach(System.out::println);
    };
  }

} 
```

该方法将在应用程序启动时运行。它将一些示例数据加载到资源服务器中，只是为了让您在下一节中有所了解。

## 测试你的 Spring Boot 资源服务器

HTTPie 是一个很棒的命令行实用程序，它使得针对资源服务器运行请求变得很容易。如果没有安装 HTTPie，使用`brew install httpie`安装。或者去他们的网站去实现它。或者只是跟着走。

确保你的 Spring Boot 应用正在运行。如果不是，使用`./gradlew bootRun`启动它。

对您的资源服务器运行 GET 请求:`http :8080/kayaks`，它是`http GET http://localhost:8080/kayaks`的简写。

你会看到这个:

```
HTTP/1.1 200
Content-Type: application/hal+json;charset=UTF-8
Date: Wed, 21 Nov 2018 20:39:11 GMT
Transfer-Encoding: chunked

{
    "_embedded": {
        "kayaks": [
            {
                "_links": {
                    "kayak": {
                        "href": "http://localhost:8080/kayaks/1"
                    },
                    "self": {
                        "href": "http://localhost:8080/kayaks/1"
                    }
                },
                "makeModel": "NDK",
                "name": "sea",
                "owner": "Andrew",
                "value": 300.12
            },
            {
                "_links": {
                    "kayak": {
                        "href": "http://localhost:8080/kayaks/2"
                    },
                    "self": {
                        "href": "http://localhost:8080/kayaks/2"
                    }
                },
                "makeModel": "Piranha",
                "name": "creek",
                "owner": "Andrew",
                "value": 100.75
            },
            {
                "_links": {
                    "kayak": {
                        "href": "http://localhost:8080/kayaks/3"
                    },
                    "self": {
                        "href": "http://localhost:8080/kayaks/3"
                    }
                },
                "makeModel": "Necky",
                "name": "loaner",
                "owner": "Andrew",
                "value": 75
            }
        ]
    },
    "_links": {
        "profile": {
            "href": "http://localhost:8080/profile/kayaks"
        },
        "self": {
            "href": "http://localhost:8080/kayaks"
        }
    }
} 
```

这个输出让您对 Spring Boot 资源返回的数据格式有了一个非常清晰的概念。您也可以使用帖子添加新的 kayak。

命令:

```
http POST :8080/kayaks name="sea2" owner="Andrew" value="500" makeModel="P&H" 
```

回复:

```
HTTP/1.1 201
Content-Type: application/json;charset=UTF-8
Date: Wed, 21 Nov 2018 20:42:14 GMT
Location: http://localhost:8080/kayaks/4
Transfer-Encoding: chunked

{
    "_links": {
        "kayak": {
            "href": "http://localhost:8080/kayaks/4"
        },
        "self": {
            "href": "http://localhost:8080/kayaks/4"
        }
    },
    "makeModel": "P&H",
    "name": "sea2",
    "owner": "Andrew",
    "value": 500
} 
```

如果您再次列出 kayak(`http :8080/kayaks`)，您会在列出的项目中看到新的 kayak。

```
HTTP/1.1 200
Content-Type: application/hal+json;charset=UTF-8
Date: Wed, 21 Nov 2018 20:44:22 GMT
Transfer-Encoding: chunked

{
    "_embedded": {
        "kayaks": [
            ...
            {
                "_links": {
                    "kayak": {
                        "href": "http://localhost:8080/kayaks/4"
                    },
                    "self": {
                        "href": "http://localhost:8080/kayaks/4"
                    }
                },
                "makeModel": "P&H",
                "name": "sea2",
                "owner": "Andrew",
                "value": 500
            }
        ]
    },
    ...
} 
```

您也可以删除 kayak。运行这个命令:`http DELETE :8080/kayaks/4`。这将删除 ID = 4 的 kayak，或者我们刚刚创建的 kayak。第三次获取皮艇列表，你会发现它不见了。

使用 Spring Boot，您可以用最少的代码创建一个功能完整的资源服务器。这些数据将被保存到您的 Postgres 数据库中。

您可以使用 Postgres 命令 shell 来验证这一点。在终端，键入`psql`进入 shell，然后键入以下命令。

连接到数据库:

```
\connect springbootjpa 
```

```
psql (9.6.2, server 9.6.6)
You are now connected to database "springbootjpa" as user "cantgetnosleep". 
```

显示表格内容:

```
SELECT * FROM kayak; 
```

```
 id | make_model | name | owner | value
----+------------+--------+--------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  1 | NDK | sea | Andrew | \xaced0005737200106a6176612e6c616e67...8704072c1eb851eb852
  2 | Piranha | creek | Andrew | \xaced0005737200106a6176612e6c616e672e...078704059300000000000
  3 | Necky | loaner | Andrew | \xaced00057372000e6a6176612e6c616e67...7870000000000000004b
  5 | P&H | sea2 | Andrew | \xaced0005737200116a6176612e6...08b0200007870000001f4
(4 rows) 
```

有几件事需要注意。首先，注意*值*被存储为二进制对象，因为它被定义为`Number`类型，而不是原语(double、float 或 int)。其次，请记住，由于`application.yml`文件中的`ddl-auto: create`行，每次启动应用程序时，这些数据都会被删除，整个表都会被重新创建。

## 设置认证

Okta 是一家软件即服务身份、认证和授权提供商。虽然我确实参与过一些项目，在这些项目中，将一切外包给 SaaS 提供商所带来的问题比它承诺解决的问题还要多，但认证和授权是这种模式完全有意义的地方。网络安全很难。发现漏洞，服务器必须快速更新。标准改变，代码需要修改。所有这些变化都有可能产生新的漏洞。让 Okta 处理安全性意味着您可以担心使您的应用程序独一无二的事情。

为了向您展示设置是多么容易，您将集成 Okta OAuth 并向资源服务器添加基于令牌的身份验证。如果你还没有，去 developer.okta.com 注册一个免费账户。一旦你有了一个帐户，打开开发者仪表板，点击**应用**顶部菜单项，然后点击**添加应用**按钮，创建一个 OpenID 连接(OIDC)应用。

[![Select OIDC app type](img/9d0c3654caa7c20e04d1f3bb2686d37c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v53XFs1u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/3135584348a718de0ec40781773d68d5c7bf1393/3e08d/assets-jekyll/blog/basic-app-spring-boot-jpa/create-app-638d33ebe4552e8d0ed4d1dd97a8b17b7853caed82bfaff17f159013947458e4.png)

选择**单页 App** 。

[![Configure OIDC application settings](img/66aba4328572f22623afa83b1ab69f09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eS5MBR4k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/fd8f195c5734fd58ea568d6008b1507d136cf573/c5a5b/assets-jekyll/blog/basic-app-spring-boot-jpa/app-settings-3eca267025e3adebbfd1ffd9f42fd314f88704ab0926ef96d6e3db50975737cd.png)

默认的应用程序设置很棒，除了你需要添加一个**登录重定向 URI** : `a`。一会儿您将使用它来检索一个测试令牌。

此外，记下您的**客户端 ID** ，因为您稍后会用到它。

[![Note your Client ID](img/bc7116efb285a05a1059129f32192385.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q_znUSQ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/8645a1dd853ee425dabdadc63f443c8c3fc7ec1e/a9dec/assets-jekyll/blog/basic-app-spring-boot-jpa/general-settings-f1e1ba4fe327a179e971c26f692b95bac4171a9eb67b742fbaafba10d211a307.png)

## 配置您的 Spring Boot 资源服务器进行令牌认证

Okta 使得在 Spring Boot 中添加令牌认证变得非常简单。他们有一个名为 Okta Spring Boot Starter 的项目(查看 GitHub 项目)将整个过程简化为几个简单的步骤。

向您的`build.gradle`文件添加几个依赖项。

```
compile('org.springframework.security.oauth.boot:spring-security-oauth2-autoconfigure:2.1.0.RELEASE')  
compile('com.okta.spring:okta-spring-boot-starter:0.6.1') 
```

将以下内容添加到`build.gradle`文件的底部(这解决了一个日志回溯日志依赖冲突)。

```
configurations.all {  
  exclude group: 'org.springframework.boot', module: 'spring-boot-starter-logging'  
  exclude group: 'org.springframework.boot', module: 'logback-classic'  
} 
```

接下来，您需要向您的`application.yml`文件添加一些配置，用 Okta OIDC 应用程序的客户端 ID 替换`{yourClientId}`,用 Okta URL 替换`{yourOktaDomain}`。类似`https://dev-123456.oktapreview.com`的东西。

```
okta:  
  oauth2:  
    issuer: https://{yourOktaDomain}/oauth2/default  
    client-id: {yourClientId}  
    scopes: openid profile email 
```

最后，您需要向您的`SpringBootVueApplication`类添加`@EnableResourceServer`注释。

```
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableResourceServer;

@EnableResourceServer // <- add me
@SpringBootApplication  
public class SpringBootJpaApplication {  

    public static void main(String[] args) {  
        SpringApplication.run(SpringBootJpaApplication.class, args);  
    }
    ...
} 
```

## 测试受保护的 Spring Boot 服务器

停止您的 Spring Boot 服务器，并使用`./gradlew bootRun`重新启动它。

从命令行运行一个简单的 GET 请求。

```
http :8080/kayaks 
```

你会得到一个 401/未授权。

```
HTTP/1.1 401
Cache-Control: no-store
Content-Type: application/json;charset=UTF-8

{
    "error": "unauthorized",
    "error_description": "Full authentication is required to access this resource"
} 
```

## 生成一个访问令牌

要现在访问服务器，您需要一个有效的访问令牌。您可以使用 **OpenID 连接调试器**来帮助您做到这一点。在另一个窗口中，oidcdebugger.com 打开。

**授权 URI** : `https://{yourOktaUrl}/oauth2/default/v1/authorize`，用你实际的 Okta 预览网址替换`{yourOktaUrl}`。

**重定向 URI** :不改变。这是您在上面的 OIDC 应用程序中添加的值。

**客户端 ID** :来自您刚刚创建的 OIDC 应用程序。

**范围** : `openid profile email`。

**State** :希望通过 OAuth 重定向过程传递的任何值。我把它设置为`{}`。

**Nonce** :可以不管。Nonce 的意思是“使用一次的数字”,是一种简单的安全措施，用于防止同一请求被多次使用。

**响应类型** : `token`。

**响应模式** : `form_post`。

[![Generate an Access Token](img/648fffcf6a2f50d4338860d44d31932a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bLDlAx3V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/c8ba4d2f1fb088de29b1240df2ed769893995ad4/650a1/assets-jekyll/blog/basic-app-spring-boot-jpa/oidc-debugger-42318e5066742651860c1932a60690d0bd74db3ca086a8a681679621e5b842e3.png)

点击**发送请求**。如果您没有登录 developer.okta.com，那么您需要登录。如果您(很可能)已经登录，那么将为您的登录身份生成令牌。

[![Access Token success](img/401cb973c5f7f7d0635b70a1021e7ac7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qfG-Nzd4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/47c360a5ec90017f9bb0a6dead0a1c8ef3435785/9b5e1/assets-jekyll/blog/basic-app-spring-boot-jpa/access-token-success-f60ec13c63bd7b0ce8c522a917133d886efcc3ba5ca02465a5ff8202f12eddbb.png)

## 使用访问令牌

您通过将类型为**的**包含在**授权**请求报头中来使用令牌。

```
Authorization: Bearer eyJraWQiOiJldjFpay1DS3UzYjJXS3QzSVl1MlJZc3VJSzBBYUl3NkU4SDJfNVJr... 
```

使用 HTTPie:
发出请求

```
http :8080/kayaks 'Authorization: Bearer eyJraWQiOiJldjFpay1DS3UzYjJXS3QzSVl1...' 
```

## 添加集团授权

到目前为止，授权方案一直是相当二元的。该请求是否携带有效令牌。现在，您将添加基于组的身份验证。请注意，尽管有时在名声不好的网站上可以互换使用，但是角色和组并不是一回事，它们是实现授权的不同方式。

**角色**是用户可以继承的权限集合。一个**组**是分配了一组标准权限的用户集合。然而，在令牌的范围内，以及如何在 JPA 中使用 Spring Security，实现是完全相同的；它们都是作为字符串“权威”从 OAuth OIDC 应用程序传递给 Spring 的，所以目前它们基本上是可以互换的。区别在于什么是受保护的，以及它们是如何定义的。

要在 Okta 中使用基于组的授权，需要在访问令牌中添加一个“groups”声明。创建一个`Admin`组(**用户** > **组** > **添加组**)并将您的用户添加到其中。您可以使用您注册的帐户，或者创建一个新用户(**用户** > **添加人员**)。导航到 **API** > **授权服务器**，点击**授权服务器**标签，编辑默认授权服务器。点击**索赔**标签和**添加索赔**。将其命名为“groups”，并将其包含在访问令牌中。将值类型设置为“Groups”，并将过滤器设置为`.*`的正则表达式。

使用 [OIDC 调试器](https://oidcdebugger.com/)创建新的访问令牌。通过访问 [jsonwebtoken.io](https://www.jsonwebtoken.io/) 并输入您生成的访问令牌，查看您解码后的令牌。

有效载荷看起来有点像这样:

```
{  "ver":  1,  "jti":  "AT.Hk8lHezJNw4wxey1czypDiNXJUxIlKmdT16MrnLGp9E",  "iss":  "https://dev-533919.oktapreview.com/oauth2/default",  "aud":  "api://default",  "iat":  1542862245,  "exp":  1542866683,  "cid":  "0oahpnkb44pcaOIBG0h7",  "uid":  "00ue9mlzk7eW24e8Y0h7",  "scp":  [  "email",  "profile",  "openid"  ],  "sub":  "andrew.hughes@mail.com",  "groups":  [  "Everyone",  "Admin"  ]  } 
```

**组**声明携带用户被分配到的组。您用来登录 developer.okta.com 网站的用户也将是“所有人”组和“管理”组的成员。

为了让 Spring Boot 和资源服务器很好地处理基于组的授权，您需要对代码做一些修改。

首先，在`com.okta.springbootjpa`包中添加一个名为`SecurityConfiguration`的新 Java 类。

```
package com.okta.springbootjpa;

import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
} 
```

这个配置类是启用`@PreAuthorize`注释所必需的，您将使用这个注释来基于组成员关系保护资源服务器。

接下来，将`@PreAuthorize`注释添加到`KayakRepository`中，就像这样:

```
...
import org.springframework.security.access.prepost.PreAuthorize;
...

@RepositoryRestResource  
@PreAuthorize("hasAuthority('Admin')")  
public interface KayakRepository extends CrudRepository<Kayak, Long> {  
} 
```

最后，在`SpringBootJpaApplication`、**中删除**中的`ApplicationRunner init(KayakRepository repository)`方法(或者只是注释掉`@Bean`的注释)。如果跳过这一步，构建将会失败，并出现以下错误:

```
 AuthenticationCredentialsNotFoundException: An Authentication object was not found in the SecurityContext 
```

因为没有用户登录，`@PreAuthorize`注释实际上阻止了`init()`方法以编程方式创建引导数据。因此，随着方法的运行，它会抛出一个错误。

请注意，您在`@PreAuthorize`注释中使用了`hasAuthority()`，而不是**`hasRole()`。不同之处在于，`hasRole()`期望组或角色全部大写，并且有一个`ROLE_`前缀。当然，这是可以配置的，但是`hasAuthority()`没有这个包袱，它只是检查你在`application.yml`中定义为`okta.oauth2.roles-claim`的任何声明。**

 **## 在你的 Spring Boot 应用中测试管理员用户

重启你的 Spring Boot 应用(从`./gradlew bootRun`开始)。

尝试一个未经验证的 GET 请求:`http :8080/kayaks`。

```
HTTP/1.1 401
Cache-Control: no-store
Content-Type: application/json;charset=UTF-8

{
    "error": "unauthorized",
    "error_description": "Full authentication is required to access this resource"
} 
```

用你的代币试试。

命令:

```
http :8080/kayaks 'Authorization: Bearer eyJraWQiOiJldjFpay1DS3UzYjJXS3QzSVl1MlJZc3VJSzBBYUl3NkU4SDJf...' 
```

回复:

```
HTTP/1.1 200
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Content-Type: application/hal+json;charset=UTF-8

{
    "_embedded": {
        "kayaks": []
    },
    "_links": {
        "profile": {
            "href": "http://localhost:8080/profile/kayaks"
        },
        "self": {
            "href": "http://localhost:8080/kayaks"
        }
    }
} 
```

成功了！我们没有任何 kayaks，因为我们必须移除上面的`init()`方法，所以`_embedded.kayaks`数组是空的。

**提示:**接下来，如果您不想复制和粘贴整个巨大的令牌字符串，您可以将它存储到一个 shell 变量中，然后像这样重用它:

```
TOKEN=eyJraWQiOiJldjFpay1DS3UzYjJXS3QzSVl1MlJZc3VJSzBBYUl3NkU4SDJf...
http :8080/kayaks 'Authorization: Bearer $TOKEN' 
```

## 创建非管理员用户

为了演示基于组的授权，您需要在 Okta 上创建一个非管理员的新用户。转到[developer.okta.com](https://developer.okta.com)仪表盘。

从顶部菜单中，选择**用户**和**人员**。

点击**添加人员**按钮。

给用户一个**名**、**姓**和**用户名**(这也将是**主电子邮件**)。这些值并不重要，你也不需要查看邮件。你只需要知道电子邮件地址/用户名和密码，这样你就可以在一分钟内登录 Okta。

**密码**:将下拉菜单改为管理员设置的**。**

为用户分配密码。

点击**保存**。

您刚刚创建了一个用户，该用户不是 *Admin* 组的成员，而是默认组 *Everyone* 的成员。

## 在您的 Spring Boot 应用中测试基于组的授权

注销您的 Okta 开发者仪表板。

返回到 [OIDC 调试器](https://oidcdebugger.com)并生成一个新令牌。

这一次，以新的非管理员用户身份登录。您将被要求选择一个安全问题，之后您将被重定向到`https://oidcdebugger.com/debug`页面，在那里您的令牌可以被复制。

如果你愿意，可以去 [jsonwebtoken.io](https://www.jsonwebtoken.io/) 解码你的新令牌。在有效负载中， *sub* 声明将显示用户的电子邮件/用户名，而 *groups* 声明将只显示 *Everyone* 组。

```
{  ...  "sub":  "test@gmail.com",  "groups":  [  "Everyone"  ]  } 
```

如果您使用新令牌在`/kayaks`端点上发出请求，您将得到 403/Access Denied。

```
http :8080/kayaks 'Authorization: Bearer eyJraWQiOiJldjFpay1DS3UzYjJX...'

HTTP/1.1 403
...

{
    "error": "access_denied",
    "error_description": "Access is denied"
} 
```

为了展示`@PreAuthorize`注释的真正威力，创建一个方法级安全约束。将`KayakRepository`类改为如下:

```
@RepositoryRestResource  
public interface KayakRepository extends CrudRepository<Kayak, Long> {  

    @PreAuthorize("hasAuthority('Admin')")  
    <S extends Kayak> S save(S entity);  

} 
```

这仅将`save()`方法限制为 Admin 组的成员。存储库的其余部分将受到限制，只需要认证，而不需要特定的组成员。

重启你的 Spring Boot 服务器。再次运行相同的请求。

```
http :8080/kayaks 'Authorization: Bearer eyJraWQiOiJldjFpay1DS3UzYjJX...' 
```

```
HTTP/1.1 200
...

{
    "_embedded": {
        "kayaks": []
    },
    "_links": {
        "profile": {
            "href": "http://localhost:8080/profile/kayaks"
        },
        "self": {
            "href": "http://localhost:8080/kayaks"
        }
    }
} 
```

kayaks 存储库是空的，所以`_.embedded.kayaks`是一个空数组。

尝试创建一个新的皮艇。

```
http POST :8080/kayaks name="sea2" owner="Andrew" value="500" makeModel="P&H" "Authorization: Bearer eyJraWQiOiJldjFpay1DS3UzYjJX..." 
```

你会得到另一个 403。“保存”在这里相当于一个 HTML 帖子。

但是，如果您使用从原始 admin 帐户生成的令牌，它就可以工作。

**注意:**您的令牌可能会过期，您必须再次退出 developer.okta.com，并在 [OIDC 调试器](https://oidcdebugger.com/)上重新生成令牌。

发布一个新的 kayak，使用你的管理员帐户生成的令牌。

这次你会得 201 分。

```
HTTP/1.1 201
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Content-Type: application/json;charset=UTF-8
...

{
    "_links": {
        "kayak": {
            "href": "http://localhost:8080/kayaks/1"
        },
        "self": {
            "href": "http://localhost:8080/kayaks/1"
        }
    },
    "makeModel": "P&H",
    "name": "sea2",
    "owner": "Andrew",
    "value": 500
} 
```

成功！

看一下 [Spring Data 的`CrudRepository`接口](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html)，了解一下可以被覆盖并被赋予方法级安全性的方法。`@PreAuthorize`注释不仅可以用于组，还可以用于其他很多方面。Spring 的表达式语言(SpEL)的全部功能都可以被利用。

```
public interface CrudRepository<T, ID> extends Repository<T, ID> {
  <S extends T> S save(S entity);
  <S extends T> Iterable<S> saveAll(Iterable<S> entities);
  Optional<T> findById(ID id);
  boolean existsById(ID id);
  Iterable<T> findAll();
  Iterable<T> findAllById(Iterable<ID> ids);
  long count();
  void deleteById(ID id);
  void delete(T entity);
  void deleteAll(Iterable<? extends T> entities);
  void deleteAll();
} 
```

就是这样！很酷吧？在本教程中，您设置了一个 PostgreSQL 数据库，创建了一个使用 Spring Data 和 JPA 来持久化数据模型的 Spring Boot 资源服务器，然后用非常少的代码将这个数据模型转换成一个 REST API。此外，您使用 Okta 将 OIDC 认证和 OAuth 2.0 授权添加到您的服务器应用程序中。最后，您实现了一个简单的基于组的授权方案。

如果你想了解这个完整的项目，你可以在 GitHub 的[@ okta developer/okta-spring-boot-JPA-example](https://github.com/oktadeveloper/okta-spring-boot-jpa-example)找到回购。

请关注本系列的下一篇文章，它将介绍如何在 Spring WebFlux 中使用 NoSQL 数据库(MongoDB)。

## 了解有关 Spring Boot、Spring Security 和安全认证的更多信息

如果您想了解关于 Spring Boot、Spring Security 或现代应用程序安全性的更多信息，请查看这些优秀的教程:

*   [开始使用 Spring Boot、OAuth 2.0 和 Okta](https://developer.okta.com/blog/2017/03/21/spring-boot-oauth)
*   [在 15 分钟内将单点登录添加到您的 Spring Boot 网络应用中](https://developer.okta.com/blog/2017/11/20/add-sso-spring-boot-15-min)
*   [通过多重身份认证保护您的 Spring Boot 应用](https://developer.okta.com/blog/2018/06/12/mfa-in-spring-boot)
*   [用 Spring Boot 和 GraphQL 构建一个安全的 API](https://developer.okta.com/blog/2018/08/16/secure-api-spring-boot-graphql)

如果你想深入了解，可以看看 Okta Spring Boot Starter GitHub 项目。

这是一个很棒的关于 Spring 数据和保护 Spring Boot 项目的参考:[https://docs . Spring . io/Spring-Data/rest/docs/current/reference/html/](https://docs.spring.io/spring-data/rest/docs/current/reference/html/)。

Vlad Mihalcea 有一个很棒的教程，名为[结合 JPA 和 Hibernate](https://vladmihalcea.com/9-postgresql-high-performance-performance-tips/) 使用 PostgreSQL 时的 9 个高性能技巧。

Baeldung 有一个关于 Spring 数据/ Spring Boot 项目中安全方法的有用教程:[https://www.baeldung.com/spring-security-method-security](https://www.baeldung.com/spring-security-method-security)。

最后，如果你需要更多关于 Mac OS X 上的 PostgreSQL 的帮助，请看[这个 codementor.io 教程](https://www.codementor.io/engineerapart/getting-started-with-postgresql-on-mac-osx-are8jcopb)。

如果你对这篇文章有任何问题，请在下面添加评论。更多精彩内容，请在 Twitter 上关注 [@oktadev](https://twitter.com/oktadev) ，就像我们在脸书关注[，或者订阅](https://www.facebook.com/oktadevelopers/)[我们的 YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。**
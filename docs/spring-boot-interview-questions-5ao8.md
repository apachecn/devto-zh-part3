# Spring Boot 面试问题

> 原文：<https://dev.to/anilkkurmi/spring-boot-interview-questions-5ao8>

20 大 Spring Boot 面试问题和答案列表

1.  使用弹簧靴有什么好处？它与 Spring 框架有何不同？Spring Boot 通过提供自动配置使得创建一个独立的应用程序变得非常容易。它提供生产就绪功能，如执行器。

特征

自动配置
创建最简单的独立应用
提供嵌入式 tomcat、Jetty 服务器
无需部署 war 文件
无需维护应用/web 服务器
提供生产就绪特性，如指标、JMX、健康检查和审计。
提供各种启动器以简化依赖性管理
轻松集成第三方库
外部化配置
配置文件特定属性文件
Spring boot 是 Spring framework 的附加组件。Spring Boot 通过自动配置简化了应用程序的创建。

1.  Spring Boot 常用的注释有哪些？
    @SpringBootApplication:标记一个 Spring Boot 应用的主类
    @SpringBootTest:用于集成测试
    @Configuration:用于提供 bean 配置
    @ Conditional Bean
    @ Bean
    @ DataJpaTest
    @ WebMVCTest
    @ Value
    @ controller advice

2.  如何在 Spring boot 应用启动前定制环境或 ApplicationContext？SpringApplication 类具有 ApplicationListeners 和 ApplicationContextInitializers，用于将定制应用于上下文或环境。Spring Boot 加载了许多这样的定制。它在内部使用 META-INF/spring.factories

有许多方法可以注册其他自定义:

I .在运行 SpringApplication 之前，通过调用 spring application 上的 addListeners 和 addInitializers 方法以编程方式实现。
二。通过在 application.properties.
III 中设置 context.initializer.classes 或 context.listener.classes 进行声明。通过添加一个 META-INF/spring.factories 并添加应用程序用作库的 jar 文件。

1.  如何创建自定义 Spring Boot 汽车配置？自动配置是 Spring Boot 以最少的配置自动配置应用程序的方式。自动配置类可以捆绑在外部 jar 中，由 Spring Boot 应用程序获取。自动配置可以关联到一个“starter”依赖项，该依赖项提供自动配置代码以及您将与之一起使用的典型库。

在幕后，自动配置是通过标准的@Configuration 类实现的。附加的@Conditional 注释用于约束自动配置应用的时间。通常，自动配置类使用@ ConditionalOnClass 和@ ConditionalOnMissingBean 注释。这确保了自动配置仅在找到相关的类并且您没有声明自己的@Configuration 时才适用。

1.  如何在 Spring Boot 禁用特定的自动配置 bean？要排除/禁止自动配置 bean 加载到应用程序，我们可以将带有 exclude 或 excludeName 属性的@EnableAutoConfiguration 注释添加到配置类中。

禁用特定自动配置的另一个选项是通过设置应用程序属性文件中的 spring.autoconfigure.exclude 属性。

1.  什么是 Spring Boot 执行器？如何创建自定义执行器？致动器可以为 Spring Boot 应用程序提供生产就绪的特性，而无需您自己实际实现这些功能。默认情况下，执行器端点是安全的。以下是 Spring Boot 的一些常见终点

/health —显示应用程序健康信息。它通常被监控软件用来在生产系统停机时提醒某人。
/info —显示任意应用程序信息
/metrics —显示当前应用程序的“指标”信息；
/trace —显示跟踪信息
/mappings —显示所有@RequestMapping 路径的整理列表。
默认情况下，除关机外的所有端点都是启用的。为了启用/禁用执行器端点，我们可以使用它的 management.endpoint..启用的属性。例如

management . Endpoint . shut down . enabled = true
Spring Boot 使用 Jersey、Spring MVC 或 Spring WebFlux 通过 HTTP 使用@Endpoint、@WebEndpoint 或@WebEndpointExtension 自动公开端点。我们可以添加一个用@Endpoint 注释的@Bean，任何用@ReadOperation、@WriteOperation 或@DeleteOperation 注释的方法都会通过 JMX 自动公开，在 web 应用程序中，也会通过 HTTP 公开。

1.  如何在 Spring Boot 配置记录器？如何更改默认日志记录级别？Spring Boot 对所有内部日志记录使用公共日志记录，但对底层日志实现保持开放。为 Java Util 日志记录、Log4J2 和 Logback 提供了默认配置。默认情况下，如果我们使用启动器，Logback 用于日志记录。

通过使用- debug 标志启动应用程序来启用调试模式。
$ Java-jar myapp . jar-debug
From application . properties
debug = true
默认情况下，Spring Boot 只记录到控制台，不写日志文件。如果我们想写日志文件，我们可以在 application.properties 中设置 logging.file 或 logging.path 属性。

更改日志级别
logging . level . root = WARN
logging . level . com . myapp = DEBUG
logging . level . org . third pary . app = ERROR

1.  加载多个外部配置/属性文件有哪些不同方法？我们可以通过多种方式提供负载配置文件。Spring Boot 属性按以下顺序加载。根据我们需求，我们可以使用一种或多种方法。

命令行参数:$ Java-jar myapp . jar-dspring . config . location = class path:job 1 . properties，class path:job 2 . properties com . app . main class

1.  设置 Java 系统属性(System.getProperties())。
2.  设置操作系统环境变量。
3.  打包 jar 之外的应用程序属性(application.properties 包括 YAML 和概要文件变量)。
4.  打包在 jar 中的应用程序属性(application.properties 包括 YAML 和概要文件变量)。
5.  @Configuration 类上的@PropertySource 批注。
6.  默认属性(使用 spring application . setdefaultproperties 指定)。
7.  解释 Spring Boot 档案是如何工作的？如何配置多个配置文件？Spring Profiles 提供了一种方法来隔离应用程序配置的各个部分，并使其只在特定的环境中可用。例如，我们可以使用单独的概要文件进行测试。任何@Component 或@Configuration 都可以用 [@profile](https://dev.to/profile) 来标记，以限制它何时被加载。

@ Configuration
[@ profile](https://dev.to/profile)(“生产”)
public class my security Configuration {
}
我们可以使用 spring.profiles.active 环境属性来指定哪些概要文件是活动的。

从命令行，我们传递以下参数

$ Java-jar myapp . jar—spring . profiles . active = dev

1.  如何使用自定义的弹簧引导父 POM？Spring Boot 提供了更容易创建 Spring Boot 应用程序的父 POM。父 pom.xml 负责依赖性和插件管理。

为了创建自定义的父 POM，我们可以使用 spring-boot-starter-parent 作为依赖项，并自定义 spring-boot-maven-plugin。

org . spring framework . boot
spring-boot-dependencies
$ { spring framework . boot . version }
POM
导入

1.  如何更改 Spring Boot 中的默认上下文路径？我们可以简单地通过在 application . properties server . servlet . context-path =/rest/中定义来更改默认的上下文路径

我们也可以从命令行设置系统属性、操作系统环境变量。

1.  如何将 Spring Boot 应用程序部署为一场战争？在 our POM . XML 中添加的 spring-boot-maven-plugin 通过使用 spring-boot:repackage 目标自动尝试重写归档文件，使它们可执行。我们可以通过以下方式更改 pom.xml 的默认打包

war
然后，我们初始化 Tomcat 上下文所需的 Servlet 上下文。我们可以实现 SpringBootServletInitializer 接口。

@SpringBootApplication
公共类 MainClass 扩展 SpringBootServletInitializer {
}

1.  如何更改 Spring Boot 的默认服务器端口？默认情况下，嵌入式服务器在端口 8080 上启动。我们可以通过在 application.properties 中定义它来更改它

server.port=8084
我们也可以通过实现 WebServerFactoryCustomizer 接口并从命令行传递来动态更改。

1.  如何在 Spring Boot 自定义默认的 Spring Security？Spring Boot 通过简单地添加安全启动器依赖性来自动配置安全性。SecurityAutoConfiguration 类包含各种默认的自动配置。默认情况下，Spring Boot 将启用基本身份验证安全性。

我们可以通过扩展 WebSecurityConfigurerAdapter 类和覆盖 configure 方法来自定义 Spring 安全性。

@Configuration
公共类 SecurityConfig 扩展 WebSecurityConfigurerAdapter {
@ Override
void configure(http security http){
}
}

1.  如何禁用 Spring Boot 应用程序中的 Spring Security？我们可以通过从 Spring Boot 应用程序中排除 SecurityAutoConfiguration 类来禁用安全自动配置。

@ spring boot application(exclude = { securityautoconfiguration . class })
public class SpringSecurityConfig {
}
我们还可以通过允许所有请求来绕过 spring 安全性

@ Configuration
public class SpringSecurityConfig {
@ Bean
public security webfilterchain filter chain(server http security http){
http . authorize exchange()。anyExchange()。permit all()；
返回 http . build()；
}
}

1.  Spring Boot 综合测试怎么写？Spring Boot 提供了许多实用程序和注释来帮助测试你的应用程序。Spring Boot 为集成测试提供了一个@SpringBootTest 注释。@SpringBootTest 注释通过创建测试中使用的 ApplicationContext 来工作。@SpringBootTest 也提供各种定制。

@ run with(spring runner . class)
@ spring boot test
public class integration test {
}

1.  如何在 Spring Boot 只测试数据库层(JPA)？我们可以使用@DataJpaTest 注释来测试数据库层。默认情况下，它配置一个内存嵌入式数据库，例如 H2 数据库。它扫描@ Entity beans，并配置 Spring 数据 JPA 存储库。常规@ Component beans 不会加载到 ApplicationContext 中。同样，默认情况下，数据 JPA 测试是事务性的，并在每次测试结束时回滚。

@ run with(spring runner . class)
@ DataJpaTest
公共类 DatabaseTest{

}
我们可以注入一个 TestEntityManager bean，它为专门为测试设计的标准 JPA EntityManager 提供了一个替代方案。同样，Spring Boot 为 MongoDB 测试提供了@DataMongoTest 注释，为 Redis 应用程序测试提供了@DataRedisTest 注释。

1.  如何在 Spring Boot 配置两个数据库和两个 EntityManager？为了在我们的应用程序中使用多个数据库，我们必须创建自己的数据源 bean。

# 数据库 1

first.url = [url]
...

# 数据库

second.url = [url]
....

@ Bean(" db1 ")
@ Primary
@ configuration properties(prefix = " first ")
公共数据源 primaryDataSource() {
返回 DataSourceBuilder.create()。build()；
}

@ Bean(" DB2 ")
@ configuration properties(prefix = " second ")
公共数据源 secondaryDataSource() {
返回 DataSourceBuilder.create()。build()；
}
同样，我们可以通过定制 bean 来创建多个 EntityManager。Spring boot 为了方便起见提供了 EntityManagerBuilder。

@Bean("em1")
公共 localcontainereentitymanagerfactorybean em1(
EntityManagerFactoryBuilder){
返回构建器
。
数据源(customDataSource1())。包(DataSource.class)
。build()；
}

@Bean("em2")
公共 localcontainereentitymanagerfactorybean em2(
EntityManagerFactoryBuilder){
返回...；
}

1.  如何定制对返回 XML 或 JSON 的多内容协商的支持？我们可以在 WebMvcConfigurerAdapter 配置类中覆盖 configureContentNegotiation 方法。

@ override
public void configure contentnegotiation(内容协商配置){
...我...
defaultContentType(媒体类型)。APPLICATION_JSON)。
介质类型(“xml”，介质类型)。APPLICATION_XML)。
介质类型(“json”，介质类型)。APPLICATION_JSON：
}
}

# 和

produces = { "application/json "，" application/xml" }

1.  如何在 Spring Boot 注册 Servlet，过滤器和监听器？当使用嵌入式服务器时，我们可以通过使用 Spring beans 或扫描 Servlet 组件来注册 Servlet、过滤器和监听器。

我们可以使用 FilterRegistrationBean 注册过滤器，使用 ServletRegistrationBean 注册 Servlet，使用 ServletListenerRegistrationBean 注册侦听器。

@ Bean
public FilterRegistrationBean cust filter(){
FilterRegistrationBean registration = new FilterRegistrationBean()；
registration . set filter(new my filter())；
退货登记；
}
[https://practice overflow . com/take-Test/siew v1 ypgbd 7 ibkot 3 nu/Spring-Boot-IQ-Test-2019:-contains-top-20-questions-to-practice-your-Spring-Boot-knowledge](https://practiceoverflow.com/take-test/sieWV1ypGBd7iBKoT3Nu/Spring-Boot-IQ-Test-2019:-contains-top-20-questions-to-practice-your-Spring-boot-knowledge)
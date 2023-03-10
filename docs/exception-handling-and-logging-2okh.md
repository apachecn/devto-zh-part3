# 异常处理和日志记录

> 原文：<https://dev.to/charles1303/exception-handling-and-logging-2okh>

#### 简介

在我的上一篇文章[中，我们讨论了将我们的应用程序集成到外部 API 的实现和步骤。我们强调了用于执行任务的设计模式和策略。它们分为以下几个部分。](https://dev.to/charles1303/a-use-case-implementation-for-external-api-integration-eoj)

*   设计模式

*   表演

*   测试

在本文中，我将在上一篇文章的基础上讨论如何实现以下需求:

*   异常处理

*   记录

#### 异常处理

异常处理可以被看作是为我们的应用程序提供弹性的一种方式。它允许应用程序即使在出错时也能继续正常运行，同时报告出错的原因。想象一下，当我们试图访问外部 API 时，由于它在某个时候不可用，我们得到一个 404 HttpException，或者我们得到一个数据库连接异常，这很可能导致 500 HttpException。我们不希望我们的应用程序由于这种情况而变得不可用或行为异常。通过给出符合预期响应格式或应用程序结构的预定义响应，它仍应正常工作。这是一个很好的实践，因为一个系统宕机并不意味着我们部署基础架构中的所有其他系统或功能都宕机或不可访问。相反，我们的应用程序应该仍然是可访问的，并正常响应，但以一种可展示的方式和行为报告出了问题。我们可以将异常处理视为大数据世界中的一种规定性分析。

在我们的 Spring 实现中，我们创建了一个类并用 *ControllerAdvice* 对其进行注释，如 **CardDetailControllerAdvise 类**所示。然后我们定义处理异常场景的方法。确保在您的控制器中抛出适当的预期异常，如 **CardDetailController 类**所示。我还引入了两个新类 **RecordNotFoundException** 和 **GeneralException** ，它们在我的业务用例中使用。我还在 CardDetailControllerAdvise 类中引入了 **HttpStatusCodeException 类**来处理 HTTP 级别的异常(在本例中是我们的 404 和 500)。当然，您可以扩展或修饰 advice 类中的**getHttpExceptionDetails**方法来添加其他 HTTP 异常。

另一方面，在实现异常处理时，一个干净的方法是避免在决策中使用异常。例如，与其这样做:

```
 public int execute () {

    try{
        // Code to execute
        return 1;
     }catch(Exception ex) {
       return -1;
    }

} 
```

要么这样做

```
 public int execute () {

        int status = -1;

        try {
            //Code to execute
            status = 1;
        }catch(FileNotFoundException ex ) {
          //Log exception details
        }
        return status;
    } 
```

或者像这样简单地抛出异常，让它传播给方法的调用者，然后由调用者决定如何处理，就像我们的例子一样

```
 public int execute () throws CustomException {

        int status = -1;

        try {
            //Code to execute
            status = 1;
        }catch(FileNotFoundException ex ) {
          throw new CustomException("Could not access file resource.", e);
        }
        return status;
    } 
```

在这里，我们将我们的异常包装在一个 CustomException 类中，当这个异常场景发生时抛出。我们只需覆盖 java.lang.Exception 类的构造函数，该类接受一条消息(这是我们希望呈现给客户端的消息)和一个 Throwable 对象(在本例中是抛出的异常对象)。

```
 public class CustomException extends Exception {  
        public CustomException(String message, Exception ex) {
        super(message, ex);
        }
    } 
```

上面的实现可以在 **CardDetailService 类**的**getCardRequestLogsCountGroupedByCard**方法中看到。这给出了与应用程序预定义的响应格式一致的响应格式，并允许您在 **CardDetailControllerAdvise 类**的帮助下，使用更集中的方法来控制异常消息内容。

现在你猜怎么着！我们的 **CacheIntegrationTest 类**现在失败了，因为它现在抛出了一个基于上述实现的异常。在这种情况下，因为我们的**CardDetailRequestLogRepository 类**实际上正在被模仿，所以它的**getCardRequestLogsCountGroupedByCardNumber**方法将返回 null，导致抛出 GeneralException 类。因此，我们需要在 CacheIntegrationTest 类中创建一个 *ExpectedException* 规则，并测试预期会抛出的 GeneralException。

包装我们的 API 异常是一个很好的实践，这样可以在高层次的分析和调查中定位异常的来源和原因。

此外，由于写入日志时固有的 I/O 操作增加以及由于抛出异常而导致的内存使用，我们应该要么记录异常，要么抛出异常，但不能像前面所示的那样两者都记录。这样做会导致通常所说的*烫手山芋*反模式设计。这就造成了这样一种情况，大量 CPU 密集型工作以相对较少的有价值的输出完成，导致应用程序的 CPU 使用率和内存消耗达到峰值。抛出并记录异常还会导致一堆并不真正需要的日志消息。因此，大量的文本会降低日志的可见性。

除了上述处理异常的方法之外，人们确实采用了将来自外部 API 的数据响应缓存到数据存储中的策略。当外部 API 不可用或出现问题时，可以从缓存的系统中访问这些数据响应。这种策略是一种被称为*断路器*的设计模式。容错库 *Hystrix* 是这种模式的一个常见实现，稍后我将向您展示。

首先，我们在项目中包含了 spring-cloud-starter-网飞-hystrix 依赖项。

```
 <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        <version>2.1.1.RELEASE</version>
    </dependency> 
```

然后我们在 **CardDetailService 类**中创建一个方法 **verifyCardDetailHystrix** ，并用 *HystrixCommand* 对其进行注释，并设置必要的 *HystrixProperty* 值。然后，我们创建一个回退方法 **defaultCardDetailDto** ，当 API 在一段时间后不可访问时将调用该方法，以便返回我们缓存的或默认的数据。最后，我们通过用 *EnableCircuitBreaker* 注释我们的**应用程序类**来启用断路器。仅此而已。每当 API 不可用时，就会调用 defaultCardDetailDto 方法。除了我使用的那些，还有许多其他的 hystrix 属性可以调整，以适合您的应用程序规范。

#### 测井

我们已经讨论了如何跟踪应用程序活动，呈现默认或缓存的数据，并在出错时通知用户应用程序的状态。如果我们还想在一切顺利、没有任何例外的情况下跟踪应用程序的状态(即快乐之路)，该怎么办呢？这样做的好处是，我们可以全面了解应用程序中发生的所有活动。它还可以帮助我们重放应用程序生命周期中任何给定时间点之间的所有活动。

尽管我们前面在伪代码中介绍了日志记录的概念，但我们将深入讨论实现应用程序日志记录以进行审计的方法和策略。

日志记录是审核或跟踪应用程序或系统中发生的每一项活动的一种方式。这是至关重要的，因为这可以告诉我们在应用程序生命周期的任何特定时间点发生的活动。实现这一点的方法有很多，但我将重点介绍两种常见的方法。

在第一种方法中，我们可以使用标准库，如 *Log4j* 或 *Sl4j* 实现，在代码中加入日志信息。在这里，我们将这些库作为依赖项添加到我们的项目中，然后调用打印出我们想要打印的任何内容的方法。为了避免不必要的 I/O 操作，我们可以在方法体的开头和结尾调用这些方法。

```
 public class CardDetailService {
        Logger logger = LoggerFactory.getLogger(CardDetailService.class);

        public int execute () throws CustomException {

           logger.debug("execute method started");

            int status = -1;

            try {
                //Code to execute
                status = 1;
            }catch(FileNotFoundException ex ) {
              throw new CustomException("Could not access file resource.", e);
            }
              logger.debug("execute method ended");
            return status;
        }

    } 
```

(对于 PHP Laravel 世界中的人来说，有一个附带的 Log facade，它具有与上面相同的签名调用，即 Log::debug('信息性消息。');)

这种方法告诉我们在给定的时间点应用程序中发生的活动/操作。这样做的缺点是，它不能告诉我们在此期间被操作的对象的状态。但是请注意，在上面的实现中，如果抛出异常，它也会被记录下来。

我们还可以控制日志级别来减少日志信息量，从而减少 I/O 操作。这可以在我们的项目中使用 **application.yml** 和 **logback-spring.xml** 文件或者在容器/应用服务器级别完成。请注意，我是如何根据我们之前的建议，将基于 java 包的 application.yml 文件中的日志级别设置为*错误*级别的。我们还设置了将写入日志文件的每个日志事件的输出模式，以便打印日志时间和级别、触发日志的类以及要打印的消息。在 logback-spring.xml 文件中，我们设置了 *RollingFileAppender* 属性来控制将要生成的每个日志文件的名称和大小，以及服务器上要保存的日志文件总数的最大数量和大小。

另一种日志记录方法使用一种非常常见的设计模式，称为*事件源*。在这里，我们存储所有的事件/活动以及应用程序中对象或模型的相应变化。然后，为了检索一个对象或模型的状态，我们读取与之相关的不同事件/活动，并逐个应用它们。它确保对应用程序状态的所有更改都存储为一系列事件。这与 *JPA Hibernate Envers* 实现非常相似(其中我们用 *@Audited* 注释 JPA 实体，导致在数据库中创建模型状态的镜像),除了在事件源中，这些变化与导致这些模型状态变化的实际终端用户活动相关联。

在 Laravel 世界中，这是通过添加 EventSauce 或 prooph 依赖项来实现的。您创建您的模型，然后定义事件，创建投影仪并处理副作用，如使用反应器的通知。考虑到本文的范围和我们使用的框架，我不会详细讨论这个问题，但是概念是相同的，这个模式也可以应用于其他技术栈。

在我们的例子中，使用 Spring 框架，我将展示一个高级和基本的实现。

首先，我们将如下所示的依赖关系引入到我们的 **pom.xml** 文件中:

```
 <dependency>
            <groupId>org.axonframework</groupId>
            <artifactId>axon-spring-boot-starter</artifactId>
            <version>4.1</version>
        </dependency>
        <dependency>
            <groupId>org.axonframework</groupId>
            <artifactId>axon-test</artifactId>
            <version>4.0.3</version>
            <scope>test</scope>
        </dependency> 
```

然后下载 axon 服务器，解压到您选择的文件夹中。这就是我们将用作我们的 *EventStore* 的内容。

我们继续创建聚合**(CardDetailRequestAggregate)**、事件**(CardDetailRequestCreatedEvent)**和命令**(CreateCardDetailRequestCommand)**类。然后，我们在**CardDetailRequestAggregate**中为 CardDetailRequestCreatedEvent 和 CreateCardDetailRequestCommand 类创建命令和事件处理程序方法。简而言之，command 类和用 *CommandHandler* 注释的方法负责根据用户通过 *CommandGateway* 接口的输入来确定哪个动作或事件被触发。在实现上类似于*命令*设计模式。而事件类和相应的带注释的 *EventSourcingHandler* 方法通过 *EventStore* 接口负责作为被触发的事件或动作的结果而发生的副作用。事件组件还通过 EventStore 接口负责从聚合模型中检索和持久化聚合模型。CommandGateway 接口和 EventStore 接口的用法可以在 **CardDetailCommandComponent 类**中看到。方法 **logCardDetailRequest** 是我们在上一篇文章前面的 **CardDetailService 类**中的现有函数，它被重载以接受 CardDetailRequestCreatedEvent 对象，并用 *EventHandler* 进行注释。当我们通过 CommandGateway 发送命令对象时，EventHandler 注释将触发 logCardDetailRequest 方法，如 CardDetailCommandComponent 类的**createCardDetalRequestLog**方法所示。

我们现在可以使用 *java -jar axonserver.jar* 命令和 spring boot 应用程序启动 axon 服务器。当我们到达终点时，我们的函数将按预期被调用。如果您转到 Axon 服务器仪表板，您将看到一个条目，该条目包含 CardDetailRequestCreatedEvent 事件，该事件由您发出请求的卡号和日期的值触发。这种方法的美妙之处在于，我们不再像以前调用 logCardDetailRequest 方法那样，让 CardDetailService 类中的 **verifyCardDetailHystrix** 方法承担日志记录的责任。因此，在某种程度上，我们将*单一责任*原则应用于该方法，这是干净编码的原则之一( *S.O.L.I.D* )。

因为日志记录或多或少是一个非功能性需求，所以这里要考虑的一个关键因素是确保日志记录过程不会影响应用程序性能，从而影响用户体验。出于这个原因，最好使日志记录过程异步，这就是我在返回对象的服务类中使用 CompletableFuture 的原因，这当然是 Axon 框架的默认实现。如果有资源，另一个需要考虑的问题是将事件源实现指向与主应用程序数据库不同的数据存储，作为其事件存储。在我们的例子中，这是由我们在本地运行的 Axon 服务器处理的。Axon 实现也可以配置为使用其他数据存储，如 RDBMS 或 NOSql 数据库作为其事件存储。这样，如果主应用程序数据库出现问题，您就可以拥有某种备份数据来重放您的应用程序生命周期并恢复以前生成的数据。

现在，我们的应用程序已经具备了弹性、容错能力以及异常处理和日志记录的最佳实践，这些都是架构良好的系统所必需的。别担心，我已经更新了我们的 repo [这里的](https://github.com/charles1303/binlistImpl)以包含本文中讨论的所有实现。在我的下一篇文章中，我将重点介绍**安全性**，这是构建应用程序的一个非常关键的要求，以及如何使用 *Docker* 作为虚拟化技术来实现应用程序的**可伸缩性**和**可用性**。编码快乐！
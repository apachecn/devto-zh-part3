# 使用 Spring Boot 配置回退

> 原文：<https://dev.to/lankydandev/configuring-logback-with-spring-boot-4lnk>

当您使用一个 Spring Boot 启动器依赖项时，Spring Boot 提供了开箱即用的 Logback，因为它们包括`spring-boot-starter-logging`在没有任何配置的情况下提供日志记录，并且可以根据需要进行更改。有两种方法可以提供您自己的配置，如果您只需要简单的修改，可以将它们添加到属性文件中，比如`application.properties`，或者对于更复杂的需求，您可以使用 XML 或 Groovy 来指定您的设置。在本教程中，我们将重点关注使用 XML 来定义自定义日志配置，并研究这样做的一些基础知识，以及简要了解使用属性文件来指定对 Spring Boot 提供的标准设置的简单更改。

在这篇文章中，我使用了依赖关系`spring-boot-starter`来拉进`spring-boot-starter-logging`，可以在下面找到。

```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

这将利用`spring-boot-starter-logging`，而它又依赖于。

```
<dependencies>
  <dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
    <scope>compile</scope>
  </dependency>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-to-slf4j</artifactId>
    <version>2.11.1</version>
    <scope>compile</scope>
  </dependency>
  <dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>jul-to-slf4j</artifactId>
    <version>1.7.25</version>
    <scope>compile</scope>
  </dependency>
</dependencies> 
```

Enter fullscreen mode Exit fullscreen mode

`logback-classic`包含了`logback-core`依赖项，在它们之间包含了我们开始所需的一切。上面显示的库版本是针对 Spring Boot 的`2.1.1.RELEASE`版本的。这些依赖关系在 Spring Boot 版本之间保持不变，但是它们自己的版本可能会略有不同。

在我们开始配置日志回溯之前，有必要快速浏览一下如何从一个类中向日志发送消息。

```
@Service
public class MyServiceImpl implements MyService {

  private static final Logger LOGGER = LoggerFactory.getLogger(MyServiceImpl.class);

  @Override
  public void doStuff(final String value) {
    LOGGER.trace("doStuff needed more information - {}", value);
    LOGGER.debug("doStuff needed to debug - {}", value);
    LOGGER.info("doStuff took input - {}", value);
    LOGGER.warn("doStuff needed to warn - {}", value);
    LOGGER.error("doStuff encountered an error with value - {}", value);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`LOGGER`允许使用代表每个日志记录级别的方法将消息写入日志，`trace`、`debug`、`info`、`warn`、`error`紧随其后。大括号/花括号将被作为方法参数传入的值替换。

现在，我们可以从一个相对简单的例子开始，看看如何配置 Logback 本身。下面是 logback.xml 文件，logback 将搜索该文件来配置其设置。

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>
        %d{dd-MM-yyyy HH:mm:ss.SSS} %magenta([%thread]) %highlight(%-5level) %logger{36}.%M - %msg%n
      </pattern>
    </encoder>
  </appender>

  <root level="info">
    <appender-ref ref="STDOUT"/>
  </root>

</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

它创建了一个类`ConsoleAppender`的 appender，它将像`System.out.print`通常所做的那样向控制台输出日志消息。设置了日志消息将遵循的模式，该模式提供了一些符号，这些符号由根据已发送到记录器的消息生成的值来替换。示例中包含了一些符号，下面是对每个符号的解释。

*   `%d`–以`SimpleDateFormat`允许的格式输出日志信息出现的时间。
*   `%thread`–输出出现日志消息的线程的名称。
*   `$-5level`–输出日志消息的记录级别。
*   `%logger{36}`–输出日志信息出现的包+类名。括号内的数字代表包+类名的最大长度。如果输出长度超过指定长度，它将从根包开始获取每个单独包的第一个字符的子字符串，直到输出长度小于最大长度。班名永远不会减。一个很好的图表可以在[转换 word 文档](https://logback.qos.ch/manual/layouts.html#conversionWord)中找到。
*   `%M`–输出出现日志消息的方法的名称(很明显，这很慢，不推荐使用，除非您不担心性能或方法名称对您特别重要)。
*   `%msg`–输出实际的日志信息。
*   `%n`–换行。
*   `%magenta()`–将括号中包含的输出颜色设置为洋红色(其他颜色也可用)。
*   `highlight()`–根据记录级别将括号中包含的输出颜色设置为(例如错误=红色)。

然后在根记录器中引用创建的 appender。在上面的例子中，日志级别被设置为 INFO(可以使用小写或大写)。导致它只输出在日志级别 INFO 或更高级别(INFO、WARN、ERROR)定义的消息。

Logback 中可用的日志记录级别有:

*   关闭(不输出日志)
*   错误
*   警告
*   信息
*   调试
*   找到；查出

返回到上面显示的代码片段，日志级别为 INFO，只有 INFO 或更高级别的消息(警告和错误)才会输出到日志中。因此，如果我们调用`MyService.doStuff("value")`,它将生成以下内容(与 spring 相关的日志已经从这个和所有后续输出示例中删除)。

```
28-08-2017 13:32:18.549 [main] INFO  com.lankydan.service.MyServiceImpl.doStuff - doStuff took input - value
28-08-2017 13:32:18.549 [main] WARN  com.lankydan.service.MyServiceImpl.doStuff - doStuff needed to warn - value
28-08-2017 13:32:18.549 [main] ERROR com.lankydan.service.MyServiceImpl.doStuff - doStuff encountered an error with value - value 
```

Enter fullscreen mode Exit fullscreen mode

请注意，即使跟踪和调试级别的消息被发送到记录器，它们也不会显示，因为它们低于 INFO 级别。

如果您想在`application.properties`中编写前面代码示例的等效代码，您可以按如下方式进行。

```
logging.level.root=info
logging.pattern.console=%d{dd-MM-yyyy HH:mm:ss.SSS} %magenta([%thread]) %highlight(%-5level) %logger.%M - %msg%n 
```

Enter fullscreen mode Exit fullscreen mode

当以这种形式完成时，不需要 logback.xml 文件，正如您所看到的，配置非常短，对于更简单的设置非常有用。

使用 Spring Boot 时，会提供一个默认的 Logback 配置，当您添加自己的 logback.xml 时，该配置会被覆盖。如果您希望包含 Spring Boot 的配置，可以在标记内添加以下内容。

```
<include resource="org/springframework/boot/logging/logback/base.xml"/> 
```

Enter fullscreen mode Exit fullscreen mode

有关这方面的更多信息，请参见 [Spring Boot 文档–为日志记录配置日志回溯](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-logging.html#howto-configure-logback-for-logging)。

如果您想要记录与根级别不同级别的类的消息，那么您可以为该类定义自己的记录器。这将允许您为该特定类设置日志记录级别，并指定该类特有的其他属性。下面是如何为一个单独的类定义一个记录器。

```
<logger name="com.lankydan.service.MyServiceImpl" level="debug">
  <appender-ref ref="STDOUT" />
</logger> 
```

Enter fullscreen mode Exit fullscreen mode

如果您继续运行这段代码，仍然定义根日志记录器，它将生成。

```
27-08-2017 17:02:10.248 [main] DEBUG com.lankydan.service.MyServiceImpl.doStuff - doStuff needed to debug - value
27-08-2017 17:02:10.248 [main] DEBUG com.lankydan.service.MyServiceImpl.doStuff - doStuff needed to debug - value
27-08-2017 17:02:10.248 [main] INFO  com.lankydan.service.MyServiceImpl.doStuff - doStuff took input - value
27-08-2017 17:02:10.248 [main] INFO  com.lankydan.service.MyServiceImpl.doStuff - doStuff took input - value
27-08-2017 17:02:10.248 [main] WARN  com.lankydan.service.MyServiceImpl.doStuff - doStuff needed to warn - value
27-08-2017 17:02:10.248 [main] WARN  com.lankydan.service.MyServiceImpl.doStuff - doStuff needed to warn - value
27-08-2017 17:02:10.248 [main] ERROR com.lankydan.service.MyServiceImpl.doStuff - doStuff encountered an error with value - value
27-08-2017 17:02:10.248 [main] ERROR com.lankydan.service.MyServiceImpl.doStuff - doStuff encountered an error with value - value 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，每个日志消息都生成了两次，这可能不是您想要的。要解决这个问题，需要使用`additivity="false"`。不使用`additivity="false"`将导致消息打印两次，因为根日志附加器和类级附加器都写入日志。即使根级别是`ERROR`，通过将类级别设置为`DEBUG`，它也将全局覆盖它，并将导致根附加器也写入`MyServiceImpl`类的`DEBUG`级别。下面是包含该属性的代码应该是什么样子。

```
<logger name="com.lankydan.service.MyServiceImpl" additivity="false" level="debug">
  <appender-ref ref="STDOUT" />
</logger> 
```

Enter fullscreen mode Exit fullscreen mode

另一个可能的解决方案是只设置类的日志级别，而不写入日志(由于没有定义 appender)，这相当于上面的版本，但是假设另一个日志 appender(在这种情况下是根 appender)正在写入日志以使其工作

```
<logger name="com.lankydan.service.MyServiceImpl" level="debug"/> 
```

Enter fullscreen mode Exit fullscreen mode

如果使用这两种解决方案中的任何一种，输出都会返回到预期的结果。

```
27-08-2017 16:30:47.818 [main] DEBUG com.lankydan.service.MyServiceImpl.doStuff - doStuff needed to debug - value
27-08-2017 16:30:47.834 [main] INFO  com.lankydan.service.MyServiceImpl.doStuff - doStuff took input - value
27-08-2017 16:30:47.834 [main] WARN  com.lankydan.service.MyServiceImpl.doStuff - doStuff needed to warn - value
27-08-2017 16:30:47.834 [main] ERROR com.lankydan.service.MyServiceImpl.doStuff - doStuff encountered an error with value - value 
```

Enter fullscreen mode Exit fullscreen mode

通过添加以下内容，可以在`application.properties`中编写类级日志。

```
logging.level.com.lankydan.service.MyServiceImpl=debug 
```

Enter fullscreen mode Exit fullscreen mode

包级日志记录也可以通过简单地使用包名而不是 logger 标记中的类名来定义。

```
<logger name="com.lankydan.service" additivity="false" level="debug">
  <appender-ref ref="STDOUT" />
</logger> 
```

Enter fullscreen mode Exit fullscreen mode

通过将日志添加到一个 springframework 包中，然后转移到一个类中，可以找到更多的证据。例如

```
<logger name="org.springframework.boot" level="debug">
  <appender-ref ref="STDOUT" />
</logger> 
```

Enter fullscreen mode Exit fullscreen mode

与
相比

```
<logger name="org.springframework.boot.SpringApplication" level="debug">
  <appender-ref ref="STDOUT" />
</logger> 
```

Enter fullscreen mode Exit fullscreen mode

打印出完全不同数量的日志行。也许是几百行对一两行，而`SpringApplication`日志包含在`org.springframework.boot`日志中。

`application.properties`中的包级日志遵循使用包而不是类名的相同格式。

```
logging.level.com.lankydan.service=debug 
```

Enter fullscreen mode Exit fullscreen mode

可以定义属性，允许通过配置文件重用它们，这在需要标记日志要转到的输出文件夹时很方便。

```
<property name="LOG_PATH" value="logs"/> 
```

Enter fullscreen mode Exit fullscreen mode

这个名为`LOG_PATH`的属性将在后面的例子中使用，并将使用目录`DEV_HOME/logs`，其中`DEV_HOME`是你的项目的根目录(至少我的项目是这样的……)。实际上，这可能不是保存日志的最佳位置，但是对于本教程的需要来说，这是合适的。`LOG_PATH`是一个对默认 Spring Boot 记录设置很重要的属性，但是可以创建任何名称的属性。通过添加`${LOG_PATH}`，可以在配置的其余部分访问`LOG_PATH`的值。

这种配置可以通过`application.properties`实现，因为`LOG_PATH`在 Spring Boot 很重要。

```
logging.path=logs 
```

Enter fullscreen mode Exit fullscreen mode

当您定义自己的属性/变量时，这也是有效的，允许您从代码的其余部分引用它。比如说。

```
propertyA=value
propertyB=${propertyA} # extra configuration if required 
```

Enter fullscreen mode Exit fullscreen mode

`${propertyA}`将被替换为`propertyA`的值，允许`propertyB`使用它。

要将日志保存到文件`FileAppender`中可以使用。这是一个简单的文件附加器，它将把所有日志保存到一个单一的文件中，这个文件可能会变得非常大，所以你更有可能使用我们稍后将会看到的`RollingFileAppender`。

```
<appender name="SAVE-TO-FILE" class="ch.qos.logback.core.FileAppender">

  <file>${LOG_PATH}/log.log</file>

  <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
    <Pattern>
      %d{dd-MM-yyyy HH:mm:ss.SSS} [%thread] %-5level %logger{36}.%M - %msg%n
    </Pattern>
  </encoder>

</appender> 
```

Enter fullscreen mode Exit fullscreen mode

没有什么特别的，它遵循与`ConsoleAppender`相同的结构，只是命名了一个保存日志消息的文件。值得注意的是，当保存到文件时，我已经删除了添加到编码器图案的颜色，因为它将包含不应该显示的字符，并将使日志文件混乱。这个 appender 可以像前面显示的`STDOUT` appender 一样被引用，允许它被实际使用。这将在下面显示，下面的代码片段将使用相同的代码。

```
<logger name="com.lankydan.service.MyServiceImpl" additivity="false" level="debug">
  <appender-ref ref="SAVE-TO-FILE"/>
</logger> 
```

Enter fullscreen mode Exit fullscreen mode

继续前面设置了`logging.path`的`application.properties`片段，如果其他设置没有被太多地使用，它实际上会导致日志被输出到文件(以及控制台)。因此，您可以就此打住，但是如果这样做，写入文件的模式和文件名就不受您的控制了。下面的例子将展示与上面所示的`SAVE-TO-FILE`追加器相似的配置。

```
logging.pattern.console=
logging.path=logs
logging.file=${logging.path}/log.log
logging.pattern.file=%d{dd-MM-yyyy HH:mm:ss.SSS} [%thread] %-5level %logger{36}.%M - %msg%n 
```

Enter fullscreen mode Exit fullscreen mode

这与 XML 配置的不同之处在于，该示例显示了记录器中引用的 appender 用于`MyServiceImpl`，但是上面的`application.properties`片段还将包括根记录器，因此将所有日志消息输出到文件。添加了`logging.pattern.console`来阻止它输出到控制台，以保持它与上面的 XML 代码一致(这似乎不是一个好方法，但我没有看到其他解决方案)。

`RollingFileAppender`将根据滚动策略将日志保存到不同的文件中。这很方便，因为它允许将日志输出拆分成您可以控制的各种形式。例如，您可以根据日期来分隔日志文件，以便查看过去特定日期发生的错误；根据文件大小来分隔日志文件，以便不需要搜索大量永无止境的文件；或者两者兼而有之，并根据日期和大小来分隔。下面是一些代码片段，展示了我们刚刚谈到的策略。

`TimeBasedRollingPolicy`将根据日期创建一个新文件。下面的代码将每天创建一个新文件，并使用`%d`符号将日期附加到日志文件的名称上。`%d`符号的格式很重要，因为可以从中推断出翻转时间周期。下面的示例将每天滚动一次，但是要每月滚动一次，可以使用不同的`%d{MM-yyyy}`模式，该模式排除了日期中的日部分。只要`%d`符号中的格式符合`SimpleDateFormat`所允许的格式，就可以使用不同的滚动周期，而不仅仅是每天或每月。

```
<appender name="SAVE-TO-FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">

  <file>${LOG_PATH}/log.log</file>

  <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
    <Pattern>
      %d{dd-MM-yyyy HH:mm:ss.SSS} [%thread] %-5level %logger{36}.%M - %msg%n
    </Pattern>
  </encoder>

  <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
    <fileNamePattern>
      ${LOG_PATH}/archived/log_%d{dd-MM-yyyy}.log
    </fileNamePattern>
    <maxHistory>10</maxHistory>
    <totalSizeCap>100MB</totalSizeCap>
  </rollingPolicy>

</appender> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我已经包含了一些对`TimeBasedRollingPolicy`可用的属性。`maxHistory`指定归档日志文件在被自动删除之前将保留多长时间。它们的保留时间取决于文件名中指定的转存时间段，因此在上面的示例中，转存时间段是每天，允许在删除归档日志之前最多存储 10 天的归档日志。`totalSizeCap`限制了所有归档日志文件的最大大小，它要求在删除归档文件时将`maxHistory`属性设置为`maxHistory`优先于`totalSizeCap`。

这种配置超出了在`application.properties`文件中所能做的范围，下面的例子也是如此。尽管默认配置允许日志文件在达到 10MB 时翻转，并且最多允许 7 个归档日志文件。

要仅滚动文件大小，需要使用滚动策略`FixedWindowRollingPolicy`和触发策略`SizeBasedTriggeringPolicy`。在前面的示例中，日志在转存时被保存到归档文件夹中，但是对于此策略，我没有保存它们，因为日志的分离主要是为了帮助它们更容易遍历，因为文件较小。

```
<appender name="SAVE-TO-FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">

  <file>${LOG_PATH}/log.log</file>

  <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
    <Pattern>
      %d{dd-MM-yyyy HH:mm:ss.SSS} [%thread] %-5level %logger{36}.%M - %msg%n
    </Pattern>
  </encoder>

  <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
    <fileNamePattern>
      ${LOG_PATH}/log_%i.log
    </fileNamePattern>
    <minIndex>2</minIndex>
    <maxIndex>3</maxIndex>
  </rollingPolicy>

  <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
    <maxFileSize>1KB</maxFileSize>
  </triggeringPolicy>

</appender> 
```

Enter fullscreen mode Exit fullscreen mode

在`FixedWindowRollingPolicy`中找到的`minIndex`和`maxIndex`的可选属性指定了`%i`在日志文件名中可以取的最小值和最大值。因此，在上面的例子中，当日志翻转时，它们可以取名字`log_2.log`和`log_3.log`(虽然从 2 开始很奇怪，只是为了清楚起见，通常从 1 开始)。生成日志文件的过程如下(以上面的代码片段为例)；`log.log`文件将接受所有新的日志输入，当到达`maxFileSize`时，`log.log`被重命名为存档文件`log_2.log`并创建一个新的`log.log`文件，当`log_2.log`也达到最大值时，所有日志文件被重命名并随着一个新的`log.log`文件被再次创建。如果未设置`maxIndex`,此过程将继续，但当设置时，具有指定最大索引的日志文件将在应创建另一个归档文件时被删除(它包含最旧的消息)。按照上述相同的例子，这意味着当应该创建`log_4.log`时，删除`log_3.log`并相应地重命名所有其他日志。

如果你对我上面写的关于文件如何滚动的内容感到困惑，不要担心，因为即使是我也认为在写完那个解释之后，它可以做得更好。所以下面我再次尝试说明它是如何工作的(希望更容易理解)。

*   `log.log`达到最大文件大小- > `log.log`重命名为`log_2.log`并创建新的`log.log`
*   `log_2.log`达到最大文件大小- > `log_2.log renamed to log_3.log`，`log.log`命名为`log_2.log`，新的`log.log`被创建
*   `log_3.log`达到最大文件大小- > `log_3.log`被删除，`log_2.log`改名为`log_3.log`，`log.log`改名为`log_2.log`，新的`log.log`被创建

如果我在向您解释这一过程时仍然做得不好，那么请查看[fixed windowrolling policy documents](https://logback.qos.ch/manual/appenders.html#FixedWindowRollingPolicy),如果我失败了，它将有望帮助您了解这一过程…

`SizeAndTimeBasedRollingPolicy`取上述两个例子的一部分，允许它在大小和时间上翻转。注意，它同时使用了`%d`和`%i`符号，分别在文件名中包含日期和日志号。

```
<appender name="SAVE-TO-FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">

    <file>${LOG_PATH}/log.log</file>

    <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <Pattern>
        %d{dd-MM-yyyy HH:mm:ss.SSS} [%thread] %-5level %logger{36}.%M - %msg%n
      </Pattern>
    </encoder>

    <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
      <fileNamePattern>
        ${LOG_PATH}/archived/log_%d{dd-MM-yyyy}_%i.log
      </fileNamePattern>
      <maxFileSize>10MB</maxFileSize>
      <maxHistory>10</maxHistory>
      <totalSizeCap>100MB</totalSizeCap>
    </rollingPolicy>

</appender> 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，它包含了`maxFileSize`、`maxHistory`和`totalSizeCap`，为 it 提供了对单个文件大小以及文件集合的控制。因此，上面的示例将保留 10 天的历史记录，分成 10MB 的文件，当所有文件的总大小达到 100MB 时，最早的文件将被删除。

现在我们已经了解了如何定义多个可以输出到控制台或文件的 appenders，我们可以将它们组合起来，一次输出到两种形式。只需在记录器中引用多个 appenders。

```
<logger name="com.lankydan.service.MyServiceImpl" additivity="false" level="debug">
  <appender-ref ref="STDOUT"/>
  <appender-ref ref="SAVE-TO-FILE"/>
</logger> 
```

Enter fullscreen mode Exit fullscreen mode

所以现在这个记录器将通过`STDOUT`输出到控制台，并使用`SAVE-TO-FILE` appender 输出到文件。

通过`application.properties`可以实现类似的配置。如果您返回到页面，您可能能够自己找出如何做，因为前面的示例添加了一行额外的代码，以防止它打印到控制台和文件。同样，这将包含来自根日志记录器的日志消息，而不仅仅是上面代码片段中的`MyServiceImpl`。

```
logging.path=logs
logging.file=${logging.path}/log.log
logging.pattern.file=%d{dd-MM-yyyy HH:mm:ss.SSS} [%thread] %-5level %logger{36}.%M - %msg%n 
```

Enter fullscreen mode Exit fullscreen mode

Spring Boot 在使用 Logback 时提供的一个有用特性是能够在不同环境之间分离配置。因此，如果您想在开发环境中保存到文件并打印到控制台，但在生产环境中只打印到文件，这可以轻松实现。

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

  <!-- config for STDOUT and SAVE-TO-FILE -->

  <springProfile name="dev">
    <root level="info">
      <appender-ref ref="STDOUT"/>
      <appender-ref ref="SAVE-TO-FILE"/>
    </root>
    <logger name="com.lankydan.service.MyServiceImpl" additivity="false" level="debug">
      <appender-ref ref="STDOUT"/>
      <appender-ref ref="SAVE-TO-FILE"/>
    </logger>
  </springProfile>

  <springProfile name="prod">
    <root level="info">
      <appender-ref ref="SAVE-TO-FILE"/>
    </root>
    <logger name="com.lankydan.service.MyServiceImpl" additivity="false" level="error">
      <appender-ref ref="SAVE-TO-FILE"/>
    </logger>
  </springProfile>

</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

实现这一点的第一步是将 logback.xml 文件重命名为 logback-spring.xml，允许使用`springProfile`标记。在这个标签中，可以提供一个名称，这个名称可以通过属性、环境变量或 VM 选项来设置。下面是如何将`springProfile`的名字设置为`dev`，它已经被用来表示一个开发环境。

在 application.properties 中设置或作为环境变量

```
spring.profiles.active=dev 
```

Enter fullscreen mode Exit fullscreen mode

或者作为虚拟机选项

```
-Dspring.profiles.active=dev 
```

Enter fullscreen mode Exit fullscreen mode

现在，当应用程序运行时，`dev`的`springProfile`将被用于将日志输出到控制台和文件。如果随后将其推向生产，则需要将属性设置为`prod`，这将把配置更改为被认为合适的配置，例如只将日志写入文件，并可能更改所有或某些类/包的日志记录级别。

也可以通过`application.properties`提供类似的配置。嗯，实际上不是`application.properties`，而是来自`application-dev.properties`和`application-prod.properties`，它们是每个环境的独立属性文件。遵循`application-{environment}.properties`的命名约定，其中`{environment}`被替换为环境名称。根据您的 VM 选项或环境变量，可以选择其中之一，就像通过 logback-spring.xml 中的`springProfile`完成一样。

应用程序开发属性

```
logging.level.root=info
logging.level.com.lankydan.service=debug
logging.path=logs
logging.file=${logging.path}/log.log
logging.pattern.file=%d{dd-MM-yyyy HH:mm:ss.SSS} [%thread] %-5level %logger{36}.%M - %msg%n
logging.pattern.console=%d{dd-MM-yyyy HH:mm:ss.SSS} %magenta([%thread]) %highlight(%-5level) %logger.%M - %msg%n 
```

Enter fullscreen mode Exit fullscreen mode

应用产品属性

```
logging.level.root=info
logging.level.com.lankydan.service=error
logging.path=logs
logging.file=${logging.path}/log.log
logging.pattern.file=%d{dd-MM-yyyy HH:mm:ss.SSS} [%thread] %-5level %logger{36}.%M - %msg%n
logging.pattern.console= 
```

Enter fullscreen mode Exit fullscreen mode

我想我应该在这一点上结束这篇文章，因为它比我最初预期的要长得多。也就是说，Logback 和 Spring Boot 还可以做很多我在这里没有提到的事情。总之，通过本教程，您应该已经了解了如何在 Spring Boot 中使用 Logback，包括如何使用属性文件来更改 Spring Boot 提供的默认设置，以及如何更进一步，通过 logback.xml 和 logback-spring.xml 使用 Logback 来创建您自己的定制配置。并且对属性文件中的配置所能提供的限制有所了解，这样您就知道什么时候应该切换到直接使用 logback 来让您到达终点。

这些例子中使用的代码可以在我的 [GitHub](https://github.com/lankydan/logback-with-springboot-config) 上找到。

要了解我的新帖子，您可以通过 [@LankyDanDev](https://twitter.com/LankyDanDev) 关注我。

最初发布于 2017 年 8 月 31 日，更新于 2019 年 1 月 9 日。
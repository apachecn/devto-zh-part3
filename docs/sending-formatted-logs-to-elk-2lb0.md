# 向 ELK 发送格式化日志

> 原文：<https://dev.to/miranda/sending-formatted-logs-to-elk-2lb0>

如果你使用 ELK 和 java，你可能会使用 elastic beat 来监听服务器中的日志文件，解析并发送到你的日志存储器 elasticsearch。然而，由于您的应用程序正在生成日志，它可以将其格式化为预期的 Kibana json 文档，并降低日志处理管道的复杂性。

loggingeventassyncdisruptionappender 能够将日志收集到一个环形缓冲区中，并将其异步发送到 Redis 服务器，它将在那里等待几秒钟，直到 logstash 工作人员可以将它们发送到 elasticsearch 服务器。在这个例子中，一个使用 Spring Boot 的应用程序正在用通讯员*spring . profiles . active*environment、prod、dev 等标记日志。您可以将其他暴露的字段和[包含在文档](https://github.com/logstash/logstash-logback-encoder#standard-fields)中。

Logback logstash 编码器:*[https://github.com/logstash/logstash-logback-encoder](https://github.com/logstash/logstash-logback-encoder)*

```
<appender name="REDIS_APPENDER" class="net.logstash.logback.appender.LoggingEventAsyncDisruptorAppender">
            <ringBufferSize>131072</ringBufferSize>
            <appender class="de.idealo.logback.appender.RedisBatchAppender">
                <connectionConfig>
                    <scheme>NODE</scheme>
                    <host>redis.host</host>
                    <port>6379</port>
                    <password>password</password>
                    <key>your_app_logs</key>
                </connectionConfig>
                <maxBatchMessages>1000</maxBatchMessages>
                <maxBatchSeconds>10</maxBatchSeconds>
                <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
                    <providers>
                        <mdc/>
                        <pattern>
                            <pattern>
                                {
                                "timestamp": "%d{yyyy-MM-dd'T'HH:mm:ss.SSSZZ}",
                                "message": "%message",
                                "logger": "%logger",
                                "thread": "%thread",
                                "level": "%level",
                                "host": "${HOSTNAME}",
                                "env": "${spring.profiles.active}",
                                "app": "front"
                                }
                            </pattern>
                        </pattern>
                        <stackTrace>
                            <throwableConverter class="net.logstash.logback.stacktrace.ShortenedThrowableConverter">
                                <maxDepthPerThrowable>30</maxDepthPerThrowable>
                                <maxLength>4096</maxLength>
                                <shortenedClassNameLength>20</shortenedClassNameLength>
                                <rootCauseFirst>true</rootCauseFirst>
                            </throwableConverter>
                        </stackTrace>
                    </providers>
                </encoder>
            </appender>
        </appender> 
```
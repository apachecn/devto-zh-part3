# Ktor 应用程序的正常关闭

> 原文：<https://dev.to/viniciusccarvalho/graceful-shutdown-of-ktor-applications-1h53>

# 做完后清理

打扫卫生是你习惯于一天多次告诉你的孩子的事情，然而我们中没有多少人记得用我们的资源做这件事。

随着我编写更多的 kotlin 和 ktor 应用程序，最近困扰我的一件事是，在我的应用程序被 kubernetes 终止(缩小)后，我需要清理一些临时资源。

在我的场景中，我在启动时创建了一些临时的 [Google Cloud Pubsub](https://cloud.google.com/pubsub) 订阅，我必须在缩减操作期间删除它们。

在阅读 ktor 的文档时，我发现它内置了对[应用事件](https://ktor.io/advanced/events.html)的支持。应该很容易吧？

## 以 Ktor 开头

启动 ktor 项目最简单的方法是访问 [http://start.ktor.io](http://start.ktor.io) ，遵循 spring starter 的相同原则，它会为您生成一个包含所有依赖项甚至一些基本配置文件的项目。

* * *

**重要**

我对所有 ktor 项目做的一个改变是使用`shadow`插件启用`fat jar`特性。我更喜欢这种方式来分发我的应用程序。只需修改你的`build.gradle` `buildscript`和`plugins`部分如下:

```
buildscript {
    repositories {
        jcenter()
    }

    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
        classpath 'com.github.jengelman.gradle.plugins:shadow:4.0.3'
    }
}

apply plugin: 'kotlin'
apply plugin: 'application'
apply plugin: "com.github.johnrengelman.shadow" 
```

* * *

如果您在 IDE 上导入项目，那么您的主类应该类似于下面这样(根据您在开始时选择的特性，可能会有所不同)。

```
fun main(args: Array<String>): Unit = io.ktor.server.netty.EngineMain.main(args)

@Suppress("unused") // Referenced in application.conf
@kotlin.jvm.JvmOverloads
fun Application.module(testing: Boolean = false) {
    install(ContentNegotiation) {
        jackson {
            enable(SerializationFeature.INDENT_OUTPUT)
        }
    }
    install(CallLogging) {
        level = Level.INFO
        filter { call -> call.request.path().startsWith("/") }
    }
    install(DefaultHeaders) {
        header("X-Engine", "Ktor") // will send this header with each response
    }
    routing {
        get("/") {
            call.respondText("HELLO WORLD!", contentType = ContentType.Text.Plain)
        }
        get("/json/jackson") {
            call.respond(mapOf("hello" to "world"))
        }
    }
} 
```

你现在可以运行`./gradlew build`来在`build/libs/<APPNAME>-<VERSION>-all.jar`上打包一个胖 jar 文件，只要运行这个 jar 文件，你就会看到:

```
[main] INFO  Application - No ktor.deployment.watch patterns specified, automatic reload is not active
[main] INFO  Application - Responding at http://0.0.0.0:8080
[main] INFO  Application - Application started: io.ktor.application.Application@79517588 
```

## 启用生命周期回调

正如我之前说过的，我们可以订阅我们示例中的特定`ApplicationEvents`，让我们来看看`ApplicationStarted`和`ApplicationStopped`

如果你改变你的主类来增加两行:

```
fun Application.module(testing: Boolean = false) {
    environment.monitor.subscribe(ApplicationStarted){
        println("My app is ready to roll")
    }
    environment.monitor.subscribe(ApplicationStopped){
        println("Time to clean up")
    } 
```

现在您再次运行它:

```
[main] INFO  Application - No ktor.deployment.watch patterns specified, automatic reload is not active
[main] INFO  Application - Responding at http://0.0.0.0:8080
My app is ready to roll
[main] INFO  Application - Application started: io.ktor.application.Application@79517588 
```

我们可以看到我们的回调已经被调用，但是当您使用 CTRL+C 终止您的进程时会发生什么呢？

结果是我们的回调没有机会执行。那么是哪里出了问题呢？

## 了解 ktor 如何启动

Ktor 有几种方法可以引导服务器。这里使用的是使用`EngineMain`类作为入口点。这个类将搜索一个`application.conf`文件，在我们的例子中是这样的:

```
ktor {
    deployment {
        port = 8080
        port = ${?PORT}
    }
    application {
        modules = [ io.igx.kotlin.ApplicationKt.module ]
    }
} 
```

`module`部分定义了我们如何配置服务器，但是它的启动/停止行为驻留在`EngineMain`中。

在 SIGINT 的情况下,`Netty`版本(我没有在其他服务器实现中尝试过)不会等待应用程序终止，因此我们的订户不可能被调用。

## 如何修复

另一种引导服务器的方法是使用`embeddedServer`函数。为了解决这个问题，这就是我们将要做的事情。

```
fun main() {
    val server = embeddedServer(Netty, port = 8080){
        module()
    }.start(false)
    Runtime.getRuntime().addShutdownHook(Thread {
        server.stop(1, 5, TimeUnit.SECONDS)
    })
    Thread.currentThread().join()
} 
```

注意，我们重用了生成的应用程序文件中的所有模块配置，但是现在我们手动引导服务器，并且通过添加一个在服务器上调用`stop`方法的关闭挂钩，我们可以优雅地等待我们的订户运行。

在打包和运行这个版本之前，不要忘记更改 build.gradle 文件的`mainClassName`属性。

现在运行并使用 SIGINT:
杀死它

```
[main] INFO  ktor.application - No ktor.deployment.watch patterns specified, automatic reload is not active
[main] INFO  ktor.application - Responding at http://0.0.0.0:8080
My app is ready to roll
[main] INFO  ktor.application - Application started: io.ktor.application.Application@327514f
START all SERVERS
[Thread-1] INFO  ktor.application - Application stopping: io.ktor.application.Application@327514f
Time to clean up
[Thread-1] INFO  ktor.application - Application stopped: io.ktor.application.Application@327514f 
```

从日志中的`Time to clean up`消息可以看出，我们的方法在终止期间被成功调用。

# 最后的想法

这个非常简单的改变对我的用例来说意义重大，清理资源非常重要，尤其是在一个实例每天可以来来去去很多次的弹性环境中。

我已经联系了 ktor 团队，要求将这一点加入到`EngineMain`方法中，希望他们将来会增加这个特性。

快乐编码
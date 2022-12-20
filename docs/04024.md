# 带 http4s 教程的护栏

> 原文：<https://dev.to/kkreuning/guardrail-with-http4s-tutorial-55p2>

本文介绍了如何使用 Twilio 的[护栏](https://guardrail.dev)安全地生成和维护一个 [http4s](https://http4s.org) REST API 服务器。我想写这篇文章，作为我未来的自己和其他对这项技术感兴趣的人的参考。

[![real guardrail in its natural habitat](img/0f301dcc81e8e61356094a36bdaba96e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CGWT0VjO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kkreuning.github.io/asseimg/guardrail.jpg)

## TL；博士-我只想要密码！

在[护栏-http4s-教程](https://github.com/kkreuning/guardrail-http4s-example)找到完成的项目

## 护栏的情况

引用护栏网的网站:

> 护栏是一个代码生成工具，能够读取 OpenAPI/Swagger 规范文件并生成 Scala 源代码，主要针对 akka-http 和 http4s web 框架，使用 circe 进行 JSON 编码/解码。

这很好，但是 Swagger 已经能够生成 Scala 代码了，为什么护栏还存在呢？

简而言之**类型安全**，正如我们将看到的，在处理 Guardrail 生成的代码时不可能做错事情，因为编译器的类型检查器会防止我们犯错误。

更重要的是，当使用护栏时，我们被迫首先开发 API，我们的 OpenAPI/Swagger 规范是我们 API 的唯一来源。

现在，事不宜迟，让我们用护栏搭建一个 http4s 服务器吧！

## 先决条件和设置

对于本教程，我们将需要 [sbt](http://www.scala-sbt.org/1.0/docs/Setup.html) 。为了节省时间，我们将使用 http4s g8 模板，在您的终端中执行以下操作:

```
> sbt new http4s/http4s.g8 
```

我将使用默认值。如果您想要不同的名称，本教程中的路径和文件名可能会有所不同。当`sbt`完成后，打开新创建的目录。从删除标准路由开始，因为我们不打算使用它们。删除下列文件和目录:

*   `src/main/scala/com/example/quickstart/HelloWorld.scala`
*   `src/main/scala/com/example/quickstart/Jokes.scala`
*   `src/main/scala/com/example/quickstart/QuickstartRoutes.scala`
*   `src/test`

在`QuickstartServer.scala`中，删除对您刚刚删除的文件的引用，这样我们就剩下以下文件:

```
package com.example.quickstart

import cats.effect.{ConcurrentEffect, Effect, ExitCode, IO, IOApp, Timer, ContextShift}
import cats.implicits._
import fs2.Stream
import org.http4s.client.blaze.BlazeClientBuilder
import org.http4s.HttpRoutes
import org.http4s.implicits._
import org.http4s.server.blaze.BlazeServerBuilder
import org.http4s.server.middleware.Logger
import scala.concurrent.ExecutionContext.global

object QuickstartServer {

  def stream[F[_]: ConcurrentEffect](implicit T: Timer[F], C: ContextShift[F]): Stream[F, Nothing] = {
    for {
      client <- BlazeClientBuilder[F](global).stream

      httpApp = (
        HttpRoutes.empty[F] // We will add our own routes here later
      ).orNotFound

      finalHttpApp = Logger.httpApp(true, true)(httpApp)

      exitCode <- BlazeServerBuilder[F]
        .bindHttp(8080, "0.0.0.0")
        .withHttpApp(finalHttpApp)
        .serve
    } yield exitCode
  }.drain
} 
```

现在让我们看看我们是否还能运行应用程序:

```
> sbt run 
```

应用程序应该会编译，我们应该会看到如下输出:

```
[scala-execution-context-global-92] INFO o.h.s.b.BlazeServerBuilder - http4s v0.20.0 on blaze v0.14.0 started at http://[0:0:0:0:0:0:0:0]:8080/ 
```

恭喜你！我们有一个工作的 http4s 应用程序。现在它只是坐在那里什么也不做，所以让我们创建一些端点！

## API 规范和护栏 sbt 插件

在本教程中，我们将重新创建之前删除的 Hello World 端点。将以下规格另存为`src/main/resources/api.yaml` :

```
openapi: "3.0.0"
info:
  title: http4s Guardrail example
  version: 0.0.1
tags:
  - name: hello
paths:
  /hello:
    get:
      tags: [hello]
      x-scala-package: hello
      operationId: getHello
      summary: Returns a hello message
      responses:
        200:
          description: Hello message
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/HelloResponse'
components:
  schemas:
    HelloResponse:
      type: object
      properties:
        message:
          type: string
      required:
        - message 
```

规范中需要注意的两点是:

1.  我们必须为我们的操作提供一个`operationId`
2.  提供一个`x-scala-package`值来将相关的操作组合在一起是一个很好的做法。

对于代码生成，我们将使用[SBT-guardian 插件](https://github.com/twilio/sbt-guardrail)，向`project.plugins.sbt`添加下面一行:

```
addSbtPlugin("com.twilio" % "sbt-guardrail" % "0.46.0") 
```

向`build.sbt`添加以下行:

```
guardrailTasks in Compile := List(
  ScalaServer(
    specPath = (Compile / resourceDirectory).value / "api.yaml",
    pkg = "com.example.quickstart.endpoints",
    framework = "http4s",
    tracing = false
  )
) 
```

并将以下依赖关系添加到现有的`libraryDependencies` :

```
"io.circe" %% "circe-java8" % CirceVersion, 
```

要查看代码生成器的运行情况，运行:

```
> sbt compile 
```

看看`target/scala-2.12/src_managed/main/com/example/quickstart`目录，这是我们生成的代码所在的位置，让我们看看那里有什么:

`definitions`目录包含 case 类，它们被用作请求和响应主体以及序列化和反序列化的帮助器代码。例如，我们在 API 规范中定义了一个`HelloResponse`模式，我们得到了一个相应的`HelloResponse.scala`文件。

`hello`包的名字来源于`x-scala-package`值。`hello/Routes.scala`包含一个`trait`，它带有我们必须实现的方法。该特征中的方法对应于 API 规范中的操作/ `operationId`。

`Http4sImplicits.scala`和`Implicits.scala`包含暗示。他们在那里把一切粘在一起。

到目前为止一切顺利，现在我们必须实际实现我们生成的端点。

## 实现生成端点

在`src/main/scala/com/example/quickstart/endpoints/hello/HelloHandlerImpl.scala`创建一个新文件，内容如下:

```
package com.example.quickstart.endpoints.hello

import cats.Applicative
import cats.implicits._
import com.example.quickstart.endpoints.definitions.HelloResponse

class HelloHandlerImpl[F[_] : Applicative]() extends HelloHandler[F] {
  override def getHello(respond: GetHelloResponse.type)(): F[GetHelloResponse] = {
    for {
      message <- "Hello, world".pure[F]
    } yield respond.Ok(HelloResponse(message))
  }
} 
```

我们在这里所做的是实现生成的`HelloHandler`。看着`getHello`方法的签名我们可以看到护栏天才，一切都是打字！如果您仍然不同意，请尝试重写代码，用其他东西而不是`200 OK`来响应，并编译它(提示，您不能这样做)。

在我们忘记之前，让我们将我们的 hello routes 添加到应用程序中，打开`src/main/scala/com/example/quickstart/QuickstartServer.scala`并替换为:

```
package com.example.quickstart

import cats.effect.{ConcurrentEffect, Effect, ExitCode, IO, IOApp, Timer, ContextShift}
import cats.implicits._
import com.example.quickstart.endpoints.hello.{HelloHandlerImpl, HelloResource}
import fs2.Stream
import org.http4s.client.blaze.BlazeClientBuilder
import org.http4s.HttpRoutes
import org.http4s.implicits._
import org.http4s.server.blaze.BlazeServerBuilder
import org.http4s.server.middleware.Logger
import scala.concurrent.ExecutionContext.global

object QuickstartServer {

  def stream[F[_]: ConcurrentEffect](implicit T: Timer[F], C: ContextShift[F]): Stream[F, Nothing] = {
    for {
      client <- BlazeClientBuilder[F](global).stream

      httpApp = (
        new HelloResource().routes(new HelloHandlerImpl())
      ).orNotFound

      finalHttpApp = Logger.httpApp(true, true)(httpApp)

      exitCode <- BlazeServerBuilder[F]
        .bindHttp(8080, "0.0.0.0")
        .withHttpApp(finalHttpApp)
        .serve
    } yield exitCode
  }.drain
} 
```

改变的是我们在`httpApp`上加了一行`new HelloResource().routes(new HelloHandlerImpl())`。

现在我们可以再次运行应用程序:

```
> sbt run 
```

一旦它开始运行，我们就可以使用`curl` :
测试我们的端点

```
> curl http://localhost:8080/hello
{"message":"Hello, world"}% 
```

🎉成功！现在世界上一切都好。也就是说，直到 API 需求改变…

## API 规范变更

护栏使得改变 API 规范变得轻而易举。之前我说过我们正在重新创建由 g8 http4s 模板提供的标准 hello world 路由。但是我们遗漏了一些东西，也就是说，我们希望`/hello`端点用任何给定的名称进行响应。让我们将`src/main/resources/api.yaml`处的 API 规范更改为

```
openapi: "3.0.0"
info:
  title: http4s Guardrail example
  version: 0.0.1
tags:
  - name: hello
paths:
  /hello:
    get:
      tags: [hello]
      x-scala-package: hello
      operationId: getHello
      summary: Returns a hello message
      parameters:
        - $ref: '#/components/parameters/NameParam'
      responses:
        200:
          description: Hello message
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/HelloResponse'
components:
  parameters:
    NameParam:
      name: name
      in: query
      description: Name to greet
      schema:
        type: string
  schemas:
    HelloResponse:
      type: object
      properties:
        message:
          type: string
      required:
        - message 
```

改变的是我们给`/hello`端点增加了一个参数。

如果我们通过调用:
再次触发代码生成器

```
> sbt compile 
```

护栏将通知我们，我们正在改变一个现有的文件:

```
Warning:
  The file ~/Developer/quickstart/target/scala-2.12/src_managed/main/com/example/quickstart/endpoints/hello/Routes.scala contained different content than was expected.

  Existing file: ): F[GetHelloResponse] }\nclass HelloResource[F[_]]
  New file : name: Option[String] = None): F[GetHelloResponse] 
```

接着是一堆编译器错误。这实际上是编译器告诉我们，我们需要改变我们的实现，因为它与生成的代码不同步。很好。打开`src/main/scala/com/example/quickstart/endpoints/HelloHandlerImpl.scala`并替换为以下内容:

```
package com.example.quickstart.endpoints.hello

import cats.Applicative
import cats.implicits._
import com.example.quickstart.endpoints.definitions.HelloResponse

class HelloHandlerImpl[F[_] : Applicative]() extends HelloHandler[F] {
  override def getHello(respond: GetHelloResponse.type)(name: Option[String] = None): F[GetHelloResponse] = {
    for {
      message <- s"Hello, ${name.getOrElse("world")}".pure[F]
    } yield respond.Ok(HelloResponse(message))
  }
} 
```

现在再次运行应用程序:

```
> sbt run 
```

一旦它开始运行，我们可以尝试得到一个个性化的问候:

```
> curl http://localhost:8080/hello\?name\=Kay
{"message":"Hello, Kay"}% 
```

这就是更新您的 API 规范是多么容易！

## 结论

在几分钟内，我们就能够创建一个简单的 REST API 服务器，它具有根据 API 规范生成的安全类型端点。更好的是，我们现在有了构建应用程序的基础。正如我们所看到的，护栏迫使我们忠于我们的 API 规范，从而使我们的生活变得更加轻松。

IMO 护栏已经生产就绪，但有时边缘会很粗糙。如果你喜欢护栏，他们找[投稿](https://github.com/twilio/guardrail/blob/master/CONTRIBUTING.md)。

可以在[护栏-http4s-教程](https://github.com/kkreuning/guardrail-http4s-example)找到完成的项目。

感谢您的阅读。
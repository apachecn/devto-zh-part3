# Java 中的无框架 REST API

> 原文：<https://dev.to/piczmar_0/framework-less-rest-api-in-java-1jbl>

[![Photo by Hieu Vu Minh on Unsplash](img/4dba961bab1a5de2f815e79d9438dd18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hh3IXbP7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bp5s7ju2fnxa5t7p7ckx.jpg)

Java 生态系统充满了框架和库。当然没有 JavaScript 世界那么多，也不会很快过时，但这个事实让我敢说我们已经忘记了如何创建一个完全无框架的应用程序。

你可能会说:[弹簧](https://spring.io/)是一个标准，为什么要重新发明一个轮子。 [Spark](http://sparkjava.com/) 是一个不错的小型 REST 框架。
[光息-4j](https://doc.networknt.com/getting-started/light-rest-4j/) 又是一个。

我告诉你，当然，你是对的。一个框架给你带来了很多额外的东西，但是你也获得了很多魔法、学习开销、你很可能不会用到的额外特性以及 bug。

服务中的外部代码越多，开发人员犯错误的几率就越大。

开源社区非常活跃，框架中的这些错误很有可能会很快得到修复，但我仍然鼓励你重新考虑是否真的需要一个框架。

如果你正在做一个小的服务或者一个控制台应用程序，也许你可以没有它。

坚持使用纯 java 代码，您会得到(或失去)什么？想想这些:

*   你的代码可以更干净、更可预测(或者如果你是一个糟糕的编码者，你的代码会一塌糊涂)
*   你将对你的代码有更多的控制权，你将不会被框架所约束(但是你将不得不经常为框架给你的现成的东西写自己的代码)
*   您的应用程序将会部署和启动得更快，因为框架代码不需要初始化许多类(或者如果您搞砸了，根本不启动，例如多线程)
*   如果你在 Docker 上部署你的应用程序，你的图片会更薄，因为你的罐子也会更薄

我做了一个小实验，尝试开发一个无框架的 REST API。

我认为从学习的角度来看，这可能很有趣，而且有点令人耳目一新。

当我开始构建它的时候，我经常遇到错过 Spring 提供的一些现成特性的情况。

在那个时候，我没有打开另一个 Spring 功能，而是重新思考并自己开发它。

对于真正的商业案例，我可能更喜欢使用 Spring，而不是重新发明一个轮子。

尽管如此，我相信这是一次非常有趣的经历。

## 开始。

我将一步一步地完成这个练习，但并不总是在这里粘贴完整的代码。你总是可以从 [git 库](https://github.com/piczmar/pure-java-rest-api)的一个单独的分支中检查每一步。

用初始的`pom.xml`文件创建一个新的 Maven 项目:

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.consulner.httpserver</groupId>
  <artifactId>pure-java-rest-api</artifactId>
  <version>1.0-SNAPSHOT</version>

  <properties>
    <java.version>11</java.version>
    <maven.compiler.source>${java.version}</maven.compiler.source>
    <maven.compiler.target>${java.version}</maven.compiler.target>
  </properties>

  <dependencies></dependencies>
</project> 
```

包括 java.xml.bind 模块依赖，因为这些模块在 JDK 11 中被 JEP-320 移除了。

```
<dependency>
    <groupId>org.glassfish.jaxb</groupId>
    <artifactId>jaxb-runtime</artifactId>
    <version>2.4.0-b180608.0325</version>
</dependency> 
```

和 [Jackson](https://medium.com/r/?url=https%3A%2F%2Fgithub.com%2FFasterXML%2Fjackson) 进行 JSON 连载

```
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.7</version>
</dependency> 
```

然后我们将使用 [Lombok](https://medium.com/r/?url=https%3A%2F%2Fprojectlombok.org%2F) 来简化 POJO 类:

```
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.18.0</version>
  <scope>provided</scope>
</dependency> 
```

以及 [vavr](https://medium.com/r/?url=http%3A%2F%2Fwww.vavr.io%2F) 用于功能编程设施

```
<dependency>
  <groupId>io.vavr</groupId>
  <artifactId>vavr</artifactId>
  <version>0.9.2</version>
</dependency> 
```

我从空`Application`主班开始。

你可以从 [step-1](https://medium.com/r/?url=https%3A%2F%2Fgithub.com%2Fpiczmar%2Fpure-java-rest-api%2Ftree%2Fstep-1) 分支得到一个初始代码。

## 第一个端点

web 应用的起点是`com.sun.net.httpserver.HttpServer`类。
最简单的`/api/hello`端点可能如下:

```
import java.io.IOException;
import java.io.OutputStream;
import java.net.InetSocketAddress;

import com.sun.net.httpserver.HttpServer;

class Application {

    public static void main(String[] args) throws IOException {
        int serverPort = 8000;
        HttpServer server = HttpServer.create(new InetSocketAddress(serverPort), 0);
        server.createContext("/api/hello", (exchange -> {
            String respText = "Hello!";
            exchange.sendResponseHeaders(200, respText.getBytes().length);
            OutputStream output = exchange.getResponseBody();
            output.write(respText.getBytes());
            output.flush();
            exchange.close();
        }));
        server.setExecutor(null); // creates a default executor
        server.start();
    }
} 
```

当您运行主程序时，它将在端口`8000`启动 web 服务器，并暴露出正在打印`Hello!`的第一个端点，例如使用 curl:

```
curl localhost:8000/api/hello 
```

从 [step-2](https://medium.com/r/?url=https%3A%2F%2Fgithub.com%2Fpiczmar%2Fpure-java-rest-api%2Ftree%2Fstep-2) 分支自己尝试一下。

## 支持不同的 HTTP 方式

我们的第一个端点工作起来很有魅力，但是您会注意到，无论您使用哪种 HTTP 方法，它的响应都是一样的。
例如:

```
curl -X POST localhost:8000/api/hello
curl -X PUT localhost:8000/api/hello 
```

在没有框架的情况下自己构建 API 的第一个问题是，我们需要添加自己的代码来区分这些方法，例如:

```
 server.createContext("/api/hello", (exchange -> {

            if ("GET".equals(exchange.getRequestMethod())) {
                String respText = "Hello!";
                exchange.sendResponseHeaders(200, respText.getBytes().length);
                OutputStream output = exchange.getResponseBody();
                output.write(respText.getBytes());
                output.flush();
            } else {
                exchange.sendResponseHeaders(405, -1);// 405 Method Not Allowed
            }
            exchange.close();
        })); 
```

现在再次尝试请求:

```
curl -v -X POST localhost:8000/api/hello 
```

响应会是这样的:

```
> POST /api/hello HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.61.0
> Accept: */*
> 
< HTTP/1.1 405 Method Not Allowed 
```

还有一些事情需要记住，比如每次从 api 返回时刷新输出或关闭交换。当我使用 Spring 时，我甚至不用去想它。

从[步骤-3](https://medium.com/r/?url=https%3A%2F%2Fgithub.com%2Fpiczmar%2Fpure-java-rest-api%2Ftree%2Fstep-3) 分支尝试这一部分。

## 解析请求参数

解析请求参数是另一个“特性”,与使用框架相反，我们需要自己实现它。
假设我们希望我们的 hello api 以一个名称作为参数进行响应，例如:

```
curl localhost:8000/api/hello?name=Marcin

Hello Marcin! 
```

我们可以用如下方法解析参数:

```
public static Map<String, List<String>> splitQuery(String query) {
        if (query == null || "".equals(query)) {
            return Collections.emptyMap();
        }

        return Pattern.compile("&").splitAsStream(query)
            .map(s -> Arrays.copyOf(s.split("="), 2))
            .collect(groupingBy(s -> decode(s[0]), mapping(s -> decode(s[1]), toList())));

    } 
```

并如下使用:

```
 Map<String, List<String>> params = splitQuery(exchange.getRequestURI().getRawQuery());
String noNameText = "Anonymous";
String name = params.getOrDefault("name", List.of(noNameText)).stream().findFirst().orElse(noNameText);
String respText = String.format("Hello %s!", name); 
```

您可以在[步骤 4](https://medium.com/r/?url=https%3A%2F%2Fgithub.com%2Fpiczmar%2Fpure-java-rest-api%2Ftree%2Fstep-4) 分支中找到完整的示例。

同样，如果我们想使用路径参数，例如:

```
curl localhost:8000/api/items/1 
```

要按 id=1 获取 item，我们需要自己解析路径，从中提取 id。这越来越麻烦了。

## 安全端点

每个 REST API 中的一个常见情况是用凭证保护一些端点，例如使用基本认证。
对于每个服务器上下文，我们可以如下设置一个认证器:

```
HttpContext context =server.createContext("/api/hello", (exchange -> {
  // this part remains unchanged
}));
context.setAuthenticator(new BasicAuthenticator("myrealm") {
    @Override
    public boolean checkCredentials(String user, String pwd) {
        return user.equals("admin") && pwd.equals("admin");
    }
}); 
```

`BasicAuthenticator`中的“myrealm”是一个域名。领域是一个虚拟名称，可用于分隔不同的身份验证空间。
你可以在 [RFC 1945](https://tools.ietf.org/html/rfc1945#section-11) 中了解更多信息

您现在可以通过添加一个类似于
的`Authorization`头来调用这个受保护的端点

```
curl -v localhost:8000/api/hello?name=Marcin -H 'Authorization: Basic YWRtaW46YWRtaW4=' 
```

`Basic`之后的文本是 Base64 编码的`admin:admin`，在我们的示例代码中是硬编码的凭证。
在实际应用中，你可能会从文件头中得到用户的身份验证信息，并与数据库中存储的用户名和密码进行比较。
如果您跳过标题，API 将以状态
响应

```
HTTP/1.1 401 Unauthorized 
```

从[步骤 5](https://medium.com/r/?url=https%3A%2F%2Fgithub.com%2Fpiczmar%2Fpure-java-rest-api%2Ftree%2Fstep-5) 分支检查完整代码。

## JSON、异常处理程序和其他

现在是更复杂例子的时候了。

根据我过去的软件开发经验，我开发的最常见的 API 是交换 JSON。

我们将开发一个 API 来注册新用户。我们将使用内存数据库来存储它们。

我们的用户域对象将会很简单:

```
@Value
@Builder
public class User {

    String id;
    String login;
    String password;
} 
```

我正在使用 Lombok 注释来保存我从构造函数和 getters 样板代码，它将在构建时生成。

在 REST API 中，我只想传递登录名和密码，所以我创建了一个单独的域对象:

```
@Value
@Builder
public class NewUser {

    String login;
    String password;
} 
```

用户将在一个服务中创建，我将在我的 API 处理程序中使用该服务。服务方法只是存储用户。在完整的应用程序中，它可以做更多的事情，比如在用户注册成功后发送事件。

```
public String create(NewUser user) {
    return userRepository.create(user);
} 
```

我们的存储库的内存实现如下:

```
 import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;

import com.consulner.domain.user.NewUser;
import com.consulner.domain.user.User;
import com.consulner.domain.user.UserRepository;

public class InMemoryUserRepository implements UserRepository {

    private static final Map USERS_STORE = new ConcurrentHashMap();

    @Override
    public String create(NewUser newUser) {
        String id = UUID.randomUUID().toString();
        User user = User.builder()
            .id(id)
            .login(newUser.getLogin())
            .password(newUser.getPassword())
            .build();
        USERS_STORE.put(newUser.getLogin(), user);

        return id;
    }
} 
```

最后，让我们在 handler:
中将它们粘在一起

```
protected void handle(HttpExchange exchange) throws IOException {
        if (!exchange.getRequestMethod().equals("POST")) {
            throw new UnsupportedOperationException();
        }

        RegistrationRequest registerRequest = readRequest(exchange.getRequestBody(), RegistrationRequest.class);

        NewUser user = NewUser.builder()
            .login(registerRequest.getLogin())
            .password(PasswordEncoder.encode(registerRequest.getPassword()))
            .build();

        String userId = userService.create(user);

        exchange.getResponseHeaders().set(Constants.CONTENT_TYPE, Constants.APPLICATION_JSON);
        exchange.sendResponseHeaders(StatusCode.CREATED.getCode(), 0);

        byte[] response = writeResponse(new RegistrationResponse(userId));

        OutputStream responseBody = exchange.getResponseBody();
        responseBody.write(response);
        responseBody.close();
    } 
```

它将 JSON 请求翻译成`RegistrationRequest`对象:

```
@Value
class RegistrationRequest {

    String login;
    String password;
} 
```

我稍后将它映射到域对象`NewUser`，最终将它保存在数据库中，并将响应写成 JSON。

我需要将`RegistrationResponse`对象翻译回 JSON 字符串。

JSON 的编组和解组是用 Jackson 对象映射器(`com.fasterxml.jackson.databind.ObjectMapper`)完成的。

这就是我如何在应用程序主方法中实例化新的处理程序:

```
 public static void main(String[] args) throws IOException {
        int serverPort = 8000;
        HttpServer server = HttpServer.create(new InetSocketAddress(serverPort), 0);

        RegistrationHandler registrationHandler = new RegistrationHandler(getUserService(), getObjectMapper(),
            getErrorHandler());
        server.createContext("/api/users/register", registrationHandler::handle);

        // here follows the rest.. 

 } 
```

您可以在 [step-6](https://medium.com/r/?url=https%3A%2F%2Fgithub.com%2Fpiczmar%2Fpure-java-rest-api%2Ftree%2Fstep-6) git 分支中找到工作示例，其中我还添加了一个全局异常处理程序，API 使用该程序
来响应标准的 JSON 错误消息，例如当 HTTP 方法不受支持或 API 请求格式错误时。

您可以运行该应用程序，并尝试以下示例请求之一:

```
curl -X POST localhost:8000/api/users/register -d '{"login": "test" , "password" : "test"}' 
```

回复:

```
{"id":"395eab24-1fdd-41ae-b47e-302591e6127e"} 
```

```
curl -v -X POST localhost:8000/api/users/register -d '{"wrong": "request"}' 
```

回复:

```
< HTTP/1.1 400 Bad Request
< Date: Sat, 29 Dec 2018 00:11:21 GMT
< Transfer-encoding: chunked
< Content-type: application/json
< 
* Connection #0 to host localhost left intact
{"code":400,"message":"Unrecognized field \"wrong\" (class com.consulner.app.api.user.RegistrationRequest), not marked as ignorable (2 known properties: \"login\", \"password\"])\n at [Source: (sun.net.httpserver.FixedLengthInputStream); line: 1, column: 21] (through reference chain: com.consulner.app.api.user.RegistrationRequest[\"wrong\"])"} 
```

此外，我偶然遇到了一个项目 [java-express](https://github.com/Simonwep/java-express)
，它是 Node.js [Express](https://expressjs.com/) 框架
的 java 对应物，并且也使用 jdk.httpserver，因此本文中涉及的所有概念都可以在现实生活中的应用程序框架中找到:
，它也足够小，可以快速消化代码。
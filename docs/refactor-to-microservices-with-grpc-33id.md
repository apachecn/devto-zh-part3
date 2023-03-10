# 使用 gRPC 重构微服务

> 原文：<https://dev.to/guglielmino/refactor-to-microservices-with-grpc-33id>

## 简介

开发人员的工作很辛苦，我们经常暴露在地狱般的环境中(我有夸张的倾向:-)。前段时间是 [DLL 地狱](https://en.wikipedia.org/wiki/DLL_Hell)，最近是[回调地狱](http://callbackhell.com/)，但是我最害怕的是**遗留代码地狱**。

[![Legacy code Hell](img/27973b4e23e8f9d5ce872216096988be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x6E5nhMf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://24t9d72kcs873my15o9hr1pu-wpengine.netdna-ssl.com/wp-content/uploads/2014/12/06-programming-coding-is-hell.png) 
<sub>信用 toggl.com</sub>

在一个完美的世界中，你从零开始创建项目，选择适合你想要实现的架构模式和工具。因为我们并不在一个完美的世界中，所以更多的时候，我们需要处理遗留代码。在我的职业生涯中，这种情况发生过很多次，当然我并不孤单，这就是心理学家赚很多钱的原因。

## 劈开地狱

一片地狱总比完全的地狱好，或者至少应该是这样。我将要描述的是一种将遗留应用程序分割成小块并试图将它们作为一个应用程序来管理的方法。

本文的主题是一个完整的 Python 2.7 应用程序。该方法旨在创建一个概念证明，以验证整体代码库到微服务架构的渐进移植。微服务是一个被滥用的术语，也可以说是一个流行词，但如果实用主义者采用它，它是一个有趣的架构模式，有很多好处。例如，将“monolith”代码库从 Python 2.7 迁移到 Python 3.x 可能会很痛苦。相反，将项目拆分成小的组件(或服务)，并让它们相互通信，会容易得多。以这种方式分割项目的基础是定义一种有效的方式来管理服务到服务的通信。它必须简单、快速、可扩展并经过实战检验，它的名字是 RPC 系统(远程过程调用)。

## RPC

远程过程调用是一个很老的想法，自从第一台计算机的网络开始普及，一些 RPC 系统被实现。RPC 通常基于请求/响应模式，周围有许多 RPC 系统，通常以非常不同的方式实现。尽管如此，想法总是一样的:进程 *A* 向进程 *B* 发出请求，后者可以对 *A* 做出响应。这些进程可以运行在同一个主机上，也可以运行在不同的主机上，假设它们能够通过网络相互通信。这是一个简化的视图，但是从逻辑的角度来看，它解决了我们的需求。当然，在选择正确的 RPC 时，还需要考虑更多的因素，具体来说应该是:

*   弹回的
*   性能
*   安全的
*   语言不可知

最后一点在今天尤为重要，我是“银弹”方法的强烈反对者，这通常是“如果你只有一把锤子，一切看起来都像钉子”。有了众多语言的选择，你会发现有些组件用 JavaScript 开发会更好，有些用 Python，有些用 Go，真是太棒了！(同时，如果被遗弃也是危险的)。

## 验证架构变更

验证架构方法的最佳实践是创建(至少)一个试点项目，如果您愿意，也可以是 PoC。同时，必须明确定义要验证的需求列表，在这种情况下，它们是:

*   应该能够调用在不同 Python 版本(2.x 和 3.x)中实现的服务
*   应该能够调用用不同语言实现的服务，比如 JavaScript
*   应该在容器环境中工作

通常情况下，最好保持列表很短，确认我们具体需要什么。在这种情况下，具体的需求，说白了，就是检查 gRPC 如何在容器环境中与不同的语言一起工作。

### gRPC 作为服务通信系统

gRPC 是一个现代的、开源的远程过程调用(RPC)框架，可以在任何地方运行，这是你可以从[官方网站 FAQ](https://grpc.io/faq/) 中读到的。这看起来正是我们要找的，值得一试。

gRPC 使用[协议缓冲区](https://developers.google.com/protocol-buffers/)作为序列化数据和定义服务接口的机制。使用一种特定的语言来创建接口是一种非常常见的方法，在 RPC 术语中它被称为 [IDL](https://en.wikipedia.org/wiki/Interface_description_language) 。典型地，IDL 是一种定制的描述语言，专门用于设计服务通信中使用的接口。
关注项目结构如果你使用 IDL，你至少需要两样东西:

*   一个或多个 IDL 源，用于服务接口
*   在代码中使用(编译或动态加载)IDL 定义的方法

简而言之，IDL 是需要相互通信(单向或双向)的进程之间共享的契约。这是管理项目结构的重要一点，因为您需要决定如何让使用 IDL 的项目共享它们。

## 定义界面

让我们从我们将在 PoC 中使用的 IDL 接口的示例开始。

```
syntax = "proto3";

import "common.proto";

package notificator;

service NotificatorService {
    rpc SendNotification(NotificationPayload) returns (Result) {}
}

message NotificationPayload {
    string destination  = 1;
    string message = 2;

} 
```

乍一看可能很吓人，但实际上很简单。这里的要点是服务定义、服务提供什么操作以及数据是如何构造的。用简单的英语翻译上面的 IDL，我们定义了一个`NotificationService`，公开了一个名为`SendNotification`的方法，该方法期望接收一个`NotificationPayload`作为输入，并以一个`Result`作为输出。`Result`是在一个外部文件中定义的，用来测试 IDL 文件如何组织拆分代码。
一件重要的事情立即显现出来，那就是创建和维护这些文件需要额外的工作。这是 gRPC 的核心方面，有一个严格的接口定义，一个服务间的契约，对保持服务间通信的控制是非常重要的。

最后，IDL 文件可以在运行时加载，或者使用 gRPC 工具从它们静态地生成代码。没有理想的解决方案，它主要依赖于构建和部署基础设施，在这个项目中，我使用了后一种方法。

## 实现

是时候开始编写代码了，但是首先必须定义一个项目结构。因为我的首选方法是尽可能简单地开始，所以我创建了如下的项目文件夹。

```
 ├── client-2.x
├── protos
│   ├── common.proto
│   └── notification.proto
└── server-3.x 
```

这里没有什么特别的，两个文件夹`client-2.x`和`server-3.x`包含了一个假设的服务和他的消费者的代码，我称它们为客户端和服务器以明确角色，但是请记住，在 gRPC 中没有角色的概念，它定义了接口，服务如何相互交互不是它需要知道的事情。intersting 文件夹是`protos`，我把 IDL 源码和接口定义放在这里。该项目包括一个发送通知的服务(无论它是推送通知，短信或其他任何东西)。然后，服务定义定义了发送通知的方法、带有消息体和目的地址的有效负载。在协议缓冲区 IDL 中翻译这相当于上一段中 IDL 接口的代码。

在协议缓冲区中，方法参数和返回类型，需要被定义为自定义类型，换句话说，你不能使用原始类型，像`string`或`bool`，因为它们是，必须定义一个自定义类型。
在我们这里`NotificationPayload`的定义显示在底部，而`Result`是从`common.proto`导入的。proto 文件中的一个 cavets 类型定义是关于分配给每个属性的数字(就像上面例子中的`destination = 1`或`message = 2`)。这些数字与[协议缓冲编码](https://developers.google.com/protocol-buffers/docs/encoding)的工作方式有关。重要的是要知道，它们在消息定义中必须是唯一的，最重要的是，如果发生更改，编码数据将与使用旧计数法的客户端不兼容。

关于协议缓冲区还有许多其他细节，它们在官方协议缓冲区文档中有详细的记录。

## 安装依赖项

两个项目，`client-2.x`和`server-3-x`，都带有一个`requirements.txt`文件。作为一个事实上的标准，有了这个文件，用`pip install -r requirement.txt`安装所有的项目依赖项就很简单了。
在需求文件中寻找项目需要的东西会很有趣，特别是两个核心包`grpcio`和`grpcio-tools`，它们是 gRPC 实现和一个工具包，使用 gRPC 的核心包。

### 关于 Makefile 的说明

你会在项目中注意到一些 Makefiles，这并不是因为我是一个怀旧的 C/C++开发人员:-)。这是因为 Python 缺乏一种定义脚本的标准方式，就像 Node.js 在`package.json`中用`scripts`来定义脚本一样。我发现`Makefile`是一个很好的妥协，不用创建定制的 shell 脚本，所以项目依赖项可以用`make install`安装，只需输入`make`就可以列出提供的所有命令。当然，`make`必须安装在系统上，如何安装不在讨论范围之内，取决于操作系统，但是有大量的相关文档。

## 调用服务

好的，但是我们如何使用 IDL 通过 gRPC 调用服务呢？正如我之前写的，有两种方法可以使用`proto`文件，在这个项目中，我们从 IDL 生成代码。我们之前注意到，除了 Python gRPC 包之外，还有一个叫做`grpc_tools`的包。很难猜测，但它原来是一个为 gRPC 提供工具的包。提供的一个功能是从`proto`文件开始生成代码，这就是我们将要使用的。
让我们从`client-2.x`项目开始，对于`server-3.x`来说完全一样，使用项目中提供的 make 文件运行`make build`即可。实际上，Makefile 运行 Python gRPC 工具，查看客户端或服务器中提供的一个 Makefile，我们可以了解如何运行。

```
python -m grpc_tools.protoc -I../protos --python_out=. --grpc_python_out=. ../protos/common.proto
python -m grpc_tools.protoc -I../protos --python_out=. --grpc_python_out=. ../protos/notification.proto 
```

运行上述命令将产生一些新的 Python 源文件。这些文件是服务的 Python 翻译和在`proto`文件中定义的有效载荷。需要注意的是，每个`proto`文件都创建了两个文件。按照惯例，这些文件有相同的`proto`名字和后缀，一个是`_pb2.py`，另一个是`_pb2_grpc.py`。很简单，前者是定义数据结构的地方，比如`NotificationPayload`，后者是服务存根所在的地方。
让我们从客户端开始，调用`NotificationService`就像下面的代码一样简单。

```
 with grpc.insecure_channel('{0}:{1}'.format(GRPC_HOST, GRPC_PORT)) as channel:
        stub = notification_pb2_grpc.NotificatorServiceStub(channel)
        stub.SendNotification(
                   notification_pb2.NotificationPayload(destination="Fabrizio", message="Hello!!!")
                   ) 
```

很简单吧？这是创建一个 gRPC 通道的问题，实例化存根并在存根上调用我们的`SendNotification`,因为它是在我们的项目中的某个地方定义的，如果你熟悉设计模式，这是一个代理。`insecure_channel`是为了承担一部分安全开销，gRPC 地址安全很重要，但为了保持代码可读，我选择绕过这一部分(无论如何，这在[官方网站](https://grpc.io/docs/guides/auth.html)上有很好的记录)。

关于环境的一个重要注意事项:我写道 PoC 的一个需求是测试不同 Python 版本之间的服务通信。如果你想在没有 Docker 的情况下测试这个项目(下面有更多关于它的信息),你需要在同一台机器上为客户机使用 Python 2.7，为服务器使用 Pythion 3.6。这可以通过`virtualenv`来完成，这里可以找到[的快速介绍](https://blog.dbrgn.ch/2012/9/18/virtualenv-quickstart/)，无论如何，如果你喜欢“尽快让我看看它是如何工作的”方法，请阅读下面的“在 Docker 中运行”段落。

## 创建服务

现在，我们几乎拥有了一切，我们定义了 IDL，开发了客户端，但是我们缺少主菜:服务！
我故意把服务实现放在客户端之后，已经定义了 IDL 和客户端，应该很清楚我们需要它做什么。需要重点关注的是，我们需要在代码中的某个地方，在我们超级酷的`NotificationService`下面，实现我们希望通过 gRPC 提供的服务。

```
class NotificatorServiceServicer(notification_pb2_grpc.NotificatorServiceServicer):
    def SendNotification(self,  request, context):
        logging.debug(f"handling notification message '{request.message}' to {request.destination})  ")
        return common_pb2.Result(status=True) 
```

很明显，我们在这里实现的是:IDL 中定义的接口。基类`notification_pb2_grpc.NotificatorServiceServicer`有效载荷和结果是在 IDL 中设计的。
实现很简单:我们使用来自请求的`message`和`destination`，也就是`NotificationPayload`，来记录一个消息，用一个`Result`包装一个成功状态`status=True`来响应。

定义服务并不足以使其对客户端可用，我们需要一种通过网络公开服务的方法，四行代码就足够了。

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
notification_pb2_grpc.add_NotificatorServiceServicer_to_server(
    NotificatorServiceServicer(), server)
server.add_insecure_port(f"0.0.0.0:5001")
server.start() 
```

简而言之，我们创建一个 gRPC 服务器实例，将我们的服务绑定到它，定义监听请求和运行服务器的端口。在引擎盖下发生了很多事情，但现在让我们满足于此。

此时，用 Python 3.6 在一个`virtualenv`中运行服务器，用 Python 2.7 在另一个中运行客户端，它们应该开始相互调用，完整的源代码在[这里](https://github.com/guglielmino/grpc-python-poc)可以找到

## 使用其他语言呢？

我没有忘记检查 PoC 最重要的一点，测试与其他语言的互操作性。现在，我们对 gRPC 及其工作方式有了一些信心，是时候介绍一个新的客户端了。这个使用 JavaScript，工作方式与 Python 2.x 完全相同。当然，几乎任何语言都有 gRPC 绑定(C，C++，Java，C#，...)但是我选择使用 JavaScript，因为现在它是最广泛使用的语言之一。在我之前说过的项目结构中，我省略了 JavaScript 客户端，真正的项目结构如下。

```
 ├── client-2.x
├── js-client     <<<=== You are here!!!
├── protos
│   ├── common.proto
│   └── notification.proto
└── server-3.x 
```

显然，JavaScript 客户机旨在具有与 Python 客户机相同的行为，如果您对 Node.js 环境有信心，您知道第一步是安装依赖项(也称为节点模块)。

```
npm intall 
```

所有模块就绪后，我们需要从原型文件生成 gRPC 代理代码，就像我们在 Python 版本中所做的那样。像往常一样，Node.js 环境中有一个在`package.json`中定义的脚本

```
npm run build 
```

这是一个快捷方式，但是“在引擎盖下”,该命令与用于 Python 客户端的命令非常相似。

```
grpc_tools_node_protoc --js_out=import_style=commonjs,binary:. --grpc_out=. --plugin=protoc-gen-grpc=node_modules/grpc-tools/bin/grpc_node_plugin -I ../protos/ common.proto && grpc_tools_node_protoc --js_out=import_style=commonjs,binary:. --grpc_out=. --plugin=protoc-gen-grpc=node_modules/grpc-tools/bin/grpc_node_plugin -I ../protos/ notification.proto 
```

简而言之，我使用了 Node.js 专用的`protoc`(也就是 protobuf IDL 编译器)，这个命令创建了四个文件，就像我在上面使用 Python 调用的`protoc`一样

## 在 Docker 中运行

### 容器

如果您在这一点上遵循了所有的文章说明，那么您可以在本地运行所有的东西，但是因为我的需求之一是在容器环境中测试项目，所以项目包含 Dockerfile 和 docker-compose 定义。同样，Docker 的安装不在讨论范围之内(我觉得这就像是跳伞者挂钩的发明者开的玩笑(*))

### 用 docker 编写本地运行

假设在机器中配置了 Docker 环境，那么运行项目就是在根文件夹中运行`docker-compose up`的事情。过一会儿，控制台将被来自服务器和客户端的消息淹没。

[![running docker compose](img/d86d78f54141b1831d16938173ef2667.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6a2uQYap--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v81w0g2q3l3hluxoufn4.gif)

每次迭代在标准输出上打印三条消息。

```
client_1  | DEBUG:root:Client: sending notification calling gRPC server
server_1  | DEBUG:root:handling notification message 'Hello!!!' to Fabrizio)  
client_1  | DEBUG:root:Client: notification sent 
```

## 结论

我们只触及了冰山一角，gRPC 相当复杂，我忽略了很多细节。如果在这一点上 gRPC 如何帮助将架构分解成组件已经很清楚了，那么我就实现了我的主要目标。显而易见的建议是在高级主题中深化，[官方网站](https://grpc.io/)是开始的地方，并尝试在一些小项目中使用它。

**(*)跳伞钩发明者的笑话**

一个发明家去专利局说:“我发明了一个钩子来拯救跳伞者的生命，我想申请专利”。

员工说:“嗯，告诉我它是怎么工作的”

*发明者*:“很简单，如果降落伞打不开，跳伞者可以用钩子来保命”

员工:“好吧，但是跳伞运动员应该在哪里挂钩？”

*发明家*:“嘿，我不能一个人把所有的东西都编出来！”
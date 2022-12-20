# grpc-mock:node . js 上的一个简单的模拟 gRPC 服务器

> 原文：<https://dev.to/yoshiyukikato/grpc-mock-a-simple-mock-grpc-server-on-nodejs-2563>

大家好！新年快乐🌅

不幸的是，今年年初我患了重感冒😷有几天休息对我来说很无聊，所以我躺在床上开发 Node.js 库 [grpc-mock](https://github.com/YoshiyukiKato/grpc-mock) 。

`grpc-mock`使您能够仅通过传递关于响应的规则来构建 gRPC 模拟服务器。我认为这在你编写一个包含 gRPC 调用的测试时会很有用😎

下面是一个简单的例子。

```
const {createMockServer} = require("grpc-mock");
const mockServer = createMockServer({
  protoPath: "/path/to/greeter.proto",
  packageName: "greeter",
  serviceName: "Greeter",
  rules: [
    { method: "hello", input: { message: "Hi" }, output: { message: "Hello" } }
  ]
});
mockServer.listen("0.0.0.0:50051"); 
```

```
syntax="proto3";

package greeter;

service Greeter {
  rpc Hello (RequestGreet) returns (ResponseGreet) {}
}

message RequestGreet {
  string message = 1;
}

message ResponseGreet {
  string message = 1;
} 
```

规则被描述为一个对象。

```
{ method: "hello", input: { message: "Hi" }, output: { message: "Hello" } } 
```

当用`{ message: "Hi" }`调用`hello`方法时，该规则使 gRPC 服务器通过`{ message: "Hello" }`做出响应。

你也可以通过[正则表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)字符串指定输入模式。下面是一条规则，当使用任何参数调用 hello 方法时，该规则使服务器通过`{ message: "Hello" }`进行响应:

```
{ method: "hello", input: ".*", output: { message: "Hello" } } 
```

## 流支持

grpc-mock 支持客户机/服务器流。当使用 stream 时，您为规则中的`streamType`属性设置一个适当的值。下面显示了带有相应 rpc 定义的示例规则。

### 仅客户端流

当服务器在`howAreYou`方法的调用中接收到`{ message: "Hi" }`和`{ message: "How are you?" }`的流序列时，它通过`{ message: "I'm fine, thank you" }`做出响应。请将`"client"`设置为`streamType`属性。

```
{
  method: "howAreYou",
  streamType: "client",
  stream: [
    { input: { message: "Hi" } },
    { input: { message: "How are you?" } },
  ],
  output: { message: "I'm fine, thank you" }
} 
```

```
rpc HowAreYou (stream RequestGreet) returns (ResponseGreet) {} 
```

### 仅服务器端流

当用`{ message: "Hi. I'm John. Nice to meet you" }`调用`niceToMeetYou`方法时，服务器通过`{ message: "Hi, I'm Sana" }`和`{ message: "Nice to meet you too" }`的流序列进行响应。请将`"server"`设置为`streamType`属性。

```
{
  method: "niceToMeetYou",
  streamType: "server",
  stream: [
    { output: { message: "Hi, I'm Sana" } },
    { output: { message: "Nice to meet you too" } },
  ],
  input: { message: "Hi. I'm John. Nice to meet you" }
} 
```

```
rpc NiceToMeetYou (RequestGreet) returns (stream ResponseGreet) {} 
```

### 相互(客户端和服务器)流

Mutual stream 案例可能有点混乱。在一个`chat`方法的调用中，服务器收到`{ message: "Hi" }`时通过`{ message: "Hi there" }`响应，收到`{ message: "How are you?" }`时通过`{ message: "I'm fine, thank you." }`响应。其中，`{ message: "Hi" }`和`{ message: "How are you?" }`的输入必须按顺序发送。请将`"mutual"`设置为`streamType`属性。

```
{
  method: "chat",
  streamType: "mutual",
  stream: [
    { input: { message: "Hi" }, output: { message: "Hi there" } },
    { input: { message: "How are you?" }, output: { message: "I'm fine, thank you." } },
  ]
} 
```

```
rpc Chat (stream RequestGreet) returns (stream ResponseGreet) {} 
```

## 结论

在这篇文章中，我简单地介绍了用于构建 gRPC 模拟服务器的 Node.js 库。本库正在开发中，欢迎您的评论、问题和公关🤗

如果 lib 对你的编码有帮助，我很高兴。

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)/[grpc-mock](https://github.com/YoshiyukiKato/grpc-mock)

### Node.js 上的一个简单模拟 gRPC 服务器

<article class="markdown-body entry-content" itemprop="text">

# grpc-mock

[![npm version](img/6a43103ffc9d954d668898ec982e768c.png)](https://badge.fury.io/js/grpc-mock)

Node.js 上的一个简单模拟 gRPC 服务器

```
const {createMockServer} = require("grpc-mock")
const mockServer = createMockServer({
  protoPath: "/path/to/greeter.proto"
  packageName: "greeter"
  serviceName: "Greeter"
  rules: [
    { method: "hello", input: { message: "test" }, output: { message: "Hello" } },
    { method: "goodbye", input: ".*", output: { message: "Goodbye" } },

    {
      method: "howAreYou",
      streamType: "client",
      stream: [
        { input: { message: "Hi" } },
        { input: { message: "How are you?" } },
      ],
      output: { message: "I'm fine, thank you" }
    },

    {
      method
```

…</article>

[View on GitHub](https://github.com/YoshiyukiKato/grpc-mock)
# Node.js 上的 GRPC 更简单

> 原文：<https://dev.to/yoshiyukikato/grpc-on-nodejs-more-simply-h1>

在 Node.js 上开发 GRPC 应用时，我们可以使用 [`grpc`](https://www.npmjs.com/package/grpc) 和 [`@grpc/proto-loader`](https://www.npmjs.com/package/@grpc/proto-loader) 。虽然这些漂亮的包提供了统一的 API 来构建 GRPC 服务器和客户端，但是它们太原始了，我不能直接在我的应用程序代码中使用。我想要更简化的 API，比如`createServer`和`createClient`。

所以，我正在开发 [grpc-kit](https://github.com/YoshiyukiKato/grpc-kit) 。是一个`grpc`和`@grpc/proto-loader`的小型包装器库。当然它提供了`createServer`和`createClient`😆

下面是一个例子。

**greeter.proto**

```
syntax="proto3";

package greeter;

service Greeter {
  rpc Hello (RequestGreet) returns (ResponseGreet) {}
  rpc Goodbye (RequestGreet) returns (ResponseGreet) {}
}

message RequestGreet {
  string name = 1;
}

message ResponseGreet {
  string message = 1;
} 
```

**server.js**

```
const {createServer} = require("grpc-kit");
const server = createServer();

server.use({
  protoPath: "/path/to/greeter.proto",
  packageName: "greeter",
  serviceName: "Greeter",
  routes: {
    hello: (call, callback) => {
      callback(null, { message: `Hello, ${call.request.name}` });
    },
    goodbye: async (call) => {
      return { message: `Goodbye, ${call.request.name}` };
    }
  }
});

server.listen("0.0.0.0:50051"); 
```

`createServer`返回`grpc.Server`的包装类的实例。该类提供了用于添加服务的`use`方法。您可以用路由映射来声明性地配置服务。路由图由在`greeter.proto`中声明的方法对和处理函数组成(同步/异步函数都可用😎).服务配置完成后，通过`listen`方式启动服务器。

**client.js**

```
//client.js
const {createClient} = require("grpc-kit");
const client = createClient({
  protoPath: "/path/to/greeter.proto",
  packageName: "greeter",
  serviceName: "Greeter"
}, "0.0.0.0:50051");

client.hello({ name: "Jack" }, (err, response) => {
  if(err) throw err;
  console.log(response.message);
});

client.goodbye({ name: "John" }, (err, response) => {
  if(err) throw err;
  console.log(response.message);
}); 
```

`createClient`返回`grpc.Client`的一个实例。它调用在`greeter.proto`中声明的方法，并在回调中接收响应。

关于 API 的详细信息，请查看[库](https://github.com/YoshiyukiKato/grpc-kit)。图书馆正在建设中。欢迎任何意见和建议。谢谢大家！

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png) [吉行加藤](https://github.com/YoshiyukiKato) / [ grpc-kit](https://github.com/YoshiyukiKato/grpc-kit)

### 在 Node.js 上更简单地使用 grpc

<article class="markdown-body entry-content" itemprop="text">

# grpc-kit

[![npm version](img/62e4c77e30673d80c112b8271b862ba7.png)](https://badge.fury.io/js/grpc-kit)

在 Node.js 上更简单地使用 grpc。

## 快速启动

### 安装

```
$ npm install grpc @grpc/proto-loader grpc-kit
```

### 样机

```
syntax="proto3"
package greeter
service Greeter {
  rpc Hello (RequestGreet) returns (ResponseGreet) {}
  rpc Goodbye (RequestGreet) returns (ResponseGreet) {}
}
message RequestGreet {
  string name = 1;
}

message ResponseGreet {
  string message = 1;
}
```

### 计算机网络服务器

```
const {createServer} = require("grpc-kit")
const server = createServer();

server.use({
  protoPath: "/path/to/greeter.proto",
  packageName: "greeter",
  serviceName: "Greeter",
  routes: {
    hello: (call, callback) => {
      callback(null, { message: `Hello, ${call.request.name}` });
    },
    goodbye: async (call) => {
      return { message: `Goodbye, ${call.request.name}`
```

…</article>

[View on GitHub](https://github.com/YoshiyukiKato/grpc-kit)
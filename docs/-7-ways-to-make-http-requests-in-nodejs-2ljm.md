# 在 Node.js 中发出 HTTP 请求的 7 种方法

> 原文：<https://dev.to/attacomsian/-7-ways-to-make-http-requests-in-nodejs-2ljm>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/http-requests-in-nodejs/)。*

* * *

HTTP 请求是大多数现代语言的核心部分。对于新开发人员来说，学习如何发出 HTTP 请求来交换数据可能是一个挑战。幸运的是，对于 Node.js 开发人员来说，情况并非如此。有许多久经考验的解决方案可用于发出任何类型的 HTTP 请求。让我们看看一些最流行的支持 HTTP 请求的 Node.js 库。

出于测试目的，我们将在所有示例中使用[JSONPlaceholder](https://jsonplaceholder.typicode.com/)fake todo REST API。我们的 REST API 返回以下 JSON 响应:

```
{  "userId":  1,  "id":  1,  "title":  "delectus aut autem",  "completed":  false  } 
```

在继续之前，确保您已经在您的机器上安装了最新的 LTS 版本的 [Node.js 和 npm](https://nodejs.org/en/download/) 。

## 1。HTTP —标准 HTTP 库

`HTTP`和`HTTPS`模块都打包在标准库中。使用这些模块，您可以轻松地发出 HTTP 请求，而无需安装外部包。但是，不幸的是，这些都是低级模块，与其他解决方案相比不是很用户友好。

```
const https = require('https');

https.get('https://jsonplaceholder.typicode.com/todos/1', (response) => {
  let todo = '';

  // called when a data chunk is received.
  response.on('data', (chunk) => {
    todo += chunk;
  });

  // called when the complete response is received.
  response.on('end', () => {
    console.log(JSON.parse(todo).title);
  });

}).on("error", (error) => {
  console.log("Error: " + error.message);
}); 
```

## 2。请求

[请求](https://github.com/request/request)是一个简化的 HTTP 客户端，与默认的`HTTP`模块相比更加用户友好。它在社区中非常受欢迎，被认为是 Node.js 项目的首选 HTTP 客户端。

与`HTTP`模块不同，您需要使用以下命令从节点包管理器(npm)将它作为依赖项安装:

```
$ npm install request --save 
```

下面是一个示例代码片段，它使用`Request` HTTP 客户端来调用我们的假 REST API:

```
const request = require('request');

request('https://jsonplaceholder.typicode.com/todos/1', { json: true }, (err, res, body) => {
  if (err) { 
      return console.log(err); 
  }
  console.log(body.id);
  console.log(body.title);
}); 
```

## 3。针

[Needle](https://github.com/tomas/needle) 是 for Node.js 的一个可流式传输的 HTTP 客户端，支持代理、iconv、cookie、deflate 和多部分请求。要从 npm 安装 Needle，请在您的终端中运行以下命令:

```
$ npm install needle --save 
```

下面的代码片段将执行同样的任务，调用我们的假 REST API 并打印细节:

```
const needle = require('needle');

needle.get('https://jsonplaceholder.typicode.com/todos/1', {json: true}, (err, res) => {
    if (err) { 
          return console.log(err); 
      }
    let todo = res.body;
    console.log(todo.id);
    console.log(todo.title);
}); 
```

从版本`2.0.x`开始，针也支持承诺。[承诺](https://www.promisejs.org/)有助于编写更复杂的涉及事件链的代码。上面的代码片段可以使用 Promises 编写如下:

```
const needle = require('needle');

needle.get('https://jsonplaceholder.typicode.com/todos/1', {json: true})
    .then(res => {
        let todo = res.body;
        console.log(todo.id);
        console.log(todo.title);
    }).catch(err => {
        console.log(err);
    }); 
```

您还可以发现上面代码片段中的不同之处，错误处理是用`.catch()`完成的，因为我们现在使用了承诺。

## 4。Axios

[Axios](https://github.com/axios/axios) 是一个基于 Promise 的 HTTP 客户端，用于浏览器和 Node.js。与上面提到的 HTTP 客户端不同，Axios 会自动将响应数据转换为 JSON 对象。在您的终端中从项目根目录运行以下命令:

```
$ npm install axios --save 
```

因为它支持承诺，我们需要编写更少的代码来调用我们的假 REST API，就像我们在上面为`HTTP`客户端:
所做的那样

```
const axios = require('axios');

axios.get('https://jsonplaceholder.typicode.com/todos/1')
  .then(res => {
    console.log(res.data.id);
    console.log(res.data.title);
  })
  .catch(err => {
    console.log(err);
  }); 
```

Axios 的另一个好处是它支持使用`axios.all`的多个并发请求。例如，我们可以并发调用我们的假 REST API 来一次获得两个 todos 信息:

```
const axios = require('axios');

axios.all([
      axios.get('https://jsonplaceholder.typicode.com/todos/1'),
      axios.get('https://jsonplaceholder.typicode.com/todos/2')
    ]).then(axios.spread((res1, res2) => {
      console.log(res1.data.title);
      console.log(res2.data.title);
    })).catch(err => {
      console.log(err);
    }); 
```

## 5。超级代理

[SuperAgent](https://github.com/visionmedia/superagent) 是另一个流行的 HTTP 库，类似于 Axios，用于在 Node.js 和浏览器中发出 AJAX 请求。就像 Axios 一样，它将响应数据解析成 JSON，这很酷。使用以下命令从 npm 安装 SuperAgent:

```
$ npm install superagent --save 
```

下面是使用 SuperAgent 调用我们的假 REST API 的代码片段:

```
const superagent = require('superagent');

superagent.get('https://jsonplaceholder.typicode.com/todos/1')
.end((err, res) => {
  if (err) { 
      return console.log(err); 
  }
  console.log(res.body.id);
  console.log(res.body.title);
}); 
```

SuperAgent 可通过插件高度扩展。SuperAgent 有许多插件可用于执行不同的任务，如无缓存、URL 前缀和后缀等。您可以轻松编写自己的插件来扩展 SuperAgent 的功能。

## 6。得到

[Got](https://github.com/sindresorhus/got) 是 Node.js 的另一个用户友好的轻量级 HTTP 请求库。使用以下命令从 npm 安装 Got:

```
$ npm install got --save 
```

就像 Axios 和 Needle 一样，也得到了支持承诺。下面的代码片段将调用我们的假 REST API 来获取 todo 信息:

```
const got = require('got');

got('https://jsonplaceholder.typicode.com/todos/1', { json: true })
    .then(res => {
      console.log(res.body.id);
      console.log(res.body.title);
    }).catch(err => {
      console.log(err.response.body);
    }); 
```

## 7。节点提取

[Node-fetch](https://github.com/bitinn/node-fetch) 是一个轻量级的 HTTP 请求库，为 Node.js 带来了浏览器的`window.fetch`功能

```
$ npm install node-fetch --save 
```

与 Axios 类似，Node-fetch 最新版本支持承诺。下面是调用我们的假 REST API 以使用 Node-fetch 模块获得 todo 信息的代码片段:

```
const fetch = require('node-fetch');

fetch('https://jsonplaceholder.typicode.com/todos/1')
    .then(res => res.json()) // expecting a json response
    .then(json => {
        console.log(json.id);
        console.log(json.title);
    })
    .catch(err => {
        console.log(err);
    }); 
```

## 结论

这篇文章并没有涵盖 Node.js 可用的所有 HTTP 请求库。它只是解释了 HTTP 功能如何在 Node.js 中的一些最流行的 HTTP 客户端中工作。Node.js 还有几十个其他的 HTTP 客户端可用，如 [simple-get](https://github.com/feross/simple-get) ，它提供了一种最简单的方式来进行 HTTP 请求，并支持不到 100 行的 HTTPS、重定向和流。

你最喜欢用什么 HTTP 客户端在 Node.js 中发出 HTTP 请求？欢迎[发微博给我](https://twitter.com/attacomsian)，让我知道或询问任何进一步的问题。

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。
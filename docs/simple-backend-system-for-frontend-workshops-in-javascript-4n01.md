# Javascript 前端工作室的简单后端系统

> 原文：<https://dev.to/hamatti/simple-backend-system-for-frontend-workshops-in-javascript-4n01>

我在许多编程研讨会上担任教练。如果你也在一个俱乐部执教过，你可能会注意到在深度和宽度之间找到合适的平衡是很有挑战性的。特别是在前端研讨会上，我不想让学生们对构建后端或 API 系统感到困惑，但我确实想给他们提供易于使用和可扩展的系统。

我基于 [create-react-app](https://github.com/facebook/create-react-app) 构建了一个初学者工具包，它不涉及前端的基本 react 方面，而是添加了一个后端、几个命令和一个 API 客户端，这样用户就不必担心自己进行 HTTP 调用。

您可以从[Hamatti/CRA-with-JSON-server](https://github.com/Hamatti/cra-with-json-server)中找到我的初学者工具包代码

## 我的车间哲学

教的方法有很多。有些人采取理论先行的方法，只有当某件事已经学得足够多的时候，才推进到下一件事。对于长期学习来说，这是一个很好的方法，但是在短期研讨会(比如周末两天的研讨会)中，你最终不会完成很多事情。

我喜欢《Rails Girls》的风格，我的哲学也深受其影响。通过利用预先构建的东西，你可以完成很多事情。研讨会的目的不是掌握一些东西，而是引起兴趣，了解构建项目的各个不同部分，并将一些东西部署到云上以展示给你的朋友。

当你想了解更多的时候，你可以回到特定的步骤并了解更多。

## 后端存储

很幸运，我的一个朋友，[约翰尼斯](https://twitter.com/jelmnai)，给我介绍了 [`json-server`](https://github.com/typicode/json-server) 。这是一个很棒的 npm 包，它为您提供了存储在简单 JSON 文件中的数据的 HTTP 端点。

`json-server`是一个工具，它提供了比我们今天要经历的更多的东西。它非常适合用大量的端点来原型化和模仿 API。今天，我们主要对最简单的 GET、POST、PATCH 和 DELETE 端点感兴趣。

如前所述，我的第一个方法(我希望以后能更容易地插入任何系统)是使用 create-react-app，所以如果你想继续，用
创建一个新的应用

```
$ create-react-app my-new-project 
```

Enter fullscreen mode Exit fullscreen mode

并安装`json-server`

```
$ npm install -g json-server 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们在`backend/db.json`中创建一个 JSON 存储。我还加了一个`backend/readme.md`解释怎么用。你的`backend/db.json`可以从一个空的 JSON
开始

```
{} 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您想为教程设置它，您可以用您想要的集合预先填充它。在这种情况下，集合只是一个带有对象数组的键。

```
{  "authors":  [  {  "name":  "Juhis",  "username":  "hamatti",  "email":  "juhamattisantala@gmail.com"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

如果你接着运行

```
$ json-server backend/db.json -p 3001 
```

Enter fullscreen mode Exit fullscreen mode

您将获得进入 [http://localhost:3001](http://localhost:3001) 的 API 访问权限。你可以在一个新的终端标签中运行 curl 来获取数据:

```
$ curl http://localhost:3001/authors 
```

Enter fullscreen mode Exit fullscreen mode

你应该看到

```
[  {  "name":  "Juhis",  "username":  "hamatti",  "email":  "juhamattisantala@gmail.com"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

在你的终端里。

为了更简单，我给`package.json`添加了一个 npm 脚本来运行它。

```
"scripts":  {  "backend":  "json-server ./backend/db.json -p 3001"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在你将能够用`npm run backend`启动后端 API，你不必知道幕后发生了什么。

## API

在`src/api/apiClient.js`中，我已经创建了基本的 getters 和 setters 来访问 API:
中的数据

```
import axios from "axios";

const config = {
  baseURL: "http://localhost:3001",
  headers: {
    "Content-Type": "application/json"
  }
};

const client = axios.create(config); 
```

Enter fullscreen mode Exit fullscreen mode

我使用 axios 进行通话，并使用配置创建一个客户端。设置完成后，我有了这些与 API 交互的函数。

```
export function getAuthors(ctx) {
  return client.get("/authors").then(({ data }) => {
    ctx.setState({
      authors: data
    });
  });
}

export function updateAuthors(id, data) {
  return client.patch(`/authors/${id}`, data);
}

export function createAuthors(data) {
  return client.post(`/authors`, data);
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个简单的设置，用户不需要接触`backend/`文件夹或者`src/api/`文件夹，他们只需要把它们导入到他们想要查询 API 的地方。

第一个函数`getAuthors`目前是非常 React 专用的:它期望一个参数是有状态 React 组件的`this`,并且它将数据直接保存到状态中。

## 建立新的端点

只需要预定义的数据结构和端点就可以完成一个基本的研讨会，但当人们可以自己决定添加什么数据时，这是最有趣的。

因此，让我们添加一个功能来动态创建它。这个脚本目前还不是最防错的:如果你用相同的输入运行它两次，它就会崩溃。

在`package.json`中，我添加了一个新的脚本:

```
"scripts": {
    "generateAPI": "node generateAPI.js"
} 
```

Enter fullscreen mode Exit fullscreen mode

以及文件`generateAPI.js` :

```
"use strict";

const fs = require("fs");
const DB_PATH = "backend/db.json";
const API_CLIENT_PATH = "src/api/apiClient.js";

let key = process.argv[2];
if (!key) return;
key = key.toLowerCase();

let originalData = JSON.parse(fs.readFileSync(DB_PATH));
originalData[key] = [];

// Write to file
fs.writeFileSync(DB_PATH, JSON.stringify(originalData));

const titleCase = `${key.charAt(0).toUpperCase()}${key.substr(1)}`;

const newFunctions = `export function get${titleCase}(ctx) {
    return client.get("/${key}").then(({ data }) => {
      ctx.setState({ ${key}: data
      });
    });
  }

  export function update${titleCase}(id, data) {
    return client.patch(\`/${key}/\${id}\`, data);
  }

  export function create${titleCase}(data) {
    return client.post("/${key}/", data);
  }`;

const originalApiClient = fs.readFileSync(API_CLIENT_PATH);

const newApiClient = `${originalApiClient}\n\n${newFunctions}`;

fs.writeFileSync(API_CLIENT_PATH, newApiClient);

console.log(`${key} was added into the database and API.

You can now import new functions with

    import { get${titleCase}, update${titleCase} and create${titleCase} } from './api/apiClient';

and you will have access to the data.`); 
```

Enter fullscreen mode Exit fullscreen mode

这相当简单:你用`npm run generateAPI [model]`运行它，它将`model`作为`"model": []`集合添加到`backend/db.json`中，并将`get[Model]`、`update[Model]`和`create[Model]`函数生成到`src/api/apiClient.js`中。

这是一个相当粗糙的系统，但是根据我的测试，它运行得相当好。通过一个简单的 npm 脚本命令，您可以将新的集合添加到您的存储中，并从`apiClient.js`导入新的功能。
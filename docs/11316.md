# 从 GraphQL API 获取数据

> 原文：<https://dev.to/eveporcello/fetching-data-from-a-graphql-api-3adh>

经常听到有人将 GraphQL 描述为 REST 杀手。在这里削减你的休息服务，结束你的端点。但是今天，我们不要把焦点放在冲突上。让我们考虑一下 GraphQL 和 REST 的共同点:HTTP 请求。

没错。如果您知道如何发送 HTTP 请求，那么您已经拥有了构建与任何 GraphQL API 通信的客户端应用程序所必需的工具。我们可以使用任何发送 HTTP 请求的方法来获取数据。让我们使用`fetch`构建一个小客户端，它将在浏览器中工作:

```
const query = `
  query {
    Lift(id: "panorama") {
      name
      status
    }
  }
`;

const url = "https://snowtooth.moonhighway.com/";

const opts = {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ query })
};

fetch(url, opts)
  .then(res => res.json())
  .then(console.log)
  .catch(console.error); 
```

我们将发送带有一些选项的`fetch`请求。然后我们将把响应转换成 JSON。当我们将结果记录到控制台时，我们将看到以下内容:

**结果**

```
{  "data":  {  "Lift":  {  "name":  "Panorama",  "status":  "HOLD"  }  }  } 
```

您甚至可以在 HTML 页面上显示您的结果，方法是用一些简单的 DOM 操作:
替换`console.log`

```
fetch(url, opts)
  .then(res => res.json())
  .then(
    ({ data }) => `
        <p>
          Favorite Lift: ${data.Lift.name} Status: ${data.Lift.status} </p>
  `
  )
  .then(text => (document.body.innerHTML = text))
  .catch(console.error); 
```

### 同`fetch`突变

每当我们想要改变数据时，我们将使用 GraphQL 变异。我们的 API 支持一种叫做`setLiftStatus`的变异。这个变异接受你想要改变的`Lift`的`id`和那个电梯的新`status`。`LiftStatus`的选项是一个 GraphQL 枚举，一个特定字段的受限选项列表。这些选项是`OPEN`、`CLOSED`和`HOLD`。首先将`setLiftStatus`突变字符串定义为一个变量:

```
const mutation = `
  mutation {
    setLiftStatus(id: "panorama", status: CLOSED) {
      name
      status
    }
  }
`; 
```

然后我们希望使用相同的 GraphQL 端点。我们将调整请求的主体，以包含突变:

```
var url = "https://snowtooth.moonhighway.com/";

var opts = {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ query: mutation })
}; 
```

最后，我们可以发送请求并记录结果:

```
fetch(url, opts)
  .then(res => res.json())
  .then(console.log)
  .catch(console.error); 
```

我们的结果将反映出全景图的提升状态确实被突变改变了:

**结果**

```
{  "data":  {  "Lift":  {  "name":  "Panorama",  "status":  "CLOSED"  }  }  } 
```

### 图 ql 请求

还有其他一些框架可以用来向 API 发送 GraphQL 操作。一个最简单的例子就是`graphql-request`。GraphQL 请求将`fetch`请求包装在一个承诺中，该承诺可用于向 GraphQL 服务器发出请求。它还为您处理发出请求和解析数据的细节。`graphql-request`由[普里斯马](https://prisma.io)的团队维护。

要开始使用`graphql-request`，您需要安装它:

```
npm install graphql-request --save 
```

从那里，您将导入并使用模块作为`request` :

```
import { request } from "graphql-request";

const query = `
  query {
    Trail(id: "grandma") {
      name
      status
    }
  }
`;

request("https://snowtooth.moonhighway.com/graphql", query)
  .then(console.log)
  .catch(console.error); 
```

请求函数接收`url`和`query`，向服务器发出请求，并在一行代码中返回数据。正如所料，返回的数据是所有用户的 JSON 响应:

```
{  "Trail":  {  "name":  "Grandma",  "status":  "OPEN"  }  } 
```

请注意，这里的结果与`fetch`请求略有不同。用`graphql-request`发送的请求只返回对象，而不是返回`data`键下的数据。如果你想在你的应用中使用这些数据，你可以通过`Trail`键访问这些数据。

在客户机上使用 GraphQL 不需要大量的设置。从这些选项中的一个开始，立即看到 GraphQL 的好处。

*这篇文章最初发表在[月球公路网站](https://moonhighway.com/fetching-data-from-a-graphql-api)上。*
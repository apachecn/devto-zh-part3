# 我创建了 graphql-rest-proxy，它将您的 REST api 转换为 graphql 服务器

> 原文：<https://dev.to/acro5piano/i-created-graphql-rest-proxy-which-converts-your-rest-api-to-graphql-server-5heg>

我已经创建了 graphql-rest-proxy。该工具支持将 GraphQL 请求代理给 REST api。

[https://github.com/acro5piano/graphql-rest-proxy](https://github.com/acro5piano/graphql-rest-proxy)

我们都知道 GraphQL 很棒，所以你想从 REST api 迁移到 GraphQL。

然而，将您当前的 REST api 替换为全新的 GraphQL 服务器需要付出很大的努力。

`graphql-rest-proxy`来解决这个问题！它根据定义的模式将 GraphQL 代理给 REST API。

[![image](img/697c4134a491c230bf5b6be362ade27d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QIilWt0L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/acro5piano/graphql-rest-proxy/blob/mastimg/how-it-works.png%3Fraw%3Dtrue)

# 安装

```
npm -g install graphql-rest-proxy 
```

Enter fullscreen mode Exit fullscreen mode

或者如果使用纱线:

```
yarn global add graphql-rest-proxy 
```

Enter fullscreen mode Exit fullscreen mode

# 入门

**第一步。定义您的模式。**

`schema.graphql`

```
type  User  {  id:  Int  name:  String  isActive:  Boolean  }  type  Query  {  getUser:  User  @proxy(get:  "https://my-rest-api.com/user")  } 
```

Enter fullscreen mode Exit fullscreen mode

**第二步。运行您的代理服务器。**

```
graphql-rest-proxy schema.graphql

# => graphql-rest-proxy is running on http://localhost:5252 
```

Enter fullscreen mode Exit fullscreen mode

**第三步。请求！**

```
curl  -XPOST  -H  'Content-Type:  application/json'  \  -d  '{  "query":  "{  getUser  {  id  name  isActive  }  }"  }'  \  http://localhost:5252/graphql 
```

Enter fullscreen mode Exit fullscreen mode

它会这样返回:

```
{  "data":  {  "getUser":  {  "id":  1,  "name":  "Tom",  "isActive":  false  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

# 例子

**基本查询代理**

```
type  User  {  id:  Int  name:  String  }  type  Query  {  getUser:  User  @proxy(get:  "https://my-rest-api.com/user")  getUsers:  [User]  @proxy(get:  "https://my-rest-api.com/users")  } 
```

Enter fullscreen mode Exit fullscreen mode

**带参数查询**

可以通过`$id`引用查询参数的 id。

```
type  User  {  id:  Int  name:  String  }  type  Query  {  getUserById(id:  Int!):  User  @proxy(get:  "https://my-rest-api.com/users/$id")  } 
```

Enter fullscreen mode Exit fullscreen mode

**输入参数突变**

将`variables`突变到 REST API。

```
type  UserInput  {  name:  String!  }  type  User  {  id:  Int  name:  String  }  type  Mutation  {  createUser(user:  UserInput!):  User  @proxy(post:  "https://my-rest-api.com/users")  updateUser(id:  Int!,  user:  UserInput!):  User  @proxy(patch:  "https://my-rest-api.com/users/$id")  } 
```

Enter fullscreen mode Exit fullscreen mode

请求示例:

```
fetch('http://localhost:5252/graphql', {
  method: 'patch',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    query: gql`
      mutation UpdateUser($id: Int!, $user: UserInput!) {
        updateUser(id: $id, user: $user) {
          id
          name
        }
      }
    `,
    variables: {
      id: 1,
      user: {
        name: 'acro5piano',
      },
    },
  }),
}) 
```

Enter fullscreen mode Exit fullscreen mode

**嵌套对象**

您可以通过`$id`引用父对象的 id。

```
type  Post  {  id:  Int  title:  String  }  type  User  {  id:  Int  name:  String  posts:  [Post]  @proxy(get:  "https://my-rest-api.com/users/$id/posts")  }  type  Query  {  getUser:  User  @proxy(get:  "https://my-rest-api.com/user")  } 
```

Enter fullscreen mode Exit fullscreen mode

更多信息，请看看[https://github.com/acro5piano/graphql-rest-proxy](https://github.com/acro5piano/graphql-rest-proxy)

# 发展状况

仍处于测试阶段。如果您有任何建议或功能请求，请随时打开新问题或提出请求！

待办事项:

*   [ ]更多类型支持
    *   [ ]片段
    *   [ ]标量
*   [ ]重构
*   [ ]日志记录

请尝试一下，并让我知道您的反馈/意见。

# 侧注

之前用 Rollup.js 创建 npm 包，这次尝试了@pika/pack。

[https://www.pikapkg.com/blog/introducing-pika-pack/](https://www.pikapkg.com/blog/introducing-pika-pack/)

@pika/pack 是一个很棒的工具，减少了很多步骤和学习成本。例如，我们可以设置可执行命令、代码嵌入、版本管理等等。以后想写一篇关于@pika/pack 的文章。
# 用 Node.js 和 GraphQL 构建一个 CRUD 应用

> 原文：<https://dev.to/oktadev/build-a-crud-app-with-node-js-and-graphql-n0m>

构建一个简单的 CRUD(创建、读取、更新、删除)应用程序有很多选择。到目前为止，最新的数据访问层是 GraphQL。这很棒，因为开发人员可以使用简单的强类型语言来定义模型及其关系，然后提供函数来定义每个部分应该如何解决。然后，用户可以挑选他们想要的部分，GraphQL 服务器只收集服务请求所需的信息。

GraphQL 不仅是一个真正强大的工具，而且后端和前端开发人员使用起来都很有趣。今天，我将向您展示如何使用 GraphQL 创建一个简单的 CRUD 应用程序，以便能够查询和编辑一组报价。使用 Okta，我还将向您展示如何在 GraphQL 中验证用户，以防止匿名用户编辑现有报价。

## 为 Node.js 应用程序创建 GraphQL 服务器

首先，您需要为 Node.js 设置一个`package.json`来控制您的依赖项。安装`eslint`来帮助您提前捕捉代码中的错误也是一个好主意。大多数编辑器都有某种`eslint`插件，这样你就可以在你写代码的时候看到错误。

```
mkdir node-graphql
cd node-graphql
npm init -y
npm install --save-dev eslint@5.16.0 
```

在这个目录下创建一个新文件`.eslintrc`来添加一些基本的设置，这样 eslint 就可以对你正在使用的环境有所了解:

```
{  "extends":  "eslint:recommended",  "parserOptions":  {  "ecmaVersion":  2018  },  "env":  {  "es6":  true,  "node":  true  }  } 
```

现在编辑你的`package.json`文件，使`scripts`部分看起来像这样:

```
{  "start":  "node .",  "test":  "eslint ."  } 
```

您的编辑器应该内联给您警告，但是您现在也可以随时运行`npm test`来获得错误和警告的完整列表。

对于 GraphQL 服务器，Apollo 服务器是快速启动和运行的好方法。您还需要创建不同的 id 来跟踪您的报价，因此您可以使用`uuid`来实现这一点。用以下内容安装这些依赖项:

```
npm install apollo-server@2.5.0 graphql@14.3.0 uuid@3.3.2 
```

现在创建一个新文件`index.js`，它将成为服务器的主文件。它应该是这样的:

```
const { ApolloServer, gql } = require('apollo-server');
const uuid = require('uuid/v4');

const typeDefs = gql`
  type Quote {
    id: ID!
    phrase: String!
    quotee: String
  }

  type Query {
    quotes: [Quote]
  }
`;

const quotes = {};
const addQuote = quote => {
  const id = uuid();
  return quotes[id] = { ...quote, id };
};

// Start with a few initial quotes
addQuote({ phrase: "I'm a leaf on the wind. Watch how I soar.", quotee: "Wash" });
addQuote({ phrase: "We're all stories in the end.", quotee: "The Doctor" });
addQuote({ phrase: "Woah!", quotee: "Neo" });

const resolvers = {
  Query: {
    quotes: () => Object.values(quotes),
  },
};

const server = new ApolloServer({ typeDefs, resolvers });

server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`); // eslint-disable-line no-console
}); 
```

`typeDefs`定义了数据的结构。这将为您的用户生成一些有趣的文档，并使推理对象及其关系变得容易。`Query`类型是一种特殊的类型，它告诉 GraphQL 用户可以查询什么，可以传入什么参数(如果有的话),以及将返回什么。

GraphQL 服务器的下一个重要部分是如何实际解决这些查询。这些被称为`resolvers`，只是一组返回数据或数据模型的函数。这里我们只返回普通对象，GraphQL 只显示请求的内容。您还可以使用一个带有 getterss 的类对象，这些 getter 只有在被请求时才会运行，因此如果用户没有请求这些信息，就不一定需要执行更复杂的计算。

在这里，我们只是使用一个简单的 JavaScript 对象来使事情快速进行，所以我们所有的报价都将存储在内存中。您还可以将解析器中多个地方的部分拼凑在一起。例如，您可以从数据库或一些外部 API 获取数据。

您的服务器现已准备就绪。为了启动它，从你的项目文件夹中运行`npm start`。这将在`http://localhost:4000`启动一个服务器。这将把你带到一个检查你的`typeDefs`的游乐场，自动添加一些你可以搜索的文档。它有各种各样的其他功能，像自动完成和显示错误。

[![Cool Features](img/7aaa321aecdb51297aaeda9279b05fd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zlGLRO2E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-graphql/cool-features-1e8387e0e869839f707201a3af4b7875c35688bd57b1e34574ada7ad0a740e82.png)

去查看一下，并尝试运行一个简单的查询来查看现有的报价。

[![Simple GraphQL Query](img/c10e613ee73e7244e01d9c144b9c406d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LQyR1lMe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-graphql/simple-graphql-query-5558467b13c75098d85f18d44cc7cea04ee0517a0e1c86d1da11b0c916a0f08a.png)

## 将 CRUD 的 CUD 部分添加到你的 GraphQL Node.js App 中

您现在能够从服务器读取数据，但是为了拥有一个完整的 CRUD 应用程序，您需要能够创建、更新和删除。在 GraphQL 中，编辑数据是通过变异完成的。首先在`typeDefs`中定义一些新的类型。

```
 type  Mutation  {  addQuote(phrase:  String!,  quotee:  String):  Quote  editQuote(id:  ID!,  phrase:  String,  quotee:  String):  Quote  deleteQuote(id:  ID!):  DeleteResponse  }  type  DeleteResponse  {  ok:  Boolean!  } 
```

然后，您需要添加解析器来处理这些类型。您已经有了一个`addQuote`函数，因此解析器将是最简单的。解析器将需要返回新的/编辑过的报价，除了在`deleteQuote`的实例中。因为报价已经不存在，所以返回它没有意义，所以相反，你可以只返回一个`ok`或者`true`或者`false`，这取决于删除是否成功。

```
const resolvers = {
  // Add below existing Query resolver
  Mutation: {
    addQuote: async (parent, quote) => {
      return addQuote(quote);
    },
    editQuote: async (parent, { id, ...quote }) => {
      if (!quotes[id]) {
        throw new Error("Quote doesn't exist");
      }

      quotes[id] = {
        ...quotes[id],
        ...quote,
      };

      return quotes[id];
    },
    deleteQuote: async (parent, { id }) => {
      const ok = Boolean(quotes[id]);
      delete quotes[id];

      return { ok };
    },
  },
}; 
```

重启服务器(您可以使用`ctrl-c`来停止它，然后重新运行`npm start`)，然后继续尝试。下面是一些示例查询和变异:

```
mutation  Create  {  addQuote(phrase:  "You know nothing, Jon Snow.")  {  id  }  }  query  Read  {  quotes  {  id  phrase  quotee  }  }  mutation  Update($id:  ID!)  {  editQuote(id:  $id,  quotee:  "Ygritte")  {  id  phrase  quotee  }  }  mutation  Delete($id:  ID!)  {  deleteQuote(id:  $id)  {  ok  }  } 
```

**注意**:一旦你得到了你想要更新或删除的东西的 id，你需要把这个 id 作为一个变量传入。可以点击页面底部的`QUERY VARIABLES`链接展开变量编辑器；然后你需要使用 JSON 来传递变量。例如:

```
{  "id":  "4ef19b4b-0348-45a5-9a9f-6f68ca9a62e6"  } 
```

## 向 Node.js 应用程序添加用户验证

一个非常常见的流程是允许任何人至少读取数据的一个子集，但是只允许经过身份验证的用户写数据来修改数据。您已经实现了应用程序的整个 CRUD 部分，但是添加身份验证非常简单，因此您可以阻止匿名用户访问应用程序的某些部分。

这就是 Okta 发挥作用的地方。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

如果你还没有，那么[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)。

你需要保存一些信息，以便在应用程序中使用。创建一个名为`.env`的新文件。在其中，输入您的组织 URL。

```
OKTA_ORG_URL=https://{yourOktaOrgUrl} 
```

接下来，登录您的开发人员控制台，导航到**应用**，然后单击* *添加应用**。选择**本土**，然后点击**下一步**。不用担心它只提到了原生应用的 iOS 和 Android。这对于直接从 GraphQL 进行身份验证是必要的。GraphQL 服务器将拥有一个用于生成安全 JWT 的客户端秘密，它不会暴露给用户。

在下一页，给你的应用命名，并确保在点击**完成**之前选择**资源所有者密码**。

[![Create New Application Settings](img/ea8f467d88f76eda87fd78c3da364453.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e07z3rVg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-graphql/create-new-application-settings-6c2c57b48537021c8b65d273b2df2d96a392bd17bf0aa522ca85ccbfe36b2444.png)

创建应用程序后，点击**客户端凭证**部分的**编辑**。将**客户端认证**改为**使用客户端认证**。这将生成一个客户端机密。

[![Use Client Authentication](img/0621c8d5ac68aa00017dc455109aec24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f3VjWEyF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-graphql/use-client-authentication-00a5f09103a09444ec6a2a13434e9f0c9d865f38dfb18bad8ebc53569439a1c4.png)

将客户端 ID 和密码保存到您的`.env`文件:

```
OKTA_CLIENT_ID={yourClientID}
OKTA_CLIENT_SECRET={yourClientSecret} 
```

您需要从 Okta 获得的最后一条信息是一个 API 令牌。在您的开发者控制台中，导航到**API**->-**令牌**，然后点击**创建令牌**。您可以有许多标记，所以只需给这个标记一个提醒您它的用途的名称，比如“GraphQL Quotes”。你会得到一个你现在只能看到的令牌。如果您丢失了令牌，您必须重新创建一个。也将此添加到`.env`中。

```
OKTA_TOKEN={yourOktaAPIToken} 
```

为了让您的代码加载`.env`文件，您需要安装一个名为`dotenv`的新依赖项。运行以下命令:

```
npm install dotenv@8.0.0 
```

然后在您的`index.js`文件的最顶端，添加下面一行:

```
require('dotenv').config(); 
```

现在创建一个名为`auth.js`的新文件。在这里，您将创建一些为用户生成令牌、验证所提供的令牌以及获取有关用户的更多信息所需的实用函数。

您需要引入更多的依赖项:

```
npm install @okta/jwt-verifier@0.0.15 @okta/okta-sdk-nodejs@2.0.0 node-fetch@2.6.0 
```

在您的`auth.js`文件的开头，添加以下`require`语句:

```
const fetch = require('node-fetch');
const { AuthenticationError } = require('apollo-server');
const JWTVerifier = require('@okta/jwt-verifier');
const okta = require('@okta/okta-sdk-nodejs'); 
```

您将需要一个函数来为用户生成令牌。用户将提供他们的用户名和密码，然后您将这些信息转发给 Okta 的 API 并返回一个令牌。如果认证失败，抛出一个用户将看到的错误:

```
const basicAuth = Buffer.from(
  [
    process.env.OKTA_CLIENT_ID,
    process.env.OKTA_CLIENT_SECRET,
  ].join(':')
).toString('base64');

const getToken = async ({ username, password }) => {

  const response = await fetch(`${process.env.OKTA_ORG_URL}/oauth2/default/v1/token`, {
    method: 'POST',
    headers: {
      authorization: `Basic ${basicAuth}`,
      'accept': 'application/json',
      'content-type': 'application/x-www-form-urlencoded',
    },
    body: new URLSearchParams({
      username,
      password,
      grant_type: 'password',
      scope: 'openid',
    }).toString(),
  });

  const { error_description, access_token } = await response.json();

  if (error_description) throw new AuthenticationError(error_description);

  return access_token;
}; 
```

一旦用户登录，他们将使用他们的令牌作为认证，而不是他们的用户名和密码。您需要一种方法来验证令牌是合法的(例如，具有有效的签名并且没有过期)。这个函数将返回一个有效的、经过身份验证的用户的用户 ID。否则将返回`undefined`。

```
const verifier = new JWTVerifier({
  issuer: `${process.env.OKTA_ORG_URL}/oauth2/default`,
  clientId: process.env.OKTA_CLIENT_ID,
});

const getUserIdFromToken = async (token) => {
  if (!token) return;

  try {
    const jwt = await verifier.verifyAccessToken(token)
    return jwt.claims.sub;
  } catch (error) {
    // ignore
  }
}; 
```

您可能还需要关于您的用户的更详细的信息，例如他们的姓名。使用 Okta 的 Node SDK:
可以得到这个

```
const client = new okta.Client({
  orgUrl: process.env.OKTA_ORG_URL,
  token: process.env.OKTA_TOKEN,
});

const getUser = async (userId) => {
  if (!userId) return;

  try {
    const user = await client.getUser(userId);
    return user.profile;
  } catch (error) {
    // ignore
  }
}; 
```

您还需要导出这些函数，以便在`index.js` :
中使用

```
module.exports = { getToken, getUserIdFromToken, getUser }; 
```

最终的`auth.js`文件应该是这样的:

```
const fetch = require('node-fetch');
const { AuthenticationError } = require('apollo-server');
const JWTVerifier = require('@okta/jwt-verifier');
const okta = require('@okta/okta-sdk-nodejs');

const basicAuth = Buffer.from(
  [
    process.env.OKTA_CLIENT_ID,
    process.env.OKTA_CLIENT_SECRET,
  ].join(':')
).toString('base64');

const getToken = async ({ username, password }) => {

  const response = await fetch(`${process.env.OKTA_ORG_URL}/oauth2/default/v1/token`, {
    method: 'POST',
    headers: {
      authorization: `Basic ${basicAuth}`,
      'accept': 'application/json',
      'content-type': 'application/x-www-form-urlencoded',
    },
    body: new URLSearchParams({
      username,
      password,
      grant_type: 'password',
      scope: 'openid',
    }).toString(),
  });

  const { error_description, access_token } = await response.json();

  if (error_description) throw new AuthenticationError(error_description);

  return access_token;
};

const verifier = new JWTVerifier({
  issuer: `${process.env.OKTA_ORG_URL}/oauth2/default`,
  clientId: process.env.OKTA_CLIENT_ID,
});

const getUserIdFromToken = async (token) => {
  if (!token) return;

  try {
    const jwt = await verifier.verifyAccessToken(token)
    return jwt.claims.sub;
  } catch (error) {
    // ignore
  }
};

const client = new okta.Client({
  orgUrl: process.env.OKTA_ORG_URL,
  token: process.env.OKTA_TOKEN,
});

const getUser = async (userId) => {
  if (!userId) return;

  try {
    const user = await client.getUser(userId);
    return user.profile;
  } catch (error) {
    // ignore
  }
};

module.exports = { getToken, getUserIdFromToken, getUser }; 
```

现在回到`index.js`，您需要将用户添加到上下文中，以便解析器可以很容易地看到是谁在尝试发出请求。在文件顶部附近导入新函数(通常所有导入都在任何其他代码之前完成，本地导入在从外部依赖项导入之后完成)。当用户在编辑过程中没有登录时，您还将抛出一个`AuthenticationError`，所以请确保也导入它:

```
const { ApolloServer, AuthenticationError, gql } = require('apollo-server');
const uuid = require('uuid/v4');

const { getToken, getUserIdFromToken, getUser } = require('./auth'); 
```

通过将此添加到您的`typeDefs` :
中，为您的用户创建一个新的登录突变

```
type  Mutation  {  # ...  login(username:  String!,  password:  String!):  Authentication  }  type  Authentication  {  token:  String!  } 
```

您的登录变异解析器应该如下所示:

```
 login: async (parent, { username, password }) => ({
    token: await getToken({ username, password }),
  }), 
```

为了让解析器知道用户是否经过身份验证，推荐的方法是将用户添加到上下文中。上下文是在任何解析器被命中之前构建的，然后传递给每个解析器，因此只需要在任何请求开始时进行身份验证。创建一个新的`context`函数，并将其传递给 Apollo 服务器。

```
const context = async ({ req }) => {
  const [, token] = (req.headers.authorization || '').split("Bearer ");

  return {
    user: await getUser(await getUserIdFromToken(token)),
  };
};

const server = new ApolloServer({ typeDefs, resolvers, context }); 
```

为了将这些拼凑在一起，您现在可以在实际执行任何工作之前，在添加、编辑和删除变异中抛出一个错误，当然，除非用户正确登录。为了检查用户，您需要添加`context`作为解析器的第三个输入参数。

```
 addQuote: async (parent, quote, context) => {
    if (!context.user) throw new AuthenticationError("You must be logged in to perform this action");
    // ...etc
  },
  editQuote: async (parent, { id, ...quote }, context) => {
    if (!context.user) throw new AuthenticationError("You must be logged in to perform this action");
    // ...etc
  },
  deleteQuote: async (parent, { id }, context) => {
    if (!context.user) throw new AuthenticationError("You must be logged in to perform this action");
    // ...etc
  }, 
```

最后，您的`index.js`文件应该如下所示:

```
require('dotenv').config();

const { ApolloServer, AuthenticationError, gql } = require('apollo-server');
const uuid = require('uuid/v4');

const { getToken, getUserIdFromToken, getUser } = require('./auth');

const typeDefs = gql`
  type Quote {
    id: ID!
    phrase: String!
    quotee: String
  }

  type Query {
    quotes: [Quote]
  }

  type Mutation {
    login(username: String!, password: String!): Authentication
    addQuote(phrase: String!, quotee: String): Quote
    editQuote(id: ID!, phrase: String, quotee: String): Quote
    deleteQuote(id: ID!): DeleteResponse
  }

  type Authentication {
    token: String!
  }

  type DeleteResponse {
    ok: Boolean!
  }
`;

const quotes = {};
const addQuote = quote => {
  const id = uuid();
  return quotes[id] = { ...quote, id };
};

addQuote({ phrase: "I'm a leaf on the wind. Watch how I soar.", quotee: "Wash" });
addQuote({ phrase: "We're all stories in the end.", quotee: "The Doctor" });
addQuote({ phrase: "Woah!", quotee: "Neo" });

const resolvers = {
  Query: {
    quotes: () => Object.values(quotes),
  },
  Mutation: {
    login: async (parent, { username, password }) => ({
      token: await getToken({ username, password }),
    }),
    addQuote: async (parent, quote, context) => {
      if (!context.user) throw new AuthenticationError("You must be logged in to perform this action");

      return addQuote(quote);
    },
    editQuote: async (parent, { id, ...quote }, context) => {
      if (!context.user) throw new AuthenticationError("You must be logged in to perform this action");

      if (!quotes[id]) {
        throw new Error("Quote doesn't exist");
      }

      quotes[id] = {
        ...quotes[id],
        ...quote,
      };

      return quotes[id];
    },
    deleteQuote: async (parent, { id }, context) => {
      if (!context.user) throw new AuthenticationError("You must be logged in to perform this action");

      const ok = Boolean(quotes[id]);
      delete quotes[id];

      return { ok };
    },
  },
};

const context = async ({ req }) => {
  const [, token] = (req.headers.authorization || '').split("Bearer ");

  return {
    user: await getUser(await getUserIdFromToken(token)),
  };
};

const server = new ApolloServer({ typeDefs, resolvers, context });

server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`); // eslint-disable-line no-console
}); 
```

## 测试你的认证

重启你的服务器，现在一切都准备好了。尝试运行一些突变，你会发现你一开始会得到一个错误。如果你在开发模式下，你会得到一个堆栈跟踪，但是如果你在生产模式下运行(比如用`NODE_ENV=production npm start`)，你只会看到错误代码。

[![Authentication Error](img/743aa01c116474d07c0fc77bbb3d6b74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_yliCqQ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-graphql/authentication-error-42662daf05164ec5f598dc564f7fefce6d0a4465c7dccb2a965b2b6283fd277d.png)

为了登录，运行`login`突变。您可以像这样以内联方式提供输入:

```
mutation  {  login(username:  "myusername@example.com",  password:  "hunter2")  {  token  }  } 
```

或者您可以使用变量来代替:

[![Login Mutation With Variables](img/9f45937e9004085b76f16f200f3233ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1y4nMa2n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-graphql/login-mutation-with-variables-8937435d680417bbdb7451888999dc7526e7ccf5fecbf9acd3e305d2497db390.png)

如果你提供正确的用户名和密码，你将得到一个令牌回来。复制这个令牌，然后点击屏幕底部的`HTTP HEADERS`并进入`{ "Authorization": "Bearer eyJraWQiOi...1g6Kdicw" }`(尽管使用你从`login`突变中获得的完整的、长得多的令牌)。

再试一次，你应该能够成功地编辑报价。

[![Successful Authenticated Edit](img/d0c09d006783e735970b887775b9a84c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qCc3FBxE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/node-graphql/successful-authenticated-mutation-1c77ef75269d16ff2680bbfb4edde317c37911de883c784fc1f2d74eb0df667f.png)

## 了解有关 GraphQL、Node.js 和安全认证的更多信息

我希望您在学习 GraphQL 和体验操场的过程中获得了乐趣。这个项目没有太多复杂的部分，但是您有身份验证，能够读写数据集，并且为与您共享 API 的任何人提供了很好的文档。您可以看到扩展类型定义和解析器是多么容易，从而构建更有实质意义的东西。

如果你想看最终的样本代码，可以在 github 上找到[。](https://github.com/oktadeveloper/okta-node-graphql-crud-example)

如果您想了解更多关于 GraphQL 或 Node 的知识，请查看 Okta 开发者博客上的其他文章:

*   [使用 React、GraphQL 和用户认证构建健康跟踪应用](https://developer.okta.com/blog/2018/07/11/build-react-graphql-api-user-authentication)
*   [建立并理解一个简单的 Node.js 网站，并进行用户认证](https://developer.okta.com/blog/2018/08/17/build-and-understand-user-authentication-in-node)
*   [用 Express、React 和 GraphQL 构建一个简单的 Web 应用](https://developer.okta.com/blog/2018/10/11/build-simple-web-app-with-express-react-graphql)
*   [教程:用 Node.js 构建一个基本的 CRUD App](https://developer.okta.com/blog/2018/06/28/tutorial-build-a-basic-crud-app-with-node)
*   [用 Express 和 GraphQL 构建一个简单的 API 服务](https://developer.okta.com/blog/2018/09/27/build-a-simple-api-service-with-express-and-graphql)

如果你对这篇文章有任何问题，请在下面添加评论。更多精彩内容，请在 Twitter 上关注 [@oktadev](https://twitter.com/oktadev) ，就像我们在脸书关注[，或者订阅](https://www.facebook.com/oktadevelopers/)[我们的 YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。
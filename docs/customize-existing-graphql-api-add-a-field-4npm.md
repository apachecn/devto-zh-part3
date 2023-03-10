# 自定义现有的 GraphQL API /添加字段

> 原文：<https://dev.to/onelittlenightmusic/customize-existing-graphql-api-add-a-field-4npm>

## TL；速度三角形定位法(dead reckoning)

您可以使用**模式拼接技术**(附加模式和`mergeSchema`)向现有的 GraphQL API 添加新字段，并将其作为另一个 API 发布。

```
const addition = gql`
extend type TypeA {
    fieldA: Int
}
`
const schema = mergeSchemas({
    schemas:[original, addition], 
    resolvers: { 
        TypeA: {
            fieldA: {
                resolve: (p, a, c, i) => funcA(p),
                fragment: fragmentRequired // for funcA
            },
        }
    }
}); 
```

## 概念

GraphQL 是优秀的数据发布和获取 API。GraphQL 的模式是一个优势，它使得共享已发布数据的模式信息变得非常容易。

当我们更深入地使用 GraphQL 时，我们可以**在现有的 GraphQL API 中添加一个新字段，并轻松地将其发布为新的 GraphQL API** 。
例如，您可以添加简单的统计字段如“总和”或“平均值”来返回值计算现有字段值。

我们利用事实工具`graphql-tools`提供的**模式拼接技术**。

## 样本代码

该示例代码将 Github API v4 作为现有的 GraphQL API。(您必须拥有 Github 访问令牌)

我们将样本部署到 AWS。但是如果你没有 AWS 账号，可以离线测试。

以下是全部源代码。
另存为`graphql.js`。

```
const { ApolloServer, gql } = require('apollo-server-lambda');
const {makeRemoteExecutableSchema, mergeSchemas, introspectSchema } = require('graphql-tools');
const fetch = require('node-fetch');
const { HttpLink } = require('apollo-link-http');

// (Just a preparation) function for fetching Github schema
const createRemoteSchema = async () => {
    const uri = 'https://api.github.com/graphql';
    const headers = { Authorization: `bearer <GITHUB_ACCESS_TOKEN>`}; // Change to your own Github token
    const link = new HttpLink({uri, fetch, headers});
    return makeRemoteExecutableSchema({
        schema: await introspectSchema(link),
        link
    });
};

// New two functions (A, B) added as fields to a type "Organization" in Github schema.
// NOTE: these functions requires "Organization.repositories.stargazers.totalCount".
// "repos" means repositories, but the name is changed because of a reason to explain later.

// Function A: get stargazer number sum of multiple repositories.
const funcA = (organization) => {
    const array = organization.repos.nodes.map(e => e.stargazers.totalCount)
    return array.reduce((a,b) => a + b)
}

// Function B: get max from stargazer numbers of multiple repositories.
const funcB = (organization) => 
  Math.max.apply(Math, organization.repos.nodes.map(e => e.stargazers.totalCount))

// New schema incluging new fields.
const createNewSchema = async () => {
    // 1\. Original Github schema
    const originalSchema = await createRemoteSchema();

    // 2\. Schema extension to add new field
    const schemaExtension = gql`
        extend type Organization {
            "countSum: new field for Function A (calculate sum)" # comment for Function A
            countSum: Int # Function A
            "countMax: new field for Function A (get sum)" # comment for Function B
            countMax: Int # Function B
        }
    `;

    // 3\. Fragment about which field must be prefetched.
    // IMPORTANT: Function A and B require "Organization.repositories.stargazers.totalCount". 
    // So I must set a new fragment for prefetching required data.
    // Fragment name cannot conflict with existing field name (e.g. "repositories"). 
    // This is why the name "repos" is changed.
    const fragmentRequired = `fragment repos on Organization {
            repos: repositories(first: 20) { # some arg required. 
                nodes {
                    stargazers {
                        totalCount
                    }
                }
          }
        }`;

    // 4\. final schema = 1\. original schema + 2\. schema extension (with 3\. required fragment)
    const finalSchema = mergeSchemas({
        schemas:[originalSchema, schemaExtension], // 1\. + 2.
        resolvers: { // resolvers for 2.
            Organization: {
                countSum: { // Function A
                    resolve: (parent, args, context, info) => funcA(parent),
                    fragment: fragmentRequired // 3.
                },
                countMax: { // Function B
                    resolve: (parent, args, context, info) => funcB(parent),
                    fragment: fragmentRequired
                }
            }
        }
    });

    return finalSchema
}

// (just a common pattern) start GraphQL server with new schema.
let handler
module.exports.graphqlHandler = async (event,context, callback) => {
    if(handler == null) {
        const server = new ApolloServer({ schema: await createNewSchema() });
        handler = server.createHandler();
    } else {
        console.log("Already initialized")
    }

    context.callbackWaitsForEmptyEventLoop = false;
    return new Promise((resolve, reject) => {
            handler(event, context, callback);
    });
} 
```

`package.json`就是这里。
与`graphql.js`保存在同一个目录下。

```
{  "name":  "apollo-lambda-test",  "version":  "1.0.0",  "main":  "index.js",  "license":  "MIT",  "dependencies":  {  "apollo-link-http":  "^1.5.14",  "apollo-server-lambda":  "^2.4.8",  "graphql":  "^14.2.1",  "node-fetch":  "^2.5.0",  "serverless-offline":  "^4.10.0"  }  } 
```

`serverless.yml`就是这里。

```
service: apollo-lambda
provider:
  name: aws
  runtime: nodejs8.10
functions:
  graphql:
    # this is formatted as <FILENAME>.<HANDLER>
    handler: graphql.graphqlHandler
    events:
    - http:
        path: graphql
        method: post
        cors: true
    - http:
        path: graphql
        method: get
        cors: true
plugins:
  - serverless-offline 
```

## 运行

```
yarn install # or npm install
serverless deploy # or serverless offline for offline test 
```

## 测试

在您的浏览器中，访问上一个命令输出中指定的 URL(如果您选择离线，则访问`localhost:3000/graphql`)。

[![](img/1bbde374304fb68ac27cea59422e54ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eSEpMHMD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jpiezyuy2egch3buzzcm.png)

运行以下查询。

这个查询包括字段“countSum”和“countMax”，这是我们添加到 Github API 中的。

```
query  simple  {  organization(login:  "serverless")  {  ...org  }  }  fragment  org  on  Organization  {  id  location  name  countSum  countMax  } 
```

您将看到 countSum 和 CountMax 的查询结果。

## 深潜

您可以在“模式”选项卡中找到新字段的定义。

[![](img/b281e48e2819402b430363c6844e6f78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BCv7_m-i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c1ec3782jc7ar7qvf73g.png)

在 Github 的现有 API 模式中，出现了新添加的定义。

在我们像这样更改查询之后，我们可以查看现有的“存储库”字段。

```
query simple
{
  organization(login: "serverless") {
    ...org
  }
}

fragment org on Organization {
  id
  location
  name
  countSum
  countMax
  # added----
  repositories(first: 2) {
    nodes {
      name
      stargazers {
        totalCount
      }
    }
  }
  # end----
} 
```

结果在这里。

[![](img/108a8e6be76cfa8333598c68b923025d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aWy9VX1Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rf8aj62lnp7vphymhvt7.png)

```
{  "data":  {  "organization":  {  "id":  "MDEyOk9yZ2FuaXphdGlvbjEzNzQyNDE1",  "location":  "San Francisco, CA",  "name":  "Serverless",  "countSum":  34419,  "countMax":  29759,  "repositories":  {  "nodes":  [  {  "name":  "serverless",  "stargazers":  {  "totalCount":  29759  }  },  {  "name":  "serverless-helpers-js",  "stargazers":  {  "totalCount":  11  }  }  ]  }  }  }  } 
```

但是现在很清楚，计算字段“countSum”是**而不是仅仅 2 个存储库**的总和。
这意味着**“countSum”在后台查询**预取的 20 个仓库的数据，并计算这些数据的总和。这就是片段的意义。
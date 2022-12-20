# GitHub GraphQL API 入门👨‍🔬

> 原文：<https://dev.to/thomasaudo/get-started-with-github-grapql-api--1g8b>

几年来，graph QLAPI 越来越受欢迎。本教程的目的不是教你 GraphQL 的概念和技术，而是教你如何查询 GitHub GraphQL API。
我将使用 [Node.js](https://fr.wikipedia.org/wiki/Node.js) 作为例子，但是你可以用任何允许你执行 [HTTP](https://fr.wikipedia.org/wiki/Hypertext_Transfer_Protocol) 请求的编程语言来实现它。

## 设置

首先，你需要创建一个[个人访问令牌](https://github.com/settings/tokens/new)。

```
github > settings > developper settings > personnal access token > generate new token 
```

Github 会询问你一个令牌描述和你的应用范围，选择你需要的。

现在我们有了令牌，是查询的时候了🎯

## 创建查询

为了测试和创建查询，GitHub 提供了一个非常强大的工具: [GraphQL API Explorer](https://developer.github.com/v4/explorer/) 。

对于这个例子，我创建了一个非常简单的查询来获取我的 GitHub 固定存储库，这对于 GitHub REST API 是不可能的。

```
query{
  repositoryOwner(login: "thomasaudo") {
    ... on User {
      pinnedRepositories(first: 6) {
        edges {
          node {
            name,
            description,
            url
          }
        }
      }
    }
  }
} 
```

文档真的完成了📒

## 查询

为了查询 GitHub 服务器，我们将需要向[唯一端点](https://api.github.com/graphql)
发送一个 post 请求，当然，我们必须稍微定制一下我们的请求:

*   一个参数:查询
*   承载认证也称为令牌认证:您的个人访问令牌(在报头中)

GitHub 官方文档中的一个 CURL 示例:

```
curl -H "Authorization: bearer token" -X POST -d " \
 { \
   \"query\": \"query { viewer { login }}\" \
 } \
" https://api.github.com/graphql 
```

我创建了一个非常简单的 nodeJS 示例。为了执行查询，我使用了“axios”包。

```
// Require axios to perform easy promise-based POST request
const axios = require('axios');
// Define constant
// Endpoint URL
const githubUrl = 'https://api.github.com/graphql'
// Your personal access token
const token = '7bacddc5d40dabfe6edca28b986a0247cfe3803b'
// The Authorization in the header of the request
const oauth = {Authorization: 'bearer ' + token}
// The GraphQL query, a string
const query = '{' +
                'repositoryOwner(login: "thomasaudo") { ' +
                  '... on User {' +
                    'pinnedRepositories(first: 6) {' +
                      'edges {' +
                        'node {' +
                          'name,' +
                          'description,' +
                          'url' +
                          '}' +
                        '}' +
                      '}' +
                    '}' +
                  '}' +
                '}'

// Post request, axios.post() return a Promise
axios.post(githubUrl, {query: query}, {headers: oauth})
  .then(function (response) {
    // On success, print the response
    console.log(response.data);
  })
  .catch(function (error) {
    On error, print the error
    console.log(error);
  }); 
```

我们到了！

我希望你喜欢我的第一篇文章🚀
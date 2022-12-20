# 使用 Lambda 授权器将身份验证从 Express.js 迁移到 API 网关

> 原文：<https://dev.to/paulswail/migrating-authentication-from-express-js-to-api-gateway-using-a-lambda-authorizer-450m>

*这是系列文章的第 6 部分[将单一的 SaaS 应用程序迁移到无服务器——决策日志](https://winterwindsoftware.com/serverless-migration-journal/)。*

在将任何路由从我的 Express.js API 迁移到 API Gateway + Lambda 之前，我首先需要实现一个身份验证和授权机制，以便 API 网关端点与它们的遗留 API 端点遵循相同的身份验证逻辑。

我对此的约束如下:

*   保留遗留应用程序正在使用的相同的后端 MongoDB 用户和会话存储，因为我想避免/最小化遗留应用程序的代码更改。这排除了使用 AWS Cognito 或 Auth0 等专用认证服务的可能性，而这些服务将是我在绿地应用中进行认证的第一站。
*   客户端对现有 API 进行身份验证，首先通过调用登录端点获取会话令牌，然后在后续请求中在 Cookie 或授权 HTTP 头中提供该令牌。这种行为需要在我的 API 网关实现中重现。
*   登录端点本身(即首先如何获得令牌)不在此任务的范围内，现在将继续使用遗留登录端点。
*   这将是一个临时的解决方案，因为我对这个迁移过程的长期目标是取代 MongoDB 作为我的后端数据存储。

## 使用 Lambda 授权器认证 API 请求

API Gateway 允许您定义一个 [Lambda 授权器](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html)来执行定制的认证和授权逻辑，然后才允许客户端访问他们所请求的实际 API 路由。Lambda Authorizer 函数有点类似于 Express.js 中的中间件，因为它在主路由处理器函数之前被调用，它可以直接拒绝请求，或者如果它允许请求继续，它可以用主路由处理器可以引用的额外数据(例如用户和角色信息)来增强请求事件。

## 认证 vs 授权

在我们深入实施细节之前，我想弄清楚这些相关的“auth”概念之间的区别，因为它们经常被混淆，AWS 命名的“Lambda Authorizer”在这里没有帮助:

*   *认证*就是验证你是谁的过程。当您使用正在进行身份验证的用户名和密码登录电脑或应用程序时。
*   *授权*是验证你有权访问某些东西的过程。因为资源上配置的权限允许您访问而获得对资源的访问就是授权。

*[(认证和授权有什么区别？](https://serverfault.com/questions/57077/what-is-the-difference-between-authentication-and-authorization#57082)-服务器故障)*

如果您正在实现一个 Lambda 授权器，您的函数将总是需要执行身份验证(即，确保您是您所说的那个人)，但不一定需要执行授权(即，检查您是否有权限访问您所请求的资源)。

在我的例子中，我决定(目前)我的 Lambda 授权器将只执行认证，并且授权逻辑将驻留在路由处理器函数中，因为不同的路由需要不同的权限。随着我开始将更多的路由迁移到 Lambda，我可能会决定将公共授权逻辑转移到共享的 Lambda 授权器。

*要深入了解使用 Lambda 授权器的不同策略，请查看[AWS Lambda 和 API 网关定制授权器的完整指南](https://www.alexdebrie.com/posts/lambda-custom-authorizers/)。*

## 逆向工程快速认证逻辑

我的遗留 API 使用了 [Passport.js](http://www.passportjs.org/) 和 [express-session](https://github.com/expressjs/session) 中间件。
我可以把这些模块导入我的 Lambda 授权函数。但是，我决定不这么做，原因如下:

*   这些模块是专门为与 Express 一起使用而构建的，所以我最终不得不从 Lambda 中以非标准的方式调用它们。
*   我不想给我的 Lambda 添加大量新的依赖项，并招致额外的冷启动开销和由此带来的安全威胁。

所以我决定在 Github 上检查这些模块的代码，并将必要的逻辑提取到我的 Lambda 函数中。我不会在这里分享完整的实现代码，但它遵循以下步骤来处理请求:

1.  从 HTTP 请求头(`Cookie`或`Authorization`头)获取令牌。
2.  使用会话密钥解密令牌并从中提取 SessionID。
3.  使用 SessionID，从 MongoDB 获取会话对象并获取存储在其中的用户数据。
4.  向请求上下文添加用户数据。

## 允许和拒绝请求

如果请求被成功认证，为了告诉 API Gateway 它可以继续调用所请求路由的处理程序，Lambda Authorizer 函数需要返回一个响应，该响应包含一个 IAM 策略文档，允许调用者调用对处理程序的访问。

下面是 Lambda Authorizer 函数为允许的请求返回的响应示例:

```
{  "principalId":  "my_user_id",  "policyDocument":  {  "Version":  "2012-10-17",  "Statement":  [  {  "Action":  "execute-api:Invoke",  "Effect":  "Allow",  "Resource":  "*"  }  ]  },  "context":  {  "userId":  "my_user_id",  "customerAccountId":  "my_customer_account_id",  "fullName":  "John Smith",  "roles":  "[]"  }  } 
```

注意这里的`context`对象，我在其中提供了存储在 MongoDB 中的用户记录的更多信息。API Gateway 使这个上下文数据对处理函数可用(我们将在下面介绍)。

这是一条令人愉快的道路，但是请求被拒绝的原因有很多，例如

*   未提供令牌
*   提供的令牌无效
*   会话过期

在每一种情况下，我都想向客户机发回一个 HTTP 401 未授权状态代码，但是从阅读 AWS 文档中并不能立即看出我该如何做。

在普通的 API Gateway Lambda 处理程序中，在响应中有一个可以设置的`statusCode`字段，但是 Lambda Authorizer 响应不是这样工作的。[示例](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html)展示了抛出一个错误(或者如果您使用遗留节点，在回调中传递一个错误)。然而，当我对此进行测试时，API Gateway 返回了一个 403 错误。我不知道发生了什么，直到我意识到错误消息中的实际字符串需要匹配 API Gateway 的内置消息之一- >状态代码映射。我没有意识到这一点，一直在使用我自己定制的错误字符串，API Gateway 不知道如何处理这些字符串，所以它只是默认返回 403。

```
import { CustomAuthorizerEvent, AuthResponse } from 'aws-lambda';

/** Built-in error messages that API Gateway auto-maps to HTTP status codes */
export enum APIGatewayErrorMessage {
    /** 401 */
    Unauthorized = 'Unauthorized',
    /** 403 */
    AccessDenied = 'Access Denied',
}

/** Lambda Authorizer handler */
export const handler = async (event: CustomAuthorizerEvent): Promise<AuthResponse> => {
    if (!event.headers) {
        // No token provided
        throw new Error(APIGatewayErrorMessage.Unauthorized);
    }
    // first check Authorization bearer header
    if (event.headers.Authorization) {
        const [key, val] = event.headers.Authorization.split(' ');
        if (key && key.toLowerCase() === 'bearer' && val) {
            return authenticateToken(val);
        }
        // Badly formed header
        throw new Error(APIGatewayErrorMessage.Unauthorized);
    }
    // ... rest of auth logic
}; 
```

## 将验证逻辑连接到私有端点

到目前为止，我已经介绍了 Lambda Authorizer 的实现，但是没有展示如何将它连接到想要保护的端点。因为我的服务中还没有真正的端点，所以我创建了一个测试`private-endpoint`。这个端点只是将从 Lambda Authorizer 传递给它的用户上下文数据返回给经过身份验证的客户端。

以下是我的`serverless.yml`文件的相关部分:

```
custom:
    vpcSettings:
      securityGroupIds:
        - !Ref MLabSecurityGroup
      subnetIds:
        - ${cf:vpc.SubnetAPrivate}
        - ${cf:vpc.SubnetBPrivate}
    lambda_authorizer:
        name: authorizer
        resultTtlInSeconds: 0
        identitySource: ''
        type: request

functions:
    # Lambda Authorizer function
    authorizer:
        handler: src/functions/authorizer.handler
        vpc: ${self:custom.vpcSettings}
        environment:
            SESSION_SECRET: ${ssm:/autochart/${self:provider.stage}/session-secret~true}
    private-endpoint:
        handler: src/functions/private-endpoint.handler
        vpc: ${self:custom.vpcSettings}
        events:
        - http:
            path: ${self:custom.apiRoot}/private
            method: get
            authorizer: ${self:custom.lambda_authorizer} 
```

首先，您会注意到，为了访问我的 MongoDB 数据库，我的函数需要在 VPC 内部。我还将一个`SESSION_SECRET`环境变量(从 SSM 参数存储中获取)传递给我的`authorizer`函数。这与传统 API 用来签署会话密钥的会话秘密相同。
`private-endpoint`函数的`http.authorizer`属性是端点处理器和授权者函数之间的连接。

然后，`private-endpoint`处理函数可以通过`event.requestContext.authorizer`字段访问自定义用户数据:

```
// src/functions/private-endpoint.ts
import { APIGatewayProxyEvent, APIGatewayProxyResult } from 'aws-lambda';

export const handler = wrap(async (event: APIGatewayProxyEvent): Promise<APIGatewayProxyResult> => {
    const response = {
        authContext: event.requestContext.authorizer,
    };
    return {
        statusCode: 200,
        body: JSON.stringify(response),
    };
}); 
```

## 要缓存还是不要缓存

API Gateway 允许您在一段时间内缓存 Lambda 授权者的响应。这很有用，因为它通过调用一个额外的函数和到 MongoDB 的往返来获取会话数据，避免了每个请求产生的额外延迟。虽然这看起来很谨慎，但我决定不在现阶段实施，原因如下:

*   现有的遗留 API 目前没有授权缓存，因此到 MongoDB 的往返不会增加额外的延迟。
*   缓存可能会引入奇怪的行为，并且需要跨新的和遗留 API 的复杂失效逻辑(例如，如果用户注销)。
*   我无法确定我的用例是否支持在 cookie 或授权头中使用 auth 令牌。API Gateway 允许您指定零个或多个“[身份源](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-authorizers-authorizerid.html#apis-apiid-authorizers-authorizerid-prop-updateauthorizerinput-identitysource)，它们规定了授权逻辑中所需的 HTTP 请求参数。如果指定了此参数，则该参数用于形成缓存键。然而，从我的测试来看，如果你提供了不止一个源，那么 API Gateway 会对每个参数进行 and 运算，结果就是要求客户端提供所有的头。这不适合我的用例。

在我观察了我的迁移端点的真实延迟后，我将重新考虑跳过身份验证缓存的决定。

## 下一步

现在我已经有了我的 auth 逻辑，我可以开始从遗留 API 迁移“事件度量”服务了。我将在下一篇文章中讨论这个问题。

✉️ ***如果你喜欢这篇文章，并希望从我这里获得关于迁移到无服务器的未来更新，你可以订阅[我关于在 AWS 中构建无服务器应用的每周简讯](https://winterwindsoftware.com/newsletter/)。***

* * *

您还可能会喜欢:

*   [担心无服务器带走](https://winterwindsoftware.com/concerns-that-serverless-takes-away/)
*   [“无服务器”的不同定义](https://winterwindsoftware.com/serverless-definitions/)
*   [无服务器词汇表](https://winterwindsoftware.com/serverless-glossary/)

*原载于 winterwindsoftware.com*。
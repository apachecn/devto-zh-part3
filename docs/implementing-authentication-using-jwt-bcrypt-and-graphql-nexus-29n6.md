# 使用 JWT、Bcrypt 和 GraphQL Nexus 实现身份验证

> 原文：<https://dev.to/novvum/implementing-authentication-using-jwt-bcrypt-and-graphql-nexus-29n6>

您已经完成了应用程序框架的编码，但是还缺少一样东西——身份验证。这可以使用 [JSON Web 令牌](https://jwt.io/)和 Bcrypt 来添加。对于大多数模式构建框架来说，本教程的基础应该是相似的，但是我们将使用 [GraphQL Nexus](https://nexus.js.org/) 。我们也使用 [Prisma](https://www.prisma.io/) 作为我们的 ORM，但是任何其他的 ORM 或者数据库也可以。

本教程假设您了解 GraphQL 的变异、查询、解析器和上下文——如果您不了解 GraphQL， [How to GraphQL](https://howtographql.com/) 是一个很好的起点。

最终的应用程序将允许用户通过存储和使用 JSON Web 令牌来创建帐户和登录。jwt 是包含要在各方之间传输的信息的字符串，是对用户进行身份验证的好方法，因为它们可以安全地存储用户信息并提供数字签名。

我们的应用程序将允许用户使用这些 jwt 登录和注册。在后端，我们将创建一个有效负载，添加一个 JWT 秘密，并设置登录和注册突变，以正确生成授权头。在前端，我们将把一个授权令牌传递到我们的头中，并设置我们的查询来获取当前登录的用户。

#### 后端

#### 1。安装我们的工具🛠

首先，我们需要安装 Bcrypt 和 JSON Web 令牌！

```
yarn add bcrypt jsonwebtoken 
```

现在你准备好去抓 started✨了

#### 2。创造我们智威汤逊的秘密🗝️

我们可以建立我们的 JWT 秘密——在我们的`config.ts`文件中，添加了以下内容:

```
export default {  
  ...  
  jwt: {  
    JWT_SECRET: 'super-secret',  
  },  
} 
```

#### 3。创建有效负载🚚

为了正确地将令牌和用户信息返回给请求者，我们需要设置一个有效负载。

```
export const UserLoginPayload = objectType({  
  name: 'UserLoginPayload',  
  definition: t =&gt; {  
    t.field('user', {  
      type: 'User',  
    })  
    t.string('token')  
  },  
}) 
```

我们在这里做的是创建一个名为`userLoginPayload`的对象类型。我们将该类型定义为能够返回我们的`User`字段，以及用户注册或登录时生成的令牌。

#### 4。设置登录和注册突变🚪🚶

为了设置用户注册和登录，我们创建了两个新的变异字段，`userLogin`和`userRegister`。我们可以将返回类型设置为`UserLoginPayload`来返回`User`和`token`，我们的参数是从前端的一个表单中收集的用户名和密码。以下是 GraphQL Nexus 中的突变:

```
export const userLogin = mutationField('userLogin', {  
  type: UserLoginPayload,  
  args: {  
    username: stringArg({ required: true }),  
    password: stringArg({ required: true }),  
  },  
})

export const userRegister = mutationField('userRegister', {  
  type: UserLoginPayload,  
  args: {  
    username: stringArg({ required: true }),  
    password: stringArg({ required: true }),  
  },  
}) 
```

在这之后，一个解析器被添加到突变中。

```
export const userLogin = mutationField('userLogin', {  
  type: UserLoginPayload,  
  args: {  
    username: stringArg({ required: true }),  
    password: stringArg({ required: true }),  
  },  
  resolve: async (root, args, context, info) =&gt; {  
    try {  
      const { password, ...user } = await context.prisma.user({  
        where: {  
          userName: args.username,  
        },  
      })  
      var validpass = await bcrypt.compareSync(args.password, password)  
      if (validpass) {  
        const token = jwt.sign(user, config.jwt.JWT_SECRET)  
        return {  
          user: user,  
          token,  
        }  
      }  
      return null  
    } catch (e) {  
      console.log(e)  
    }  
  },  
}) 
```

我们已经添加了解析器。这可能有点让人不知所措，所以让我们把它分成几部分。

```
const { password, ...user } = await context.prisma.user({  
        where: {  
          userName: args.username,  
        },  
      }) 
```

在这里，我们试图获得`User`数据。`await context.prisma.users({where: {userName: args.username}`从数据库中获取我们的`User`信息，并将信息存储在`password, ...user`中。我们已经分离了密码，因此它不会包含在我们的用户变量或 JSON Web 令牌数据中，如下一步所示。

```
var validpass = await bcrypt.compareSync(args.password, password)  
      if (validpass) {  
        const token = jwt.sign(user, config.jwt.JWT_SECRET)  
        return {  
          user: user,  
          token,  
        }  
      }  
      return null 
```

我们使用 Bcrypt 进行比较，看我们的密码值是否相等。如果密码匹配，使用配置文件中的 JWT 秘密和`user`生成一个 JWT。(如果我们没有事先分离密码数据，它将与用户数据一起返回并存储在 JWT 中😱！)虽然最后，我们现在返回我们的有效载荷(与 JWT 一起的`user`数据)！

注册的过程相对类似。

```
export const userRegister = mutationField('userRegister', {  
  type: UserLoginPayload,  
  args: {  
    username: stringArg({ required: true }),  
    password: stringArg({ required: true }),  
  },  
  resolve: async (root, args, context) =&gt; {  
    try {  
      const existingUser = await context.prisma.user({  
        where: {  
          userName: args.username,  
        },  
      })  
      if (existingUser) {  
        throw new Error('ERROR: Username already used.')  
      }  
      var hash = bcrypt.hashSync(args.password, 10)

      const { password, ...register } = await context.prisma.createUser({  
        userName: args.username,  
        password: hash,  
      })  
      const token = jwt.sign(register, config.jwt.JWT_SECRET)  
      return {  
        user: register,  
        token: token,  
      }  
    } catch (e) {  
      console.log(e)  
      return null  
    }  
  },  
}) 
```

让我们再来一次。

```
const existingUser = await context.prisma.user({  
        where: {  
          userName: args.username,  
        },  
      })  
      if (existingUser) {  
        throw new Error('ERROR: Username already used.')  
      } 
```

之前，我们查询用户名是否存在。这相对来说是一样的，只是现在如果有东西返回，我们会抛出一个错误，因为每个用户名都应该是唯一的。

```
var hash = bcrypt.hashSync(args.password, 10)

      const { password, ...register } = await context.prisma.createUser({  
        userName: args.username,  
        password: hash,  
      }) 
```

我们使用 bcrypt 散列传递给表单的密码，传递密码和我们想要生成的 salt 长度。之后，`createUser`突变用我们的用户名和新散列的密码创建了一个新用户。

```
const token = jwt.sign(register, config.jwt.JWT_SECRET)  
      return {  
        user: register,  
        token: token,  
      } 
```

有效负载的生成和返回方式与用户登录相同。

#### 5。将用户添加到上下文🧮

我们的用户现在可以登录并注册了！现在，我们可以创建一个查询和查看器字段来将信息返回到前端。

让我们从将当前用户添加到上下文开始。

```
export interface Context {  
  prisma: Prisma  
  currentUser: User  
}

export default async ({ req }) =&gt; {  
  const currentUser = await getUser(  
    req.get('Authorization'),  
    config.jwt,  
    prisma,  
  )  
  return {  
    prisma,  
    currentUser  
  }  
} 
```

这里，我们添加了从我们的`Context`导出的`User`类型的变量`currentUser`。我们可以使用一个`getUser`函数(我们将在下一步讨论如何创建这个函数——总之，它返回我们的`User`类型)通过传入我们的带有`req.get('Authorization')`的令牌(它从我们的头中获取我们的令牌)、我们的 JWT 秘密和 Prisma 客户端来返回我们的用户信息。

#### 6。创建 getUser 函数👶

因为我们希望在应用程序中查询用户信息，所以我们需要从消息头中获取用户的令牌。

```
export default async (authorization, secrets, prisma: Prisma) =&gt; {  
  const bearerLength = 'Bearer '.length  
  if (authorization && authorization.length &gt; bearerLength) {  
    const token = authorization.slice(bearerLength)  
    const { ok, result } = await new Promise(resolve =&gt;  
      jwt.verify(token, secrets.JWT_SECRET, (err, result) =&gt; {  
        if (err) {  
          resolve({  
            ok: false,  
            result: err,  
          })  
        } else {  
          resolve({  
            ok: true,  
            result,  
          })  
        }  
      }),  
    )  
    if (ok) {  
      const user = await prisma.user({  
        id: result.id,  
      })  
      return user  
    } else {  
      console.error(result)  
      return null  
    }  
  }  
  return null  
} 
```

让我们一步一步来。

```
const bearerLength = 'Bearer '.length  
  if (authorization && authorization.length &gt; bearerLength) {  
    const token = authorization.slice(bearerLength)  
    ...  
  }  
  return null  
} 
```

这里我们有一些基本的错误检查来查看令牌是否比我们的`Bearer`字符串长——如果是，我们可以通过切掉`Bearer`字符串来提取令牌。

```
const { ok, result } = await new Promise(resolve =&gt;  
      jwt.verify(token, secrets.JWT_SECRET, (err, result) =&gt; {  
        if (err) {  
          resolve({  
            ok: false,  
            result: err,  
          })  
        } else {  
          resolve({  
            ok: true,  
            result,  
          })  
        }  
      })  
    ) 
```

现在我们用我们的秘密来验证令牌，并用传入的令牌以及来自我们的 JWT 的`result`(这是我们的`user`类型)来解析我们的承诺。

```
if (ok) {  
      const user = await prisma.user({  
        id: result.id,  
      })  
      return user  
    } else {  
      console.error(result)  
      return null  
    }  
  } 
```

最后，如果令牌有效，我们用从令牌获得的 id 查询用户，并返回它！

#### 7。创建用户查询和查看器字段🔬

我们可以创建一个查看器字段和用户查询，以便能够在我们的应用程序中查询当前登录的用户信息。

```
t.string('getCurrentUser', {  
  resolve: async (root, args, context, info) =&gt; {  
    return context.prisma.user  
  },  
}) 
```

我们可以创建一个新的查询，`getCurrentUser`——这将返回我们在`Context`函数中得到的值，这样我们现在就可以方便地查询当前登录的用户了！

最后，我们应该在查询中添加一个`viewer`字段。

```
t.field('viewer', {  
      type: 'User',  
      nullable: true,  
      resolve: (root, args, context) =&gt; {  
        return context.currentUser  
      },  
    }) 
```

这只是返回我们添加到上下文中的`currentUser`。

#### 前端

#### 1。登录和注册💎

既然我们的后端已经完成，我们可以使用我们在后端创建的解析器实现一个简单的前端解决方案。

```
const SIGNUP_MUTATION = gql`  
  mutation UserRegister($username: String!, $password: String!) {  
    userRegister(username: $username, password: $password) {  
      user {  
        id  
        userName  
      }  
      token  
    }  
  }  
`; 
```

这里有一个简单的注册变异，在提交表单时创建一个新用户。我们使用我们在后端创建的`userRegister`函数，简单地传递用户名和密码，同时返回任何想要的信息。

```
&lt;Mutation  
    mutation={SIGNUP_MUTATION}  
    onCompleted={data =&gt; _confirm(data)}  
  &gt;  
...  
&lt;/Mutation&gt; 
```

接下来，我们可以将注册突变添加到由`react-apollo`提供的`Mutation`组件中。当变异完成后，我们称这个函数为`_confirm`。

```
_confirm = async data =&gt; {  
  const { token } = data.userLogin;  
  this._saveUserData(token);  
};

_saveUserData = async token =&gt; {  
  try {  
    await AsyncStorage.setItem(AUTH_TOKEN, token);  
  } catch (e) {  
    console.log("ERROR: ", e);  
  }  
}; 
```

我们的`_confirm`函数所做的是获取我们从突变中返回的`data`，并从中提取令牌，将其传递给`_saveUserData`。这个函数将`token`存储在`AsyncStorage`中(如果您没有使用 Native 开发，令牌将存储在`LocalStorage`)。

警告:顺便提一下，使用 localStorage 来存储我们的 JWT 并不是生产中的最佳实践——你可以在这里阅读更多相关内容。

登录的过程非常相似，我们只需用我们的`LOGIN_MUTATION`替换掉我们的`SIGNUP_MUTATION`。

#### 2。将令牌插入报头💯

```
const authLink = setContext(async (_, { headers }) =&gt; {  
  const token = await AsyncStorage.getItem(AUTH_TOKEN);  
  return {  
    headers: {  
      ...headers,  
      authorization: token ? `Bearer ${token}` : ""  
    }  
  };  
}); 
```

我们使用`apollo-link-context`的`setContext`函数来设置应用程序的标题。我们从`AsyncStorage`获取授权令牌，然后将它存储在我们的头中。

#### 3。查询用户信息🙆

由于我们所有的辛勤工作，我们可以在应用程序中的任何地方查询用户的信息——是的，就是这么简单！

```
const GET_USER = gql`  
  query getUser {  
    viewer {  
      id  
    }  
  }  
`; 
```

#### 结论

至此，您的身份验证设置完毕！我们现在已经创建了解析器来返回所需的有效负载，并且可以在应用程序的任何地方查询当前登录的用户。本教程的灵感来自斯潘塞·卡莉的伟大教程，[graph QL authentic ation with React Native&Apollo](https://medium.com/handlebar-labs/graphql-authentication-with-react-native-apollo-part-1-2-9613aacd80b3)——如果你想更深入地了解我们在本教程中讨论的内容，可以看一看。如果您有任何问题或建议，请随时发表评论，通过 [Twitter](http://twitter.com/novvumio) 联系我们，或者通过[我们的网站](http://novvum.io)联系我们。谢谢大家！
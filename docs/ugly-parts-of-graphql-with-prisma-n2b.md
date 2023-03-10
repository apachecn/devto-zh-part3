# GraphQL 与 Prisma 的丑陋部分

> 原文：<https://dev.to/fcpauldiaz/ugly-parts-of-graphql-with-prisma-n2b>

# 堆栈

首先，这是一个我认为 graphql 结合 [Prisma](https://prisma.io) 和 [graphql-yoga](https://github.com/prisma/graphql-yoga) 的缺点的帖子。

## 第一

对于这种模型

```
type Company @db(name: "companies") {
  id: ID! @id
  active: Boolean! @default(value: true)
  name: String!
  createdBy: User! @relation(link: INLINE)
  createdAt: DateTime! @createdAt
  updatedAt: DateTime! @updatedAt
} 
```

这是变异是如何完成的。

```
async createCompany(parent, args, ctx, info) {
    // 1\. Check if they are logged in
    if (!ctx.request.userId) {
      throw new Error('You must be logged in!');
    }
    // 2\. create
    const company = await ctx.db.mutation.createCompany(
      {
        data: {
          ...args,
          active: true,
        }
      },
      info
    );
    return company;
  } 
```

现在，为了添加 createdBy 属性，应该将它添加到数据中

```
 {
        data: {
          ...args,
          active: true,
          // notice the connect object
          createdBy: { connect: { id: ctx.request.userId } }
        }
 } 
```

在 connect 对象中添加 userId 感觉有点臃肿，不过没什么大不了的，还好。这将只在公司的文档中添加 id。

现在，如果我们想将用户作为整个子文档添加呢？

那么现有的公司模型不能被扩展，用户的当前模型应该具有@embedded 配置。

```
type User @embedded {
  name: String!
  email: String! @unique
  password: String!
  resetToken: String
  resetTokenExpiry: Float
} 
```

这就是事情变得复杂的时候

现在我们想使用创建公司突变

```
 {
        data: {
          ...args,
          active: true,
          user: {
              create: {
                 // here goes all the user attributes that are required
              }
          },
          createdBy: { connect: { id: ctx.request.userId } }
        }
      }, 
```

这一次用户对象应该包装在创建对象周围，猜猜如果用户有另一个子文档会发生什么？

```
 {
        data: {
          ...args,
          active: true,
          user: {
              create: {
                 // what if the user has a permissions subdocument ?
                 permissions: {
                     create: {
                          name: "ADMIN"
                     }
                 }
              }
          },
          createdBy: { connect: { id: ctx.request.userId } }
        }
      }, 
```

每次添加子文档时，它都必须被一个 create 对象所包围！那真的很烦。对于复杂的模型，这将会用大量的 create 对象来增加文档的创建。

而这里又是另外一件事，对于每个 create 对象，在数据库中保存的时候，即使模型中没有指定，也会为每一级创建一个 _id 属性，所以这是没有必要的。

## 第二

重用定义的第一个突变

```
async createCompany(parent, args, ctx, info) {
    // 1\. Check if they are logged in
    if (!ctx.request.userId) {
      throw new Error('You must be logged in!');
    }
    // 2\. create
    const company = await ctx.db.mutation.createCompany(
      {
        data: {
          ...args,
          active: true,
        }
      },
      info
    );
    return company;
  } 
```

当使用 Apollo 客户端从前端调用时

这就是这种变异的叫法

```
 // create new company
 const promise = mutate({
       mutation: COMPANY_MUTATION,
      variables: { name: inputValue.value }
 });

promise.then((item) => {
  // to access the data returned from the server.
  const name = item.data.createCompany.name;
}); 
```

因此，响应中填充了一个名为 data 的对象和另一个名为服务器中定义的方法的对象。这也让人感觉臃肿，因为现在在访问前端数据时，您必须知道后端的方法名。

# 结束

这份名单很短，但我真的不喜欢他们。你怎么想呢?
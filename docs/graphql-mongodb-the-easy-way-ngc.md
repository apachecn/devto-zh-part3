# GraphQL + Mongodb。简单的方法。

> 原文：<https://dev.to/alvarojsnish/graphql-mongodb-the-easy-way-ngc>

编辑:看到这篇文章的反响，我会更新一个，使用 Prisma，新的 GraphQL 特性，将可用于任何你想使用的数据库。谢谢，Á·阿尔瓦罗。
到了！新版本:[https://dev . to/alvarojsnish/graph QL-mongo-v2-the-easy-way-6cb](https://dev.to/alvarojsnish/graphql-mongo-v2-the-easy-way-6cb)

大家好！我的名字是阿尔瓦罗，这是我在这里的第一个职位。我一直在 medium 等其他网站写作。

但是！现在我在这里，我希望能在这里呆一段时间。

最近几个月我一直在玩 GraphQL，真的，我喜欢它。

今天，我们将学习:

1.  如何设置 GraphQL 服务器
2.  如何查询这个 API
3.  连接到 mongo

在应用程序中，我们将有经过验证的用户，只有这样他们才会创建帖子。

开始吧！

# 1。用巴别塔设置节点

```
mkdir graphql-test && cd graphql-test
yarn init -y
yarn add --dev nodemon @babel/core @babel/node @babel/preset-env 
```

Enter fullscreen mode Exit fullscreen mode

我用的是纱线，你可以用 npm。
在根目录下创建一个. babelrc 文件，然后粘贴这个配置:

```
{
  "presets": ["@babel/preset-env"]
} 
```

Enter fullscreen mode Exit fullscreen mode

# 2。创建我们的文件和目录组织

1.  在根目录中，创建文件夹 src
2.  src 内部:模型、模式和解析器
3.  现在，在 src 中，创建 index.js
4.  安装我们将使用的所有软件包:

```
yarn add mongoose jsonwebtoken bcrypt express graphql cors apollo-server apollo-server-express 
```

Enter fullscreen mode Exit fullscreen mode

1.  在 package.json 中创建一个脚本来启动服务器:

```
{  "name":  "graphql-test",  "version":  "1.0.0",  "main":  "index.js",  "license":  "MIT",  "scripts":  {  "dev":  "nodemon --exec babel-node src/index.js"  },  "devDependencies":  {  "@babel/core":  "^7.4.5",  "@babel/node":  "^7.4.5",  "@babel/preset-env":  "^7.4.5",  "apollo-server":  "^2.6.1",  "apollo-server-express":  "^2.6.1",  "bcrypt":  "^3.0.6",  "cors":  "^2.8.5",  "express":  "^4.17.1",  "graphql":  "^14.3.1",  "jsonwebtoken":  "^8.5.1",  "mongoose":  "^5.5.12",  "nodemon":  "^1.19.1"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在 index.js 中是一切开始的地方。

# 3。创建 mongo 模型

因为我们想专注于 GraphQL，所以让我们稍微加快一下 mongo 的速度:

在模型内部，创建 userModel 和 postModel:

邮件模型. js

```
import mongoose from 'mongoose';

const postSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
  },
  content: {
    type: String,
    required: true,
  },
  author: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'user',
  },
});

const post = mongoose.model('post', postSchema);

export default post; 
```

Enter fullscreen mode Exit fullscreen mode

userModel.js

```
import bcrypt from 'bcrypt';
import mongoose from 'mongoose';

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
    unique: true,
  },
  password: {
    type: String,
    required: true,
  },
  posts: [
    {
      type: mongoose.Schema.Types.ObjectId,
      ref: 'post',
    },
  ],
});

userSchema.pre('save', function() {
  const hashedPassword = bcrypt.hashSync(this.password, 12);
  this.password = hashedPassword;
});

const user = mongoose.model('user', userSchema);

export default user; 
```

Enter fullscreen mode Exit fullscreen mode

# 4。创建我们的模式

在/src/schemas 中，我们将创建 postSchema.js 和 userSchema.js

```
import { gql } from 'apollo-server';

export default gql`
  type Post {
    id: ID!
    title: String!
    content: String!
    author: User!
  }

  extend type Query {
    post(id: ID!): Post!
    posts: [Post!]!
  }

  extend type Mutation {
    createPost(title: String!, content: String!): Post!
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

```
import { gql } from 'apollo-server';

export default gql`
  type User {
    id: ID!
    name: String!
    posts: [Post!]!
  }

  type Token {
    token: String!
  }

  extend type Query {
    user(id: ID!): User!
    login(name: String!, password: String!): Token!
  }

  extend type Mutation {
    createUser(name: String!, password: String!): User!
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

1.  我们使用 *extend* 符号，因为我们将创建一个 linkSchema，以使用我们一起添加的每个模式。我们只能有一种查询类型，所以扩展它我们可以两种都用，也适用于突变和订阅。
2.  在用户中我们不添加密码(安全原因)，所以客户端不能查询它。

这是我们的链接模式:

```
import userSchema from './user';
import postSchema from './post';
import { gql } from 'apollo-server';

const linkSchema = gql`
  type Query {
    _: Boolean
  }
  type Mutation {
    _: Boolean
  }
`;

export default [linkSchema, userSchema, postSchema]; 
```

Enter fullscreen mode Exit fullscreen mode

我在 schemas/index.js 中创建了它，它是我们稍后将在索引中导入的模式。

# 5。创建我们的解决方案

与模式相同，我们在 src/resolvers
中创建了 postResolvers.js 和 userResolvers.js

```
import { AuthenticationError } from 'apollo-server';

export default {
  Query: {
    post: async (parent, { id }, { models: { postModel }, me }, info) => {
      if (!me) {
        throw new AuthenticationError('You are not authenticated');
      }
      const post = await postModel.findById({ _id: id }).exec();
      return post;
    },
    posts: async (parent, args, { models: { postModel }, me }, info) => {
      if (!me) {
        throw new AuthenticationError('You are not authenticated');
      }
      const posts = await postModel.find({ author: me.id }).exec();
      return posts;
    },
  },
  Mutation: {
    createPost: async (parent, { title, content }, { models: { postModel }, me }, info) => {
      if (!me) {
        throw new AuthenticationError('You are not authenticated');
      }
      const post = await postModel.create({ title, content, author: me.id });
      return post;
    },
  },
  Post: {
    author: async ({ author }, args, { models: { userModel } }, info) => {
      const user = await userModel.findById({ _id: author }).exec();
      return user;
    },
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
import bcrypt from 'bcrypt';
import jwt from 'jsonwebtoken';
import { AuthenticationError } from 'apollo-server';

export default {
  Query: {
    user: async (parent, { id }, { models: { userModel }, me }, info) => {
      if (!me) {
        throw new AuthenticationError('You are not authenticated');
      }
      const user = await userModel.findById({ _id: id }).exec();
      return user;
    },
    login: async (parent, { name, password }, { models: { userModel } }, info) => {
      const user = await userModel.findOne({ name }).exec();

      if (!user) {
        throw new AuthenticationError('Invalid credentials');
      }

      const matchPasswords = bcrypt.compareSync(password, user.password);

      if (!matchPasswords) {
        throw new AuthenticationError('Invalid credentials');
      }

      const token = jwt.sign({ id: user.id }, 'riddlemethis', { expiresIn: 24 * 10 * 50 });

      return {
        token,
      };
    },
  },
  Mutation: {
    createUser: async (parent, { name, password }, { models: { userModel } }, info) => {
      const user = await userModel.create({ name, password });
      return user;
    },
  },
  User: {
    posts: async ({ id }, args, { models: { postModel } }, info) => {
      const posts = await postModel.find({ author: id }).exec();
      return posts;
    },
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

1.  Query 将在类型查询中解析我们在模式中创建的所有“函数”。
2.  突变，将解析我们在模式中创建的所有“函数”，在类型突变中。
3.  每次我们查询 API 寻找用户或帖子时，User / Post 将解析具体的字段或类型。这意味着每当我们查询一个用户>文章时，服务器将首先遍历查询>用户，然后，将遍历用户>文章(文章是字段的名称)。我们需要这样做，因为我们将数据存储在不同的集合中。

### 如我们所见，解析器是一个函数，它有 4 个参数(parent、args、context 和 info)。

parent:将从父解析器返回数据。例如:我们通过查询>用户>文章。Posts 会将所有数据作为父参数返回给用户。

args:将有我们在查询/变异中使用的参数。如果我们看到我们的模式，post(id: ID！):贴！将有 1 个参数，id。

context:context 是一个对象，它将包含我们在服务器配置中传递给它的所有内容，在我们的例子中，它有用于用户和 post 的 de mongo 模型，以及“me”，即登录的当前用户。

info:这个比较复杂，Prisma 在这里深入介绍:[https://www . Prisma . io/blog/graph QL-server-basics-demystifying-the-info-argument-in-graph QL-resolvers-6f 26249 f 613 a](https://www.prisma.io/blog/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a)

正如我们对模式所做的，在 src/resolvers:
中创建一个 index.js

```
import postResolver from './postResolver';
import userResolver from './userResolver';

export default [userResolver, postResolver]; 
```

Enter fullscreen mode Exit fullscreen mode

# 6 设置好一切

最后，在我们的 index.js 中的 src/文件夹:

```
import cors from 'cors';
import express from 'express';
import jwt from 'jsonwebtoken';
import mongoose from 'mongoose';
import { ApolloServer, AuthenticationError } from 'apollo-server-express';

import schemas from './schemas';
import resolvers from './resolvers';

import userModel from './models/userModel';
import postModel from './models/postModel';

const app = express();
app.use(cors());

const getUser = async (req) => {
  const token = req.headers['token'];

  if (token) {
    try {
      return await jwt.verify(token, 'riddlemethis');
    } catch (e) {
      throw new AuthenticationError('Your session expired. Sign in again.');
    }
  }
};

const server = new ApolloServer({
  typeDefs: schemas,
  resolvers,
  context: async ({ req }) => {
    if (req) {
      const me = await getUser(req);

      return {
        me,
        models: {
          userModel,
          postModel,
        },
      };
    }
  },
});

server.applyMiddleware({ app, path: '/graphql' });

app.listen(5000, () => {
  mongoose.connect('mongodb://localhost:27017/graphql');
}); 
```

Enter fullscreen mode Exit fullscreen mode

1.  通过函数 getUser，我们传递令牌并对其进行验证，如果令牌无效，那么“me”对象将为空，那么客户端将无法执行请求。
2.  当我们创建 ApolloServer 时，我们将模式作为 typeDefs 传递，将解析器作为解析器传递，并且上下文将是一个异步函数，它将解析我们之前创建的函数。无论是用户还是 null，上下文都有我们创建的 mongo 模型，所以我们可以在解析器中操作数据库。
3.  我们将 express server middelware 添加到我们的应用程序中，并将 API 端点设置为/graphql。
4.  我们将应用程序的端口设置为 5000，然后连接到数据库。我们的数据库将被命名为 graphql。

# 7。测试我们的新生儿。

1.  运行“纱线开发”或“npm 运行开发”。
2.  转到[http://localhost:5000/graph QL](http://localhost:5000/graphql)
3.  做一些查询和突变！

创建用户
[![](img/7e781584129fc212ac7252056035e211.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8mtT5rJr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gqvttc3vwnw8zk38chp2.JPG)

登录用户
[![](img/8aaac5259351fd1d2c7622a2288066e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yhshEHe7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/69z1g1g1vqyc2ggkym30.JPG)

在标题中设置令牌
[![](img/a08849e190b4e40fac86e2e87d2d6b7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bidywou9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5f9c69as4ngbge0bcdar.JPG)

创建帖子
[![](img/1df98cf32384f8dcc37261790b5cfeea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7NSOtyM---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mrsjf4i7z0uvomn5ia5o.JPG)

查询帖子
[![](img/fb8aed1c6f8cf5e8a9f747f2ee615d54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SC5CZNn8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/urll8g57k23kgypkqiyw.JPG)

### 我希望你和我一样喜欢这个！你可以随时联系我！如果你想要更好的解释，尽管开口，我很乐意这样做！
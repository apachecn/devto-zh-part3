# 演示如何将 GraphQL 与 Node 和 Vue 一起使用

> 原文：<https://dev.to/bnevilleoneill/demonstration-on-how-to-use-graphql-with-node-and-vue-in-bits-3im2>

[![](img/33f9fe48e7dc3cf02d63b47516e0aacb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I61x1Ftg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/blog-images-backup/1%2AuUXiDk5CNIwCgql8dh3pqQ.jpeg)T3】

当我在网上冲浪时，我看到了这条微博。前端开发人员似乎厌倦了时不时地请求 API。😄

> ![Ryan Florence profile image](img/12147af33d64e864028a13dd24d7f3c6.png)瑞安佛罗伦萨[@ ryanflorence](https://dev.to/ryanflorence)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)graph QL 没有像火箭一样起飞的唯一原因是后端开发人员不理解他们给前端代码带来的混乱，认为前端是一种较低级的编程形式。2018 年 11 月 08 日下午 15:39[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1060557386421723136)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1060557386421723136)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1060557386421723136)

第一次和 GraphQL 互动，就爱上了它。为什么？GraphQL 为您提供了轻松构建 API 的空间，让您不必重复做同样的事情。怎么会？让我们找出答案。

我假设您对以下内容有基本的了解:

*   java 描述语言
*   节点. js
*   view . js-检视. js

我们来看看 GraphQL 和 REST 的对比。

为了全面了解本文，我想让你克隆这个项目的[库](https://github.com/meshboy/recipe)，它包含服务器端和客户端。

在本文中，我们将关注以下几点:

*   为什么是 GraphQL
*   GraphQL 的关键组件
*   证明
*   文件上传
*   带 Vue 的 GraphQL

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

#### 什么是 GraphQL？

GraphQL 是一种查询语言，它有助于将打开 API 大门的钥匙交给访问者(客户)，当然是在您允许的情况下。嗯，如果管理不当，你的访客可能会把事情搞砸。

无论如何，您的应用程序都必须与其他应用程序进行交互。这只能通过公开部分或全部 API 来实现。实现这一点的方法之一是使用 REST 构建您的应用程序。到本文结束时，我们都会明白为什么应该在下一个应用程序中使用 GraphQL。

> GraphQL 是一种用于 API 的查询语言，也是一种用现有数据完成这些查询的运行时语言。GraphQL 为 API 中的数据提供了完整且易于理解的描述，使客户能够准确地要求他们需要的东西，使 API 更容易随时间发展，并支持强大的开发工具。

*REST Vs GraphQL*

*   GraphQL 只有一条路由，而 REST 有多条路由。随着客户端需求的增加，REST 端点也在不断增加。
*   除了 REST 有多个端点之外，它还附带了 HTTP 动词(POST、PUT、GET 等),但是 GraphQL 也涵盖了我们，因为它不需要这些东西。
*   每个 API 都应该附带文档，但是 GraphQL 给了我们一个现成的交互式 API 文档。
*   在 GraphQL 中，您可以轻松地尽可能地解析数据，但在 REST 中却不行。它肯定会有一个新的终点。
*   GraphQL 不会让您担心如何对 API 进行版本控制，因为单一路径不会改变，但是您需要关心的是，当更新不断出现时，您应该如何在使用 REST 的同时对 API 进行版本控制。

说教到此为止！

让我们来看一个典型的 GraphQL 文档。随意复制并粘贴[片段](https://www.apollographql.com/docs/apollo-server/getting-started.html)，在您的机器上感受一下。

[![](img/866a7088f044bb531fc9e75867256f48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--roSD4tCu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fzktdqdyy8jfkdp0rlt0.gif)

在这个项目的过程中，我们将和 Apollo 一起探索 GraphQL。Apollo 同时支持 NodeJs 和 VueJs。😃

让我们来看看我们项目的演示是什么样子的，这应该会给你一个未来的想法。也可以设置一下。

[![](img/12322d94c247cabc4393a7258c7d3737.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rY_AgSqB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ocziuc4ir2mhgt8y44uc.gif)

#### graph QL 的关键组件

我们将重点关注四个主要部分:

*   类型
*   问题
*   变化
*   下决心者

**1。类型:**在数据库级别上，我们为每个模型都提供了一个模式。在这个项目中，我们只有两个模型(配方和用户)。

```
const recipeSchema = new mongoose.Schema({
    name: {
        type: String,
    },
    description: {
        type: String,
    },
    difficultyLevel: {
        type: Number,
    },
    fileUrl: {
        type: String
    },
    steps: [{
        type: String
    }],
    averageTimeForCompletion: {
        type: Number,
    },
userId: {
        type: mongoose.Schema.Types.ObjectId,
        ref: 'User'
    }
}, { timestamps: true });
export const Recipe = mongoose.model('Recipe', recipeSchema); 
```

Enter fullscreen mode Exit fullscreen mode

*数据库级模式*

我们还有一个 API 级别的模式，它将符合上面的数据库模式。这个模式也像数据库模式上的验证器一样。具有 GraphQL 无法识别的类型的查询将被退回。

```
type Recipe {
  id: ID!
  name: String!
  description: String!
  difficultyLevel: Int!
  image: String!
  steps: [String]!
  averageTimeForCompletion: Int!
  user: User!
} 
```

Enter fullscreen mode Exit fullscreen mode

*API 级模式*

在编写模式时，GraphQL 支持不同的对象类型:Int、Float、String、Boolean、ID，就像 MongoDB 中的 _id 一样。它还支持 List，以防您的数据库模式有一个对象列表和更多的对象。

！sign 确保对象不返回空值。当你再次确定时，总是使用它，一个值将被返回。如果它似乎已被使用并且值为 null，将会引发一个错误。在上面的代码片段中，我们不希望 id 类型的 ID 为 null。

我们也可以拥有自己的自定义类型，就像我们拥有文件类型一样。

```
type File {
    id: ID!
    url: String!
} 
```

Enter fullscreen mode Exit fullscreen mode

**2。查询:**在 REST 中，获取数据的标准方式是使用 GET。同样的概念也适用于 GraphQL 查询。我们在需要获取数据的地方使用查询。查询在 GraphQL 中只能定义一次。

为了更好地理解，每个 GraphQL 组件在项目中都有自己的文件。在 recipe 项目中，关键字 Query 被多次使用。

让我们看看如何编写查询来获取会话中用户的属性。

```
type User {
    id: ID!
    email: String!
    name: String!
    token: String
    createdAt: String!
    updatedAt: String!
}
type Query {
    getMe: User!
} 
```

Enter fullscreen mode Exit fullscreen mode

*查询用户*

因为我们已经在用户资源中声明了查询，所以我们还必须在配方资源中声明查询。你应该知道它会抛出一个错误。好吧，我们都准备好了。对于多次使用的关键字查询，需要使用关键字 extend 对其进行扩展。

为了获取用户食谱，我们需要一个与该食谱相关联的用户 ID。GraphQL 查询也可以接受参数。

```
extend type Query {
    Recipe(id: ID!): Recipe!
    Recipes: [Recipe]!
} 
```

Enter fullscreen mode Exit fullscreen mode

*配方*

上面有两个不同的查询。第一个函数使用 id 类型的参数 ID 获取一个配方，该参数 ID 不能为 null，而后者返回系统中的所有配方。

**3。突变:**一个 API 不仅仅是获取，在某些时候，数据必须被存储、更新或删除以保持我们平台的活力。要执行这个操作(POST、PUT、DELETE 等)，最好在 mutation 下声明它。

```
type Mutation {
    createUser(email: String!, name: String!, password: String)
} 
```

Enter fullscreen mode Exit fullscreen mode

*创建一个用户(突变)*

createUser 变异本可以写得更好，但我更喜欢使用变量声明风格。是的，你也可以为你的突变声明一个变量。

如果您需要客户机在发送响应之前提供输入，变量将会派上用场。

```
type Mutation {
  createUser(input: NewUser!): User!
  loginUser(input: LoginUser!): User!
} 
```

Enter fullscreen mode Exit fullscreen mode

*用户登录和注册*

变量可以有任何形式。只要通过创建或声明您的变量类型来通知 GraphQL，就不会有警报。😉

在上面的变异中，我们有 loginUser 和 createUser，它们接受必须返回 User 类型的 NewUser 类型的输入和也返回 User 的 LoginUser 类型的输入。我们已经在上面看到了用户的样子。

> 输入是变量的一种形式，为具有声明类型的参数提供服务。

```
input LoginUser {
  email: String!
  password: String!
}
input NewUser {
  email: String!
  name: String!
  password: String!
} 
```

Enter fullscreen mode Exit fullscreen mode

登录和注册类型

```
type Recipe {
  id: ID!
  name: String!
  description: String!
  difficultyLevel: Int!
  image: String
  steps: [String]!
  averageTimeForCompletion: Int!
  user: User!
}
input NewRecipe {
  name: String!
  description: String!
  difficultyLevel: Int!
  image: Upload
  steps: [String]! 
  averageTimeForCompletion: Int!
}
input UpdateRecipe {
  id: ID!
  name: String
  description: String
  difficultyLevel: Int!
  image: String
  steps: [String]
  averageTimeForCompletion: Int!
}
input DeleteRecipe {
    id: ID!
}
# query is use when you want to get anything from 
#the server just like (GET) using REST
extend type Query {
    Recipe(id: ID!): Recipe!
    Recipes: [Recipe]!
}
# performing actions (that requires DELETE, PUT, POST) 
#just as in REST requires a mutation 
extend type Mutation {
    deleteRecipe(input: DeleteRecipe!): Recipe!
    updateRecipe(input: UpdateRecipe!): Recipe!
    newRecipe(input: NewRecipe!): Recipe!
} 
```

Enter fullscreen mode Exit fullscreen mode

*配方模式*

在上面的代码片段中，用户可以决定删除一个配方，更新一个配方，并根据我们在 Mutation 中的声明创建一个新配方。

有没有注意到一个没有声明但是使用了 Upload 的类型？但是 GraphQL 似乎对此并不介意。我们将在文件部分讨论这个问题。

**4。** **解析器:**既然我们已经声明了类型、突变和查询。这与客户对我们数据库的要求有什么联系？

让我们来看看这个流程…

<figure>[![](img/54eac88e36fb40823e289ebe92026418.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kWO0eJoP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/741/1%2Aa6F8kPNU5PT6NPH8zEyq3g.png)

<figcaption>graph QL 如何处理数据的概述</figcaption>

</figure>

所以在 GraphQL 中，我们有标量类型(字符串、整数、布尔、浮点、枚举)。如果我们足够幸运地将我们的数据库对象与我们拥有的类型相匹配，GraphQL 将为我们解决这个问题。

让我们来处理我们在突变中声明的动作。记住我们在突变中声明的参数是输入。无论我们期望从客户那里得到什么，都可以在变量输入中找到。

```
import { User } from './user.model'; 
import { Recipe } from '../recipe/recipe.model'; 
import { signIn, verify } from '../../modules/auth';''
const loginUser = async(root, { input }) => {
    const { email, password } = input;
    const user = await User.findOne({ email }).exec();
    const errorMsg = 'Wrong credentials. Please try again';
    if(!user) {
        throw new Error (errorMsg);
    }
    if(user.comparePassword(password)) {
        user.token = signIn({ id: user._id, email: user.email });
        return user;
    }
    else{
        throw new Error (errorMsg);
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

*登录用户*

让我们花点时间来看看查询和突变的参数。以 loginUser 为例。

每个解析器中有四个主要参数(根值、参数、上下文和信息)。

*   rootValue:主要用于处理嵌套解析器(主分支的子分支)。

```
type User {
    id: ID!
    email: String!
    name: String!
    token: String
    recipes: [Recipe]!
    createdAt: String!
    updatedAt: String!
} 
```

Enter fullscreen mode Exit fullscreen mode

*用户类型*

看上面的类型，在我们的数据库模型中有一个我们没有声明的对象。这意味着 GraphQL 期望我们解决(处理它并赋值)。

配方将被视为嵌套解析器。分支上面的 rootValue 就是用户。这样做的好处是可以获取用户创建的食谱。

```
User: {
        recipes(user) {
           return Recipe.find({ userId: user.id }).exec()
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

*嵌套解析器*

*   arguments:它保存从客户端传递的参数。在我们定义的突变中，我们期望客户端传入一个输入
*   上下文:它包含需要在解析器之间共享的任何数据。在这个项目中，我们将经过验证的用户传递给上下文
*   信息:这是我们可能永远不会使用的唯一参数。这是原始的 GraphQL 查询

对于 loginUser，用户传递从参数对象析构的电子邮件和密码，并返回一个用户，就像我们在变异中声明的那样(User！)如果凭证是正确的，否则我们抛出一个错误。

我们在突变类型(`server/api/resources/user/user.graphql file`)中的声明也必须与传递给解析器的声明相同。

```
export const userResolvers = {
    Query: {
        getMe
    },
    Mutation: {
        createUser, //createUser: createUser
        loginUser //loginUser: loginUser
    },
    User: {
        recipes(user) {
           return Recipe.find({ userId: user.id }).exec()
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*用户解析器*

#### 认证

有几种方法可以验证我们的 GraphQL 应用程序，特别是当我们想要公开查询或变异操作时(loginUser 和 createUser)。

我们可以通过对公共路由使用 REST 和单独保护单一 GraphQL 路由来保护它。就个人而言，我总是选择这个选项，因为它为您的应用程序提供了灵活性。

在这个项目中，我们在解析器级别认证了 GraphQL。当用户成功登录时，使用 jwebtokens 生成的令牌被返回给客户端，并被传递给头部。

```
import { verify } from './api/modules/auth';
const setMiddleware = (app) => {
    app.use(async (req, res, next) => {
        try{
            const token = req.headers.authorization || '';
            const user = await verify(token);
            req.user = user;
        }
        catch(e) {
            console.log(e)
            req.user = null;
        }
        next();
    });
};
export default setMiddleware; 
```

Enter fullscreen mode Exit fullscreen mode

#### 中间件

中间件交叉检查每个请求中的报头，获取令牌，验证并在成功的情况下给出适当的有效载荷(用户)。中间件然后被传递到 Apollo 服务器。

因为我们使用 express，apollo-server-express 是我们最好的工具。

```
setMiddleware(app);
const path = '/recipe'
graphQLRouter.applyMiddleware({ app, path});
const secret = process.env.TOKEN_SECRET;
const expiresIn = process.env.EXPIRES_IN || '1 day';
export const signIn = payload => jsonwebtoken.sign(payload, secret, { expiresIn });
export const verify = token => {
    return new Promise((resolve, reject) => {
        jsonwebtoken.verify(token, secret, {}, (err, payload) => {
            if(err){
                return reject(err);
            }
            return resolve(payload);
        })
    })
}
export const throwErrorIfUserNotAuthenticated = user => 
        {if(!user) throw new Error('hey!. You are not authenticated')} 
```

Enter fullscreen mode Exit fullscreen mode

*认证*

这是关键，每个球员都走到了一起。GraphQL 标签(gql)使得编写和组合模式变得更加容易。

```
import { ApolloServer, gql } from 'apollo-server-express';
import merge from 'lodash.merge'
import { userType, userResolvers } from './resources/user';
import { recipeType, recipeResolvers } from './resources/recipe';
import { fileType } from './resources/file';
const typeDefs = gql`${userType}${recipeType}${fileType}`;
export const graphQLRouter = new ApolloServer(
    {
        typeDefs,
        resolvers: merge({}, userResolvers, recipeResolvers),
        context: ({req, res})=> ({ user: req.user })
    }
); 
```

Enter fullscreen mode Exit fullscreen mode

对于每个解析器，我们检查用户是否为空，一旦为空，客户端就被退回。系统告诉客户端对请求进行身份验证。

```
const getRecipe = (root, { id }, { user }) => {
    throwErrorIfUserNotAuthenticated(user); 
    return Recipe.findById(id).exec();
}; 
```

Enter fullscreen mode Exit fullscreen mode

检查用户是否有权访问此解析程序。

#### 文件上传

除了发送简单的数据，我们还会以某种方式上传文件。Apollo server 2.0 ~通过开箱即用处理文件，让生活变得更加轻松。GraphQL 识别上传标量。它帮助处理一个可变文件对象。它被添加到 NewRecipe 输入中，以处理请求附带的文件。

```
const createRecipe = async (root, { input }, { user }) => {
  throwErrorIfUserNotAuthenticated(user);
  //    bring out the image from input for file processing
  const { image, ...recipeObject } = await input;
  let url = "";
  if (image) {
    const result = await uploadFile(image);
    url = result.url;
  }
  const recipe = await Recipe.findOne({ name: input.name.toLowerCase() });
  if (recipe) {
    throw new Error("Recipe already exists!");
  }
  Object.assign(recipeObject, { image: url, userId: user.id });
  return Recipe.create(recipeObject);
}; 
```

Enter fullscreen mode Exit fullscreen mode

*创建新配方*

文件流正在被处理，完成后，URL 将与 rest 输入对象一起保存。

```
import cloudinary from "cloudinary";
import streamToBuffer from "stream-to-buffer";
cloudinary.config({
  cloud_name: process.env.CLOUDINARY_CLOUD_NAME,
  api_key: process.env.CLOUDINARY_API_KEY,
  api_secret: process.env.CLOUDINARY_API_SECRET
});
export const uploadFile = async file => {
  const { mimetype, stream } = await file;
  // process image
  return new Promise((resolve, reject) => {
    if (!Object.is(mimetype, "image/jpeg")) {
      throw new Error("File type not supported");
    }
    streamToBuffer(stream, (err, buffer) => {
      cloudinary.v2.uploader
        .upload_stream({ resource_type: "raw" }, (err, result) => {
          if (err) {
            throw new Error("File not uploaded!");
          }
          return resolve({ url: result.url });
        })
        .end(buffer);
    });
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看医生。如果您的端口号是 3000 并且您的 MongoDB 在本地运行，那么可以在[http://localhost:3000/recipe](http://localhost:3000/recipe)上访问它。一旦你能与文档很好地互动，客户端就变得非常容易。

#### 移交给客户端

如果你熟悉从 Vue 向服务器发送数据，使用 vue-apollo 没有太大区别。你所需要做的就是写出你的问题。Vue-apollo 为您处理其余的事情。

那么，让我们设置我们的 Vue-apollo:

```
import Vue from 'vue';
import VueApollo from "vue-apollo";
import apolloUploadClient from "apollo-upload-client"
import { InMemoryCache } from "apollo-cache-inmemory";
import { ApolloClient } from "apollo-client";
import { setContext } from "apollo-link-context";
Vue.use(VueApollo);
const baseUrl = "http://localhost:3000/recipe";
const uploadClientLink = apolloUploadClient.createUploadLink({
  uri: baseUrl
});
const interceptor = setContext((request, previousContext) => {
  const token = localStorage.getItem("token");
  if(token) {
    return {
        headers: {
          authorization: token
        }
      };
  }
});
const apolloClient = new ApolloClient({
  link: interceptor.concat(uploadClientLink),
  cache: new InMemoryCache(),
  connectToDevTools: true
});
const instance = new VueApollo({
  defaultClient: apolloClient
});
export default instance;
view raw 
```

Enter fullscreen mode Exit fullscreen mode

apollo-link-context 中的 setContext 允许我们在请求传递到服务器之前拦截它。记住，我们需要将一个令牌传递给头部，以便访问经过身份验证的资源。

每个响应都被缓存。cache: new InMemoryCache()，从上面的演示中可以看出，当一个新的菜谱被创建时，我们希望它和其他菜谱一起反映在所有菜谱页面中。但是响应已经被缓存了。它返回从内存中获取的响应。这有利有弊。

这个。$apollo 使我们能够访问 Vue apollo，只要它已被添加(Vue.use(VueApollo))到 Vue。

让我们创建我们的查询:

```
import gql from "graphql-tag";
// user object pointing to loginUser to make the return response pretty
export const LOGIN_QUERY = gql`
  mutation LoginUser($input: LoginUser!) {
    user: loginUser(input: $input) {
      token
    }
  }
`;
export const REGISTERATION_QUERY = gql`
  mutation RegisterUser($input: NewUser!) {
    user: createUser(input: $input) {
      token
    }
  }
`;
export const ALL_RECIPES_QUERY = gql`
  query {
    recipeList: Recipes {
      id
      name
      image
    }
  }
`;
export const GET_USER_QUERY = gql`
  query {
    user: getMe {
      name
      email
      recipes {
        id
        name
        image
      }
    }
  }
`;
export const CREATE_RECIPE_QUERY = gql`
  mutation createRecipe($input: NewRecipe!) {
    newRecipe(input: $input) {
      id
    }
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

这里的查询结构是我们在服务器端所期望的，查询的响应是基于选择的。

`GET_USER_QUERY`获取会话中用户的姓名、电子邮件、食谱(id、姓名和图像)。

```
apollo: {
    user: query.GET_USER_QUERY
  } 
```

Enter fullscreen mode Exit fullscreen mode

apollo 被添加到组件中，以获取与对象相关的特定查询。getMe 查询与用户对象相关联。

在 my-recipe 页面上，我们不希望响应来自缓存内存。

```
this.$apollo.queries.user.refetch(); 
```

Enter fullscreen mode Exit fullscreen mode

refetch 使用相应的(用户)查询从服务器获取最新数据。这应该谨慎使用。

可以从 apollo 访问一些有趣的选项，比如加载状态、错误、暂停查询流等等。

我们如何处理突变？

让我们看看如何创建食谱:

因为我们对单个文件感兴趣，所以 file 对象被绑定到 image(对象的一部分将被发送到服务器)。

```
onFileChange(e) {
      const files = e.target.files || e.dataTransfer.files;
      this.image = files[0];
    } 
```

Enter fullscreen mode Exit fullscreen mode

$this.apollo.mutate 接受变异及其变量。因为服务器正在请求输入。一个输入对象被传递给变量。

```
async createRecipe() {
      const recipeObject = {
        name: this.recipeName,
        description: this.description,
        difficultyLevel: this.difficultyLevel
          ? parseInt(this.difficultyLevel)
          : 0,
        image: this.image,
        steps: this.stepsList,
        averageTimeForCompletion: this.averageTime
          ? parseInt(this.averageTime)
          : 0
      };
      await this.$apollo
        .mutate({
          mutation: query.CREATE_RECIPE_QUERY,
          variables: { input: recipeObject }
        })
        .then(({ data }) => {
            this.$router.push({ name: "my-recipes" });
        })
        .catch(err => {
          console.log(err);
          this.error =
            parseGraphqlError(err) || "Something went wrong. Try again...";
        });
    } 
```

Enter fullscreen mode Exit fullscreen mode

*创建配方*

#### 结论

就像我前面说的，相比我们习惯的(REST)，GraphQL 更容易设置、更新和重构。

如果您的模式设计得不好，您的客户端可以对您的模式进行递归调用。避免会给出相同结果的查询。关于 GraphQL with Apollo 的更多信息，请点击[这里](https://www.apollographql.com/docs/)。如果你想了解更多关于 Vue Apollo 的信息，请点击这里的。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

关于如何将 GraphQL 与 Node 和 Vue 一起使用的帖子[首先出现在](https://blog.logrocket.com/demonstration-on-how-to-use-graphql-with-node-and-vue-in-bits-8981f0d65f94/)[的 LogRocket 博客](https://blog.logrocket.com)上。
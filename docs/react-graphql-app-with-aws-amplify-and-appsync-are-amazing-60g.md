# 带 Aws Amplify 和 AppSync 的 React + GraphQL app 太厉害了！

> 原文：<https://dev.to/johndemian/react-graphql-app-with-aws-amplify-and-appsync-are-amazing-60g>

这篇文章将会与你所习惯的稍有不同，并且会比你所习惯的更长，但是我保证，这将会是一篇有趣的文章。我们准备用 Aws amplify 和 AppSync 搭建一个无服务器的 React + GraphQL Web app。

## 什么是 Aws AppSync？

Aws AppSync 帮助我们为 Android 或 IOS 或 Web 应用程序创建无服务器后端。它与 Amazon DynamoDB、Elasticsearch、Cognito 和 Lambda 集成，使您能够创建复杂的应用程序，具有几乎无限的吞吐量和存储，可根据您的业务需求进行扩展。

AppSync 还支持实时订阅以及对应用数据的离线访问。

当离线设备重新连接时，AppSync 将仅同步设备离线时发生的更新，而不是整个数据库。

### app sync 是如何工作的？

[![aws app sync works](img/1044bf104caedc24419ef0a295c38115.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QbEs5VML--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dashbird.img/blog/2019-01-24/aws-appsync-work.png)

我们将使用 AppSync 可视化编辑器或 Amplify cli 创建我们的 GraphQL 模式。一旦完成，AppSync 就会处理所有事情，比如启用 Dynamodb 资源和为我们的模式创建解析器函数。

### Amplify 框架入门

首先，我们需要安装 Amplify 命令行工具，该工具用于在 AWS 上创建和维护无服务器后端。

运行以下命令安装`aws-amplify`。

```
npm install -g @aws-amplify/cli 
```

Enter fullscreen mode Exit fullscreen mode

Mac 用户需要在`npm`前使用`sudo`。

[![aws-amplify-cmd-tool installation](img/dc9958fc6873b5b31df8ed52555de1b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oAB9OQbj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dashbird.img/blog/2019-01-24/aws-amplify-cmd-tools.png)

成功安装后，您需要通过运行以下命令来配置您的 AWS 帐户。

```
amplify configure 
```

Enter fullscreen mode Exit fullscreen mode

[观看此视频](https://s3.us-east-2.amazonaws.com/amplify-get-started-video/amplifyclisetup.mp4)使用您的 Aws 帐户配置您的 cli。

### 创建 React App

使用`create-react-app`创建 react 应用程序。

```
npx create-react-app awsgraphql-react 
```

Enter fullscreen mode Exit fullscreen mode

上述命令将下载“awsgraphql-react”文件夹中所需的文件，以启动 react 应用程序。

`cd awsgraphql-react`改变工作目录。

### 添加 GraphQL 后端

运行以下命令来初始化新的 amplify 项目。

```
amplify init 
```

Enter fullscreen mode Exit fullscreen mode

它会提示不同的问题，比如选择你最喜欢的代码编辑器和你正在构建的应用类型。

[![amplify-init](img/12a5bbcbb6b03f1cfc9e1a1db7dfb37b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l6JrMkFx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dashbird.img/blog/2019-01-24/amplify-init.png)

现在在你的代码编辑器中打开你的项目文件夹，你会看到一个`amplify`文件夹和`.amplifyrc`文件被添加到你的 react 应用中。

[![folder-structure-amplify.png](img/0ae44b17488d0ef08a673bff52701c55.png)img/blog/2019-01-24/folder-structure-amplify.png)

一旦您成功地初始化了 amplify 项目，就该通过运行以下命令向我们的项目添加 AppSync **graphql** API 了。

```
amplify add api 
```

Enter fullscreen mode Exit fullscreen mode

该命令将提示两个选项`Rest`或`GraphQL`选择 GraphQL。

```
? Please select from one of the below-mentioned services (Use arrow keys)
❯ GraphQL
  REST 
```

Enter fullscreen mode Exit fullscreen mode

命名您的 GraphQL 端点并选择授权类型`Api` key。

```
? Please select from one of the below mentioned services GraphQL
? Provide API name: awsgraphqlreact
? Choose an authorization type for the API (Use arrow keys)
❯ API key
  Amazon Cognito User Pool 
```

Enter fullscreen mode Exit fullscreen mode

现在您需要选择以下选项。

```
? Do you have an annotated GraphQL schema? No
? Do you want a guided schema creation? Yes
? What best describes your project: Single object with fields (e.g., “Todo” with
 ID, name, description)
? Do you want to edit the schema now? Yes 
```

Enter fullscreen mode Exit fullscreen mode

让我们在将我们的模式推送到 aws 之前编辑它。打开您的 graphql 模式，它位于以下文件夹*amplify/back end/API/awsgraphqreact/schema . graph QL*。

删除所有内容并添加下面的模式。

```
type  Post  @model  {  id:  ID!  title:  String!  body:String!  createdAt:String!  } 
```

Enter fullscreen mode Exit fullscreen mode

这是一个`Post`对象类型，有四个字段`ID`、`title`、`body`和`createdAt`。

**@model** :这是一个模型指令，告诉 amplify cli 在 dynamodb 表中存储以下类型。

现在运行下面的命令来更新您的后端模式。

```
amplify push 
```

Enter fullscreen mode Exit fullscreen mode

该命令将提示以下问题，并为每个问题选择`yes`。

```
| Category | Resource name   | Operation | Provider plugin   |
| -------- | --------------- | --------- | ----------------- |
| Api      | awsgraphqlreact | Create    | awscloudformation |
? Are you sure you want to continue? Yes

GraphQL schema compiled successfully. Edit your schema at /Users/saigowtham/Desktop/awsgraphql-react/amplify/backend/api/awsgraphqlreact/schema.graphql
? Do you want to generate code for your newly created GraphQL API Yes
? Choose the code generation language target javascript
? Enter the file name pattern of graphql queries, mutations
and subscriptions src/graphql/**/*.js
? Do you want to generate/update all possible GraphQL operations
- queries,mutations and subscriptions Yes 
```

Enter fullscreen mode Exit fullscreen mode

如果你打开你的 aws 控制台【https://console.aws.amazon.com/appsync/ T4】，你可以看到一个完整的模式文件，包含`queries`、`mutations`和解析器函数，它是由`aws-amplify` cli 使用我们的`Post`对象类型创建的。

[![backend-graphql-schema](img/49d9d077859692fc6a9a89045f0459da.png)img/blog/2019-01-24/backend-graphql-schema.png)

### 连接 GraphQL Api 进行反应

现在，我们正在将我们的 GraphQL 后端与 react 应用程序连接起来，为此，我们首先需要下载以下软件包。

```
npm install aws-appsync graphql-tag react-apollo 
```

Enter fullscreen mode Exit fullscreen mode

成功安装后，现在在 react 应用程序中打开`index.js`文件，添加以下配置。

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import gql from 'graphql-tag';
import AWSAppSyncClient, { AUTH_TYPE } from 'aws-appsync';
import aws_config from './aws-exports';

const client = new AWSAppSyncClient({
    url: aws_config.aws_appsync_graphqlEndpoint,
    region: aws_config.aws_appsync_region,
    auth: {
        type: AUTH_TYPE.API_KEY,
        apiKey: aws_config.aws_appsync_apiKey,
    }
});

ReactDOM.render(<App />, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们从`aws-appsync`包中导入`AWSAppSyncClient`构造函数、`AUTH_TYPE`,从由 amplify cli 自动创建的`./aws-exports`文件中导入`aws_config`。

接下来，我们必须通过传递 aws_config 来实例化新的`AWSAppSyncClient`客户端。

### 运行第一个查询

在 graphql 中,‘query’用于从`graphql`端点获取数据。

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import gql from 'graphql-tag';
import AWSAppSyncClient, { AUTH_TYPE } from 'aws-appsync';
import aws_config from './aws-exports';

import { listPosts } from './graphql/queries';

const client = new AWSAppSyncClient({
    url: aws_config.aws_appsync_graphqlEndpoint,
    region: aws_config.aws_appsync_region,
    auth: {
        type: AUTH_TYPE.API_KEY,
        apiKey: aws_config.aws_appsync_apiKey,
    }
});

client.query({
    query: gql(listPosts)
}).then(({ data }) => {
    console.log(data);
});

ReactDOM.render(<App />, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们通过传递由`aws-amplify`基于我们的 graphql 端点自动生成的`listPosts`查询来调用 client.query 方法。

您将在浏览器控制台中找到这个查询的记录数据。

[![client-query](img/31f3ccd2f941b8781a0b99c053532ac3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VSuLr1RF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dashbird.img/blog/2019-01-24/client-query.png)

因为我们的 dynamodb 表中没有任何数据，所以我们得到了`0`项，这是我们应该期望的。

让我们使用“react-apollo”来运行来自`UI`的查询和变异。

*index.js*

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import AWSAppSyncClient, { AUTH_TYPE } from 'aws-appsync';
import aws_config from './aws-exports';
import { ApolloProvider } from 'react-apollo'

const client = new AWSAppSyncClient({
    url: aws_config.aws_appsync_graphqlEndpoint,
    region: aws_config.aws_appsync_region,
    auth: {
        type: AUTH_TYPE.API_KEY,
        apiKey: aws_config.aws_appsync_apiKey,
    }
});

ReactDOM.render(<ApolloProvider client={client}>
    <App />
</ApolloProvider>, document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们从“react-apollo”导入一个`ApolloProvider`组件，并通过传递一个`client`将它包装在我们的`App`组件中，这样我们就可以从 react 应用程序的任何地方访问`client`。

### 创建帖子

我们需要在`createPost.js`文件中创建一个名为`CreatePost`的新组件，帮助我们运行`Mutation`并将数据添加到我们的后端。

*create post . js*T2】

```
import React from "react";
import { Mutation } from "react-apollo";
import { createPost } from "./graphql/mutations";
import gql from "graphql-tag";

class CreatePost extends React.Component {
  handleSubmit = (e, createPost) => {
    e.preventDefault();
    createPost({
      variables: {
        input: {
          title: this.title.value,
          body: this.body.value,
          createdAt: new Date().toISOString()
        }
      }
    }).then(res => {
      this.title.value = "";
      this.body.value = "";
    });
  };
  render() {
    return (
      <div>
        <h1>Create post</h1> 
        <Mutation mutation={gql(createPost)}>
          {(createPost, { data, loading, error }) => {
            return (
              <div>
                <form
                  className="add-post"
                  onSubmit={e => this.handleSubmit(e, createPost)}
                >
                  <input
                    type="text" placeholder="Title"
                    ref={node => (this.title = node)}
                    required
                  />
                  <textarea
                    rows="3"
                    cols="40"
                    placeholder="Body"
                    ref={node => (this.body = node)}
                    required
                  />
                  <button>{loading ? "Yes boss..." : "Create Post"}
                  </button>
                </form>
                {error && <p>{error.message}</p>}
              </div>
            );
          }}
        </Mutation>
      </div>
    );
  }
}

export default CreatePost; 
```

Enter fullscreen mode Exit fullscreen mode

在`CreatePost`中，我们从‘react-Apollo’中导入了一个`Mutation`组件，从‘graph QL-tag’中导入了`gql`。然后从`./grahql/mutations`文件导入`createPost`突变。

“createPost”变异有三个动态参数，分别是`title`、`body`、`createdAt`。

**标题:**我们帖子的标题。

**正文**:我们帖子的正文。

**创建日期**:发布创建时间和日期。

在您的`App.js`中导入`createPost`组件。

*app . js*T2】

```
import React, { Component } from 'react';
import CreatePost from './createPost';

class App extends Component {
  render() {
    return (
      <div className="App">
        <CreatePost />
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

让我们通过创建第一篇文章来测试 createPost 组件。

[![create-post-mutation](img/780e0def28765d3d2a4eb9da138e305a.png)img/blog/2019-01-24/create-post-mutation.gif)

打开 aws 控制台，查看您的数据存储在 DynamoDB 表中。

### 获取数据

目前，我们没有在 UI 上呈现任何数据，所以让我们向 GraphQL 端点查询一个数据，以便我们可以看到新创建的帖子。

我们需要创建两个新组件。

*post.js*

```
import React from 'react';

class Post extends React.Component {

    componentDidMount() {
        this.props.subscribeToMore();
    }

    render() {
        const items = this.props.data.listPosts.items;

        return items.map((post) => {
            return (
                <div>
                    <h1>{post.title}</h1>
                    <p>{post.body}</p>
                    <time dateTime={post.createdAt}>
                    {new Date(post.createdAt).toDateString()}</time>
                    <br />
                </div> 
            )
        })

    }

}

export default Post; 
```

Enter fullscreen mode Exit fullscreen mode

*display posts . js*T2】

```
import React from 'react'
import { Query } from 'react-apollo'
import { listPosts } from './graphql/queries';
import { onCreatePost } from './graphql/subscriptions'
import gql from 'graphql-tag';
import Post from './post'

class DisplayPosts extends React.Component {

    subsCribeNewPosts = (subscribeToMore) => {
        return subscribeToMore({
            document: gql(onCreatePost),
            updateQuery: (prev, { subscriptionData }) => {
                if (!subscriptionData.data) return prev;
                const newPostData = subscriptionData.data.onCreatePost;
                return Object.assign({}, prev, {
                    listPosts: {
                        ...prev.listPosts,
                        items: [...prev.listPosts.items, newPostData]
                    }
                })
            }
        })
    }

    render() {
        return (
            <div className="posts">
                <Query query={gql(listPosts)}  >
                    {({ loading, data, error, subscribeToMore }) => {

                        if (loading) return <p>loading...</p>
                        if (error) return <p>{error.message}</p> 
                        return <Post data={data} subscribeToMore={() =>
                            this.subsCribeNewPosts(subscribeToMore)} />
                    }}
                </Query> 

            </div>
        )
    }
}

export default DisplayPosts; 
```

Enter fullscreen mode Exit fullscreen mode

在`DisplayPosts`组件中，我们查询帖子列表，并启用**实时**订阅，这样我们可以首先看到新创建的帖子。

在查询组件内部，我们访问`subscribeToMore`函数并将其传递给`subscribeNewPosts`方法。

**subscribe more:**每当 Post 组件被挂载到 dom 并监听添加到我们的 graphql API 中的新 Post 时，它就会被调用。

**update query:**update query 函数用于合并以前的数据和当前的数据。

通过导入`DisplayPosts`组件来更新您的`App.js`文件。

*app . js*T2】

```
import React, { Component } from 'react';
import CreatePost from './createPost';
import DisplayPosts from './displayPosts';

class App extends Component {
  render() {
    return (
      <div className="App">
        <CreatePost />
        <DisplayPosts />
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

让我们通过创建新帖子来测试我们的`DisplayPosts`组件。

[![real-time-subcriptions.gif](img/b9dd468e8a122d0fba2fcbcf08bb43e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w8lp1JUR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://dashbird.img/blog/2019-01-24/real-time-subcriptions.gif)

在上图中，我们通过打开两个新的浏览器窗口进行了测试。

### 编辑帖子

让我们创建`EditPost`组件，它可以帮助我们编辑之前创建的文章。

*编辑邮件. js*

```
import React from "react";
import { updatePost } from "./graphql/mutations";
import { Mutation } from "react-apollo";
import gql from "graphql-tag";

class EditPost extends React.Component {
  state = {
    show: false,
    postData: {
      title: this.props.title,
      body: this.props.body
    }
  };

  handleModal = () => {
    this.setState({ show: !this.state.show });
    document.body.scrollTop = 0;
    document.documentElement.scrollTop = 0;
  };

  handleSubmit = (e, updatePost) => {
    e.preventDefault();
    updatePost({
      variables: {
        input: {
          id: this.props.id,
          title: this.state.postData.title,
          body: this.state.postData.body
        }
      }
    }).then(res => this.handleModal());
  };

  handleTitle = e => {
    this.setState({
      postData: { ...this.state.postData, title: e.target.value }
    });
  };

  handleBody = e => {
    this.setState({
      postData: { ...this.state.postData, body: e.target.value }
    });
  };

  render() {
    return (
      <>
        {this.state.show && (
          <div className="modal">
            <button className="close" onClick={this.handleModal}>
              X
            </button>
            <Mutation mutation={gql(updatePost)}>
              {updatePost => {
                return (
                  <form
                    className="add-post"
                    onSubmit={e => this.handleSubmit(e, updatePost)}
                  >
                    <input
                      type="text"
                      required
                      value={this.state.postData.title}
                      onChange={this.handleTitle}
                    />
                    <textarea
                      rows="3"
                      cols="40"
                      required
                      value={this.state.postData.body}
                      onChange={this.handleBody}
                    />
                    <button>Update Post</button>
                  </form>
                );
              }}
            </Mutation>
          </div>
        )}
        <button onClick={this.handleModal}>Edit</button>
      </>
    );
  }
}

export default EditPost; 
```

Enter fullscreen mode Exit fullscreen mode

在`EditPost`中，我们将导入`Mutation`组件、`updatePost`突变和`gql`标签，然后我们通过传递`mutation`属性来使用突变组件。

在突变组件中，我们需要将函数作为子级传递，因为它使用了 render props 模式。

该函数的第一个参数是`mutation`函数，因此我们将该函数作为参数传递给`handleSubmit`方法，并用更新后的 post `title`和`body`进行调用。

打开您的`post.js`文件并添加`EditPost`组件。

*post.js*

```
import React from 'react';
import EditPost from './editPost'

class Post extends React.Component {

    componentDidMount() {
        this.props.subscribeToMore();
    }

    render() {
        const items = this.props.data.listPosts.items;

        return items.map((post) => {
            return (
                <div>
                    <h1>{post.title}</h1>
                    <p>{post.body}</p>
                    <time dateTime={post.createdAt}>
                    {new Date(post.createdAt).toDateString()}</time>
                    <br />
                    <EditPost {...post} />
                </div> 
            )
        })

    }

}

export default Post; 
```

Enter fullscreen mode Exit fullscreen mode

让我们通过编辑任何先前创建的帖子来测试我们的 EditPost 组件。

[![edit-post-mutation](img/fe55a1fe0b3e25d5f4e09ac5b030354f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VJLZ5pB6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://dashbird.img/blog/2019-01-24/edit-post-mutation.gif)

### 删除邮件

现在我们正在用**乐观 UI** 实现`DeletePost`组件。

#### 什么是乐观 UI？

例如，如果我们删除一个帖子，从服务器获得响应需要时间，只有这样我们才能更新 UI。有了乐观的用户界面，我们可以渲染这个组件，一旦我们得到服务器的响应，我们就用实际的服务器结果替换乐观的结果。

创建一个名为`deletePost.js`的新文件。

*deletePost.js*

```
import React, { Component } from 'react'
import { Mutation } from 'react-apollo';
import { deletePost } from './graphql/mutations';
import gql from 'graphql-tag';
import { listPosts } from './graphql/queries';

class DeletePost extends Component {

    handleDelete = (deletePost) => {
        deletePost({
            variables: {
                input: {
                    id: this.props.id
                }
            },
            optimisticResponse: () => ({
                deletePost: {
                    // This type must match the return type of
                    //the query below (listPosts)
                    __typename: 'ModelPostConnection',
                    id: this.props.id,
                    title: this.props.title,
                    body: this.props.body,
                    createdAt: this.props.createdAt
                }
            }),
            update: (cache, { data: { deletePost } }) => {
                const query = gql(listPosts);

                // Read query from cache
                const data = cache.readQuery({ query });

                // Add updated postsList to the cache copy
                data.listPosts.items = [
                    ...data.listPosts.items.filter(item =>
                     item.id !== this.props.id)
                ];

                //Overwrite the cache with the new results
                cache.writeQuery({ query, data });
            }
        })
    }

    render() {
        return (
            <Mutation mutation={gql(deletePost)}>
                {(deletePost, { loading, error }) => {
                    return <button onClick={
                       () => this.handleDelete(deletePost)}>
                        Delete Post</button>
                }}
            </Mutation>
        )
    }
}

export default DeletePost; 
```

Enter fullscreen mode Exit fullscreen mode

在`optimisticResponse`函数中，我们用`__typename:'ModelPostConnection'`准确地传递了删除帖子的数据，然后我们通过删除已删除的帖子来更新缓存。

通过添加`DeletePost`组件来更新您的`post.js`文件。

*post.js*

```
import React from 'react';
import EditPost from './editPost'
import DeletePost from './deletePost'

class Post extends React.Component {

    componentDidMount() {
        this.props.subscribeToMore();
    }

    render() {
        const items = this.props.data.listPosts.items;

        return items.map((post) => {
            return (
                <div key={post.id}>
                    <h1>{post.title}</h1>
                    <p>{post.body}</p>
                    <time dateTime={post.createdAt}>{
                        new Date(post.createdAt).toDateString()}</time>
                    <br />
                    <EditPost {...post} />
                    <DeletePost {...post} />
                </div> 
            )
        })
    }
}

export default Post; 
```

Enter fullscreen mode Exit fullscreen mode

[![delete-mutation](img/2e2b0921567127df4251e3781c934d3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HabtLLvY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://dashbird.img/blog/2019-01-24/delete-mutation.gif)

在上面，我们已经在离线模式下测试了它，但我们可以看到，一旦我们上线 appsync 发送一个`deletePost`突变来更新我们的后端，UI 就会通过“乐观响应”立即更新。

### 托管 React 应用

通过使用 amplify-cli，我们还可以在 Aws s3 bucket 和 CloudFront 中托管 react 应用程序。

打开您的终端并运行以下命令。

```
amplify hosting add 
```

Enter fullscreen mode Exit fullscreen mode

[![hosting-cmd](img/a8266fcb77313df988cf51ec410721c2.png)img/blog/2019-01-24/hosting-cmd.png)

[![published url](img/4ae5bcd50ab2cc107a3613b32a54e81a.png)img/blog/2019-01-24/published-url.png)

[代码库](https://github.com/saigowthamr/Serverless-React-GraphQL-app)

对于 AWS Lambdas 的监控、调试和错误检测，我们使用 Dashbird。

### 为什么是 Dashbird？

*   Dashbird 有助于我们主动监控运行状况和错误。

*   Dashbird 的一个主要特点是用户友好的界面。

*   Dashbird 可视化所有 AWS Lambda 指标，如
    内存利用率、调用计数和执行持续时间。

### DashBird 界面

[![dashbird-interface](img/524833960fdc62f4bdc42271a9c6b1ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_YeSr1rW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://dashbird.img/blog/2019-01-24/dashbird-interface.gif)

我知道，这是一个非常长的帖子，我必须祝贺你坚持下去。既然你花时间阅读了全部内容，我很想听听你的想法。请留下评论，让我知道你喜欢或不喜欢它。

* * *

## 疯狂支持 Sai 创建了如此庞大而全面的教程。我们期待着阅读他的下一部作品。点击这里查看他的网站。

我把它贴在了 Dashbird 的博客上，因为它在那里很受欢迎，我想你们可能也会喜欢它。
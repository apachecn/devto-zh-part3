# 什么是 MERN 堆栈，我如何使用它？

> 原文：<https://dev.to/iam_timsmith/what-is-the-mern-stack-and-how-do-i-use-it-55b8>

> 原文可见[此处](https://www.iamtimsmith.com/blog/what-is-the-mern-stack-and-how-does-it-work/)
> 该项目的代码可见[此处](https://github.com/iamtimsmith/simple-mern-app)。

如果你已经在开发者世界呆了一段时间，你可能会看到一些评论或文章提到“堆栈”,比如 MEAN、LAMP 或 XAMPP。

今天我们将深入探讨 MERN 堆栈，它类似于前面提到的均值堆栈。我将解释它是什么，以及在构建 web 应用程序或网站时如何使用它。让我们开始吧！

### 什么是“栈”？

也许首先要讨论的是“堆栈”的概念。有许多不同的堆栈，但它们都只是做同一件事的不同方式:创建一个前端(网站或应用程序)，允许人们以简单和可管理的方式与后台的服务器和数据库进行交互。堆栈就是用来实现这一目标的不同技术。

> "堆栈就是用来实现这一目标的不同技术."

虽然有许多不同的堆栈可供考虑，但有些已经变得比其他更常见。其中一种流行的堆栈称为均值堆栈，它包括:

*   **M** ongoDb
*   E xpress.js
*   一个 ngular.js
*   **N** ode.js

今天我们将看到 MERN 堆栈，它与 MEAN 几乎相同，只是我们将 Angular.js 替换为 React.js。这将允许我们将 MongoDB 用于我们的数据库，将 Node.js 和 Express.js 用于我们的服务器和路由，并将 React.js 用于创建供用户交互的前端。

[![How do we build with it?](img/f394166d46f749a855518c5bfb566c61.png)](https://i.giphy.com/media/tO5ddHjpXB6lG/giphy.gif)

### 我们如何用它来建造？

在我们开始深入细节之前，让我们先概述一下这些部分是如何协同工作的。我花了一段时间才“明白”这一点，因为我来自后端和前端混合的 PHP 背景。

#### 1。后端和前端

首先要明白的是，后端和前端是独立的实体。前端可以位于同一个存储库中，也可以位于单独的存储库中。

#### 2。蜜蜂

如果你现在想知道我们如何让这两者一起工作，答案是通过 API。将在我们的服务器上创建一个 API(或应用程序接口),它将提供我们的前端应用程序可以与之交互的“端点”。

举例来说，把你的左手想象成后端，右手想象成前端。

现在把你的手放在一起，手指交织在一起，就像你和自己手牵手一样。这就是模板语言的工作方式。它们允许你用从服务器转储的数据来渲染一些降价，所以两者之间有很多重叠。

现在分开你的手。这一次尽可能将手指分开，只让左手指尖接触到右手指尖。这就是 MERN 堆栈的工作原理。后端提供端点(左手的指尖)，允许访问服务器，而前端(右手的手指)调用这些端点(它们接触的地方)来访问服务器(左手)。

希望这能澄清一些事情，如果没有，就当我没提过。

### 后端

虽然我不打算在本文中一步一步地讨论如何构建它(那将是一篇单独的文章)，但我想回顾一下这个堆栈中可能/通常使用的不同部分。我浏览了几个教程，这些教程解释了如何设置服务器，但不一定解释了为什么要使用这些库来设置服务器。

[![Give me the details on MERN Stack](img/8c502bf45b9b285712339a7079e176cb.png)](https://i.giphy.com/media/5wFkqt6A8R4qAqGIFQ/giphy.gif)

一旦我们创建了我们的`app.js`文件，我们将不得不安装一些包。这里是我以前在 Express.js 项目中使用过的一些常用包，可能对你有帮助。

*   Express.js -一个 web 应用框架，内置了很多功能，包括路由。
*   一个 ODM，允许我们的 express.js 应用程序和 MongoDB 之间的交互。
*   这个库允许我们的 express.js 应用程序读取传入请求的主体(或内容)。
*   **DotENV** -允许我们对敏感数据使用. ENV 文件。
*   **Passport.js** -我们的应用程序的认证，有几种不同的认证方法选项。
*   验证器(Validator)-对多种类型的数据进行简单的验证。
*   **bCrypt** -对密码等敏感数据进行加密
*   **Nodemon** -当情况发生变化时，为我们的节点服务器进行“热重装”，这样我们就不必每次做出改变时都要停止和启动服务器

当然还有很多其他的包，但是这些是我看到的一些常用的库以及为什么使用它们。

现在我们已经讨论了一些常用的包，让我们来看看一些代码。首先，我们的服务器:

```
const express = require('express')
const mongoose = require('mongoose')
const bodyParser = require('body-parser')
const app = express()

app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())

// Import Model
const Post = require('./models/Post')

// Connect to MongoDB
mongoose.connect(
  'mongodb://localhost:27017/simple-mern',
  () => console.log('MongoDB is connected')
)

// Enable CORS
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*')
  res.header(
    'Access-Control-Allow-Headers',
    'Origin, X-Requested-With, Content-Type, Accept'
  )
  next()
})

// Get all of our posts
app.get('/api/posts', (req, res) => {
  Post.find({}).then(posts => {
    res.json(posts)
  })
})

// Get One of Our posts
app.get('/api/posts/:id', (req, res) => {
  Post.findOne({ _id: req.params.id }).then(post => {
    res.json(post)
  })
})

// Create and Update post
app.post('/api/posts', (req, res) => {
  const data = {
    title: req.body.title,
    content: req.body.content,
  }
  Post.findOne({ _id: req.body.id }, (err, post) => {
    if (post) {
      Post.findByIdAndUpdate(req.body.id, data, { upsert: false }).then(
        updated => {
          res.json(updated)
        }
      )
    } else {
      Post.create(data).then(created => {
        res.json(created)
      })
    }
  })
})

// Delete selected post
app.post('/api/posts/:id', (req, res) => {
  Post.findByIdAndDelete(req.params.id).then(post => {
    res.json({ message: 'Your post was deleted!' })
  })
})

app.listen(3333, () => console.log('Server is running on port 3333')) 
```

Enter fullscreen mode Exit fullscreen mode

这是我们简单的 API 服务器。如你所见，它有一些基本的 CRUD(创建-读取-更新-删除)功能，但并不复杂。如果我们仔细观察，可以看到我们使用`res.json()`在特定的 URL 提供输出数据，而不是输出 HTML 或另一个模板。这就是我们如何构建 API 以使数据可用于反应。

您可能还注意到，我刚刚将 mongoose 指向了我机器上自己的 mongodb 服务器。为了正常工作，MongoDB 需要安装在您的计算机上并运行。如果它没有运行，只需弹出一个终端窗口并键入以下命令:

```
mongod 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的本地机器上启动 MongoDB 服务器。因为这只是在本地完成，如果你在回购中运行代码，你将看不到我的帖子。你必须创造新的内容。如果你正在寻找一些虚拟的内容，我目前最喜欢的生成器是 [Fillerama.io](http://fillerama.io/) ，它从我最喜欢的一些电影和节目中提取文本。

如果我们对测试服务器本身感兴趣，我们可以运行下面的命令来启动服务器:

```
npm run server

or

yarn server 
```

Enter fullscreen mode Exit fullscreen mode

在服务器启动并告诉我们它正在端口 3333 上运行并且 MongoDB 已连接之后，我们可以打开 [Postman](https://www.getpostman.com/) 并在那里测试我们的路由。对于获取路线，我们可以简单地输入路线并点击“发送”。对于发布路线，我们需要选择“正文”并创建/输入标题和内容字段。

### 前端

现在我们已经启动并运行了服务器，我们可以开始处理用户将与之交互的客户端(或前端)。这将使用 React 构建，可以通过几种不同的方式来完成。

第一种方法是将必要的前端库(react、react-dom、react-router 等)添加到与后端库相同的`package.json`文件中。虽然我确实为这个项目这样做了，但是应该注意的是，我不认为这是最佳实践。我觉得随着我们项目的增长，如果使用这种方法，我们的代码库会变得更加混乱和难以使用。我决定为这个特定的应用程序走这条路，因为我知道它不会增长或真正改变。我在这里引用的应用程序只是为了演示的目的。

第二个也是更好的方法(在我看来)是为后端创建一个回购协议，为前端创建一个单独的回购协议。我们仍然可以将前端回购克隆到我们的项目目录中，没有任何问题，只要我们确保将前端包含在`.gitignore`文件中。例如，这个应用程序的文件结构包括一个名为`client`的目录，用于存放我们所有的前端代码。我们可以将它放在一个单独的回购协议中，然后将下面的内容放入后端回购协议的`.gitignore`文件中:

```
client 
```

Enter fullscreen mode Exit fullscreen mode

将`client`文件夹添加到`.gitignore`文件将确保它不会被视为项目中的第二个回购。此外，这样做使得重新设计和替换前端变得简单，而不必触及后端。

你的应用程序如何设计将完全取决于你，我只是觉得通过维护前端和后端的独立 repos，事情可以保持更有组织性。
[![Organizing](img/3a9ed7659746421e983de61f161ff66d.png)T3】](https://i.giphy.com/media/cIWnDtConYRFPxmmah/giphy-downsized-large.gif)

#### 做出反应

现在我们已经讨论了项目组织，让我们来谈谈我们实际的前端代码。下面是我的 React 应用程序的`app.js`文件，而不是在这篇文章中把代码放入每个组件，我只是在这里放一个到回购[的链接，并解释每个组件在做什么。](https://github.com/iamtimsmith/simple-mern-app) 

```
import React, { Component } from 'react'
import ReactDOM from 'react-dom'
import { BrowserRouter as Router, Route } from 'react-router-dom'
import Header from './components/header'
import Index from './components/index'
import Single from './components/single'
import New from './components/new'
import Edit from './components/edit'

const App = () => (
  <Router>
    <div>
      <Header />
      <Route path="/" exact component={Index} />
      <Route path="/new" exact component={New} />
      <Route path="/post/:id" exact component={Single} />
      <Route path="/edit/:id" exact component={Edit} />
    </div>
  </Router>
)

ReactDOM.render(<App />, document.getElementById('app')) 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，`app.js`并不复杂。它有一个`<Router>`，允许我们在 React 中设置路由，根据 url 呈现不同的组件。下面是 React 应用程序中使用的其他组件:

*   **标题** -屏幕顶部的导航栏
*   **索引** -列出所有可用的博客文章
*   **New** -允许用户创建新博客文章的表单
*   **Single** -根据 id 显示一篇博客文章
*   **编辑** -允许用户根据 id 更新博客文章的表单

我们使用 Axios 对 API 端点进行 http 调用，然后使用 React 显示我们想要的数据。我将把 Index.js 代码放在这篇文章中，这样我们就可以检查它是如何一起工作的。

```
import React, { Component } from 'react'
import { Link } from 'react-router-dom'
import axios from 'axios'

class Index extends Component {
  constructor(props) {
    super(props)
    this.state = {
      posts: [],
    }
  }

  componentDidMount() {
    axios.get('http://localhost:3333/api/posts').then(posts => {
      this.setState({
        posts: posts.data,
      })
    })
  }

  render() {
    return (
      <div className="m-8">
        <ul className="index">
          {this.state.posts.map(post => (
            <li key={post.title}>
              <h2>
                <Link to={`/post/${post._id}`}>{post.title}</Link>
              </h2>
            </li>
          ))}
        </ul>
      </div>
    )
  }
}

export default Index 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们使用了一个类组件，它允许我们使用状态和生命周期方法。这是必要的，因为 Axios 调用应该在`componentDidMount()`生命周期方法中进行。应该注意的是，当我试图调用我的本地 API 时，我得到了一个 CORS 错误。为了解决这个问题，我在 Express 服务器的`server.js`文件中添加了一些头文件。该代码被记录在`server.js`文件的注释中。

#### 渲染

在结束之前，我想谈谈渲染。如果我们运行我们的应用程序并直接转到一个特定的博客帖子，可能会出现一些内容没有显示出来的问题。这可能会导致用户的浏览体验不佳，并使搜索引擎爬虫难以对网站进行索引。为了解决这个问题，我推荐使用类似于 [Gatsby.js](https://www.gatsbyjs.org/) 或 [Next.js](https://nextjs.org/) 的东西。这两种解决方案互不相同，但根据您的需求，它们都很有用。

Gatsby.js 是一个静态站点生成器，它允许你用 React 构建一个站点，然后 Gatsby 在构建时将它转换成静态文件，使站点速度超快。有很多有用的插件，让盖茨比变得真正多才多艺。事实上，[我的站点](https://www.iamtimsmith.com)是一个 Gatsby.js 站点！由于静态文件是在构建时创建的，因此每次源内容发生变化时，都需要重新构建站点。

[Next.js](https://nextjs.org/) 则是 React 站点的服务器端渲染。它内置了很多功能，比如路由、代码分割、样式组件等等。服务器端呈现意味着数据将像在服务器上一样自动更新，但它将在浏览器尝试显示它之前呈现。这意味着用户的数据不会有任何显示问题，搜索引擎爬虫可以毫无问题地完成工作。

还有很多其他的解决方案，但这是我听说最多的两个，也是我用来解决这个问题的两个。两者都有很棒的文档，也很容易上手和运行。

### 结论

我希望这篇文章有助于澄清一些关于 MERN 堆栈如何工作的困惑。它只是使用 MongoDB、Express.js 和 Node.js 来创建一个提供 API 端点的服务器，React 应用程序可以调用这些端点来获取数据。现在你有了更好的理解，去建造一些令人敬畏的东西，把你的链接贴在下面，这样我们就可以欣赏它们了！

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[iamtimsmith](https://github.com/iamtimsmith)/[simple-mern-app](https://github.com/iamtimsmith/simple-mern-app)

### 这是博客文章附带的 MERN 应用程序的代码库

<article class="markdown-body entry-content container-lg" itemprop="text">

# 简单的 MERN 应用

这是一个 MERN 应用程序的代码库，与位于[这里](https://www.iamtimsmith.com/blog/what-is-the-mern-stack-and-how-does-it-work/)的博客文章一起使用。它不是一个复杂的或生产就绪的应用程序，而是用简单易懂的术语解释 MERN 堆栈的不同部分是如何组合在一起的。

[![Simple MERN application by Tim Smith](img/59065a79c2852dccd5b08b7271e8fe0d.png)](https://raw.githubusercontent.com/iamtimsmith/simple-mern-app/master/image.png)

要下载:

```
git clone https://github.com/iamtimsmith/simple-mern-app.git 
```

然后:

```
npm install

or

yarn 
```

要仅运行服务器:

```
npm run server

or

yarn server 
```

要仅运行客户端:

```
npm run client

or

yarn client 
```

要同时运行两者:

```
npm run dev

or

yarn dev 
```

</article>

[View on GitHub](https://github.com/iamtimsmith/simple-mern-app)
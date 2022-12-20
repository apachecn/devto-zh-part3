# 借助 Express 和 React 构建完整的 Nexmo 应用

> 原文：<https://dev.to/vonagedev/build-a-full-stack-nexmo-app-with-express-and-react-1epb>

使用 JavaScript Nexmo Client SDK，您可以提供一个前端应用程序，允许用户控制他们参与的对话。一个 [Nexmo 对话](https://developer.nexmo.com/conversation/concepts/conversation)可以包括两个[用户](https://developer.nexmo.com/conversation/concepts/user)或者更多，并且使用多种不同的媒体。让参与者控制对话的元素只会为你的应用提供更多的选择。

[React](https://reactjs.org/) 是构建前端应用的一个非常受欢迎的选择，Nexmo 客户端 SDK 中的对话和用户等概念很好地映射了 React 组件及其自身的状态控制。然而，有些事情是 Nexmo 客户端 SDK 不能做的，所以在我们考虑后端之前，您的应用程序的全貌不会出现。使用 Express，您可以添加一些简单的路由来支持您的前端管理用户，并完成您认为最好保留在服务器上的任何其他工作。

与传统的服务器提供的应用程序不同，您的 React 前端本身就是一个应用程序，这意味着您的“全栈”应用程序实际上是两个应用程序。每一个都将在自己的端口上侦听并响应请求，而不需要与另一端进行核对。从文件结构的角度来看，这就像是应用中的应用。您将在目录的根目录中设置您的 Express 服务器，然后在子目录中添加 React 应用程序——包括它自己单独的`package.json`。

## 应用程序设置

在项目目录的根目录下，首先为您的 Express 应用程序创建一个`package.json`和一个包含它的`server.js`。您还需要创建一个`.env`文件来存储您的敏感应用程序和帐户凭证。你需要安装几个来自 [npm](https://www.npmjs.com/) : [Express](https://expressjs.com/) ， [body-parser](https://www.npmjs.com/package/body-parser) ， [dotenv](https://www.npmjs.com/package/dotenv) ，当然还有 [Nexmo Node SDK](https://github.com/nexmo/nexmo-node) :

```
npm install -s express body-parser dotenv nexmo@beta 
```

为了同时启动两个应用程序，您还需要将 [`concurrently`](https://www.npmjs.com/package/concurrently) 作为开发依赖项安装:

```
npm install --save-dev concurrently 
```

接下来，您将提供识别您的 [Nexmo 应用程序](https://developer.nexmo.com/conversation/concepts/application)所需的密钥、id 和秘密，您可以将它们存储在您的`.env`文件中:

```
API_KEY=""
API_SECRET=""
APP_ID=""
PRIVATE_KEY="/private.key" 
```

您可以在 Nexmo 仪表板的[入门](https://dashboard.nexmo.com/getting-started-guide)页面找到您的 API 密匙和密码。您可以从[创建应用程序页面](https://dashboard.nexmo.com/voice/create-application)下载一个应用程序 ID 和一个生成的私钥(如果您的应用程序用于消息传递，您可以使用[创建消息应用程序](https://dashboard.nexmo.com/messages/create-application)页面)。在示例中，私钥保存在您的根目录中，所以如果您将它移动到其他地方，一定要更新`.env`中的路径。

完成设置后，您可以继续创建客户端应用程序。

## 创建 React App

你可以使用非常方便的 [create-react-app](https://github.com/facebook/create-react-app) 为你的客户快速创建一些脚手架。因为这个 React 应用程序将位于项目的子目录中，所以您可以在运行命令时指定您喜欢的子目录名称(尽管您可能希望在应用程序生成其`package.json`后将应用程序名称更改为更具描述性的名称)。在我们的例子中，我们将子目录称为“client”:

```
npx create-react-app client 
```

这将为您提供客户端所需的大部分内容，包括 React 的依赖项和一组脚本，用于启动和构建您的应用程序。你需要从 npm 获得的唯一额外的软件包是 [Nexmo 客户端 SDK](https://www.npmjs.com/package/nexmo-client) :

```
npm install -s nexmo-client@beta 
```

在您的客户端`package.json`中，您还需要添加一个代理，它将引用您的 Express 服务器和运行它的端口:

```
"proxy": "http://localhost:3001", 
```

## 快递服务器

如果您以前使用过 Express，那么初始化 Express 服务器应该很熟悉。您还需要`dotenv`和`body-parser`，后者将作为中间件附加到您的应用程序:

```
require('dotenv').config();

// init server
const express = require('express');
const app = express();
const bodyParser = require('body-parser');
app.use(bodyParser.json()); 
```

接下来，您需要创建一个新的 Nexmo 客户端，向它提供您保存在`.env`文件中的变量，这些变量`dotenv`将作为`process.env` :
的成员进行访问

```
// create a Nexmo client
const Nexmo = require('nexmo');
const nexmo = new Nexmo({
  apiKey: process.env.API_KEY,
  apiSecret: process.env.API_SECRET,
  applicationId: process.env.APP_ID,
  privateKey: __dirname + process.env.PRIVATE_KEY 
}, {debug: true}); 
```

对于这个简单的例子，我们将只创建获取 JWT 和创建新用户的端点。您现在可以定义端点的签名，我们将在后面的步骤中为这两个应用程序一起提供逻辑。最后，当然，您会希望您的 Express 服务器监听您在 React 的`package.json` :
中指定的端口

```
app.post('/getJWT', function(req, res) {});
app.post('/createUser', function(req, res) {});

app.listen(3001); 
```

## React App 组件

当您运行`create-react-app`时，它应该已经在客户端子目录中的`src/index.js`处为您的应用程序创建了一个入口点。这将加载在`src/App.js`中定义的组件，并将其呈现为您的登录页面的主体。该组件是执行管理任务的理想场所，比如获取 JWT 和登录到您的 Nexmo 应用程序。它也是两个子组件的好容器:`User`和`Conversation`。首先，导入您马上要创建的两个组件:

```
import React from 'react';
import User from './User';
import Conversation from './Conversation';

import nexmoClient from 'nexmo-client';

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {};

    this.login = this.login.bind(this);
    this.getJWT = this.getJWT.bind(this);
    this.userUpdated = this.userUpdated.bind(this);
  }

  login() {}  
  getJWT() {}  
  userUpdated() {}
  render() {}

};

export default App; 
```

在此过程中，您可以看到我们还导入了 Nexmo Client SDK 并清理了创建的类，这样就可以用我们的逻辑进行填充了。我们还为我们需要的功能添加了占位符。

现在您已经有了对两个子组件的引用(尽管我们还没有创建它们)，您可以更新您的呈现函数来将它们加载到您的页面上:

```
render() {
  return (
    <div className="nexmo">
      <User onUpdate={this.userUpdated} />
      <Conversation app={this.state.app} loggedIn={!!this.state.token} />
    </div>
  );
} 
```

### 登录 Nexmo 应用程序

当`User`组件想要报告其状态变化时，它将调用`userUpdated`,因此该函数成为执行链中的第一个环节。您将在收到的状态对象上寻找一个`username`属性，如果它存在，继续为该用户获取一个 JWT:

```
userUpdated(user) {
  if (user.username) {
    this.getJWT(user.username);
  }
} 
```

您的`getJWT`函数将主要由一个`fetch`及其响应的处理组成。您将需要`POST`该函数以 JSON 的形式接收到 Express 服务器的用户名，然后解析数据并将您的新 JWT 保存为状态属性`token`。完成后，您可以调用`login`函数来完成应用程序的初始化:

```
getJWT(username) {
  fetch('/getJWT', {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({name: username})
  })
  .then(results => results.json())
  .then(data => {
    this.setState({
      token: data.jwt
    });
    this.login();
  });
} 
```

### 从服务器获取 JWT

让我们快速跳回到您在`server.js`中的 Express 应用程序，并提供客户端`getJWT`函数将调用的端点。使用 [Nexmo 节点 SDK](https://github.com/Nexmo/nexmo-node) ，我们可以通过再次提供我们的应用程序 ID 来生成一个 JWT；我们从客户端发送的`sub`，或者用户名；以秒为单位的过期时间；以及我们希望这个令牌拥有的[权限](https://developer.nexmo.com/client-sdk/concepts/jwt-acl)。在下面的代码中，用户可以做涉及用户、对话、会话和应用程序的事情，这对于我们非常简单的应用程序来说已经足够了:

```
app.post('/getJWT', function(req, res) {
  const jwt = nexmo.generateJwt({
    application_id: process.env.APP_ID,
    sub: req.body.name,
    exp: Math.round(new Date().getTime()/1000)+3600,
    acl: {
      "paths": {
        "/v1/users/**":{},
        "/v1/conversations/**":{},
        "/v1/sessions/**":{},
        "/v1/applications/**":{}
      }
    }
  });
  res.send({jwt: jwt});
}); 
```

现在，您的服务器正在向 React 应用程序发送令牌，您可以返回到`App.js`并为您的最终函数`login`提供逻辑。真的没什么可做的。使用保存在应用程序组件状态中的新令牌，您可以登录 Nexmo 客户端，并收到对您已登录的 Nexmo 应用程序的引用。您可以将它保存到组件的状态，这样组件就完成了！

```
login() {
  let nexmo = new nexmoClient();
  nexmo.login(this.state.token).then(app => {
    this.setState({
      app: app
    });
  });
} 
```

## React 用户组件

因为您的应用程序组件正在等待用户组件触发登录流，所以现在让我们创建该组件。在与`App.js`同目录的一个新的`User.js`文件中，您可以提供组件的概要:

```
import React from 'react';

class User extends React.Component {
  constructor(props) {
    super(props);
    this.state = {};

    this.createUser = this.createUser.bind(this);
    this.setUsername = this.setUsername.bind(this);
  };

  createUser() {}
  setUsername() {}
  render() {}

};

export default User; 
```

在现实世界的应用程序中，您会希望使用一个系统来管理和存储您的用户，并对他们进行身份验证。然而，对于这个最小的例子，每次访问这个页面时，您都要创建一个新用户。在您的`render`函数中，您可以检查组件的状态是否包含一个`userId`属性。如果是这样，您可以发布一条消息，确认用户已登录。如果没有，您可以显示一个文本字段和按钮来创建新用户:

```
render() {
  if (this.state.userId) {
    return (
      <div className="userinfo userconnected">
        Connected as <span className="username">{this.state.username}</span>
      </div>
    );
  } else {
    return (
      <div className="userinfo">
        <input type="text" onChange={evt => this.setUsername(evt)} />
        <button onClick={this.createUser}>Create user</button>
      </div>
    );
  }
} 
```

### 创建新用户

创建用户实际上是一个由两部分组成的过程，首先是监听文本字段中文本的变化，然后存储更新后的值。如果您想让这个应用程序变得更加健壮，您可以选择首先根据您的用户名规则和现有用户列表检查该值，并通过样式更改向用户发送消息有效性或重复问题。但是对于这个例子，我们只是天真地存储用户输入的任何文本:

```
setUsername(evt) {
  this.setState({
    username: evt.target.value
  });
} 
```

一旦用户点击“创建用户”按钮，您就可以向您的 Express 服务器发出另一个请求。您将发送由`setUsername`保存在状态中的用户名，当服务器响应时，触发由`App`组件提供的`onUpdate`函数，当该组件被实例化时:

```
createUser() {
  fetch('/createUser', {
    method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({name: this.state.username})
  })
  .then(results => results.json())
  .then(data => { 
    this.setState({
      userId: data.id
    }, () => this.props.onUpdate(this.state));
  });
} 
```

`createUser`端点是我们尚未提供的 Express 服务器的最后一部分，所以让我们回到`server.js`并填充该逻辑。您可以在您的 Nexmo 对象上调用`users.create`，从客户端传入用户名和一个可选的显示名称(我们没有在这个应用程序的客户端代码中包含这个名称，但是您可以选择稍后提供)。如果成功，您将把新用户的 ID 传递回客户端:

```
app.post('/createUser', function(req, res) {
  nexmo.users.create({
    name: req.body.name,
    display_name: req.body.display_name || req.body.name
  },(err, response) => {
    if (err) {
      res.sendStatus(500);
    } else {
      res.send({id: response.id});
    }
  });
}); 
```

现在，在 React 和 Express 应用程序中创建用户所需的所有逻辑都可用了，因此 React 应用程序将能够登录并执行创建对话等操作。

## 反应对话成分

您需要创建的最后一个文件是`Conversation.js`，与`App.js`和`User.js`在同一个目录下。组件的轮廓甚至比您已经创建的两个还要小，但是在现实世界的应用程序中，它可能是包含最多逻辑的组件，甚至可能包含几个子组件:

```
import React from 'react';

class Conversation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {};

    this.createConversation = this.createConversation.bind(this);
  }

  createConversation() {}
  render() {}

};

export default Conversation; 
```

这个组件的`render`函数实际上只需要提供一个按钮，但是你可以禁用这个按钮，直到`App`组件通知这个`Conversation`它已经完成了初始化工作。一旦加入了对话，并且在组件状态中存储了对按钮的引用，您就可以隐藏按钮:

```
render() {
  if (this.state.conversation) {
    return (
      <div className="conversation">Joined conversation!</div>
    );
  } else {
    return (
      <div className="conversation">
        <button 
          onClick={this.createConversation} 
          disabled={!this.props.loggedIn}>Start conversation</button>
      </div>
    );
  }
} 
```

`createConversation`函数所依赖的大部分逻辑来自 Nexmo 应用程序和对话对象。当用户单击按钮时，您可以创建一个新的对话，调用传递给该组件的`app`属性。这将返回一个对话，你可以加入，然后保存为一个状态属性:

```
createConversation() {
  this.props.app.newConversation().then(conv => {
    conv.join().then(member => {
      this.setState({
        conversation: conv
      });
    });
  });
} 
```

从这里，您可以邀请其他用户加入对话，开始为对话事件提供处理程序，或者打开一个音频流，让参与者相互交谈。

## 启动应用

您希望能够启动 Express 和 React 应用程序，就像它们是一个应用程序一样，因此您需要做的最后一件事是提供让它们一起启动的机制。您已经编辑了 React 的`package.json`以了解 Express 应用程序；现在是时候给 Express 的`package.json`添加一些脚本了，这样`npm start`就可以真正开始一切了。

在项目根目录下的`package.json`中，添加或修改三个脚本:`start`、`client`和`server` :

```
"scripts":  {  "client":  "cd client && npm start",  "server":  "node server.js",  "start":  "concurrently --kill-others-on-fail \"npm run server\"  \"npm run client\""  }, 
```

您在本教程开始时安装的`concurrently`包将在 Express 服务器导航到客户端目录的同时启动它(注意，如果您称它为其他目录，您必须将“client”改为 React 子目录的名称),并运行由`create-react-app`提供的`start`脚本。如果你现在运行`npm start`，你应该能够在`http://localhost:3000`打开 React 应用的浏览器，看到你的应用正在运行。

想看看这个应用程序稍微复杂一点的版本吗？您可以[查看 Glitch](https://glitch.com/edit/#!/nexmo-express-react) 上的扩展代码，并对其进行重新混合，以进一步试验 Nexmo 对话。
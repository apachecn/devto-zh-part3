# 使用 Django 和 React 的 Python 聊天教程

> 原文：<https://dev.to/nickparsons/tutorial-chat-with-python-django-and-react-1cpk>

本教程将解释如何用 Python、Django 和 React 构建一个聊天应用程序。

与其他教程不同，我没有使用 Python/Django 进行 WebSocket 连接。虽然从技术角度来看，这样做似乎很酷，但它相当缓慢且昂贵——尤其是如果你有相当数量的用户。C++、Go、Elixir 等语言在处理聊天这个核心方面要好得多。

在本教程中，我们将使用 Stream，一个用于 chat 的 [API，它使用 Go、Raft 和 RocksDB 来处理 WebSocket 连接和其他繁重的工作。](https://getstream.io/chat/?ref=dev.to)

**目录**:

1.  [React 聊天演示 UI](#step-1)
2.  [Django/Python 设置](#step-2)
3.  [用户授权](#step-3)
4.  [Django Rest 框架](#step-4)
5.  [生成令牌以访问 Stream 的聊天服务器](#step-5)
6.  [在反应中整合授权](#step-6)
7.  [从 Python 服务器发送消息](#step-7)
8.  [最终想法](#final-thoughts)

> *以下代码的 GitHub repo 可以在[https://github.com/GetStream/python-chat-example](https://github.com/GetStream/python-chat-example)找到。*

我们来编码吧！🤓

## 步骤 1–React 聊天演示界面

在我们开始考虑 Python 聊天方面的事情之前，让我们构建一个简单的 React 前端，这样我们就有了一些好看的视觉效果:

```
$ yarn global add create-react-app
$ brew install node && brew install yarn # skip if installed
$ create-react-app chat-frontend
$ cd chat-frontend
$ yarn add stream-chat-react 
```

Enter fullscreen mode Exit fullscreen mode

将`src/App.js`中的代码替换为:

```
import React from "react";
import {
  Chat,
  Channel,
  ChannelHeader,
  Thread,
  Window
} from "stream-chat-react";
import { MessageList, MessageInput } from "stream-chat-react";
import { StreamChat } from "stream-chat";

import "stream-chat-react/dist/css/index.css";

const chatClient = new StreamChat("qk4nn7rpcn75"); // Demo Stream Key
const userToken =
  "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoiY29vbC1za3ktOSJ9.mhikC6HPqPKoCP4aHHfuH9dFgPQ2Fth5QoRAfolJjC4"; // Demo Stream Token

chatClient.setUser(
  {
    id: "cool-sky-9",
    name: "Cool sky",
    image: "https://getstream.io/random_svg/?id=cool-sky-9&name=Cool+sky"
  },
  userToken
);

const channel = chatClient.channel("messaging", "godevs", {
  // image and name are required, however, you can add custom fields
  image:
    "https://cdn.chrisshort.net/testing-certificate-chains-in-go/GOPHER_MIC_DROP.png",
  name: "Talk about Go"
});

const App = () => (
  <Chat client={chatClient} theme={"messaging light"}>
    <Channel channel={channel}>
      <Window>
        <ChannelHeader />
        <MessageList />
        <MessageInput />
      </Window>
      <Thread />
    </Channel>
  </Chat> );

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，运行`yarn start`来查看聊天的运行情况！

## 第 2 步- Django/Python 设置(如果已经有，跳过)

确保您已经启动并运行了 Python 3.7。

```
$ brew install python3

$ pip install virtualenv virtualenvwrapper
$ export WORKON_HOME=~/Envs
$ source /usr/local/bin/virtualenvwrapper.sh
$ mkvirtualenv chatexample -p `which python3`
$ workon chatexample 
```

Enter fullscreen mode Exit fullscreen mode

如果这不起作用，请尝试下面的代码片段:

```
$ python3 -m venv chatexample
$ source chatexample/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

现在你已经在你的虚拟环境中了，你应该在运行时看到 python 3:

```
$ python --version 
```

Enter fullscreen mode Exit fullscreen mode

要开始一个新的 Django 项目，使用下面的代码片段:

```
$ pip install django
$ django-admin startproject mychat 
```

Enter fullscreen mode Exit fullscreen mode

启动你的应用:

```
$ cd mychat
$ python manage.py runserver 
```

Enter fullscreen mode Exit fullscreen mode

现在，当你打开`http://localhost:8000`，你应该看到这个:

[![Django](img/a35cfa9f02697bf0ba377d88cfb672aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j-tGDVVl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/74B13x3.png)

## 第三步-用户认证

下一步，让我们设置 Django 的用户授权。

```
$ python manage.py migrate
$ python manage.py createsuperuser
$ python manage.py runserver 
```

Enter fullscreen mode Exit fullscreen mode

访问`http://localhost:8000/admin/`并登录。瞧啊。

您应该会看到如下所示的 Django 管理屏幕:

[![Django Admin](img/315726181e07198c2826a46fd43f0e59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3FcXgI4s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/4S1RUlg.png)

## 步骤 4 - Django Rest 框架

我最喜欢的集成 react 和 Django 的包之一是 Django Rest Framework。为了让一切正常工作，我们需要为以下各项创建端点:

*   用户注册
*   用户登录

我们可以自己建造。不过有一个叫 [Djoser](https://github.com/sunscrapers/djoser) 的包已经解决了这个问题。它为用户注册、登录、密码重置等配置了必要的 API 端点。

要安装 Djoser，请使用下面的代码片段:

```
$ pip install djangorestframework djoser 
```

Enter fullscreen mode Exit fullscreen mode

然后，编辑`urls.py`并将文件更改为包含:

```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('auth/', include('djoser.urls')),
    path('auth/', include('djoser.urls.authtoken')),
] 
```

Enter fullscreen mode Exit fullscreen mode

完成后，编辑`settings.py`并进行以下更改:

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'rest_framework.authtoken',
    'djoser',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.TokenAuthentication',
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

关于 Djoser 公开的 API 端点的更多信息，请看这里:
[https://djoser.readthedocs.io/en/latest/sample_usage.html](https://djoser.readthedocs.io/en/latest/sample_usage.html)

现在，让我们继续测试注册端点:

```
$ curl -X POST http://127.0.0.1:8000/auth/users/ --data 'username=djoser&password=alpine12' 
```

Enter fullscreen mode Exit fullscreen mode

## 步骤 5 -生成令牌以访问 Stream 的聊天服务器

现在我们需要定制 Djoser 视图来为 Stream 生成令牌。让我们开始吧。

让我们组织一下我们的文件，在我们的项目中创建一个聊天应用程序文件夹(确保你在正确的目录中):

```
$ python manage.py startapp auth 
```

Enter fullscreen mode Exit fullscreen mode

安装流聊天:

```
$ pip install stream-chat 
```

Enter fullscreen mode Exit fullscreen mode

使用以下逻辑在`auth/serializers.py`中创建一个定制的序列化程序:

```
from djoser.serializers import TokenSerializer
from rest_framework import serializers
from djoser.conf import settings as djoser_settings
from stream_chat import StreamChat
from django.conf import settings

class StreamTokenSerializer(TokenSerializer):
    stream_token = serializers.SerializerMethodField()

    class Meta:
        model = djoser_settings.TOKEN_MODEL
        fields = ('auth_token','stream_token')

    def get_stream_token(self, obj):
        client = StreamChat(api_key=settings.STREAM_API_KEY, api_secret=settings.STREAM_API_SECRET)
        token = client.create_token(obj.user.id)

        return token 
```

Enter fullscreen mode Exit fullscreen mode

最后，通过更新您的`settings.py`文件:
来使用定制序列化程序

```
STREAM_API_KEY = YOUR_STREAM_API_KEY # https://getstream.io/dashboard/ STREAM_API_SECRET = YOUR_STREAM_API_SECRET
DJOSER = {
    'SERIALIZERS': {
        'token': 'auth.serializers.StreamTokenSerializer',
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

重新运行您的迁移:

```
$ python manage.py migrate 
```

Enter fullscreen mode Exit fullscreen mode

为了验证它是否工作，用 POST 请求点击登录端点:

```
$ curl -X POST http://127.0.0.1:8000/auth/token/login/ --data 'username=djoser&password=alpine12' 
```

Enter fullscreen mode Exit fullscreen mode

`auth_token`和`stream_token`都应该被返回。

## 步骤 6 -在 React 中集成 Auth

出于显而易见的原因，稍后向前端添加 auth 是一个必要的步骤。在我们的例子中，它特别有用，因为我们可以从后端 API(由 Python 支持)获取一个用户令牌，并在发送消息时动态地使用它。

首先，为 Django 安装 CORS 中间件包:

```
$ pip install django-cors-headers 
```

Enter fullscreen mode Exit fullscreen mode

然后，修改您的`settings.py`来引用`djors-cors-header`中间件:

```
INSTALLED_APPS = (
    ...
    'corsheaders',
    ...
)

MIDDLEWARE = [
    ...
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    ...
] 
```

Enter fullscreen mode Exit fullscreen mode

最后，将以下内容添加到您的`settings.py`文件中:

```
CORS_ORIGIN_ALLOW_ALL = True 
```

Enter fullscreen mode Exit fullscreen mode

下一步需要对您的前端进行一些修改。首先，您需要确保通过 yarn 安装了所有的依赖项:

```
$ yarn add axios react-dom react-router-dom 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在您的`src/`目录中创建以下文件:

*   authenticate . js
*   un delegate route . js
*   withSession.js
*   Login.js
*   聊天. js

**app . js**T2】

```
import React from "react";
import { BrowserRouter as Router, Switch } from "react-router-dom";

import Chat from "./Chat";
import Login from "./Login";

import UnauthedRoute from "./UnauthedRoute";
import AuthedRoute from "./AuthedRoute";

const App = () => (
  <Router>
    <Switch>
      <UnauthedRoute path="/auth/login" component={Login} />
      <AuthedRoute path="/" component={Chat} />
    </Switch>
  </Router> );

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

**authenticate route . js**

```
import React from "react";
import { Redirect, Route } from "react-router-dom";

const AuthedRoute = ({ component: Component, loading, ...rest }) => {
  const isAuthed = Boolean(localStorage.getItem("token"));
  return (
    <Route
      {...rest}
      render={props =>
        loading ? (
          <p>Loading...</p>
        ) : isAuthed ? (
          <Component history={props.history} {...rest} />
        ) : (
          <Redirect
            to={{
              pathname: "/auth/login",
              state: { next: props.location }
            }}
          />
        )
      }
    />
  );
};

export default AuthedRoute; 
```

Enter fullscreen mode Exit fullscreen mode

**un ded route . js**

```
import React from "react";
import { Redirect, Route } from "react-router-dom";

const AuthedRoute = ({ component: Component, loading, ...rest }) => {
  const isAuthed = Boolean(localStorage.getItem("token"));
  return (
    <Route
      {...rest}
      render={props =>
        loading ? (
          <p>Loading...</p>
        ) : !isAuthed ? (
          <Component history={props.history} {...rest} />
        ) : (
          <Redirect
            to={{
              pathname: "/"
            }}
          />
        )
      }
    />
  );
};

export default AuthedRoute; 
```

Enter fullscreen mode Exit fullscreen mode

**withSession.js**

```
import React from "react";
import { withRouter } from "react-router";

export default (Component, unAuthed = false) => {
  const WithSession = ({ user = {}, streamToken, ...props }) =>
    user.id || unAuthed ? (
      <Component
        userId={user.id}
        user={user}
        session={window.streamSession}
        {...props}
      />
    ) : (
      <Component {...props} />
    );

  return withRouter(WithSession);
}; 
```

Enter fullscreen mode Exit fullscreen mode

**Login.js**

```
import React, { Component } from "react";
import axios from "axios";

class Login extends Component {
  constructor(props) {
    super(props);

    this.state = {
      loading: false,
      email: "",
      password: ""
    };

    this.initStream = this.initStream.bind(this);
  }

  async initStream() {
    await this.setState({
      loading: true
    });

    const base = "http://localhost:8000";

    const formData = new FormData();
    formData.set("username", this.state.email);
    formData.set("password", this.state.password);

    const registration = await axios({
      method: "POST",
      url: `${base}/auth/users/`,
      data: formData,
      config: {
        headers: { "Content-Type": "multipart/form-data" }
      }
    });

    const authorization = await axios({
      method: "POST",
      url: `${base}/auth/token/login/`,
      data: formData,
      config: {
        headers: { "Content-Type": "multipart/form-data" }
      }
    });

    localStorage.setItem("token", authorization.data.stream_token);

    await this.setState({
      loading: false
    });

    this.props.history.push("/");
  }

  handleChange = e => {
    this.setState({
      [e.target.name]: e.target.value
    });
  };

  render() {
    return (
      <div className="login-root">
        <div className="login-card">
          <h4>Login</h4>
          <input
            type="text"
            placeholder="Email"
            name="email"
            onChange={e => this.handleChange(e)}
          />
          <input
            type="password"
            placeholder="Password"
            name="password"
            onChange={e => this.handleChange(e)}
          />
          <button onClick={this.initStream}>Submit</button>
        </div>
      </div>
    );
  }
}

export default Login; 
```

Enter fullscreen mode Exit fullscreen mode

**Chat.js**

```
import React, { Component } from "react";
import {
  Chat,
  Channel,
  ChannelHeader,
  Thread,
  Window
} from "stream-chat-react";
import { MessageList, MessageInput } from "stream-chat-react";
import { StreamChat } from "stream-chat";

import "stream-chat-react/dist/css/index.css";

class App extends Component {
  constructor(props) {
    super(props);
    this.client = new StreamChat("<YOUR_STREAM_APP_ID>");

    this.client.setUser(
      {
        id: "cool-sky-9",
        name: "Cool Sky",
        image: "https://getstream.io/random_svg/?id=cool-sky-9&name=Cool+sky"
      },
      localStorage.getItem("token")
    );

    this.channel = this.client.channel("messaging", "godevs", {
      image:
        "https://cdn.chrisshort.net/testing-certificate-chains-in-go/GOPHER_MIC_DROP.png",
      name: "Talk about Go"
    });
  }

  render() {
    return (
      <Chat client={this.client} theme={"messaging light"}>
        <Channel channel={this.channel}>
          <Window>
            <ChannelHeader />
            <MessageList />
            <MessageInput />
          </Window>
          <Thread />
        </Channel>
      </Chat>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

请务必将`YOUR_STREAM_APP_ID`替换为有效的流应用 id，该 ID 可在[仪表盘](https://getstream.io/dashboard/?ref=dev.to)上找到。

重启你的前端应用程序，你应该会遇到一个授权墙！输入您的电子邮件和密码，将请求一个令牌并存储在本地存储中。

## 步骤 7 -从 Python 聊天服务器发送消息

有时候，您会希望使用您的基于 Python 的后端服务器来编写聊天 API。这里有一个您可以使用的快速管理命令:

在`settings.py` :
中验证已安装的应用程序如下所示

```
INSTALLED_APPS = [
    'corsheaders',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'rest_framework.authtoken',
    'djoser',
] 
```

Enter fullscreen mode Exit fullscreen mode

接下来，创建目录`chat/management/commands`。在该目录中，添加一个名为`broadcast.py`的文件，其内容为:

```
from django.core.management.base import BaseCommand, CommandError
from django.conf import settings
from stream_chat import StreamChat

class Command(BaseCommand):
    help = 'Broadcast the message on your channel'

    def add_arguments(self, parser):
        parser.add_argument('--message')

    def handle(self, *args, **options):
        client = StreamChat(api_key=settings.STREAM_API_KEY, api_secret=settings.STREAM_API_SECRET)
        client.update_user({"id": "system", "name": "The Server"})
        channel = client.channel("messaging", "kung-fu")
        channel.create("system")
        response = channel.send_message({"text": "AMA about kung-fu"}, 'system')
        self.stdout.write(self.style.SUCCESS('Successfully posted a message with id "%s"' % response['message']['id'])) 
```

Enter fullscreen mode Exit fullscreen mode

您可以尝试向聊天室发布这样的消息:

```
$ python manage.py broadcast --message hello 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到这样的响应:

[![Broadcast](img/7cf6af4c4732b42b428d6c324c645dd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bEK7ijqA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/cR7240Q.png)

## 最后的想法

我希望你喜欢这篇用 Django、Python 和 React 构建聊天应用程序的教程！

关于[流聊天](https://getstream.io/chat/?ref=dev.to)的互动之旅，请看看我们在流网站上的 [API 教程](https://getstream.io/chat/get_started/?ref=dev.to)。如果你有兴趣深入研究流聊天反应组件的代码，[完整的文档可以在这里找到](https://getstream.github.io/stream-chat-react/)。如果您对在 Stream 上构建聊天感兴趣，我们为流行的语言和框架提供了各种[SDK](https://github.com/GetStream/stream-sdks)，我们最新的是 [iOS (Swift)](https://getstream.io/tutorials/ios-chat/) 。

编码快乐！✌
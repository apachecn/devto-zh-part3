# 用 Python、Flask 和 React 构建一个简单的 CRUD 应用程序

> 原文：<https://dev.to/oktadev/build-a-simple-crud-app-with-python-flask-and-react-30k5>

当今的现代 web 应用程序通常使用服务器端语言构建，通过 API 和前端 javascript 框架提供数据，以易于使用的方式将数据呈现给最终用户。Python 是一种被公司和开发者广泛采用的动态语言。这种语言的核心价值观是，软件应该简单、易读，让开发人员更高效、更快乐。您还将使用 Flask 来帮助您快速组装 ReST API。React 是一个声明式的、高效的、灵活的 JavaScript 库，由脸书开发，用于构建用户界面。它有助于从称为组件的小而孤立的代码片段中创建复杂的、交互式的和有状态的 ui。

在本教程中，您将在前端使用 React 构建一个 JavaScript 应用程序，我们还将构建一个用 Python 编写的 ReST API，该 API 将保持不变。我们的应用程序将是一个 Github 开源书签项目(又名`kudo`)。

要完成本教程，您需要几样东西:

*   macOS 或 Linux 操作系统
*   Python 3 已安装
*   安装了 MongoDB 或 Docker 工具箱
*   [一个永远免费的 Okta 账户](https://developer.okta.com/signup/)

您将从创建后端开始。

## 用 Python 创建 ReST API

确保您安装了 Python 3。通过运行以下命令检查安装的 Python 版本:

```
python --version 
```

要安装 Python 3，您可以使用`pyenv`。

如果你用的是 macOS，可以用 Homebrew 安装:

```
brew update
brew install pyenv 
```

在使用 bash shell 的 Linux 系统上:

```
curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash 
```

安装完成后，您可以运行以下命令来安装 Python 3。

```
pyenv install 3.6.3
pyenv global 3.6.3 
```

您的 ReST API 将使用一些第三方代码(库)来帮助您(例如，连接到数据库，为您的模型创建模式，并验证传入的请求是否经过身份验证)。Python 有一个强大的工具来管理依赖关系，叫做`pipenv`。按照以下步骤在机器上安装`pipenv`:

在 macOS 上:

```
brew install pipenv 
```

```
pip install --user pipenv 
```

安装了`pipenv`之后，为您的后端代码创建一个目录:

```
mkdir kudos_oss && cd kudos_oss 
```

上面的命令将创建一个 Python 3 虚拟环境。现在你可以通过运行下面的命令来安装 Flask:

```
pipenv install flask==1.0.2 
```

Python 3 提供了一些很酷的特性，比如`absolute_import`和`print_function`，您将在本教程中用到。要导入它们，运行以下命令:

```
touch __init__.py
touch __main__.py 
```

并将以下内容复制粘贴到`__main__.py`文件中:

```
from __future__ import absolute_import, print_function 
```

您的后端将需要实现以下用户故事:

*   作为一个认证用户，我想喜欢一个 github 开源项目。
*   作为一个认证用户，我不喜欢 github 开源项目。
*   作为一名认证用户，我想列出我以前喜欢的所有书签 github 开源项目。

一个正常的 ReST API 会公开端点，这样客户端就可以使用`create`、`update`、`delete`、`read`和`list all`资源。在本节结束时，您的后端应用程序将能够处理以下 HTTP 调用:

```
# For the authenticated user, fetches all favorited github open source projects
GET /kudos

# Favorite a github open source project for the authenticated user
POST /kudos

# Unfavorite a favorited github open source project
DELETE /kudos/:id 
```

### 定义 Python 模型模式

您的 ReST API 将有两个核心模式，它们是`GithubRepoSchema`和`KudoSchema`。`GithubRepoSchema`将代表客户端发送的 Github 存储库，而`KudoSchema`将代表您将在数据库中持久存储的数据。

继续运行以下命令:

```
mkdir -p app/kudo
touch app/kudo/schema.py
touch app/kudo/service.py
touch app/kudo/ __init__.py 
```

以上命令将创建`app`目录，其中包含另一个名为`kudo`的目录。然后，第二个命令将创建三个文件:`schema.py`、`service.py`和`__init__.py`。

将以下内容复制并粘贴到`schema.py`文件中:

```
from marshmallow import Schema, fields

class GithubRepoSchema(Schema):
  id = fields.Int(required=True)
  repo_name = fields.Str()
  full_name = fields.Str()
  language = fields.Str()
  description = fields.Str()
  repo_url = fields.URL()

class KudoSchema(GithubRepoSchema):
  user_id = fields.Email(required=True) 
```

正如你可能已经注意到的，模式从`Schema`继承了[棉花糖库](https://marshmallow.readthedocs.io/en/3.0/)的一个包。Marshmallow 是一个 ORM/ODM/framework 无关的库，用于将复杂的数据类型(如对象)序列化/反序列化为本机 Python 数据类型。

安装运行以下命令的`marshmallow`库:

```
pipenv install marshmallow==2.16.3 
```

### Python ReST API 持久性与 MongoDB

太好了！现在你已经有了第一批文件。创建模式是为了表示传入的请求数据以及应用程序在 MongoDB 中持久存储的数据。为了连接和执行对数据库的查询，您将使用一个由 MongoDB 自己创建和维护的名为 [pymongo](https://api.mongodb.com/python/current/) 的库。

安装运行以下命令的`pymongo`库:

```
pipenv install pymongo==3.7.2 
```

您可以使用机器上安装的 MongoDB，也可以使用 Docker 来构建 MongoDB 容器。本教程假设您已经安装了 Docker 和 docker-compose。

将为您管理 MongoDB 容器。

创建`docker-compose.yml` :

```
touch docker-compose.yml 
```

将以下内容粘贴到其中:

```
version: '3'
services:
  mongo:
    image: mongo
    restart: always
    ports:
     - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: mongo_user
      MONGO_INITDB_ROOT_PASSWORD: mongo_secret 
```

现在，要启动 MongoDB 容器，您需要做的就是:

```
docker-compose up 
```

随着 MongoDB 的启动和运行，您已经准备好处理`MongoRepository`类了。让类只承担一个责任总是一个好主意，所以在后端应用程序 MongoDB 中唯一需要明确处理的点是在`MongoRepository`中。

首先创建一个目录，存放所有与持久性相关的文件，建议是:`repository`。

```
mkdir -p app/repository 
```

然后，创建包含 MongoRepository 类的文件:

```
touch app/repository/mongo.py
touch app/repository/ __init__.py 
```

正确安装了`pymongo`并启动和运行 MongoDB 后，将以下内容粘贴到`app/repository/mongo.py`文件中。

```
import os
from pymongo import MongoClient

COLLECTION_NAME = 'kudos'

class MongoRepository(object):
 def __init__ (self):
   mongo_url = os.environ.get('MONGO_URL')
   self.db = MongoClient(mongo_url).kudos

 def find_all(self, selector):
   return self.db.kudos.find(selector)

 def find(self, selector):
   return self.db.kudos.find_one(selector)

 def create(self, kudo):
   return self.db.kudos.insert_one(kudo)

 def update(self, selector, kudo):
   return self.db.kudos.replace_one(selector, kudo).modified_count

 def delete(self, selector):
   return self.db.kudos.delete_one(selector).deleted_count 
```

如您所见，`MongoRepository`类非常简单，它在初始化时创建一个数据库连接，然后将其保存到一个实例变量中，供以下方法使用:`find_all`、`find`、`create`、`update`和`delete`。注意，所有方法都显式使用 pymongo API。

您可能已经注意到，`MongoRepository`类读取了一个环境变量`MONGO_URL`。要导出环境变量，运行:

```
export MONGO_URL=mongodb://mongo_user:mongo_secret@0.0.0.0:27017/ 
```

由于将来您可能想要使用其他数据库，因此将您的应用程序从 MongoDB 中分离出来是一个好主意。为了简单起见，您将创建一个抽象类来表示一个`Repository`；这个类应该是在整个应用程序中使用的类。

将以下内容粘贴到`app/repository/ __init__.py`文件中:

```
class Repository(object):
 def __init__ (self, adapter=None):
   self.client = adapter()

 def find_all(self, selector):
   return self.client.find_all(selector)

 def find(self, selector):
   return self.client.find(selector)

 def create(self, kudo):
   return self.client.create(kudo)

 def update(self, selector, kudo):
   return self.client.update(selector, kudo)

 def delete(self, selector):
   return self.client.delete(selector) 
```

您可能还记得您正在研究的用户故事，即一个经过认证的用户应该能够创建、删除和列出所有喜爱的 Github 开源项目。为了做到这一点，那些`MongoRepository`的方法将会派上用场。

您将很快实现 ReST API 的端点。首先，您需要创建一个服务类，它知道如何将传入的请求有效负载转换为在`app/kudo/schema.py`中定义的表示`KudoSchema`。传入请求有效负载(由`GithubSchema`表示)和持久存储在数据库中的对象(由`KudoSchema`表示)之间的区别在于:第一个有一个`user_Id`，它决定谁拥有该对象。

将以下内容复制到`app/kudo/service.py`文件:

```
from ..repository import Repository
from ..repository.mongo import MongoRepository
from .schema import KudoSchema

class Service(object):
 def __init__ (self, user_id, repo_client=Repository(adapter=MongoRepository)):
   self.repo_client = repo_client
   self.user_id = user_id

   if not user_id:
     raise Exception("user id not provided")

 def find_all_kudos(self):
   kudos = self.repo_client.find_all({'user_id': self.user_id})
   return [self.dump(kudo) for kudo in kudos]

 def find_kudo(self, repo_id):
   kudo = self.repo_client.find({'user_id': self.user_id, 'repo_id': repo_id})
   return self.dump(kudo)

 def create_kudo_for(self, githubRepo):
   self.repo_client.create(self.prepare_kudo(githubRepo))
   return self.dump(githubRepo.data)

 def update_kudo_with(self, repo_id, githubRepo):
   records_affected = self.repo_client.update({'user_id': self.user_id, 'repo_id': repo_id}, self.prepare_kudo(githubRepo))
   return records_affected > 0

 def delete_kudo_for(self, repo_id):
   records_affected = self.repo_client.delete({'user_id': self.user_id, 'repo_id': repo_id})
   return records_affected > 0

 def dump(self, data):
   return KudoSchema(exclude=['_id']).dump(data).data

 def prepare_kudo(self, githubRepo):
   data = githubRepo.data
   data['user_id'] = self.user_id
   return data 
```

请注意，您的构造函数`__init__`接收参数`user_id`和`repo_client`，它们用于该服务的所有操作中。这就是用一个类来表示存储库的好处。就服务而言，它并不关心`repo_client`是否将数据保存在 MongoDB、PostgreSQL 中，或者通过网络将数据发送给第三方服务 API，它所需要知道的是`repo_client`是一个`Repository`实例，它配置了一个适配器来实现像`create`、`delete`和`find_all`这样的方法。

### 定义你的 ReST API 中间件

此时，您已经完成了 70%的后端。您已经准备好实现 HTTP 端点和 JWT 中间件，它们将保护您的 ReST API 免受未经身份验证的请求。

您可以从创建一个放置 HTTP 相关文件的目录开始。

```
mkdir -p app/http/api 
```

在这个目录中，您将有两个文件，`endpoints.py`和`middlewares.py`。要创建它们，运行以下命令:

```
touch app/http/api/ __init__.py
touch app/http/api/endpoints.py
touch app/http/api/middlewares.py 
```

对 ReST API 的请求是 JWT 认证的，这意味着您需要确保每个请求都携带有效的 [json web 令牌](https://stormpath.com/blog/beginners-guide-jwts-in-java/)。 [`pyjwt`](https://pyjwt.readthedocs.io/en/latest/) 会替我们照顾验证。要安装它，运行以下命令:

```
pipenv install pyjwt==1.7.1 
```

既然您已经理解了 JWT 中间件的角色，那么您需要编写它。将以下内容粘贴到`middlewares.py`文件中。

```
from functools import wraps
from flask import request, g, abort
from jwt import decode, exceptions
import json

def login_required(f):
   @wraps(f)
   def wrap(*args, **kwargs):
       authorization = request.headers.get("authorization", None)
       if not authorization:
           return json.dumps({'error': 'no authorization token provied'}), 403, {'Content-type': 'application/json'}

       try:
           token = authorization.split(' ')[1]
           resp = decode(token, None, verify=False, algorithms=['HS256'])
           g.user = resp['sub']
       except exceptions.DecodeError as identifier:
           return json.dumps({'error': 'invalid authorization token'}), 403, {'Content-type': 'application/json'}

       return f(*args, **kwargs)

   return wrap 
```

Flask 提供了一个名为`g`的模块，这是一个跨请求生命周期共享的全局上下文。这个中间件正在检查请求是否有效。如果是这样，中间件将提取经过身份验证的用户详细信息，并将它们保存在全局上下文中。

### 定义你的 ReST API 端点

HTTP 处理程序现在应该很容易了，因为您已经完成了重要的部分，现在只需要把所有的东西放在一起。

因为你的最终目标是创建一个可以在网络浏览器上运行的 JavaScript 应用程序，你需要确保网络浏览器在执行预检时感到满意，你可以在[这里](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)了解更多信息。为了实现 our your ReST API，您将安装`flask_cors`。

```
pipenv install flask_cors==3.0.7 
```

接下来，实现您的端点。继续将上面的内容粘贴到`app/http/api/endpoints.py`文件中。

```
from .middlewares import login_required
from flask import Flask, json, g, request
from app.kudo.service import Service as Kudo
from app.kudo.schema import GithubRepoSchema
from flask_cors import CORS

app = Flask( __name__ )
CORS(app)

@app.route("/kudos", methods=["GET"])
@login_required
def index():
 return json_response(Kudo(g.user).find_all_kudos())

@app.route("/kudos", methods=["POST"])
@login_required
def create():
   github_repo = GithubRepoSchema().load(json.loads(request.data))

   if github_repo.errors:
     return json_response({'error': github_repo.errors}, 422)

   kudo = Kudo(g.user).create_kudo_for(github_repo)
   return json_response(kudo)

@app.route("/kudo/<int:repo_id>", methods=["GET"])
@login_required
def show(repo_id):
 kudo = Kudo(g.user).find_kudo(repo_id)

 if kudo:
   return json_response(kudo)
 else:
   return json_response({'error': 'kudo not found'}, 404)

@app.route("/kudo/<int:repo_id>", methods=["PUT"])
@login_required
def update(repo_id):
   github_repo = GithubRepoSchema().load(json.loads(request.data))

   if github_repo.errors:
     return json_response({'error': github_repo.errors}, 422)

   kudo_service = Kudo(g.user)
   if kudo_service.update_kudo_with(repo_id, github_repo):
     return json_response(github_repo.data)
   else:
     return json_response({'error': 'kudo not found'}, 404)

@app.route("/kudo/<int:repo_id>", methods=["DELETE"])
@login_required
def delete(repo_id):
 kudo_service = Kudo(g.user)
 if kudo_service.delete_kudo_for(repo_id):
   return json_response({})
 else:
   return json_response({'error': 'kudo not found'}, 404)

def json_response(payload, status=200):
 return (json.dumps(payload), status, {'content-type': 'application/json'}) 
```

太棒了。现在都到位了！您应该能够使用下面的命令运行 ReST API:

```
FLASK_APP=$PWD/app/http/api/endpoints.py FLASK_ENV=development pipenv run python -m flask run --port 4433 
```

## 创建 React 客户端 App

为了创建你的 React 客户端应用程序，你将使用脸书令人敬畏的`create-react-app`工具来绕过所有的 webpack 麻烦。

安装`create-react-app`很简单。在本教程中，您将使用`yarn`。确保您已经安装了它，或者使用您喜欢的依赖管理器。

要安装`create-react-app`，运行命令:

```
yarn global add create-react-app 
```

您需要一个目录来放置 React 应用程序，然后在`pkg/http`文件夹中创建`web`目录。

```
mkdir -p app/http/web 
```

现在，创建一个 React 应用程序:

```
cd app/http/web
create-react-app app 
```

生成样板应用程序可能需要几分钟时间。转到最近创建的`app`目录并运行`npm start`

默认情况下，`create-react-app`生成的 React app 会在端口 3000 上运行监听。我们换成监听端口 8080 吧。

更改文件`app/http/web/app/package.json`上的`start`命令以使用正确的端口。

[![start script](img/f2e0aef0219803b5e621ee35a67bf992.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HojZajLZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-react/start-script-c44e8bff20e5f814bf0c69b39ddc1dd7cd3a8762a9bfd40b3fbdcb4c9f3e6704.png)

然后，运行 React 应用程序。

```
cd app
npm start 
```

运行`npm start`将启动一个监听端口 8080 的 web 服务器。在浏览器中打开`http://localhost:8080/`。您的浏览器应该加载 React 并呈现由`create-react-app`自动创建的 App.js 组件。

[![react app first run](img/2bad1fc3b57e53ef3e5e6023a64094c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--11HIk3iF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-react/react-app-first-run-6a0bf410b7fd1894d9c113f5455a077514a5ebc14a2f36bd23faa725a3ebee76.png)

你现在的目标是用[材质设计](https://material.io/design/)打造一个简洁美观的 UI。令人欣慰的是，React 社区已经创建了 https://material-ui.com/的[，它们基本上是转化为 React 组件的材料设计概念。](https://material-ui.com/)

运行以下命令来安装您需要的材料设计。

```
yarn add @material-ui/core
yarn add @material-ui/icons 
```

很好，现在你已经有了像网格、卡片、图标、应用程序栏和更多可以导入和使用的组件。你很快就会用到它们。我们来谈谈受保护的路线。

### 使用 Okta 为您的 React 应用程序添加认证

编写安全的用户认证和构建登录页面很容易出错，可能会导致新项目的失败。Okta 让所有用户管理功能的快速安全实施变得简单。首先注册一个[免费开发者账户](https://developer.okta.com/signup/)，并在 Okta 创建一个 OpenID Connect 应用程序。

[![okta signup](img/547f65b8d78adbf842c1f36fa4746394.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xvCWzIJT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-react/okta-developer-signup-5ca629cb8a0b5bf45298190ebff6e09a89b553be0b5042a9e8e559dd0440eb39.png)

登录后，点击**添加应用**创建一个新的应用。

[![add application](img/7b5ed3dbd98241903bab956b6dc828b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cw3sd5OS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-react/okta-add-application-024fdfa427033e2e345b48167d8fdef2592f8dcaa464be89487e257a629d39ad.png)

选择**单页 App** 平台选项。

[![select spa app](img/f53886ce56a119384b64c8ce9daf2543.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--acaMSXht--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-react/okta-choose-spa-96b1b0faa43717d47faaf99696fe2155e793c5f482b7cbae21747bdf3fd72ba4.png)

默认的应用程序设置应该和图中的一样。

[![okta spa settings](img/fbd6f6272acf23973db32ec24f493652.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XN9b0TlD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-react/okta-spa-settings-99892ff83b5e572a4c2b64d3d9b2edde2266d7f7434c18b8e60f6f005d7718e0.png)

太好了！准备好 OIDC 应用程序后，您现在可以继续前进，保护需要身份验证的路由。

### 创建您的 React 路线

[React Router](https://reacttraining.com/react-router/) 是将 URL 路由到 React 组件时使用最多的库。React Router 有一组组件，可以用来帮助用户在应用程序中导航。

您的 React 应用程序将有两条路线:

根路由不需要用户登录，它实际上是你的应用程序的登陆页面。用户应该能够访问该页面以便登录。您将使用 [Okta React SDK](https://developer.okta.com/code/react/) 来集成 react-router 和 Okta 的 OpenID Connect API。

Home 路径将呈现您的应用程序将拥有的大多数 React 组件。它应该实现以下用户故事。

经过认证的用户应该能够搜索 Github API，这是他/她喜欢的开源项目。经过身份验证的用户应该能够为自己喜欢的开源项目添加书签。经过身份验证的用户应该能够在不同的选项卡中看到他/她以前收藏的开源项目和搜索结果。

要安装`react-router`，请运行命令:

```
yarn add react-router-dom 
```

并且要安装 Okta React SDK 运行命令:

```
yarn add @okta/okta-react 
```

现在，开始创建你的主要组件:

```
mkdir -p src/Main 
```

然后，在主目录中创建一个名为`index.js` :
的文件

```
touch src/Main/index.js 
```

并将以下内容粘贴到最近创建的文件中:

```
import React, { Component } from 'react';
import { Switch, Route, BrowserRouter as Router } from 'react-router-dom'
import { Security, ImplicitCallback, SecureRoute } from '@okta/okta-react';

import Login from '../Login'
import Home from '../Home'

class Main extends Component {
 render() {
   return (
     <Router>
       <Security
         issuer={yourOktaDomain}
         client_id={yourClientId}
         redirect_uri={'http://localhost:8080/implicit/callback'}
         scope={['openid', 'profile', 'email']}>

         <Switch>
           <Route exact path="/" component={Login} />
           <Route path="/implicit/callback" component={ImplicitCallback} />
           <SecureRoute path="/home" component={Home} />
         </Switch>
       </Security>
     </Router>
   );
 }
}

export default Main; 
```

现在不要担心`Home`和`Login`组件。你很快就会着手做了。重点关注`Security`、`SecureRoute`和`ImplicitCallback`组件。

为了让路由在 React 中正常工作，您需要将整个应用程序包装在一个路由器中。类似地，要允许在应用程序中的任何地方访问身份验证，您需要将应用程序包装在 Okta 提供的`Security`组件中。Okta 也需要访问路由器，所以`Security`组件应该嵌套在路由器内部。

对于需要认证的路由，您将使用`SecureRoute` Okta 组件来定义它们。如果一个未经认证的用户试图访问`/home`，他/她将被重定向到`/`根路由。

`ImplicitCallback`组件是 Okta 完成登录过程后用户将被重定向到的路线/URI 目的地。

继续并改变`src/index.js`来安装你的主要组件。

```
import React from 'react';
import ReactDOM from 'react-dom';
import { Router } from 'react-router-dom'
import { createBrowserHistory } from 'history'

import Main from './Main';

const history = createBrowserHistory();

ReactDOM.render((
  <Router history={history}>
    <Main history={history} />
  </Router> ), document.getElementById('root')) 
```

您现在已经准备好创建登录组件了。如前所述，所有用户(不仅仅是经过身份验证的用户)都可以访问这个组件。登录组件的主要目标是对用户进行身份验证。

在目录`app`中，您会发现一个名为`src`的目录，它代表源代码。继续创建一个名为 Login 的目录。

```
mkdir -p src/Login 
```

然后，在登录目录中创建一个名为`index.js`的文件。

```
touch src/Login/index.js 
```

并将以下内容粘贴到文件中:

```
import React from 'react'
import Button from '@material-ui/core/Button';
import { Redirect } from 'react-router-dom'
import { withAuth } from '@okta/okta-react';

class Login extends React.Component {
 constructor(props) {
   super(props);
   this.state = { authenticated: null };
   this.checkAuthentication = this.checkAuthentication.bind(this);
   this.login = this.login.bind(this);
 }

 async checkAuthentication() {
   const authenticated = await this.props.auth.isAuthenticated();
   if (authenticated !== this.state.authenticated) {
     this.setState({ authenticated });
   }
 }

 async componentDidMount() {
   this.checkAuthentication()
 }

 async login(e) {
   this.props.auth.login('/home');
 }

 render() {
   if (this.state.authenticated) {
     return <Redirect to='/home' />
   } else {
     return (
       <div style={{height: '100vh', display: 'flex', alignItems: 'center', justifyContent: 'center'}}>
         <Button variant="contained" color="primary" onClick={this.login}>Login with Okta</Button>
       </div>
     )
   }
 }
}

export default withAuth(Login); 
```

为了看到登录页面工作，您需要为 Home 组件创建一个占位符。

继续创建一个名为`Home` :
的目录

```
mkdir -p src/Home 
```

然后，在该目录中，创建一个名为`index.js` :
的文件

```
touch src/Home/index.js 
```

并将以下内容粘贴到其中:

```
import React from 'react'

const home = (props) => {
  return (
    <div>Home</div>
  )
};

export default home; 
```

现在尝试运行`npm start`并在浏览器中打开`http://localhost:8080`。您应该会看到下面的页面。

[![login button](img/91565931eef9051cea8f93fe793b95b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hnq654by--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-react/app-login-button-40782929265b11b95fb375defb3a52c3b6d379ee52c180a0cfb3918d2fc34dc6.png)

在登录组件中，您使用 Okta React SDK 来检查用户是否已经登录。如果用户已经登录，他们应该被重定向到`/home`路线，否则他/她可以点击`Login With Okta`被重定向到 Okta，验证并被发送到主页。

[![login page](img/746f7d6c34fd5212eb8ff193e98d165c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fY17-Cql--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-react/app-login-0a7959211c7cf87c6831e805465116e67c19bc1f285f6d217b230c2d29190614.png)

目前，主页是空白的，但最终您会希望主页看起来是这样的:

[![home page](img/c79bcdca3077a05f52c39e256dee97ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---xw9nC5R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-react/app-home-page-424e1d03eb87ee19319ab76a89eb92edd4397af6234e1855a87f06bfe337ad8f.png)

主页组件由材料设计组件组成，如:`Tab`、`AppBar`、`Button`和`Icon`，以及一些您必须创建的定制组件。

对于您的应用程序，您需要列出所有书签开源项目以及搜索结果。正如您在上面的图片中所看到的，Home 组件使用标签将加书签的开源项目从搜索结果中分离出来。第一个选项卡列出了用户收藏的所有开源项目，而第二个选项卡将列出搜索结果。

你可以创建一个组件在“Kudos”和“Search Results”列表中表示一个开源项目，这就是 React 组件的美妙之处，它们高度灵活和可重用。

继续创建一个名为`GithubRepo` :
的目录

```
mkdir -p src/GithubRepo 
```

然后，在该目录中，创建一个名为`index.js` :
的文件

```
touch src/GithubRepo/index.js 
```

并将以下内容粘贴到其中:

```
import React from 'react';
import PropTypes from 'prop-types';
import { withStyles } from '@material-ui/core/styles';
import Card from '@material-ui/core/Card';
import CardHeader from '@material-ui/core/CardHeader';
import CardContent from '@material-ui/core/CardContent';
import CardActions from '@material-ui/core/CardActions';
import IconButton from '@material-ui/core/IconButton';
import Typography from '@material-ui/core/Typography';
import FavoriteIcon from '@material-ui/icons/Favorite';

const styles = theme => ({
  card: {
    maxWidth: 400,
  },
  media: {
    height: 0,
    paddingTop: '56.25%', // 16:9
  },
  actions: {
    display: 'flex',
  }
});

class GithubRepo extends React.Component {
  handleClick = (event) => {
    this.props.onKudo(this.props.repo)
  }

  render() {
    const { classes } = this.props;

    return (
      <Card className={classes.card}>
        <CardHeader
          title={this.props.repo.full_name}
        />
        <CardContent>
          <Typography component="p" style={{minHeight: '90px', overflow: 'scroll'}}>
            {this.props.repo.description}
          </Typography>
        </CardContent>
        <CardActions className={classes.actions} disableActionSpacing>
          <IconButton aria-label="Add to favorites" onClick={this.handleClick}>
            <FavoriteIcon color={this.props.isKudo ? "secondary" : "primary"} />
          </IconButton>
        </CardActions>
      </Card>
    );
  }
}

export default withStyles(styles)(GithubRepo); 
```

`GithubRepo`是一个非常简单的组件，它接收两个`props`:一个保存对 Github 库的引用的`repo`对象和一个指示`repo`是否被书签标记的`isKudo`布尔标志。

您需要的下一个组件是`SearchBar`。它将有两个责任:注销用户，并在搜索文本字段中每次按下`Enter`键时调用 React。

创建名为`SearchBar` :
的目录

```
mkdir -p src/SearchBar 
```

然后，在目录中创建一个名为`index.js` :
的文件

```
touch src/SearchBar/index.js 
```

粘贴以下内容:

```
import React from 'react';
import PropTypes from 'prop-types';
import AppBar from '@material-ui/core/AppBar';
import Toolbar from '@material-ui/core/Toolbar';
import InputBase from '@material-ui/core/InputBase';
import Button from '@material-ui/core/Button';
import { fade } from '@material-ui/core/styles/colorManipulator';
import { withStyles } from '@material-ui/core/styles';
import SearchIcon from '@material-ui/icons/Search';
import { withAuth } from '@okta/okta-react';

const styles = theme => ({
  root: {
    width: '100%',
  },
  MuiAppBar: {
    alignItems: 'center'
  },
  grow: {
    flexGrow: 1,
  },
  title: {
    display: 'none',
    [theme.breakpoints.up('sm')]: {
      display: 'block',
    },
  },
  search: {
    position: 'relative',
    borderRadius: theme.shape.borderRadius,
    backgroundColor: fade(theme.palette.common.white, 0.15),
    '&:hover': {
      backgroundColor: fade(theme.palette.common.white, 0.25),
    },
    marginRight: theme.spacing.unit * 2,
    marginLeft: 0,
    width: '100%',
    [theme.breakpoints.up('sm')]: {
      marginLeft: theme.spacing.unit * 3,
      width: 'auto',
    },
  },
  searchIcon: {
    width: theme.spacing.unit * 9,
    height: '100%',
    position: 'absolute',
    pointerEvents: 'none',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'center',
  },
  inputRoot: {
    color: 'inherit',
    width: '100%',
  },
  inputInput: {
    paddingTop: theme.spacing.unit,
    paddingRight: theme.spacing.unit,
    paddingBottom: theme.spacing.unit,
    paddingLeft: theme.spacing.unit * 10,
    transition: theme.transitions.create('width'),
    width: '100%',
    [theme.breakpoints.up('md')]: {
      width: 400,
    },
  },
  toolbar: {
    alignItems: 'center'
  }
});

class SearchBar extends React.Component {
  constructor(props) {
    super(props);
    this.logout = this.logout.bind(this);
  }

  async logout(e) {
    e.preventDefault();
    this.props.auth.logout('/');
  }

  render() {
    const { classes } = this.props;

    return (
      <div className={classes.root}>
        <AppBar position="static" style={{alignItems: 'center'}}>
          <Toolbar>
            <div className={classes.search}>
              <div className={classes.searchIcon}>
                <SearchIcon />
              </div>
              <InputBase
                placeholder="Search for your OOS project on Github + Press Enter"
                onKeyPress={this.props.onSearch}
                classes={{
                  root: classes.inputRoot,
                  input: classes.inputInput,
                }}
              />
            </div>
            <div className={classes.grow} />
            <Button onClick={this.logout} color="inherit">Logout</Button>
          </Toolbar>
        </AppBar>
      </div>
    );
  }
}

SearchBar.propTypes = {
  classes: PropTypes.object.isRequired,
};

export default withStyles(styles)(withAuth(SearchBar)); 
```

`SearchBar`组件接收一个名为`onSearch`的`prop`，它是在搜索文本输入中触发的每个`keyPress`事件中应该调用的函数。

`SearchBar`使用 Okta React SDK 提供的`withAuth`助手，该助手将在组件的`props`中注入`auth`对象。`auth`对象有一个名为`logout`的方法，它将从会话中清除所有与用户相关的数据。这正是您想要注销用户的原因。

现在是时候处理`Home`组件了。该组件的一个依赖项是 [`react-swipeable-views`](https://github.com/oliviertassinari/react-swipeable-views) 库，当用户改变标签时，它会添加漂亮的动画。

要安装 react-swipeable-views，运行命令:

```
yarn add react-swipeable-views 
```

您还需要对 Python ReST API 和 Github ReST API 进行 HTTP 调用。Github HTTP 客户端需要一个方法或函数来请求这个 URL: `https://api.github.com/search/repositories?q=USER-QUERY`。您将使用`q`查询字符串来传递用户想要查询 Github 存储库的术语。

创建一个名为`githubClient.js`的文件。

```
touch src/githubClient.js 
```

在里面粘贴以下内容:

```
export default {
 getJSONRepos(query) {
   return fetch('https://api.github.com/search/repositories?q=' + query).then(response => response.json());
 }
} 
```

现在，您需要创建一个 HTTP 客户端来对您在本教程的第一部分中实现的 Python ReST API 进行 HTTP 调用。因为对 Python ReST API 的所有请求都需要对用户进行身份验证，所以需要用 Okta 提供的`accessToken`来设置`Authorization` HTTP 头。

继续创建一个名为`apiClient.js`的文件。

```
touch src/apiClient.js 
```

并且安装 [`axios`](https://github.com/axios/axios) 来帮助你执行对你的 flask API 的 HTTP 调用。

```
yarn add axios 
```

然后，粘贴以下内容:

```
import axios from 'axios';

const BASE_URI = 'http://localhost:4433';

const client = axios.create({
 baseURL: BASE_URI,
 json: true
});

class APIClient {
 constructor(accessToken) {
   this.accessToken = accessToken;
 }

 createKudo(repo) {
   return this.perform('post', '/kudos', repo);
 }

 deleteKudo(repo) {
   return this.perform('delete', `/kudos/${repo.id}`);
 }

 getKudos() {
   return this.perform('get', '/kudos');
 }

 async perform (method, resource, data) {
   return client({
     method,
     url: resource,
     data,
     headers: {
       Authorization: `Bearer ${this.accessToken}`
     }
   }).then(resp => {
     return resp.data ? resp.data : [];
   })
 }
}

export default APIClient; 
```

太好了！您的`APIClient`的方法`perform`正在将用户的`accessToken`添加到每个请求的`Authorization` HTTP 头中，这意味着它正在验证每个请求。当服务器接收到这些 HTTP 请求时，Okta 中间件将能够验证令牌，并从中提取用户详细信息。

通常，你可以创建单独的组件来获取用户的书签和搜索 github repos。为了简单起见，您将把它们都放在`HomeComponent`中。

将以下内容粘贴到`src/Home/index.js`文件中。

```
import React from 'react';
import { withStyles } from '@material-ui/core/styles';
import SwipeableViews from 'react-swipeable-views';
import Tabs from '@material-ui/core/Tabs';
import Tab from '@material-ui/core/Tab';
import Grid from '@material-ui/core/Grid';
import { withAuth } from '@okta/okta-react';

import GithubRepo from "../GithubRepo"
import SearchBar from "../SearchBar"

import githubClient from '../githubClient'
import APIClient from '../apiClient'

const styles = theme => ({
 root: {
   flexGrow: 1,
   marginTop: 30
 },
 paper: {
   padding: theme.spacing.unit * 2,
   textAlign: 'center',
   color: theme.palette.text.secondary,
 },
});

class Home extends React.Component {
 state = {
   value: 0,
   repos: [],
   kudos: []
 };

 async componentDidMount() {
   const accessToken = await this.props.auth.getAccessToken()
   this.apiClient = new APIClient(accessToken);
   this.apiClient.getKudos().then((data) =>
     this.setState({...this.state, kudos: data})
   );
 }

 handleTabChange = (event, value) => {
   this.setState({ value });
 };

 handleTabChangeIndex = index => {
   this.setState({ value: index });
 };

 resetRepos = repos => this.setState({ ...this.state, repos })

 isKudo = repo => this.state.kudos.find(r => r.id == repo.id)
  onKudo = (repo) => {
   this.updateBackend(repo);
 }

 updateBackend = (repo) => {
   if (this.isKudo(repo)) {
     this.apiClient.deleteKudo(repo);
   } else {
     this.apiClient.createKudo(repo);
   }
   this.updateState(repo);
 }

 updateState = (repo) => {
   if (this.isKudo(repo)) {
     this.setState({
       ...this.state,
       kudos: this.state.kudos.filter( r => r.id !== repo.id )
     })
   } else {
     this.setState({
       ...this.state,
       kudos: [repo, ...this.state.kudos]
     })
   }
 }

 onSearch = (event) => {
   const target = event.target;
   if (!target.value || target.length < 3) { return }
   if (event.which !== 13) { return }

   githubClient
     .getJSONRepos(target.value)
     .then((response) => {
       target.blur();
       this.setState({ ...this.state, value: 1 });
       this.resetRepos(response.items);
     })
 }
  renderRepos = (repos) => {
   if (!repos) { return [] }
   return repos.map((repo) => {
     return (
       <Grid item xs={12} md={3} key={repo.id}>
         <GithubRepo onKudo={this.onKudo} isKudo={this.isKudo(repo)} repo={repo} />
       </Grid>
     );
   })
 }

 render() {
   return (
     <div className={styles.root}>
       <SearchBar auth={this.props.auth} onSearch={this.onSearch} />
        <Tabs
         value={this.state.value}
         onChange={this.handleTabChange}
         indicatorColor="primary"
         textColor="primary"
         fullWidth
       >
         <Tab label="Kudos" />
         <Tab label="Search" />
       </Tabs> 
       <SwipeableViews
         axis={'x-reverse'}
         index={this.state.value}
         onChangeIndex={this.handleTabChangeIndex}
       >
         <Grid container spacing={16} style={{padding: '20px 0'}}>
           { this.renderRepos(this.state.kudos) }
         </Grid>
         <Grid container spacing={16} style={{padding: '20px 0'}}>
           { this.renderRepos(this.state.repos) }
         </Grid>
       </SwipeableViews>
     </div>
   );
 }
}

export default withStyles(styles)(withAuth(Home)); 
```

现在运行`npm start`并在浏览器中打开`http://localhost:8080`。你应该能够登录，搜索 GitHub 回购，并喜欢回购，并在你的 Kudos 列表中看到它！

[![final running app](img/b4ccbec2432fa8f57e81f0aced6bbd03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b5e0vd53--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-react/final-running-app-533712d704c661e6aceee0f5788d20e61b17974483006a861d607c51b49f062d.gif)

## 了解关于 Python、Flask 和 React 的更多信息

正如我们所见，React 是一个强大而简单的 JavaScript 库，拥有惊人的采用率和社区增长。在本教程中，您学习了如何用 React、Python 和 Flask 构建一个全功能、安全的 JavaScript。要了解 React 和其他技术的更多信息，请查看来自@oktadev 团队的其他优秀资源:

*   [渐进式网络应用的终极指南](https://developer.okta.com/blog/2017/07/20/the-ultimate-guide-to-progressive-web-applications)
*   [用 Python 和 Flask 构建一个简单的 CRUD 应用](https://developer.okta.com/blog/2018/07/23/build-a-simple-crud-app-with-flask-and-python)
*   [用 Node 和 React 构建一个基本的 CRUD 应用](https://developer.okta.com/blog/2018/07/10/build-a-basic-crud-app-with-node-and-react)

一如既往，如果您有任何问题，请随时给我们留下您的评论。别忘了关注我们在 [Twitter](https://twitter.com/oktadev) 上关注我们，在[脸书](https://www.facebook.com/oktadevelopers)上喜欢我们，在 [LinkedIn](https://www.linkedin.com/company/oktadev/) 上查看我们，订阅我们的 [YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。
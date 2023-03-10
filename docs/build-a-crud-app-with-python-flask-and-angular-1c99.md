# 使用 Python、Flask 和 Angular 构建 CRUD 应用程序

> 原文：<https://dev.to/oktadev/build-a-crud-app-with-python-flask-and-angular-1c99>

开发者都有自己喜欢的 GitHub 库。他们有自己喜欢的软件项目，并密切关注最新的变化。在本教程中，您将创建一个简单的 CRUD 应用程序来保存和显示您最喜欢的 Github 开源项目。您将使用 Angular 实现用户界面特性，使用 Python 实现后端。

如今，有一个 API 不仅负责将数据保存到数据库，还负责处理权限、数据流、数据可见性等业务需求，这种情况并不少见。Python 是 API 的自然选择，因为它简单而强大。出于同样的原因，Angular 在客户端是一个很好的选择。Angular 对 TypeScript 的使用使其易于上手，并且仍然强大到足以处理最高级的场景。

让我们开始吧！

要完成本教程，您需要几样东西:

*   Python 3 已安装
*   MongoDB 启动并运行
*   一个永远免费的 Okta 账户

您将从用 Python 创建后端开始。

## 设置你的 Python + Angular 环境

对于本教程，您将使用 Python 3.6.4。您可以通过运行以下命令来检查您当前的 Python 版本:

```
python --version 
```

如果需要，使用 pyenv 安装 Python 3.6。

如果您使用的是 macOS，通过运行以下命令安装 pyenv:

```
brew update && brew install pyenv 
```

在 Linux 系统上使用 bash shell:

```
curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash 
```

安装完成后，您可以运行以下命令来安装 Python 3。

```
pyenv install 3.6.4
pyenv global 3.6.4 
```

通过将 pyenv shims 路径添加到您的`$PATH`中，确保您使用的是正确的 Python 版本。

```
export PATH="$(pyenv root)/shims:$PATH" 
```

尽管 Python 提供了丰富的标准库，使得在没有第三方库的情况下开发后端成为可能，但我们将使用众所周知的 Python 库来帮助您专注于功能需求，而不是花费时间重新发明轮子。

您将使用 pipenv 来管理项目的依赖项。

```
pip install --user pipenv 
```

您的环境已经准备好运行 Python 代码，并且您现在已经准备好在后端工作了。

## 引导你的 Angular App 的 Python API

通过运行以下命令创建一个目录，您的代码将位于其中:

```
mkdir favorite_github_projects && cd favorite_github_projects 
```

Pipenv 可以为您的项目创建一个虚拟环境；这样，您就可以隔离项目的依赖项。您可以通过运行以下命令创建一个虚拟环境:

```
pipenv --three --python=$(pyenv root)/shims/python 
```

注意，创建了一个名为`Pipfile`的文件，它看起来应该是这样的:

```
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]

[packages]

[requires]
python_version = "3.6" 
```

Pipfile 将被用作项目依赖项的清单。

您将使用 Python 3 中引入的`absolute_import`和`print_function`包。要导入它们，运行以下命令:

```
touch __init__.py
touch __main__.py 
```

并将以下内容复制粘贴到`__main__.py`文件中:

```
from __future__ import absolute_import, print_function 
```

在下一节中，您将实现列出、收藏和不收藏 GitHub 项目所需的端点。

## 用 Python 创建你的 Angular 客户端的 API

您的 API 应该能够为给定用户列出所有喜爱的 GitHub 存储库。它还应该能够喜欢和不喜欢 GitHub 库。您将公开以下端点:

```
# For the authenticated user fetches all favorited GitHub projects
GET /kudos

# Favorite a GitHub project for the authenticated user
POST /kudos

# Unfavorite a favorited GitHub project
DELETE /kudos/:id 
```

偏爱一个 GitHub 项目基本上意味着一个客户端对你的 Python 服务器进行 HTTP POST 调用，它对调用有一些期望:请求体或有效负载必须是 JSON 有效负载应该有四个属性，GitHub 项目`id`、`full_name`、`description`和`html_url`。只有 GitHub 项目`id`是必需的属性。这意味着，对于没有给出`id`的任何`POST /kudos`，服务器必须拒绝该呼叫。所有请求都必须经过身份验证。

您的 Python 后端将必须表示两种数据模式，一种是传入的请求负载，另一种是服务器将在数据库上持久存储的文档。它们将分别被称为`GithubRepoSchema`和`KudoSchema`。

当偏好一个 GitHub 项目时，客户端将发送一个如下所示的有效载荷:

```
{  "description":  "okta-oidc-js",  "full_name":  "okta/okta-oidc-js",  "html_url":  "https://github.com/okta/okta-oidc-js",  "id":  97974659  } 
```

首先创建必要的文件，运行以下命令:

```
mkdir -p app/kudo
touch app/kudo/schema.py
touch app/kudo/service.py
touch app/kudo/ __init__.py 
```

您刚才运行的命令创建了 app 目录以及其中的另一个名为`kudo`的目录，该目录现在有三个文件:`schema.py`、`service.py`和`__init__.py`。

该模式有两个职责:表示数据和作为验证传入请求有效负载的参考。有一个非常有用的包叫做 [marshmallow](https://marshmallow.readthedocs.io/en/3.0/) ，它是一个 ORM/ODM/框架无关的库，用于将复杂的数据类型(比如对象)序列化/反序列化为本机 Python 数据类型。

通过运行以下命令安装 marshmallow:

```
pipenv install marshmallow==2.16.3 
```

然后，将下面的类复制并粘贴到`schema.py` :

```
from marshmallow import Schema, fields

class GithubRepoSchema(Schema):
  id = fields.Int(required=True)
  repo_name = fields.Str()
  full_name = fields.Str()
  description = fields.Str()

class KudoSchema(GithubRepoSchema):
  user_id = fields.Email(required=True) 
```

因为您的应用程序需要显示用户喜爱的 GitHub 项目，换句话说，它必须在数据库中持久化的内容与传入的请求有效负载非常相似，所以您必须为`KudoSchema`做的就是使它从`GithubRepoSchema`继承，并通过添加一个新的必填字段`user_id`来专门化它，该字段将用于过滤用户在数据库中的数据。

## 用 MongoDB 持久化你的 Python REST API

实现了数据表示之后，下一步是准备应用程序将数据持久化到 MongoDB 中。为了连接和运行数据库查询，您将使用一个由 MongoDB 自己创建和维护的名为 pymongo 的库。

可以通过运行以下命令来安装`pymongo`库:

```
pipenv install pymongo==3.7.2 
```

首先创建`MongoRepository`类。让一个类只承担一项责任总是一个好主意，所以 MongoDB 在后端应用程序中唯一要明确处理的点是在`MongoRepository`中。

首先创建一个目录，存放所有与持久性相关的文件，建议是:`repository`。

```
mkdir -p app/repository 
```

然后，为`MongoRepository`类创建文件:

```
touch app/repository/mongo.py
touch app/repository/ __init__.py 
```

现在将以下内容粘贴到`app/repository/mongo.py`文件中。

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

如您所见，`MongoRepository`类非常简单，它在初始化时创建一个数据库连接，然后将它保存到一个实例变量中，供下面的方法使用:`find_all`、`find`、`create`、`update`和`delete`。注意，所有方法都显式地使用了`pymongo` API。

为了简单起见，`MongoRepository`正在读取环境变量`MONGO_URL`以获得 MongoDB 字符串连接。你可以随意用硬编码的字符串来修改它，或者使用 [python-dotenv](https://github.com/theskumar/python-dotenv) 将所有的环境变量保存在一个地方。

由于将来您可能想要使用其他数据库，因此将您的应用程序从 MongoDB 中分离出来是一个好主意。坚实的原则告诉我们，依赖抽象类比依赖具体类更好。

继续创建一个存储库抽象。将以下内容粘贴到`app/repository/ __init__.py`文件中:

```
class Repository(object):
 def __init__ (self, adapter=None):
   if not adapter:
     raise ValueError("Invalid repository implementation")
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

太好了！现在，您有了一个抽象类来表示通用存储库，还有一个具体类来实现抽象类契约。

您将很快实现 REST API 的端点。为了保持您的端点干净，并且只负责分派请求和输出数据，您将使用服务模式并创建一个类来验证传入的请求，并处理数据库持久化和从中获取数据。

将以下内容粘贴到`app/kudo/service.py`文件:

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

这里有两件事需要注意。首先，所有引起副作用的操作都在使用`user_id`。这是因为您希望确保像喜欢、不喜欢或列出 GitHub 项目这样的操作是为正确的用户完成的。最后，服务对象不直接与`MongoRepository`类交互，而是用具体类`MongoRepository`来“改编”存储库抽象类，具体类将用于将数据持久化到 MongoDB。

## 定义你的 Python API 中间件

向 REST API 发出的请求使用 JSON Web Token (JWT)让后端知道该请求已经过授权。它是如何工作的？

1.  在前端，用户使用她/他的 Okta 帐户登录
2.  然后，客户端将被授予一个 JSON Web 令牌
3.  JSON Web Token 应该在每个请求中作为 HTTP 授权头的值发送给服务器
4.  然后，服务器将验证 JSON Web 令牌是否有效，当凭证/JWT 无效时，用 401 HTTP 状态暂停请求。

JWT 验证将在中间件中实现。您需要安装`flask`和`pyjwt`来执行 JWT 验证并将用户存储在会话中。

要安装它们，运行以下命令:

```
pipenv install pyjwt==1.7.1
pipenv install flask==1.0.2 
```

然后，继续创建必要的文件:

```
touch app/http/api/ __init__.py
touch app/http/api/endpoints.py
touch app/http/api/middlewares.py 
```

您的 JWT 中间件应该是这样的:

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
           return json.dumps({'error': 'no authorization token provied'}), 401, {'Content-type': 'application/json'}

       try:
           token = authorization.split(' ')[1]
           resp = decode(token, None, verify=False, algorithms=['HS256'])
           g.user = resp['sub']
       except exceptions.DecodeError as identifier:
           return json.dumps({'error': 'invalid authorization token'}), 401, {'Content-type': 'application/json'}

       return f(*args, **kwargs)

   return wrap 
```

如果你对上面的代码满意，把它粘贴到`middlewares.py`文件中。

您正在使用 Flask 提供的一个名为`g`的模块，这是一个跨请求生命周期共享的全局上下文。中间件检查请求是否有效，如果有效，中间件将提取经过身份验证的用户详细信息，并将它们保存在全局上下文中。

## 定义你的 Python API 端点

您的最终目标是使用 Angular 实现一个 web 应用程序，它将在 Chrome 和 Firefox 等浏览器上运行，并将使用 AJAX 与 REST API 通信。在这种情况下，您首先要考虑的是确保您的后端支持浏览器的跨来源资源共享(CORS)预检请求。CORS 是如何工作的不在本教程的讨论范围之内，所以我们将使用一个 Python 库来为您处理它。

```
pipenv install flask_cors==3.0.7 
```

HTTP 处理程序现在应该很容易，因为您已经完成了后端的重要部分，现在只需要将所有的东西放在一起。继续将以下内容粘贴到`app/http/api/endpoints.py`文件中。

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

您已经创建了三个端点，所有这些端点都用`login_required`修饰，确保请求在进一步处理之前得到授权。

您应该能够使用下面的命令运行 REST API:

```
FLASK_APP=$PWD/app/http/api/endpoints.py FLASK_ENV=development pipenv run python -m flask run --port 4433 
```

## 打造你的 Angular App

为了创建您的 Angular 客户端应用程序，您将使用 Angular 的令人敬畏的 [`ng-cli`](https://cli.angular.io/) 工具来绕过所有 JavaScript 构建过程的麻烦。

安装 [`ng-cli`](https://cli.angular.io) 相当简单。您将使用 [`npm`](https://www.npmjs.com/get-npm) ，确保您已经安装了它或者使用了您偏好的依赖管理器。

要安装`ng-cli`，运行上面的命令。更多安装选项，请查看这个[角度快速入门](https://angular.io/guide/quickstart#install-cli%20out)。

```
npm install -g @angular/cli@7.3.5 
```

导航到`app/http`目录，使用`ng-cli`创建一个角度应用:

```
cd app/http
ng new web-app 
```

在创建开始编写前端应用程序所需的所有文件之前，Angular CLI 将提示您两个问题。第一个问题是你是否想要路由，你可以输入`y`让 Angular CLI 设置你的应用启用路由。第二个问题是你想要哪种风格的 CSS，你会选择 SCSS。

[![Angular CLI New](img/49671a541265463e3609c58d4fcc5b6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7OyCYu3A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/angular-cli-new-ca001fea580d7a95f46c6f4f62ebbe7360e74e234e555aabf5a3052e1afc326f.gif)

一旦 Angular CLI 完成创建文件，您现在可以导航到新创建的目录并启动 web 服务器。

```
cd web-app
ng serve --open --port 8080 
```

运行`ng server --open`将启动一个监听端口 8080 的 web 服务器。在你的浏览器中打开这个 URL:`http://localhost:8080/`你的浏览器应该加载一个 Angular app 并呈现由`ng-cli`自动创建的 AppComponent。

[![Angular New App](img/ca3f564153c04d5ab0e91f697b41b153.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8RzTJujZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/angular-new-app-a0e6925aa1bfbd20b8ce3b2c184f3cb89f587b64d60e34bcea6c6837f7d38fde.png)

你现在的目标是用材质设计打造一个简洁美观的 UI。值得庆幸的是，Angular 有一个成熟的库来帮助你完成这个旅程， [Angular Material](https://material.angular.io/) 已经将材料设计概念转化为 Angular 组件。

运行以下命令来安装您将需要的东西:

```
npm install --save @angular/material@7.3.5 @angular/cdk@7.3.5 @angular/animations@7.2.10 
```

现在配置您的项目来使用它:

```
ng add @angular/material 
```

最后一步，删除`src/app/app.component.html`中的所有内容，并将以下内容粘贴到:

```
<router-outlet></router-outlet> 
```

现在，您已经有了像 MatToolbarModule、MatButtonModule、MatIconModule、MatCardModule、MatTabsModule、MatGridListModule 这样的组件，还有更多的组件可供导入和使用。你很快就会用到它们。我们来谈谈受保护的路线。

## 为您的 Angular 应用程序添加认证

编写安全的用户认证和构建登录页面很容易出错，这可能会导致新项目的失败。Okta 使快速安全地实现所有用户管理功能变得简单。注册一个[免费开发者账户](https://developer.okta.com/signup/)并在 Okta 中创建一个 OpenID Connect 应用程序。

[![Okta Dev Signup](img/57275e57f2fab8f0cc0621e8bd1497c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UB6v2dNZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/okta-dev-signup-5ca629cb8a0b5bf45298190ebff6e09a89b553be0b5042a9e8e559dd0440eb39.png)

登录后，点击**添加应用**创建一个新的应用。

[![Okta Add Application](img/94c7abf94410b47ca82581f7b138c81f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--94zIt6W2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/okta-add-application-024fdfa427033e2e345b48167d8fdef2592f8dcaa464be89487e257a629d39ad.png)

选择**单页 App** 平台选项。

[![Okta Single Page App](img/47807df650ea97d9311f62019b699335.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zqaWtyLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/okta-single-page-app-96b1b0faa43717d47faaf99696fe2155e793c5f482b7cbae21747bdf3fd72ba4.png)

默认的应用程序设置应该和图中的一样。

[![Okta App Settings](img/1d9d814b4e84014529378bd6762c1bb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lH-y7He7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/okta-app-settings-99892ff83b5e572a4c2b64d3d9b2edde2266d7f7434c18b8e60f6f005d7718e0.png)

有了您的 token OpenID Connect 应用程序，您现在可以继续前进并保护需要身份验证的路由。

## 以角度创建您的路线

您将使用 [@angular/router](https://angular.io/guide/router) ，一个用于将 URL 路由到 angular 组件的库。

您的角度应用将有两条路线:

根路由不需要用户登录，它实际上是你的应用程序的登陆页面。用户应该能够访问该页面以便登录。您将使用 [Okta Angular SDK](https://developer.okta.com/code/angular/) 将您的路线与 Okta 的 OpenID Connect API 集成。

Home route 将呈现您的应用程序将拥有的大部分组件。它应该实现以下用户故事。

*   经过身份验证的用户应该能够在 Github API 中搜索他/她喜欢的开源项目
*   一个经过认证的用户应该能够喜欢一个令他/她满意的开源项目
*   经过身份验证的用户应该能够在不同的选项卡中看到他/她以前喜欢的开源项目和搜索结果

要安装 [`@okta/okta-angular`](https://www.npmjs.com/package/@okta/okta-angular) ，运行命令:

```
npm install --save @okta/okta-angular 
```

现在继续将以下内容粘贴到`src/app/app-routing.module.ts`文件中。

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { LoginComponent } from './login/login.component';
import { HomeComponent } from './home/home.component';

import { OktaAuthModule, OktaCallbackComponent, OktaAuthGuard } from '@okta/okta-angular';

const routes: Routes = [
 { path: '', component: LoginComponent },
 {
   path: 'home',
   component: HomeComponent,
   canActivate: [OktaAuthGuard],
 },
 { path: 'implicit/callback', component: OktaCallbackComponent },
];

@NgModule({
 imports: [
   RouterModule.forRoot(routes),
   OktaAuthModule.initAuth({
     issuer: {yourOktaDomain},
     clientId: {yourClientId},
     redirectUri: 'http://localhost:8080/implicit/callback',
     scope: 'openid profile email'
   })
 ],
 exports: [RouterModule]
})
export class AppRoutingModule { } 
```

目前，不要担心`Login`和`Home`组件被导入。你很快就会着手做了。重点关注`OktaAuthModule`、`OktaCallbackComponent`和`OktaAuthGuard`组件。

要在应用程序的任何地方保护带身份验证的路由，您需要用 Okta 提供的`canActivate: [OktaAuthGuard]`组件配置路由。

`OktaCallbackComponent`是 Okta 完成登录过程后用户将被重定向到的路线/URI 目的地，而`OktaAuthModule`将注入一个服务，该服务公开了访问经过身份验证的用户的有用方法。

## 添加登录角度分量

您现在已经准备好创建登录组件了。所有用户(不仅仅是经过身份验证的用户)都可以访问该组件。登录组件的主要目标是对用户进行身份验证。

Angular CLI 有一个有用的生成器来加速组件创建。在目录`app/htpp/web-app`中，运行下面的命令:

```
ng generate component login 
```

[![Angular CLI Login Component](img/0830d987d612e0344e3d0490efc03f72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MWNeZPgR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/angular-cli-login-component-3fd28a196433a223bdb1c7677ca314968d46c6dc211a6ebd0c97d15ea6bc0242.gif)

`ng generate component`将不仅仅创建组件文件，它还将配置您的 Angular 应用程序来正确地注入新创建的组件。

继续将以下内容粘贴到`src/app/login/login.component.ts`文件中:

```
import { Component, OnInit } from '@angular/core';
import { OktaAuthService } from '@okta/okta-angular';
import { Router } from '@angular/router';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent implements OnInit {

  constructor(private oktaAuth: OktaAuthService, private router: Router) {
  }

  async ngOnInit() {
    const isAuthenticated = await this.oktaAuth.isAuthenticated();
    if (isAuthenticated) {
      this.router.navigate(['/home'], {replaceUrl: true})
    }
  }

  async login(event) {
    event.preventDefault();
    await this.oktaAuth.loginRedirect('/home');
  }
} 
```

然后将以下内容粘贴到`src/app/login/login.component.html`文件中:

```
<mat-card>
  <mat-card-content>
    <button mat-button (click)="login($event)" color="primary">Login</button>
  </mat-card-content>
</mat-card> 
```

下面是登录过程的工作方式:

用户导航到登录页面。

[![App Landing Page](img/8e0bedbb95027566a37f98b612b5a97e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O6F6duA1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/app-landing-page-b97ad13483905bfda8ab9f33bf1f54024d658b2c81bf8c375f0f9e5f7266333e.png)

在登录组件中，您使用 [Okta Angular SDK](https://developer.okta.com/code/angular/) 来检查用户是否已经登录。如果用户已经登录，他们应该被重定向到`/home`路线；否则，他或她可以点击`Login`然后被重定向到 Okta，进行身份验证，并被重定向到主页。

[![App Okta Login](img/77cb51527555109ae14b552cdb5a47f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xDNRWQ3B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/app-okta-login-0a7959211c7cf87c6831e805465116e67c19bc1f285f6d217b230c2d29190614.png)

您将很快开始制作家庭组件。登录过程完成后，您应该会看到:

[![App Post Login](img/8d8151bb1252b15080145ae1ce85c081.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JCuiciYh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/app-post-login-374010a999596305e694717e4ac2f433f8c2ba7205e0b2fccf052d7001e4dbc7.png)

## 添加您的棱角分明的家居组件

`Home`是你的应用程序的主要组成部分。它需要列出所有喜欢的开源项目以及 GitHub 搜索结果，这是使用标签完成的。

它是这样工作的:

当呈现 Home 组件时，您应该对您的 Python REST API 进行 HTTP 调用，以获取所有用户喜爱的开源存储库。`@angular/core`提供了一个组件可以实现的模块`OnInit`,以便在组件呈现时触发`ngOnInit`方法。您将使用`ngOnInit`对您的 Python REST API 进行 HTTP 调用。

[![App Login Flow](img/921ea1b0c14a7ea18e59fd11138fb2b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NCAIRqNl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/app-login-flow-1284469b893dd47a879c082f9fd1f8edfe383c09ee7e7012162033a225d48695.gif)

用户可以在屏幕顶部的文本输入中键入关键字。方法`onSearch`将在输入的`keyPress`事件上被调用。每当用户输入`Return/Enter`时，该方法将对 GitHub API 执行查询。

一旦 GitHub 回复了开源库列表，您将在“SEARCH”选项卡中显示所有的库。那么用户可以选择任何一个存储库。偏好存储库将对 REST API 进行 HTTP 调用，并将其保存到数据库中。

[![App Home Working](img/2f172e17f18be34600c892062901c6ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tB9JgHMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/app-home-working-e236ec122d45c0dc55345ef6ab6f875a76f5a6cffe60bade8725ca14a41b7217.gif)

Home 组件还负责在用户点击`Logout`时注销用户。发生这种情况时，与会话相关的所有数据都将被清除，用户将被重定向到登录页面。

[![App Logout Flow](img/3cc5e2857068a3f146832bba387d8762.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cUfvPbti--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/app-logout-flow-80693c5f4f4cecc6698ae8c5415e402ce204b71c01bd7851250322557945d432.gif)

`/home`是一条经过认证的路线，所以如果用户试图未经认证就访问它，他们应该被重定向到 Okta 的登录页面。

[![App Home Login](img/7e92ea1827bd142d0628a078323a1643.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HnK7PCb2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/app-home-login-aa8f462dfeb7200f704cbb039027f6badda2c2b660b58d07800b9b038e4fbc94.gif)T3】

```
ng generate component home 
```

将以下内容粘贴到`src/app/home/home.component.ts`中。

```
import { Component, OnInit } from '@angular/core';
import { OktaAuthService } from '@okta/okta-angular';
import { GithubClientService } from '../gb-client.service';
import { ApiClientService } from '../api-client.service';

@Component({
 selector: 'app-home',
 templateUrl: './home.component.html',
 styleUrls: ['./home.component.scss']
})
export class HomeComponent implements OnInit {

 selectedTab: Number;
 repos: Array<any>;
 kudos: Array<any>;

 constructor(
   private oktaAuth: OktaAuthService,
   private githubClient: GithubClientService,
   private apiClient: ApiClientService
 ) {
   this.selectedTab = 0;
   this.repos = [];
   this.kudos = [];
 }

 async ngOnInit() {
   this.apiClient.getKudos().then( (kudos) => {
     this.kudos = kudos;
   } )
 }

 async logout(event) {
   event.preventDefault();
   await this.oktaAuth.logout('/');
 }

 onSearch = (event) => {
   const target = event.target;
   if (!target.value || target.length < 3) { return; }
   if (event.which !== 13) { return; }

   this.githubClient
     .getJSONRepos(target.value)
     .then((response) => {
       target.blur();
       this.selectedTab = 1;
       this.repos = response.items;
     })
 }

 onKudo(event, repo) {
   event.preventDefault();
   this.updateBackend(repo);
 }

 updateState(repo) {
   if (this.isKudo(repo)) {
     this.kudos = this.kudos.filter( r => r['id'] !== repo.id );
   } else {
     this.kudos = [repo, ...this.kudos];
   }
 }

 isKudo(repo) {
   return this.kudos.find( r => r['id'] === repo.id );
 }

 updateBackend = (repo) => {
   if (this.isKudo(repo)) {
     this.apiClient.deleteKudo(repo);
   } else {
     this.apiClient.createKudo(repo);
   }
   this.updateState(repo);
 }
} 
```

然后将以下内容粘贴到`src/app/home/home.component.html`中。

```
<mat-toolbar color="primary">
 <input matInput (keyup)="onSearch($event)" placeholder="Search for your OOS project on Github + Press Enter">
 <button mat-button (click)="logout($event)">LOGOUT</button>
</mat-toolbar>

<mat-tab-group mat-align-tabs="center" [selectedIndex]="selectedTab" dynamicHeight>
   <mat-tab label="KUDO">
     <mat-grid-list cols="4">
         <mat-grid-tile *ngFor="let repo of kudos" rowHeight='200px' >
             <mat-card class="card">
               <mat-card-header class="title">
                 <mat-card-title>{{repo.full_name}}</mat-card-title>
               </mat-card-header>
               <mat-card-content>
                 {{repo.description}}
               </mat-card-content>
               <mat-card-actions>
                 <button mat-icon-button [color]="isKudo(repo) ? 'accent' : 'primary'" (click)="onKudo($event, repo)">
                   <mat-icon >favorite</mat-icon>
                 </button>
               </mat-card-actions>
             </mat-card>
         </mat-grid-tile>
     </mat-grid-list>
   </mat-tab>
   <mat-tab label="SEARCH">
     <mat-grid-list cols="4">
         <mat-grid-tile *ngFor="let repo of repos" rowHeight='200px' >
             <mat-card class="card">
               <mat-card-header class="title">
                 <mat-card-title>{{repo.full_name}}</mat-card-title>
               </mat-card-header>
               <mat-card-content>
                 {{repo.description}}
               </mat-card-content>
               <mat-card-actions>
                 <button mat-icon-button [color]="isKudo(repo) ? 'accent' : 'primary'" (click)="onKudo($event, repo)">
                   <mat-icon >favorite</mat-icon>
                 </button>
               </mat-card-actions>
             </mat-card>
         </mat-grid-tile>
     </mat-grid-list>
   </mat-tab>
</mat-tab-group> 
```

## 从 Angular 调用 Python API

太好了！现在，您需要对 Python REST API 和 GitHub REST API 进行 HTTP 调用。GitHub HTTP 客户端需要一个函数来请求这个 URL: `https://api.github.com/search/repositories?q=USER-QUERY`。您将使用`q`查询字符串来传递用户想要查询 Github 存储库的术语。

Angular CLI 为服务提供了一个很好的生成器。要创建 GitHub 客户端，运行以下命令:

```
ng generate service gbClient 
```

然后，将以下内容粘贴到`src/app/gb-client.service.ts`文件中:

```
import { Injectable } from '@angular/core';

@Injectable({
 providedIn: 'root'
})
export class GithubClientService {

 constructor() { }

 getJSONRepos(query) {
   return fetch('https://api.github.com/search/repositories?q=' + query).then(response => response.json());
 }

 getJSONRepo(id) {
   return fetch('https://api.github.com/repositories/' + id).then(response => response.json());
 }
} 
```

现在，您需要创建一个 HTTP 客户端来对已经实现的 Python REST API 进行 HTTP 调用。因为对 Python REST API 的所有请求都需要对用户进行身份验证，所以需要用 Okta 提供的`acessToken`来设置`Authorization` HTTP 头。

```
ng generate service apiClient 
```

[![CLI Generate Service](img/c63192bf2b6a5ead6d35a51f950618cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Keik9ifE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets-jekyll/blog/python-angular/cli-generate-service-e367bcdc39db36a236b8c122d0614d539b9c3b4f70f6ff2cd46a189feedf2000.gif)

然后，将以下内容粘贴到`src/app/api-client.service.ts`文件中。

```
import { Injectable } from '@angular/core';
import { OktaAuthService } from '@okta/okta-angular';
import { HttpClient, HttpHeaders } from '@angular/common/http';

@Injectable({
 providedIn: 'root'
})
export class ApiClientService {
 constructor(private oktaAuth: OktaAuthService, private http: HttpClient) {
 }

 createKudo(repo) {
   return this.perform('post', '/kudos', repo);
 }

 deleteKudo(repo) {
   return this.perform('delete', `/kudo/${repo.id}`);
 }

 updateKudo(repo) {
   return this.perform('put', `/kudos/${repo.id}`, repo);
 }

 getKudos() {
   return this.perform('get', '/kudos');
 }

 getKudo(repo) {
   return this.perform('get', `/kudo/${repo.id}`);
 }

 async perform (method, resource, data = {}) {
   const accessToken = await this.oktaAuth.getAccessToken();
   const url = `http://localhost:4433${resource}`;

   const httpOptions = {
     headers: new HttpHeaders({
       'Content-Type': 'application/json',
       'Authorization': `Bearer ${accessToken}`
     })
   };

   switch (method) {
     case 'delete':
       return this.http.delete(url, httpOptions).toPromise();
     case 'get':
       return this.http.get(url, httpOptions).toPromise();
     default:
       return this.http[method](url, data, httpOptions).toPromise();
   }
 }
} 
```

最后，您需要确保您的 Angular 应用程序正确地导入了所有模块。为此，确保您的`src/app/app.module.ts`文件看起来像这样:

```
import { BrowserModule } from '@angular/platform-browser';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { NgModule } from '@angular/core';
import { HttpClientModule } from '@angular/common/http';

import {
 MatToolbarModule,
 MatButtonModule,
 MatIconModule,
 MatCardModule,
 MatTabsModule,
 MatGridListModule
} from '@angular/material';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { LoginComponent } from './login/login.component';
import { HomeComponent } from './home/home.component';
@NgModule({
 declarations: [
   AppComponent,
   LoginComponent,
   HomeComponent
 ],
 imports: [
   BrowserModule,
   BrowserAnimationsModule,
   AppRoutingModule,
   HttpClientModule,
   MatToolbarModule,
   MatButtonModule,
   MatIconModule,
   MatCardModule,
   MatTabsModule,
   MatGridListModule
 ],
 providers: [],
 bootstrap: [AppComponent]
})
export class AppModule { } 
```

现在，您可以同时运行 Angular 前端和 Python 后端来查看最终结果:

要启动 Python REST API 运行:

```
cd kudos_oss &&
FLASK_APP=$PWD/app/http/api/endpoints.py FLASK_ENV=development pipenv run python -m flask run --port 4433 
```

然后开始角度应用:

```
cd app/http/web-app && ng serve --open --port 8080 
```

您可能已经注意到，您的 Python REST API 正在监听端口 4433，而 Angular 应用程序由端口 8080 上的一个进程提供服务。你只需要在浏览器中打开这个 URL `http://localhost:8080`。

## 了解更多关于 Angular、Python 和 Flask 的信息

在本教程中，我指导您使用 Angular 和 Python 开发了一个单页面 web 应用程序。仅仅使用几行代码，您就能够实现客户端和服务器的用户认证。Angular 利用了 TypeScript，它是 JavaScript 语言的超集，并添加了类型信息。

如果你准备好了解 Angular 的更多信息，我们还有一些其他资源供你参考:

*   [Angular 6 -有什么新功能，为什么要升级？](https://developer.okta.com/blog/2018/05/09/upgrade-to-angular-6)
*   [用 Angular 7 和 Spring Boot 构建一个基本的 CRUD 应用](https://developer.okta.com/blog/2018/08/22/basic-crud-angular-7-and-spring-boot-2)
*   [用 Python、Flask 和 React 构建一个简单的 CRUD 应用](https://developer.okta.com/blog/2018/12/20/crud-app-with-python-flask-react)

一如既往，我们希望您关注我们，了解我们团队的更多精彩内容和更新。你可以在推特[@奥克塔德夫](https://twitter.com/OktaDev)，在[脸书](https://www.facebook.com/oktadevelopers/)，以及 [LinkedIn](https://www.linkedin.com/company/oktadev/) 上找到我们。
# 带有 Vue.js 的 Ruby on Rails API

> 原文：<https://dev.to/justalever/ruby-on-rails-api-with-vue-js-46k8>

你知道 Ruby on Rails 可以作为一个严格的基于 API 的后端应用程序吗？这对你有什么好处？可以把它看作是多个*未来*应用程序直接吸收和使用这些数据的单一事实来源。从本地移动应用程序到前端框架，任何东西都可以使用这些数据。许多应用程序本质上可以与“真实的来源”交流，这意味着所有应用程序更加一致。

在这个版本中，我将制作一个简单而全面的应用程序，其中 Ruby on Rails 是我们的后端，Vue.js + Axios 是我们的前端。我将创建两个相互通信的应用程序，以实现与普通基于 Rails 的应用程序相同的结果，但具有 API 的所有功能。

#### 用于此构建

*   `Rails 5.2.2`
*   `Ruby 2.5`
*   储存`bcrypt 3.1.7`
*   储存`rack-cors`
*   宝石 [`redis 4.1.0`](https://github.com/redis/redis-rb)
*   宝石 [`jwt-sessions`](https://github.com/tuwukee/jwt_sessions)

### 我们到底在建造什么？

这个应用程序的核心很简单。这将是一个出售的黑胶唱片档案，并按艺术家分类。我们不会实现大量的外来逻辑，而只是让基于 API 的应用程序的基础井然有序。我们将触及认证(不使用 Devise)和基本 CRUD。

将有两个应用程序。

*   Ruby on Rails 后端——这将处理我们的数据、会话和认证。
*   Vue.js 前端——这将是视图层，但也是负责向我们基于 rails 的后端发送和接收数据的层。前端将在不同的实例上运行，使用 [Vue-CLI](https://cli.vuejs.org/) 来帮助我们设置应用程序。

### 录像

#### 第一部分

[https://www.youtube.com/embed/eUedqaHS4RQ](https://www.youtube.com/embed/eUedqaHS4RQ)

#### 第二部分

[https://www.youtube.com/embed/TBZwkWznhZs](https://www.youtube.com/embed/TBZwkWznhZs)

#### 第三部分

[https://www.youtube.com/embed/Pa6B0J_l8oc](https://www.youtube.com/embed/Pa6B0J_l8oc)

#### 第四部分

[https://www.youtube.com/embed/4e-ZlVsT5z4](https://www.youtube.com/embed/4e-ZlVsT5z4)

#### 第五部分

[https://www.youtube.com/embed/o_z5pol6vZE](https://www.youtube.com/embed/o_z5pol6vZE)

#### 第六部分

[https://www.youtube.com/embed/d9_-8yZmOF4](https://www.youtube.com/embed/d9_-8yZmOF4)

#### 第七部分

[https://www.youtube.com/embed/VqhcKlee2L0](https://www.youtube.com/embed/VqhcKlee2L0)

#### 第八部分

[https://www.youtube.com/embed/zFgoIPNBddc](https://www.youtube.com/embed/zFgoIPNBddc)

## 后端

我们的后端将是一个非常精简的 Rails 应用程序，没有基于视图的层。Rails 有一个方便的`api`模式，你可以在创建新应用的过程中通过传递标志`--api`来初始化它。让我们开始吧。

### 在 API 模式下创建 app

```
$ rails new recordstore-back --api 
```

Enter fullscreen mode Exit fullscreen mode

#### 添加宝石

1.  取消对`rack-cors`和`bcrypt`的注释。
2.  添加`redis`和`jwt_sessions`
3.  `bundle install`

下面是我的`Gemfile`
的当前状态

```
# Gemfile - Jan 2019
source 'https://rubygems.org'
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby '2.5.3'

# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
gem 'rails', '~> 5.2.2'
# Use sqlite3 as the database for Active Record
gem 'sqlite3'
# Use Puma as the app server
gem 'puma', '~> 3.11'
# Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder
# gem 'jbuilder', '~> 2.5'
# Use ActiveModel has_secure_password
gem 'bcrypt', '~> 3.1.7'

# Use ActiveStorage variant
# gem 'mini_magick', '~> 4.8'

# Use Capistrano for deployment
# gem 'capistrano-rails', group: :development

# Reduces boot times through caching; required in config/boot.rb
gem 'bootsnap', '>= 1.1.0', require: false

# Use Rack CORS for handling Cross-Origin Resource Sharing (CORS), making cross-origin AJAX possible
gem 'rack-cors'
gem 'redis', '~> 4.1'
gem 'jwt_sessions', '~> 2.3'

group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
end

group :development do
  gem 'listen', '>= 3.0.5', '< 3.2'
  # Spring speeds up development by keeping your application running in the background. Read more: https://github.com/rails/spring
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby] 
```

Enter fullscreen mode Exit fullscreen mode

### 创建用户模型

这一次我们不会使用“设计”了！Rails 有一些方便的内置功能来帮助用户设置身份验证。这条路线当然更复杂，但我建议这样做是为了更多地了解像 Devise 这样的流行工具是如何工作的(并解决许多令人头痛的问题)。

为了避免过于复杂，我们的`User`模型暂时不会与`Record`或`Artist`模型相关联。稍后我们可以添加它，这样一个`User`就可以通过前端接口向应用程序添加一个`Artist`和`Record`。

```
$ rails g model User email:string password_digest:string 
```

Enter fullscreen mode Exit fullscreen mode

`password_digest`字段将利用我们在初始设置时取消注释的`bcrypt` gem。它为您的密码创建了一个标记化的版本，以获得更好的安全性。

我们需要修改迁移文件，在`email`和`password_digest`列中包含默认的`null: false`。

```
# db/migrate/20190105164640_create_users.rb
class CreateUsers < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      t.string :email, null: false
      t.string :password_digest, null: false

      t.timestamps
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们在
中迁移它

```
$ rails db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

### 创建艺术家模型

`Artist`模型将是我们应用程序中的父关系。一张唱片(即将推出)将属于一位艺术家

```
$ rails g scaffold Artist name 
```

Enter fullscreen mode Exit fullscreen mode

注意，当资源被搭建时，没有视图被创建？这又是我们的 API 模式在起作用。我们的控制器也渲染`JSON`但是默认。

### 创建记录模型

我们的`Record`模型会多几个领域，属于一个艺术家。这个脚手架创建了一个`Record`模型(类)，它在新的`records`数据库表上有`title`、`year`、`artist_id`和`user_id`列。这就创建了一个考虑到所有这些数据的新迁移。

```
$ rails g scaffold Record title year artist:references user:references 
```

Enter fullscreen mode Exit fullscreen mode

在
中迁移两个模型

```
$ rails db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

### 命名我们的 API

搭建了我们需要的模型和数据结构之后，让我们来谈谈路由。API 经常变化。一个常见的趋势是引入允许第三方在认为合适时选择新 API 版本的版本。这样做对每个人来说都会出现较少的错误，但在后端会有更多的设置，主要是处理路由和文件位置。

为了命名我们的应用程序，我想做一个`v1`类型的概念，最终看起来像这样:

```
Rails.application.routes.draw do
  namespace :api do
    namespace :v1 do      
     # routes go here
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

命名空间允许我们在任何时候进一步扩展东西，比如我们推出一个新版本或者决定用后端构建更多。我们所有的数据都将存在于名称空间中，但我们的用户相关数据不会。我们可能不会对后端的用户群做太多改变，因为后端需要一个 API。随着应用的扩展，您的结果可能会有所不同。

### 更新路线

接下来，我们需要将最近搭建的资源添加到组合中

```
Rails.application.routes.draw do
  namespace :api do
    namespace :v1 do
      resources :artists
      resources :records
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

更新名称空间后，我们需要移动控制器来适应。将`artists_controller.rb`和`records_controller.rb`移动到`app/controllers/api/v1/`。一定要修改两者，使其包含新的命名空间，如下所示。顺便说一下，如果你的服务器正在运行，你应该重启它。

这里是艺术家控制器:

```
# app/controllers/api/v1/artists_controller.rb
module Api
  module V1
    class ArtistsController < ApplicationController
      before_action :set_artist, only: [:show, :update, :destroy]

      def index
        @artists = Artist.all

        render json: @artists
      end

      def show
        render json: @artist
      end

      def create
        @artist = Artist.new(artist_params)

        if @artist.save
          render json: @artist, status: :created
        else
          render json: @artist.errors, status: :unprocessable_entity
        end
      end

      def update
        if @artist.update(artist_params)
          render json: @artist
        else
          render json: @artist.errors, status: :unprocessable_entity
        end
      end

      def destroy
        @artist.destroy
      end

      private
      def set_artist
          @artist = Artist.find(params[:id])
      end

      def artist_params
          params.require(:artist).permit(:name)
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这里是`records_controller.rb`文件

```
module Api
  module V1
    class RecordsController < ApplicationController
      before_action :set_record, only: [:show, :update, :destroy]

      def index
        @records = current_user.records.all

        render json: @records
      end

      def show
        render json: @record
      end

      def create
        @record = current_user.records.build(record_params)

        if @record.save
          render json: @record, status: :created
        else
          render json: @record.errors, status: :unprocessable_entity
        end
      end

      def update
        if @record.update(record_params)
          render json: @record
        else
          render json: @record.errors, status: :unprocessable_entity
        end
      end

      def destroy
        @record.destroy
      end

      private
      def set_record
        @record = current_user.records.find(params[:id])
      end

      def record_params
        params.require(:record).permit(:title, :year, :artist_id)
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 获取 JWT _ 会话设置

JSON Web 令牌是我们在这个应用程序中处理身份验证的方式。不是基于 API 的 Rails 应用程序使用基于会话的令牌来验证给定`User`的登录/会话。对于 API 驱动的前端应用程序，我们没有相同的会话逻辑来做这样的事情。我们还希望我们的 API 可以用于其他应用程序或我们构建的东西，如移动应用程序、本地应用程序等(可能性是无限的)。这个概念就是基于 API 的应用程序风靡一时的原因。

让我们设置 JWTSessions。

```
# app/controllers/application_controller.rb

class ApplicationController < ActionController::API
   include JWTSessions::RailsAuthorization
end 
```

Enter fullscreen mode Exit fullscreen mode

在您的`application_controller.rb`文件中添加以下 include。我们从之前安装的 gem 中得到这个。

注意你的控制器是如何继承自`ActionController::API`而不是默认的`ApplicationController`。那就是全力的`API`模式！

我们需要对未经授权的请求进行一些异常处理。让我们将文件扩展为以下内容:

```
# app/controllers/application_controller.rb

class ApplicationController < ActionController::API
  include JWTSessions::RailsAuthorization
  rescue_from JWTSessions::Errors::Unauthorized, with :not_authorized

  private

  def not_authorized
    render json: { error: 'Not Authorized' }, status: :unauthorized
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要一个加密密钥。默认情况下，JWTSessions gem 使用`HS256`算法，它需要提供一个加密密钥。

gem 默认使用 Redis 作为令牌库，所以这就是为什么你在我们的`Gemfile`中看到它。我们需要一个正在运行的`redis-server`实例。使用本地内存进行测试是可能的，但是我们将在这个版本中使用`redis`,因为它无论如何都会在生产中运行。[查看自述文件了解更多信息](https://github.com/tuwukee/jwt_sessions#token-store)

创建一个名为`jwt_sessions.rb`的新初始化文件，并添加下面的

```
# config/initializers/jwt_sessions.rb

JWTSessions.encryption_key = 'secret' # use something else here 
```

Enter fullscreen mode Exit fullscreen mode

如果你愿意的话，绝对值得在这里使用你的密钥以外的东西！

### 签约端点

因为我们走的是基于令牌的路线，所以我们可以选择将它们存储在客户端 cookies 或本地存储上。归结起来就是你在哪里着陆的偏好。两种选择各有利弊。Cookies 易受 CSRF 攻击，本地存储易受 XSS 攻击。

当选择`cookies`作为令牌存储选项时，`JWT_Sessions` gem 提供了一组令牌——访问、刷新和 CSRF。

我们将利用 CSRF 验证的 cookies

gem 中的会话是一对名为`access`和`refresh`的令牌。访问令牌的生命周期较短，默认值为 1 小时。另一方面，Refresh 的使用寿命较长，约为 2 周。所有这些都是可配置的。

我们将在一个我们可以生成的`signup_controller`文件中做一些逻辑处理。

```
$ rails g controller signup create 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以省略在`config/routes.rb`
中生成的路由

```
Rails.application.routes.draw do
    get 'signup/create' # remove this line
    ...
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们将注册逻辑添加到控制器中。为此，我们将利用 JWT 会议宝石。

```
# app/controllers/signup_controller.rb

class SignupController < ApplicationController
  def create
    user = User.new(user_params)
    if user.save
      payload = { user_id: user.id }
      session = JWTSessions::Session.new(payload: payload, refresh_by_access_allowed: true)
      tokens = session.login

      response.set_cookie(JWTSessions.access_cookie,
                          value: tokens[:access],
                          httponly: true,
                          secure: Rails.env.production?)
      render json: { csrf: tokens[:csrf] }
    else
      render json: { error: user.errors.full_messages.join(' ') }, status: :unprocessable_entity
    end
  end

  private

  def user_params
    params.permit(:email, :password, :password_confirmation)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了很多事情，但并不是太不可能理解。我们将把用户指向端点`signup/create`方法。如果一切顺利，我们这样做可以实现以下目标。

*   使用允许的参数(电子邮件、密码、密码确认)创建新用户
*   将 user_id 指定为有效负载
*   使用有效负载& JWTSessions 创建一个新的基于令牌的会话。
*   用我们的 JWTSession 令牌[:access]设置一个 cookie
*   呈现最终的 JSON & CSRF 令牌以避免跨源请求漏洞。
*   如果这都不起作用，我们将错误呈现为 JSON

### 签到/签出端点

登录控制器非常类似于注册减去用户的创建，以及如果用户不能成功登录会发生什么。有`create`方法，也有`destroy`方法让用户退出。

```
# app/controllers/signin_controller.rb

aclass SigninController < ApplicationController
  before_action :authorize_access_request!, only: [:destroy]

  def create
    user = User.find_by!(email: params[:email])
    if user.authenticate(params[:password])
      payload = { user_id: user.id }
      session = JWTSessions::Session.new(payload: payload, refresh_by_access_allowed: true)
      tokens = session.login
      response.set_cookie(JWTSessions.access_cookie,
                        value: tokens[:access],
                        httponly: true,
                        secure: Rails.env.production?)
      render json: { csrf: tokens[:csrf] }
    else
      not_authorized
    end
  end

  def destroy
    session = JWTSessions::Session.new(payload: payload)
    session.flush_by_access_payload
    render json: :ok
  end

  private

  def not_found
    render json: { error: "Cannot find email/password combination" }, status: :not_found
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果登录不成功，我们将呈现来自应用程序控制器私有方法的`not_authorized`方法。

### 刷新终点

有时在 web / JS 客户机中存储刷新令牌不够安全。我们可以使用 token 进行操作——只有在您目前看到的`refresh_by_access_allowed`方法的帮助下。这将访问令牌链接到刷新令牌并刷新它。

创建一个`refresh_controller.rb`文件，包括以下内容:

```
# app/controllers/refresh_controller.rb
class RefreshController < ApplicationController
  before_action :authorize_refresh_by_access_request!

  def create
    session = JWTSessions::Session.new(payload: claimless_payload, refresh_by_access_allowed: true)
    tokens = session.refresh_by_access_payload do
      raise JWTSessions::Errors::Unauthorized, "Somethings not right here!"
    end
    response.set_cookie(JWTSessions.access_cookie,
                        value: tokens[:access],
                        httponly: true,
                        secure: Rails.env.production?)
    render json: { csrf: tokens[:csrf] }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我希望只有过期的访问令牌被用于刷新，所以在`refresh_by_access_payload`方法中我们添加了一个异常。我们可以在这里做更多的事情，比如发送通知、刷新会话或者完全忽略它。

JWT 图书馆会自动检查过期声明。为了避免过期的访问令牌，我们可以利用`claimless_payload`方法。

`before_action :authorized_refresh_by_access_request!`用作保护层来保护端点。

### 更新控制器添加访问请求

很像 Devise 的内置`authorize_user!`方法，我们可以在我们的控制器上使用 JWT 的一个。

```
# app/controllers/api/v1/artists_controller.rb

module Api
  module V1
    class ArtistsController < ApplicationController
        before_action :authorize_access_request!, except: [:show, :index]
      ...
      end
   end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

而我们的记录控制者:

```
# app/controllers/api/v1/records_controller.rb

module Api
  module V1
    class RecordsController < ApplicationController
        before_action :authorize_access_request!, except: [:show, :index]
      ...
      end
   end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 创造`current_user`

同样，与 Devise 非常相似，我们希望登录的给定用户有一个助手。我们必须在应用程序控制器内部自己建立这一点。

```
# app/controllers/application_controller.rb

class ApplicationController < ActionController::API
  include JWTSessions::RailsAuthorization
  rescue_from JWTSessions::Errors::Unauthorized, with: :not_authorized

  private

  def current_user
    @current_user ||= User.find(payload['user_id'])
  end

  def not_authorized
    render json: { error: 'Not authorized' }, status: :unauthorized
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 确保我们可以授权某些跨来源请求

Ruby on Rails 在`config/initializers/`中附带了一个`cors.rb`文件。如果你没有看到一个自由创建它。`config/initializers`中的每个文件都会被自动加载。

在该文件中，我们可以指定允许发送/接收请求的特定来源。我们的前端将在不同的本地服务器上运行，所以这是我们可以传递它的地方。当你的应用是活的，你可能会指向一个活的域/子域。

如果您还没有，请确保在您的`Gemfile`中添加/取消注释`rack-cors`，并运行`bundle install`。如果服务器正在运行，请重新启动它。

```
# config/initializers/cors.rb

Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins 'http://localhost:8081'

    resource '*',
      headers: :any,
      credentials: true,
      methods: [:get, :post, :put, :patch, :delete, :options, :head]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

您的源将是您的前端端口正在运行的任何东西。我的情况是`8081`。您可以用逗号分隔多个原点，以实现安全访问。

### 恐鸟路由！

定义了所有端点之后，我们可以将它们添加到 API 名称空间之外的路由中。我当前的路线文件如下所示:

```
Rails.application.routes.draw do
  namespace :api do
    namespace :v1 do
      resources :artists do
        resources :records
      end
    end
  end

  post 'refresh', controller: :refresh, action: :create
  post 'signin', controller: :signin, action: :create
  post 'signup', controller: :signup, action: :create
  delete 'signin', controller: :signin, action: :destroy
end 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在 ruby 的一行代码中定义请求、控制器、URL 路径名和触发动作。爱死了！

### 数据

通过在终端中运行`rails c`，在 rails 控制台中创建一些测试数据。我将随机创建一些艺术家，以便我们在测试前端应用程序时有一些数据可以显示。

```
Artist.create!(name: "AC/DC")
Artist.create!(name: "Jimi Hendrix")
Artist.create!(name: "Alice in Chains")
....
# repeat for however many artists you would like to add 
```

Enter fullscreen mode Exit fullscreen mode

## 前端

让我们采用 Vue.js 作为前端，并处理构建的这一部分。这个应用程序将存在于 rails 应用程序中，但会单独运行。我们可以将源代码放在应用程序的根文件夹中，而不是将它们分开。

我们的工具箱将由 [Node.js](https://nodejs.org/en/) 、 [VueJS CLI](https://cli.vuejs.org/) 、 [Yarn](https://yarnpkg.com/en/) 和 [Axios](https://github.com/axios/axios) 组成。

如果你是 Vue 的新手，一开始可能有点难以理解，但这是一个像 Rails 一样的惯例。与 Angular 或 React 等框架相比，你可以在任何类型的应用程序中使用它的事实说服了我。

在撰写/记录这篇文章的时候，我正在使用 node 的以下版本:

```
$ node -v
v11.4.0
$ yarn -v
1.12.3 
```

Enter fullscreen mode Exit fullscreen mode

#### 安装视图 CLI

```
$ yarn global add @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

`global`意味着这是在系统级安装，而不是直接安装在你的项目`node_modules`中，尽管仍然依赖于它们。

我们可以检查`vue`的版本来验证安装

```
$ vue --version
2.9.6 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建新项目

`cd`进入你的 rails 应用程序，如果你还没有的话，运行下面的:

```
$ vue init webpack recordstore-front 
```

Enter fullscreen mode Exit fullscreen mode

这将引发一系列问题。如果你想继续关注，以下是我的回复

```
? Project name recordstore-front
? Project description A Vue.js front-end app for a Ruby on Rails backend app.
? Author Andy Leverenz <andy@web-crunch.com>
? Vue build standalone
? Install vue-router? Yes
? Use ESLint to lint your code? Yes
? Pick an ESLint preset Standard
? Set up unit tests Yes
? Pick a test runner karma
? Setup e2e tests with Nightwatch? No
? Should we run `npm install` for you after the project has been created? (recommended) yarn 
```

Enter fullscreen mode Exit fullscreen mode

### 启动 app

```
$ cd recordstore-front
$ yarn dev 
```

Enter fullscreen mode Exit fullscreen mode

Webpack 应该在这里施展它的魔法，你应该可以打开浏览器在`localhost:8081`上看到新的 Vue 应用

我的工作目录是这样的:

```
$ tree . -I "node_modules"
.
├── README.md
├── build
│   ├── build.js
│   ├── check-versions.js
│   ├── logo.png
│   ├── utils.js
│   ├── vue-loader.conf.js
│   ├── webpack.base.conf.js
│   ├── webpack.dev.conf.js
│   ├── webpack.prod.conf.js
│   └── webpack.test.conf.js
├── config
│   ├── dev.env.js
│   ├── index.js
│   ├── prod.env.js
│   └── test.env.js
├── index.html
├── package.json
├── src
│   ├── App.vue
│   ├── assets
│   │   └── logo.png
│   ├── components
│   │   └── HelloWorld.vue
│   ├── main.js
│   └── router
│   └── index.js
├── static
├── test
│   └── unit
│   ├── index.js
│   ├── karma.conf.js
│   └── specs
│   └── HelloWorld.spec.js
└── yarn.lock

10 directories, 25 files 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:如果你想让`tree`在你的系统上工作，你需要安装它。我用自制软件运行了以下程序:

```
$ brew install tree 
```

Enter fullscreen mode Exit fullscreen mode

### 添加顺风 CSS

#### 安装顺风 CSS

注意:[一股新的顺风今天以测试形式](https://next.tailwindcss.com)存在。请随意使用它。

我一直喜欢顺风，所以我把它加入到我的项目中。你可以使用一些更完整的东西，比如 Bootstrap，然后简单地通过 CDN 链接它，但是就像我说的，Tailwind 非常好。我用纱
加一下

```
$ yarn add tailwindcss --dev 
```

Enter fullscreen mode Exit fullscreen mode

根据顺风文档，我们需要直接从 node_modules 文件夹
运行`init`命令

```
$ ./node_modules/.bin/tailwind init
   tailwindcss 0.7.3
   ✅ Created Tailwind config file: tailwind.js 
```

Enter fullscreen mode Exit fullscreen mode

一个`tailwind.js`文件应该出现在您的项目中，准备进行配置。

#### 添加一个 CSS 文件

我们的 CSS 将编译下来，但我们需要它有一个地方让它这样做。在我们的`src`目录中添加一个`main.css`文件。

```
src/
 assets/
 components/
 routes/
 App.vue
 main.js
 main.css 
```

Enter fullscreen mode Exit fullscreen mode

Insie `main.css`我们需要以下内容:

```
/* recordstore-frontend/src/main.css */

@tailwind preflight;

@tailwind components;

@tailwind utilities; 
```

Enter fullscreen mode Exit fullscreen mode

在`main.js`中添加以下内容

```
// recordstore-frontend/src/main.js
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'
import './main.css'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

差不多完成了，我们只需要告诉我们的应用程序关于`tailwind.js`

### PostCSS config

我们需要在我们的`.postcss.config.js`文件中将 tailwind 声明为一个插件，并配置 purge css。

```
// recordstore-frontend/.postcss.config.js

module.exports = {
  "plugins": {
    "postcss-import": {},
    "tailwindcss": "./tailwind.js",
    "autoprefixer": {}
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 清理

我将从`src/components`中删除默认的`HelloWorld`组件，并在`main.js`中删除引用它的行

### 安装和配置 Axios

```
$ yarn add axios vue-axios 
```

Enter fullscreen mode Exit fullscreen mode

安装完这两个包后，我将为我们的 axios 内部创建一个家

在`src`内创建一个名为`backend`的新文件夹，在该文件夹内创建一个名为`axios`的文件夹，最后在其中创建一个`index.js`文件。在这里，我们将为 axios 提供一些全局缺省值，并将我们的 API URL 指定为一个常量，它将在每个请求中使用。

```
// recordstore-frontend/src/backend/axios/index.js

import axios from 'axios'

const API_URL = 'http://localhost:3000'

const securedAxiosInstance = axios.create({
  baseURL: API_URL,
  withCredentials: true,
  headers: {
    'Content-Type': 'application/json'
  }
})

const plainAxiosInstance = axios.create({
  baseURL: API_URL,
  withCredentials: true,
  headers: {
    'Content-Type': 'application/json'
  }
})

securedAxiosInstance.interceptors.request.use(config => {
  const method = config.method.toUpperCase()
  if (method !== 'OPTIONS' && method !== 'GET') {
    config.headers = {
      ...config.headers,
      'X-CSRF-TOKEN': localStorage.csrf
    }
  }
  return config
})

securedAxiosInstance.interceptors.response.use(null, error => {
  if (error.response && error.response.config && error.response.status === 401) {
    // If 401 by expired access cookie, we do a refresh request
    return plainAxiosInstance.post('/refresh', {}, { headers: { 'X-CSRF-TOKEN': localStorage.csrf } })
      .then(response => {
        localStorage.csrf = response.data.csrf
        localStorage.signedIn = true
        // After another successfull refresh - repeat original request
        let retryConfig = error.response.config
        retryConfig.headers['X-CSRF-TOKEN'] = localStorage.csrf
        return plainAxiosInstance.request(retryConfig)
      }).catch(error => {
        delete localStorage.csrf
        delete localStorage.signedIn
        // redirect to signin if refresh fails
        location.replace('/')
        return Promise.reject(error)
      })
  } else {
    return Promise.reject(error)
  }
})

export { securedAxiosInstance, plainAxiosInstance } 
```

Enter fullscreen mode Exit fullscreen mode

我们刚才所做的要点是，axios 没有我们所追求的所有逻辑。我们围绕 axios 构建了两个包装器来获得我们想要的东西。我们正在传递凭证，对照来自 Rails 的 CSRF 令牌进行检查。通过这样做，我们可以建立一些逻辑，判断是否满足正确的标准，以便让用户登录和注销，发送正确的数据，等等。

### 主 Vue 配置

`main.js`档是我们的下一站。我们将导入我们的依赖项并进行更多的配置:

```
// recordstore-frontend/src/main.js

import Vue from 'vue'
import App from './App'
import router from './router'
import VueAxios from 'vue-axios'
import { securedAxiosInstance, plainAxiosInstance } from './backend/axios'
import './main.css' // tailwind

Vue.config.productionTip = false
Vue.use(VueAxios, {
  secured: securedAxiosInstance,
  plain: plainAxiosInstance
})

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  securedAxiosInstance,
  plainAxiosInstance,
  components: { App },
  template: '<App/>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意我们如何利用 VueAxios，以及我们新的`secured`和`plain`实例。可以把这些看作是我们将在运行时在 Vue 组件上使用的作用域逻辑。当我们创建每个组件时，您将看到它是如何工作的。

### 路由在前端

我将从我们一直在构建的登录组件开始，但重点关注使用 Vue 路由器的前端路由。

```
// recordstore-frontend/router/index.js

import Vue from 'vue'
import Router from 'vue-router'
import Signin from '@/components/Signin'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Signin',
      component: Signin
    }
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 构建`Signin` Vue 组件

```
<!-- recordstore-frontend/src/components/Signin.vue -->

<template>
  <div class="max-w-sm m-auto my-8">
    <div class="border p-10 border-grey-light shadow rounded">
      <h3 class="text-2xl mb-6 text-grey-darkest">Sign In</h3>
      <form @submit.prevent="signin">
        <div class="text-red" v-if="error">{{ error }}</div> 
        <div class="mb-6">
          <label for="email" class="label">E-mail Address</label>
          <input type="email" v-model="email" class="input" id="email" placeholder="andy@web-crunch.com">
        </div>
        <div class="mb-6">
          <label for="password" class="label">Password</label>
          <input type="password" v-model="password" class="input" id="password" placeholder="Password">
        </div>
        <button type="submit" class="font-sans font-bold px-4 rounded cursor-pointer no-underline bg-green hover:bg-green-dark block w-full py-4 text-white items-center justify-center">Sign In</button> 
        <div class="my-4"><router-link to="/signup" class="link-grey">Sign up</router-link></div>
      </form>
    </div>
  </div> </template> 
<script>
export default {
  name: 'Signin',
  data () {
    return {
      email: '',
      password: '',
      error: ''
    }
  },
  created () {
    this.checkSignedIn()
  },
  updated () {
    this.checkSignedIn()
  },
  methods: {
    signin () {
      this.$http.plain.post('/signin', { email: this.email, password: this.password })
        .then(response => this.signinSuccessful(response))
        .catch(error => this.signinFailed(error))
    },
    signinSuccessful (response) {
      if (!response.data.csrf) {
        this.signinFailed(response)
        return
      }
      localStorage.csrf = response.data.csrf
      localStorage.signedIn = true
      this.error = ''
      this.$router.replace('/records')
    },
    signinFailed (error) {
      this.error = (error.response && error.response.data && error.response.data.error) || ''
      delete localStorage.csrf
      delete localStorage.signedIn
    },
    checkSignedIn () {
      if (localStorage.signedIn) {
        this.$router.replace('/records')
      }
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

该组件是一个基本的登录表单，如果您还没有帐户，可以通过链接访问我们的注册表单。我们利用 Tailwind 设计风格，利用 Vue 设计功能。在`script`块中，我检查用户是否已经在组件创建时登录，如果是，他们将重定向到`/records`，如果不是，他们将看到这个表单。我们实际的`signin`方法在表单提交被触发时执行 post 请求。

### `Signup`分量

```
<!-- recordstore-frontend/src/components/Signup.vue -->

<template>
  <div class="max-w-sm m-auto my-8">
    <div class="border p-10 border-grey-light shadow rounded">
      <h3 class="text-2xl mb-6 text-grey-darkest">Sign Up</h3>
      <form @submit.prevent="signup">
        <div class="text-red" v-if="error">{{ error }}</div> 
        <div class="mb-6">
          <label for="email" class="label">E-mail Address</label>
          <input type="email" v-model="email" class="input" id="email" placeholder="andy@web-crunch.com">
        </div> 
        <div class="mb-6">
          <label for="password" class="label">Password</label>
          <input type="password" v-model="password" class="input" id="password" placeholder="Password">
        </div> 
        <div class="mb-6">
          <label for="password_confirmation" class="label">Password Confirmation</label>
          <input type="password" v-model="password_confirmation" class="input" id="password_confirmation" placeholder="Password Confirmation">
        </div>
        <button type="submit" class="font-sans font-bold px-4 rounded cursor-pointer no-underline bg-green hover:bg-green-dark block w-full py-4 text-white items-center justify-center">Sign Up</button> 
        <div class="my-4"><router-link to="/" class="link-grey">Sign In</router-link></div>
      </form>
    </div>
  </div> </template> 
<script>
export default {
  name: 'Signup',
  data () {
    return {
      email: '',
      password: '',
      password_confirmation: '',
      error: ''
    }
  },
  created () {
    this.checkedSignedIn()
  },
  updated () {
    this.checkedSignedIn()
  },
  methods: {
    signup () {
      this.$http.plain.post('/signup', { email: this.email, password: this.password, password_confirmation: this.password_confirmation })
        .then(response => this.signupSuccessful(response))
        .catch(error => this.signupFailed(error))
    },
    signupSuccessful (response) {
      if (!response.data.csrf) {
        this.signupFailed(response)
        return
      }

      localStorage.csrf = response.data.csrf
      localStorage.signedIn = true
      this.error = ''
      this.$router.replace('/records')
    },
    signupFailed (error) {
      this.error = (error.response && error.response.data && error.response.data.error) || 'Something went wrong'
      delete localStorage.csrf
      delete localStorage.signedIn
    },
    checkedSignedIn () {
      if (localStorage.signedIn) {
        this.$router.replace('/records')
      }
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

对于`Signup.vue`组件来说，大部分逻辑是相同的。这里我们介绍一个新的领域和不同的`POST`路线上的`signup`路径。这指向我们的 rails 应用程序上的`/signup`，如`config/routes.rb`中所定义的。

### `Header.vue`分量

我想在我们的路由器上有一个全局头组件。为此，我们需要将它导入到我们的主`App.vue`文件中。最后，`Header.vue`文件看起来如下:

```
<!-- recordstore-frontend/src/components/Header.vue -->

<template>
  <header class="bg-grey-lighter py-4">
    <div class="container m-auto flex flex-wrap items-center justify-end">
      <div class="flex-1 flex items-center">
        record vinyl<path d="M23.938 10.773a11.915 11.915 0 0 0-2.333-5.944 12.118 12.118 0 0 0-1.12-1.314A11.962 11.962 0 0 0 12 0C5.373 0 0 5.373 0 12s5.373 12 12 12 12-5.373 12-12c0-.414-.021-.823-.062-1.227zM12 16a4 4 0 1 1 0-8 4 4 0 0 1 0 8zm0-5a1 1 0 1 0 0 2 1 1 0 0 0 0-2z"></path> 
        <a href="/" class="uppercase text-sm font-mono pl-4 font-semibold no-underline text-indigo-dark hover:text-indigo-darker">Record Store</a>
      </div>
      <div>
        <router-link to="/" class="link-grey px-2 no-underline" v-if="!signedIn()">Sign in</router-link>
        <router-link to="/signup" class="link-grey px-2 no-underline" v-if="!signedIn()">Sign Up</router-link>
        <router-link to="/records" class="link-grey px-2 no-underline" v-if="signedIn()">Records</router-link>
        <router-link to="/artists" class="link-grey px-2 no-underline" v-if="signedIn()">Artists</router-link>
        <a href="#" @click.prevent="signOut" class="link-grey px-2 no-underline" v-if="signedIn()">Sign out</a>
      </div>
    </div>
  </header> </template> 
<script>
export default {
  name: 'Header',
  created () {
    this.signedIn()
  },
  methods: {
    setError (error, text) {
      this.error = (error.response && error.response.data && error.response.data.error) || text
    },
    signedIn () {
      return localStorage.signedIn
    },
    signOut () {
      this.$http.secured.delete('/signin')
        .then(response => {
          delete localStorage.csrf
          delete localStorage.signedIn
          this.$router.replace('/')
        })
        .catch(error => this.setError(error, 'Cannot sign out'))
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这个文件被导入到这里:

```
<!-- src/components/App.vue-->
<template>
  <div id="app">
    <Header/>
    <router-view></router-view>
  </div> </template> 
<script>
import Header from './components/Header.vue'

export default {
  name: 'App',
  components: {
    Header
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 艺术家

我们在数据库中已经有数据，所以让我们从我们的`Artists.vue`组件
开始

```
<!-- recordstore-frontend/src/components/artists/Artists.vue -->

<template>
  <div class="max-w-md m-auto py-10">
    <div class="text-red" v-if="error">{{ error }}</div>
    <h3 class="font-mono font-regular text-3xl mb-4">Add a new artist</h3>
    <form action="" @submit.prevent="addArtist">
      <div class="mb-6">
        <input class="input"
          autofocus autocomplete="off"
          placeholder="Type an arist name"
          v-model="newArtist.name" />
      </div>
      <input type="submit" value="Add Artist" class="font-sans font-bold px-4 rounded cursor-pointer no-underline bg-green hover:bg-green-dark block w-full py-4 text-white items-center justify-center" />
    </form> 
    <hr class="border border-grey-light my-6" />

    <ul class="list-reset mt-4">
      <li class="py-4" v-for="artist in artists" :key="artist.id" :artist="artist">

        <div class="flex items-center justify-between flex-wrap">
          <p class="block flex-1 font-mono font-semibold flex items-center ">
            music artist<path d="M15.75 8l-3.74-3.75a3.99 3.99 0 0 1 6.82-3.08A4 4 0 0 1 15.75 8zm-13.9 7.3l9.2-9.19 2.83 2.83-9.2 9.2-2.82-2.84zm-1.4 2.83l2.11-2.12 1.42 1.42-2.12 2.12-1.42-1.42zM10 15l2-2v7h-2v-5z"></path>
            {{ artist.name }}
          </p> 
          <button class="bg-tranparent text-sm hover:bg-blue hover:text-white text-blue border border-blue no-underline font-bold py-2 px-4 mr-2 rounded"
          @click.prevent="editArtist(artist)">Edit</button> 
          <button class="bg-transprent text-sm hover:bg-red text-red hover:text-white no-underline font-bold py-2 px-4 rounded border border-red"
         @click.prevent="removeArtist(artist)">Delete</button>
        </div> 
        <div v-if="artist == editedArtist">
          <form action="" @submit.prevent="updateArtist(artist)">
            <div class="mb-6 p-4 bg-white rounded border border-grey-light mt-4">
              <input class="input" v-model="artist.name" />
              <input type="submit" value="Update" class=" my-2 bg-transparent text-sm hover:bg-blue hover:text-white text-blue border border-blue no-underline font-bold py-2 px-4 rounded cursor-pointer">
            </div>
          </form>
        </div>
      </li>
    </ul>
  </div> </template> 
<script>
export default {
  name: 'Artists',
  data () {
    return {
      artists: [],
      newArtist: [],
      error: '',
      editedArtist: ''
    }
  },
  created () {
    if (!localStorage.signedIn) {
      this.$router.replace('/')
    } else {
      this.$http.secured.get('/api/v1/artists')
        .then(response => { this.artists = response.data })
        .catch(error => this.setError(error, 'Something went wrong'))
    }
  },
  methods: {
    setError (error, text) {
      this.error = (error.response && error.response.data && error.response.data.error) || text
    },
    addArtist () {
      const value = this.newArtist
      if (!value) {
        return
      }
      this.$http.secured.post('/api/v1/artists/', { artist: { name: this.newArtist.name } })

        .then(response => {
          this.artists.push(response.data)
          this.newArtist = ''
        })
        .catch(error => this.setError(error, 'Cannot create artist'))
    },
    removeArtist (artist) {
      this.$http.secured.delete(`/api/v1/artists/${artist.id}`)
        .then(response => {
          this.artists.splice(this.artists.indexOf(artist), 1)
        })
        .catch(error => this.setError(error, 'Cannot delete artist'))
    },
    editArtist (artist) {
      this.editedArtist = artist
    },
    updateArtist (artist) {
      this.editedArtist = ''
      this.$http.secured.patch(`/api/v1/artists/${artist.id}`, { artist: { title: artist.name } })
        .catch(error => this.setError(error, 'Cannot update artist'))
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这个组件负责几件事情。我意识到这可以进一步浓缩成多个组件，但由于时间的原因，我包含了所有内容。在这个文件中，我们有一个表单、艺术家列表和编辑艺术家时的更新表单。我们将遍历来自 Rails 应用程序的数据，以显示数据库中的数据，并使用 Vue 通过 JavaScript 和 Axios 执行基本的 CRUD 操作。

请注意我是如何在许多 axios 请求中指向`api/v1/artists`的。这是我们之前在 rails 应用程序上创建的完全有效的名称空间。很酷的东西！

### `Records.vue`分量

```
<!-- recordstore-frontend/src/components/artists/Records.vue -->

<template>
  <div class="max-w-md m-auto py-10">
    <div class="text-red" v-if="error">{{ error }}</div>
    <h3 class="font-mono font-regular text-3xl mb-4">Add a new record</h3>
    <form action="" @submit.prevent="addRecord">
      <div class="mb-6">
        <label for="record_title" class="label">Title</label>
        <input
          id="record_title"
          class="input"
          autofocus autocomplete="off"
          placeholder="Type a record name"
          v-model="newRecord.title" />
      </div> 
      <div class="mb-6">
        <label for="record_year" class="label">Year</label>
        <input
          id="record_year"
          class="input"
          autofocus autocomplete="off"
          placeholder="Year"
          v-model="newRecord.year"
        />
       </div> 
      <div class="mb-6">
        <label for="artist" class="label">Artist</label>
        <select id="artist" class="select" v-model="newRecord.artist">
          <option disabled value="">Select an artist</option>
          <option :value="artist.id" v-for="artist in artists" :key="artist.id">{{ artist.name }}</option>
        </select>
        <p class="pt-4">Don't see an artist? <router-link class="text-grey-darker underline" to="/artists">Create one</router-link></p>
       </div>

      <input type="submit" value="Add Record" class="font-sans font-bold px-4 rounded cursor-pointer no-underline bg-green hover:bg-green-dark block w-full py-4 text-white items-center justify-center" />
    </form>

    <hr class="border border-grey-light my-6" />

    <ul class="list-reset mt-4">
      <li class="py-4" v-for="record in records" :key="record.id" :record="record">

        <div class="flex items-center justify-between flex-wrap">
          <div class="flex-1 flex justify-between flex-wrap pr-4">
            <p class="block font-mono font-semibold flex items-center">
              record vinyl<path d="M23.938 10.773a11.915 11.915 0 0 0-2.333-5.944 12.118 12.118 0 0 0-1.12-1.314A11.962 11.962 0 0 0 12 0C5.373 0 0 5.373 0 12s5.373 12 12 12 12-5.373 12-12c0-.414-.021-.823-.062-1.227zM12 16a4 4 0 1 1 0-8 4 4 0 0 1 0 8zm0-5a1 1 0 1 0 0 2 1 1 0 0 0 0-2z" ></path>
              {{ record.title }} &mdash; {{ record.year }}
            </p>
            <p class="block font-mono font-semibold">{{ getArtist(record) }}</p>
          </div>
          <button class="bg-transparent text-sm hover:bg-blue hover:text-white text-blue border border-blue no-underline font-bold py-2 px-4 mr-2 rounded"
          @click.prevent="editRecord(record)">Edit</button>

          <button class="bg-transparent text-sm hover:bg-red text-red hover:text-white no-underline font-bold py-2 px-4 rounded border border-red"
         @click.prevent="removeRecord(record)">Delete</button>
        </div>

        <div v-if="record == editedRecord">
          <form action="" @submit.prevent="updateRecord(record)">
            <div class="mb-6 p-4 bg-white rounded border border-grey-light mt-4">

              <div class="mb-6">
                <label class="label">Title</label>
                <input class="input" v-model="record.title" />
              </div>

              <div class="mb-6">
                <label class="label">Year</label>
                <input class="input" v-model="record.year" />
              </div>

              <div class="mb-6">
                <label class="label">Artist</label>
                <select id="artist" class="select" v-model="record.artist">
                  <option :value="artist.id" v-for="artist in artists" :key="artist.id">{{ artist.name }}</option>
                </select>
              </div>

              <input type="submit" value="Update" class="bg-transparent text-sm hover:bg-blue hover:text-white text-blue border border-blue no-underline font-bold py-2 px-4 mr-2 rounded">
            </div>
          </form>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'Records',
  data () {
    return {
      artists: [],
      records: [],
      newRecord: [],
      error: '',
      editedRecord: '' }
  },
  created () {
    if (!localStorage.signedIn) {
      this.$router.replace('/')
    } else {
      this.$http.secured.get('/api/v1/records')
        .then(response => { this.records = response.data })
        .catch(error => this.setError(error, 'Something went wrong'))

      this.$http.secured.get('/api/v1/artists')
        .then(response => { this.artists = response.data })
        .catch(error => this.setError(error, 'Something went wrong'))
    }
  },
  methods: {
    setError (error, text) {
      this.error = (error.response && error.response.data && error.response.data.error) || text
    },
    getArtist (record) {
      const recordArtistValues = this.artists.filter(artist => artist.id === record.artist_id)
      let artist

      recordArtistValues.forEach(function (element) {
        artist = element.name
      })

      return artist
    },
    addRecord () {
      const value = this.newRecord
      if (!value) {
        return
      }
      this.$http.secured.post('/api/v1/records/', { record: { title: this.newRecord.title, year: this.newRecord.year, artist_id: this.newRecord.artist } })

        .then(response => {
          this.records.push(response.data)
          this.newRecord = '' })
        .catch(error => this.setError(error, 'Cannot create record'))
    },
    removeRecord (record) {
      this.$http.secured.delete(`/api/v1/records/${record.id}`)
        .then(response => {
          this.records.splice(this.records.indexOf(record), 1)
        })
        .catch(error => this.setError(error, 'Cannot delete record'))
    },
    editRecord (record) {
      this.editedRecord = record
    },
    updateRecord (record) {
      this.editedRecord = '' this.$http.secured.patch(`/api/v1/records/${record.id}`, { record: { title: record.title, year: record.year, artist_id: record.artist } })
        .catch(error => this.setError(error, 'Cannot update record'))
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

`Records.vue`组件与`Artists.vue`组件非常相似，因为相同的基本 CRUD 操作完全有效。我介绍了艺术家记录与一个新的`select`字段的关系，它从我们的后端抓取数据，并保存它一旦一个新的记录被保存。我们循环通过`Record`和`Artist`数据来获取必要的 id 和字段，以便正确地保存、编辑、更新和删除字段。

### 接下来去哪里？

我们的应用程序还远未完成，但它运行良好。我们有基于 JWT 的认证和一个完整的基于 CRUD 的 Vue 应用程序在前端工作。我们的后端正在以我们预期的方式与前端对话 [![🎉](img/d76286464e481ca64023a3b418630adf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3jJxnDly--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/1f389.png) 。我在处理`location:`属性的 Rails `artists_controller.rb`和`records_controller.rb`文件中发现了最后一个 bug。通常这些会存在，但我已经删除了它们，因为一个奇怪的命名空间问题，我不太明白。也许你知道解决办法？

从这里，我邀请你扩展应用程序和/或在你自己的项目中使用它作为指南。我从这个版本中学到了很多。我不得不承认，这是迄今为止我接手的最难的一个。希望这足以向您展示一种在现代前端框架中使用 Ruby on Rails 的新方法。

### 到目前为止的级数

*   [让我们构建:使用 Ruby on Rails——简介](https://web-crunch.com/lets-build-with-ruby-on-rails-introduction/)
*   [让我们构建:使用 Ruby on Rails——安装](https://web-crunch.com/lets-build-with-ruby-on-rails-installation/)
*   [让我们构建:用 Ruby on Rails——带评论的博客](https://web-crunch.com/lets-build-with-ruby-on-rails-blog-with-comments)
*   [让我们构建:用 Ruby on Rails——Twitter 的克隆版](https://web-crunch.com/lets-build-with-ruby-on-rails-a-twitter-clone/)
*   [让我们用 Ruby on Rails 构建一个可移植的克隆版本](https://web-crunch.com/lets-build-dribbble-clone-with-ruby-on-rails/)
*   [让我们用 Ruby on Rails 构建——项目管理应用](https://web-crunch.com/lets-build-with-ruby-on-rails-project-management-app/)
*   [让我们构建:使用 Ruby on Rails——论坛](https://web-crunch.com/lets-build-with-ruby-on-rails-discussion-forum/)
*   [让我们构建:使用 Ruby on Rails——将应用部署到 Heroku](https://web-crunch.com/lets-build-with-ruby-on-rails-deploying-an-app-to-heroku/)
*   [让我们建立:用 Ruby on Rails——电子商务音乐商店](https://web-crunch.com/ruby-on-rails-ecommerce-music-shop/)
*   [让我们构建:使用 Ruby on Rails——带 Stripe 订阅支付的图书图书馆应用](https://web-crunch.com/lets-build-ruby-on-rails-book-library-stripe-subscription-payments/)
*   [让我们构建:用 Ruby on Rails——带应用内消息的交易应用](https://web-crunch.com/lets-build-with-ruby-on-rails-trade-app-in-app-messaging/)
*   [让我们来构建:使用 Ruby on Rails——多租户锻炼追踪应用](https://web-crunch.com/lets-build-with-ruby-on-rails-multitenancy-workout-tracker-app/)
*   [让我们构建:使用 Ruby on Rails——支付调度应用](https://dev.to/justalever/lets-build-with-ruby-on-rails--event-scheduling-app-with-payments-2h7d-temp-slug-7516635)

### 无耻的塞时间

[![](img/f572d5df905b6366d1201f6c51d1b8c2.png)](http://hellorails.io)

我有一门新课程叫做 [Hello Rails](https://hellorails.io) 。Hello Rails 是一门现代课程，旨在帮助您快速开始使用和理解 Ruby on Rails。如果你是 Ruby 或 Ruby on Rails 的新手，我邀请你去看看这个网站。该课程将与这些构建非常相似，但是是一个超级深入的版本，具有更现实的目标和可交付成果。[💌得到通知](https://hellorails.io/)！

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。

帖子 [Ruby on Rails API with Vue.js](https://web-crunch.com/ruby-on-rails-api-vue-js/) 首先出现在 [Web-Crunch](https://web-crunch.com) 上。
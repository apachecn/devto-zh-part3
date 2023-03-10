# 用 Athena & Granite 创建 JSON API

> 原文：<https://dev.to/blacksmoke16/creating-a-json-api-with-athena--granite-510i>

**更新:**2019 年 4 月 22 日为雅典娜版`0.6.0`
**更新:**2019 年 11 月 24 日为雅典娜版`0.7.0`和水晶`0.31.1`
**更新:**2020 年 2 月 7 日为雅典娜版`0.8.0`
**更新:**2020 年 6 月 10 日为雅典娜版`0.9.0`
**更新:**2020 年 7 月 11 日为雅典娜版

# 雅典娜

## [T1】简介](#intro)

几个月前(一年前的这个时候)我开始创建一个新的 web 框架，但是有一些关键的不同。我想要一个允许路由的动作容易被记录、测试和灵活的东西。我也不想在每条路由中一直手动处理将 route/query/body 参数转换成预期类型的样板文件。最后，我想利用 Crystal 的注释为定义路由提供一个简单而灵活的 DSL。

考虑到我经历过的所有框架，Symfony 给了我很大的帮助。这个想法的结果是[雅典娜](https://github.com/athena-framework/athena)。

现在，我想写一篇博文，展示一个带有 Athena 的 JSON API 在实际应用中的样子，而不仅仅是一般的文档。

## 教程

本教程将*而不是*涵盖任何前端工作(用户界面/UX)。它只是假设传入 API 的请求来自前端 JS 框架或其他东西。请求是 JSON 的，所以它与框架无关。

我将采取一种相当缓慢的方法，使本教程既适用于新手也适用于老手。

### 要求

*   水晶安装在你的机器上。(截至本文撰写时，最新版本为`0.35.1`)
*   您首选的 HTTP 客户端。我会用`cURL`
*   您偏好的 IDE/编辑器。
*   你的偏好。我将使用 Postgres。
    *   (可选)Docker。是我运行数据库的工具。

### 议程

增加以下功能:

*   注册/登录
    *   验证用户
*   创建/阅读/更新/删除文章
    *   验证文章

### 搭建博客

我们可以利用 crystal binary 来构建我们的应用程序。这将创建一个具有给定名称的新目录，其中包含 crystal 应用程序所需的文件；包括基本的目录结构，一个`shard.yml`，一个`.gitignore`，都是自动为我们生成的。我将继续在我的主目录中创建它，然后将`cd`放入新创建的目录中；为下一步做好准备。

```
$ cd ~/
$ crystal init app blog
$ cd ./blog 
```

Enter fullscreen mode Exit fullscreen mode

### 依赖关系

我将使用[花岗岩](https://github.com/amberframework/granite)作为我们与雅典娜配对的选择。首先将以下内容添加到您的`shard.yml`文件中。

我还将要求`jwt` shard 生成 jwt，用作我们选择的认证方法。

**注意:**我正在使用 Postgres，因此我正在安装 PG shard 以便与 Granite 一起使用。如果您使用另一个 DB 适配器，您将需要安装那个 shard。

```
dependencies:
  granite:
    github: amberframework/granite
    version: 0.21.1
  pg:
    github: will/crystal-pg
    version: 0.21.1
  athena:
    github: athena-framework/athena
    version: 0.10.0
  jwt:
    github: crystal-community/jwt
    version: 1.4.2
  assert:
    github: blacksmoke16/assert
    version: 0.2.0 
```

Enter fullscreen mode Exit fullscreen mode

然后安装所需的依赖项:

```
$ shards install 
```

Enter fullscreen mode Exit fullscreen mode

### 定义我们的型号&控制器

我们的博客将有两个模型和两个数据库表:

1.  用户——存储注册了我们博客的用户
2.  文章-用户撰写的博客文章。

出于本教程的目的，我将在 Postgres 中执行原始 SQL 来创建表。有一些迁移碎片可以自动完成这项工作；可能是未来的迭代。

首先，让我们创建一个新的模式来保存我们的表，并让我们的 DB 用户能够访问该数据库。

我将使用 docker 运行我的 PG 数据库，包含以下组合文件:

```
version: '3.1'
services:
  pg:
    image: postgres:11.2-alpine
    container_name: pg
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_USER=blog_user
      - POSTGRES_PASSWORD=mYAw3s0meB!log
      - POSTGRES_DB=blog
    volumes:
      - pg-data:/var/lib/postgresql/data

volumes:
  pg-data: 
```

Enter fullscreen mode Exit fullscreen mode

```
CREATE SCHEMA "blog";
ALTER ROLE "blog_user" SET SEARCH_PATH TO "blog"; 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**使用 Docker 是可选的，只要你有一个 DB 连接就可以了。

既然已经安装了我们的依赖项，我们需要它们，并在我们的`blog.cr`文件中设置我们的 DB 连接。它应该类似于:

```
# Register an adapter to connect to our DB
Granite::Connections << Granite::Adapter::Pg.new(name: "my_blog", url: "postgres://blog_user:mYAw3s0meB!log@localhost:5432/blog?currentSchema=blog")

# Require some standard library things we'll need
require "crypto/bcrypt/password"

# Require our ORM and DB adapter
require "granite"
require "granite/adapter/pg"

# Require Athena
require "athena"

# This will eventually be replaced by Athena's validation component
require "assert"

# Require JWT shard
require "jwt"

module Blog
  VERSION = "0.10.0"

  # Runs the HTTP server with the default settings
  ART.run
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 用户模式

接下来，让我们考虑我们的用户需要哪些列:

*   id : Int64 -自动生成的 id，用于唯一标识每个用户
*   first_name : String -用户的名字
*   last_name : String -用户的姓氏
*   email:字符串-用户的电子邮件，也用于登录。对于每个用户应该是唯一的
*   password : String -用户的密码
*   created_at : Time -创建用户的时间
*   updated_at : Time -用户更新的时间(名称/电子邮件/密码更改)
*   deleted_at : Time -删除用户的时间

将此转换成 SQL 语句:

```
CREATE TABLE "blog"."users"
(
    "id"         BIGSERIAL NOT NULL PRIMARY KEY,
    "first_name"  TEXT      NOT NULL,
    "last_name"  TEXT      NOT NULL,
    "email"      TEXT      NOT NULL,
    "password"   TEXT      NOT NULL,
    "created_at" TIMESTAMP NOT NULL DEFAULT NOW(),
    "updated_at" TIMESTAMP NOT NULL DEFAULT NOW(),
    "deleted_at" TIMESTAMP NULL
); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的表已经创建好了，我们可以继续创建我们的第一个模型。我将首先创建一个新的目录来存储我们的模型；以及创建我们的`user.cr`文件。

```
$ mkdir ./src/models
$ touch ./src/models/user.cr 
```

Enter fullscreen mode Exit fullscreen mode

使用我们的列表作为参考，我将创建用户模型。

```
@[ASRA::ExclusionPolicy(:all)]
class Blog::Models::User < Granite::Base
  include ASR::Serializable
  include Assert

  connection "my_blog"
  table "users"

  column id : Int64, primary: true
  column first_name : String
  column last_name : String
  column email : String
  column password : String
  column created_at : Time
  column updated_at : Time
  column deleted_at : Time?
end 
```

Enter fullscreen mode Exit fullscreen mode

需要指出几件事:

*   `connection`宏定义了这个模型应该使用哪个适配器来连接数据库。传递给宏的值与在`blog.cr`中注册 DB 适配器时设置的名称相同。
*   我添加了一个`Models`名称空间，只是为了添加一些组织并帮助分离文档。因此，请务必在`blog.cr`的`Blog`模块中添加一个`include Models`以及一个`require "./models/*"`。
*   稍后我们将回到`include ASR::Serializable`和`include Assert`的话题。

让我们来回顾一下。到目前为止我们做了什么？

1.  注册我们的适配器以连接到我们的数据库。
2.  需要所有需要的碎片。
3.  创建了我们的`User`模型和表格。

现在所有这些开始的步骤都完成了，我们现在可以创建我们的`user_controller`来保存我们的路由以创建一个用户。

#### 用户控制器

与前面类似，我将创建一个新目录来保存我们的控制器，并创建我们的`user_controller.cr`文件。

```
$ mkdir ./src/controllers
$ touch ./src/controllers/user_controller.cr 
```

Enter fullscreen mode Exit fullscreen mode

```
class Blog::Controllers::UserController < ART::Controller
end 
```

Enter fullscreen mode Exit fullscreen mode

我还命名了控制器，所以一定要在你的`blog.cr`文件中有`include Controllers`和`require "./controllers/*"`。我将创建的第一个端点将是一个`POST /user`端点，以便向我们的数据库添加用户。为此，将以下代码添加到`UserController`类中。

```
@[ART::Post("user")]
def new_user(user : Blog::Models::User) : Blog::Models::User
  user
end 
```

Enter fullscreen mode Exit fullscreen mode

Athena 的路线定义与您可能习惯的路线定义有些不同。雅典娜使用水晶的注释。顶部注释用路径`/user`定义了一个`POST`端点，并将路由的动作设置为`new_user`方法。然而，Athena 不能够自动提供复杂类型，比如我们的`User`对象给我们的动作；我们必须利用一个`ParamConverter`来完成这个任务。`ParamConverter`允许定义定制逻辑，负责将请求中的数据转换成另一种类型供操作使用。在这个例子中，将请求体转换成我们的`User`模型的一个实例；让我们现在就开始创建它。

```
# Define our converter, register it as a service, inheriting from the base interface struct.
@[ADI::Register]
struct Blog::Converters::RequestBody < ART::ParamConverterInterface
  # Define a customer configuration for this converter.
  # This allows us to provide a `model` field within the annotation
  # in order to define _what_ model should be used on deserialization.
  configuration model : Granite::Base.class

  # :inherit:
  def apply(request : HTTP::Request, configuration : Configuration) : Nil
    # Be sure to handle any possible exceptions here to return more helpful errors to the client.
    raise ART::Exceptions::BadRequest.new "Request body is empty" unless body = request.body.try &.gets_to_end

    # Deserialize the object, based on the type provided in the annotation
    obj = configuration.model.from_json body

    # Run the validations
    obj.validate!

    # Add the resolved object to the request's attributes
    request.attributes.set configuration.name, obj, configuration.model
  rescue ex : Assert::Exceptions::ValidationError
    # Raise a 422 error if the object failed its validations
    raise ART::Exceptions::UnprocessableEntity.new ex.to_s
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

Athena 使用了很多接口，以使类型更加 DI 友好，更容易测试，等等。该接口只需要一个方法`apply(request : HTTP::Request, configuration : Configuration) : Nil`，其唯一目的是基于所提供的*配置*，将转换逻辑应用于所提供的*请求*。转换器只是一个继承自`ART::ParamConverterInterface`的结构。稍后我们将讨论`@[ADI::Register]`注释。

我们的`RequestBody`转换器也利用了 Athena 的错误处理系统。Athena 提供了一组继承自`ART::Exceptions::HTTPException`的常见 HTTP 异常，这种类型的子节点被认为映射到一个`HTTP`错误；还可以添加自定义子项。除非像平常一样被营救，否则不要返回 500。默认情况下，异常是 JSON 序列化的，但是如果需要的话可以定制。

最常见的情况是，参数转换器希望将转换后的值存储在请求的属性中。属性保存在一个 [ART::ParameterBag](https://athena-framework.github.io/athena/Athena/Routing/ParameterBag.html) 实例中。`ParameterBag`是存储键/值对的容器；它可用于在请求的上下文中存储任意数据。默认情况下，Athena 会在请求的属性中查找与 action 参数同名的值；这包括路径/查询参数或存储在其中的任何自定义值。

现在我们已经定义了转换器，我们可以继续在我们的`new_user`路线上实现它。只需应用注释，第一个参数映射到转换器应该应用的动作参数的名称，而`converter`命名参数接受我们想要使用的特定的`ParamConverter.class`。也可以定义该转换器的任何额外配置。在这种情况下，我们指定希望将请求体反序列化为一个`User`对象。

由于 param converter 提供了一个实际的`User`模型对象，我们可以在操作中调用`.save`来保存给定的对象，然后返回用户对象。我们还可以使用`ART::View`注释，通过让操作返回一个`201 Created`状态代码而不是标准的`200 OK`，使我们的操作更加 REST 友好。我们的`new_user`动作现在看起来像:

```
@[ART::Post("user")]
@[ART::View(status: :created)]
@[ART::ParamConverter("user", converter: Blog::Converters::RequestBody, model: Blog::Models::User)]
def new_user(user : Blog::Models::User) : Blog::Models::User
  user.save
  user
end 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们现在能够通过我们的`POST /user`端点创建用户。让我们试一试。

启动 HTTP 服务器。

```
$ crystal ./src/blog.cr 
```

Enter fullscreen mode Exit fullscreen mode

让我们注册一个用户:

```
curl --request POST \
  --url http://localhost:3000/user \
  --header 'content-type: application/json' \
  --data '{
  "first_name": "foo",
  "last_name": "bar",
  "email": "fakeemail@domain.com",
  "password": "monkey123"
}' 
```

Enter fullscreen mode Exit fullscreen mode

成功！用户被持久化，现在有了一个 id 和时间戳。

```
{  "id":  1,  "first_name":  "foo",  "last_name":  "bar",  "email":  "fakeemail@domain.com",  "password":  "monkey123",  "created_at":  "2020-07-11T22:42:33Z",  "updated_at":  "2020-07-11T22:42:33Z"  } 
```

Enter fullscreen mode Exit fullscreen mode

然而，当前的实现存在一些问题。

1.  什么可以阻止某人将其密码/姓名/电子邮件设置为空字符串？

    1.  当然，我们可以依靠前端进行验证，但这很容易绕过。
2.  我们可能不应该以明文显示用户的密码，更不用说在响应中返回它了。

3.  如果有人用同一封邮件发了两次帖子会怎么样？因为我们使用电子邮件作为我们面向用户的唯一标识符，我们应该处理这个。

让我们回到我们的`User`模型来解决这些问题。这就是`ASR::Serializable`和`Assert`派上用场的地方。

> **注:** `Assert`最终将作为独立组件移入`athena-framework`组织进行验证。

我们可以更新我们的模型，看起来像:

```
@[ASRA::ExclusionPolicy(:all)]
class Blog::Models::User < Granite::Base
  include ASR::Serializable
  include Assert

  connection "my_blog"
  table "users"

  has_many articles : Article

  @[ASRA::Expose]
  @[ASRA::ReadOnly]
  column id : Int64, primary: true

  @[ASRA::Expose]
  @[Assert::NotBlank]
  column first_name : String

  @[ASRA::Expose]
  @[Assert::NotBlank]
  column last_name : String

  @[ASRA::Expose]
  @[Assert::NotBlank]
  @[Assert::Email(mode: :html5)]
  column email : String

  @[ASRA::IgnoreOnSerialize]
  @[Assert::Size(Range(Int32, Int32), range: 8..25, min_message: "Your password is too short", max_message: "Your password is too long")]
  column password : String

  @[ASRA::Expose]
  @[ASRA::ReadOnly]
  column created_at : Time

  @[ASRA::Expose]
  @[ASRA::ReadOnly]
  column updated_at : Time

  column deleted_at : Time?
end 
```

Enter fullscreen mode Exit fullscreen mode

另外，将您的`new_user`操作更新为:

```
@[ART::Post("user")]
@[ART::ParamConverter("user", converter: Blog::Converters::RequestBody, model: Blog::Models::User)]
def new_user(user : Blog::Models::User) : Blog::Models::User
  raise ART::Exceptions::Conflict.new "A user with this email already exists." if User.exists? email: user.email
  user.save
  user
end 
```

Enter fullscreen mode Exit fullscreen mode

通过这些更改，我们解决了之前发现的问题 1 和 3。我们将在解释发生了什么后不久解决问题 2。

首先，我们包含了`ASR::Serializable`和`Assert`，以便添加增强的序列化和断言功能。接下来，我们给我们的`User`模型的类添加了一个注释。`@[ASRA::ExclusionPolicy(:all)]`。这个注释改变了模型的整体序列化策略。在这种情况下，它将*只*序列化通过`@[ASRA::Expose]`公开的字段。这很方便，特别是对于较大的模型，可以更容易地只序列化预期的字段，并防止序列化通过其他模块包含的其他实例变量。

我还向`password`属性添加了`@[ASRA::IgnoreOnSerialize]`注释。这告诉 Athena 的序列化器密码可以被反序列化，但是*不应该被序列化。*

接下来，我添加了注释来公开我们希望返回的字段。我还向`id`字段添加了一个`@[ASRA::ReadOnly]`并暴露了时间戳字段，这防止了该属性被反序列化；因为它是由数据库管理的。

我还向我们希望验证的字段添加了额外的注释。我断言:

*   `first_name`字段不为空
*   `last_name`字段不为空
*   `email`不为空，是有效的电子邮件
*   `password`的长度在 8 到 25 个字符之间

最后，我在`UserController`中添加了一个`User.exists? email: user.email`查询来检查给定电子邮件中是否存在用户，如果存在，则抛出一个适当的错误消息。

让我们来测试一下！

```
curl --request POST \
  --url http://localhost:3000/user \
  --header 'content-type: application/json' \
  --data '{
  "first_name": "foo",
  "last_name": "",
  "email": "",
  "password": "monkey"
}' 
```

Enter fullscreen mode Exit fullscreen mode

产生以下响应:

```
{  "code":  422,  "message":  "Validation tests failed: 'last_name' should not be blank, 'email' is not a valid email address, 'email' should not be blank, Your password is too short"  } 
```

Enter fullscreen mode Exit fullscreen mode

Tada！轻松验证您的模型。此外，尝试向用户发布之前使用过的电子邮件现在会产生此错误

```
{  "code":  409,  "message":  "A user with this email already exists."  } 
```

Enter fullscreen mode Exit fullscreen mode

问题 2 可以通过在我们的模型
上添加一个`before_save`回调来解决

```
@[ASRA::ExclusionPolicy(:all)]
class Blog::Models::User < Granite::Base
  ...

  before_save :hash_password

  def hash_password : Nil
    if p = @password
      @password = Crypto::Bcrypt::Password.create(p).to_s
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这将在保存模型之前执行并散列密码。

#### 授权控制器

此时，我们现在有了一个`POST /user`端点，它将与一个前端表单配对，用于用户注册。但是为了让用户“登录”,我们需要做一些事情来告诉前端有一个活动的会话。有多种方法可以做到这一点:在 cookie 中设置一个 JWT 令牌，生成一个会话密钥并将其存储在我们的用户表中，或者在接收到正确的用户名和密码后将一个 JWT 令牌返回给前端，供前端存储在某种形式的 HTML5 存储中。为此，我将选择后一种方法，并返回一个 JWT 令牌供前端处理。

首先，我将在我们的`./src/controllers`目录下创建一个新的控制器文件来保存我们的登录逻辑。

```
$ touch ./src/controllers/auth_controller.cr 
```

Enter fullscreen mode Exit fullscreen mode

我还打算借此机会展示一些额外的功能；即处理原始的`HTTP::Request`对象，并引入`ART::Response`。

```
class Blog::Controllers::AuthController < ART::Controller
  # Type hinting an action argument to `HTTP::Request` will supply the current request object.
  @[ART::Post("login")]
  def login(request : HTTP::Request) : ART::Response
    # Raise an exception if there is no request body
    raise ART::Exceptions::BadRequest.new "Missing request body." unless body = request.body

    # Parse the request body into an HTTP::Params object
    form_data = HTTP::Params.parse body.gets_to_end

    # Handle missing form values
    handle_invalid_auth_credentials unless email = form_data["email"]?
    handle_invalid_auth_credentials unless password = form_data["password"]?

    # Find a user with the given ID
    user = Blog::Models::User.find_by email: email

    # Raise a 401 error if either a user isn't found or the password does not match
    handle_invalid_auth_credentials if !user || !(Crypto::Bcrypt::Password.new(user.password).verify password)

    # If an `ART::Response` is returned then it is used as is for the response,
    # otherwise, like the other endpoints, the response value is by default JSON serialized
    ART::Response.new({token: user.generate_jwt}.to_json, headers: HTTP::Headers{"content-type" => "application/json"})
  end

  private def handle_invalid_auth_credentials : Nil
    # Raise a 401 error if values are missing, or are invalid;
    # this also handles setting an appropiate `www-authenticate` header
    raise ART::Exceptions::Unauthorized.new "Invalid username and/or password.", "Basic realm=\"My Blog\""
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

原始请求对象可以通过类型提示动作参数`HTTP::Request`来获得，Athena 将知道在执行动作时提供请求对象。然后，我们验证所有必需的字段都存在，并且找到了一个具有给定凭证的用户；否则，我们返回一个 401 错误供前端处理。

需要注意的一点是这个动作的返回类型是`ART::Response`。在高层次上，Athena 的实现只是试图将一个`HTTP::Request`转换成一个`ART::Response`。如果一个动作返回一个`ART::Response`，那么这个请求基本上就完成了，并被原样返回(假设没有监听器进一步修改它，稍后会详细介绍)。否则，就像我们的其他操作一样，如果返回类型不是一个`ART::Response`，那么结果值将通过视图层，以便将该值转换成一个`ART::Response`。默认情况下，这是 JSON 序列化它，但是如果需要的话，也可以定制。

接下来，我们需要在我们的用户对象上实现`generate_jwt`方法，看起来像:

```
def generate_jwt : String
  JWT.encode({
    "user_id" => @id,
    "exp"     => (Time.utc + 1.week).to_unix,
    "iat"     => Time.utc.to_unix,
  },
    ENV["SECRET"],
    :hs512
  )
end 
```

Enter fullscreen mode Exit fullscreen mode

此方法将生成一个 JWT，其主体包括:

*   用户的 id
*   现在+ 1 周的到期日期
*   JWT 创建的时间

我生成了一个安全字符串，并将其导出为一个 env 变量，作为签署令牌的密钥。

```
$ export SECRET=MY_SECURE_STRING 
```

Enter fullscreen mode Exit fullscreen mode

这只是一个简单的例子，并不代表如何最好地使用 JWT 令牌。如果这是真的，您可以包括其他声明或更改细节以最适合您的用例。

重启服务器并发送请求后:

```
curl --request POST \
  --url http://localhost:3000/login \
  --header 'content-type: application/x-www-form-urlencoded' \
  --data 'email=fakeemail%40domain.com&password=monkey123' 
```

Enter fullscreen mode Exit fullscreen mode

您应该得到一个 JSON 对象，其中包含您的 JWT 令牌。成功！但是，如果您使用了无效的凭证，您将返回 401 错误。

```
{  "code":  401,  "message":  "Invalid username and/or password"  } 
```

Enter fullscreen mode Exit fullscreen mode

因为我们想象的前端将把这个令牌存储在浏览器的本地存储器中，所以我们并不真正需要一个`/logout`端点。然而，如果你想创建一个令牌，你可以让它在从前端删除令牌之前发出一个请求*。通过这种方式，您可以告诉您的服务器，如果您有其他任务/清理工作要做，某个给定的用户会注销。*

#### 文章模式

此时，我们能够注册新用户，允许用户登录，同时验证并抛出有用的错误。

议程上的下一项是创建我们的`Article`模型、表格和控制器。我现在会讲得快一点，因为它和以前很相似。

接下来让我们考虑一篇文章需要哪些列:

*   id:int 64——自动生成的 ID，用于唯一标识每篇文章
*   user _ id:int 64——撰写文章的用户
*   标题:字符串-文章的标题
*   身体:字符串-身体
*   created_at : Time -文章创建的时间
*   updated_at : Time -文章更新的时间
*   deleted_at : Time -文章被删除的时间

将此转换成 SQL 语句:

```
CREATE TABLE "blog"."articles"
(
    "id"         BIGSERIAL NOT NULL PRIMARY KEY,
    "user_id"    BIGINT    NOT NULL REFERENCES "blog"."users",
    "title"      TEXT      NOT NULL,
    "body"       TEXT      NOT NULL,
    "created_at" TIMESTAMP NOT NULL DEFAULT NOW(),
    "updated_at" TIMESTAMP NOT NULL DEFAULT NOW(),
    "deleted_at" TIMESTAMP NULL
); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的表已经创建好了，我们可以继续创建第二个模型。我将首先创建`article.cr`文件。

```
$ touch ./src/models/article.cr 
```

Enter fullscreen mode Exit fullscreen mode

```
@[ASRA::ExclusionPolicy(:all)]
class Blog::Models::Article < Granite::Base
  include ASR::Serializable
  include Assert

  connection my_blog
  table "articles"

  @[ASRA::Expose]
  @[ASRA::ReadOnly]
  belongs_to user : User

  @[ASRA::Expose]
  @[ASRA::ReadOnly]
  column id : Int64, primary: true

  @[ASRA::Expose]
  @[Assert::NotBlank]
  @[Assert::NotNil]
  column title : String

  @[ASRA::Expose]
  @[Assert::NotBlank]
  @[Assert::NotNil]
  column body : String

  @[ASRA::Expose]
  @[ASRA::ReadOnly]
  column updated_at : Time

  @[ASRA::Expose]
  @[ASRA::ReadOnly]
  column created_at : Time

  @[ASRA::ReadOnly]
  column deleted_at : Time?
end 
```

Enter fullscreen mode Exit fullscreen mode

这个模型与我们的`User`模型非常相似，主要区别在于`belongs_to user : User`宏。这个宏扩展并创建了`user_id`字段。它还创建一个 getter 和 setter 来检索和设置相关的用户对象。在这种情况下，是文章的作者。

我们还想回到`User`模型，并在表定义下面添加`has_many articles : Article`。这定义了一个返回用户文章数组的方法。`articles = user.articles`e . x。

接下来，是`ArticleController`。

#### 条控制器

```
$ touch ./src/controllers/article_controller.cr 
```

Enter fullscreen mode Exit fullscreen mode

```
class Blog::Controllers::ArticleController < ART::Controller
  @[ART::Post("article")]
  @[ART::View(status: :created)]
  @[ART::ParamConverter("article", converter: Blog::Converters::RequestBody, model: Blog::Models::Article)]
  def new_article(article : Blog::Models::Article) : Blog::Models::Article
    article.save
    article
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

同样，这看起来几乎与我们的`UserController`中的`new_user`动作相同。

请求使用您的用户 id 的`user_id`和之前检索到的令牌:
创建一篇文章

```
curl --request POST \
  --url http://localhost:3000/article \
  --header 'content-type: application/json' \
  --header 'authorization: Bearer TOKEN' \
  --data '{
    "user_id": 1,
    "title": "My Athena Blog",
    "body": "Athena makes developing JSON APIs easy!"
}' 
```

Enter fullscreen mode Exit fullscreen mode

成功创建文章:

```
{  "user_id":  1,  "id":  1,  "title":  "My Athena Blog",  "body":  "Athena makes developing JSON APIs easy!",  "updated_at":  "2020-07-11T22:57:56Z",  "created_at":  "2020-07-11T22:57:56Z"  } 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们现在可以创建文章了。但是，您是否看到了这个实现的问题？没有围绕`user_id`的验证，也没有任何授权来阻止随机的人为他们想要的任何用户创建文章。让我们利用刚才“登录”时得到的生成的 JWT 令牌，在请求流中添加一些授权检查。

### 授权

JWT 的核心要点之一是，一旦通过验证，使用我们的密钥并检查主体中的声明，就可以确保它是有效的令牌，并且我们应该处理请求。此外，由于这是一个 REST API，我们需要启用 CORS 来允许我们的前端实际上向它发出请求。我们可以通过启用 Athena 的 CORS 监听器来实现后者。

我们只需要简单地定义一些我们希望监听器如何操作的配置，更多配置信息见 [ART::Config::CORS](https://athena-framework.github.io/athena/Athena/Routing/Config/CORS.html) 。在应用程序的根目录下创建一个名为`athena.yml`的文件，内容如下:

```
--------
routing:
  cors:
    allow_credentials: true
    allow_origin: 
      - https://api.myblog.com
    allow_methods:
      - GET
      - POST
      - PUT
      - DELETE 
```

Enter fullscreen mode Exit fullscreen mode

Athena 使用 [Athena::EventDispatcher](https://github.com/athena-framework/event-dispatcher) 来处理进入请求/响应生命周期，这与更标准的`HTTP::Handler`方法相反。

当处理一个请求时，Athena 发出各种各样的[事件](https://athena-framework.github.io/athena/Athena/Routing/Events.html)，这些事件可以被监听以尽早处理请求，就像 [CORS](https://athena-framework.github.io/athena/Athena/Routing/Listeners/CORS.html) 监听器一样，或者为响应添加额外的信息，比如头/cookie 等。这方面的一个很好的例子是，当发生未处理的异常时，为了进行日志记录，可以利用。

对于我们认证用户的目标，我们将在[请求](https://athena-framework.github.io/athena/Athena/Routing/Events/Request.html)事件上创建一个监听器。这将用于验证令牌是否存在以及它是否有效。让我们开始吧。

首先，让我们创建一个新目录来存储我们的处理程序。

```
$ mkdir ./src/listeners
$ touch ./src/listeners/security_listener.cr 
```

Enter fullscreen mode Exit fullscreen mode

```
struct Blog::Listeners::SecurityListener
  # Define the interface to implement the required methods
  include AED::EventListenerInterface

  # Specify that we want to listen on the `Request` event.
  # The value of the has represents this listener's priority;
  # the higher the value the sooner it gets executed.
  def self.subscribed_events : AED::SubscribedEvents
    AED::SubscribedEvents{
      ART::Events::Request => 10,
    }
  end

  # Define a `#call` method scoped to the `Request` event.
  def call(event : ART::Events::Request, _dispatcher : AED::EventDispatcherInterface) : Nil
    # Allow POST user and POST login through since they are public
    # In the future Athena will most likely have a more structured way to handle auth
    if event.request.method == "POST" && {"/user", "/login"}.includes? event.request.path
      return
    end

    # Return a 401 error if the token is missing or malformed
    raise ART::Exceptions::Unauthorized.new "Missing bearer token", "Bearer realm=\"My Blog\"" unless (auth_header = event.request.headers.get?("Authorization").try &.first) && auth_header.starts_with? "Bearer "

    # Get the JWT token from the Bearer header
    token = auth_header.lchop "Bearer "

    begin
      # Validate the token
      body = JWT.decode token, ENV["SECRET"], :hs512
    rescue decode_error : JWT::DecodeError
      # Throw a 401 error if the JWT token is invalid
      raise ART::Exceptions::Unauthorized.new "Invalid token", "Bearer realm=\"My Blog\""
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

既然我们的侦听器被定义了，我们面临一些新的问题。

1.  我们如何告诉雅典娜使用它？
2.  我们如何让应用程序的其余部分知道当前经过身份验证的用户？

这两个问题都可以通过 Athena 的另一个特性——依赖注入(DI)来解决。Athena 使用[Athena::dependency injection](https://github.com/athena-framework/dependency-injection)来简化有用对象的共享。虽然我将在本文中介绍 DI 的要点，但是除了 shard 中的 API 文档之外，请参见 Crystal 中的[依赖注入，以获得更详细的实例。](https://dev.to/blacksmoke16/dependency-injection-in-crystal-2d66)

服务容器包含各种有用对象的实例，也称为服务。然后，这些服务可以提供给其他服务，而不必手动实例化一切。它还允许更容易地测试类型，因为它们可以依赖于抽象(接口)而不是具体类型。在我们的例子中，它将允许我们定义一个服务，该服务将存储当前已验证的用户，以便在应用程序的其余部分访问它。

首先让我们定义一个类型来存储用户，又名`UserStorage`。我们将创建一个新目录来存储不适合其他地方的服务。

```
$ mkdir ./src/services
$ touch ./src/services/user_storeage.cr 
```

Enter fullscreen mode Exit fullscreen mode

```
# The ADI::Register annotation tells the DI component how this service should be registered
@[ADI::Register]
class Blog::UserStorage
  # Use a ! property since they'll always be a user defined in our use case.
  #
  # It also provides a `user?` getter in cases where it might not be.
  property! user : Blog::Models::User
end 
```

Enter fullscreen mode Exit fullscreen mode

因为我们将它定义为`class`，所以相同的实例被注入到每个类型中，也就是说，用户最初设置的将保持设置，直到请求完成。另一方面，`struct`将导致服务的副本被注入。

请务必在`src/blog.cr`中要求我们的新目录。

现在让我们更新我们的安全监听器，我省略了没有改变的行。

```
# Define and register a listener to handle authenticating requests.
@[ADI::Register]
struct Blog::Listeners::SecurityListener
  # Define the interface to implement the required methods
  include AED::EventListenerInterface

  # Define our initializer for DI to inject the user storage.
  def initialize(@user_storage : UserStorage); end

  # Define a `#call` method scoped to the `Request` event.
  def call(event : ART::Events::Request, _dispatcher : AED::EventDispatcherInterface) : Nil
    ...

    # Set the user in user storage
    @user_storage.user = Blog::Models::User.find! body[0]["user_id"]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

通过简单地用`@[ADI::Register]`注释类型，Athena 为我们处理“连接”一切。我们所需的`UserStorage`依赖关系是基于类型限制自动解析和注入的。监听器也自动注册，因为它通过 [ADI.auto_configure](https://athena-framework.github.io/dependency-injection/Athena/DependencyInjection.html#auto_configure(type,options)-macro) 实现了`AED::EventListenerInterface`。

既然我们已经验证了请求，我们可以继续添加读取/更新/删除文章的功能。

我们的`ArticleController`现在看起来像:

```
# The `ART::Prefix` annotation will add the given prefix to each route in the controller.
# We also register the controller itself as a service in order to allow injecting our `UserStorage` object.
# NOTE: The controller service must be declared as public.  In the future this will happen behind the scenes
# but for now it cannot be done automatically.
@[ART::Prefix("article")]
@[ADI::Register(public: true)]
class Blog::Controllers::ArticleController < ART::Controller
  # Define our initializer for DI
  def initialize(@user_storage : Blog::UserStorage); end

  @[ART::Post(path: "")]
  @[ART::View(status: :created)]
  @[ART::ParamConverter("article", converter: Blog::Converters::RequestBody, model: Blog::Models::Article)]
  def new_article(article : Blog::Models::Article) : Blog::Models::Article
    # Set the owner of the article to the currently authenticated user
    article.user = @user_storage.user
    article.save
    article
  end

  @[ART::Get(path: "")]
  def get_articles : Array(Blog::Models::Article)
    # We are also using the user in UserStorage as an additional conditional in our query when fetching articles
    # this allows us to only returns articles that belong to the current user.
    Blog::Models::Article.where(:deleted_at, :neq, nil).where(:user_id, :eq, @user_storage.user.id).select
  end

  @[ART::Put(path: "")]
  @[ART::ParamConverter("article", converter: Blog::Converters::RequestBody, model: Blog::Models::Article)]
  def update_article(article : Blog::Models::Article) : Blog::Models::Article
    article.save
    article
  end

  @[ART::Get("/:id")]
  @[ART::ParamConverter("article", converter: Blog::Converters::DB, model: Blog::Models::Article)]
  def get_article(article : Blog::Models::Article) : Blog::Models::Article
    article
  end

  @[ART::Delete("/:id")]
  @[ART::ParamConverter("article", converter: Blog::Converters::DB, model: Blog::Models::Article)]
  def delete_article(article : Blog::Models::Article) : Nil
    article.deleted_at = Time.utc
    article.save
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这些额外的方法将允许:

*   列出当前用户的所有文章
*   更新文章
*   获取特定文章
*   删除特定文章

后两者使用了一种新的转换器；`DB`。这将执行一个 DB 查询，用所提供的`id`查找所提供类型的记录，否则返回一个`404`错误。代码如下:

```
@[ADI::Register]
struct Blog::Converters::DB < ART::ParamConverterInterface
  # Define a customer configuration for this converter.
  # This allows us to provide a `model` field within the annotation
  # in order to define _what_ model should be queried for.
  configuration model : Granite::Base.class

  # :inherit:
  #
  # Be sure to handle any possible exceptions here to return more helpful errors to the client.
  def apply(request : HTTP::Request, configuration : Configuration) : Nil
    # Grab the `id` path parameter from the request's attributes
    primary_key = request.attributes.get "id", Int32

    # Raise a 404 if a record with the provided ID does not exist
    raise ART::Exceptions::NotFound.new "An item with the provided ID could not be found" unless model = configuration.model.find primary_key

    # Set the resolved model within the request's attributes
    # with a key matching the name of the argument within the converter annotation
    request.attributes.set configuration.name, model, configuration.model
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

由于 Crystal 处理反序列化的方式，在某些情况下更新 DB 模型可能有点棘手。在其他框架中，需要在`PUT`主体中包含*所有*模型的属性，甚至是那些由数据库管理的不可编辑的属性，比如`id`或时间戳。Athena 的序列化器包含了[对象构造器](https://athena-framework.github.io/serializer/Athena/Serializer/ObjectConstructorInterface.html)的概念；它确定在反序列化过程中如何构造新对象。在我们的例子中，我们可以定义一个自定义的构造函数，从数据库中获取对象，这样我们就不需要在我们的`PUT`请求中包含时间戳或其他不可编辑的属性。

在`request_body_converter.cr`中添加以下代码:

```
# Define a custom `ASR::ObjectConstructorInterface` to allow sourcing the model from the database
# as part of `PUT` requests, and if the type is a `Granite::Base`.
#
# Alias our service to `ASR::ObjectConstructorInterface` so ours gets injected instead.
@[ADI::Register(alias: ASR::ObjectConstructorInterface)]
class DBObjectConstructor
  include Athena::Serializer::ObjectConstructorInterface

  # Inject `ART::RequestStore` in order to have access to the current request.
  # Also inject `ASR::InstantiateObjectConstructor` to act as our fallback constructor.
  def initialize(@request_store : ART::RequestStore, @fallback_constructor : ASR::InstantiateObjectConstructor); end

  # :inherit:
  def construct(navigator : ASR::Navigators::DeserializationNavigatorInterface, properties : Array(ASR::PropertyMetadataBase), data : ASR::Any, type)
    # Fallback on the default object constructor if the type is not a `Granite` model.
    unless type <= Granite::Base
      return @fallback_constructor.construct navigator, properties, data, type
    end

    # Fallback on the default object constructor if the current request is not a `PUT`.
    unless @request_store.request.method == "PUT"
      return @fallback_constructor.construct navigator, properties, data, type
    end

    # Lookup the object from the database; assume the object has an `id` property.
    object = type.find data["id"].as_i

    # Return a `404` error if no record exists with the given ID.
    raise ART::Exceptions::NotFound.new "An item with the provided ID could not be found." unless object

    # Apply the updated properties to the retrieved record
    object.apply navigator, properties, data

    # Return the object
    object
  end
end

# Make the compiler happy when we want to allow any Granite model to be deserializable.
class Granite::Base
  include ASR::Model
end 
```

Enter fullscreen mode Exit fullscreen mode

这种类型允许我们从数据库获取原始对象，然后将更新后的值应用于它，而不是从请求体创建一个全新的对象。DB 逻辑只应用于`PUT`请求上的`Granite::Base`类型，其他的都使用默认行为，根据请求体中的数据创建一个新对象。

然后我们可以更新我们的`RequestBody`转换器，看起来像:

```
# Define our converter, register it as a service, inheriting from the base interface struct.
@[ADI::Register]
struct Blog::Converters::RequestBody < ART::ParamConverterInterface
  # Define a custom configuration for this converter.
  # This allows us to provide a `model` field within the annotation
  # in order to define _what_ model should be used on deserialization.
  configuration model : Granite::Base.class

  # Inject the Serializer instance into our converter.
  def initialize(@serializer : ASR::SerializerInterface); end

  # :inherit:
  def apply(request : HTTP::Request, configuration : Configuration) : Nil
    # Be sure to handle any possible exceptions here to return more helpful errors to the client.
    raise ART::Exceptions::BadRequest.new "Request body is empty." unless body = request.body.try &.gets_to_end

    # Deserialize the object, based on the type provided in the annotation.
    object = @serializer.deserialize configuration.model, body, :json

    # Run the validations.
    object.validate!

    # Add the resolved object to the request's attributes.
    request.attributes.set configuration.name, object, configuration.model
  rescue ex : Assert::Exceptions::ValidationError
    # Return a `422` error if the object failed its validations.
    raise ART::Exceptions::UnprocessableEntity.new ex.to_s
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

从这里开始，您可能希望用一些 ACL 逻辑来更新`ArticleController#update_article`，比如:

```
 @[ART::Put(path: "")]
  @[ART::ParamConverter("article", converter: Blog::Converters::RequestBody, model: Blog::Models::Article)]
  def update_article(article : Blog::Models::Article) : Blog::Models::Article
    # Ensure that a user cannot edit someone else's article
    raise ART::Exceptions::Forbidden.new "Only the author of the article can edit it." if article.user_id != @user_storage.user.id
    article.save
    article
  end 
```

Enter fullscreen mode Exit fullscreen mode

我们完事了。我希望这是对 Athena 及其特性的很好的介绍。如果你想看关于 Granite/Athena 的任何具体内容，或者如果我错过了什么，请随时发表评论或加入 Athena [Gitter](https://gitter.im/athena-frameworkcr/community) 频道。

本教程的完整代码可在 GitHub 上获得。
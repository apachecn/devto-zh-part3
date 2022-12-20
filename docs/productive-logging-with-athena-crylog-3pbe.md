# 使用 Athena & Crylog 进行高效日志记录

> 原文：<https://dev.to/blacksmoke16/productive-logging-with-athena-crylog-3pbe>

# **这篇文章是为老版本的雅典娜写的，不再有效。它的内容已经集成到[中，用 Athena & Granite](https://dev.to/blacksmoke16/creating-a-json-api-with-athena--granite-510i) 和水晶中的[依赖注入创建了一个 JSON API。](https://dev.to/blacksmoke16/dependency-injection-in-crystal-2d66)**

# **同样，`Crylog`已经被弃用，取而代之的是标准库的[日志](https://crystal-lang.org/api/Log.html)模块。**

# 测井

在创建任何应用程序时，日志记录都是一个重要的工具。日志记录添加了对调试问题、进行分析或只是收集用户行为数据有用的信息；所有这些都可以用来为未来的决策提供信息。

然而，并不是所有的日志都是相同的。日志记录应该以这样的方式设置，以便根据环境只记录有用的信息。例如，开发应用程序的开发人员希望看到调试级别的信息，这样他们就可以获得所有可能的信息，以便跟踪问题并找到瓶颈。在生产环境中运行的应用程序应该*而不是*查看调试级日志，这样日志文件就不会混乱，从而导致实际的重要问题在混乱中丢失。

## Crylog

Crylog 是基于 [Monolog](https://github.com/Seldaek/monolog/) 的一个新的灵活的 Crystal 日志框架。它允许应用程序拥有多个命名的记录器，每个记录器都有不同的处理程序、处理器和格式化程序。

处理程序是对记录的消息做一些事情的东西。例如记录到文件或标准输出。但也可能是发送信息给哨兵，或数据库等。

处理器将额外的数据添加到每个消息中。一个很好的用例是将用户/客户 id 添加到每个记录的消息中，以代表导致该消息被记录的用户。

最后，格式化程序当然决定了消息如何序列化。格式化程序允许根据处理程序使用不同的样式，因为 HTML 对于电子邮件处理程序来说很好，但是对于日志文件来说就不那么好了。

当一起使用时，Crylog 允许定制各种记录器来处理应用程序的各个方面。例如在生产环境中只记录警告或更高级别的消息，而在开发过程中记录所有消息。

## 雅典娜

Athena 是一个基于注释的 web 框架，它使用 Crylog 作为它的日志解决方案。在本文中，我将展示 Crylog 与一些简洁的 Athena 特性(如依赖注入)相结合时带来的一些好处。这将是我几个月前写的[博客文章](https://dev.to/blacksmoke16/creating-a-json-api-with-athena--granite-510i)的延续。

### 【依赖注入】( DI)

Athena 的新特性之一是服务容器层。这允许项目在整个项目中共享有用的对象，也就是服务。这些对象存在于一个称为服务容器(SC)的特殊类中。对象实例可以从容器中检索，甚至可以作为构造函数 DI 的一种形式直接注入到类中。虽然本文并不专门关注 DI，但是它将会用到它。更多相关信息，请参见 [Athena 文档](https://github.com/Blacksmoke16/athena/blob/master/docs/dependency_injection.md)。

## 议程

首先，让我们定义一些我们希望通过日志记录实现的目标。

*   当创建/删除文章、用户登录或新用户注册时，记录一些信息性消息，以及一些上下文信息。
*   这两种情况都应该记录到一个文件中，但是开发环境也应该记录到 STDOUT 中。

默认情况下，当在`development`环境中时，Athena 会将所有消息记录到 STDOUT 和一个`development.log`文件中，而`production`环境会将所有消息记录到一个`production.log`文件中，但只针对警告和更高级别。此外，由于我们想在我们的记录器中添加一些自定义逻辑，我们需要定义自己的配置。

### 安全 HTTP 处理程序

在上一个教程中，我们创建了一个`HTTP::Handler`类来处理每个请求中使用的令牌的授权。我确实计划将本文中使用的一些与安全相关的特性内置到 Athena 本身中，但是现在这要由用户来定义。

然而，这种方法存在一些问题。正如我在上一个教程中停止的那样，我们如何知道哪个用户登录了我们的控制器动作？为了解决这个问题，我们可以创建一个存储当前用户的`UserStorage`类，这样其他类就可以通过 DI 访问它。让我们在`src`下创建一个名为`services`的新目录，我将把`UserStorage`服务放在那里。接下来用下面的代码创建一个名为`user_storage.cr`的新文件。请确保在您的`blog.cr`文件中也需要它。

```
module Blog
  @[Athena::DI::Register]
  class UserStorage < Athena::DI::ClassService
    # Use a ! property since they'll always be a user defined in our use case.
    # It also defines a nilable getter method to make sure there is a user
    property! user : Blog::Models::User
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

注意我们是如何从`Athena::DI::ClassService`继承的，这允许 Athena 获得所有要注册的服务的列表，而`@[Athena::DI::Register]`允许对服务如何注册进行一些配置。

现在，我们可以返回到我们的安全处理程序，获取用户存储服务，并设置用户。

```
module Blog
  class SecurityHandler
    include HTTP::Handler

    def call(ctx : HTTP::Server::Context) : Nil
      ...
      # Get the user storage service from the container
      user_storage = Athena::DI.get_container.get("user_storage").as(UserStorage)

      # Set the user in user storage
      user_storage.user = Blog::Models::User.find! body[0]["user_id"]

      # Call the next handler
      call_next ctx
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以做类似的事情，更新`ArticleController`中的引用。

```
module Blog::Controllers
  @[Athena::Routing::ControllerOptions(prefix: "article")]
  class ArticleController < Athena::Routing::Controller
    include Athena::DI::Injectable

    def initialize(@request_stack : Athena::Routing::RequestStack, @user_storage : UserStorage); end

    @[Athena::Routing::Post(path: "")]
    @[Athena::Routing::ParamConverter(param: "body", type: Blog::Models::Article, converter: Athena::Routing::Converters::RequestBody)]
    def new_article(body : Blog::Models::Article) : Blog::Models::Article
      # Sets the owner of the blog post as the current authed user
      body.user = @user_storage.user
      body.save
      body
    end

    @[Athena::Routing::Get(path: "")]
    def get_articles : Array(Blog::Models::Article)
      # We are also using the user in UserStorage as an additional conditional in our query when fetching articles
      # this allows us to only returns articles that belong to the current user.
      Blog::Models::Article.where(:deleted_at, :neq, nil).where(:user_id, :eq, @user_storage.user.id).select
    end

    @[Athena::Routing::Put(path: "")]
    @[Athena::Routing::ParamConverter(param: "body", type: Blog::Models::Article, converter: Athena::Routing::Converters::RequestBody)]
    def update_article(body : Blog::Models::Article) : Blog::Models::Article
      body.user = @user_storage.user
      body.save
      body
    end

    @[Athena::Routing::Get(path: "/:article_id")]
    @[Athena::Routing::ParamConverter(param: "article", pk_type: Int64, type: Blog::Models::Article, converter: Athena::Routing::Converters::Exists)]
    def get_article(article : Blog::Models::Article) : Blog::Models::Article
      article
    end

    @[Athena::Routing::Delete(path: "/:article_id")]
    @[Athena::Routing::ParamConverter(param: "article", pk_type: Int64, type: Blog::Models::Article, converter: Athena::Routing::Converters::Exists)]
    def delete_article(article : Blog::Models::Article) : Nil
      article.deleted_at = Time.utc
      article.save
      @request_stack.response.status = HTTP::Status::ACCEPTED
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们还能够注入`RequestStack`来访问请求/响应。

> 注意:如果类在请求/响应周期内被实例化，服务只能被自动注入，就像`ArticleController`一样。在它之外实例化的类，如`SecurityHandler`，不能访问同一个容器，服务必须手动获取。

现在我们已经解决了身份验证问题，我们可以继续添加一些日志记录了。

### 添加日志

我们先走两条公共路线。只需转到您的`AuthController`并在`{token: user.generate_jwt}`前添加一个`Athena.logger.info "User logged in", Crylog::LogContext{"user_id" => user.id}`。这将记录

> [2019-11-20T01:40:40Z]主。信息:用户已登录{"user_id":1}

带有登录用户 id 的消息。我们可以在我们的`UserController`中做类似的事情。保存用户模型后添加一个`Athena.logger.info "New user registered", Crylog::LogContext{"user_id" => body.id, "email" => body.email, "first_name" => body.first_name, "last_name" => body.last_name}`。这将记录某人注册了该用户的一些信息。

我们也可以在我们的`ArticleController`中做同样的事情，比如添加`Athena.logger.info "Article ##{body.id} was created", Crylog::LogContext{"user_id" => @user_storage.user.id}`。然而，我们可以通过使用 Crylog 处理器来保持事情更加干燥，因为`ArticleController`是经过认证的端点，在`UserStorage`中会有一个用户。

让我们在`src`下创建一个名为`logger`的新目录，我将把处理器放在那里。接下来用下面的代码创建一个名为`user_processor.cr`的新文件。确保在您的`blog.cr`文件中也需要它。

```
module Blog
  struct UserProcessor < Crylog::Processors::LogProcessor
    def call(message : Crylog::Message) : Nil
      user_storage = Athena::DI.get_container.get("user_storage").as(UserStorage)

      # Return early if a message was logged in a public endpoint there won't be a user in storage
      return unless user = user_storage.user?

      # Add the current user's id to all log messages
      message.extra["user_id"] = user.id
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这将把当前用户的 id 作为消息额外属性的一部分添加到每个记录的消息中。它还处理我们记录用户登录和注册的情况，这种情况下存储中没有用户。另一种选择是专门为公共内容定义一个日志记录器，但是现在这有点大材小用了。

接下来我们需要告诉 Crylog 使用我们的处理器。我们还将处理议程中的第二个要点。在`blog.cr`模块中添加以下代码:

```
 def Athena.configure_logger
    # Create the logs dir if it doesn't exist already.
    Dir.mkdir Athena.logs_dir unless Dir.exists? Athena.logs_dir
    Crylog.configure do |registry|
      registry.register "main" do |logger|
        handlers = [] of Crylog::Handlers::LogHandler

        if Athena.environment == "development"
          # Log to STDOUT and development log file if in develop env.
          handlers << Crylog::Handlers::IOHandler.new(STDOUT)
          handlers << Crylog::Handlers::IOHandler.new(File.open("#{Athena.logs_dir}/development.log", "a"))
        elsif Athena.environment == "production"
          # Log only to a file if in production env.
          handlers << Crylog::Handlers::IOHandler.new(File.open("#{Athena.logs_dir}/production.log", "a"))
        end

        # Tell crylog to use our processor on the main logger.
        logger.processors = [Blog::UserProcessor.new] of Crylog::Processors::LogProcessors

        logger.handlers = handlers
      end
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

这个方法告诉 Crylog 如何配置要使用的记录器。如您所见，我们在 dev 环境中记录到 STDOUT 和一个`development.log`文件，但在 prod 环境中只记录到一个`production`文件。注意我们用`a`选项打开日志文件；这使得消息被附加到日志中，而不是完全覆盖它。我们也在告诉它使用我们的`UserProcessor`。有关可能的日志配置的更多信息，请查看 [Crylog 文档](https://github.com/Blacksmoke16/crylog/tree/master/docs)。

之后，启动服务器，开始查看记录的消息。记录在`ArticleController`中的消息将包含当前用户 id；就像用户登录时一样，但不需要显式指定。

从这里，可以定义额外的处理器/处理器等，以便将这些消息记录到 Greylog、Sentry，或者甚至发送电子邮件，如果它们的严重性足够高的话。在决定如何最好地为您的项目实现日志记录时，Crylog 允许各种各样的灵活性。

2019 年 11 月 19 日更新，基于之前的教程/新的 Athena 版本进行了一些细微的润色和更改
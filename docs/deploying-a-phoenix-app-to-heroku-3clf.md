# 将 Phoenix 应用部署到 Heroku

> 原文：<https://dev.to/logicmason/deploying-a-phoenix-app-to-heroku-3clf>

在本快速指南中，我们将构建一个新的 Phoenix 应用程序，设置 Heroku 并进行部署！Heroku 可能不是 Elixir 应用程序的理想选择，但 Elixir 和 Phoenix 是在 Heroku 上运行的理想堆栈！

[https://www.youtube.com/embed/Wwlz5zGgtFA](https://www.youtube.com/embed/Wwlz5zGgtFA)

如果你还没有在你的电脑上安装和设置 Elixir，浏览一下第一课的设置部分，然后你就可以继续学习了。这些是我们要复习的步骤:

*   报名参加 Heroku ( [这里](https://signup.heroku.com/))
*   安装 Heroku Toolbelt ( [此处下载](https://toolbelt.heroku.com/))
*   创建新的 Phoenix 应用程序
*   在 Heroku 上为它创建一个新应用程序
*   添加 Phoenix 静态构建包
*   在 Heroku 上创建一个自由层数据库
*   更改我们项目中的一些设置
*   为 Heroku 制作一个`Procfile`和`elixir_buildpack.config`
*   把一切都推给 Heroku 部署！

## 快速启动

我们将调用我们的应用程序`Hello`，所以下面是我们需要运行的:

```
mix phx.new hello
cd hello
git init
heroku create --buildpack "https://github.com/HashNuke/heroku-buildpack-elixir.git"
heroku buildpacks:add https://github.com/gjaldon/heroku-buildpack-phoenix-static.git
heroku addons:create heroku-postgresql:hobby-dev 
```

## 申请文件更改

Heroku 将机密存储在系统环境变量中，而不是一个机密文件中，因此我们可以删除`prod.secrets.exs`。然后，我们可以更新`prod.exs`来从`System.get_env`那里获得我们需要的秘密。在那里，我们还可以设置应用程序使用 SSL，并将主机名更新为 Heroku 在我们创建应用程序时命名的名称。最后将数据库设置添加到文件的底部:

```
use Mix.Config

config :hello, HelloWeb.Endpoint,
  load_from_system_env: true,
  url: [scheme: "https", host: "NAME_OF_HEROKU_APP.herokuapp.com", port: 443],
  force_ssl: [rewrite_on: [:x_forwarded_proto]],
  cache_static_manifest: "priv/static/cache_manifest.json",
  secret_key_base: Map.fetch!(System.get_env(), "SECRET_KEY_BASE")

# Do not print debug messages in production
config :logger, level: :info

# Configure your database
config :hello, Hello.Repo,
  adapter: Ecto.Adapters.Postgres,
  pool_size: String.to_integer(System.get_env("POOL_SIZE") || "10"),
  ssl: true,
  url: System.get_env("DATABASE_URL") 
```

如果你的应用要使用通道，那么更新你的`user_socket.ex`文件，为套接字添加一个超时。Heroku 将时间限制在 55 秒，所以为了安全起见，将时间设置为 45 秒左右:

`transport(:websocket, Phoenix.Transports.WebSocket, timeout: 45_000)`

## Heroku 的应用设置

默认情况下，Phoenix 使用的池大小为 10。这是非常低的，理想情况下，我们希望使用尽可能多的可用资源。在 Heroku 上，我们有一个最大的 20 个池，所以我们将应用程序设置为使用 18 个，对于混合任务只使用 2 个。我们还将使用 Phoenix 生成一个密钥，并将其设置在 Heroku 中，这样应用程序就可以在生产中从系统环境中获得它:

```
heroku config:set POOL_SIZE=18
heroku run "POOL_SIZE=2 mix hello.task"
mix phx.gen.secret
heroku config:set SECRET_KEY_BASE="<SECRET PHOENIX JUST GENERATED ABOVE>" 
```

## 添加新文件

在我们应用程序的根目录中，我们将为 Heroku 添加两个文件——一个名为`Procfile`的文件只有一行:

```
web: MIX_ENV=prod mix phx.server 
```

还有一个叫做`elixir_buildpack.config`的，保存一些配置设置:

```
erlang_version=20.1
elixir_version=1.7.2
always_rebuild=false
runtime_path=/app 
```

**重要提示**:确保两个文件都有 LF 行尾。赫罗库对 CRLF 失误了！

## 部署！

```
git add .
git commit -m "prepares for heroku"
git push heroku master 
```

*最初发表于[方士营](https://alchemist.camp/episodes/deploy-phoenix-heroku)T3】*
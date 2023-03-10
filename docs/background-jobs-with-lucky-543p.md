# 幸运的背景工作

> 原文：<https://dev.to/jwoertink/background-jobs-with-lucky-543p>

与许多 web 应用程序和 API 一样，有时您需要在后台处理一些任务。为此，您通常会使用后台作业处理器。这有助于让一些事情变得异步，并加快应用程序用户的速度。

例如，当用户登录到您的站点时进行日志记录。也许你存储了他们使用的 IP 地址、他们登录的时间、尝试登录的次数以及他们使用的设备。如果您以内嵌方式完成所有这些操作，那么这个过程将会跟踪所有这些信息，并且用户必须等待您在数据库中创建这个新记录，然后才能真正登录。或者，您将该信息推送到后台作业，并让用户登录。然后，您可以按照自己的节奏更新记录，因为没有任何东西依赖于数据。

如果你是第一次接触 [Crystal Lang](https://crystal-lang.org/) 和/或第一次接触 [Lucky](https://luckyframework.org/) ，但是你来自另一种语言，比如 Rails，你可能会对 Sidekiq 很熟悉。现在， [Sidekiq](https://github.com/mperham/sidekiq.cr/) 确实存在于水晶世界中，这很神奇。它甚至是由创建 Ruby 版本的同一个人创建的！但是在我写这篇文章的时候，我花了将近一个小时试图弄清楚如何把它和 Lucky 集成在一起，并且我遇到了相当多的问题。谢天谢地，我找到了一个替代品，我已经在本地安装并运行了大约 2 分钟。

## 蚊子

看一看[蚊子](https://github.com/robacarp/mosquito)。它的 API 与 Sidekiq 使用的 API 相差不远，所以对我来说，这个概念很快也很简单。这也使用 Redis，因为我打算在 Sidekiq 中使用 Redis，所以我不需要安装额外的东西。

假设你已经准备好了你的幸运应用程序，你可以从添加蚊子开始:

```
dependencies:
  mosquito:
    github: robacarp/mosquito 
```

Enter fullscreen mode Exit fullscreen mode

跑完你的`shards install`，你就可以出发了。

现在，我们只需要设置我们的蚊子亚军。创建一个新文件`./src/mosquito.cr`。

```
require "./dependencies"
require "./workers/*"

Mosquito::Runner.start 
```

Enter fullscreen mode Exit fullscreen mode

## 设置工人

接下来，我们需要设置工人。创建一个新文件夹`./src/workers/`，让我们在其中创建一个`login_worker.cr`文件。

```
class LoginWorker < Mosquito::QueuedJob
  params user_id : Int64
  params ip : String

  def perform
    user = UserQuery.find(user_id)
    if user
      # create new Login record from the user
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 使用职工

在某个时候，我们必须启动我们制作的`LoginWorker`，所以在其中一个动作中，我们可以添加这个:

```
post "/login" do
  form = SessionForm.new(params)
  form.submit do |f, user|
    if user
      LoginWorker.new(user_id: user.id, ip: "127.0.0.1").enqueue
      # redirect logged in
    else
      # show your errors
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 引导整个事情

现在你已经安装了蚊子，并设置好了，你只需要启动整个应用程序。因为 Mosquito 运行在它自己的进程中，所以你必须在一个单独的进程中运行它。

在您的`Procfile.dev`中添加行`worker: crystal src/mosquito.cr`。

```
web: lucky watch --reload-browser
assets: yarn watch
worker: crystal src/mosquito.cr 
```

Enter fullscreen mode Exit fullscreen mode

现在当你运行`lucky dev`的时候，它会在一个单独的进程中启动蚊子！

### ProTip:

1.  如果你的应用和我的一样，只是一个 api，你的`Procfile.dev`可能看起来更像

```
web: crystal src/server.cr
worker: crystal src/mosquito.cr 
```

Enter fullscreen mode Exit fullscreen mode

1.  蚊子需要知道你的堆栈，因为它在一个单独的进程中运行。如果你想精简它，请记住，无论你把什么放到你的工人文件中，你都需要把它从你的应用程序栈中分离出来。

2.  默认情况下，Mosquito 会查看您的`REDIS_URL` ENV，或者只获取一个普通的 localhost 值。确保为生产更新那个，以及为您的生产更新您的`Procfile`。

3.  如果您更愿意构建目标(可能是 docker 的目标)，并分别运行它们，那么您可以用:
    更新您的`shard.yml`

```
targets:
  server:
    main: src/server.cr
  mosquito:
    main: src/mosquito.cr 
```

Enter fullscreen mode Exit fullscreen mode

然后用`shards build --production --static --release --no-debug`来获得你的`server`和`mosquito`二进制文件。只要按照你需要的方式运行它们，你就会做得很好。
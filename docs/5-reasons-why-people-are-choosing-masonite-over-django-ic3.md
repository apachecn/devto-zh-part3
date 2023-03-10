# 人们选择 Masonite 而不是 Django 的 5 个原因

> 原文：<https://dev.to/masonite/5-reasons-why-people-are-choosing-masonite-over-django-ic3>

# 简介

Masonite 是一个越来越受欢迎的 Python web 框架，最近在 Python 社区获得了很多关注。

我在这里不是说 Django 是垃圾，Masonite 是世界上最好的东西，但是在过去的一年里，我和很多开发者讨论了他们为什么从 Django 转向 Masonite。下面我将详细介绍我发现的它们之间的共同原因，以及为什么 Masonite 对你的下一个项目更有意义。

其中一个特别的开发人员，Abram Isola，给出了一些非常好的反馈，解释了为什么他的合同投标(与一个非常大的公司)最终赢得了所有其他投标。

在我们开始之前，如果您想跟随 Masonite 进一步探索，这里有几个链接:

*   [官方休闲频道](http://slack.masoniteproject.com)

*   [推特](https://twitter.com/masoniteproject)

好了，开始吧:)

## 1。现有数据库

一些业务应用程序要求您使用现有的数据库，以便为它们创建新的集成。

用 Django 管理现有的数据库并不特别困难。您可以对 Django 使用类似于`inspect db`的东西，以便生成适当的模型和模型模式属性。当您必须在同一个应用程序中管理几个数据库时，问题就出现了，其中一些是现有的，而另一些您需要自己管理。

您仍然可以使用 Django 和数据库路由器之类的东西来实现这一点，但是这需要大量的设置和测试。当您开始开发一个应用程序时，您只想立即投入运行并启动业务逻辑。

### 活动记录模式表单

Masonite 和 Django 都使用 ORM 的主动记录风格，但是主要的区别在于，astoral 对它有更多的动态被动方法。

例如，使用 Django，您可以通过使用类的属性来指定表中的列。

在 Django 中，这可能类似于:

```
from django.db import models

class Musician(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    instrument = models.CharField(max_length=100) 
```

Enter fullscreen mode Exit fullscreen mode

因此，现在无论何时在模型和数据库之间检索、存储和交换数据，Django 都知道要处理什么类型的数据。

演说家更有活力一点。astorar 将使用表中的模式来映射属性值。事实上，演说家根本不需要属性的明确定义。Masonite 中的一个类似模型如下所示:

```
from config.database import Model

class Musician(Model):
    pass 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们没有指定名字、姓氏或乐器，因为 Orator 知道何时从表中获取数据，以及在模型上设置哪些属性。

## 2。班级脚手架

Masonite 拥有所谓的“工艺”命令。这些被称为 craft 命令，因为它们是一个简单的命令，比如:

```
$ craft model Project 
```

Enter fullscreen mode Exit fullscreen mode

那..“工艺品”..这样你就可以停止一遍又一遍地写多余的样板文件。我们都知道控制器的开始是一样的，我们都知道基于函数的视图的声明也是一样的，我们都知道基于类的视图通常也是一样的。

当我们进入开发的清洗和重复周期时，我们作为开发人员不断地一遍又一遍地编写相同的样板文件。创建一个文件，创建一些类样板文件，创建类定义，导入需要的类等。

这就是为什么 Masonite 有十几个命令，允许您从命令行创建视图、控制器、队列作业、命令、中间件、模型、数据库种子等。这为您和您的团队节省了大量开发时间。

花时间写出类定义不仅耗费开发人员的时间，还会分散开发人员的注意力。作为开发人员，您只想生成类定义，然后立即开始处理该类需要包含的业务逻辑。

此外，开发人员不再需要记住需要导入什么基类或导入来启动和运行样板代码。只要让 craft command 为您做所有困难的工作，这样您就可以专注于您的应用程序。

如果您想节省大量编写样板代码的时间，那么可以考虑在您的下一个应用程序中使用 Masonite。

## 3。文件夹结构

这通常是个人偏好，许多开发人员可能不同意这个原因，但 Masonite 提供了一个非常可靠的文件夹结构。

默认情况下，Masonite 有一些不同的理念。

其中之一是保存 1 个 1 类文件。一些开发人员可能认为这是正常的，但是许多人仍然通常为每个文件编写多个类。一旦你开始使用 1 类 1 文件系统，你将永远不会回头。

另一个理念是保持关注点的分离。如果你跑:

```
$ craft job SomeJob 
```

Enter fullscreen mode Exit fullscreen mode

例如，它将该类放在`app/jobs`目录中。需要制作一个中间件？中间件将被创建在`app/http/middleware`目录中。需要创建周期性任务？任务将被创建在`app/tasks`目录中。以此类推。

这使得您不会意外地将所有的逻辑捆绑在同一个地方，这使得以后的重构非常令人头疼。通常，将所有相关的逻辑放在各自的模块中是一种很好的做法。

### 自带应用程序

另一方面，Django 有一个很好的起始结构，并且拥有框架的自包含应用程序部分。这允许您创建自包含的包，如果操作正确，这些包可以交换、删除、插入其他应用程序等等。

问题在于，这在理论上很棒，但在实践中却很糟糕。事实上，我认为这有点反模式，至少 Django 是这样做的。

任何创建了独立应用程序的人都知道，它在最初几天工作得非常好，然后你开始摔跤，因为你的应用程序模型开始依赖其他应用程序。最后，您最终将所有模型放入一个应用程序中，然后在其中构建整个应用程序。也许你使用其他自包含的应用程序作为包，但它们通常最终打破了“自包含”的承诺。

我认为这是 Django 最大的销售之一，这有点言过其实。Django 向你推销一个应用程序，这个应用程序被分解成独立的应用程序，而 Masonite 正在构建一个应用程序，这个应用程序具有一个非常好的关注点文件结构的分离，可以让你保持滚动。

## 4。中间件

Masonite 的中间件系统允许一些非常简单的实现，同时保持非常复杂和动态。Abram Isola 选择 Masonite 而不是 Django，因为 Masonite 的中间件有一些 Django 没有的功能。

首先，Masonite 有两种类型的中间件。这两种类型使用完全相同的类，但这取决于创建后将类放在哪里。

第一种类型的 Masonite 中间件是所谓的 HTTP 中间件。这个中间件像你通常期望的那样运行每个请求。

```
HTTP_MIDDLEWARE = [
    AuthenticationMiddleware,
] 
```

Enter fullscreen mode Exit fullscreen mode

第二种中间件叫做路由中间件:

```
ROUTE_MIDDLEWARE = {
    'auth': AuthenticationMiddleware,
} 
```

Enter fullscreen mode Exit fullscreen mode

这也是您之前编写的相同中间件类，只是在配置文件下面几行的不同列表中。路由中间件更灵活一些。您可以将其附加到特定路线:

```
ROUTES = [
    Get('/dashboard', 'DashboardController@show').middleware('auth')
] 
```

Enter fullscreen mode Exit fullscreen mode

或者一组路线:

```
ROUTES = [
    RouteGroup([
        Get('/dashboard', 'DashboardController@profile'),
        Get('/dashboard/stats', 'DashboardController@stats'),
        Get('/dashboard/users', 'DashboardController@users'),
    ], middleware=['auth'])
] 
```

Enter fullscreen mode Exit fullscreen mode

如果您的应用程序需要非常精确的访问控制或简单的中间件集成，您应该为您的下一个项目考虑 Masonite。

Django 确实有中间件，它运行在每个请求上，你需要在中间指定它是否应该实际执行任何基于输入的逻辑，比如请求路由。

### Django 中间件示例

```
def global_auth_middleware(get_response):
    def middleware(request):
        if not request.user.is_authenticated:
            return redirect('/login/')
        return get_response(request)
    return middleware 
```

Enter fullscreen mode Exit fullscreen mode

### Masonite 中间件示例

```
from masonite.request import Request

class AuthenticationMiddleware:

    def __init__(self, request: Request):
        self.request = request

    def before(self):
        if not self.request.user():
            self.request.redirect_to('login')

    def after(self):
        pass 
```

Enter fullscreen mode Exit fullscreen mode

## 5。内置功能和驱动程序

Django 和 Masonite 都被称为“包含电池的框架”,但 Masonite 被吹嘘为“现代的和以开发人员为中心的 Python web 框架”,因为它包含了包含电池的特性。

Masonite 具有内置亚马逊 S3 上传、RabbitMQ 队列支持、Mailgun 和 SMTP 电子邮件发送支持、简单的数据库播种以轻松拆卸、重建和播种数据的数据库、内置 websocket 支持的 Pusher、内置循环任务调度等功能。如果不需要花太多时间来整合，所有这些功能都是“必备”的，现在只需几秒钟就能设置好。

*   你的应用程序有通知吗？简单，只需使用 websocket 功能向用户推送通知。

*   你的应用程序发送电子邮件吗？用 RabbitMQ 将它们全部排队，这样您的应用程序的响应速度就会更快。

*   你的应用程序处理用户上传吗？使用亚马逊 S3 上传功能来管理它们。

*   应用程序的某些部分需要每小时或每天午夜运行一些任务吗？简单只需使用任务调度功能来调度任务运行，每天，每 3 天，或每 5 天下午 3 点轻松。

Masonite 采用了许多您可能需要花费数小时来研究、实现和测试的功能，并允许您专注于自己的业务逻辑，并使用我们为您构建的集成！

Django 确实有很多现成的功能，但远没有 Masonite 多，我们通常每两周增加一个新功能。明天醒来，你会发现全新的整合。更新 Masonite，您就可以开始摇滚了。

* * *

感谢您给我时间解释为什么人们从 Django 转向 Masonite:)

请务必使用以下链接关注 Masonite:

*   [官方休闲频道](https://slack.masoniteproject.com)

*   [推特](https://twitter.com/masoniteproject)

*   [Masonite 文档](https://docs.masoniteproject.com)

*   [泥瓦匠](https://github.com/masoniteframework/masonite)

*   [Masonite 核心](https://github.com/masoniteframework/core)

*   [牛逼的泥瓦匠](https://awesome.masoniteproject.com/)
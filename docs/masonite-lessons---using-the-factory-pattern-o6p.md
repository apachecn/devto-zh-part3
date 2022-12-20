# Masonite 课程-使用工厂模式

> 原文：<https://dev.to/masonite/masonite-lessons---using-the-factory-pattern-o6p>

# 前言

Masonite 有许多非常正面和明确的设计模式。我的目标一直是，使用 [Masonite](https://github.com/masoniteframework/masonite) 的开发人员通过使用或在文档中阅读这些模式，并通过真实世界的例子理解它们，从而成为更好的开发人员。

我将撰写这些文章，讨论各种设计模式以及我如何在日常应用中使用它们。

# 简介

设计模式非常简单。作为开发人员，我们会遇到代码问题。作为开发人员，我们软件开发的 5 个目标是交付一个产品

*   准时
*   在预算内
*   无故障(无错误)
*   满足现在的需求
*   满足未来的需求

作为开发人员，我们需要能够以满足现在和未来需求的方式完成这些任务。这些都是开发者一直面临的问题。多年来，开发人员一次又一次地遇到同样的问题，就像木匠有“行业诀窍”一样，开发人员也是如此。这些诀窍被称为软件设计模式。它们只是我们在开发过程中经常遇到的问题的解决方案。

一旦你开始成为一名优秀的开发人员，你将能够将问题映射到特定的设计模式，并且能够设计一个软件架构来满足你正在解决的问题的需求。

# 图样

工厂模式用于在运行时实例化一个对象。就是这样。它只是一个类，可以根据传递给它的值返回不同类型的对象。例如，你可以将`chocolate`的值传入`IceCreamFactory`并返回一个`ChocolateIceCream`类。

这可能需要在运行时完成，这也是该模式发挥作用的地方。例如，`chocolate`的值可以存储在为某个用户提取的数据库列中。

# 代码示例

```
class IceCreamFactory:

    flavors = {
        'chocolate': ChocolateIceCream,
        'vanilla': VanillaIceCream,
        'strawberry': StrawberryIceCream,
    }

    def make(self, flavor):
        return self.flavors[flavor]() 
```

就是这样。我们刚刚使用工厂模式创建了一个工厂。现在我们所要做的就是指定一个我们需要的风味，然后我们就可以得到正确的风味了:

```
flavor = IceCreamFactory().make('strawberry') #== <some.module.StrawberryIceCream> 
```

# 问题-现实世界的例子

这里有一个我如何使用这种模式的真实例子。我们正在配置服务器。为简单起见，我们希望提供 2 项服务:

*   Postgres
*   Nginx

现在，我们需要在两种不同类型的服务器上提供这两种服务:

*   人的本质
*   一种自由操作系统

现在请注意，我们有几种不同的可能性可供选择:

*   在 Ubuntu 上安装 Postgres
*   在 Ubuntu 上启动 Postgres
*   停止 Ubuntu 上的 Postgres
*   在 Debian 上安装 Nginx......等等

可能性是指数级的，尤其是当我们添加新的服务器、新的服务或者在它们之间添加层时(例如通过 docker 而不是 bash 命令安装)。

我们如何解决这个问题？这是几种模式的组合，但在本文中，我们将重点关注工厂。

# 解

这里的解决方案是使用 2 个工厂。

第一个工厂将是一个`ServerFactory`，它将负责获取几个服务器(Ubuntu，Debian，Centos，..).

第二个工厂将是一个`ServicesFactory`，它将负责获取几个服务(Postgres，Nginx，RabbitMQ，..).

## 服务器工厂

使用上面的锅炉板，让我们继续创建我们的`ServerFactory`:

```
class ServerFactory:

    servers = {
        'ubuntu': UbuntuServer,
        'debian': DebianServer,
        'centos': CentosServer,
    }

    def make(self, server):
        return self.servers[server]() 
```

注意，我们只是改变了服务器的冰淇淋口味(UNIX 口味？).

## 服务工厂

现在，让我们对服务做同样的事情:

```
class ServiceFactory:

    services = {
        'postgres': PostgresService,
        'nginx': NginxService,
        'rabbitmq': RabbitMQService,
    }

    def make(self, service):
        return self.services[service]() 
```

请注意，所有这些代码都是相同的锅炉板。

## 结合工厂

现在，如果我们从数据库的角度来考虑:

> 一个服务器**有许多**服务

对吗？因为一个服务器**有许多**服务，让我们模仿一下。让我们让`UbuntuServer`访问`ServiceFactory`:

```
from app.factories import ServiceFactory

class UbuntuServer:

    services = ServiceFactory()

    def connect(self):
        self.establish_connection()
        return self 
```

嘣。完成了。现在我们的`UbuntuServer`可以访问我们所有的服务。

# 一起使用

让我们继续在我们的应用程序中使用这两个工厂:

```
from app.factories import ServerFactory

def show(self):
  user = User.find(1)
  user.server #== 'ubuntu' 
  server = ServerFactory().make(user.server) #== <app.servers.UbuntuServer>
  server.connect()

  for service in ('postgres', 'nginx', 'rabbitmq'):
      server.services.make(service).install() 
```

# 未来

设计模式对于解决现在和未来的问题都很有用。这种模式很有用，因为:

*   支持更多服务器就像创建新服务器一样简单。
*   支持更多服务就像创建新服务一样简单。

这两个东西可以彼此独立存在，因此可以彼此独立地放大或缩小。
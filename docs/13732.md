# 更新了 Masonite 2.1 的队列返工

> 原文：<https://dev.to/masonite/updated-queue-rework-for-masonite-21-4hfh>

# 前言

我想写这篇文章来展示这个版本中的一些变化，因为这比几行文件的变化要大一些。

Masonite 中的队列特性允许你将任务放入一些受支持的驱动程序中，以便在以后运行它们，可以是异步的，也可以是通过 RabbitMQ 这样的消息代理。

最初的队列特性是在大约 3 天内编写的，并被放入 Masonite 并发布。它做了我个人需要它做的一切，既然它挠了我的痒，那么也许其他 Masonite 开发者也会发现它很好。

在 Masonite 2.2(将于 2019 年 6 月发布)的开发过程中，我想采用一些概念并“返工”它们，使它们在内部变得更好，这通常只需要从头重写实现，而不涉及外部接口。

换句话说，我想做一个相当大的重写，但也使它成为一个非破坏性的变化，可以进入框架的下一个次要版本。

所以在这篇文章中，我将谈论队列返工的所有新特性。

## 队列:工作命令

`queue:work`命令有一些变化。您现在可以使用`-d`或`--driver`选项:
来指定您想要启动工作器的驱动程序

```
$ craft queue:work -d amqp 
```

这将为特定的驱动程序运行一个队列工作器。

## 失败的作业

失败的工作是 Masonite 的一个非常棒的新增功能。以前，当一个作业失败时，它会卡在队列中。如果作业失败，您必须清除它。

现在，队列中的作业将尝试连续运行 3 次，中间间隔 1 秒钟，最后将该作业称为失败作业。

如果作业失败，它将调用作业的`failed`方法。一个正常的作业现在可能看起来像这样:

```
from masonite.queues import Queueable
from masonite.request import Request
from masonite import Mail

class SendWelcomeEmail(Queueable):
    def __init__(self, request: Request, mail: Mail):
        self.request = request
        self.mail = mail

    def handle(self):
        return 2/0 # fails 
    def failed(self, payload, error):
        self.mail.to('developer@company.com').send('
            The welcome email failed with error {}
        '.format(error)) 
```

第一个参数是试图运行的负载，它是一个信息字典，看起来像这样:

```
payload == {
    'obj': <class app.jobs.SomeJob>,
    'args': ('some_variables',), 
    'callback': 'handle', 
    'created': '2019-02-08T18:49:59.588474-05:00', 
    'ran': 3
} 
```

而错误可能是类似`division by zero`的东西。

现在，当你的工作失败时，你可以非常积极地看待它们

### 保存失败的作业

如果一项工作失败了，它就会消失。您不能再次运行失败的作业。

这就是为什么我们现在有一个失败作业的表和迁移。您可以通过运行:
来创建表并将其迁移到您的数据库中

```
$ craft queue:table
$ craft migrate 
```

这将为您在迁移目录中创建新的迁移文件。然后，您可以手动迁移。

现在，当作业失败时，它会将其序列化并存储在该表中。为了再次运行作业，您可以运行

```
$ craft queue:work --failed 
```

它将所有作业再次发送到队列中重新运行。

## 内部改写

为 Masonite 开发驱动程序的人。这个特性在内部重写了驱动程序的编写方式。所有的驱动程序现在必须从`BaseQueueDriver`继承，这给了每个驱动程序一些特性来符合 Masonite。

因为我知道目前没有其他为 Masonite 编写的队列驱动程序，所以我很乐意做出这样的改变。

队列的契约现在有了更多的方法。这意味着需要更多的方法来创建符合队列协定的队列驱动程序。

`queue:work`命令现在是队列驱动程序不可知的，而以前它只适用于`amqp`驱动程序。只要你的驱动继承了`QueueContract`，你就可以添加一个驱动到 Masonite。

* * *

如果您想贡献或支持 Masonite 的发展，请前往 [Masonite Github](https://github.com/masoniteframework/masonite) 并给它一颗星！
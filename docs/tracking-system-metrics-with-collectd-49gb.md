# 使用 collectd 跟踪系统指标

> 原文：<https://dev.to/nexttech/tracking-system-metrics-with-collectd-49gb>

通过收集系统指标，系统管理员可以监控可用资源、检测瓶颈，并对服务器和项目做出明智的决策。

在 Next Tech，我们将收集的系统指标用于许多方面，例如:

*   检测问题，例如服务器没有足够的资源。
*   相反，对于确定领域，我们可以通过减少服务器资源来节省资金。
*   确保所有服务(如 web 服务器或数据库)都在运行。

在本教程中，我们将介绍如何安装和配置 **[collectd](https://collectd.org/index.shtml)** ，这是一个开源守护程序，它收集系统性能统计数据，并提供存储和发布它们的方法。然后，使用`collectd`的`write_http`插件，我们将使用 HTTP POST 请求将度量数据发送到 **Flask** 应用程序。

我们开始吧！

# 第一步:加载 Python 环境

进入 Python 沙箱最简单的方法是使用下一个沙箱，它让您在几秒钟内就可以访问在线计算环境。你可以[点击这里](https://c.next.tech/2JzId1J)启动一个，或者[点击这里](https://realpython.com/installing-python)阅读如何在你的电脑上安装 Python。

# 第二步:安装 collectd

第一步，安装`collectd`。您可以通过在终端中输入以下命令来做到这一点:

```
apt-get update
apt-get install collectd 
```

一旦`collectd`成功安装，继续下一步——配置守护进程！

# 第三步:配置 collectd

我们需要配置`collectd`,以便它知道收集什么数据以及如何发送收集的值。

在`etc/collectd/collectd.conf`可以找到`collectd`配置文件。如果你懂 Vim，可以直接运行:
修改配置文件

```
vim etc/collectd/collectd.conf 
```

否则，我们将把配置文件链接到一个在沙箱中容易编辑的文件。为此，在您的终端中运行以下三个命令，将原始配置文件链接到我们的新配置文件:

```
cp /etc/collectd/collectd.conf /root/sandbox/collectd.conf
rm /etc/collectd/collectd.conf
ln -s /root/sandbox/collectd.conf /etc/collectd/collectd.conf 
```

现在，打开文件，看看默认的`collectd`配置。这个文件有四个部分:全局、日志、LoadPlugin 部分和插件配置。

## 全局

文件的第一部分显示了全局设置。以散列(`#`)开头的行被注释掉——我们将删除一些散列，这样这些设置如下:

```
Hostname "localhost"
FQDNLookup true
BaseDir "/var/lib/collectd"
PluginDir "/usr/lib/collectd"
#TypesDB "/usr/share/collectd/types.db" "/etc/collectd/my_types.db"

AutoLoadPlugin false

CollectInternalStats false

Interval 10 
```

## 测井

配置文件的下一部分显示了用于记录守护进程在初始化以及加载或配置其他插件时生成的消息的插件。

对于本节(以及下一节)中的每个插件，配置中都有一个`LoadPlugin`行，后面是插件的选项。几乎所有这些行都被注释掉了，以保持默认配置精简。

应该只启用一个日志插件。我们将使用`LogFile`。

删除`LoadPlugin logfile`行之前的散列，并编辑插件配置，通过更改`File`参数将输出写入该文件。这个部分应该是这样的:

```
LoadPlugin logfile

<Plugin logfile>
    LogLevel "info"
    File "/root/sandbox/collectd.log"
    PrintSeverity true
</Plugin> 
```

确保默认日志插件`syslog`被移除或注释掉。

## LoadPlugin 部分

下一部分显示了功能列表。默认情况下，会启用以下插件:

| 插件 | 描述 |
| --- | --- |
| 电池 | 收集电池电荷、汲取的电流和电池电压。 |
| 中央处理器 | 收集 CPU 在各种状态下花费的时间，例如执行用户代码、执行系统代码、等待 IO 操作和空闲。 |
| df | 收集文件系统使用信息，即已装载的分区上使用了多少空间以及有多少空间可用。 |
| 唱片 | 收集硬盘和分区(如果支持)的性能统计信息。 |
| 熵 | 收集系统上的可用熵。 |
| 连接 | 收集有关流量(每秒八位字节数)、每秒数据包数和接口错误(一秒内的错误数)的信息。 |
| 中断请求 | 收集操作系统处理每个中断的次数。 |
| 负荷 | 收集系统负载(运行队列中可运行任务的数量)。这些数字大致反映了机器的利用率。 |
| 记忆 | 收集物理内存利用率-已用、缓冲、缓存、空闲。 |
| 处理 | 收集进程的数量，按其状态分组(如运行、睡眠、僵尸等。).它还可以收集关于所选进程的详细统计信息。 |
| 交换 | 收集当前写入硬盘或系统称为“交换”的任何内容的内存量。 |
| 用户 | 计算当前登录到系统的用户数量。 |

我们的`collectd`守护进程将使用这些插件自动收集数据。可用插件的完整列表和每个插件的简短描述可以在[这里](https://git.octo.it/?p=collectd.git;a=blob;hb=master;f=README)找到。

我们还想启用`write_http`插件，以便`collectd`知道将收集的数据发送到哪里:

| 插件 | 描述 |
| --- | --- |
| write_http | 使用 HTTP POST 请求将 collectd 收集的值发送到 web 服务器。 |

在列表中找到这个插件，移除杂凑来启用它。

## 插件配置

最后一部分显示了所有列出的插件的配置。你可以在 [collectd.conf(5)手册](https://collectd.org/documentation/manpages/collectd.conf.5.shtml)中看到所有的插件选项。

找到`write_http`的插件配置。我们将修改它，如下所示:

```
<Plugin "write_http">
    <Node "example">
        URL "http://127.0.0.1:5000";
        Format JSON
    </Node>
</Plugin> 
```

注意，我们将输出的格式指定为 JSON。

我们不会修改任何其他插件的配置，但是你可以自己随意修改。

# 第四步:验证配置

每当配置文件改变时，重启`collectd`是很重要的。在您的终端中运行以下命令来执行此操作:

```
systemctl restart collectd 
```

您也可以运行此命令来检查`collectd` :
的状态

```
systemctl status collectd 
```

如果一切正常，您应该在输出中看到`Active: active (running)`。

您还应该能够看到`collectd`已经初始化，插件已经加载到您的`collectd.log`文件中。

最后，我们可以通过运行以下命令来验证配置文件中是否存在问题:

```
collectd -t ; echo $? 
```

该命令测试配置，然后退出。它应该返回输出`0`。

# 第五步:创建 Flask app

我们已经让`collectd`正确跟踪我们的指标...现在让我们使用 Flask 创建一个 web 应用程序，这样`collectd`可以通过 HTTP POST 请求发送这些数据！

Flask 是一个强大的微框架，用于用 Python 创建 web 应用程序。它带有内置的开发服务器，是构建 RESTful web 服务的完美框架。帮助将函数绑定到 URL 的 route decorator 可以将 HTTP 方法作为参数，为以理想的方式构建 API 铺平道路。

首先，让我们通过运行以下命令来安装 Flask:

```
pip3 install Flask 
```

现在，创建一个名为`flask_app.py`的新文件。

编写这个程序有三个步骤:

1.  创建一个 WSGI 应用程序实例，因为 Flask 中的每个应用程序都需要一个实例来处理请求。
2.  定义一个将 URL 和处理它的函数相关联的路由方法。
3.  激活应用服务器。

将下面的代码复制到您目录下的`flask_app.py`文件中:

```
from flask import Flask, request

app = Flask(__name__)

@app.route('/', methods=['GET', 'POST'])
def get_data():
    print(request.data)
    return 'This is working!' 
```

这个代码片段执行了前两步——我们使用 Flask 的`Flask`类创建了一个 WSGI 应用程序实例，然后我们定义了一个路由，它映射了路径“`/`”和函数`get_data`,以使用 Flask 的[装饰函数](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route) `Flask.route()`处理请求。

在`Flask.route()`装饰器中，我们将请求方法指定为`GET`和`POST`。然后，我们的`get_data`方法使用`request.data`打印传入的请求数据。

继续我们课程的最后一步，激活应用程序的服务器来查看我们的数据！

# 第六步:运行 Flask app

要运行 Flask 应用程序，首先需要通过导出`FLASK_APP`环境变量:
来告诉终端使用什么应用程序

```
export FLASK_APP=flask_app.py 
```

然后，执行以下命令来启用开发环境，包括交互式调试器和重新加载器:

```
export FLASK_ENV=development 
```

最后，我们可以用下面的代码运行应用程序:

```
flask run 
```

在应用程序开始运行后，在你的终端中你应该看到你的`collectd`数据作为 JSON 输出进来！

# 总结

在本教程中，我们介绍了:

1.  安装`collectd`
2.  配置多个`collectd`插件
3.  构建基本的烧瓶应用程序
4.  从烧瓶内的`collectd`接收数据

我们希望你喜欢这个教程，并学到一些新的东西！如果您有任何意见或问题，请不要犹豫，在下面给我们留言。

本教程摘自一堂关于 Next Tech 的课。如果您有兴趣探索我们的其他课程，[来看看吧](https://c.next.tech/2JVxrCu)！
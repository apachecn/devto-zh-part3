# Pituophis:设置一个 Gopher 服务器

> 原文：<https://dev.to/dotcomboom/pituophis-setting-up-a-gopher-server-17kl>

这是我为地鼠库编写的教程，名为 [Pituophis](https://github.com/dotcomboom/pituophis) ，目前版本为 0.95。虽然 API 或多或少已经完成，但仍可能会有变化。

# 设置服务器

## 安装活塞

如果您还没有安装 Pituophis，请运行`pip3 install pituophis`或 Python 3 安装程序的等效程序。您很可能需要 Python 3.7 或更高版本，因为它使用异步套接字连接。

## 基础脚本

首先，您需要打开您拥有的任何舒适的环境，并创建一个新的 Python 文件。将它保存到一个新目录，然后在同一个目录中创建一个名为“pub”的新目录。

这是应该放在文件中的内容:

```
import pituophis

pituophis.serve('127.0.0.1', 70, pub_dir='pub/') 
```

对于一个将从`pub`目录中提供文件的 Gopher 服务器来说，就是这样！如果可用的话，Pituophis 还会发送每个目录中的 gophermap 文件。要运行服务器，只需保存并启动脚本。如果您使用低于 1024 的端口(如端口 70)，您可能需要以提升的权限运行它。

主机，在本例中是`127.0.0.1`，应该改为客户机将要连接的主机。如果你只打算在你的机器上使用服务器，让它保持原样是没问题的，但是如果你在私有或公共 IP 地址或域名上运行它，就把它改成那样。

## Alt 处理程序

Pituophis 还允许您使用 alt 处理程序，这个函数将接收请求，然后发出要返回的内容。例如，如果 Pituophis 注意到发布目录中不存在路径，它会将请求传递给 alt 处理程序。

要使用 alt 处理程序，首先要定义它并设置它的使用:

```
import pituophis

def alt(request):
    return False

pituophis.serve('127.0.0.1', 70, pub_dir='pub/', alt_handler=alt) 
```

如果 alt 处理程序不能对请求做任何事情，它会返回 False，所以 Pituophis 会像往常一样返回一个错误。

这个例子使用 alt 处理程序发回一个菜单，显示请求的属性和几个选择器:

```
import pituophis

def alt(request):
    if (request.path == '/stats'):
        menu = [
            pituophis.Selector(text="Path: " + request.path),
            pituophis.Selector(text="Query: " + request.query),
            pituophis.Selector(text="Host: " + request.host),
            pituophis.Selector(text="Port: " + str(request.port)),
            pituophis.Selector(text="Client: " + request.client),
            pituophis.Selector(),
            pituophis.Selector(itype="I", text="View server.png", path="/server.png", host=request.host, port=request.port),
            pituophis.Selector(itype="0", text="View some text", path="/txt", host=request.host, port=request.port)
        ]
        return menu
    return False

pituophis.serve('127.0.0.1', 70, pub_dir='pub/', alt_handler=alt) 
```

记住，如果发布目录中存在一个名为`stats`的目录或文件，alt 处理程序将不会运行，而是发送那个静态文件。
[![](img/689c2edd90ddb300a81535f2d9aa8155.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zy2NsO6F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o1qaofxxv9sunancxyyd.png)
# SignalR 核心 python 客户端:一个简单的聊天中心客户端

> 原文：<https://dev.to/mandrewcito/singlar-core-python-client-58e7>

# [T1】简介](#intro)

为了结束这个星期天，我决定出版一个我在工作和在家都在工作的图书馆。我会尽快的。我将在其中加入额外的特性，比如登录(这已经完成了，但是没有包含在 [PyPi 包](https://dev.toPyPi%20package)中)。

我试图保持与其他 signalR 客户的相似性。使得实施更容易。示例将与 [aspnet 核心](https://codeload.github.com/aspnet/Docs/zip/master)聊天示例兼容。

库基于 [websocket_client](https://pypi.org/project/websocket_client/) 移除这种依赖性是我还没有做出的决定。

# 例子

使用该库的一个小方法是:

```
hub_connection = HubConnection(server_url)
hub_connection.build()
hub_connection.on("ReceiveMessage", print)
hub_connection.start()
hub_connection.send("SendMessage", [username, message])
hub_connection.stop() 
```

Enter fullscreen mode Exit fullscreen mode

一个完全控制台的例子，第一次运行一个 signalR 核心聊天的例子，主机和端口必须替换成你的:

```
from signalrcore.hub_connection import HubConnection

def input_with_default(input_text, default_value):
    value = input(input_text.format(default_value))
    return default_value if value is None or value.strip() == "" else value

server_url = input_with_default('Enter your server url(default: {0}): ', "ws://localhost:62342/chathub")

username = input_with_default('Enter your username (default: {0}): ', "mandrewcito")

hub_connection = HubConnection(server_url)
hub_connection.build()
hub_connection.on("ReceiveMessage", print)
hub_connection.start()
message = None
# Do login 
while message != "exit()":
    message = input(">> ")
    if message is not None and message is not "" and message is not "exit()":
        hub_connection.send("SendMessage", [username, message])
hub_connection.stop() 
```

Enter fullscreen mode Exit fullscreen mode

# 未来

*   身份验证(实现已经完成，但还需要一点重构)
*   信息包

# 链接

[Github](https://github.com/mandrewcito/signalrcore)
[Pypi](https://pypi.org/project/signalrcore/)

欢迎对此有任何反馈，在下面留下评论:)。
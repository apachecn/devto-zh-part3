# SignalR 核心 python 客户端(IV):改进语法

> 原文：<https://dev.to/mandrewcito/signalr-core-python-client-iv-improving-syntax-2pf6>

正如 Loongle (github 用户)在 github 上的这个[问题](https://github.com/mandrewcito/signalrcore/issues/2)中所建议的。库语法应该迁移到类似于 javascript 的方法。

```
 this.hubConnection = new signalR.HubConnectionBuilder()
      .withUrl('http://localhost:55563/imessage/chart', {
        accessTokenFactory: () => this.tokenService.get().token
      }).build(); 
```

Enter fullscreen mode Exit fullscreen mode

```
hub_connection = HubConnectionBuilder()
    .with_url(server_url, {
        "access_token_factory": lambda : tokenfactory().token
        })
    .build() 
```

Enter fullscreen mode Exit fullscreen mode

库示例显然会改变，看起来会像:

### 聊天

```
hub_connection = HubConnectionBuilder().with_url(server_url).build()
hub_connection.on("ReceiveMessage", print)
hub_connection.start() 
```

Enter fullscreen mode Exit fullscreen mode

### 与 auth 聊天

```
hub_connection = HubConnectionBuilder()\
    .with_url(server_url, {
        "access_token_factory": lambda: signalr_core_example_login(login_url, username, password)
    })\
    .build()

hub_connection.on("ReceiveSystemMessage", print)
hub_connection.on("ReceiveChatMessage", print)
hub_connection.on("ReceiveDirectMessage", print)
hub_connection.start() 
```

Enter fullscreen mode Exit fullscreen mode

### 流明

```
hub_connection = HubConnectionBuilder().with_url(server_url).build()
hub_connection.start()
time.sleep(10)
hub_connection.stream(
    "Counter",
    [10, 500]).subscribe({
        "next": lambda x: print("next callback: ", x),
        "complete": lambda x: bye(False, x),
        "error": lambda x: bye(True, x)
    }) 
```

Enter fullscreen mode Exit fullscreen mode

我将很快包括这些变化和 catch 块(我接受任何建议)。我在 message pack 之前做了这个改变，这是一个微小的改变，我更喜欢现在做这个改变，因为基本语法的改变太晚了是一种痛苦。

这个帖子的意义是一个问题。你喜欢哪种语法？。将它放到下一个 s 版本库中..这是下一次迁移的好决定？这个周末可能会有变化，所以如果你认为我做得不对，请在下面发帖。

## 待办事宜

*   信息包

## 链接

[Github](https://github.com/mandrewcito/signalrcore)
[Pypi](https://pypi.org/project/signalrcore/)

感谢您的阅读，并在:D 下面写下您的任何想法
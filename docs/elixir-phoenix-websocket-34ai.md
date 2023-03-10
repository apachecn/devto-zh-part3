# elixir :用 phoenix 连接 Websocket 制作简单的聊天 APP

> 原文：<https://dev.to/gumi/elixir-phoenix-websocket-34ai>

[Phoenix](https://phoenixframework.org/) 是用 Elixir 制作的 web 框架。 使用 Channel 的功能可以进行实时通信。 在去年的[gumi Inc. Advent Calendar 2018](https://qiita.com/advent-calendar/2018/gumi) 的“[尝试用 Websocket 连接 Elixir/Phoenix 和 react](https://qiita.com/hiromoon/items/331b7c15a77600a9c3de)”
中，公开了 web sockkke 本文的前半部分，关于 Phoenix 项目聊天室的制作方法稍加说明。 这次使用的是 Elixir 1.8.0 和 Phoenix 1.4.2。

# 制作 Phoenix 项目

关于 Phoenix 的安装，请阅读官方网站的“[Installation](https://hexdocs.pm/phoenix/installation.html) ”。 首先，用[`mix phx.new`](https://hexdocs.pm/phoenix/Mix.Tasks.Phx.New.html)命令创建 Phoenix 项目(项目名称`chat`)。 `--database`选项是 Ecto 中使用的数据库适配器的规定。 这次设为[`mysql`](https://github.com/xerions/mariaex)(默认值为[`postgres`](https://github.com/elixir-ecto/postgrex))。 另外，在这个聊天 APP 应用程序中，不使用数据库的功能。

```
mix phx.new chat --database mysql 
```

Enter fullscreen mode Exit fullscreen mode

APP 展览开始制作时，会被问到`Fetch and install dependencies? [Yn]`。 输入`Y`的话，依存关系也会被安装，所以很简单。

```
Fetch and install dependencies? [Yn] y
* running mix deps.get
* running mix deps.compile
* running cd assets && npm install && node node_modules/webpack/bin/webpack.js --mode development 
```

Enter fullscreen mode Exit fullscreen mode

安装结束时，将显示在本地服务器上启动 APP 应用程序的步骤。 按照指示输入命令后打开`http://localhost:4000`，会显示雏形页面吧(图 001 )。

```
cd chat
mix ecto.create
mix phx.server 
```

Enter fullscreen mode Exit fullscreen mode

#### [001■Phoenix 项目的雏形页](#%E5%9B%B3001%E2%96%A0phoenix%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%81%AE%E3%81%B2%E3%81%AA%E5%BD%A2%E3%83%9A%E3%83%BC%E3%82%B8)

[![1904001_001.png](img/22578f666e9c482f909d2b1e726974b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eVC0qIuY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c06hk8nixrvpq9k2loyt.png)

# [使用](#-raw-channel-endraw-%E3%82%92%E4%BD%BF%E3%81%86)`channel`

Phoenix 项目的`lib/chat_web/endpoint.ex`已经使用[`Phoenix.Endpoint`行为](https://hexdocs.pm/phoenix/Phoenix.Endpoint.html)添加了端点，如下所示。

```
defmodule ChatWeb.Endpoint do
  use Phoenix.Endpoint, otp_app: :chat

  socket "/socket", ChatWeb.UserSocket,
    websocket: true,
    longpoll: false

end 
```

Enter fullscreen mode Exit fullscreen mode

然后，决定端点模块`ChatWeb.UserSocket`的是`lib/chat_web/channels/user_socket.ex`。 [使用的是`Phoenix.Socket`行为](https://hexdocs.pm/phoenix/Phoenix.Socket.html)。 在此模块中，必须将消息路由到适当的信道。 为此，请取消调用[`channel/3`](https://hexdocs.pm/phoenix/Phoenix.Socket.html#channel/3)的代码的注释。

```
defmodule ChatWeb.UserSocket do
  use Phoenix.Socket

  ## Channels
  channel "room:*", ChatWeb.RoomChannel  # コメントアウトを外す

end 
```

Enter fullscreen mode Exit fullscreen mode

现在，只要从客户端以" room:"开头的主题发送消息，就会路由到`ChatWeb.RoomChannel`模块。

# 认证客户端的连接

管理聊天室信息的`ChatWeb.RoomChannel`作为`lib/chat_web/channels/room_channel.ex`新规定。 模块必须使用[`Phoenix.Channel`行为](https://hexdocs.pm/phoenix/Phoenix.Channel.html)来验证客户端是否连接到主题。 为了认证而安装的是[`join/3`](https://hexdocs.pm/phoenix/Phoenix.Channel.html#c:join/3)。

允许任何人连接聊天室" room:lobby "。 其他聊天室都是私人的。 实际上，会通过数据库等进行认证吧。 在这里，我只返回错误。 验证连接的返回值为`{:ok, socket}`。 拒绝时，返回`{:error, reply}`。 现在，Channel 已经准备好了。

```
defmodule ChatWeb.RoomChannel do
  use Phoenix.Channel

  def join("room:lobby", _message, socket) do
    {:ok, socket}
  end

  def join("room:" <> _private_room_id, _params, _socket) do
    {:error, %{reason: "authorized"}}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

Phoenix 项目规定了在`assets/js/socket.js`中安装了套接字的简单客户端。 要连接，只需改写为正确的房间名称(`"room:lobby"`)。 关于插座的连接，请参阅“[Socket Connection](https://hexdocs.pm/phoenix/js/#phoenix) ”。

```
socket.connect()

// Now that you are connected, you can join channels with a topic:
// let channel = socket.channel("topic:subtopic", {})  // 以下に修正
const channel = socket.channel("room:lobby", {});
channel.join()
  .receive("ok", resp => { console.log("Joined successfully", resp) })
  .receive("error", resp => { console.log("Unable to join", resp) })

export default socket 
```

Enter fullscreen mode Exit fullscreen mode

*   [`connect()`](https://hexdocs.pm/phoenix/js/#socketconnect):连接插座。
*   [`channel()`](https://hexdocs.pm/phoenix/js/#socketchannel):初始化第 1 自变量主题的 Channel。 第二个参数是传递给信道的参数。
*   [`join()`](https://hexdocs.pm/phoenix/js/#channeljoin):连接到信道。 返回值为[`Push`](https://hexdocs.pm/phoenix/js/#push)。
*   [`receive()`](https://hexdocs.pm/phoenix/js/#pushreceive):针对第 1 参数的状态，调用第 2 参数的回调。

连接插座的准备已经准备好了，在 APP 应用程序的`assets/js/app.js`上显示`import`。 请启用以下已注释的代码:

```
import socket from "./socket"  // コメントアウトを外す 
```

Enter fullscreen mode Exit fullscreen mode

这样客户端和服务器就连接起来了。 浏览器控制台应该显示" Joined successfully "。 如果将传递给`channel()`的第 1 参数的主题改写为其他名称，则会显示“Unable to join”的错误吧。

# 发送聊天消息

在页面上添加文本输入字段，以便进行聊天(图 002 )。 模板是`lib/chat_web/templates/page/index.html.eex`。 原来的内容全部消失也没关系。 替换为以下代码 001 中的两行。 `<ul>`要素是之后添加发送来的信息的地方。

#### 代码 001■ `lib/chat_web/templates/page/index.html.eex`

```
<ul id="messages"></ul>
<input id="chat-input" type="text"> 
```

Enter fullscreen mode Exit fullscreen mode

#### 图 002■添加了文本输入字段的页面

[![1904001_004.png](img/14a8c4aed9bd668260df412b2e9806a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qz16y1P4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/03h16rl89zghr6s7xmaf.png)

在`assets/js/socket.js`中，添加用于与服务器交换的代码如下。 在文本输入字段中按下[return]/[Enter]键时，用[`push()`方法](https://hexdocs.pm/phoenix/js/#channelpush)发送信息。 第一个参数是事件名称，第二个参数的对象是包含输入字段文本的消息主体。

[`on()`方法](https://hexdocs.pm/phoenix/js/#channelon)接收信息。 仍然为第一个参数提供要等待的事件名称，并确定第二个参数的侦听器函数处理接收到的消息。 总之，决定用`console.log()`方法确认内容。

```
const channel = socket.channel('room:lobby', {});
// 追加↓
const chatInput = document.querySelector('#chat-input');
const messagesContainer = document.querySelector('#messages');

chatInput.addEventListener('keypress', (event) => {
  if (event.keyCode === 13) {
    channel.push('new_msg', { body: chatInput.value });
    chatInput.value = '';
  }
});
channel.on('new_msg', (payload) => {
  console.log(payload.body);  // 確認用
}); 
```

Enter fullscreen mode Exit fullscreen mode

处理经由信道发送来的信息的是回调[`handle_in/3`](https://hexdocs.pm/phoenix/Phoenix.Channel.html#c:handle_in/3)。 在第一个参数事件中进行模式匹配，第二个参数在映射中接收消息主体。 第 3 自变量为[`Phoenix.Socket`](https://hexdocs.pm/phoenix/Phoenix.Socket.html)。 回调在`lib/chat_web/channels/room_channel.ex`中规定如下。

从`handle_in/3`调用的[`broadcast!/3`](https://hexdocs.pm/phoenix/Phoenix.Channel.html#broadcast!/3)向侦听器发送事件。 第 1 参数是套接字，第 2 参数是事件，第 3 参数是要发送的消息。

```
defmodule ChatWeb.RoomChannel do

  def handle_in("new_msg", %{"body" => body}, socket) do
    broadcast!(socket, "new_msg", %{body: body})
    {:noreply, socket}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

从客户端发送页面字段中输入的文本时，通过`handle_in/3`调用的`broadcast!/3`作为事件发送。 然后，在`assets/js/socket.js`中调用由`channel.on()`规定的侦听器函数。 作为消息发送的输入字段中的文本将输出到控制台。 `lib/chat_web/channels/room_channel.ex`的内容总结为以下代码 002。

#### 代码 002■ `lib/chat_web/channels/room_channel.ex`

```
defmodule ChatWeb.RoomChannel do
  use Phoenix.Channel

  def join("room:lobby", _message, socket) do
    {:ok, socket}
  end

  def join("room:" <> _private_room_id, _params, _socket) do
    {:error, %{reason: "authorized"}}
  end

  def handle_in("new_msg", %{"body" => body}, socket) do
    broadcast!(socket, "new_msg", %{body: body})
    {:noreply, socket}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们完成`assets/js/socket.js`的侦听器函数。 从确认用的语句中，按照以下代码 003 所示改写传递给`channel.on()`的第 2 参数的行函数公式。 在动态制作的要素中加入信息正文的文本，插入页面，完成了简单的聊天 APP 的动作(图 003 )。

#### 代码 003■ `assets/js/socket.js`

```
// Now that you are connected, you can join channels with a topic:
// let channel = socket.channel("topic:subtopic", {})  // 以下に修正
const channel = socket.channel('room:lobby', {});
// 追加↓
const chatInput = document.querySelector('#chat-input');
const messagesContainer = document.querySelector('#messages');

chatInput.addEventListener('keypress', (event) => {
  if (event.keyCode === 13) {
    channel.push('new_msg', { body: chatInput.value });
    chatInput.value = '';
  }
})

channel.on('new_msg', (payload) => {
  const messageItem = document.createElement('li');
  messageItem.innerText = `[${Date()}] ${payload.body}`;
  messagesContainer.appendChild(messageItem);
})
// 追加↑
channel.join()
  .receive("ok", resp => { console.log("Joined successfully", resp) })
  .receive("error", resp => { console.log("Unable to join", resp) }) 
```

Enter fullscreen mode Exit fullscreen mode

#### t0003■从输入栏发送的文本被添加到页面中

[![1904001_005.png](img/6c87a35b6badb5fabf4bfb6178cd25d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K5oRtwj5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sxa78c0jfi9i2ja7zkyz.png)
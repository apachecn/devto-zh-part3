# 什么是 websockets

> 原文：<https://dev.to/joshualoran/what-are-websockets-14f5>

web 套接字是一种计算机通信协议，它通过单个 TCP 连接提供全双工通信通道。TCP 代表传输控制协议，是[互联网协议簇](https://en.wikipedia.org/wiki/Internet_protocol_suite)中的主要通信协议之一。建立全双工通信是 web 套接字的强大功能。

什么是全双工通信？想象一段只有一条车道宽、两端都有邮局的道路(A 和 B)。汽车可以向任何一个方向行驶，但只有当对方告诉他们一切正常时才能行驶，否则他们可能会迎面相撞(而且，如果他们稍微偏离道路，他们就会爆炸成一百万个闪亮的碎片)。所以，一辆车离开 A，向 B 行驶，B 知道不要走，直到来自 A 的车到达 B，说道路畅通。汽车 A 可能请求将某些东西送回邮局 A，和/或它可能通过投递一些邮件来传达一些信息。

[![alt text](img/60bbbbf8cf28c4e963e1e38bd441b1c6.png "One Lane Road")](https://res.cloudinary.com/practicaldev/image/fetch/s--zVt0oNVU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.dangerousroads.oimg/stories/__Roads00000e/BigKahekili%2520Highway0.jpg)

这在网络交流中经常发生。客户端发送一个请求和/或信息，服务器获取请求/信息，处理它并发回一些信息。轮流，我的幼儿园老师，史密斯夫人，很高兴，一切都很好。但是 web sockets 抛弃了所有的礼貌。

我们将单车道道路描绘成标准通信，其中两个实体通过轮流发送请求和信息来进行通信。但是，我知道这很疯狂，如果有一条双车道公路。如果有这样一个世界，两个实体可以随时发送信息，并且可以随时接收对方发送的信息。这种双向道路意味着每一方都不必发出请求，因为不需要控制轮到谁，一个实体只需要“订阅”另一个实体来接受可能来自该方向的任何信息。

[![alt text](img/d561fad911e6e14b52753cad357bbf7c.png "Two Lane Road")](https://res.cloudinary.com/practicaldev/image/fetch/s--fvWpcz1W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.pinimg.com/originals/39/3a/81/393a813c18c76da08c22efc0c6086413.jpg)

好吧，另一个思维实验。实体 A 是执行某些功能并返回答案的服务器。然而，这一次不只是实体 B 作为客户端，有数百个客户端，它们都有这条通往/来自服务器的 2 车道道路。服务器现在可以更新所有客户端，而不需要来自每个单独客户端的请求/响应。服务器可以简单地将一些信息“广播”给同时“订阅”该通道(或频道)的所有客户端，并且每个客户端可以将信息发送给服务器进行处理。

酷！客户端不再需要轮询或长时间轮询来保持与服务器状态同步。信息现在是实时的，由双方在处理后发送，并在任何时候收到。我们现在生活在一个自由的世界里，一切都随心所欲。现在我们可以做一些很酷的事情，比如制作多人实时游戏！

为了深入了解 web 套接字，我制作了一个使用它们的双人井字游戏。Ruby on rails 和 Action cable 为初学者提供了一个非常简单的 web 套接字实现。这里是一些基本的设置，让你开始使用 Ruby 和 Actioncable，并带有一个 React 前端来使用 websockets。

首先通过在控制台中输入以下内容来生成 React 前端:
 `create-react-app <your-frontend-project-name>
cd <your-frontend-project-name>
yarn add actioncable
yarn start` 
然后通过在控制台中输入以下内容来启动 rails 后端线框:
 `rails new <your-backend-project-name> --api
cd <your-backend-project-name>
rails g scaffold <your-model-name> <your-models-attributes>
rails db:migrate` 
下一步是通过在 rials 控制台中创建一个实例来持久化模型的实例:
 `rails c
<your-model>.create!(attributes)` 
确保通过取消注释 gem 文件中的“rack-cors”gem 和应用程序中的以下内容来设置 CORS

```
config.middleware.insert_before 0, Rack::Cors do
      allow do
        origins '*'
        resource '*', :headers => :any, :methods => [:get, :post, :options]
      end
    end 
```

运行捆绑包安装。

现在来连接 actioncable。接下来的几个步骤相当简单。导航到您的`config.routes.rb`，并在最后一个`end`前添加以下内容。
 `mount ActionCable.server => '/cable'` 
然后，到你的控制台输入如下:
 `rails g channel <your-model-name>` 

现在 Actioncable 可以接收进入`/cable`的 websockets，你的 rails 后端将有一个新文件`app/channels/<your-model>_channel.rb`

打开那个文件。

```
class <your-model>Channel < ApplicationCable::Channel
  def subscribed
    # stream_from "some_channel"
    stream_from '<your-model>'
  end

  def unsubscribed
    # Any cleanup needed when channel is unsubscribed
  end

  def receive(data)
    <your-model> = <Your-Model>.find(data["id"])
    <your-model>.update!(attr: data["attr"])
    ActionCable.server.broadcast('<your-model>', data)
  end
end 
```

这里，我们将`stream_from`添加到订阅方法中，然后告诉 rails 如何处理 receive 方法中接收到的数据。我们的后端现在设置为流媒体！

[![alt text](img/08541c6dbb143814c5e5a3eae02b10d7.png "cool stream")](https://res.cloudinary.com/practicaldev/image/fetch/s--B3Y2fD0a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media0.giphy.com/media/2csuIJj6TmuKA/giphy.webp%3Fcid%3D790b76115cdb073431614a326f5eedb6%26rid%3Dgiphy.webp)

现在，让我们设置我们的前端来订阅并与我们的后端对话。为了实现这一目标，我们需要做一些事情。首先，连接到我们在后端设置的`/cable`。第二，订阅我们的模特频道。最后，用户在前端发送任何数据。

为了完成这三件事，我们必须首先导入 actioncable，方法是将`import ActionCable from 'actioncable'`放在 app.js 的顶部，并在 componentDidMount 方法中创建一个消费者。接下来，我们必须设置我们的订阅，并在收到数据时设置回调。最终的文件可能类似于:

```
import React, { Component } from 'react'
import './App.css'
import ActionCable from 'actioncable'

class App extends Component {
  state = { <your-state>: undefined }

  componentDidMount() {
    window.fetch('http://localhost:3001/<your-model>/1').then(data => {
      data.json().then(res => {
        this.setState({ <your-state>: res.<model-attribute> })
      })
    })

    const cable = ActionCable.createConsumer('ws://localhost:3001/cable')
    this.sub = cable.subscriptions.create('NotesChannel', {
      received: this.handleReceiveNewText
    })
  }

  handleReceiveNewData = ({ <model-attribute> }) => {
    if (<model-attribute> !== this.state.<your-state>) {
      this.setState({ <model-attribute> })
    }
  }

  handleChange = e => {
    this.setState({ <your-state>: e.target.value })
    this.sub.send({ <your-state>: e.target.value, id: 1 })
  } 
```

这就是用 rails 和 react 设置 websocket 的要点。显然还需要做更多的事情，比如在屏幕上显示/抓取信息(受控输入是最好的)、数据净化、授权等等。但是，这是一个让你开始的超级简单的设置。
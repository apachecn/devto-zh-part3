# 针对使用 React/Rails 堆栈的 Flatiron 学生的提示

> 原文：<https://dev.to/isalevine/tips-for-flatiron-students-working-with-a-react-rails-stack-3l0l>

这篇文章是专门为 Flatiron 学校的学生写的，他们的项目需要同时使用 React 前端和 Rails 后端。通过用这个设置做几个项目，包括在 Mod 4 中一个非常有挑战性的 ActionCable 实现，我想传递一些使用这个堆栈的建议。(对发现这些有用的人表示敬意！)

首先，我最重要的建议是:

## 如果你正在使用 ActionCable，不要急于使用“React-ActionCable-Provider”包，除非你已经知道如何使用 ACTION-CABLE！

这是我在上一个项目中犯的主要错误:由于没有太多关于使用带有 ActionCable 的 Rails 后端的 React 项目的文档/社区帮助，我匆忙地使用了 [react-actioncable-provider 包](https://github.com/cpunion/react-actioncable-provider),而没有花时间彻底地学习它——甚至没有事先学习 actioncable。当然，我浏览了一些半相关的 ActionCable 教程等等，但是当我读到那个包的推荐时，我立刻投入进去，再也没有回头。

通常，我尽量避免在我的学校项目中使用软件包/gems/其他捆绑软件(或者至少默认使用它们)，因为我担心对软件包了解太多而对我正在学习的核心语言了解不够，或者走捷径而错过有价值的底层学习。在这种情况下，我在两个方面都搞砸了——我花了大量的时间试图调试和使用软件包本身，最终在项目之外对 ActionCable 或 WebSockets 了解很少，我知道在我熟练使用它之前，我需要对 ActionCable 进行更多的练习。

### 以下是我希望自己花时间在行动中学会的几件事:

**1。创建订阅**——这是 react-actioncable-provider 抽象掉的东西，所以创建和管理多个订阅变成了一个令人困惑的麻烦。下面是软件包中的代码——我建议花时间通读一下[action cable docs on Rails Guides](https://edgeguides.rubyonrails.org/action_cable_overview.html#example-1-user-appearances)和一些教程，以了解如何使用每个函数(**接收、初始化、连接、断开、拒绝**)工作:

**更新:我的同学 Josh 刚刚发表了[一篇优秀的 WebSockets 和 ActionCable](https://dev.to/wtfcodingpotato/what-are-websockets-14f5)** 的概述和教程，也来自他的 Mod 4 项目——这是一个很好的起点！！

```
var ActionCableController = createReactClass({

    this.cable = this.props.cable.subscriptions.create(this.props.channel, {
      received: function (data) {
        onReceived && onReceived(data)
      },
      initialized: function () {
        onInitialized && onInitialized()
      },
      connected: function () {
        onConnected && onConnected()
      },
      disconnected: function () {
        onDisconnected && onDisconnected()
      },
      rejected: function () {
        onRejected && onRejected()
      }
    })
  }, 
```

**2。将 Auth 构建到集合类**中——这是一个重要的部分，包括在包含游戏或聊天室的程序中，在这些程序中，你希望限制特定用户的访问——显式地利用和探索‘app/channels/application _ cable/Connection . Rb’中的连接类，并在**早期**这样做，有助于防止以后的问题重构和调试。再次， [Rails Guides 通过 docs:](https://edgeguides.rubyonrails.org/action_cable_overview.html#server-side-components-connections)

```
 # app/channels/application_cable/connection.rb
module ApplicationCable
  class Connection < ActionCable::Connection::Base
    identified_by :current_user

    def connect
      self.current_user = find_verified_user
    end

    private
      def find_verified_user
        if verified_user = User.find_by(id: cookies.encrypted[:user_id])
          verified_user
        else
          reject_unauthorized_connection
        end
      end
  end
end 
```

**3。部署到 Heroku** -我甚至不知道对此该说些什么…因为我仍然不完全理解*是哪些*最后一刻的配置让应用在 Heroku 上神奇地运行了差不多四分钟。只是，要确保给自己留有充足的时间，提前尝试在 Heroku 上部署一个 ActionCable 教程应用，以了解会发生什么。

react-actioncable-provider 无疑是一个强大的包，我肯定要把那个项目中任何成功的 actioncable 功能归功于包的创建者李杰(Li Jie)-但是我最好先学习 ActionCable，而不是试图同时学习它和这个包。

不要冲进去！**先学动作——索！！**

## 铁轨提示

以下是我在项目过程中获得的一些方便的 Rails 指针/技巧:

### active support::JSON . encode()和 ActiveSupport::JSON.decode()

该项目涉及使用 ActionCable 来广播来自 [Canvas drawings](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API) 的巨大 JSON 格式的线条和像素数据阵列，以及将它们作为字符串存储在数据库中以供以后检索和重画。这让我想到了方便的 Rails 模块， [ActiveSupport::JSON](https://api.rubyonrails.org/classes/ActiveSupport/JSON.html) ，它有两个有用的方法:。encode()和。decode()，它的功能与您在 Javascript 中可能熟悉的 JSON.stringify()方法非常相似:

**。encode()会把一个 JSON 对象变成一个字符串:**

```
 ActiveSupport::JSON.encode({ team: 'rails', players: '36' })
# => "{\"team\":\"rails\",\"players\":\"36\"}" 
```

您可以将它存储在数据库中的单个字符串或文本单元格中！

**。decode()将接受一个 JSON 格式的字符串并返回一个 Ruby hash:**

```
ActiveSupport::JSON.decode("{\"team\":\"rails\",\"players\":\"36\"}")
=> {"team" => "rails", "players" => "36"} 
```

*来自[活动支持模块文档](https://api.rubyonrails.org/classes/ActiveSupport/JSON.html)的片段*

这种编码/解码策略会变得非常低效，尤其是 JSON 对象越大，但是将整个内容作为一个字符串塞进数据库的一个单元格中是非常有趣的！

### 在 Rails 中，可以使用 params["_json"]访问 Javascript fetch()请求发送的 JSON

在前端，我用字符串化的 JSON 向 Rails 发送 fetch() post 请求，如下所示:

```
 patchMessage = (newMessage) => {
    fetch(`${API_ROOT}/messages/${this.props.message.id}`, {
      method: "PATCH",
      headers: HEADERS,
      body: JSON.stringify(newMessage)
    })
  } 
```

在紧要关头，我们公然无视强参数，使用 params["_json"]，从控制器内部的请求中访问那个 JSON——这里有一个例子，以防您结束类似的紧要关头:

```
 # /app/controllers/messages_controller.rb
  def update
    @message = Message.find(params[:id])
    @message.update(text: params["_json"])
    render json: @message
  end 
```

## 反应过来的提示

最后，一些反应技巧/窍门:

### 用 React-Router 传递道具(没错，它让代码看起来很丑)

如果使用 React-Router 是一个项目需求，您可能会发现自己需要通过一个组件传递道具，而不是作为道具传递的组件。[道具(哈哈！)对于泰勒·麦金尼斯的这个解决方案:](https://tylermcginnis.com/react-router-pass-props-to-components/)

```
<Route exact path="/" render={(props) => <UserForm {...props} rerenderApp={this.rerenderApp} />} /> 
```

所以:创建一个 render={} prop，用一个箭头函数将(props)传递给你的组件，并像平常一样在那里指定组件的 props。

### 不想更新状态？请改用 this.forceUpdate()

这是我在尝试让窗口滚动和调整大小来重新渲染一些组件时尝试的一个技巧...我不认为它有效，但我偶然发现了 this.forceUpdate()方法，它允许您在不设置状态的情况下强制重新呈现:

```
 handleResize = () => {
      this.forceUpdate();
  }; 
```

### 省去部署的麻烦——将所有 URL(如 API 端点)作为常量存储在一个文件中

这可能只是良好的编码卫生，但是让自己成为一个 constants.js 文件，在一个地方保存所有相关的常数——比如 **API 端点 URL**和 **fetch()请求头**。当你在 Heroku 上部署并需要交换 URL 时，你的生活将会变得*简单得多。不要忘记导出它们！* 

```
// /src/constants/index.js
export const API_ROOT = "https://draw-n-discuss-backend-rails.herokuapp.com/";
export const API_WS_ROOT = "ws://localhost:3000/cable";
export const HEADERS = {
  'Content-Type': 'application/json',
  'Accept': 'application/json'
} 
```

## 结论

我希望你们 Mod 4 的学生能在这里找到有用的东西！祝你的项目好运，请随时在评论中添加任何进一步的建议！
# 使用 React & Rails 进行 Spotify Web API 授权

> 原文：<https://dev.to/jasonbasuil/spotify-web-api-authorization-with-react-rails-32ni>

在进入熨斗学校模块 4 的项目周时，我一直热衷于学习如何使用 OAuth 从一些我最喜欢的应用程序中访问真实、有趣的数据。在我之前参与的项目中，我要么使用假数据，要么使用公开可用的数据(最低限度需要一个 API 密匙)。

然而，我真的想使用来自真实人物的数据，并利用这些数据创建一个有趣的应用程序。这就是 OAuth 出现的原因，我决心要搞清楚。有人警告我，在应用程序中实现 OAuth 可能会很有挑战性，尤其是如果您是第一次这么做的话。在 YouTube 教程、信息丰富的博客帖子和其他人的帮助下，我和我的项目合作伙伴能够在项目的第一天获得真实的 Spotify 数据。

### OAuth 是什么，为什么要用它？

对于第三方应用程序来说，这是获得对 web 服务及其附带数据的有限访问的一种简单方法。它允许您的应用程序访问和/或修改用户自己的数据。

[![Authentication vs. Authorization](img/b2bd1f3b1a279861ec7b45ab22d631c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pzbn6l8q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://swoopnow.com/wp-content/uploads/2018/02/security-authentication-user-authorization-websites.png)

### 授权流程概述

[![Authorization Flow](img/ea7904551e2569430acfc9cf1ccb369d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x09iwo8Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.spotify.com/assets/AuthG_AuthoriztionCode.png)

### 1 -创建一个简单的 react 主页

*   创建-反应-应用
*   主页上有一个按钮，可以重定向到 Spotify 授权页面

```
class Button extends Component {
  render() {
    return (
      <a href="http://localhost:3000/api/v1/login">
      Log in to Spotify
      </a>
    )
  }
} 
```

### 2 -在 Spotify Developer 上创建您的应用

*   命名您的应用程序
*   接收您的 client _ id/client _ secret(Spotify 如何识别您的应用程序并同意或拒绝适当的响应)
*   指定一个 redirect_uri(在请求之后你的应用程序去哪里？)

### 3 -设置 Rails 后端 API

```
rails new <metrobeat_backend> --database=postgresql --api 
```

*   设置您的模型和控制器
*   创建和迁移数据库
*   配置您的路线

```
# OAuth
  namespace :api do
    namespace :v1 do
      get '/login', to: "auth#spotify_request"
      get '/auth', to: "auth#show"
      get '/user', to: "users#create"
    end
  end 
```

### 4 -创建一个 AuthController 来处理初始请求

```
class Api::V1::AuthController < ApplicationController

  def spotify_request
    url = "https://accounts.spotify.com/authorize"
    query_params = {
      client_id: Rails.application.credentials[Rails.env.to_sym][:spotify]    
      [:client_id],
      response_type: 'code',
      redirect_uri: 'http://localhost:3000/api/v1/user',
      scope: "user-library-read 
      playlist-read-collaborative
      playlist-modify-private
      user-modify-playback-state
      user-read-private
      user-top-read
      playlist-modify-public",
     show_dialog: true
    }
    redirect_to "#{url}?#{query_params.to_query}"
  end
end 
```

*   范围是这个请求的一个更有趣的参数。它告诉我们

*   如何隐藏您的 client_id
    1)更改您的凭据
    2)格式化您的凭据
    3)读取您的凭据

这里有一篇有用的文章解释了如何隐藏你的密钥:
[如何在 rails 中隐藏你的秘密](https://medium.com/@jonathanmines/hiding-your-secrets-in-rails-5-using-credentials-e37174eede99)

### 5 -创建一个用户控制器来处理访问和刷新令牌

这一过程中较为脆弱和复杂的部分之一。为什么？

*   向 Spotify API 发出 post 请求，请求用户访问和刷新令牌
*   保存用户的访问和刷新令牌
*   处理用户访问令牌的 1 小时过期时间

```
#Update the user access/refresh_tokens
    if @user.access_token_expired?
      @user.refresh_access_token
    else
      @user.update(
        access_token: auth_params["access_token"], 
        refresh_token: auth_params["refresh_token"]
      )
    end 
```

### 6 -访问和保存真实的 Spotify 数据

有趣的部分！

*   确保您可以正确保存用户数据
*   从我们的后端 API 获取用户及其访问令牌
*   从用户在初始授权请求范围内同意的任何 Spotify Web API 端点获取
*   随心所欲地操纵你得到的回应

### Spotify 端点&你可以用它们做很酷的事情

*   获取用户的播放列表、曲目、专辑等。
*   使用 Spotify Web 播放 SDK
*   音频分析(小节、节拍、速度、可跳性、语速等。)

关于 Spotify API 端点的完整列表:
[Spotify 开发者- Web API 参考](https://developer.spotify.com/documentation/web-api/reference-beta/)

### 流程中的关键要点

*   这个过程一开始可能会令人望而生畏，但是有几个资源可以帮助你。
*   文档是关键！幸运的是，Spotify 的 Web API 文档简单易懂。即使一段代码对你来说没有意义，上下文最终也会帮助你理解它。
*   使用 OAuth 为使用真实世界的数据来制作酷而有趣的应用程序打开了许多大门。
*   它让您更清楚哪些类型的数据可供请求使用它的应用程序使用。

### 有用资源:

[Spotify API 与 React 和 rails 的授权流程](https://medium.com/@davidjtomczyk/spotify-api-authorization-flow-with-react-and-rails-7f42845a43c)
这家伙(他也是一个熨斗毕业生，在这个项目的初始设置中帮了大忙)
[Spotify 授权文档指南](https://developer.spotify.com/documentation/general/guides/authorization-guide/)
[如何在 Rails 中隐藏秘密](https://medium.com/@jonathanmines/hiding-your-secrets-in-rails-5-using-credentials-e37174eede99)
[Rest 客户端](https://www.rubydoc.info/gems/rest-client/1.8.0)
[DevTips:从 Spotify API 中提取数据- #13 React JS 原型制作](https://www.youtube.com/watch?v=rzk8n-4gMWs)
# 我的 React/Redux 投资组合项目

> 原文：<https://dev.to/kritirai/my-react-redux-portfolio-project-2h4k>

[![Trailista](img/86ea504a328a8fe27960bee084ec43ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RwN2c0Ey--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/fla25b0.jpg) 
为了我 Flatiron 在线 fullstack 编程课程的最后一个项目，我构建了一个名为***[Trailista](https://github.com/kriti-rai/trailista)***的应用程序，它根据位置从[徒步项目数据 API](https://www.hikingproject.com/data) 中拉出一个路径列表。你可以在这里观看我的应用程序[的简短演示](https://www.youtube.com/watch?v=Tq6YfMEufpQ&t=1s)或者继续阅读，因为我简要介绍了设置和我在这个过程中遇到的一些障碍。

这个应用程序肯定是一个 WIP，但到目前为止，这里是可用的功能:

*   人们可以按城市和国家搜索路径

[![search](img/9ac588c59ca8c08a8574308687746728.png)](https://i.giphy.com/media/lrbwszQmty9ybUdBQs/giphy.gif)

*   人们可以登录或注册

[![registration](img/7a93427faacb50900a5befb8948d81ca.png)](https://i.giphy.com/media/j0verhIyrSczORIdK0/giphy.gif)

*   人们可以将踪迹添加到他们的收藏夹列表中，然后根据自己的意愿删除它们

    [![favorites](img/a6a227572f4c993fee60e669e7702614.png)](https://i.giphy.com/media/mDXrTRqf6EMfTAyNat/giphy.gif)

## 工作方式

*   我的应用程序从*徒步旅行项目 API* 端点提取路径，看起来像这样

> [www.hikingproject.com/data/get-trails?lat=40.0274&lon =-105.2519&max distance = 10&KEY = APP _ KEY](http://www.hikingproject.com/data/get-trails?lat=40.0274&lon=-105.2519&maxDistance=10&key=APP_KEY)

*   客户端提供 *lat* 、 *lon* 和 *maxDistance* 的值以及*徒步项目*提供的*键*

*   然后，该应用程序接收用户输入并将其插入到一个`fetch`请求中，然后将其发送到*徒步旅行项目* API 以获得响应

听起来非常简单，除了用户向应用程序提供地址，例如一个地方的名称，而*徒步旅行项目* API 端点想要 GPS 坐标形式的地址，即纬度和经度。所以我必须想办法将位置输入转换成 GPS 坐标。在翻找了一段时间*徒步项目*的 FAQ 论坛后，我发现有人在一个帖子中提到使用 *[MapBox](https://docs.mapbox.com/api/search/#geocoding)* 来执行他们所谓的“正向地理编码”，即把地址转换成坐标的过程(相反的是*反向地理编码*)。我也调查了谷歌地图 API，但显然他们收费了。所以，我决定继续使用 MapBox。

伙计，这很有趣吗？是的，它包含了一些扣人心弦的插曲，但是我对我处理它的方式感到相当自豪。我的两个功能，我的应用程序的英雄，`getCoordinates()`和`fetchHikes()`，协同工作来实现这一点。

*   `getCoordinates()`接收用户输入，并向*地图框* API 发送一个`fetch`请求，如下所示:

```
getCoordinates= (e) => { 
    e.preventDefault();
    axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${this.state.city}.json?country=${this.state.countryCode}&access_token=APP_KEY`)
      .then(resp => {
        this.setState({
          longitude: resp.data.features[0].geometry.coordinates[0],
          latitude: resp.data.features[0].geometry.coordinates[1]
        })
                ... 
```

*   然后，响应数据用于设置状态中的`longitude`和`latitude`的值，然后通过调度动作`fetchHikes()`向*徒步项目的* API 发出`fetch`请求

```
getCoordinates= (e) => {
...
   this.props.fetchHikes(this.state.latitude, this.state.longitude, this.state.maxDistance, this.state.maxResults)
  })
} 
```

## 该设置了

这篇[文章](https://www.fullstackreact.com/articles/how-to-get-create-react-app-to-work-with-your-rails-api/)通过用 Rails API 设置一个 React 应用程序，很好地指导了人们。我对我将使用的资源有一个粗略的想法，所以我首先从我的模型和控制器开始，然后设置(API)路由(在 *'/config/routes.rb'* )，我可以向其发送`fetch`和/或`post`请求。

### 重要！

需要注意的一点是你的 *CORS* (跨源资源共享)设置。由于我的前端和后端运行在不同的域上，我必须找到一种方法来告诉我的后端服务器，我的前端发送的请求是无害的。如果您最初用命令`$ rails new myapp --api`设置 Rails API，那么您会得到一个已经设置好的`cors.rb`文件，您只需取消对以下命令的注释:

```
#Gemfile

gem 'rack-cors' 
```

```
#app/config/initializers/cors.rb

Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins 'localhost:3000'

    resource '*',
      headers: :any,
      methods: [:get, :post, :put, :patch, :delete, :options, :head]
  end
end 
```

一旦所有这些都处理好了，我就转移到我的*客户端*端，也就是前端部分。此外，在从*徒步旅行项目*中提取数据之前，我创建了一些种子数据来测试这些数据在我的前端是如何呈现的。

### 路线

我在`App.js`设置了路线。我在互联网上发现的一件有趣的事情是，如果一个人没有登录，我可以创建一个助手`PrivateRoute`来锁定一条路线，比如我的“*/用户*”路线。这篇[文章](https://reacttraining.com/react-router/web/example/auth-workflow)讲述了如何建立一个`PrivateRoute`。

### 组件

我从表示组件开始，它们大多是无状态的，并且有 DOM 标记。随着我的进一步发展，我最终添加了道具、状态、连接到商店等等，这些都促使我考虑根据组件的功能——容器和表示——来划分组件。容器与事情如何工作有关，包含逻辑，并且大多数时候是有状态的。另一方面，后者只是“愚蠢”的组件，只关心*事情看起来如何*，没有任何逻辑或状态知识。

然而，正如 Dan Abramov 在这篇文章中指出的，如果没有必要，这种模式不应该作为硬性规定来执行。因此，你可能会看到我的一些组件既作为容器又作为表示，或者我的一些表示组件内部有逻辑，而不是纯粹的表示。

### 减速器

说到关注点的分离，我决定让一个缩减器只管理状态的一个独立部分，而不是一个巨大的缩减器。所以，我最后用了三个减速器，`alertsReducer`，`hikesReducer`，`userReducer`。所有这些缩减功能然后被组合成一个单独的缩减器，`rootReducer`，使用`redux`提供的`combineReducers`辅助功能。这个减速器就是传递给`createStore`的东西。

# 结论

我从构建这个应用程序中获得了很多乐趣。就像所有其他的项目一样，这个项目伴随着挫折而来，但我明白了，正是这一点让最终的成就变得如此值得。然而，这还没有结束，我还想继续在应用程序上工作，增加一些更多的功能，如评论，用户可以评级和创建远足等等。据我所知，天空是极限，永远有改进的空间。到目前为止，我很高兴我已经准备好接受最终评估了。旅程才刚刚开始！
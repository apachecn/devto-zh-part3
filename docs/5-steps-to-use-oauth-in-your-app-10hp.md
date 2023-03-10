# 在应用中使用 OAuth 的 5 个步骤

> 原文：<https://dev.to/crishanks/5-steps-to-use-oauth-in-your-app-10hp>

# OAuth 是什么？

[![tinder oauth example](img/68e7b85d9b6bb70c62862710eb26bbd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uDTQqXsu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zhpq7exsjpvl8ypm9j29.png)

OAuth 允许开发人员为特定用户创建超精选内容。它本质上允许用户授予你的应用程序有限的权限，以访问 Spotify、脸书 Instagram 等其他应用程序的特定、允许的功能。因此，开发者可以使用用户已经为自己策划的功能和内容。Searchmicroservices 表示，“允许最终用户的账户信息被第三方服务使用，如脸书，而不会暴露用户的密码。”

你以前见过这个。约会应用 X 想要访问你的 Instagram 图片。音乐流媒体应用 Y 想要访问您的脸书个人资料信息。这样的例子不胜枚举。

在我看来，OAuth 之所以如此有趣，是因为它能够管理对用户更有价值的个性化内容。为什么？通过 OAuth，用户选择让我们的应用程序集成他们已经在使用的另一个应用程序的某些功能，我们可以假设这已经为他们提供了大量的价值。

# 为什么要用 OAuth？

想象一下，一个应用程序可以帮助用户创建与朋友的图片拼贴，并在他们生日时自动将这些图片发送给他们。如果在我们的应用程序中，用户无需打开另一个应用程序，就可以即时访问带有标记好友的图片列表，这不是很好吗？如果他们不必离开我们的应用程序，整理他们发布的图片，手动找到那些标记的图片，并在每个生日为每个朋友导入它们，这难道不是很酷吗？在这个假设的例子中，OAuth 将是允许我们访问用户的照片和标签(他们可能已经非常关心的东西)的门户，所以我们可以为他们做所有这些。

# 在应用中使用 OAuth

在这个例子中， [Jason Basuil](https://dev.to/jasonbasuil) 和我连接到 Spotify 公共 API 来创建 MetroBeat，这是一个通过猜测提供给他们的歌曲的速度(BPM)来制作播放列表的应用程序。

OAuth 有 3 个主要参与者:**用户**(Stacy)**消费者** (MetroBeat)，以及**服务提供商** (Spotify)。

### 步骤 1 -获取客户端 ID 和客户端密码

[![OAuth rails routes](img/fae9eb19045ce60c309180e8528b7a14.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vsmzdFAu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y96e5sie0c4lab4dc31h.png)

[通过 Spotify Developer 设置账户](https://developer.spotify.com/dashboard/)。演练很简单。他们会给你一个客户 ID 和客户密码。确保不要向任何人透露这些！您需要将这些内容包含在对 Spotify 的初始请求中，以便获得访问令牌并发出请求(我稍后会解释)。向 Spotify 发出初始请求，其中包含客户端 ID 和密码的编码版本。[关于如何在 Rails 中编码信息的更多细节](https://medium.com/cedarcode/rails-5-2-credentials-9b3324851336)。

### 步骤 2 -向服务提供商验证消费者身份

我们首先设置了 MetroBeat 来重定向到`get '/login', to: "auth#spotify_request"`。`#spotify_request`使用客户 ID 和密码向 Spotify 发出初始请求，确认是我们，我们彼此认识，并建议我们应该找个时间一起吃早午餐。这会将 Stacy 重定向到一个提示，询问她是否允许 MetroBeat 访问她的 Spotify 帐户。

[![Rails Auth Controller](img/8cf23a1ee739bf49b322580e303ed702.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CurwT862--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9vodi6cakz1elg92jdw2.png)

这段代码向 Spotify 发出请求，并包含一个到我们的`get '/user', to: "users#create"`的重定向。您还会注意到这个请求的主体指定了我们需要 Stacy 同意的权限(范围)。您可以根据需要添加任意多或任意少的数量。最佳实践要求我们不要添加任何不必要的东西。当 Stacy 说一切正常时，她被创建为一个用户，并被分配了一个访问令牌。

### 步骤 3 -创建用户并为她分配一个访问令牌

由于我们发出了最初的请求，我们将在 Spotify 的响应中得到一些参数。我们需要从中获取`params[:code]`,并将其与客户端 ID 和机密一起包含在我们的请求体中。我们将再次向 Spotify 请求刷新令牌和访问令牌。

消费者(MetroBeat)需要提醒服务提供商(Spotify)，每次我们通过 Spotify API 向 Stacy 的 Spotify info 发出请求时，都是我们。这是通过使用**访问令牌**来完成的。

[![Refresh and Access Token](img/724a909719f42dc50cb50f5e2da03181.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AHd6zKEE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c8ipdbvd56u8nxszjtqd.png)

当 Stacy 授予 MetroBeat 访问她的 Spotify 帐户的权限时，我们需要将这个访问令牌放在对 Spotify API 端点的每个请求中。
这些`auth_params`将为我们取回访问权和刷新令牌，我们需要这些令牌来进行请求。`user_params`包含来自 Stacy 的 Spotify 帐户的个人资料信息，我们可以用它来验证她的身份并让她登录(比如她的用户名和个人资料图片。我们看不到她的密码)。

然后，我们可以将她创建为用户，并确保在数据库中将这些用户和身份验证参数与她相关联，以便我们以后可以使用它们。

[![rails create user](img/4c3170dc8f4cbd76309059c0511a3931.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1zLlh0wR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fg3w9y6o62bvup4u731j.png)

Spotify 的访问令牌每小时过期一次，所以如果 Stacy 已经玩了大约 55 分钟，我们会申请一个新的令牌。

### 步骤 4 -从服务提供商 API 请求用户信息

现在我们可以提出一些请求，并看到数据变得栩栩如生！我们可以创建新的播放列表并向其中添加歌曲，查看歌曲分析，包括歌曲速度、国家热门曲目等等。

为了将这些请求附加到组件和事件监听器，我们将移动到 ReactJS 前端。

[![React fetch user](img/f7e1ce757f009c44f2bebb8b2db9544f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3cUBTcqb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h8ymf7ukfhw2js8471bw.png)

[![React fetch playlists](img/f931e1731b77435c392adda60ed03259.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V8EXTszV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dty9wiln0at37erlccag.png)

[![React create playlist](img/8dba1464d15dd843de00fd14936a82c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mca7WMWH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n56udsoxzahp1dfaueei.png)

###### *用户[1]最初在某些领域是硬编码的，只是为了测试目的*

我们提出了其他请求(例如，获取歌曲分析信息)，但是没有限制。您需要密切关注服务提供商的请求格式。每个请求类型的头和主体需要匹配它们的需求。

### 步骤 5 -在 React 组件中实现请求

现在剩下要做的就是决定何时何地显示我们从请求中得到的信息。在计划您的请求时，请确保牢记[异步编程](https://eloquentjavascript.net/11_async.html)。您需要先取回用户信息和访问令牌，然后才能发出其他请求。

[![react login button](img/7441a43306b80cb28884e97f45a97de8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yqAFJkk---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4kt6e4sgltbpyaupy4vp.png)

[![react song card](img/dada4a6da8b8968ec8fe82e1b8c299ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fr7Fc0vk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/okst81sfav8w582yv43n.png)

# 结论

OAuth 是一种非常有趣和安全的技术，可以帮助您创建方便、有组织、定制的内容，这些内容对用户来说非常重要。用户、消费者和服务提供商在整个过程中密切合作，来回传递访问令牌和其他重要信息，以验证你的应用程序，并允许你通过服务提供商访问用户的信息。使用 Rails 和 React 是一种很好的方式来提出这些请求，并创建完全属于自己的用户体验。
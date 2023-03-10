# 关于 Cloudflare Workers 新特性的简要指南

> 原文：<https://dev.to/cloudflare/a-brief-guide-to-what-s-new-with-cloudflare-workers-di8>

上周末，在 JSConf EU 2019 上， [@ag_dubs](https://twitter.com/ag_dubs) 上台向大会介绍了我们在 Cloudflare 上构建的无服务器平台。

液体错误:内部

演讲结束后，Cloudflare 工作人员的大量更新立即上线。说真的——即使其中一个是令人兴奋的发布，但总的来说，团队向我们的无服务器平台发送了*六个*新更新:

*   一个官方的，开源的工人 CLI 工具，[牧马人](https://github.com/cloudflare/workers)
*   新的和改进的[文档](https://workers.cloudflare.com/docs)
*   支持将多个 Workers 脚本部署到您的域
*   免费 workers.dev 子域来部署您的应用程序
*   工人的免费层:每天最多 100k 个请求*(!)*
**   用于编辑和发布应用程序的改进界面*

 *结果是:一个部署无服务器应用程序的超快速平台，一个坚如磐石的 CLI 工具，以及支持它的文档。

> ![signalnerve profile image](img/b90940c6c33667ec482462417e2d07e6.png)信号神经[@信号神经](https://dev.to/signalnerve)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)🎁@cloudflare Workers 今日更新:
> 
> 🆓10 万个请求/天空闲层
> 🤠全新的 CLI
> 📔全新文档网站
> 🥳免费 [workers.dev](https://t.co/Lr1dzwH1vN) 子域名来部署你的项目
> 
> 更多关于这个的下周，但是请查看 [@ritakozlov_](https://twitter.com/ritakozlov_) 的文章👇
> 
> 2019 年 06 月 02 日下午 14:21[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1135189702997360641)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1135189702997360641)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1135189702997360641)

所以！说了这么多，我们该如何开始呢？作为开发人员的拥护者，我的主要目标之一是让开发人员更容易开始使用这个平台。随着我们新的 CLI 工具的推出，这比以往任何时候都更容易。让我们使用 Workers 部署一个简单的应用程序，这样我们就可以看到它在实践中是什么样子。

*顺便提一下:要将您的应用程序部署到员工中，您需要一个 Cloudflare 帐户。好消息！我们有一个奇特的新注册流程来帮助你做到这一点。[在这里报名](https://dash.cloudflare.com/sign-up/workers)。*

## 安装和生成

[Wrangler](https://github.com/cloudflare/workers) 是我们全新的命令行工具，用于创建、构建和发布 Cloudflare Workers 项目。安装起来超级简单:

```
npm install -g @cloudflare/wrangler 
```

Enter fullscreen mode Exit fullscreen mode

为确保成功安装牧马人，运行`wrangler --help`:

[![Wrangler Help](img/0861ef6150b3281fafe764a5666a425b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9F6dlK1J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ocw7un613zky4mmz6t5u.gif)

我最喜欢的牧马人功能之一是`generate`命令。我们已经建立了大量的模板(查看我们的[模板库](https://workers.cloudflare.com/docs/templates/))，这使得快速开始一个新的工人项目变得非常容易。默认情况下，Wrangler 使用我们的 JavaScript + Webpack 模板，因此您可以立即开始使用 NPM 包，并在您的项目中使用新的 JavaScript 功能。现在让我们试试`generate`命令:

```
wrangler generate my-first-app
cd my-first-app 
```

Enter fullscreen mode Exit fullscreen mode

在幕后，Wrangler 使用 Git 复制我们的模板，并填写您的配置细节，使项目成为您自己的。现在你已经创建了一个项目，让我们打开`index.js` :
，看看代码的内部

```
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

/**
 * Fetch and log a request
 * @param {Request} request
 */
async function handleRequest(request) {
  return new Response('Hello worker!', { status: 200 })
} 
```

Enter fullscreen mode Exit fullscreen mode

每个 Workers 应用程序都从监听一个`fetch`事件开始。当客户端向您的应用程序发出 HTTP 请求时，您的工人代码可以使用`event.respondWith`来响应该事件:相应地，该响应应该返回 JavaScript [Response](https://workers.cloudflare.com/docs/reference/runtime/apis/fetch/#response) 类的一个实例。

我们的事件处理器`handleRequest`非常简单。它接受一个`request`参数(类型为[请求](https://workers.cloudflare.com/docs/reference/runtime/apis/fetch/#request)，并创建一个新的`Response`返回给客户端。该响应返回文本“Hello worker”；通过更改响应的主体(第一个参数)，并设置自定义标题和状态，您可以构建并向客户端返回您想要的任何响应！

如果你曾经和[服务人员](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers)一起工作过，这个 API 对你来说应该很熟悉。Workers platform API 在很大程度上模仿了 Service Worker API，所以即使您是在 Workers 平台上开发的新手，像`addEventListener`和事件处理这样的东西应该会让您感觉更舒服一些——它仍然只是 JavaScript！

所有工人应用程序都部署到“边缘”:这是一个对遍布全球的 Cloudflare 服务器的庞大[网络](https://www.cloudflare.com/network/)的花哨术语。这实际上意味着你的应用程序离你的用户很近*。这种构建 *edge 应用*的模式是对典型客户端/服务器模式的一个非常显著的改变:您的用户不需要从弗吉尼亚州的服务器来回发出请求来使用您的应用——相反，您的应用将被发布到离您在世界各地的所有用户最近的 Cloudflare 服务器上。在边缘上构建应用程序是非常令人兴奋的，我很高兴能够在未来继续探索它的含义。*

 *## 配置和发布

有了处理请求和提供响应的应用程序，我们现在可以把代码送到边缘了！虽然牧马人很棒，但不幸的是，我们不能逃避以配置的形式做一点家务:牧马人需要您的 Cloudflare 帐户详细信息，以便能够成功部署您的项目。您将需要来自您的 Cloudflare 帐户的一些值来将代码部署到 Cloudflare 工作人员:

*   全局 API 密钥
*   与您的 Cloudflare 帐户关联的电子邮件地址
*   帐户 ID

为了找到这些值，我们在文档中编写了一个简短的指南，帮助您快速获取信息:[“查找您的 Cloudflare API 密钥”](https://workers.cloudflare.com/docs/quickstart/api-keys/)

准备好这些值后，让我们配置 Wrangler 以部署到您的 Cloudflare Workers 帐户。为此，我们将使用 Wrangler 的`config`命令！运行`wrangler config`应该会以交互方式提示您输入电子邮件和 API 密钥:

要将 Wrangler 配置为部署到您的 Cloudflare Workers 帐户，请通过`config`子命令设置您的默认凭据。您应该只需要这样做一次。运行`wrangler config`应该会以交互方式提示您输入您的电子邮件和 API 密钥:

```
$ wrangler config
Enter email:
foo@bar.com
Enter api key:
123456abcdef 
```

Enter fullscreen mode Exit fullscreen mode

要配置您的项目，请在生成的项目的根目录下完成`wrangler.toml`文件。该文件包含 Wrangler 连接到 Cloudflare Workers API 并发布您的代码所需的信息。我们在这个文件中的主要任务是用在您的仪表板中找到的值填充`account_id`字段。

```
# The name of your Workers application
name = "my-first-app"

# Your Cloudflare account ID
account_id = "$yourAccountId"

# The kind of application you're deploying to Cloudflare (defaults to "webpack")
type = "webpack" 
```

Enter fullscreen mode Exit fullscreen mode

还有一个配置步骤，也是重要的一步:是时候选择您的`workers.dev`子域了。每位 Cloudflare 用户都会得到一个，所以请明智地选择！对于我的项目，我将被部署到`signalnerve.workers.dev`。当你准备好申请一个子域时(如果你*已经有了*的子域，你可以跳过这一步)，使用`subdomain`命令:

```
wrangler subdomain signalnerve 
```

Enter fullscreen mode Exit fullscreen mode

Wrangler 的更新配置意味着所有新的应用程序都将被部署到该域上:例如，我们的项目`my-first-app`将在`my-first-app.signalnerve.workers.dev`发布。

有了配置好的子域，剩下唯一要做的事情就是发布我们的项目。万岁！正如你可能想象的那样，我们也有一个命令:

```
wrangler publish 
```

Enter fullscreen mode Exit fullscreen mode

Wrangler 将接受您的项目，构建它，并将其发送到我们的网络。几乎立刻，您应该看到项目在您的域中实时运行:

[![Live](img/749cd57ee92577b30f6ffc03070b75eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GQ_snM3Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m7k6osq3fuy2y7yt830t.png)

至此，我们已经部署了我们的第一个 Workers 应用程序——太好了！

我们将何去何从？如果你有兴趣了解你可以构建什么，我们的新文档有一些很棒的教程来帮助你[构建一个应用程序](https://workers.cloudflare.com/docs/tutorials/build-an-application)或者[构建一个无服务器功能](https://workers.cloudflare.com/docs/tutorials/build-a-serverless-function)，并且如前所述，[模板库中越来越多的模板](https://workers.cloudflare.com/docs/templates/)可以用于你的下一个伟大的项目。

你用工人部署了什么吗？请让我知道！我很想在 Twitter 上听到什么进展顺利(什么不顺利)[。](https://twitter.com/signalnerve)**
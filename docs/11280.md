# 为什么我用的是 Surge 而不是 GitHub 页面

> 原文：<https://dev.to/bholmesdev/why-im-using-surge-and-not-github-pages-4lf5>

*更新*
将在这里留下一个小小的免责声明，Surge 最近没有看到多少关于其回购的更新，所以请注意这一点。现在使用肯定是安全的(还没有看到任何漏洞警告)，但在未来值得期待。如果这个项目最终消亡，Netlify 和 [Zeit Now](https://zeit.co/now) 是**伟大的**替代品，提供了与 GitHub 页面类似的好处👍

## 嗯，那么到底什么是“激增”呢？🤔

GitHub Pages 在这一点上非常丰富，可能不需要介绍。另一方面，Surge 是一个低调得多的项目。它实现了与 GitHub Pages 相同的目标，允许用户在一个可访问的 URL 上发布他们的静态站点，URL 可以是他们选择的以“https”结尾的任何名称。surge 的不同之处在于该网站是如何被放到万维网上的。

Surge 可以让你在命令行中从 0 到 published，无需任何额外的设置。一旦您安装了这个包，您就可以在您的项目目录中使用他们的 CLI 来创建一个帐户...

```
👉 surge

    Welcome to surge!
    Please login or create an account by entering email and password:

    email: jamesKPolk@napoleonofthestump.gov
    password: 
```

Enter fullscreen mode Exit fullscreen mode

输入要部署到的域...

```
 domain: forgottenpresidents.club 
```

Enter fullscreen mode Exit fullscreen mode

然后嘣💥！你站起来了。注意，我在这里也使用了自定义的 URL 扩展。当然，如果您拥有该域的权限，您可以指定这一点，但是您可以省略扩展名来使用完全免费的`surge.sh`。

## 看起来很容易，但是我的 git 钩子在哪里？

GitHub Pages 的美妙之处在于，它会简单地跟踪您的主分支，并在更新时重建/发布您的站点。令人欣慰的是，surge 提供了一个选项，只需对您的`package.json`做一点点补充，就可以挂钩推送和提交。这个代码片段将完成推送时的重新部署:

```
"devDependencies": { "surge": "latest", "git-scripts": "0.2.1" },
    "git": { 
        "scripts": { "pre-push": "surge --project ./ 
        --domain forgottenpresidents.surge.sh" }
       } 
```

Enter fullscreen mode Exit fullscreen mode

## 整齐。但是到底有什么好处呢？

Surge 提供了一些 GitHub 页面不会开箱即用的细节。首先，您显然不需要使用 GitHub 来管理您的版本控制，因此您可以使用任何您想要的定制解决方案，并且仍然使用相同的`surge`命令进行部署。

另一个优势是 [Zeit Now](https://zeit.co/now) 也未能解决的:客户端路由回退。

扩展我们的[詹姆斯·K·伯克尔](https://www.youtube.com/watch?v=StTiCU_fqCg)的例子，假设我们部署了一个简单的项目文件夹，其中有几个`html`文件:

```
 ./build
      - index.html
      - what_i_stand_for.html
      - donate_now.html
      - 200.html 
```

Enter fullscreen mode Exit fullscreen mode

注意我们的`200.html`加在底部。每当给定的路由无效时，这就作为一种后备服务。假设这些都是我们构建中的`html`文件，我们知道有人访问，比如说，forgottenpresidents.club/sendfanmail 会得到一个`404`，因为那个文件不存在。然而，由于我们有一个`200.html`，Surge 知道只提供这个页面，因为这是一个后备。这比传统的`404.html`更灵活地捕捉错误，因为我们有时不希望某些路径导致`404`，比如在 JavaScript 中处理的 URL 中的动态用户名，但应该总是路由到同一个基本页面。这使得路由更加简洁，不管你是使用 React router 之类的库，还是自己开发浏览器的历史 API，因为我们不再需要处理重定向了🎉

最后一个主要优势是 Zeit Now 共享的:在几秒钟内随时部署。您可以立即部署您的更改并查看它们的效果，而不是弄脏您的提交历史。最近在测试我正在开发的一个网站的移动布局时，这非常有用，在这个网站上，mobile Safari 比我的 Chrome 浏览器更挑剔。我可以将我的更改部署到我选择的任何 URL，并在刷新页面后立即看到我的编辑！

## 好吧，我开始明白了。有哪些局限性？

因此，Surge 是为非常擅长完成特定任务而打造的。如果除了静态站点或客户端应用程序之外，您还拥有其他任何东西，那么很遗憾，您无法使用这个工具做太多事情。您拥有的任何花哨的后端都需要单独部署，或者您可以使用前面提到的 Zeit Now 在一个地方部署所有东西。现在是一个更加灵活的工具，正在迅速获得牵引力，但我仍然忠于 Surge，因为路由的细微之处和缺乏任何必要的`config`文件。

当然，您应该使用在您的工作流程中最有效的工具。但是下一次你在构建你的投资组合网站的 v175 时，可以考虑尝试一下 Surge😊

## 学点小东西？

太棒了。如果你错过了，我发布了一个[我的“网络魔法”时事通讯](https://tinyletter.com/bholmesdev)来探索更多像这样的知识金块！

这个东西解决了 web 开发的[【首要原则】](https://www.swyx.io/first-principles-approach/)。换句话说，是什么让我们所有的 web 项目运转起来的所有 janky 浏览器 API、弯曲的 CSS 规则和半可访问的 HTML？如果你正在寻找超越框架的*，这是给你亲爱的网络巫师的🔮*

 *[立即在此订阅](https://tinyletter.com/bholmesdev)。我保证永远教，永远不会垃圾邮件❤️*
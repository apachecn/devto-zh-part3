# 工具箱 4:使用工具箱窗口模块实现刷新到更新版本流程

> 原文：<https://dev.to/webmaxru/workbox-4-implementing-refresh-to-update-version-flow-using-the-workbox-window-module-4e3c>

[![](img/b3bef4b2ff120dfc464e38f25e140c73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fOOVBgMc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AL0WSeQSNj3Q6kzNv5rN4bg.png)

非常受欢迎的 PWA helper 库的下一个主要版本刚刚发布。工具箱 4 给现有的模块带来了许多有趣的增加，只有[一些小的突破性变化](https://developers.google.com/web/tools/workbox/guides/migrations/migrate-from-v3#breaking_changes)。此外，它还提供了一个名为 [*工具箱窗口*](https://developers.google.com/web/tools/workbox/modules/workbox-window) 的全新模块，以一种简单而强大的方式来满足开发人员的需求，以注册服务人员，挂钩到其生命周期，并提供与应用程序的双向通信渠道。这是在*窗口上下文*中使用的第一个 Workbox 模块，即在我们的应用程序(不是服务人员的)代码中使用。

让我们探索一下这个新模块，看看构建众所周知的“刷新到更新版本”技术(UX PWA 最佳实践之一)需要什么。由于我们在构建应用程序时经常使用这个流程，并且 Workbox 现在公开了相应的工具，所以我们只需要找到一个简单且健壮的代码来构建这个流程。这篇文章是我试图找到代码:最小和稳定。但是首先，我所说的这种流动是什么？

### 刷新-更新-版本 101

你打开一些网站。几秒钟后，它会显示一些提示/对话，说“该网站的新版本可用。[刷新页面获取]”。在大多数情况下，这意味着:

1.  这是一个服务工作者驱动的起源(如果它也有 Web App Manifest，我们可以称之为 PWA)，您看到的 UI 不是从网络上获取的，而是从浏览器的缓存中获取的
2.  你不久前使用这个浏览器访问了这个网站
3.  在上次访问和当前访问之间，部署的版本已更新

你看到的对话是一种权衡，一种是立即显示应用程序 UI 的可能性，而不是从网络上获取它(使用预缓存版本)，另一种是将应用程序的实际版本交付给用户的需要。那么妥协在哪里呢？事实上，我们仍然从缓存存储中加载应用的预缓存(“旧”)版本，但如果服务人员知道有新版本可用，它会获取更新的资源，更新缓存并向应用发送消息(使用广播频道 API 或 postMessage)。我们捕捉到这条消息，并显示出臭名昭著的“应用程序已更新。点击刷新上传”消息给用户。下一页加载—我们从缓存中提供“新”版本(当然，如果我们的服务人员以正确的方式执行了上面列出的所有操作)。

这种技术的另一个变体——我们不从服务人员那里发送任何信号，而是在我们的应用程序中监听其生命周期的变化。在我们的例子中，由提取字节不同的服务工作器引起的 onupdatefound 和 onstatechange 事件的组合可能意味着服务工作器中注入的“to precache”列表中提到的资源的散列和的改变。反过来，这意味着该应用程序的新版本已经建立——因此我们可以安全地显示“该应用程序已更新”的消息。

### Workbox v3 选项有刷新到更新版本流程

首先，让我们感谢 Workbox 维护者提供了神奇的 precacheAndRoute()方法，我们可以在自己的服务人员中使用它。这种方法处理预缓存、版本维护、获取更新的资源、更新缓存等所有的复杂性。我们只需传递带有资源及其散列和的对象(由来自 Workbox 系列的另一个助手构建——Workbox-build 模块),它就可以工作了。此外，service worker 中的另一行代码:
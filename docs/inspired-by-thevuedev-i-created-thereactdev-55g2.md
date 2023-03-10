# 受@thevuedev 的启发，我创建了@TheReactDev

> 原文：<https://dev.to/frontendwizard/inspired-by-thevuedev-i-created-thereactdev-55g2>

这个周末我偶然发现了这篇文章:

[![danielelkington image](img/109027ba17c3a9f28592a0c05ad89a3d.png)](/danielelkington) [## 一个在推特上发布关于 Vue 的新文章的机器人

### 丹尼尔·艾尔金顿 3 月 3 日 196 分钟阅读

#vue #typescript #serverless](/danielelkington/a-bot-that-tweets-new-dev-articles-about-vue-4p5a)

我立刻想到:“嘿，这是个很棒的主意。让我们也做一个关于反应的，为什么不呢？”

我照做了。我创造了一个机器人，它每分钟都检查 dev.to/t/react/latest，并在推特上发布任何新文章。在这里: [@TheReactDev](https://twitter.com/thereactdev)

# 打字稿？那鸿

我决定不在这本书上打字。这是一个超级小的函数，我觉得 JavaScript 可以更快地完成这项工作。

# 无服务器

当你做无服务器时，你有许多选择。可以去 AWS，Azure，GCloud 等。这篇文章详细解释了如何设置 Azure 功能，因为我以前从未使用过 Azure，所以我也跟着做了。Azure 与 VSCode 的集成非常酷，不可否认，但我可能会切换到无服务器框架，成为某种平台不可知的。不过，对于这样的项目来说，这并不重要。😄

**更新**:我对 azure 有些头疼。可能是因为我没有太多的经验。因此，几个小时后，我切换到 AWS，使用无服务器框架作为工具。

# 刮削

因为 dev.to 还没有 api，所以我不得不从 url 中删除数据。我以前用过木偶戏，但我绝对喜欢这个`x-ray` lib。API 太干净了。我肯定会用更多的。

# 我遇到的问题

起初，当我从@TheReactDev 获取最近的 tweets 时，twitter api 并不总是返回实体上发布的 URL。

```
[
  //...
  [ { url: 'https://t.co/AA3sKrLsDZ',
      expanded_url:
       'https://dev.to/peterj/run-a-react-app-in-a-docker-container-kjn',
      display_url: 'dev.to/peterj/run-a-r…',
      indices: [Array] } ],
  [ { url: 'https://t.co/hptME9oAgf',
      expanded_url: 'https://twitter.com/i/web/status/1102686466436681731',
      display_url: 'twitter.com/i/web/status/1…',
      indices: [Array] } ],
  // ..
] 
```

正因为如此，我开始检查推文正文上的文章标题和作者。问题是 twitter API 有时会截断主体。这个问题的解决方案是在 tweets 请求上使用`tweet_mode: "extended"`。

但是，就在我写这篇文章的时候，我发现这也解决了链接的问题。在这种模式下，twitter API 不仅会返回`full_text`，还会返回完整的`extended_url`。😅

检查 URL 更加可靠，所以我重写了我的函数来使用它。

# 电报

我还添加了一个电报机器人，它可以在出现问题时向某个频道发送消息。我选择电报，因为这可能是我收到通知最快的方式。

# 回购

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [前端向导](https://github.com/frontendwizard) / [真实开发](https://github.com/frontendwizard/TheReactDev)

### 馈@TheReactDev twitter 的函数。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 真实发展

馈@TheReactDev twitter 的函数。

这个项目的灵感来自于[这篇文章](https://dev.to/danielelkington/a-bot-that-tweets-new-dev-articles-about-vue-4p5a)。

这个函数依赖于几个环境变量:

*   `dev_tag`:爬虫将获取最新文章的 dev.to 标签。
*   将发布文章的账户名。
*   Twitter 键
    *   `twitter_consumer_key`
    *   `twitter_consumer_secret`
    *   `twitter_access_token`
    *   `twitter_access_token_secret`
*   为了维护/调试的目的，电报机器人将记录消息
*   `telegram_chat_id`:机器人发送消息的通道

您只需要创建一个`env.yml`文件并将这些值放入其中，就像这样:

```
dev_tag: "react"
twitter_bot_screen_name: "NameOfTheTwitterAccount"
twitter_consumer_key: "..."
twitter_consumer_secret: "..."
twitter_access_token: "..."
twitter_access_token_secret: "..."
telegram_bot_token: "..."
telegram_chat_id: "..."
```

值得注意的是，我在这里使用了电报，因为它很简单，而且可能是…

</article>

[View on GitHub](https://github.com/frontendwizard/TheReactDev)

# 喜欢吗？

嗯，做这个非常有趣。当你看到该功能在 twitter 上发布新文章时，你会有一种奇妙的感觉。😄

如果你喜欢，一定要关注 twitter 上的 [@TheReactDev](https://twitter.com/thereactdev) ！

谢谢你的时间，直到下一个！
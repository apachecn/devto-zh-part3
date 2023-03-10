# Reddit 机器人制作 Reddit 回复机器人

> 原文：<https://dev.to/seiyria/making-a-reddit-reply-bot-f55>

有很多关于用 Python 编写 reddit 机器人的文档，但我甚至很难找到 Node 的基本文档——甚至 reddits 官方 wiki 上列出的一些库已经过时或已有 5 年历史(阅读:不太支持新的 reddit)。所以，我想写一个简单而常见的用例:*回复给你贴标签的用户*。

### 创建 Reddit 应用程序

首先，前往[https://www.reddit.com/prefs/apps](https://www.reddit.com/prefs/apps)，点击“创建应用”——你需要这样做，这样 reddit 就不会使用你的个人用户账户。你也应该为你的机器人注册一个新的 reddit 账号(特别是当它可以被召唤的时候)。确保您将您的主帐户和 bot 帐户添加为该应用程序的开发人员。

创建应用程序时，您需要填写如下字段:

[![](img/1b5a18f72fbc7f95a06f18ecced86365.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yORT8DHs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/vclhFhT.png)

您不需要担心`about uri`或`redirect uri`字段，因为您不会使用它们，所以您可以输入您想要的任何内容。

一旦进入，您将看到这样的屏幕:

[![](img/c5e396fe085a79d860fd9a889ddde815.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0Iks7Fxi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/IKpfGVt.png)

请注意这一点，因为您很快就会需要这些信息。

### 启动节点工程

对于类似这样的东西，我发现使用 [`snoostorm`](https://www.npmjs.com/package/snoostorm) (一个包裹 [`snoowrap`](https://www.npmjs.com/package/snoowrap) )非常容易。这个库使得收到评论变得异常简单。

您首先需要创建一个`snoowrap`对象，然后使用它来创建一个`CommentStream`对象。为此，您需要您的 reddit bot 用户名、密码、应用程序密码和应用程序 id。您必须指定一个唯一的用户代理，所以称它为类似于`my-node-js-bot`的东西。所以，这样配置吧(我的是根据上图配置的):

```
const Snoowrap = require('snoowrap');
const { CommentStream } = require('snoostorm');

const client = new Snoowrap({
    userAgent: 'my-node-js-bot',
    clientId: 'qR6rJnQ7sEJZDw',
    clientSecret: 'OCoo9pYnlC2K6fxQQxbcIPQ5MA4',
    username: 'myusernamebutactuallybot',
    password: 'mypasswordbutactuallybot'
}); 
```

Enter fullscreen mode Exit fullscreen mode

有了这个客户机对象，您终于可以开始监听新的评论了！前往 [/r/testingground4bots](https://www.reddit.com/r/testingground4bots) ，加入一个线程或创建自己的线程。然后，添加一些代码开始观看评论:

```
// pollTime is 10000 because reddit is very strict on posting too frequently
// at first, you'll only be able to post once every 10 minutes, so make sure you get it right!
const comments = new CommentStream(client, { 
    subreddit: 'testingground4bots', 
    limit: 10, 
    pollTime: 10000 
});

comments.on('item', (item) => {
    console.log(item);
}); 
```

Enter fullscreen mode Exit fullscreen mode

启动这个机器人，你会在你的终端上看到大量的评论。你可能想知道这是为什么-你甚至还没有看到任何新的进来！嗯，当你启动你的机器人时,`client`将总是给你前 X 个条目(在这个例子中是 10 个),然后它将从那里开始跟踪。

我们可以很容易地解决这个问题:

```
// reddits api doesn't use millis
const BOT_START = Date.now() / 1000;

const comments = new CommentStream(client, { 
    subreddit: 'testingground4bots', 
    limit: 10, 
    pollTime: 10000 
});

comments.on('item', (item) => {
    if(item.created_utc < BOT_START) return;

    console.log(item);
}); 
```

Enter fullscreen mode Exit fullscreen mode

太好了，现在你只能看到最新的评论了。希望你已经有了足够的 reddit 账号，可以在这个 subreddit 上发表一些帖子。如果你这样做了，你会看到你的终端在发布后很快就填满了它们。

### 使其相互作用

到目前为止，你已经有了一个机器人，它可以阅读评论——这是一个很好的开始！但是你想让它和你的观众互动，对吗？那么，一个美好的“你好世界”怎么样？来看看:

```
const BOT_START = Date.now() / 1000;

const comments = new CommentStream(client, { 
    subreddit: 'testingground4bots', 
    limit: 10, 
    pollTime: 10000 
});

comments.on('item', (item) => {
    if(item.created_utc < BOT_START) return;

    item.reply('hello world!');
}); 
```

Enter fullscreen mode Exit fullscreen mode

在那里，任何时候有评论进来，机器人都会回复“你好，世界！”等待...那可能会经常说话，不是吗？这可能会有点烦人。 Reddit 建议在提到你的机器人时特别回复，所以有一个相当简单的方法:

```
const BOT_START = Date.now() / 1000;

const canSummon = (msg) => {
    return msg && msg.toLowerCase().includes('/u/myusernamebutactuallybot');
};

const comments = new CommentStream(client, { 
    subreddit: 'testingground4bots', 
    limit: 10, 
    pollTime: 10000 
});

comments.on('item', (item) => {
    if(item.created_utc < BOT_START) return;
    if(!canSummon(item.body)) return;

    item.reply('hello world!');
}); 
```

Enter fullscreen mode Exit fullscreen mode

那里！所以，这样做是为了确保你的机器人找到的评论*实际上是指机器人本身。这个`canSummon`函数将做基本的检查，以确保你的机器人不会错误地发送一堆人的评论。现在做一个评论说`/u/myusernamebutactuallybot`(更确切地说，你应该检查一下*你自己的机器人名字*)，你应该很快就会看到一个回复说“你好，世界！”作为回答。*

这就是你要做的！🎉
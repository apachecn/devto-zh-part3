# 围棋电报机器人

> 原文：<https://dev.to/detunized/telegram-bot-in-go-3hd4>

这是我第四次或者第五次尝试喜欢围棋。以前的几次都不太管用。不过，我责怪自己。我相信我选择了错误的项目类型。这次是命中注定的。这是一个后端应用程序，它被部署到一个远程服务器上，它使用并发性，并且不太复杂。这是一个电报机器人，帮助我跟踪随机的事情。

## 想法

我希望能够向聊天发送简单的命令，让机器人记住发生了什么，什么时候。这有点像日志文件。我希望能够问机器人什么时候或者多长时间发生一次事情。它可能是这样的:

```
me: eat
bot: Recorded 'eat'. Last 'eat' happened 8 hours ago.

me: sleep, 10 hours ago
bot: Recorded 'sleep' at 23:15, yesterday.
     Last 'sleep' happened 1 day 12 hours ago.

me: /since run
bot: Last 'run' happened 2 days 15 hours ago.

me: /stats blog
bot: So far 'blog' happened 20 times, average
     time between events is 4 days 5 hours. 
```

问题和答案有点笨拙，但我不认为我会在这里实现整个自然语言处理的事情。这是个简单的机器人，记得吗？我有更多有用的命令，但是我将从记录和基本查询开始。

## 建筑

这个词有点大，是吧？为了与电报服务器对话，我将对电报机器人 API 使用 [Go 绑定。我计划在最终版本中使用 webhooks。我将从轮询循环开始，因为可以在本地运行轮询机器人。](https://github.com/go-telegram-bot-api/telegram-bot-api)

我打算用 SQLite 来存储数据。我喜欢零配置和设置。我喜欢我可以把数据库复制到任何我想复制的地方。这样我可以很容易地组织备份。例如，我可以时不时地用电子邮件发给自己。除非我把 bot 向全世界开放，否则我不指望数据库会有多大增长。

## 代码

让我们直接进入编码吧。Go Telegram Bot API 在自述文件中提供了一个很好的起点。这是一个回声机器人。它只是将接收到的文本发送回用户。我从那开始。

为了让机器人连接到电报网络，它必须有一个用户名和一个 API 令牌。这两样东西你都可以通过电报和一个特殊机器人交谈得到。这个机器人叫做[机器人之父](https://t.me/BotFather)。

[![The Botfather](img/0e994c5785371e8c75ca04912780157d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Oq2ZHDyQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/zQRcF0w.png)

互动相当琐碎愉快。只要跟着对话走，他会给你你需要的东西。只是确保不要要求太多。

[![20 bots](img/eecc625633271905a43b654a24047741.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gs3N9fKv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/eCAQ4q5.jpg)

我从僵尸父亲那里得到的 API 令牌应该是私有的。谁拥有令牌，谁就拥有机器人。为了不让它出现在 repo 中，我添加了一些代码来从一个简单的 JSON 配置中读取它。除此之外，它只是自述文件中的代码。

```
// Config represents the structure of the config.json file
type Config struct {
    Token string `json:"token"`
}

func readConfig() Config {
    file, err := os.Open("config.json")
    if err != nil {
        log.Panic(err)
    }

    defer file.Close()
    bytes, err := ioutil.ReadAll(file)
    if err != nil {
        log.Panic(err)
    }

    var config Config
    err = json.Unmarshal(bytes, &config)
    if err != nil {
        log.Panic(err)
    }

    return config
} 
```

第一天应该够了。如果你好奇，可以在 GitHub 上找到代码[。这个版本被标记为`day-1`。](https://github.com/detunized/since-bot/tree/day-1)

*原载于[detunized.net](https://detunized.net/posts/2019-03-28-telegram-bot-in-go/)T3】*
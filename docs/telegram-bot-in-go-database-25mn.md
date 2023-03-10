# Go 中的电报机器人:数据库

> 原文：<https://dev.to/detunized/telegram-bot-in-go-database-25mn>

今天我要给我的机器人添加一个数据库。正如我在[上一篇文章](https://dev.to/detunized/telegram-bot-in-go-3hd4)中提到的，我将使用 SQLite 来保持简单，因为管理层拒绝了我对 Oracle 这个项目的预算请求。

我很快为 Go 购买了一个 SQLite 包，并找到了 [go-sqlite3](https://github.com/mattn/go-sqlite3) 。这似乎是一个非常受欢迎的套餐。它允许我使用标准的`database/sql`包，它充当驱动程序，允许 Go 数据库引擎与 SQLite 数据库对话。到目前为止一切顺利。那我就用那个。

因为我用 Go 写所有的东西，所以我非常需要在某个地方使用 goroutines。所以我做的第一件事是将回复功能移到一个 goroutine 中。

```
for update := range updates {
    go reply(bot, update)
} 
```

这应该会让事情进展得更快！不要介意电报将[不允许](https://core.telegram.org/bots/faq#my-bot-is-hitting-limits-how-do-i-avoid-this)在任何延长的时间内频繁请求的事实。无论如何我都要试试。

要使用数据库，我必须先打开它。是这样做的:

```
func openDatabase() *sql.DB {
    db, err := sql.Open("sqlite3", "./since.db")
    if err != nil {
        log.Panic(err)
    }

     _, err = db.Exec("CREATE TABLE IF NOT EXISTS events (" +
        "id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT, " +
        "user INTEGER, " +
        "name TEXT, " +
        "date INTEGER);")

     if err != nil {
        log.Panic(err)
    }

     return db
} 
```

如果表不存在，我也会创建一个。因为我几乎不用 SQL，所以我更喜欢尖叫，这样人们就能听到我。也许数据库会有一种紧迫感和[更快地处理我的查询](https://twitter.com/shipilev/status/703176579191410689)。

一旦数据库成功打开并且没有人惊慌，就该开始将传入事件保存到其中了。我是这样做的:

```
func store(message *tgbotapi.Message, db *sql.DB) {
    _, err := db.Exec("INSERT INTO events (user, name, date) VALUES ($1, $2, $3);",
        message.From.ID,
        message.Text,
        message.Date)
    if err != nil {
        log.Panic(err)
    }
}

store(update.Message, db) 
```

现在，每次我向我的机器人发送消息时，它都会将其内容存储在数据库中。这是一个开始。机器人仍然像以前一样回复。还是真的很哑。我们将在第二天让它变得更聪明。

如果你好奇，可以在 GitHub 上找到代码[。这个版本被标记为`day-2`。](https://github.com/detunized/since-bot/tree/day-2)

*原载于[detunized.net](https://detunized.net/posts/2019-03-29-telegram-bot-in-go-database/)T3】*
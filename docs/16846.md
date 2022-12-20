# 第五天:为讨厌云的人制作一个 swift macOS 密码管理器

> 原文：<https://dev.to/swlkr/day-5-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-3p9k>

[< -第 4 天，请点击此处](https://dev.to/swlkr/day-4-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-43a1)

## 活生生的生命

我知道这看起来像是一个坏主意，我的意思是，当你真正地生活而不是整天编码时会发生什么？接下来会发生什么？今天，我和几个飞到城里的朋友去滑雪，玩得很开心！只摔了几下，自己都惊叹！我不是专业的滑雪板手，甚至不是一个好的滑雪板手，所以这很酷。
[![](img/1ce7648e925dd22ac6d15d03bbabd1fc.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--cqgE5RrE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m8rpcng9ti251gx0ex45.JPG)

## 连胜必须继续

听着，我已经连续 34 天每天都在写作，4 天都在编写这个产品，我不能因为我过了今天的生活就打破记录！这种情况还在继续。离我筋疲力尽昏倒只有 30 分钟了，所以让我们看看我能做些什么。事实证明，并不多。不过，我确实设法在视图上添加了标签和文本字段，所以这很了不起。我把 sqlite 代码藏在一个结构后面，我可能会尝试一些涉及继承或协议的东西，所以我最终得到了类似于活动记录的东西，这里是我离开的地方:

```
import SQLite

struct Login {
    let id = Expression<Int64>("id")
    let username = Expression<String?>("username")
    let email = Expression<String>("email")
    let key = Expression<String>("key")
    var db: Connection

    static func database() -> Connection {
        let path = NSSearchPathForDirectoriesInDomains(
            .applicationSupportDirectory, .userDomainMask, true
            ).first! + "/" + Bundle.main.bundleIdentifier!

        do {
            try FileManager.default.createDirectory(
                atPath: path, withIntermediateDirectories: true, attributes: nil
            )
        } catch {
            print("Unexpected error: \(error).")
        }

        var conn : Connection?
        do {
            conn = try Connection("\(path)/app.db")
        } catch {
            print("Unexpected error: \(error).")
        }

        return conn!
    }

    init() {
        db = Login.database()
    }
} 
```

明天继续收看第六天的节目🔥
# 第六天:为讨厌云的人制作一个 swift macOS 密码管理器

> 原文：<https://dev.to/swlkr/day-6-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-1361>

[< -第 5 天，请点击此处](https://dev.to/swlkr/day-5-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-3p9k)

我将开始更好地布局这些帖子，更像一个时间表，我认为这可能很酷，也更有启发性，因为我实际上花了多少钱在这件事情上，这样我就可以计算当我试图以 29 美元/生命周期出售它时，我每小时赚多少美元，也许可以把它放在 [Setapp](https://setapp.com) 上。我希望这至少是最低工资，但根据我过去的兼职项目，我对此表示怀疑。

### 上午 11 点 06 分

决定我今天想做什么。我有点落后了，所以我真的需要找出最快的方法将数据从`AddLoginViewController`导入 SQLite 和 Keychain

### 上午 11 点 47 分

这就是一点点成功的样子，它并不漂亮，但很有效。我的问题有两个，一个是 ContainerView 按钮不能触发，结果是我需要在`AddNewLoginViewController`上调用 addChild 来让它像这样工作

```
 @IBAction func addNewLogin(_ sender: NSMenuItem) {
        let storyboard = NSStoryboard(name: NSStoryboard.Name("Main"), bundle: Bundle.main)
        let vc = storyboard.instantiateController(withIdentifier: "AddLoginViewController") as! NSViewController

        for sView in containerView.subviews {
            sView.removeFromSuperview()
        }

        addChild(vc)
        vc.view.frame = containerView.bounds
        containerView.addSubview(vc.view)
    } 
```

然后，按钮开始启动，这样 SQLite.swift 就可以接管了

```
 @IBAction func addButtonClicked(_ sender: NSButton) {
        print("clicked")
        let login = Login()
        let db = Database.open()
        try! db.run(login.table.insert(login.email <- emailTextField.stringValue, login.username <- usernameTextField.stringValue, login.key <- UUID.init().uuidString))

        for row in try! db.prepare(login.table) {
            print("id: \(row[login.id]), key: \(row[login.key]), email: \(row[login.email]), name: \(row[login.username])")
        }
    } 
```

哦，我还添加了一些结构，用于打开数据库连接和包装结构表定义:

```
import SQLite

public struct Database {
    static func open() -> Connection {
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
}

public struct Login {
    let table = Table("login")
    let id = Expression<Int64>("id")
    let url = Expression<String?>("url")
    let username = Expression<String?>("username")
    let email = Expression<String?>("email")
    let key = Expression<String>("key")
} 
```

所以现在只是波兰的工作

[![](img/b295efe0653e88c4da06e30c95a39a1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jr6DXyRD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7umpzl72oyttbpvfqwck.png)

明天第七天😬
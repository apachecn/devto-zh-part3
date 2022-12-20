# 第九天:为讨厌云的人制作一个 swift macOS 密码管理器

> 原文：<https://dev.to/swlkr/day-9-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-4koj>

[<——第 8 天去这里](https://dev.to/swlkr/day-8-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-39j3)

📅2019 年 1 月 9 日
🚀距离发布还有 21 天
🔥九天连胜
🤑0 美元
📈0 客户
⌚️花费了 7.55 小时
💻18 个文件被更改，987 个插入(+)，171 个删除(-)
🏁今天的目标:编辑/删除现有密码

### 晚上十一点三十五分

绝对是最专注的一天，因为我在午夜前只有 30 分钟，只是要从菜单上删除工作，但明天肯定要加快速度。

### 晚上 11 点 49 分

我做到了，我破解了密码。幸运的是，函数式编程可以很好地翻译成大多数其他语言，甚至是静态类型的语言(我来自 clojure)。我需要做的是将删除菜单项映射到`AppDelegate.swift`中的一个函数，然后像第一响应者那样做，最后将该函数定义复制并粘贴到`PasswordListViewController.swift`。

从那以后，它只是想出如何从`Row`对象和 viola 的数组中获取多行，`sqlite.swift`可以用一个`.filter([].contains(id))`来接管。下面是它在代码
中的样子

```
@IBAction func deleteLogin(_ sender: NSMenuItem) {
    do {
        let indices = tableView.selectedRowIndexes
        let ids = indices.compactMap { (logins?.count ?? -1 > $0) ? logins?[$0][login.id] : nil}
        let lg = login.table.filter(ids.contains(login.id))
        if try db.run(lg.delete()) > 0 {
            logins = Array(try! db.prepare(login.table))
            tableView.reloadData()
        } else {
            print("login not found")
        }
    } catch {
        print("delete failed: \(error)")
    }
} 
```

⌚️ ~花了 8 个小时。明天将会是更有成效的一天。我确信这一点
# 第三天:为讨厌云的人制作一个 swift macOS 密码管理器

> 原文：<https://dev.to/swlkr/day-3-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-5dii>

[< -第二天请点击此处](https://dev.to/swlkr/day-2-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-3h38)

我今天的目标是使用主密码解锁应用程序，并显示一个空白屏幕，密码列表将在那里！我们开始吧！

在对诸如如何声明类、如何声明结构、如何填充表格视图以及如何在窗口中切换视图控制器等各种事情进行了大量搜索之后:

*   [https://docs . swift . org/swift-book/language guide/properties . html](https://docs.swift.org/swift-book/LanguageGuide/Properties.html)
*   [https://stack overflow . com/questions/44953830/swift-array-to-table view-in-MAC OS](https://stackoverflow.com/questions/44953830/swift-array-to-tableview-in-macos)

我取得了一些切实的进步！

[![](img/418529e1d61ce9af1fce2bb04f961fdf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3ZQa8ZWm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yux89xx7i7cs0j5s7n4x.gif)

下面是检查主密码和显示不同视图控制器的代码，我的密钥是`self.view.window?.contentViewController = viewController`

```
 @IBAction func unlockButtonPressed(_ sender: NSButton) {
        let keychain = KeychainSwift()
        let masterPassword : String? = keychain.get("MasterPassword")

        if masterPassword == masterPasswordTextField.stringValue {
            let storyboard = NSStoryboard(name: NSStoryboard.Name("Main"), bundle: Bundle.main)
            let viewController = storyboard.instantiateController(withIdentifier: "PasswordListViewController") as! NSViewController
            self.view.window?.contentViewController = viewController
        }
    } 
```

我还摆弄了一下密码列表视图，但我想这是我明天要做的事情，毕竟我还在做全职工作

```
import Cocoa
import KeychainSwift

struct Login {
    let id: Int64
    let username: String?
    let email: String?
    let key: String
}

class PasswordListViewController: NSViewController {
    let logins = [Login]()

    override func viewDidLoad() {
        super.viewDidLoad()
    }

    override var representedObject: Any? {
        didSet {

        }
    }

    func numberOfRows(in tableView: NSTableView) -> Int {
        return logins.count
    }

    func tableView(_ tableView: NSTableView, objectValueFor tableColumn: NSTableColumn?, row: Int) -> Any? {
        return logins[row]
    }
} 
```

明天收听第 4 天，实际添加密码并显示在表格视图中！将会有一些混乱的 SQLite 动作。
# 第二天:为讨厌云的人制作一个 swift macOS 密码管理器

> 原文：<https://dev.to/swlkr/day-2-making-a-swift-macos-password-manager-for-people-who-hate-the-cloud-3h38>

[< -第一天到这里](https://dev.to/swlkr/day-1-making-a-native-macos-password-manager-for-people-who-hate-the-cloud-3j68)

我今天的目标是创建一个模式！昨天我想得太多了，开始纠结 xcode 和 cocoa 有多糟糕。以下是模式:

注册

*   id(整数)
*   电子邮件(文本)
*   用户名(文本)
*   url(文本)
*   名称(文本)
*   密钥(uuid)

好了，现在我根据[韵对上一篇文章的](https://dev.to/rhymes)评论，把我的配色方案改回浅色

[![](img/1b2d18f9fb841fd82cadb86fdc1ef425.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i3vJokTo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ofgjul99jqxizl32uury.png)

过了一段时间，我意识到 CoreData 不适合我。所以我决定引入一些第三方库[stephencelis/SQLite . swift](https://github.com/stephencelis/SQLite.swift)和 [evgenyneu/keychain-swift](https://github.com/evgenyneu/keychain-swift) 来帮助我在 2020 年 1 月 31 日之前完成。简而言之，我今天的成就如下:

*   试图扰乱 CoreData 10 分钟！！决定不去了。
*   直接使用 SQLite3 lib 搜索 SQLite 与 Swift 的集成，再次…不太适合我。
*   找到了 SQLite.swift，在 CoreData 失败后感觉很有效率
*   意识到试图用 scrypt 在 swift 中加密文本也是错误的，于是用 keychain 代替了 keychain-swift
*   最后，我用 sqlite 创建了登录表，并惊奇地发现在使用了一堆其他人的代码之后，它是如此的简单
*   我的工作效率仍然很高，从故事板初始化视图控制器切换到在 appDidFinishLaunching 中检查主密码的钥匙串，如下所示:

```
let storyboard = NSStoryboard(name: NSStoryboard.Name("Main"), bundle: Bundle.main)
        let window = storyboard.instantiateController(withIdentifier: "WindowController") as! NSWindowController
        let keychain = KeychainSwift()
        let masterPassword : String? = keychain.get("MasterPassword")
        if masterPassword == nil {
            let viewController = storyboard.instantiateController(withIdentifier: "SetMasterPasswordViewController") as! NSViewController
            window.contentViewController = viewController
        } else {
            let viewController = storyboard.instantiateController(withIdentifier: "UnlockViewController") as! NSViewController
            window.contentViewController = viewController
        }
        window.showWindow(self) 
```

看看所有的重复和那些硬编码字符串*喘息*。这就是我在大约 24 小时/月内发布应用程序所需要的时间。我还得到了主密码的实际设置以及工作，我进入奖金领土的一天！

```
 @IBAction func setMasterPasswordButtonClicked(_ sender: NSButton) {
        if masterPasswordTextField.stringValue.count > 12 {
            let kc = KeychainSwift()
            kc.set(masterPasswordTextField.stringValue, forKey: "MasterPassword")
        }
    } 
```

看看那些花括号，和 clojure 相比，看起来像行噪声。好吧，我期待着第三天，我打赌我新发现的对其他人写的免费代码的依赖(没有错误，我敢肯定)我真的可以开始在 SQLite 中存储数据和在不同网站/应用程序的 keychain 中存储密码了！
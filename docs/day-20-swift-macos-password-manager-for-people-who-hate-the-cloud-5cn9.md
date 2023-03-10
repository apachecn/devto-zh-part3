# 第 20 天:为讨厌云的人提供 Swift macOS 密码管理器

> 原文：<https://dev.to/swlkr/day-20-swift-macos-password-manager-for-people-who-hate-the-cloud-5cn9>

[<——第 19 天去这里](https://dev.to/swlkr/day-19-swift-macos-password-manager-for-people-who-hate-the-cloud-283f)

📅2019 年 1 月 20 日
🚀距离发布还有 10 天
🔥19 天连胜
💰2.99 美元的价格
🤑0 美元收入
📈0 顾客
⌚️花了 30 个小时
💻82 个文件被更改，247751 个插入(+)，545 个删除(-)
🏁今天的目标:**解析网站上的图标**

**下午 3 点 16 分**
又开始迟到了！周日晚上编码！我们开始吧。哦，等等，我想花一点时间来谈谈整体架构，以及它实际上如何保护您的密码安全。

因此，有两件事共同作用，使这件事成为万里无云的现实。

**1 个钥匙扣**

我知道钥匙链可以与云同步，这是你的选择，但没有更好的地方来存储敏感的东西。我的选择差不多是钥匙串或 NSUserDefaults，我选择将主密码保存在钥匙串中。这是如何工作的，电脑的登录密码保护钥匙串，反过来保护您的主密码。

**2 个 SQLite 与[SQLcipher](https://github.com/sqlcipher/sqlcipher)T3】**

因此，这是一个加密文件(加密良好的文件集)，由 SQLite.swift 读取，并以明文形式传递密钥(您的主密码)，幸运的是，这是一个桌面应用程序，而不是 web 应用程序，所以明文不是那么糟糕！

我在这两件事上写的每一行代码都是锦上添花，希望这份锦上添花值点钱，否则也没关系。很高兴知道我控制着存储我所有密码的代码，✊，当我打开源代码时，任何人都可以审计这些代码！

**下午 3 点 21 分**
好了，既然结束了，我们走吧。今天是一般的设计清理和解析每个登录网站的图标。

**晚上 7:02**
吸进网飞，不过我取消了，所以很好。网站上的图标哦，把密码生成器带回来。

**晚上 7 点 54 分**
嘿！带回了密码生成器，还有每个密码的 NSUserTemplate 图标，太神奇了

[![](img/4df2d256cf67ee19342a971c116605e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wZVDtvXM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e8e0pr4ybka6lnx0tx7w.png)

**晚上 8:02**
我发现这个库可以用 swift 解析 FavIcon/touch icons[leonbreedt/FavIcon](https://github.com/leonbreedt/FavIcon)他似乎值得信任，还看了几分钟代码，LGTM！信任不是在互联网上赢得的，而是给予的！

晚上 9:42
在看一部关于特氟隆(PFOA/C-8)有多糟糕的纪录片时，我的图像工作了！万岁！不过我必须修复它，因为苹果的图标不知何故是一个长方形，而谷歌的是一个 favicon，模糊不清。不酷。下面是一些代码！

如何将 NSImage 写入磁盘

```
extension NSImage {
    func write(to url: URL, fileType: NSBitmapImageRep.FileType, options: Data.WritingOptions = .atomic) -> Bool {
        do {
            if let bits = representations.first as? NSBitmapImageRep {
                let data = bits.representation(using: fileType, properties: [:])
                try data?.write(to: url, options: options)
            }
            return true
        } catch {
            print(error)
            return false
        }
    }
} 
```

还有截图！

[![](img/990bf6888de46e87921055e2e128ccad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kG96EKJD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cln7w1ua7kztnej1zkz2.png)

这就是今天的目标，完成了。明天见👋
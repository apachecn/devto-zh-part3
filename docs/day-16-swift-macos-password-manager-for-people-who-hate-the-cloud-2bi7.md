# 第 16 天:为讨厌云的人提供 Swift macOS 密码管理器

> 原文：<https://dev.to/swlkr/day-16-swift-macos-password-manager-for-people-who-hate-the-cloud-2bi7>

[<——第 15 天去这里](https://dev.to/swlkr/day-15-swift-macos-password-manager-for-people-who-hate-the-cloud-2e24)

📅2019 年 1 月 16 日
🚀距离发布还有 14 天
🔥十五天连胜
💰2.99 美元的价格(现在进入一杯咖啡的领域)
🤑0 美元收入
📈0 顾客
⌚️花费了 13.5 小时
💻20 个文件被更改，1324 个插入(+)，171 个删除(-)
🏁今天的目标:**复制用户名、电子邮件和密码按钮**

早上 6:00
决定累一点，看看我能在工作前完成多少。

**早上 7 点 06 分**
嘿！Xcode 终于崩溃了。试图使用重力区域和堆栈视图，并移动故事板中的最后一个重力区域，导致 Xcode 退出得非常快，甚至没有一个沙滩球那么快🏖

上午 8:00
在这一点上，他已经花了几个小时处理堆栈视图。还是不理解他们。当用户名或电子邮件为零时，是时候弃船想出别的办法了

**上午 8 点 10 分**
这是一个美女:

```
 override func viewDidLoad() {
        super.viewDidLoad()
        let loginTable = Login()
        let kc = KeychainSwift()

        nameOrUrlValue.stringValue = login?[loginTable.name] ?? login?[loginTable.url] ?? ""

        let username = login?[loginTable.username] ?? ""
        let email = login?[loginTable.email] ?? ""
        let password = kc.get(login?[loginTable.key] ?? "") ?? ""

        if username.count > 0 {
            label1.stringValue = "Username"
            value1.stringValue = username

            if email.count > 0 {
                label2.stringValue = "Email"
                value2.stringValue = email

                label3.stringValue = "Password"
                value3.stringValue = password
            } else {
                label2.stringValue = "Password"
                value2.stringValue = password
            }

        } else {
            label1.stringValue = "Email"
            value1.stringValue = email

            label2.stringValue = "Password"
            value2.stringValue = password
        }
    } 
```

当你嵌套了 if/else 语句时，你知道你做错了。嘘，别告诉任何人。这是我们的小秘密。那是什么意思？动作快，打破社会？听起来不太对劲。

我甚至没有把复印按钮放进去，也许下班后我可以把它塞进去。

**晚上 7 点**
我在这里，我不带啤酒，准备摇滚

**晚上 7 点 27 分**
搞定！27 分钟，我做了相当多的事情，我决定放弃“查看”视图，只在从列表中选择内容时显示编辑视图，不确定我是否喜欢这样，看，复制按钮:

[![](img/08d374077dc9e20accf661d064cb96da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e29aDju8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/amn0m0qxrpu45h7f7qm1.gif)

冷静如黄瓜

明天见👋